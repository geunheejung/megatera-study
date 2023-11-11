# CORS(동일 출처 정책)

동일 출처 정책은 한 출처에서 로드한 문서 또는 스크립트가 다른 출처의 리소스와 상호 작용하는 방식을 제한하는 중요한 보안 메커니즘.

### Origin

* 웹 콘텐츠의 출처는 해당 콘텐츠에 엑세스 하는데 사용되는 URL의 스키마(프로토콜), 호스트 이름(도메인) 및 포트에 의해 정의 된다.
* 스키마, 호스트 이름 or 포트 가 모두 일치하는 경우에만 두 개체의 출처가 동일하다.
* 일부 작업은 출처가 동일한 콘텐츠로 제한되며, 이 제한은 CORS를 사용하여 해제할 수 있다.

#### Examples

```
http://example.com/app1/index.html
http://example.com/app2/index.html

http://example.com:80
http://example.com

// 서로 다른 호스트
http://example.com/app1
https://example.com/app2


// 서로 다른 스키마
http://example.com
http://www.example.com
http://myapp.example.com

// 서로 다른 포트
http://example.com
http://example.com:8080
```

### File origins

최신 브라우저는 일반적으로 `file:///` 스키마를 사용하여 로드된 파일의 원본을 불투명한 원본으로 처리한다. 즉, 파일에 같은 폴더에 있는 다른 파일이 포함되어 있는 경우 파일이 동일한 출처에서 온 것으로 간주하지 않고 CORS 오류를 트리거할 수 있다.

URL 사양에 따르면 파일의 출처는 구현에 따라 다르며, 일부 브라우저에서는 보안에 영향을 미치는 경우에도 동일한 디렉토리 또는 하위 디렉토리에 있는 파일을 동일한 출처로 취급할 수 있다.



### Cross-origin network access

동일 오리진 정책은 XMLHttpRequest 또는 \<img> 요소를 사용할 때와 같이 서로 다른 두 오리진 간의 상호 작용을 제어한다.

이러한 상호 작용은 일반적으로 세 가지 범주로 분류된다.

* Cross-origin writes are typically allowed(교차 오리진 쓰기는 일반적으로 허용) : 링크, 리다이렉션, 양식 제출 등이 그 예이다. 일부 HTTP 요청은 preflight 가 필요하다.
* Cross-origin embedding is typically allowed(교차 오리진 임베딩은 일반적으로 허용)
* Cross-origin reads are typically disallowed, but read access is often laked by embedding.\
  (교차 출처 읽기는 일반적으로 허용되지는 않지만, 임베딩을 통해 읽기 엑세스가 유출되는 경우가 많다.)
  * 임베드된 이미지의 크기, 임베드된 스크립트의 동작 또는 리소스 가용성을 읽을 수 있음

#### How to allow cross-origin access

* CORS를 사용한다.&#x20;
* CORS는 서버가 브라우저에서 콘텐츠 로드를 허용해야 하는 다른 호스트를 지정할 수 있도록 하는 HTTP의 일부이다.

#### preflight

* 브라우저가 교차 출처 리소스를 호스팅하는 서버에게 실제 요청을 허용할지 확인하는 메커니즘



1. 서버쪽에서 미들웨어를 사용하여 cors 를 허용해줄 수 있음.

```javascript
import express from 'express';
import cors from 'cors';

const app = express();

app.use(cors());
```

2. 또는 프론트 쪽에서 proxy 서버 또는 request header에 명시해줄 수 있음.

서로 다른 출처가 자원 접근에 대해 허용한다고 설정해줘야 한다.

















