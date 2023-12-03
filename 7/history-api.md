# History Api

### History API

***

`history` 전역 객체를 통해 브라우저 세션 히스토리에 대한 접근을 제공한다. 사용자의 방문 기록을 앞뒤로 탐색하고, 방문 기록 스택의 내용을 조작할 수 있는 유용한 메서드와 속성을 노출한다.

### Window.history

***

`Window.history` 읽기 전용 속성은 `History` 객체로의 참조를 반환한다. History 객체는 브라우저의 세션 기록(현재 페이지를 불러온 탭 혹은 프레임이 방문했던 페이지)을 조작할 때 사용한다.

### 개념 및 사용법

***

사용자의 방문 기록을 앞뒤로 이동하는 것은 back(), forward() 그리고 go() 메서드를 사용하여 수행한다.

```tsx
history.back(); // 사용자가 브라우저 도구 모음에서 '뒤로 가기' 버튼을 클릭한 것과 동일

history.forward(); // '앞으로 가기' 버튼을 클릭한 것처럼 앞으로 이동 가능
```

#### 방문 기록의 특정 지점으로 이동

```tsx
history.go(-1);
```

세션 기록에서 현재 페이지에 대한 상대 위치로 식별되는 특정 페이지를 로드할 수 있다. (현재 페이지의 상대 위치는 `0`)

```tsx
history.go(-1); // history.back();
history.go(1); // history.forward();
```

`go()` 메서드의 또 다른 용도는 `0` 을 전달하거나 인수 없이 호출하여 현재 페이지를 새로고침하는것이다.

```tsx
// 아래 두 줄의 코드는
// 모두 페이지를
// 새로고침 합니다.
history.go(0);
history.go();
```

### 인터페이스

***

#### History

* 브라우저 세션 기록(현재 페이지가 로드된 탭 또는 프레임에서 방문한 페이지)을 조작 가능

#### PopStateEven

* popstate 이벤트의 인터페이스

`popstate` 이벤트에 대한 수신기를 할당한다. 현재 탭의 브라우저 기록 내에서 추가, 바꾸기 및 이동하는 history 객체의 몇 가지 메서드를 보여준다.

```tsx
window.addEventListener("popstate", (event) => {
  alert(
    `location: ${document.location}, state: ${JSON.stringify(event.state)}`,
  );
});

history.pushState({ page: 1 }, "title 1", "?page=1");
history.pushState({ page: 2 }, "title 2", "?page=2");
history.replaceState({ page: 3 }, "title 3", "?page=3");
history.back(); // "location: <http://example.com/example.html?page=1>, state: {"page":1}"라는 알림이 발생합니다.
history.back(); // "location: <http://example.com/example.html>, state: null"라는 알림이 발생합니다.
history.go(2); // "location: <http://example.com/example.html?page=3>, state: {"page":3}"라는 알림이 발생합니다.
```
