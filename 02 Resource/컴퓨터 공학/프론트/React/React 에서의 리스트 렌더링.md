리스트를 렌더링할 떄 다음과 같은 방식으로 한다.

```js
const products = [
  { title: 'Cabbage', isFruite: false, id: 1},
  { title: 'Garlic', isFruite: false, id: 2},
  { title: 'Apple', isFruite: true, id: 3},
];

export default function ShoppingList() {
  const listItems = products.map(product => 
    <li
      key={product.id}
      style={{
        color: product.isFruite ? 'magenta' : 'darkgreen'
      }}
    >
    
    {product.title}
    </li>
  );

  return (
    <ul>{listItems}</ul>
  )
}
```

여기서 `<li>` 에 key 속성이 있다는 점을 주목해야 한다. React는 나중에 항목을 삽입, 삭제 또는 재정령할 때 어떤 일이 일어났는지 알기 위해 Key를 사용한다고 한다. 중요한 포인트는 식별하기 위해서 사용한다는 점인 것 같다. 



---

[[HTML에서 Attribute란?]]
[[js 리스트 렌더링 ]]