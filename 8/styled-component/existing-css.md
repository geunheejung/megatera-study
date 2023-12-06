# Existing CSS

스타일 컴포넌트 를 기존 CSS 와 함께 사용하기로 선택한 경우 알아야 할 몇 가지 구현 세부 사항, 스타일드 컴포넌트는

* 클래스가 포함된 실제 스타일 시트를 생성하고
* classNames 프로퍼티를 통해 해당 클래스를 스타일드 컴포넌트의 DOM 노드에 첨부한다.
* 런타임 중에 문서 헤드 끝에 생성된 스타일시트를 삽입한다.

## Issues with specificity

글로벌 클래스를 스타일이 지정된 컴포넌트 클래스와 함께 적용하면 결과가 예상과 다를 수 있다. 프로퍼티가 두 클래스 모두에 동일한 특이성으로 정의된 경우 마지막 클래스가 승리한다.

```jsx
// MyComponent.js
const MyComponent = styled.div`background-color: green;`;

// my-component.css
.red-bg {
  background-color: red;
}

// For some reason this component still has a green background,
// even though you're trying to override it with the "red-bg" class!
<MyComponent className="red-bg" />
```

스타일드 컴포넌트가 기본적으로 런타임 중에 `<head>` 끝에 스타일을 삽입하기 때문에, 해당 스타일은 단일 클래스명 선택자보다 우선한다.

```css
/* my-component.css */
.red-bg.red-bg {
  background-color: red;
}
```

* 명시도를 올려주면 해결된다.
