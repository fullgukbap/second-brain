
### 목적
나는 현재 서버를 logging 하여 에러 처리와 모종의 이유로 발생할 수 있는 문제를 해결하기 위해 서버를 기록하고자 한다.  그러기 위해서는 나는 다음을 알고 싶다.

- 어떤 정보를 기록해야 하는가 ?
	- 기록할 때 어디에 저장하는가?
- zap 라이브러리를 사용하는 방법



### 어떤 정보를 기록해야 하는가?
- 오류 내용(오류 메시지, 오류 코드, 스택 추적 또는 호출 스택 정보(panic하면 나오는 정보 말하는 거임))
- 요청정보(메소드, 엔드포인트, header, body, query parmas, path value)
	- + 클라이언트 정보 (클라이언트의 IP, 식별할 만한것들)
- 응답정보(응답 status code, body)
- 기타 
	- 데이터베이스 query 결과 기록
	- 외부 서비스 API 호출 결과 
- 커스텀 로그(데이터베이스 초기화 완료, 쿼리 날리기 준비 완료!)


+ 위  에러 메시지에는 시간에 항상 접두사로 붙인다.
+ 로그 수준을 만들어 필터링을 통해 쉽게 찾을 수 있다.

그래서 나는 다음과 같은 것들을 저장하려고 한다.
- 데이터베이스 질의 이력 (메모장에 저장)
- 요청 정보 및 응답 정보  (메모장에 저장)
- 시스템 내 로그 정보       (메모장 저장 및 console에 출력 )

##### 그러면 기록은 어디에?
나는 파일로 하기로 했다. 그런데 다른 곳에서는 로깅을 분석해주는 툴이 있다고 한다. 그러나 나는 파일에 하는 걸로 ㅎㅎ


### zap 라이브러리 사용법
그딴거 모르겠고 그냥 예제 코드 가져옴 ㅋㅋ
```go
package main

import (
	"fmt"
	"os"
	"time"

	"go.uber.org/zap"
	"go.uber.org/zap/zapcore"
)

func main() {
	initLogger()
}

func createDirectoryIfNotExists() {
	path, _ := os.Getwd()

	if _, err := os.Stat(fmt.Sprintf("%s/logs", path)); os.IsNotExist(err) {
		_ = os.Mkdir("logs", os.ModePerm)
	}
}

func getLogWriter() zapcore.WriteSyncer {
	path, _ := os.Getwd()

	f, _ := os.OpenFile(fmt.Sprint(path+"/logs/filename.txt"), os.O_APPEND|os.O_CREATE|os.O_WRONLY, 0644)

	return zapcore.AddSync(f)
}

func getEncoder() zapcore.Encoder {
	newEncoderConfig := zap.NewProductionEncoderConfig()
	newEncoderConfig.EncodeTime = zapcore.TimeEncoder(func(t time.Time, enc zapcore.PrimitiveArrayEncoder) {
		enc.AppendString(t.UTC().Format("2006-01-02T15:04:05Z0700"))
	})
	// newEncoderConfig.EncodeLevel = zapcore.CapitalColorLevelEncoder
	return zapcore.NewConsoleEncoder(newEncoderConfig)
}

func initLogger() {
	createDirectoryIfNotExists()

	core := zapcore.NewCore(getEncoder(), getLogWriter(), zapcore.DebugLevel)
	logger := zap.New(core, zap.AddCaller())

	logger.Info("hi")
	logger.Info("안녕하세요")

	zap.ReplaceGlobals(logger)
}

```