# Location

### Location

***

* Location 인터페이스는 객체가 연결된 장소(URL)을 표현합니다.
* Location 인터페이스에 변경을 가하면 연결된 객체에도 반영되는데, Document와 Window 인터페이스가 이런 Location을 가지고 있습니다.

각각 Document.location, window.location으로 접근할 수 있습니다.

### Document.location

***

Document.location 은 Location 객체를 반환하는 읽기 전용 속성이다. Location은 document의 URL에 대한 정보를 포함하고 해당 URL을 변경하거나 다른 URL을 로딩하는 메서드를 제공한다.

Document.location은 읽기 전용 Location 객체지만, 문자열을 할당할 수도 있다.

즉, 대부분의 경우 document.location을 문자열처럼 사용 가능하다.

```tsx
document.location = '<http://a.com>'
document.location.href = '<http://a.com>'
```

서로 동의어이다. 다른 문자열을 할당한다면 브라우저가 할당된 웹사이트를 로드한다.

### Window: location property

***

Window.location 프로퍼티는 읽기 전용 속성이며, 문서의 현재 위치에 대한 정보가 포함된 Location 객체를 반환한다.

Window.location은 읽기 전용 Location 객체이지만 문자열을 할당할 수도 있다.

```tsx
location = '<http://www.example.com>'
location.href = '<http://www.example.com>'
```

서로 동의어이며, 대부분의 경우 문자열처럼 `location` 으로 작업할 수 있다.
