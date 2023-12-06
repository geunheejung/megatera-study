# Theming

* `<ThemeProvider>` 를 통해 완전한 테마를 지원
* 컨텍스트 api를 통해 그 아래의 모든 react 컴포넌트에 테마를 제공

렌더링 트리에서 모든 스타일 컴포넌트 는 여러 레벨 깊이에 잇더라도 제공된 테마에 엑세스할 수 있다.

```jsx
// Define our `fg` and `bg` on the theme
const theme = {
  fg: "#BF4F74",
  bg: "white"
};

// This theme swaps `fg` and `bg`
const invertTheme = ({ fg, bg }) => ({
  fg: bg,
  bg: fg
});

render(
  <ThemeProvider theme={theme}>
    <div>
      <Button>Default Theme</Button>

      <ThemeProvider theme={invertTheme}>
        <Button>Inverted Theme</Button>
      </ThemeProvider>
    </div>
  </ThemeProvider>
);
```

트리 상위의 다른 `ThemeProvider` 에서 부모 테마를 받는다. 테마 자체를 컨텍스트에 맞게 만들 수 있다.

via `useContext` React hook

```jsx
import { useContext } from 'react'
import { ThemeContext } from 'styled-components'

const MyComponent = () => {
  const themeContext = useContext(ThemeContext)

  console.log('Current theme: ', themeContext)
  // ...
}
```

via `useTheme` custom Hook

```jsx
import { useTheme } from 'styled-components'

const MyComponent = () => {
  const theme = useTheme()

  console.log('Current theme: ', theme)
  // ...
}
```
