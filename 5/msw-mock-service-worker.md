# MSW(Mock Service Worker)

## 고민

***

웹사이트를 개발하다 보면 백엔드와 프론트 간의 개발 속도 차이로 인한 문제와 API 응답 데이터에 의존하는 로직에 대한 테스트 코드 작성이 어려운 문제 등이 자주 발생한다.

이러한 문제들은 현재 진행 중인 개발에 집중하기 어렵게 만들 뿐 아니라 중복 코드와 같은 불필요한 작업을 초래한다.

## API 응답 데이터에 의존을 갖는 내부 로직에 대한 테스트코드 작성

***

API호출 로직이 포함된 코드에 대하여 검증 로직이 필요할 때 발생하게 된다.

> API를 호출과 동시에 해당 응답데이터를 가공하는 함수를 작성하거나 혹은 UI컴포넌트 의 결과가 의도한 대로 잘 나오는지 확인해야할때

중요한 로직은 순수 함수 형태로 분리해 의존성을 제거하는 것이 좋지만, 서비스가 복잡해지면 API 호출 관련 의존성을 분리하는 것이 어려워진다. 따라서, API 호출 관련 의존성이 있는 코드를 테스트할 때는 Mockup 처리를 해야하는 불편함이 생긴다.

모든 의존하는 api 호출 함수를 상위 컴포넌트부터 하위 컴포넌트까지 파악하여 각각 별도의 Mockup 처리를 해주어야 한다.

테스트 코드 작성만 해도 신경 쓸 부분이 많은데, 목업 처리로 인해 온전히 테스트 코드에만 집중하기 힘든 상황에 놓인다. 동일한 api에 대해 각자 mockup 처리하는 중복코드가 발생할 수 있다. 이러한 중복을 최소화하기 위한 방법을 또 고민해야 한다.

## MSW란?

***

Service Worker 라는 기술을 이용해서 Mockup 작업을 돕는 라이브러리이다.

서비스 워커를 이용하면 네트워크 요청이나 응답을 가로채서 조작하는것이 가능하다. MSW는 이러한 특성을 이용해서 실제 api 요청이 발생했을 시 미리 준비해둔 목업 데이터로 대신 응답을 보낸다.

### 기존 Mockup 처리방식

***

기존에는 네트워크 요청을 가로채기 위해 각각의 개발자가 네이티브 http 통신 모듈을 다른 함수로 대체하여 Mockup 처리를 하거나, Postman mockup server와 같은 목업서버를 직접 구축하여 테스트할 때 해당 서버에서 응답데이터를 받아 활용하는 방식.

### msw 채택 이유

***

기존 방식과는 다르게 네트워크 요청이 발생하면 데이터만 교체하는 방식을 사용, 복잡한 처리 없이 서비스워커를 이용해 보다 간단하게 처리 가능. msw에서는 별도의 프로덕트 내부로직의 수정 없이 브라우저 환경, 테스트 환경에서 각기 다른 mocking 상태를 만들어 줄 수 있다.

### Handler 생성

***

특정 api 경로로 요청이 시작되었을 때 우리가 의도한 Mockup Data가 사용되게 하기 위해서는 mockup 처리를 원하는 api 경로와 이에 따른 mockup data를 매핑하는 과정이 필요하다.

Express 에서 Router를 작성하는 형태와 비슷하게 생김.

```jsx
// src/mocks/handlers.ts
import { rest } from 'msw'

export const handlers = [
  /* user id를 이용해서 User 정보를 가져오는 API */
  rest.get('https://dev.api.me/user/:userId', (req, res, ctx) => {
    const { userId } = req.params
    return res(
      ctx.json({
        id: userId,
        firstName: 'John',
        age: 38,
      }),
    )
  }),
]
```

### Resolver 생성

***

Resolver는 위의 handler에서 (req, res, ctx) 형태의 코드를 일컬으며 백엔드에서 api 개발 시 작성되는 서비스 로직과 유사하다. 각 api 경로로 들어왔을때 보내진 정보를 갖고 데이터를 가공하여 반환하는 역할을 한다.

```jsx
// src/mocks/handlers.ts
import { rest } from 'msw'
import { mockUser } from './resolvers'

export const handlers = [
  /* user id를 이용해서 User 정보를 가져오는 API */
  rest.get('https://dev.api.me/user/:userId', mockUser),
]

// src/mocks/resolvers.ts
export const mockUser: ResponseResolver<RestRequest, RestContext>  = (req, res, ctx): any => {
  const { userId } = req.params
  return res(
    ctx.json({
      id: userId,
      firstName: 'John',
      age: 38,
    }),
  )
}
```

express 와 동일하게 req, res, ctx 객체 활용이 가능하며, 이곳에서는 api 호출 시 같이 기입된 파라미터, 바디, 헤더 값에 대한 활용이 가능하다. 해당 값들은 req 객체에 들어가 있다.

