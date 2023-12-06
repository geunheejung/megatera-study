# CSS-in-JS

## 요약

* 컴포넌트로 생각하기 : 더이상 스타일시트의 묶음을 유지보수 할 필요가 없다. CSS-in-JS는 CSS 모델을 문서 레벨이 아니라 컴포넌트 레벨로 추상화한다.

## JSS는 CSS보다 더 강력한 추상화이다.

* JSS의 아이디어는 정반대이다. 로직과 스타일링에 하나의 언어만 사용하면서 두 언어의 장점을 모두 누리자는 것이다.
* CSS가 컴포넌트와 애플리케이션을 위해 설계된 것이 아니다.
* 확장성, 유지보수성이 뛰어난 CSS를 작성하는데 필요한 모든 것을 갖추고 있다.
* JavaScript를 CSS 규칙을 설명하는 언어로 활용하고 잘못된 부분은 건너뛰면 된다.
* JSS는 깔끔하고 추론하기 쉬운 방식으로 그렇게 할 수 있도록 도와주는 도구이다.

JS를 사용하여 스타일을 선언적, 유지보수 가능한 방식으로 설명한다.

### Inline Style과 CSS-in-JS 는 다르다.

```jsx
const textStyles = {
  color: white,
  backgroundColor: black
}

<p style="color: white; backgrond-color: black;">inline style!</p>

//

import styled from 'styled-components';

const Text = styled.div`
  color: white,
  background: black
`
<p class="hash136s21">Hello CSS-in-JS</p>
```

CSS-in-JS는 DOM 상단에 \<style> 태그를 추가했고, 인라인 스타일은 DOM 노드에 속성으로 추가했다.

### 왜 중요할까?

1. 모든 CSS 기능을 JS 이벤트 핸들러로 지정할 수 있는 것은 아니다. → 온전히 CSS의 기능을 구현할 수 없음.
2. CSS in JS를 사용하면, 손쉽게 모든 CSS의 힘을 그대로 누릴 수 있다. → 실제 CSS가 생성 되기에, CSS의 기능을 온전히 누릴 수 있다.

## CSS-in-JS 사용 시 장점

* 컴포넌트로 생각 : CSS 모델을 문서 레벨이 아니라 컴포넌트 레벨로 추상화한다.(모듈성)
* 진정한 분리 법칙 : 스코프가 있는 선택자로는 충분하지 않다. CSS에 명시적으로 정의 하지 않는 경우, 부모 요소에서 자동으로 상속되는 속성이 있다.
* 스코프가 있는 선택자 : CSS는 하나의 전역 네임스페이스만 있다. → 선택자 충돌
* 벤더 프리픽스
* 코드 공유
* 현재 화면에 사용중인 스타일만 DOM에 있다.
* 죽은 코드 제거

## CSS-in-JS의 주요 이점

1. 스타일은 지역 스코프이다.
2. 스타일은 적용되는 컴포넌트와 함께 배치된다.
3. 스타일은 자바스크립트 변수를 사용할 수 있다.

## CSS Modules

* 장점:
  * 스코프 스타일과 코로케이션을 제공한다.
  * 그리고 일반 CSS 파일로 컴파일되므로 사용에 따른 런타임 성능비용이 없다.
* 단점 :
  * 여전히 일반 CSS
  * 일반 CSS에는 DX를 개선하고 코드 중복을 줄이는 기능이 아직 부족하다

## Sass Modules

* 장점:
  * 런타임 비용 없이 지역 스코프의 CSS 모듈 스타일, Sass의 강력한 빌드시간 장점을 누릴 수 있다.
* 단점:
  * CSS-in-JS의 이점(3) : 스타일에서 자바스크립트 변수를 사용하는 기능 을 잃게 된다.
    * export 블록을 사용하여 DRY하게 코드 작성이 가능하긴 하다.

## 유틸리티 클래스

```jsx
<FlexH alignItems="center">...</FlexH>
```

* 매우 일반적인 스타일을 적용하는 것이 덜 편리하다.

```jsx
<div className="d-flex align-items-center">...</div>
```

* 요소에 단일 CSS 프로퍼티를 설정하는 CSS 클래스를 의미한다.

## CSS-in-JS

* JavaScript를 사용하여 컴포넌트의 스타일을 지정하는 스타일링 기법이다.
  * js 파싱 → CSS 생성(보통은 `<style> Element`로) → DOM에 첨부

JavaScript를 사용하여 스타일을 ‘선언’적이고 ‘유지 관리’하기 쉬운 방식으로 설명함으로써 **CSS를 컴포넌트**

