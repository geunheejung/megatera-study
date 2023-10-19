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

### 타입 추론&#x20;

* 타입스크립트는 자바스크립트 언어를 안다. 그래서 대부분의 경우 타입을 생성해준다.(생각보다 똑똑함)
  * 사람이 임의로 타입을 할당하는 것 보다 더 똑똑할 수 있다.

```typescript
// Some code
let name = 'geuni'; // string;
const name2 = 'geuni'; // 'geuni' const는 상수로 추론되어 리터럴 타입이 된다!
```

자바스크립트가 동작하는 방식을 이해함으로써 타입스크립트는 자바스크립트 코드를 받아들이면서 타입을 가지는 타입 시스템을 구축할 수 있다.

* 타입스크립트는 자바스크립트의 슈퍼셋 이니깐!

### 유니온

* 타입스크립트에서 타입을 집합의 개념으로 이해하면 좋다.
* 유니언 타입은 여러 타입 중 하나일 수 있음을 선언하는 방법이다.
  * 합집합!

```typescript
// Some code
type MyBool = true | false; // true집합과 false집합

interface Tteokbokki {
  spicy: number;
  hasFishCake?: boolean;
}

interface Pizza {
  toping: string;
  cheeze: boolean;
}

type Food = Tteokbokki | Pizza; // Tteokbokki 집합과 Pizza 집합 중 어디에 더 많이 속하는가!

const eat = (food: Food) => {
  if ('toping' in food) {
    // food is Pizza
    console.log('피자 먹는다!')
  } else {
    // food is Tteokbokki
    console.log('떡볶이 먹는다!')  
  }
}
```

이러한 유니온 타입을 통해서 레거시 코드(지원이 종료되었거나 오래된 코드)에 대해 대처할 수 있다!!

```typescript
// Some code
type Menu = '라면' | '김밥' | '김밥에 단무지 추가(단종되었지만.. 아직 아는 사람들이 많음..)'

주문('김밥에 단무지 추가(단종되었지만.. 아직 아는 사람들이 많음..)')
```

### 교집합(Intersection Type)

```typescript
// Some code
type JuniorDeveloper = {
   react: boolean;
}
type InterMDeveloper = JuniorDeveloper & {
   typeScript: boolean;
}
type SeniorDeveloper = JuniorDeveloper & InterMDeveloper & {
   jest: boolean;
}

let me: JuniorDeveloper = {
   react: true,
   typeScript: true, // ERROR!!! ㅠㅠ
}
```

타입스크립트는 주로 기존 객체 타입을 결합하는 데 사용되는 교차 유형을 제공 한다!

### 제네릭!

1. 데이터의 형태가 매번 명확할 순 없다.&#x20;
2. 데이터의 형태 중에 일부분은 다른 타입으로 받고 싶을 경우가 있다

제네릭은 타입의 대하여 사용자에게 정할 수 있게 해준다. 여태까지 제공자가 타입을 정했다. 숫자를 보내라든지, 문자를 보내라든지..

제네릭을 통해서 사용자가 타입을 전달 함으로써 타입의 대한 재활용성과 유연함을 가질 수 있게 해준다.

타입 스크립트가 어렵게 느껴지는 이유는 특정한 데이터 또는 함수의 대해 타입을 정의할 때 각각의 케이스 별로 타입 체크 되도록 하기가 어려울 때가 많다.&#x20;

제네릭은 이때 유연하게 해결할 수 있게 도와준다!

그리고 이는 타입 스크립트의 똑똑한 타입 추론과 타입 내로잉이 합쳐져 특정한 상황에서 동적으로 할당되는 타입 변수의 의미로 제네릭을 사용하여 타입을 추론 시켜놓을 수 있다!

{% embed url="https://velog.io/@geuni1013/Promise.all-%ED%83%80%EC%9E%85-%EC%A0%95%EC%9D%98" %}

