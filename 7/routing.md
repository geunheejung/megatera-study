# Routing

### Routing

***

일반적인 웹 사이트는 URL에 따라 다른 웹 페이지를 보여준다. 하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게 함으로써 구현 가능하다.

location을 사용할 경우, 테스트가 진행되는 Node 환경에서는 window 객체가 없기에 문제가 된다. 이를 함수로 분리하여 path를 반환하는 형태로? 추상화하거나 또는 React Router를 사용할 수 있다.

BrowserRoute를 잡아주는 이유는, 환경에 따라 라우팅의 주체가 되는 값이 다르기에 그렇다.

Browser라면 path에 의해, windowl.location 에 의해 컨트롤 된다 하면

다른 환경이라면 또 다른 값에 따라 관리가 될 것이다.

#### \<BrowserRouter>

* 브라우저의 주소창에 URL을 사용하여 현재 위치를 저장
* 브라우저에 내장된 히스토르 스택을 사용하여 탐색

```tsx
import * as React from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";

const root = createRoot(document.getElementById("root"));

root.render(
  <BrowserRouter>
    {/* The rest of your app goes here */}
  </BrowserRouter>
);
```

HTML5의 History API(pushState, replaceState, popstate event)를 사용하여 URL과 UI를 동기화해주는 `Router` 이다.

BrowserRouter는 라우터 돔을 적용하고 싶은 컴포넌트의 최상위 컴포넌트를 감싸주는 래퍼 컴포넌트이기 때문에, App컴포넌트를 감싸주면 된다.

#### `Route`

***

React 라우터 앱에서 가장 중요한 부분일 것이다. URL 세그먼트와 컴포넌트, 데이터 로딩 및 데이터 변형을 연결한다.

라우트 중첩을 통해 복잡한 애플리케이션 레이아웃과 데이터 종속성을 단순하고 선언적으로 만들 수 있다.

라우터는 라우터 생성 함수에 전달되는 객체이다.

**createBrowserRouter 버전**

```tsx
const router = createBrowserRouter([
  {
    // it renders this element: 이 요소를 렌더링합니다.
    element: <Team />,

    // when the URL matches this segment: URL이 세그먼트와 일치하는 경우
    path: "teams/:teamId",

    // with this data loaded before rendering: 렌더링하기 전에 이 데이터를 로드합니다.
    loader: async ({ request, params }) => {
      return fetch(
        `/fake/api/teams/${params.teamId}.json`,
        { signal: request.signal }
      );
    },

    // performing this mutation when data is submitted to it
		// 데이터가 제출될 때 이 mutation(변화를 일으키는)를 수행합니다.
    action: async ({ request }) => {
      return updateFakeTeam(await request.formData());
    },

    // and renders this element in case something went wrong
		// 문제가 발생할 경우 이 요소를 렌더링합니다.
    errorElement: <ErrorBoundary />,
  },
]);
```

```tsx
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<Team />}
      path="teams/:teamId"
      loader={async ({ params }) => {
        return fetch(
          `/fake/api/teams/${params.teamId}.json`
        );
      }}
      action={async ({ request }) => {
        return updateFakeTeam(await request.formData());
      }}
      errorElement={<ErrorBoundary />}
    />
  )
);
```

두 스타일 모두 권장하지 않으며 동작은 동일하다. 이 문서의 대부분에서는 JSX 스타일을 사용할 것인데, 이는 대부분의 사람들이 React Router의 맥락에서 익숙한 스타일이기 때문이다.

#### MemoryRouter

***

내부에 배열로 위치를 저장한다. `<BrowserHistory>`, `HashHistory` 와 달리 브라우저의 히스토리 스택과 같은 **외부 소스에 연결되지 않는다.** 테스트와 같이 히스토리 스택을 완벽하게 제어해야 하는 시나리오에 이상적이다.

> React Router의 대부분의 테스트는 Memory Router를 소스로 사용하여 작성되었으므로, 테스트를 둘러보는 것만으로도 훌륭한 사용 예시를 볼 수 있다.

```tsx
import * as React from "react";
import { create } from "react-test-renderer";
import {
  MemoryRouter,
  Routes,
  Route,
} from "react-router-dom";

describe("My app", () => {
  it("renders correctly", () => {
    let renderer = create(
      <MemoryRouter initialEntries={["/users/mjackson"]}>
        <Routes>
          <Route path="users" element={<Users />}>
            <Route path=":id" element={<UserProfile />} />
          </Route>
        </Routes>
      </MemoryRouter>
    );

    expect(renderer.toJSON()).toMatchSnapshot();
  });
});
```
