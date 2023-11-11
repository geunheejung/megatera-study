# useEffect

> 컴포넌트를 외부 시스템과 동기화할 수 있는 React Hook이다.

#### Parameters

setup:

* 이펙트 로직이 포함된 함수
* 설정 함수는 선택적으로 정리 함수를 반환 가능
* 변경된 종속성으로 다시 렌더링할 때마다 리액트는
  * 먼저 이전 값으로 정리 함수(제공한 경우)를 실행한 다음
  * 새 값으로 설정 함수를 실행한다.
* 컴포넌트가 DOM에서 제거된 후 리액트는 정리 함수를 실행

optional:

* 설정 코드 내에서 참조된 모든 반응형 값의 목록
* 리액트는 `Object.is` 메서드 비교를 사용하여 각 의존성을 이전 값과 비교한다.
* deps 인수를 생략하면 컴포넌트를 다시 렌더링할 때마다 Effect가 다시 실행된다.

### 어쩌면 Effects가 필요 없을 수 도 있다.

> 외부 Effect와 동기화 하는 것이 아니라면 ,Effect는 필요 없을 수 있다.

이펙트는 리액트 패러다임을 벗어날 수 있는 탈출구이다.

* 리액트를 "외부로" 나가서 컴포넌트를 리액트가 아닌 위젯, 네트워크 또는 브라우저 DOM과 같은 외부 시스템과 동기화할 수 있다.

외부 시스템이 관여하지 않는 경우(ex: 일부 props, state가 변경될 때 컴포넌트의 상태를 업데이트 하려는 경우)에는 Effect가 필요하지 않다.

* 불필요한 이펙트를 제거하면 코드를 더 쉽게 따라갈 수 있고,&#x20;
* 실행 속도가 빨라지며
* 오류 발생률이 줄어든다.

#### 렌더링을 위한 데이터를 변환하는 데 effects가 필요하지 않다.

* 목록을 표시하기 위해 필터링 > 목록이 변경될 때 상태 변수를 업데이트 하는 Effect > 렌더링이 연속하여 발생함

상태를 업데이트할 때

1. 리액트는 먼저 컴포넌트 함수를 호출해 화면에 표시될 내용을 계산
2. React는 이러한 변경 사항을 DOM에 "커밋"하여 화면을 업데이트
3. 그런 다음 React가 Effect를 실행한다.

\= "커밋"하기 전에, 재조정 과정에서 변경의 필요한 부분만 구한 다음 커밋으로 넘어간다.

#### 프로퍼티가 변경되면 모든 상태 재설정

1. ProfilePage 컴포넌트는 userId props를 받는다.
2. 페이지에는 댓글 입력이 포함되어 있으며, 댓글 상태 변수를 사용하여 해당 값을 보관한다.

```javascript
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Avoid: Resetting state on prop change in an Effect
  // Effect에서 props의 상태 변화에 의해 state 재설정
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```

이는 비효율적인데, ProfilePage와 그 자식들이 먼저 오래된 값으로 렌더링 한 다음 다시 렌더링하기 때문이다. 만약 ProfilePage 가 댓글 UI안에도 있다면 복잡성과 함께 배가 될 것이다.

직접적으로 Effect에서 상태 변화에 대해 명령하지 않고, 명시적인 Key를 전달하여 각 사용자의 프로필이 개념적으로 다른 프로필임을 React에게 알리자.

```jsx
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```

재조정 과정에서 Key의 변화에 의해 컴포넌트를 다시 그려낸다. 이로 인해 comment는 알아서 초기화 된다.

React는 동일한 컴포넌트가 동일한 레벨에서 렌더링될 때 상태를 보존한다. React가 State를 유지할 수 있는 조건은 동일한 호스트 트리 내에서의 변화이다.&#x20;

Profile Component 에 userId 를 Key로 전달하면, React가 userId가 다른 두 개의 Profile 컴포넌트를 상태를 공유해서는 안 되는 두 개의 다른 컴포넌트로 취급하도록 요청하는 것 이다.

#### Props가 변경될 때 일부 상태 조정하기.

```jsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // 🔴 Avoid: Adjusting state on prop change in an Effect
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
}
```

앞서 예제와 비슷하다. 특정 데이터에 의해 기존의 상태를 업데이트 해야하는 경우이다. 새로운 배열을 받으면 List의 상태를 초기화 하는 흔한 예제이다.

> 이것 역시 이상적이지 않다. 항목이 변경될 때마다 목록과 그 하위 컴포넌트는 처음에 오래된 선택 값으로 렌더링 된다.
>
> 그런 다음 React는 DOM을 업데이트하고 Effects를 실행한다. 그 이후 또 한번 상태 변화로 인해 렌더링이 트리거 된다.

Effect를 삭제하는 것으로 시작하자. 대신 렌더링 중에 직접 상태를 조정하자.

