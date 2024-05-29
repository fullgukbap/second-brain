Go 언어에서 Any 타입이 해당 타입하고 같은지 확인하는 방법은 Type Assertion을 사용하면 된다.

type assertion을 사용하는 방법은 다음과 같다.

```go
var x any = 24

if v, ok := x.(int); ok {
	fmt.Printf("x is int: %d\n", v)
}
```

