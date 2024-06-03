Component는 레고 블럭이다. 이것을 React는 함수를 이용해 제작한다.

예를 들면 다음과 같다.
```javascript
function MyButton() {
	return (
		<button>I'm a button</button>
	);
}
```

블럭은 블럭으로 인해 또 생성될 수 있기에 다음과 같이 작성할 수 있다.
```javascript
export default function MyApp() {
	return (
		<div>
			<h1> welcome to my app </h1>
			<MyButton />
		</div>
	)
}
```

---
[[Component란]]