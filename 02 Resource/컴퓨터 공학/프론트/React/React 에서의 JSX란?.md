React 코드를 작성하던 중 이러한 코드를 봤다.
```javascript
function MyButton() {
  return (
    <button>
      I'm a button
    </button>
  );
}

```

여기서 사용된 문법은 JSX라는 문법인데, JSX란 무엇일까?

JSX(Javascript Syntax eXtensions)는 Javascript를 확장한 문법이다. 
JSX는 리액트로 프로젝트를 개발할 때 사용되므로 공식적인 자바스크립트 문법이 아니다.
브라우저에서 실행하기 전에 바벨을 사용하여 일반 자바스크립트 형태의 코드로 반환된다.
JSX는 하나의 파일에 자바스크립트와 HTML을 동시에 작성하여 편리하다. 

JSX란 HTML 코드와 Javascript 코드를 손쉽게 작성해줄 수 있게 해주는 확장 문법이다.