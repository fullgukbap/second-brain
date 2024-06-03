React에서는 조건부 렌더링 하는 방법은 간단하다. 그냥 IF문을 갈겨버리면 끝이다. 마치 다음과 같이 할 수 있다.

(AniminPanel, LoginForm와 같은 컴포넌트가 이미 존재했다는 전제)
```js
let content;
if (isLoggedIn) {
	content = <AdminPanel />;
} else {
	content = <LoginForm />;
}

return (
	<div>
		{content}
	</div>
)
```
