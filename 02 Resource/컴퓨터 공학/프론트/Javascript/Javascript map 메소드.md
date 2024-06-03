어떤 배열에 있는 모든 요소 값을 변겨하고 싶을때가 있습니다. 그때 for문을 이용해 처리하면 되지만 Array.map()을 통해 쉽게 처리해봅시다.

```javascript
let arr = [3, 4, 5, 6]

let modifiedArr = arr.map(function(element) {
	return element * 3;
})
```

이렇게 하면 3이 곱해진 배열로 새로 만들어지게 된다.