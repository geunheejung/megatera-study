# REST API

* 컴퓨터 시스템 간의 **상호운용성**을 제공하는 방법 중 하나이다.
* **상호운용성(Interoperability)** : 하나의 시스템이 동일 또는 이기종의 다른 시스템과 아무런 제약 없이 서로 호환되어 사용할 수 있는 성질

#### 역사

1. 인터넷에서 정보를 어떻게 공유할 것 인지
2. 하이퍼 텍스트로 연결함으로써 문제를 해결
3. HTML이라는 형식으로 정보를 표현
4. 그 정보들에 대한 식별자로 URL을 만듬
5. 그 정보들을 전송하는 방법으로 HTTP라는 프로토콜&#x20;



로이 필딩 : HTTP라는 프로토콜 설계자 중 1인

* 어떻게 하면 웹을 망가트리지 않고 HTTP를 진보시킬 수 있을까
* HTTP Object Model => REST

#### 제약조건

* REST API는 REST 아키텍처 스타일을 따르는 API이다.
* 아키텍쳐 스타일이란 제약조건의 집합이다.
  * client server
  * stateless
  * cache
  * uniform interface
  * layered system
  * code on demand (optional)

#### uniform interface

* 리소스가 uri로 식별되어야 한다
* representation 전송을 통해서 리소스를 조작해야 한다
* self-descriptive messages
* hypermedia as the engine of application state (HATEOAS)

#### self-descriptive messages

* 목적지
* 어떤 문법으로 작성된 것인지

```javascript
HTTP/1.1 200 OK
Content-Type: application/json-patch+json

[ { "op": "remove", "path": "/a/b/c" }]
```

* 메시지를 봤을때 이것만으로 해석이 가능해야 REST 스럽다고 할 수 있다.

#### uniform interface 필요 이유

* 서버와 클라이언트의 독립적 진화를 위해서 필요하다.&#x20;
* 서버에서 uri 가 바뀌는 등 변화가 일어나도 클라이언트에 문제가 없어야 함

#### 대체적으로 "필딩 제약 조건" 4가지를 모두 만족하지 않고, Resource와 HTTP Verb만 도입하는 수준으로 사용

* HTTP Verb : GET, POST, PUT, FETCH 등등&#x20;
* Resource : URL에 자원을 표현
  * `/write-post -> /posts(GET, POST)`
* CRUD에 대해 HTTP Method를 대입. React는 복수, 단수로 나뉨.

















































