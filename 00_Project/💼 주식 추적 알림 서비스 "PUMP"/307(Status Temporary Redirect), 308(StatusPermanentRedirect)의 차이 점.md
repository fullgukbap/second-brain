### 내가 왜 이 개념을 알려고 하는가?
Oauth를 구현하던 중 Status Temporary Redirect 를 자주 사용하길레 308과 어떤 차이점이 존재하는지 궁굼해서 

### 그러면 한번 개념을 알아볼까?

### http status code 300 번 대는 무슨 역활을 하나?
- Redirection과 관련된 것들을 300번대 이다. 

##### 307 Status Temporary Redirect 
status temporary(임시적인) 리다이렉트 

HTTP 307 Status Temporary redirect 리다이렉트 상태 응답 코드는 요청한 리소스가 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location "이 페이지는 현재 영어로만 제공됩니다") 헤더에 주어진 URL 로 임시로 옮겨졌다는 것을 나타냅니다. 그래서 즉시 브라우저가 Redirect 시키게 됩니다.

원래 요청한 메소드와 Body 를 재사용하여 요청을 리다이렉트 합니다.

`307`과 [`302`](https://developer.mozilla.org/ko/docs/Web/HTTP/Status/302)가 유일하게 다른점은 `307`은 Method 와 Body 를 변경하지 않고 리다이렉트 요청을 하도록 보장합니다

##### 308 Status Permanent Redirect의 차이 점
하이퍼텍스트 전송 프로토콜 (HTTP) **`308 Permanent Redirect`** 리디렉션 상태 응답 코드는 요청된 리소스가 [`Location`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Location "이 페이지는 현재 영어로만 제공됩니다") 헤더에 지정된 URL로 확실히 이동되었음을 나타냅니다.

그리고 다음에 redirection 대상이 된 url로 요청이 되면 저절로 redirection된 url로 이동된다. 

### 그러면 어떤 차이점이 있는가?
임시적인가 영구적인가의 차이 점인데
그리고 다음에 redirection 대상이 된 url로 요청이 되면 저절로 redirection된 url로 이동된다는 차이 점이 있다.