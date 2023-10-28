# JSX Transform



브라우저는 JSX를 기본적으로 이해하지 못함 -> 대부분의 리액트 사용자는 바벨, 타입스크립트와 같은 컴파일러를 사용해 JSX 코드를 일반 자바스크립트로 변환한다.

CRA, Next.js와 같이 사전 구성된 많은 툴킷에도 내부에 JSX 트랜스폼이 포함되어 있음.



React 17 릴리즈와 함께 JSX 트랜스폼을 몇 가지 개선하고 싶었지만, 기존 설정을 깨고 싶지 않음.



#### 업그레이드 이점

새 Transform을 사용하면 React를 Import 하지 않고도 JSX를 사용할 수 있다.

* 설정에 따라 컴파일된 출력의 번들 크기가 약간 개선될 수도 있다.



#### 새로운 Transform에서는 무엇이 달라졌나요?

***

JSX를 사용하면 컴파일러가 브라우저가 이해할 수 있는 React 함수 호출로 변환한다. 이전 JSX 트랜스폼은 JSX를 `React.createElement(...)` 호출로 변환했다.

```jsx
import React from 'react';

function App() {
  // <h1>Hello world</h1>?
  return React.createElement('h1', null, 'Hello world');
}
```

1. JSX가 React.createElement로 컴파일되었기 때문에 JSX를 사용하는 경우 React가 스코프 안에 있어야 한다.
2. React.createElement가 허용하지 않는 몇 가지 성능 개선, 단순화가 있다.

이러한 문제를 해결하기 위해 React 17 에서는 **Babel 및 TS와 같은 컴파일러에서만 사용하도록** 의도된 **두 개의 새로운 진입점을 React 패키지**에 도입함.

새로운 JSX 트랜스폼은 JSX를 React.createElement로 변환하는 대신 **React 패키지의 새로운 진입점에서 특수 함수를 자동으로 가져와서 호출**한다.

```jsx
// Inserted by a compiler (don't import it yourself!)
import {jsx as _jsx} from 'react/jsx-runtime';

function App() {
  // <h1>Hello World</h1>
  return _jsx('h1', { children: 'Hello world' });
}
```

* 원래 코드에서 JSx를 사용하기 위해 React를 더 이상 import할 필요가 없다.
  * 하지만 React가 제공하는 Hook, 다른 export를 사용하려면 여전히 React를 import 해야 함.

이 변경 사항은 기존의 모든 JSX 코드와 완벽하게 호환되므로 컴포넌트를 변경할 필요가 없다.



만약, React 17 또는 추가적으로 Babel을 설정함으로써 사용하지 않는 React Import를 제거하고 싶다면 리액트에서 제공해준다.

[https://ko.legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#removing-unused-react-imports](https://ko.legacy.reactjs.org/blog/2020/09/22/introducing-the-new-jsx-transform.html#removing-unused-react-imports)