**준** 자체로 추상화 할 수 있다.

```jsx
import styled from 'styled-components';
// Create a component that renders a <p> element with blue text
const BlueText = styled.p`
  color: blue;
`;

<BlueText>My blue text</BlueText>
```

* css-in-js 는 기존 css 기술로는 구현하기 어려운 부분을 지원해준다.(조건문만으로 스타일 동적 지정)
* 또한 JavaScript와 동일한 블록에 CSS를 캡슐화하여 해당 모듈로만 범위를 지정하는 모듈식 코드를 작성할 수 있다.

## 왜 CSS-in-JS인가?

1. [범위가 지정된(scoped) 스타일](https://www.notion.so/CSS-in-JS-3da6183df39449daa554078f96f7590f?pvs=21)
2. 필수적인(critical) CSS
3. 더 똑똑한 최적화
4. 패키지 관리
5. 브라우저가 아닌 환경의 스타일링

### 범위가 지정된(scoped) 스타일

* 스타일의 유지보수를 보다 쉽게 만들려는 시도를 해왔다.
* BEM : `Block__elemen—modifier` 패턴으로 제한함으로써 최적화를 이루려고 하는 이름 만들기 규칙
  * BEM 스타일의 코드베이스에서 개발자는 항상 BEM 규칙을 따르려고 해야 한다.
  * 이는 엄격하게 지켰을 경우 잘 작동한다.

그런데 범위 지정이라는 근본적인 작업이 단지 규칙 하나에만 의존해야 하는가? 그들이 확실히 말하든 아니든 대부분의 CSS-in-JS 라이브러리들은 BEM 기반의 사고방식을 따르면서 각각의 UI에 스타일을 지정하려고 한다.

```jsx
import { css } from 'glamor'

const title = css({
  fontSize: '1.8em',
  fontFamily: 'Comic Sans MS',
  color: 'blue'
})

console.log(title)
// → 'css-1pyvz'
```

* CSS 선택자는 어디에도 존재하지 않는다.
* 직접 선언된 선택자 래퍼런스는 존재하지 않는 대신 라이브러리가 자동으로 만들어준다.
* 라이브러리가 래퍼런스를 관리 → 전역에서 클래스 이름이 충돌할 일이 없다 → 우리가 직접 클래스에 BEM과 같은 규칙을 바탕으로 접두어를 붙일 필요가 없다.

이 선택자의 범위(scope)는 주변 코드의 범위 규칙을 따른다. 코드가 모듈화 되어있고, 이 선택자들이 앱의 다른 영역에서 사용하고 싶다면 JavaScript 모듈로 변환 후 필요한 곳에서 불러와 사용하면 된다.

모듈화된 선택자를 사용해서 어떤 코드에서도 주어진 스타일을 쉽게 추적할 수 있다는 사실은 → 코드 베이스를 지속해서 유지 보수성이 있게 한다는 측면에서 무척 강력하다.

> 단순한 규칙의 사용 → 스타일 범위가 기본 지정 되도록 강제하는 방향으로 나아가 → 스타일 코드의 기본 품질을 향상. → BEM은 선택 가능한 옵션(opt-in)에서 → 시스템에 포함된(baked-in) 규칙이 됨.

### 인라인(inline) 스타일이 아닌 CSS 직접 생산

초기에 대부분의 CSS-in-JS 라이브러리들은 각각의 요소에 스타일을 직접 붙이는 방식을 사용함. 이 방식의 큰 결점은 그 **‘스타일’ 속성이 CSS가 할 수 있는 모든 것을 할 수 없다는 점.**

새로운 라이브러리들은 과거의 방식을 사용하는 대신 런타임에서 전역에 스타일을 추가하고 제거하는 **동적인 스타일 시트의 사용에 집중.**

```jsx
const styles = {
  button: {
    padding: '10px',
    '&:hover': {
      background: 'blue'
    }
  },
  '@media (min-width: 1024px)': {
    button: {
      padding: '20px'
    }
  }
}
```

* 컴포넌트 라이브러리들과 잘 결합한다는 약간의 장점
* 컴포넌트와 관련된 스타일에 집중함으로써 스타일이 코드 레벨에서 더 밀접하게 통합 → 사용자는 효과적으로 BEM을 논리적인 결론으로 받아들임.

다만, 스타일과 컴포너트가 너무 강하게 연결 → CSS-in-JS 커뮤니티의 많은 사람은 스타일 추출, 명칭, 컴포넌트의 재사용이 가지는 중요성을 놓치고 있다 느낌.

### 범위가 지정되지 않은 스타일

#### Styles Components

* 스타일시트를 생성하는 대신 컴포넌트에 스타일이 직접 연결된 형태로 만들도록 강제함.

```jsx
import styled from 'styled-components'

const Title = styled.h1`
  font-family: Comic Sans MS;
  color: blue;
`

<Title>Hello World!</Title>
```

#### Glamorous

* 기존 요소에 클래스를 붙이지 않고, 생성된 컴포넌트를 단순히 렌더링한다.

```jsx
import glamorous from 'glamorous'

const Title = glamorous.h1({
  fontFamily: 'Comic Sans MS',
  color: 'blue'
})
```

* 문자열 대신 객체를 사용하여 라이브러리가 CSS 문법 분석기(parser)를 사용할 필요가 없도록 했으며 그 결과 라이브러리의 크기가와 성능 지표가 향상됨.
* 어떤 문법을 사용하든 이 두 방식으로 만들어진 스타일은 ‘범위가 지정되어 있지 않다’
* 그들은 컴포넌트와 결코 분리될 수 없다.
* React와 같은 라이브러리를 사용할 때 컴포넌트는 기본적인 구성 요소이며 우리 스타일의 일부가 되었다.

“만약 앱의 모든 것을 컴포넌트로 표현한다면, 스타일이라고 그러지 않을 이유가 있는가?”

### 필수적인(critical) CSS

* 현재 페이지 렌더링에 필수적인 스타일을 도큐먼트 헤드에 추가해서 최초 로딩 시간을 향상하는 방법 → 최근의 모범 사례

단 하나의 픽셀도 화면에 렌더링하기 전에 가능한 모든 시각적인 스타일을 불러오도록 강제하는 일반적인 스타일 로딩 방시과는 뚜렷하게 대조적이다.

서버 렌더링 앱 개발 시, 필수 CSS 추출하는 일은 단순히 최적화 과정이 아니다.

* CSS-in-JS는 기본으로 필수적인 CSS가 우선해서 작동하도록 요구한다.

#### Aphroidite

```jsx
import { StyleSheet, css } from 'aphrodite'

const styles = StyleSheet.create({
  title: { ... }
})
// 인라인에서 사용되는 CSS 함수가 각각의 렌더링 과정에서 어떤 스타일이 사용되었는지 추적함.
const Heading = ({ children }) => (
  <h1 className={css(styles.title)}>{ children }</h1>
)

import { StyleSheetServer } from 'aphrodite';

// 모든 스타일이 JS로 선언, 페이지에 필요한 스타일을 정적인 문자열로 구성된 CSS로 쉽게 추출 가능
const { html, css } = StyleSheetServer.renderStatic(() => {
	// 서버 사이드 렌더링 시 도큐먼트의 head에 삽입 가능
  return ReactDOMServer.renderToString(<App/>);
});

// 필수적인 CSS 블록을 아래와 같은 방식으로 렌더링 가능
const criticalCSS = `
 <style data-aphrodite>
  ${css.content}
 </style>
`;
```

React에서 컴포넌트는 마크업을 자바스크립트 안에서 선언하지만, 서버에서 표준 HTML 문자열로 렌더링 될 수 있다.

어느 방법이든 클라이언트 사이드 자바스크립트는 SPA을 시작하기 위한 코드 묶음(bundle)이 필요하며, 불려져 활성화된 후 브라우저 렌더링을 시작한다.

서버상에서는 HTML, CSS렌더링은 동시에 이루어지므로 Aphrodite 같은 라이브러리는 앞서 살펴보았듯이 한 번의 호출을 통해 필수적인 CSS, 서버 렌더링 된 HTML을 능률적으로 생성할 수 있도록 도와준다.

```jsx
const appHtml = `
  <div id="root">
    ${html}
  </div>
```

이는 우리가 비슷한 방법으로 React 컴포넌트를 정적인 HTML로 렌더링할 수 있게 해준다.

CSS-in-JS를 서버에서 사용 → SPA 자바스크립트 없이 작동 → 더 빠르게 렌더링 가능

**선택자의 범위 지정과 마찬가지로 필수적인 CSS를 렌더링하는 모범 사례 → 선택 가능한 옵션 → 시스템에 포함된 요소가 됨.**

### 패키지 관리

```jsx
const styles = {
  ...rules,
  ...moreRules,
  fontFamily: 'Comic Sans MS',
  color: 'blue'
}
```

위 방식으로 스타일을 작성하기 시작한다면 앱의 다른 코드처럼 같은 패턴, 같은 도구, 같은 기반 구조, 같은 생태계 를 사용하면서 얼마든지 스타일 코드를 조합하고 공유 가능.
