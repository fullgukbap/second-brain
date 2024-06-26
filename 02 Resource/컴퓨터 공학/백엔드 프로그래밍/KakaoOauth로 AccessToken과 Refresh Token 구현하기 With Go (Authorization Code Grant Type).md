
### 들어가기 앞서서
![[Pasted image 20240507201026.png]]
저희는 어떤 서비스를 이용할 때 종종 위의 유명한 회사들인 구글, 카카오, 네이버들의 아이디와 패스워드를 이용해 쉽게 로그인 하고는 합니다. 저희는 이런 서비스를 통해 아주 손쉽게 회원가입 및 로그인을 진행할 수 있죠
그렇기에 이번 포스트에서는 위처럼 아주 편한 로그인(이하 oauth)에 대해서 알아보려고 합니다

- Oauth가 무엇이죠?
- Oauth의 효용이 뭐야?
- Oauth에도 유형들이 있다고?
- KaKao Oauth 사용해보기
- Kakao Oauth를 Go언어로 구현해보기 
  
### Oauth가 무엇이죠? 
Oauth란 Open Authorization 약자로써 사용자 인증 단계를 타 서비스에 위임하는 것을 뜻합니다. 위에서 언급했듯이 사용자들이 손쉽게 로그인 하게 해주는 프로토콜입니다. 아마 글을 읽으면 이러한 기능이 생긴 이유를 지레 짐작하셨을 겁니다. oauth가 생긴이유는 바로 "귀찮음"이죠. 생각해보세요 앱의 사용자들이 어떤 서비스를 이용할 때마다 계속해서 ID와 Password를 입력한다면 그것은 매우 귀찮을 것이고 같은 현상이 반복되는 것은 더욱더 역사적으로 봤을떄 좋지 못해요. 그래서 oauth가 생긴 이유죠.

### Oauth의 효용이 뭐야
아무래도 로그인 과정을 직접 구현하기 않고 위임하다 보니 개발 과정이 편합니다. 실제로 개발을 해보면 로그인 구현은 뚝딱이죠! 또한 사용자의 비밀정보를 저희 서버가 직접 저장하지 않다 보니 사용자의 데이터를 보호할 수 있습니다. 왜냐하면 Oauth는 사용자의 인가를 위임하는 것이기 떄문이죠! 

그러나 반면에 좋지 않은 결과를 불러올 수 있어요. 만에하나 구글같은 서버가 갑자기 따운되었다고 생각해보죠 그러면 Google Oauth를 일시적으로 사용할 못할테니 저희는 그 시간동안 많은 사용자들은 버려버리는 결과가 초래되죠 이처럼 인가과정이 어떠한 누군가에 의존되기 때문에 위험할 수 있어요.


### Oauth를 사용해 볼까요?

##### 사용 서비스에 등록을 해봅시다.
Oauth를 사용한다라 함은 제 3사의 서비스를 이용한다를 뜻하기도 때문에 사용하고자 하는 제3자 서비스에 등록을 해야 합니다. 등록을 하기 위해서는 callback URI를 입력해줘야 하고, 입력을 하게 되면 Oauth 회사 측에서 Client ID와 Client Password를 줍니다. 이것을 통해 kakao의 Resource Server가 인증 및 인가하는데 사용합니다. 즉 아래와 같은 상태가 됩니다.

![[생활코딩의 oauth 강좌중 등록 과정을 도식화.png]]

카카오톡 같은 경우 ClientID는 앱 REST API 키  [내 애플리케이션] > [앱 키]에서 확인 가능하고
client_secret은 [내 애플리케이션] > [보안]에서 보기 가능합니다.

그런 다음 다음과 같은 방식으로 진행된다.
![[Authorizationn Code Grant type 도식화 1.png]]

