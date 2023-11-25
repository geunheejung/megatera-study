# Redux 따라하기

### TSyringe

***

싱글톤으로 사용하려 하는 객체의 대하여 타입이 정의되어 있어야 한다.

![](../.gitbook/assets/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA\_2023-11-25\_08.59.30.png)

Redux의 경우, 이러한 과정 없이 초기 state만 지정해주고 초기화만 해주면 끝이지만, TSyringe는 위 과정이 하나 더 추가 된다.

Store인데, 거기에 +a “우리 초기 상태는 이것이고, 리듀서는 이거야” 라고 정의함.

```tsx
class Store {
	// ...
	state = {};

	reducer = (state, action) => {
	  return { ...state };
	}
	// ...
}
```

기존의 상태값에 대하여 마다마다 멤버 변수로 관리하지 않고, 객체로 관리함

### useDispatch

***

```tsx
import { ActionTypes } from "../stores/Store";
import useStore from "./useStore"

export default () => {
  const store = useStore();

  return (actionType: ActionTypes) => {
    store.dispatch({ type: actionType });
  }
}

// 사용
// as-is
const handleClickDecrease = () => {
  store.dispatch({ type: "descrease" });
};
//to-be
const handleClickDecrease = () => {
  dispatch("descrease");
};
```

왜 굳이 분리했을까? 기존에는 store에 dispatch를 하기 위해서 store를 알아야 하고, store를 안다는것은 dispatch만을 원하지만 그 이상의 것을 알게 된다는 것이고, 알게 된다. 그리고 알게 되는것이 많아질수록 의존성이 높아진다. 추가로 dispatch의 대해 분리 하였기에 캡슐화를 통해 사용성 또는 코드의 명확함을 늘릴 수 있고, 변경에 있어 용이하다.

### useSelector

```tsx
type Selector<Value> = (state: State) => Value;

const useSelector = <Value>(selector: Selector<Value>) => {
  const store = useStore();

  return selector(store.state);
}
```

```tsx
// 사용
// as-is
const { count } = store.state;
// to-be
const count = useSelector(getCount);
```

기존에는 store에 직접 접근하여 값을 얻어야 했다. dispatch를 분리 하였을 때와 동일한 장점이 있다. 추가로 파생 데이터를 구하는 로직을 한번 더 분리할 수 있다.

### slice reducer

***

```tsx
// stores/reducers.ts
const increase = (state: State, action: Action<ActionType>) => {
  switch (action.type) {
    case ACTION_TYPE.INCREASE:
      return {
        ...state,
        count: state.count + 1,
      };
    default:
      return state;
  }  
}

const descrease = (state: State, action: Action<ActionType>) => {
  switch (action.type) {
    case ACTION_TYPE.DESCREASE:
      return {
        ...state,
        count: state.count - 1,
      };
    default:
      return state;
  }  
}

const reducers = {
  increase,
  descrease
}

export default reducers;
```

```tsx
// stores/Store.ts

// ...

const reducer = (state: State, action: Action<ActionType>) => {
  
  const f = Reflect.get(reducers, action.type);

  if (!f) return state;

  return f(state, action);
};

// ...
```

컴포넌트에서 Store에 변화를 요청하는 Action을 전송할 경우, Store는 이 Action을 reducer에게 넘기고, reducer가 받은 Action에 맞는 새로운 값을 Store에게 전달한다.

reducer는 Store에게 받는 Action의 처리하므로, 여러 다양한 도메인의 Action일 경우 reducer가 복잡해진다.

이럴 경우 위와 같이 각각의 도메인에 맞는 Reducer로 분리해낼 수 있다.

### reducers

```tsx
// /stores/BaseStore.ts
export default class BaseStore<State, ActionType> {
  state: State;
  constructor(initialState: State, reducers: { [key: string]: Reducer<State, ActionType, any> }) {
    this.state = initialState;
    
    this.reducer = (state: State, action: Action<ActionType>) => {
  
      const f = Reflect.get(reducers, action.type as string);
    
      if (!f) return state;
    
      return f(state, action);
    };
  }

  reducer = (state: State, action: any) => state;

  // 현재 Store의 사용자들 모두에게 변경 사실을 전파하기 위함.
  listeners = new Set<Listener>();

  dispatch(action: Action<ActionType>) {
    this.state = this.reducer(this.state, action);
    this.publish();
  }

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }

  publish() {
    this.listeners.forEach((listener) => listener());
  }
}
```

1. 단일한 reducer의 대해 대응
2. 분할된 reducer의 대해 대응
3. 분할된 reducer의 대한 대응 코드, 즉 현재 Action에 맞는 reducer를 호출하는 부분을 Base Store로 옮김

```tsx
// /stores/Store.ts

const initialState = { count: 0 };

export type State = typeof initialState;

export const getCount = (state: State) => state.count;

@singleton()
export default class Store extends BaseStore<typeof initialState, ActionType> {
  constructor() {
    super(initialState, reducers);
  }

  updateCounter(isIncrease = true) {
    this.state = this.reducer(this.state, { type: isIncrease ? 'increase' : 'descrease', payload: 10 });
    this.publish();
  }  
}
```

* 공통된 부분을 BaseStore로 분리하여 Store가 집중해야할 범위가 좁아졌다.

### 렌더링 여파

***

```tsx
const NameCard = () => {
  const {
    state: { name },
  } = useStore();

  useEffect(() => {
    console.log(name);
  });

  return <div>Name: {name}</div>;
};

export default NameCard;

// 사용
function App() {
  return (
    <div className="App">
      <Counter />
      <Counter />
      <CounterControl />

      <NameCard />
    </div>
  );
}

export default App;
```

* NameCard가 사용하는 상태가 변경되지 않았음에도, Store의 변화에 의해 forceUpdate 변경 여파가 전달된다.

```tsx
type Selector<Value> = (state: State) => Value;

const useSelector = <Value>(selector: Selector<Value>) => {  
  const store = container.resolve(Store);

  const [state, setState] = useState(selector(store.state));

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    const update = () => {
      const newState = selector(store.state);
      if (newState !== state) {
        forceUpdate();
        setState(newState);
      }
    }

    store.addListener(update);
    
    return () => {
      store.removeListener(update);
    }
  }, [store, forceUpdate]);

  return selector(store.state);
}

export default useSelector;
```

* useStore를 통해 사용할 경우, 자동으로 구독이 되기 때문이다.
* 그러면, useSelector를 이용해, 값이 변화 하였을때만 forceUpdate를 해주면 된다.

기존의 문제는 나와 관련 없는 데이터의 변화에 의해 렌더링이 이뤄졌던 것 이였다.

그래서, 처음부터 바로 구독하는 형식이 아닌, 내가 현재 사용중인 값이 변화했을 경우에 구독하고, 실제로 내가 사용중인 값이 업데이트 될 때만 등록되도록 해줬다.

다만 위 코드에서 문제는, 의존성 배열에 state가 추가되어 있지 않기에 업데이트 된 다음 새 상태로 업데이트 되어야 하는데 이전 상태에 대한 state가 갱신되지 않음.
