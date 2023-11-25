# 💉 TSyringe

## singleton()

***

```tsx
import {singleton} from "tsyringe";

@singleton()
class Foo {
  constructor() {}
}

// some other file
import "reflect-metadata";
import {container} from "tsyringe";
import {Foo} from "./foo";

const instance = container.resolve(Foo);
```

* Class를 Global Container 내에서, Singleton으로 등록하는 Class Decorator 팩토리이다.

전역에서 → 하나이며, 사용할 때 `new` 키워드와 함께 인스턴스화 하지 않아도, DI Container가 인스턴스화를 알아서 해주며, 현재 Store가 다른 Store를 의존할 경우 따로 인스턴스화 하여 의존성 주입을 해주지 않아도 알아서 조립을 해준다.

아울러, Factory 기능까지 같이 있다.

## Container

***

* 제어의 역전(IoC) 컨테이너의 일반적인 원칙은 컨테이너에 토큰을 제공하고 그 대가로 인스턴스/값을 얻는 것 이다.
* 컨테이너는 대부분의 경우 토큰을 자동으로 알아내지만, 두 가지 주요 예외, 즉 인터페이스와 비 클래스 유형은 생성자 매개변수에 `@inject()`데코레이터를 사용해야 주입할 수 있다.
* 데코레이션된 클래스를 사용하려면 컨테이너에 등록해야 한다.
* 등록은 `토큰 - 프로바이더` 쌍의 형태로 이루어지므로 `토큰과 프로바이더`의 대해 잠시 논의할 필요가 있다.

### Injection Token

***

토큰은 문자열, 심볼, 클래스 생성자 또는 `DelayedConstructor` 의 인스턴스일 수 있다.

```tsx
type InjectionToken<T = any> =
  | constructor<T>
  | DelayedConstructor<T>
  | string
  | symbol;
```

### Providers

***

* 컨테이너에는 Providers 라는 개념이 있다. Providers는 D.I 컨테이너에 등록되며,
* 주어진 토큰에 대한 인스턴스를 해결하는 데 `필요한 정보를 컨테이너에 제공`한다.

### **Class Provider**

```tsx
{
  token: InjectionToken<T>;
  useClass: constructor<T>;
}
```

* 생성자가 클래스를 확인하는 데 사용된다.
* 클래스 Provider를 등록할 때 별칭(토큰이 클래스 자체가 아닌 클래스 Provider)을 만들지 않는 한 생성자 자체에서 사용하면 된다.

### Value Provider

```tsx
{
  token: InjectionToken<T>;
  useValue: T
}
```

* 토큰을 주어진 값으로 해석하는 데 사용된다.
* 상수, 이미 특정 방식으로 인스턴스화 된 사물을 등록할 때 유용하다.

### Factory provider

```tsx
{
  token: InjectionToken<T>;
  useFactory: FactoryFunction<T>;
}
```

* 지정된 팩토리를 사용하여 토큰을 확인하는 데 사용된다. 팩토리는 의존성 컨테이너에 대한 전체 엑세스 권한을 가진다.
* 사용할 수 있는 팩토리는 두 가지가 제공되지만, `FactoryFunction<T>` 시그니처와 일치하는 함수는 모두 팩토리로 사용 가능하다.

```tsx
type FactoryFunction<T> = (dependencyContainer: DependencyContainer) => T;
```

### Resolution

***

* 토큰을 인스턴스로 교환하는 프로세스이다.
* 컨테이너는 완전히 구성된 객체를 반환하기 위해 Resolved 되는 토큰의 종속성을 재귀적으로 충족한다.
* 객체가 Resolved되는 일반적인 방법은 컨테이너에서 resolve()를 사용하는것이다.

```tsx
const myFoo = container.resolve(Foo);
const myBar = container.resolve<Bar>("Bar");
```

## useCallback

```tsx
import { useCallback, useState } from "react";

const useForceUpdate = () => {
  const [state, setState] = useState(0);

  // useCallback은 파라미터로 주입된 함수를 클로져 영역에 저장한다.
  // 그말은 즉슨, forceUpdate 변수를 평가할 때, 이미 존재하는 값을 가져오는 것이다.
  // 그말은 즉슨, foreceUpdate가 다시 평가된다 하더라도, 함수의 참조는 남아있기에 여전히 같은 값을 바라본다.
  // 즉, 현재 함수의 바깥 스코프에 useCallback으로 주입한 함수가 존재한다.
  const forceUpdate = useCallback(() => setState(prev => prev + 1), []);

  return forceUpdate;
};

export default useForceUpdate;
```

## 의도를 명확하게

```tsx
const store = container.resolve(Store);
const forceUpdate = useForceUpdate();

useEffect(() => {    
  store.forceUpdates.add(forceUpdate);

  return () => {
    store.forceUpdates.delete(forceUpdate);
  };
}, [store, forceUpdate]);
```

