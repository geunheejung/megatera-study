# createElement

React Element를 생성할 수 있다. -> JSX를 작성하는 대신 사용 가능.

`const element = createElement(type, props, ...children)`

* 주어진 타입, 프로퍼티, children 로 React Element를 생성한다.

**type**

* 유효한 React Component 유형
* 태그 이름 문자열('div, 'span') | React Component(function, class, Fragment)

**props**

* 객체 | null
* null -> 빈 객체와 동일하게 취급
* React는 전달한 프로퍼티와 일치하는 프로퍼티를 가진 Element를 생성함
* props 객체의 ref, key 는 특수한 것이므로 반환된 엘리먼트의 프로퍼티로 사용 불가
  * element.ref, element.key 는 사용 가능 , element.props.ref, element.props.key 는 불가

**optional**

* children : React Element | 문자열 | 숫자 | Portal | 빈 노드 | React 노드의 배열을 포함한 모든 리액트 노드 가능

**Returns**

* type : 전달한 타입
* props : ref, key를 제외한 전달한 프로퍼티.
* ref: 전달한 참조&#x20;
* key: 전달한 key, 문자열로 강제 변환됨



일반적으로 컴포넌트에서 엘리먼트로 반환하거나 다른 요소의 자식 엘리먼트로 만듬.

엘리먼트의 프로퍼티를 읽을 수 있지만, 생성된 후에는 모든 요소를 불투명하게 처리하고 렌더링만 하는것이 좋다.

**주의 사항**

**React 엘리먼트와 프로퍼티는 불변으로 취급**

* 생성 후 내용을 직접 변경하면 안된다.
* 개발 과정에서 React는 이를 강제하기 위해 반환된 엘리먼트와 그 프로퍼티를 얕게 freeze 함

**JSX를 사용할 때 사용자 정의 컴포넌트를 렌더링하려면 대문자로 태그를 시작해야 함.**

* Something -> React.createElement(Something)
* something -> React.createElement('something)
  * 문자열이므로 기본 제공 HTML 태그로 취급된다.

자식 컴포넌트로 전달하는 엘리먼트들이 정적 엘리먼트일 경우 createElement에 파라미터로 여러 개의 파라미터를 전달해야 한다.

`createElement('h1', {}, child1, child2, child3)`

만약 자식 컴포넌트들이 동적인 경우, 세 번째 인자로 전체 배열을 전달하자.

`createElement('ul', {}, listItems)`

이렇게 하면 React가 동적 컴포넌트가 담긴 리스트의 대해 누락된 Key에 대해 경고한다.

* 정적 리스트의 경우 컴포넌트 순서가 바뀌지 않으므로 이 작업이 필요하지 않음.



