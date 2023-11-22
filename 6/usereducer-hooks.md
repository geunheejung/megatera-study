# useReducer Hooks

## 왜 사용 해야 하나요?

***

useReducer 는 React 컴포넌트에서 더 복잡한 상태 로직을 관리하는 데 일반적으로 사용되는 React Hook이다. 더 일반적으로 사용되는 useState Hooks 의 대안으로,

* 여러 개의 하위 값을 포함하는 복잡한 상태 로직이 있거나,
* 다음 상태가 이전 상태에 의존할 때 특히 유용하다.

### 1. 복잡한 상태 로직

컴포넌트의 상태 로직이 여러 하위 값이나 상호 의존성으로 인해 복잡해지면, useState Hooks로 해당 사앹를 관리할 경우 복잡하고 따라하기 어려운 코드가 될 수 있다. useReducer Hooks를 사용하면 보다 중앙 집중화되고 체계적인 방식으로 상태 업데이트를 처리할 수 있다.

### 2. 예측 가능한 상태 전환

useReducer 는 보다 예측 가능하고 제어 가능한 상태 업데이트 방식을 권장한다. 상태의 변경 사항을 설명하는 Action을 Dispatch 하면 Reducer 함수가 현재 상태에서 다음 상태로 전환하는 방법을 지정하는 방식으로 작동한다. 상태 변경을 더 쉽게 이해하고 추론할 수 있다.

### 3. 더 쉬운 디버깅

상태 전환은 Reducer 함수에 의해 결정되므로 다양한 동작에 대한 반응으로 상태가 어떻게 변화하는지 디버깅하고 추적하기가 더 쉬워진다. 이는 상태 관련 버그를 추적하기 어려울 수 있는 대규모 코드베이스에서 특히 유용하다.

### 4. Proo Drilling 피하기

컴포넌트 깊게 중첩된 경우 여러 수준의 컴포넌트를 통해 상태 및 업데이터 함수를 전달해야 하는 경우가 있다. useReducer는 Tree의 모든 컴포넌트가 액세스할 수 있는 중앙 집중식 상태 관리 솔루션을 제공

### 5. 불필요한 재렌더링 방지

useState를 사용하면 업데이터 함수가 호출할 때마다 컴포넌트가 다시 렌더링된다. 이와 대조적으로 useReducer를 사용하면 dispatch 함수를 메모리에 저장하여 dispatch 함수가 렌더링 간에 동일하게 유지될 때 불필요한 재렌더링을 방지함으로써 잠재적으로 성능 최적화가 가능하다.

### 6. 복잡한 로직을 위한 일관된 API

비동기 연산, 조건부 상태 업데이트 또는 이전 상태 값에 대한 종속성을 포함하는 복잡한 로직이 있는 경우, useReducer를 통해 이를 관리하면 일관된 API를 제공할 수 있다. 별도의 작업으로 다양한 경우를 처리하고 로직을 깔끔하고 체계적으로 업데이트 가능하다.

## 간단한 예제

***

```jsx
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>Increment</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>Decrement</button>
    </div>
  );
}
```

이 예제에서 useReducer 함수는 다양한 액션에 대한 반응으로 상태가 어떻게 변해야 하는지를 정의하고, useReducer는 Counter 컴포넌트에서 상태를 관리하고 액션을 Dispatch하는 데 사용된다.

## 단점

***

### 1. 학습 곡선

* useState 에 비해 약간의 학습이 필요하다

### 2. 보일러 플레이트

* useState 에 비해 더 많은 상용구 코드를 작성해야 한다.

### 3. 단순한 상태에는 과하다

* Boolean 값을 toggle 한다거나 단일 값을 추적하는 등 간단한 상태 로직이 있는 컴포넌트의 경우, useReducer를 사용하는 것이 과할 수 있다.

### 4. 성능 고려 사항

* 디스패치 함수를 메모화하고 성능을 최적화하는 메커니즘을 제공하지만, 특정 경우에는 useReducer로 인해 발생하는 오버헤드가 정당화되지 않을 수 있다.
* 최소한의 상태 로직이 있는 컴포넌트의 경우 성능 향상 효과가 미미할 수 있다.

### 5. 추가적인 개념적 복잡성

* useReducer를 도입하면 상태 관리에 추상화 계층이 하나 더 추가된다.

### 6. 관심사 분리

* useReducer는 컴포넌트에서 상태 로직을 분리하는 것을 권장하지만, 여러 파일이나 함수에서 우려 사항을 분리하는 결과를 초래할 수 있다.
* 이러한 분리는 대규모 애플리케이션에는 유용할 수 있지만 소규모 컴포넌트에는 불필요한 추상화로 보일 수 있다.

## 요약

***

useState(), useReducer() 중 어떤 것을 선택할지는 컴포넌트의 특정 요구사항과 상태 로직의 복잡성에 따라 달라진다. 상태 전환이 간단하고 단순한 경우에는 useState()가 더 적합할 수 있다. 그러나 더 복잡한 상태 관리 시나리오, 특히 여러 개의 하위 값이나 복잡한 로직이 포함된 시나리오의 경우 useReducer 가 더 체계적이고 유지 관리하기 쉬운 솔루션을 제공할 수 있다.
