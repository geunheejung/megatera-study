# TypeScript

#### ts-node

* Node.js에서 TS를 실행 시키는 도구
* ts 파일을 미리 컴파일 하지 않고 바로 실행 시키는 엔진
* JIT(Just In Time) 으로 TS -> JS 변환하여 실행 가능

#### 타입 별칭과 인터페이스 차이

* interface 가 가지는 대부분의 기능은 type 에서도 동일하게 사용 가능하다.

**가장 핵심적인 차이**

* 타입은 새 프로퍼티를 추가하도록 개방될 수 없다.
* 인터페이스의 경우 항상 확장될 수 있다.

```typescript
// Some code
interface Animal {
    name: string;
}

// Animal 확장
interface Bear extends Animal {
    honey: boolean;
}

const bear = getBear();

// 교집합을 통하여 타입 확장하기. -> 타입은 집합의 개념으로써 이해하면 좋다!
type TAnimal = { name: string };
type TBear = { honey: boolean };
type bear = TAnimal & TBear
```

#### 튜플

* 셀 수 있는 수량의 순서 있는 열거.
* 튜플은 미리 정의된 길이와 각 인덱스에 대한 타입이 있는 타입 배열 입니다.

```typescript
// Some code
const Address: [Address1, Address2, DetailAddress]
```

* 길이도, 인덱스의 대한 타입도 고정 되었다!

왜 튜플을 쓸까..

* VIP 유저에게 생일일 경우 축하 알림 및 쿠폰을 주는 이벤트를 실시!
* 알아야 할 정보

```
// Some code
유저의 고유 정보
생일 날짜
VIP 회원 여부
```

그럼 위 정보를 우리 끼리 약속 하는 것이다.

```typescript
// Some code
const userInfo1 = [1, 'email', 'id', 'password', 'isVip']
const userInfo2 = [1, 'email', 'id', 'password', 'isVip']
const userInfo3 = [1, 'email', 'id', 'password', 'isVip']
```

위와 같이 배열의 인덱스마다 데이터를 약속 했다!

* 만약, 이 규칙을 모르는 어떤 개발자가.. 아이디와 고유 인덱스, 생년월일 VIP 여부를 바꾼다면..??

```typescript
// Some code
const userInfo3 = [1, 'email', 'id', 'isVip', 'password']
```

* 이러면 에러가 난다는것은 런타임 까지 가야만 안다...

```typescript
// Some code
type UserInfoTuple = [number, string, string, string, boolean];

// initialize correctly
const userInfo1 : UserInfo = [1, 'user1@example.com' , 'abcd123' , '1999-01-01' , true];

// initialize incorrectly - ERROR!! 인덱스 마다 위치한 데이터의 타입이 다르다~
const userInfo4 : UserInfo = ['user4@example.com', 4 , true , '2002-10-25' , 'abcd123456']

```

TS 에서 튜플..

* push 메서드로 데이터 추가 시 배열의 길이가 늘어난다..
* 즉, readonly 키워드도 같이 써주자!

```typescript
// Some code
type UserInfo = readonly [number , string, string ,string , boolean]
```