그래서 다음과 같이 작성하면 된다.
```go
package main

import (
	"context"
	"crypto/rand"
	"encoding/base64"
	"fmt"
	"log"
	"net/http"
	"time"

	"github.com/spf13/viper"
	"golang.org/x/oauth2"
)

func init() {
	viper.SetConfigFile(".env")
	err := viper.ReadInConfig()
	if err != nil {
		log.Fatalf("viper로 .env 해석 중 에러 발생 : %v", err)
	}
}

var kakaoOauthConfig *oauth2.Config

func main() {
	kakaoOauthConfig = &oauth2.Config{
		ClientID:     viper.GetString("OAUTH_KAKAO_CLIENT_ID"),
		ClientSecret: viper.GetString("OAUTH_KAKAO_CLIENT_SECRET"),
		Endpoint: oauth2.Endpoint{
			// Authrizaiton Server의 URL
			AuthURL: "https://kauth.kakao.com/oauth/authorize",

			// Resource Server의 URL
			TokenURL: "https://kauth.kakao.com/oauth/token",
		},
		RedirectURL: "http://localhost:8080/auth/kakao/callback",
	}

	http.HandleFunc("/", indexHandler)
	http.HandleFunc("/auth/kakao/login", kakaoOuathLoginHandler)
	http.HandleFunc("/auth/kakao/callback", kakaoOauthCallbackHandler)
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatalf("서버 Listening중 에러 발생 : %v", err)
	}
}

func indexHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, "index handler")
}

func kakaoOuathLoginHandler(w http.ResponseWriter, r *http.Request) {
	state := GenerateStateOauthCookie(w)

	// Resource 서버에게 인가하게 해주는 url 생성
	kakaoAuthorizationServerURL := kakaoOauthConfig.AuthCodeURL(state)

	http.Redirect(w, r, kakaoAuthorizationServerURL, http.StatusTemporaryRedirect)
}

func kakaoOauthCallbackHandler(w http.ResponseWriter, r *http.Request) {
	oauthState, _ := r.Cookie("oauthstate")
	if r.FormValue("state") != oauthState.Value {
		log.Printf("invalid google aouth state cookie: %s state:%s\n", oauthState.Value, r.FormValue("state"))
		http.Redirect(w, r, "/", http.StatusTemporaryRedirect)
		return
	}

	token, err := kakaoOauthConfig.Exchange(context.Background(), r.FormValue("code"))
	if err != nil {
		log.Printf("code가 유효하지 않습니다. code: %s, err: %v", r.FormValue("code"), err)
	}

	fmt.Printf("token: %+v", token)

	http.Redirect(w, r, "/", http.StatusTemporaryRedirect)
}

func GenerateStateOauthCookie(w http.ResponseWriter) string {
	expiration := time.Now().Add(1 * 24 * time.Hour)
	b := make([]byte, 16)
	rand.Read(b)
	state := base64.URLEncoding.EncodeToString(b)
	cookie := &http.Cookie{Name: "oauthstate", Value: state, Expires: expiration}
	http.SetCookie(w, cookie)
	return state
}

```





### Kakao Oauth 사용해보기
그러면 한번 대한민국의 대표 메신저 앱인 kakao의 oauth를 사용해 볼까요? 
아래의 그림은 카카오톡의  Oauth 흐름도에요. 보시다싶이 Service Client와 kakao Auth Server가 존재하고 그 중간을 중개하는 Service Server가 존재합니다. 

여기서 첫 번째로 Service Client(휴대폰, 웹)가 요청을 Service Server에게 요청을 하게 됩니다.

이 요청을 받은 서버는 KaKao Auth Server에게 요청을 하면 반환값으로 어떤 url이 오게 되고 그 url를 service client에게 임시적으로 redirect 시켜 줍니다. 그런데 여기서 이 url은 카카오 서버로 요청이 바로 되는 url 이며 카카오의 ID와 Password를 입력하게 됩니다. 즉 카카오가 인증을 처리하는 셈이죠

그런 뒤 동의가 되면 Service Server가 사전에 카카오에 Service Client의 어떠한 정보를 받을 선택할 수 있습니다. 고로 정보를 받는 함은 개인정보를 받다로 이어지기 떄문에 동의 화면이 출력이 되고 사용자들은 동의하게 됩니다.

그러면 kakao auth server가 Service Server에게 다시 요청을 하게 되고 이때 state, code 값이 넘어오게 됩니다. code는 해당 
![[kakao oauth 처리 흐름도.png]]



Verify vs Grant
둘다 인증에 관련된 용어이다.

