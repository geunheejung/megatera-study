# React Testing Library

* React 컴포넌트를 테스트하기 위해 설계된 라이브러리이다.

### 장점

#### 1. RTL가 다른 점은 테스트를 렌더링 할 때 React 컴포넌트의 인스턴스가 아닌 실제 DOM 노드를 사용한다는 점이다.

* 사용자가 웹 브라우저에서 애플리케이션을 실행하는 실제 환경과 유사한 환경에서 테스트 케이스가 실행된다는 것을 의미한다.
* 테스트 환경이 사용자가 애플리케이션을 사용하는 환경과 비슷할수록 테스트를 더욱 신뢰할 수 있다.

#### 2. 테스트가 사용자가 앱과 상호작용하는 방식과 유사해야한다(라이브러리의 철학)

* 사용자는 애플리케이션을 사용할 때 state, props와 상호작용한다는 사실을 알지 못한다.
* 함수 컴포넌트에서 훅을 사용하는지,&#x20;
* 클래스 컴포넌트와 함께 고차 컴포넌트를 사용하는지 신경쓰지 않는다.

\-> 사용자는 그저 인터페이스(버튼, 입력, 모달 등)를 보고 상호작용할 뿐이다.



그래서 올바른 props, state가 컴포넌트에서 변경되었는지 테스트하는 대신 RTL는 사용자가 보고 수행하는 작업을 테스트하도록 설계되었다.

\-> 접근 가능한 사용자 인터페이스를 구축하고 HTML을 구성할 때 모범 사례를 준수할 수 있다.



### 선언적 프로그래밍을 사용하여 테스트 작성하기

```javascript
// Some code
it("뭔가 수행한다.", async () => {
    const onSubmit = jest.fn();
    const onCancel = jest.fn(); 
    const result = render(<ComplexForm onSubmit={onSubmit} onCancel={onCancel} />);

    expect(result.getByLabelText('First Name')).toBeInTheDocument();
    expect(result.getByLabelText('Last Name')).toBeInTheDocument();

    await act(async () => {
      userEvent.click(result.getByLabelText('Over 21?'));
    });

    expect(result.getByLabelText('Favorite Drink?')).toBeInTheDocument();
});
```

1. 테스트 진행 상황을 이해하기 위해서는 각 코드 라인을 주의 깊게 읽어야 한다.

**대신 테스트가 선언적이라면 어떨까?**

1. 하는 일을 보여주는 테스트를 작성하는 대신 -> 사용자의 의도를 설명하는 테스트를 작성하면 어떨까?

```javascript
// Some code
it("뭔가 수행한다.", async () => {
    const { FirstNameInput, LastNameInput, clickIsOver21, FavoriteDrinkInput } = renderComplexForm();

    expect(FirstNameInput()).toBeInTheDocument();
    expect(LastNameInput()).toBeInTheDocument();

    await clickIsOver21();

    expect(FavoriteDrinkInput()).toBeInTheDocument();
  });
```

함수를 읽기만 해도 현재 상황을 즉시 파악할 수 있다

1. 이름과 성 입력이 document에 있는지 확인한다.
2. "21세 이상 입니까?" 체크박스를 클릭한 다음&#x20;
3. 즐겨 찾는 음료 입력이 document에 있는지 확인한다.

\-> 읽기 쉽고, 다른 테스트 케이스에서 재사용할 수 있다.













