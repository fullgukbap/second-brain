
어떠한 컴포넌트가 정보의 값을 보존시키고 싶을 때가 있다. 이때 `state`를 사용하면 된다. state를 사용하기 위해서는 useState 훅을 사용해야 한다.


보통 useState를 사용하면 `[something, setSomething] = useState(0)` 이런식으로 적는것이 관례라고 한다.

그리고 count값을 변경하기 위해서는 setCount를 호출해 변경하면 된다.
```js
function MyButton() {
	const [count, setCount] = useState(0);

	function handleClick() {
		setCount(count + 1);
	}

	return (
		<button onClick={handleClick}>
			Clicked {count} times
		</button>
	);
}


export default function MyApp() {
	return (
		<div>
			<MyButton />
			<MyButton />
		</div>
	)
}
```

이제 위의 코드는 각각의 컴포넌트가 각각의 상태를 보존하고 있는 상태입니다. 그림으로 표현하자면 다음과 같죠
![[Pasted image 20240603203055.png]]

하지만 같은 컴포넌트가 같은 데이터를 공유해야할 경우도 많습니다. 
![[Pasted image 20240603203121.png]]
이런 상태를 말이죠. 그러면 이런 상태를 코드로 표현하고자 한다면 어떻게 할까요?

먼저 state를 MyApp으로 옮겨줍시다. 이렇게 말이죠
```js
function MyButton() {
	

	return (
		<button onClick={handleClick}>
			Clicked {count} times
		</button>
	);
}


export default function MyApp() {
	const [count, setCount] = useState(0);

	function handleClick() {
		setCount(count + 1);
	}
	
	return (
		<div>
			<MyButton />
			<MyButton />
		</div>
	)
}
```

그러면 분명히 MyButton 에서 에러가 발생합니다. handleClick 함수와 count 변수가 없기 때문이죠. 그래서 이때 MyApp에서 MyButton을 호출할 때 escape back과 같이 명세하면 인자값을 보낼 수 있습니다. 다음과 같이 해보세요.

```
function MyButton({ count, onClick }) {

	return (
		<button onClick={onClick}>
			Clicked {count} times
		</button>
	);
}


export default function MyApp() {
	const [count, setCount] = useState(0);

	function handleClick() {
		setCount(count + 1);
	}
	
	return (
		<div>
			<MyButton count={count} onClick={handleClick} />
			<MyButton />
		</div>
	)
}
```

이렇게 작성한다면 버튼을 한번 눌렀을 떄 state 값이 동시에 바뀌는 것을 확인할 수 있죠