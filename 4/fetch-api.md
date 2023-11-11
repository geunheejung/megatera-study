# Fetch API

Request, Response 객체, 그리고 기타 네트워크 요청에 관련된 것들을 사용하고, CORS와 HTTP Origin 헤더 행동 등 관련한 개념도 포함 되어 있다.

콜백 기반 API인 XMLHttpRequest와 달리, Fetch API는 서비스 워커에서도 쉽게 사용할 수 있는 Promise 기반의 개선된 대체제이다.

응답은 Response 객체로 표현되며, JSON 응답 본문을 바로 반환하지는 않는다.

* HTTP 응답 전체를 나타내는 객체
* JSON 본문 콘텐츠를 추출하기 위해서는 json() 메서드를 호출해야 한다.

### Response: json() method

* Response 스트림을 받아 완료될 때까지 읽는다.
* 본문 텍스트를 JSON으로 파싱한 결과로 해결되는 프로미스를 반환한다.

메서드 이름은 json()이지만 결과는 JSON이 아니라 (1)JSON을 입력으로 받아 (2)파싱하여 (3)JavaScript 객체를 생성한 결과라는 점에 유의하자.

#### Return value

자바스크립트 객체로 resolves 한 promise이다. 이 객체는 객체, 배열, 문자열, 숫자 등 JSON으로 표현할 수 있는 모든 것이 될 수 있다.

#### 스트림이란?

위키백과

> 시간이 지남에 따라 사용할 수 있게 되는 일련의 데이터 요소&#x20;

"순차적인 데이터"

* 내용 만큼 크기가 결정
* 순차적인 데이터란 크기가 중요하지 않다.

데이터가 그냥 흘러갈 뿐 이다.&#x20;

#### 왜?

메모리를 아끼기 위해서이다. 100GB 단위의 데이터를 압축해서 zip 파일을 만들어야 할 경우, 데이터를 처리하기 위해서 100GB의 데이터 모두를 로딩하나?

그렇지 않다. 압축하는 프로그램은 100GB 의 큰 데이터를 전부 읽지 않아도 앞에서부터 차례대로 압축을 수행할 수 있다.&#x20;

"처음부터 모든 데이터를 알 필요 없이" 끝날 때까지 차례대로 처리해나가면 되는 것 이다.

스트림은 데이터가 끝이 날 수도 있고 안날 수도 있다. 압축의 경우 명확히 정해져있지만, 아닐 수 있다.

일반적으로 스트림에는 읽기 전용 스트림, 쓰기 전용 스트림이 있다.&#x20;

#### json()

Streams API의 ReadableStream 인터페이스는 Byte 데이터를 읽을 수 있는 스트림을 제공한다. Fetch API는 Response 객체의 body 속성을 통하여 ReadableStream의 구체적인 인스턴스를 제공한다.



다른 리소스에서 fetch된 HTML 조각들을 스트림 하기위해 가공의 Response를 만든다.

```javascript
fetch("https://www.example.org/").then((response) => {
  const reader = response.body.getReader();
  const stream = new ReadableStream({
    start(controller) {
      // 아래 함수는 각 data chunck를 다룬다.
      function push() {
        // "done"은 Boolean 이며 value는 "Uint8Array 이다."
        reader.read().then(({ done, value }) => {
          // 더이상 읽은 데이터가 없는가?
          if (done) {
            // 브라우저에게 데이터 전달이 끝났다고 알린다.
            controller.close();
            return;
          }

          // 데이터를 얻고 컨트롤러를 통하여 그 데이터를 브라우저에 넘긴다.
          controller.enqueue(value);
          push();
        });
      }

      push();
    },
  });

  return new Response(stream, { headers: { "Content-Type": "text/html" } });
});
```

앞서 얘기했던것처럼 순차적으로 흘러가는 데이터이다. 그렇기에 크기에 상관 없이 계속 나누어 읽을 수 있다.

Response.json()도 이와 동일하다. json 데이터의 대해 순차적으로 읽고 이를 JavaScript 에서 사용할 수 있도록 파싱해준다.



### 텍스트 디코더, 인코더

이진 데이터가 문자열이라면 어떨지 생각해보자.&#x20;

내장 객체 TextDecoder는 주어진 버퍼와 인코딩으로 값을 실제 자바스크립트 문자열로 읽을 수 있게 해준다.

```javascript
let decoder = new TextDecoder([label], [options]);
```

* label : 기본적인 인코딩 방식은 utf-8(big5, windows-1251 ...)
* options
  * fatal : true -> 잘못된 글자를 대상으로 예외를 던짐. false -> \uFFFD로 대체
  * ingoreBOM -> true : 바이트 순서 표식 무시

그런 다음 생성했던 객체를 디코딩함.

```javascript
let str = decoder.decode([input], [options]);
```

* input : 디코딩할 BufferSource
* options
  * `stream` : 많은 양의 데이터를 받아들여 decoder를 반복적으로 호출할 때도 decoding이 반복적으로 실행된다. 이런 경우 멀티 바이트 문자가 많은 데이터로 분할될 수 있다. \
    데이터 분할을 방지하기 위해 TextDecoder에 "unfinished" 문자를 입력시키고 다음 데이터가 오면 디코딩하도록 지시.

#### 요약

* 스트림이란 순차적인 데이터
* 입력에 있어, 순차적 특성을 이용하여 데이터를 점진적으로 읽어나간다.
* Response 스트림을 받아 완료될 때까지 읽는다.



























