1. store에 업데이트를 요청하는 코드의 대하여, 이를 클린업 하는것의 대해 마치 store 내부를 해킹하는 느낌이 든다.
2. 이럴 경우 굳이 store를 해킹하지 않고, 내부에 캡슐화 하는것이 더 의도가 명확하다.

```tsx
// 사용자
useEffect(() => {
  store.addListener(forceUpdate);

  return () => store.removeListener(forceUpdate);
}, [store, forceUpdate]);

// Store
@singleton()
export default class Store {
  count = 0;

  // 현재 Store의 사용자들 모두에게 변경 사실을 전파하기 위함.
  listeners = new Set<Listener>();

  update() {
    this.listeners.forEach((listener) => listener());
  }

	// 캡슐화
  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}
```

## 전역 store 사용 시, Test 진행 과정에서 이슈

***

```tsx
describe("App", () => {
  context("when press increse button once", () => {
    test("counter", () => {
      render(<App />);

      fireEvent.click(screen.getByTestId("IncreaseBtn"));

      expect(screen.getAllByText("Count: 1")).toHaveLength(2);
    });
  });

  context("when press increse button twice", () => {
    test("twice counter", () => {
      render(<App />);

      const inc = screen.getByTestId("IncreaseBtn");

      fireEvent.click(inc);
      fireEvent.click(inc);

      expect(screen.getAllByText("Count: 2")).toHaveLength(2);
    });
  });
});
```

* 각각의 테스트 케이스는 독립적이여야 한다.
* 하지만, 현재 테스트 컨텍스트를 벗어난 전역 컨텍스트의 값에 대하여 테스트를 진행할 때는 값의 변화가 예상과 다를 수 있다.

![](../.gitbook/assets/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2023-11-24\_10.51.54.png)

* “두 번의 클릭” 으로 2 의 카운트 값을 의도 했지만, 테스트 케이스가 독립적이지 못하여 다른 케이스에서 끼친 테스트 행위에 대한 영향을 받았다.

```tsx
beforeEach(() => {
  // 실제 데이터를 지우진 않으며, 기본 값으로 초기화만 해줌
  container.clearInstances();
});
```

* 이럴 경우, 테스트 케이스 시작 전에 초기화 로직을 넣어줘야 한다.

![](../.gitbook/assets/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2023-11-24\_10.54.57.png)

* 클린 앤 깔끔쓰

## 계층 분리

***

```tsx
export default function Counter() {
  const store = container.resolve(CounterStore);
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  const counterStore = useCounterStore();

  return <div>Count: {counterStore.count}</div>;
}
```

위 코드에서 역할을 나눈다면

* 사용자에게 현재 count 데이터를 볼 수 있도록 화면에 렌더링
* 사용자에게 보여줄 count 데이터를 관리하는 스토어와 연결
* count 데이터를 관리하는 스토어에게 “count 데이터가 변경되면 알려줘” 라고 등록하는 부분

여기서 계층을 나누면

* UI : 사용자에게 현재 count 데이터를 볼 수 있도록 화면에 렌더링
* Business :
  * 사용자에게 보여줄 count 데이터를 관리하는 스토어와 연결
  * count 데이터를 관리하는 스토어에게 “count 데이터가 변경되면 알려줘” 라고 등록하는 부분

로 나뉠 수 있을 것 이다.

계층을 나눔으로써, 각각의 관리에 대한 범위를 나눌 수 있고 나뉘어진 계층은 사용자(개발자)가 원하는 단일한 요구 만 책임지므로 테스트를 진행 하는데 있어서도 용이해진다.

```tsx
// Business
const useCounterStore = () => {
  const store = container.resolve(CounterStore);
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  return store;
}

export default useCounterStore;

// UI
export default function Counter() {
  const counterStore = useCounterStore();

  return <div>Count: {counterStore.count}</div>;
}
```

* UI 계층 입장에서는 데이터 연결, 데이터 변화에 의한 업데이트 에 대하여 `“내가 알빠야?”` 를 시전할 수 있다.

```tsx
export default function CounterControl() {
  const counterStore = useCounterStore();
  
  const handleClickIncrease = () => {
    counterStore.updateCount();
  };

  const handleClickDecrease = () => {
    counterStore.updateCount(false);
  };

  return (
    <div>
      {counterStore.count}
      <button
        data-testid="IncreaseBtn"
        type="button"
        onClick={handleClickIncrease}
      >
        Increase
      </button>

      <button type="button" onClick={handleClickDecrease}>
        Decrease
      </button>
    </div>
  );
}
```

* CounterControl 입장에서는 값이 바뀌는 과정에 대해서 몰라도 된다.
* 그저 사용자가 특정한 행위를 했을 때 내가 원하는 의도에 맞게 미리 CounterStore에 선언된 기능을 쓰면 그만이다. → 캡슐화에 따라 사용성 또는 표현성이 좋아질 것 이다.
