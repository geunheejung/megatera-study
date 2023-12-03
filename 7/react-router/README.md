# React Router

## React Router

***

리액트 애플리케이션에서 탐색 및 라우팅을 처리하기 위한 라이브러리이다. URL을 기반으로 애플리케이션의 다양한 컴포넌트가 렌더링되는 방식을 정의할 수 있다.

전체 페이지를 새로고침 하지 않고도 페이지 콘텐츠가 동적으로 변경되는 단일 페이지 애플리케이션(SPA)를 만들 수 있다.

## 주요 개념

***

### BrowserRouter or HashRouter

* **BrowserRouter :** HTML5 히스토리 API를 사용하여 UI를 URL과 동기화한다. Hash 기호가 없는 URL을 제공한다. 이를 위해서는 URL 변경을 올바르게 처리하기 위한 서버 측 구성이 필요하다.
* **HashRouter :** URL의 해시 부분을 사용한다. 서버 측 구성이 필요하지 않으며 간단한 설정 또는 정적 사이트 호스팅에 자주 사용된다.

### Route

* Router Router의 기본 빌딩 블록이다. Route 컴포넌트는 URL 경로와 React 컴포넌트간의 Matching을 선언하는 데 사용된다.
* URL이 지정된 경로와 일치하면 연결된 컴포넌트가 렌더링된다.

```tsx
import { Route } from 'react-router-dom';

const MyComponent = () => <div>Hello, React Router!</div>;

const App = () => (
  <div>
    <Route path="/my-path" component={MyComponent} />
  </div>
);
```

### Link and NavLink

* **Link :** 애플리케이션에서 탐색 링크를 만드는 데 사용된다. 링크 클릭 시 전체 페이지가 다시 로드되는 것을 방지하여 원할한 사용자 경험을 제공한다.
