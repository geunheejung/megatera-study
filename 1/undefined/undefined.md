# 환경 세팅

> 왜 어려운가?\
> 계속 바뀜. 전체적인 흐름을 파악하고 앞으로의 변경에 대응할 수 있는 능력을 키우는 게 중요함

* 현재 안정적이라 해서 앞으로도 안정적이라는 보장은 없다.
* 그럴수록 흐름을 파악하는것이 중요하다.

#### `-D` 키워드

***

```
npm i -D <package>
```

* devDependencies
* 빌드 과정에서 반영되지 않는다.
* 실제 런타임에 쓰이는 패키지외에 도구로써 스이는 패키지
* typescript 의 경우 컴파일 과정에서 타입 체크 용도로 쓰이며 JavaScript로 변환된 이후 런타임에서 동작 되기에 도구 이다.

#### npx

***
