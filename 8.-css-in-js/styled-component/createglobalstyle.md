# createGlobalStyle

## createGlobalStyle

전역 스타일을 처리하는 특별한 StyledComponent를 생성하는 헬퍼 함수이다.

1. styled-component는 자동으로 로컬 CSS 클래스로 범위가 지정되므로 다른 컴포넌트로부터 격리된다.
2. createGlobalStyle의 경우 이러한 제한이 제거되고 CSS 재설정이나 기본 스타일시트 같은 것을 적용할 수 있다.

children을 허용하지 않는 스타일드 컴포넌트를 반환하며, React 트리 맨위에 배치하면 컴포넌트가 렌더링 될 때 전역 스타일이 주입된다.

```jsx
import { createGlobalStyle } from 'styled-components'

const GlobalStyle = createGlobalStyle<{ $whiteColor?: boolean; }>`
  body {
    color: ${props => (props.$whiteColor ? 'white' : 'black')};
  }
`

// later in your app

<React.Fragment>
  <GlobalStyle $whiteColor />
  <Navigation /> {/* example of other top-level stuff */}
</React.Fragment>
```

GlobalStyle Component 또한 스타일드컴포넌트 이기에, `ThemeProvider` 를 사용했다면, theme props에도 접근할 수 있다.

```jsx
const GlobalStyle = createGlobalStyle<{ $whiteColor?: boolean; }>`
  body {
    color: ${props => (props.$whiteColor ? 'white' : 'black')};
    font-family: ${props => props.theme.fontFamily};
  }
`

// later in your app

<ThemeProvider theme={{ fontFamily: 'Helvetica Neue' }}>
  <React.Fragment>
    <Navigation /> {/* example of other top-level stuff */}
    <GlobalStyle $whiteColor />
  </React.Fragment>
</ThemeProvider>
```

## `css`

* 템플릿 리터럴에서 CSS를 생성하는 헬퍼 함수
* js에서 태그가 지정된 템플릿 리터럴이 작동하는 방식에 의해 → 리터럴 내부에 함수가 포함된 템플릿 리터럴을 반환하는 경우 이 함수를 사용해야 한다.
* 문자열만 사용할 시 필요 없음.

```jsx
import styled, { css } from 'styled-components'

interface ComponentProps {
  $complex?: boolean;
  $whiteColor?: boolean;
}

const complexMixin = css<ComponentProps>`
  color: ${props => (props.$whiteColor ? 'white' : 'black')};
`

const StyledComp = styled.div<ComponentProps>`
  /* This is an example of a nested interpolation */
  ${props => (props.$complex ? complexMixin : 'color: blue;')};
`
```

* 템플릿 리터럴로 전달할 수 있는 평탄화된 데이터 구조이다.
* CSS를 생략할 시, toString에 의해 텍스트로 평가될 수 있다.&#x20;
