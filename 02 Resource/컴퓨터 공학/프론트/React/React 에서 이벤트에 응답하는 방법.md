여기서 중요한 점은 컨포넌트 내에 새로운 이벤트 핸들러를 만들고 그것을 할당시켜주는 것이다.

```js
function MyButton() {
	function handleClick() {
		alert('You clicked me!')
	}

	return (
		<button onClick={handleClick}>
			Click me
		</button>
	);
}

export default function MyApp() {
	return (
		<div>
			<h1>Button Click!</h1>
			<MyButton />
		</div>
	)
}
```