```javascript
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);

  // Better: Adjust the state while rendering
  const [prevItems, setPrevItems] = useState(items);
  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
  // ...
}
```

이렇게 이전 렌더링의 정보를 저장하는 것은 이해하기 어려울 수 있지만, 이펙트에서 동일한 상태를 업데이트하는 것 보다 낫다.

React는 아직 List 자식들을 렌더링하거나 DOM을 업데이트 하지 않았기 때문에 오래된 선택 값의 렌더링을 건너 띌 수 있다.

```javascript
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

Key를 사용하여 모든 상태를 초기화하거나 렌더링 중에 모든 상태를 계산할 수 있는지 항상 확인하자.

선택된 항목을 저장(및 재설정)하는 대신 선택한 항목 ID를 저장하는 방법으로 바꿀 수 있다.

이제 상태를 "조정"할 필요가 없다. 선택한 ID를 가진 항목이 목록에 있으면 선택된 상태로 유지된다.

그렇지 않은 경우 일치하는 항목을 찾을 수 없으므로 렌더링 중 계산된 선택 항목은 Null이 된다.

#### Chains of computations : 연쇄 계산

```javascript
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 서로를 트리거 하기 위해서만 상태를 조정하는 Effects Chains
  // 🔴 Avoid: Chains of Effects that adjust the state solely to trigger each other
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1)
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (round > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  // ...
```

때때로 다른 상태에 따라 각각 상태를 조정하는 Effect를 체인으로 연결하고 싶은 유혹을 느낄 수 있다.

#### 두 가지 문제

**(1) 매우 비효율적이다.**

* 컴포넌트(및 그 자식)은 체인의 각 세트 호출 사이에 다시 렌더링해야 한다.
  * 최악의 경우 card -> 렌더 -> cardCount -> 렌더 ...

**(2) 코드가 발전함에 따라 작성한 "체인"이 새로운 요구 사항에 맞지 않는 경우가 발생한다.**

만약, 게임 이동의 기록을 단계별로 살펴볼 수 있는 방법이 추가도니다면. 각 상태 변수를 과거의 값으로 업데이트하여 이를 수행할 수 있다. -> 하지만 카드 상태를 과거의 값으로 설정하면 이펙트 체인이 다시 트리거되고 표시되는 데이터가 변경된다.&#x20;

\= 딱딱하고 취약하다.

```javascript
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Calculate what you can during rendering
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Calculate all the next state in the event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount <= 3) {
        setGoldCardCount(goldCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRound(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }

  // ...
```

대신 렌더링 중에 가능한 것을 계산하고, 이벤트 핸들러에서 상태를 조정하는 것이 좋다.

* 각자 조금 더 적절한 위치로&#x20;

\= 훨씬 더 효율적이다. 또한 게임 기록을 볼 수 있는 방법을 구현하면 다른 모든 값을 조정하는 이펙트 체인을 트리거하지 않고도 각 상태 변수를 과거의 움직임으로 설정 가능하다



> 이펙트 핸들러 내부에서 상태는 스냅샷처럼 동작한다.
>
> setRound(round + 1) 을 호출한 후에도 라운드 변수는 사용자가 버튼을 클릭한 시점의 값을 반영한다.

이벤트 핸들러에서 직접 다음 상태를 계산할 수 없는 경우도 있다.

> 여러 개의 드롭다운이 있는 Form에서 드롭다운 옵션이 이전 드롭다운의 선택된 값에 따라 달라질 경우 Effects Chains이 적합하다.(네트워크와 동기화)

### 요약

* 렌더링 중에 무언가를 계산할 수 있다면 Effect가 필요하지 않다.
* 비용이 많이 드는 계산을 캐시하려면 useEffect 대신 useMemo를 추가하자.
* 전체 컴포넌트 트리의 상태를 재설정하려면 다른 KEY를 전달하자.
* 소품 변경에 대한 응답으로 특정 상태 Flag를 재설정하려면 렌더링 중에 설정하자.(Effect에서 말고, 렌더링 중에 설정 시 커밋 전에 다시 재조정되므로 훨씬 효율적이다.)
* 컴포넌트가 표시되어 실행되는 코드는 Effects에 있어야 하고, 나머지는 이벤트에 있어야 한다.
  * 해당 로직의 이유가 무엇인지, 누구랑 연결되었는지 판단
* 여러 컴포넌트의 상태를 업데이트해야 하는 경우 단일 이벤트 중에 수행하는 것이 좋다.
* 여러 컴포넌트의 상태 변수를 동기화하려고 할 때마다 상태를 올리는것을 고려하자.
  * 어쩌면 해당 상태는 Props로 올려야할지도 모른다.
* Effects로 데이터를 가져올 수 있지만 경쟁 조건을 피하기 위해 Cleanup을 구현해야 하낟.
  * 해당 로직의 진입점은 컴포넌트의 렌더링이므로, 불필요한 호출을 막기 위해



















