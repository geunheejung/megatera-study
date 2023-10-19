# Parcel & ESLint

### Zero Configuration

* 특별한 설정 없이 바로 사용 가능한 빌드 도구
* 초기 최소한의 프로젝트를 진행할 때 빠르게 진행할 수 있다는 장점
* 러닝 커브가 낮다는 장점

Parcel은 캐싱을 사용하여 빌드 속도가 더 빠르다.

<figure><img src="../.gitbook/assets/스크린샷 2023-10-20 오전 5.36.57.png" alt=""><figcaption><p>두 번째 빌드 부터 매우 빨라짐</p></figcaption></figure>

### 자동 변환

* Parcel은 많은 트랜스파일러를 내장 지원하고 있기 때문에&#x20;
* 모듈 안의 설정 파일(.babelrc, .postcssrc 등)을 발견하면 이를 자동으로 변환하여 실행한다.
* Babel, PostCSS, PostHTML

#### 기본 사용법

```bash
// Some code
parcel index.html 
```

### ESLint

* Lint: 보푸라기
  * 에러가 있는 코드에 표시를 달아놓는 것
* 문법적인 오류, 안티 패턴을 찾아줌
  * 일관된 코드 스타일

