# Object Store

### ObjectStore

***

```tsx
export type Listener = () => void;

class ObjectStore {
  private listeners = new Set<Listener>();

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }

  protected publish() {
    this.listeners.forEach((listener) => listener());
  }
}

export default ObjectStore;
```

기존의 Redux를 모방한 방식은 단일 Store 내에서 액션과 리듀서 의 조합으로 다양한 케이스를 처리 했었다. 이번에는, 필요에 따라 여러 Store를 만드는 방식으로 진행한다.

### 값을 바꾸는 방식의 차이

***

```tsx
// 단일 Store O, dispatch 방식 O
const handleClickIncrease = () => {
  dispatch(increase());
};

const handleClickDecrease = () => {
  dispatch(descrease());
};

// 단일 Store X, dispatch 방식 X
const handleClickIncrease = () => {
  counterStore.updateCounter();
};

const handleClickDecrease = () => {
  counterStore.updateCounter(false);
};
```

dispatch 방식 보다 조금 더 명확하게 store가 제공하는 메서드를 이용하여 값을 변경 시킬 수 있다.

추가로 직접 store가 제공하는 메서드에 접근함으로 인하여 에디터의 도움을 받을 수 있다.

물론 TypeScript와 함께 사용하면, 타입 정의로 인하여 dispatch 방식으로 하더라도 자동완성의 도움과 함께 명확하게 사용 가능하긴 하다.

### 분리

***

```tsx
const useObjectStore = <T extends ObjectStore>(store: T): T => {
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  return store;
}

const useCounterStore = () => {
  const store = container.resolve(CounterStore);

  return useObjectStore(store);
}
```

```tsx
class ObjectStore {
	private listeners = new Set<Listener>();
	
	addListener(listener: Listener) {
	  this.listeners.add(listener);
	}
	
	removeListener(listener: Listener) {
	  this.listeners.delete(listener);
	}
	
	protected publish() {
	  this.listeners.forEach((listener) => listener());
	}
}

@singleton()
export default class CounterStore extends ObjectStore {
  count = 0;
  
  /**
   * count State를 업데이트 합니다.
   * isIncrease = true => 1 증가
   * isIncrease = false => 1 감소
   * @param isIncrease 
   */
  updateCounter(isIncrease = true) {
    this.count += isIncrease ? +1 : -1;
    this.publish();
  }  
}
```

공통된 부분을 분리함으로써 한번 더 집중해야할 범위를 좁힐 수 있다.
