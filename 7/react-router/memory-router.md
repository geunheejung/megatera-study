# Memory Router

## Memory Router

***

탐색 기록을 메모리에 저장하는 라우터 구현이다. 브라우저의 URL 및 히스토리 API와 상호 작용하는 `BrowserRouter` 또는 `HashRouter` 와 달리 주소 표시줄의 실제 URL에 영향을 미치지 않는다.

대신 애플리케이션의 탐색 상태를 격리하려는 테스트 또는 시나리오에 자주 사용된다.

## 사용 이유

***

### Testing

```tsx
import { render, screen } from '@testing-library/react';
import { MemoryRouter } from 'react-router-dom';
import MyComponent from './MyComponent';

test('renders MyComponent', () => {
  render(
    <MemoryRouter initialEntries={['/my-path']}>
      <MyComponent />
    </MemoryRouter>
  );

  // 렌더링된 상태를 기반으로 assertions 를 수행 합니다.
});
```

* **격리 :** React 라우터를 사용하는 React 컴포넌트를 테스트할 때 테스트 환경을 실제 URL 변경으로부터 격리하고 싶을 수 있다. MemoryRouter 를 사용하면 브라우저의 주소 표시줄에 영향을 주지 않고 탐색 및 라우팅 동작을 테스트할 수 있다.

### State Management

```tsx
import { MemoryRouter, Route, Link } from 'react-router-dom';

const App = () => (
  <MemoryRouter initialEntries={['/']}>
    <div>
      <Route path="/" exact render={() => <div>Home</div>} />
      <Route path="/about" render={() => <div>About</div>} />
      <Link to="/about">Go to About</Link>
    </div>
  </MemoryRouter>
);
```

* **격리 상태 :** 애플리케이션 내에서 탐색을 위해 별도의 상태를 유지하려는 시나리오에서 MemoryRouter는 브라우저 실제 기록에 영향을 주지 않은 격리된 라우팅 상태를 제공한다.

### SSR

```tsx
import { renderToString } from 'react-dom/server';
import { MemoryRouter } from 'react-router-dom';
import App from './App';

const html = renderToString(
  <MemoryRouter initialEntries={['/']}>
    <App />
  </MemoryRouter>
);

// Use the rendered HTML for further processing
```

**격리 렌더링 :** 실제 브라우저가 없거나 URL 변경 사항을 직접 적용할 수 없는 서버 측 렌더링 환경에서는 MemoryRouter를 사용하면 가상 라우팅 상태를 기반으로 컴포넌트를 렌더링할 수 있다.

### Non-Browser Env

```tsx
import { MemoryRouter, Route } from 'react-router-dom';

const App = () => (
  <MemoryRouter initialEntries={['/']}>
    <Route path="/" exact component={Home} />
    <Route path="/about" component={About} />
  </MemoryRouter>
);
```

* **브라우저가 없는 환경 :** 표준 브라우저 기록 API를 사용할 수 없거나 예상대로 작동하지 않을 수 있는 React Native 또는 Electron 애플리케이션과 같은 특정 환경에서는 MemoryRouter가 일관된 라우팅 메커니즘을 제공한다.

### URL Simulation

```tsx
import { MemoryRouter, Route, Link } from 'react-router-dom';

const WizardStep1 = () => (
  <div>
    <h1>Wizard Step 1</h1>
    <Link to="/wizard/step2">Next</Link>
  </div>
);

const WizardStep2 = () => (
  <div>
    <h1>Wizard Step 2</h1>
    <Link to="/wizard/step3">Next</Link>
  </div>
);

// ...
```

* **URL 변경 시뮬레이션 :** 특정 시나리오를 위해 애플리케이션 내에서 URL 변경 및 탐색을 시뮬레이션해야 하는 경우, MemoryRouter를 사용하면 실제 브라우저에 영향을 주지 않고 관리할 수 있다.

## 요약

***

메모리라우터는 실제 브라우저의 URL이나 기록에 영향을 주지 않고 인메모리에서 탐색 상태를 관리해야 하는 시나리오에서 특히 유용하다.

테스트, 비브라우저 환경, 상태 관리, 서버 측 렌더링, React 애플리케이션 내 URL 변경 시뮬레이션을 위한 유연하고 격리된 라우팅 솔루션을 제공한다.
