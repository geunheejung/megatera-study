# Create a declarations file

* DefaultTheme 는 기본적으로 props.theme의 인터페이스로 사용
* 기본 DefaultTheme 인터페이스는 비어있음. 이를 확장

```tsx
// import original module declarations
import 'styled-components';

// and extend them!
declare module 'styled-components' {
  export interface DefaultTheme {
    borderRadius: string;

    colors: {
      main: string;
      secondary: string;
    };
  }
}
```

### Using custom props

props 기반 스타일 조정 시, 해당 소품의 base tag / component props의 일부가 아닌 경우

type arguments를 사용하여 정의할 수 있다.

```tsx
import styled from 'styled-components';
import Header from './Header';

interface TitleProps {
  readonly $isActive: boolean;
}

const Title = styled.h1<TitleProps>`
  color: ${(props) => (props.$isActive ? props.theme.colors.main : props.theme.colors.secondary)};
`;
```

* h1 과 같은 표준 태그의 스타일드 컴포넌트에 사용자 정의 props를 전달할 시 해당 태그의 속성으로 주입하진 않는다.(속성 오류) 대신, React Props로는 전달 된다.