Verify는 주로 무언가가 정확하거나 유요한지 확인하는 과정을 의미한다.
Grant는 주로 권한을 부여하거나 허용하는 것을 의미한다.


### Oauth 2.0 소개
이 문장은 전통적인 클라이언트-서버 인증 모델에 대해 설명하고 있습니다. 이 모델에서 클라이언트는 서버에게 접근 제한된 리소스(보호된 리소스)에 대한 요청을 하면서 리소스 소유자의 자격 증명을 사용하여 서버와 인증합니다. 제한된 리소스에 대한 액세스를 제공하기 위해 리소스 소유자는 자신의 자격 증명을 제3자 응용 프로그램과 공유합니다. 이로 인해 여러 문제와 제약이 발생합니다.

-> 모든 서드파티 프로그램에 로그인할 때 같은 ID, PW를 사용하기 때문에 나의 ID, PW의 정보의 노출 위험도 높아졌으여 서드파티 프로그램 중 ID, PW를 암호화 하지 않은 경우가 발생하기도 한다. 

### Oauth 2.0 역활 
- Resource Owner (리소스 소유자)
	- 리소스 소유자는 보호된 자원에 대한 접근을 부여할 수 있는 주체 입니다.
	- 보호된 자원에 접근할 수 있는 권한을 부여하는 주체가 리소스 소유자 입니다.
- Resource Server (리소스 서버)
	- 보호된 리소스를 배포되어있는(호스팅 하는) 서버로, 액세스 토큰을 사용하여 보호된 리소스 요청을 수락하고 응답할 수 있는 서버 입니다.
- client (클라이언트)
	- 애플리케이션이 리소스 소유자를 대신하여 보호된 리소스를 요청하고,  보호된 리소스를 요청하는 애플리케이션 입니다. 
	  => 우리가 만든 Backend Server 말하는 것 같다.
- Authorization Server(권한 부여 서버)
	- 리소스 사용자를 성공적으로 인증한 후 클라이언트에 엑세스 토큰을 발급하는 서버 입니다.



### Oauth 2.0 프로토콜 흐름 
```
     +--------+                               +---------------+
     |        |--(A)- Authorization Request ->|   Resource    |
     |        |                               |     Owner     |
     |        |<-(B)-- Authorization Grant ---|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(C)-- Authorization Grant -->| Authorization |
     | Client |                               |     Server    |
     |        |<-(D)----- Access Token -------|               |
     |        |                               +---------------+
     |        |
     |        |                               +---------------+
     |        |--(E)----- Access Token ------>|    Resource   |
     |        |                               |     Server    |
     |        |<-(F)--- Protected Resource ---|               |
     +--------+                               +---------------+
```


### Authorization Code Grant 방법 
https://jundolblog.com/oauth2-0-%EA%B6%8C%ED%95%9C-%EB%B6%80%EC%97%AC-%EC%9D%B8%EC%A6%9D-%EB%B0%A9%EC%8B%9D-28acca311b94


Implicit 방법이 존재한다.


---

### 대한 이라는 것에서 너무 햇갈리다.
RFC 6749를 읽다가 도당체 이해가 되지 않은 문장을 발견했다. 

```
리소스 소유자는 보호된 자원에 대한 액세스를 부여할 수 있는 엔터티입니다. 리소스 소유자가 개인일 때는 엔드 유저라고 합니다.
```

여기서 나는 이해가 안되어 "~대한"에 관련된 문장의 뜻과 역할을 찾아보았다.
GPT 같은 경우 아래와 같이 대답을 해주었다.

  
"~에 대한"은 "~에 관한"이나 "~에 대하여"와 비슷한 의미로 사용됩니다. 이 표현은 어떤 대상이나 주제에 대한 설명, 해석, 이해를 나타내는 데 사용됩니다. 특히 문장에서 이 표현을 사용할 때, 그 이후에 오는 내용이 이 대상이나 주제와 관련되어 있음을 나타냅니다.

예를 들어, "여행에 대한 책을 쓰다"라고 하면, 이는 "여행"이라는 주제에 관해 책을 쓰고 있다는 것을 의미합니다. 마찬가지로, "연구에 대한 보고서"는 "연구"라는 주제에 관한 보고서를 나타냅니다.

