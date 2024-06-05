프로젝트를 생성하면 다음과 같은 코드가 생성된다.
```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import './index.css'
import App from './App'
import reportWebVitals from './reportWebVitals'
import '@fontsource/material-icons'

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement)
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals()
```

`<App />`  같은 경우에는 리액트 프레임워크의 컴포넌트이고
`<React.StrictMode>` 같은 경우에는 코드가 잘못되었는지 판단하여 적절한 오류 메시지를 보여주는 주체이다
`reportWebVitals()` 같은 경우에는 앱의 성능을 측정하는 기능으로, 리액트 개발과는 직접 관련이 없다.


React 17 Version에서는  다음과 같이 구성되어 있었는데
```typescript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'

ReactDOM.render(<App />, document.getElementById('root'))
```

서버 사이드 렌더링 기능을 향상하기 위해 React 18 Version은 위위와 같은 코드로 변견된것과 관련 있습니다.