JSX를 사용하면 Javascript에 마크업을 넣을 수 있습니다. **중괄호를 사용**하면 코드에서 일부 변수를 삽입하여 사용자에게 표시할 수 있도록 자바스크립트로 "escape back" 할 수 있습니다.  [[Escape back (이스케이프 백)이란?]]

이런 형태로 표현한다고 한다.
결론적으로는 Escape Back을 사용한다는 점.

```js
const user = {
  name: 'Hedy Lamarr',
  imageUrl: 'https://i.imgur.com/yXOvdOSs.jpg',
  imageSize: 90,
};

export default function Profile() {
  return (
    <>
      <h1>{user.name}</h1>
      <img
        className="avatar"
        src={user.imageUrl}
        alt={'Photo of ' + user.name}
        style={{
          width: user.imageSize,
          height: user.imageSize,
        }}
      />
    </>
  );
}
```


---

[[React 에서의 JSX란?]]
[[React의 JSX에서 Escape back 활용하기]]