### JESt에서 MSW 활성화

***

```jsx
// src/mocks/server.ts
import { setupServer } from 'msw/node'
import handlers from './handlers'

const server = setupServer(...handlers)

export default server
```

node 환경에서 미리 준비해둔 핸들러를 사용하기 위해서는 msw의 setupserver 메서드를 통해 server 환경을 먼저 만들어 줘야 한다.

```jsx
import { rest } from 'msw'
import server from 'mock/server'

test('test code', () => {
  beforeAll(() => server.listen())
  afterEach(() => server.resetHandlers())
  afterAll(() => server.close())
  ...
})
```

그 다음 테스트 코드안에서 미리 만들어둔 server 를 통한 목업데이터를 사용하기 위해서는 3가지 작업이 필요하다.

* 각 테스트코드를 돌기전에 서버를 활성화 시키기 위한 `server.listen()`
* 각 테스트코드가 돌고난 이후에 서버를 초기화 시키기 위한 `server.resetHandlers()`
* 모든 테스트코드가 종료된 이후에 서버를 종료하기 위한 `server.close()`

하지만 매 테스트 코드 작성마다 넣어줘야 한다는 규칙은 다소 귀찮다.

```jsx
// src/jest.config.js
module.exports = {
  setupFilesAfterEnv: ['./src/setupTests.js'],
  ...
}
```

그래서 jest의 config로 설정해주면 테스트 코드가 매번 돌기전에 기입된 코드가 미리 실행된다.

많은 개발자들이 이러한 단위 테스트를 작성함으로써 각 컴포넌트의 무결성을 보장하려 한다. 작은 단위의 테스트는 복잡도가 낮아 정상적으로 테스트가 가능하다.

```jsx
describe('검색페이지 통합테스트', () => {
  it('검색어 입력후 엔터 키를 입력하면 검색된 앨범을 보여준다.', async () => {
    render(<Search />);

    const inputBox = screen.getByRole('textbox');

    //검색어 입력
    fireEvent.change(inputBox, { target: { value: '아이유' } });
    //API 호출
    fireEvent.keyDown(inputBox, { key: 'Enter' });

    await waitFor(() => {
      //렌더링 결과 확인
      expect(screen.queryByText('조각집')).toBeInTheDocument();
    });
  });
});
```

* 검색어 입력 컴포넌트에 특정 키워드를 입력한다.
* 입력한 검색어 키워드를 사용하여 api를 호출 하고 데이터를 가져온다.
* 가져온 데이터를 검색 결과 컴포넌트에 반영하여 렌더링한다.

‘검색을 통해 검색 결과를 가져올 수 있다’ 라는 핵심 기능의 대한 테스트를 보장하지 않으면 수정에 있어서도 치명적인 이슈가 된다.

msw : api 요청을 가로채서 사전에 설정해둔 목업 데이터를 넘겨주도록 설정해 주는 도구이다.

서버 개발자와 협의가 이뤄진 api 스펙만 있다면 미리 목업을 만들어, 실제 데이터를 호출하듯이 테스트를 해볼 수 있다.

### msw를 활용해서 진행할 수 있는 대표적인 테스트

1. 서버에서 데이터를 정상적으로 잘 가져와서 렌더링 하는가
2. 서로 다른 데이터에 따라 조건부 렌더링이 잘 되는가
3. 엣지 케이스 데이터들에 대해 처리가 잘 되어있는가
4. 네트워크 에러가 발생했을 때도 처리가 잘 되는가

검색 페이지 예시를 활용해 위 테스트를 하나씩 구현하고 검증

```
// handlers.ts
/**
 * 모킹하려하는 API를 가져와 handlers에 넣어줍니다.
 * handler를 따로 분리하는 이유는 연관된 API끼리 묶어주기 위함입니다.
 */
import searchResultHandler from './api/searchResult';

const handlers = [...searchResultHandler];

export default handlers;
```

### 엣지케이스 테스트하기

***

대부분의 에러는 예상치 못한 입력이 주어질 때 발생한다. 그래서 항상 엣지 케이스를 테스트하는 코드를 넣어야 한다. 빈 리스트가 넘어오는 경우를 생각할 수 있다.

```jsx
it('텅빈 데이터를 응답받는 경우 안내문을 출력한다.', async () => {
  /**
   * 위에서와 동일한 방법으로 Empty 데이터를 처리해 줍니다.
   */
  overrideSearchResultWithEmptyData();
  render(<Search />);

  const inputBox = screen.getByRole('textbox');
  fireEvent.change(inputBox, { target: { value: '아이유' } });
  fireEvent.keyDown(inputBox, { key: 'Enter' });

  await waitFor(() => {
    expect(screen.queryByText('검색 결과가 없습니다.')).toBeInTheDocument();
  });
});
```
