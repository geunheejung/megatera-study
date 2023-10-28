# JSX

1. JSX는 정의된 의미론이 없는 ECMAScript에 대한 XML과 유사한 구문 확장이다.
2. 엔진, 브라우저에서 구현하기 위한 것이 아니다.
3. JSX를 ECMAScript 사양 자체에 통합하자는 제안이 아님.
4. **다양한 전처리기(트랜스파일러)에서** 이러한 **토큰을 표준 ECMAScript로 변환**하는 데 사용하기 위한 것이다.



**이 사양의 목적은**

1. 속성을 가진 트리 구조를 정의하기 위한 간결하고 친숙한 구문을 정의하는 것
2. 일반적이지만 잘 정의된 구문은 독립적인 구문 분석기 및 구문 강조 표시기가 커뮤니티 단일 사양을 준수할 수 있도록 함
3. 기존 언어에 새로운 언어를 포함시키는 것은 모험
4. 다른 구문 구현자, 기존 언어가 호환되지 않는 또 다른 구문 확장을 도입할 수 있음
5. 독립형 사양을 통해 다른 구문 확장 구현자가 자체 구문 설계 시 JSX를 더 쉽게 고려할 수 있도록 함
6. 이를 통해 다양한 새로운 구문 확장이 공존 가능

이 사양은 XML 또는 HTML 사양을 준수하려고 하지 않는다. JSX는 ECMAScript 기능으로 설계되었으며 XML과의 유사성은 친숙함을 위한것이다.



**수정된 프로덕션**

* JSX는 ECMAScript(ECMA-262) 문법에서 PrimaryExpression을 확장한다.



### JSX 소개

* JavaScript를 확장한 문법
* UI가 어떻게 생겨야 하는지 설명하기 위해 React와 함께 사용하는 것을 권장함
* JSX라 하면 템플릿 언어가 떠오를 수도 있지만, JavaScript의 모든 기능이 포함됨.

`JSX => React.Element`

#### JSX란?

* React에서는 본질적으로&#x20;
  * 렌더링 로직이 UI 로직(이벤트가 처리되는 방식, 시간에 따라 state가 변하는 방식, 화면에 표시하기 위해 데이터가 준비되는 방식 등)과 연결된다는 사실을 받아들인다.
* React는 별도의 파일에 마크업과 로직을 넣어 기술을 인위적으로 분리하는 대신
  * 둘 다 포함하는 "컴포넌트"라고 부르는 느슨하게 연결된 유닛으로 관심사를 분리한다.

#### 사담

***

앞서, Dan의 블로그에서 호스트 트리, 호스트 노드, 렌더러 라는 개념을 알았다. 리액트는 호스트 트리를 만들고 외부의 변화(데이터 패칭, 이벤트 발생) 에 따른 UI 변화를 효율적으로 관리하고 호스트 트리를 업데이트 한다. 호스트 트리는 호스트 노드들로 이뤄져있다.

여기서 호스트란 개념적 요소이다. 브라우저라면 호스트 트리는 DOM 트리, 호스트 노드는 DOM Node가 될 것이며, iOS 나 기타 다른 환경이라면 또 다른 요소가 될 것이다.

하지만 중요한것은 개념적으로 React는 실제 환경을 노드로 구성된 트리로 만들고, 변화의 대해서 관리해주며 추가적으로 반영된 호스트 트리를 실제 환경에 적용 시켜준다.

JSX가 이러한 호스트 노트를 만드는 문법적 설탕이라 생각해도 될 것 같다.

그리고 바벨이 이러한 JSX를 원하는 환경에 맞춰 바꿔주는 것이 아닐까?

React라면 createElement로

Vue라면 다른것으로?



#### Fragement

***

**plugin-transform-react-jsx**

```jsx
// Some code
/#PURE/React.createElement(
    React.Fragment, null, 
    /#PURE/React.createElement("p", null, "Hello, World"), 
    /#PURE/React.createElement("div", null, "Say")
);
```

* 트리 구조이기에, 부모 노드 없이는 정의될 수 없다?

**React Runtime: Automatic**

```jsx
// Some code
import { jsx as _jsx } from "react/jsx-runtime";
import { Fragment as _Fragment } from "react/jsx-runtime";
import { jsxs as _jsxs } from "react/jsx-runtime";
/*#__PURE__*/_jsxs(_Fragment, {
  children: [/*#__PURE__*/_jsx("p", {
    children: "Hello, World"
  }), /*#__PURE__*/_jsx("div", {
    children: "Say"
  })]
});
```

* createElement의 파라미터로 각각 전달했던 방식 -> children props로 전달







