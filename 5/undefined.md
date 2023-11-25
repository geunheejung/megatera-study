# 요약

## 요약

***

JEST라는 도구를 이용하여 테스트를 할 수 있고, 단위 테스트 부터 TDD 방법론까지 배웠다.

실제로 리액트로 만들어진 프론트엔드 테스트할 것 이니 React를 위한 테스트 도구인 RTL에 대해 배웠다.

UI 테스트를 많이 하는 것이 중요하기 보다는 로직들을 가능한 많이 분리하는게 중요하다.

보여지는 UI보다 UI와 연결된 비지니스 로직들은 서로 맞물려 있을수록 복잡해진다.

ui테스트를 너무 깐깐하게 하면 변화가 잦은 ui와 테스트가 깨지는 케이스가 많아진다.

ui는 간단하게 연결이 된다만 하고,

연결되는 큰 부분 중요한 부분은 따로 만드는것을 강력하게 추천한다.

범용으로 사용되는 요소가 자주 변경된다는것은 매우 큰 영향이긴 하다.

영향력을 줄일 수 있도록 처음부터 작게 만들거나

또는 정말 중요하다 생각하면 깨지는 모든 테이스를 그때 고치는게 더 맞을 수 있다.

가능하면 비지니스 로직은 따로 분리,

공용인 요소는 테스트를 깐깐하게 만들기

나머지 요소들은 Mocking 또는 msw 를 통해서 현재 시점과 벗어난 백엔드와 같은 시점을 Mocking하여 테스트 환경을 구성하고 진행하자.

하지만 본질을 잊지 말자. Mocking이 주요한게 아니라, 우리가 원하는 의도를 테스트 하기 위해서 사용하는 도구이다.

마지막으로 E2E 테스트를 통해서 결과물에 대한 명확성을 체크하자.

도구는 나의 생산성을 높임과 동시에 의사소통에도 도움이 된다. 아울러 테스트를 통해서 제대로 만들 수 있는 기회도 얻을 수 있다.