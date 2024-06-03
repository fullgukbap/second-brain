escape back은 문자 그대로를 사용한다는 뜻이다.

그래서 react 공식문서에서 다음과 같은 예제를 발견했다.
```js
return (
	<img
		className="avatar"
		scr={user.imageUrl}
    />
)
```

여기서 다음과 {user.imageUrl}과 같은 형태로 사용할 수 있다. 그러면 동적으로 이미지를 변경할 수 있으니 코드를 많이 변경할 필요가 없을 것이다.

---
[[Escape back (이스케이프 백)이란?]]
[[React 에서의 JSX란?]]