따라서, "~에 대한"은 어떤 대상이나 주제를 중심으로 다른 정보를 전달하거나 설명하는 데 사용됩니다.

국어사전 같은 경우 
_자동사_

- 1.
    
    ((‘~에 대하여’, ‘~에 대한’의 구성으로 쓰여)) 무엇을 대상으로 하다.
    
    조국에 **대한** 사랑
    
    - _참고어_
        
        [관하다](https://ko.dict.naver.com/#/entry/koko/8a7f8af5617e41c2a977539f0bf5b116)
        
- 2.
    
    (어떤 사람이 다른 사람에게 어떤 태도로) 상대하여 응하다.
    
    나에게 애인이나 되는 것처럼 **대하는** 그녀가 부담스러웠다.
    
    문형
    
    명사가 명사에게 명사으로/ 명사처럼/ 형용사게
    
- 3.
    
    (여러 사람이 또는 어떤 사람이 다른 사람과) 서로 마주하다.
    
    나는 그와 다시는 **대하고** 싶지 않다.

이렇게 제시해 주었다. 그래서 나는 아래와 같이 고민하여 결국 해석에 성공해냈다.



리소스 소유자는 보호된 자원에 대한 액세스를 부여할 수 있는 엔터티입니다.

이 문장이 내가 이해가 안된 이유 먼저 엔터티라는 용어인 것 같아

여기서 엔터티는 내가 해석하기로 주체로 해석하는데, 어떤 현상의 주가 된다다고 생각해, 그냥 그가 돋보일 때 주체라고 하는 것 같아. 

이 문장이 내가 이해가 안된 이유 두 번째는 대한 이라는 단어때문이 것 같아
대한은 리소스 소유자를 주제로 덧 붙여주는 역활을 하는거야

예를 들면 

음식에 대한 평가를 진행했습니다.
이렇게 하면 음식이 대상이 되어 평가를 진행이 되었다고 해석할 수 있지
그니깐, 음식이 주가 되어 무엇을 하였다라고 표현할 수 있지

리소스 소유자는 보호된 자원에 대한 액세스를 부여할 수 있는 엔터티입니다.

그러면 이 문장은 
주어가 리소스 소유자야, 여기서 리소스 소유자는 "나"라고도 표현할 수 있어

그러면 나는 보호된 자원에 대한 액세스 
여기서 보호된 자원은 나의 개인정보 이겟지?

그러면 나는 개인정보에 대한 접근을 부여할 수 있는 주체 입니다.

그러면 개인정보가 대상이 되어 접근을 부여할 수 있어, 근데 앞에 주어가 나왔으니 주체라는 말이 나와야겠네.

오케이 그려면 이해됐어

그러면 리소스 소유자는 나를 뜻하고, 개인정보에 대한 접근 권한을 가지고 있는 주체라고 할수 있겠군.

-> 여기서 내가 이 문장을 이해하지 못한건, ~대하여의 역활 그리고 엔티티의 뜻이다. 

엔티티는 즉 주체이다. 주체(주인 주, 몸 체) 는 어떤 무리에가장 주인인 사람을 뜻한다. 즉 메인이라는 것이다. 그러면 위 문장에서 주체는 주어를 가릴 킬 수 밖에 없다. 

---

### 호스팅(Hosting) 이란? 
호스팅을 알아보기 전에 Host에 대해서 알아보자, Host는 주인, 주최국, 진행자라는 뜻으로 IT에서는 네투워크에 연결되어 있는 컴퓨터들을 Host라고 칭합니다.

그러면 호스팅은 무엇일까요? 호스팅은 정보의 잡약체인 서버의 전체 혹은 일부를 이용할 수 있도록 임대해 주는 "서비스" 입니다. 이러한 호스팅 서비스를 사용하게 되면 안정적으로 전기를 공급해야 하는 문제, 빠르고 안정적인 인터넷 회선을 사용해 하는것, 철저한 보안을 갖춰야 하는 선행의 작업들을 생략할 수 있습니다. 다만 돈을 지불해야겠죠

