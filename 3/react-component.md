# React Component

#### React의 강력한 특징

* 컴포넌트 기반이다.
* 자체 상태를 괸리하는 캡슐화된 컴포넌트를 빌드한 다음 이를 구성하여 복잡한 U를 만들 수 있다

### Thinking in react

***

#### 목업 만들기

구현 하려는 UI의 대해서 먼저 목업을 만드는 것이다. 목업을 만들때 가장 중요한 것은 임의로 컴포넌트를 네이밍 하는 것이다.&#x20;

<figure><img src="../.gitbook/assets/s_thinking-in-react_ui_outline (1).png" alt=""><figcaption></figcaption></figure>

그리고 컴포넌트화 하는데 있어 중요한 것은 분리의 기준이다.

우리는 컴포넌트를 아래 3가지 기준으로 나눌 수 있다.

* **Programming** : 새 함수나 객체를 생성할 지 여부를 결정할 때도 동일한 기법을 사용한다. 이러한 기법 중 하나는 단일 책임 원칙으로, 컴포넌트는 이상적으로 한 가지 일만 수행해야 한다는 것.
* **CSS** : 클래스 선택자 지정 시의 기준점을 가져오는 것.
  * 컴포넌트는 조금 덜 세분화되어 있다.
* **Design** : 디자인의 레이어를 어떻게 구성할지 고려하는 것.
  * 실제 디자인을 기준으로 컴포넌트를 나누는 것이다.

#### **Information Architecture**

데이터를 기준으로 컴포넌트를 분리하는 것이다. 대체로 아주 단순한 UI를 제외하면, 데이터와 강하게 결합 되어 있다.

상단 이미지의 UI도 데이터를 기준점으로 컴포넌트를 분리하면, 의도하지 않았음에도 단일 책임 원칙을 지키는 방향으로 진행 된다.

물론 명확하게 정해진 것은 아니지만, 데이터를 기준으로 컴포넌트를 분리하는 과정에서 서로 다른 데이터를 다룰 경우&#x20;

* 컴포넌트가 길어지거나,
* 사용함에 있어 불편해지고,
* 변화에 있어 서로에게 영향을 끼치며,
* 재활용 하기가 어려워진다.

일례로 만약 우리가 ProductTable을 만들때 ProductTable 내의 아래와 같이 컴포넌트로 나누지 않고 만든다면 아주 작은 변화에도 많은 부분을 수정 해야 한다.&#x20;

* ProductHeader
* ProductRow

```json
// Some code
[
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" }
]
```

데이터와 연결된 UI의 경우 UI에서의 변화는 데이터에도 반영 되어야 하고, 데이터의 변화는 UI에도 반영 되어야 한다.

만약 데이터가  UI 구조와 맞지 않는다 하면, 데이터를 노멀라이징 하자.

화면을 보면, category를 기준으로 그룹화하여 각각의 상품들이 그 아래 렌더링 되어 있다.

UI단에서 조건문 또는 필터로 각각 처리하기 보다는,

category를 key로 하여 데이터를 재구성하면 훨씬 수월해진다.

```json
// Some code
{
    fruits: {
        category: 'Fruits',
        products: [...],
    },
    vegetables: {
        category: 'Vegetables',
        products: [...],
    },
}
```

#### 정적으로 화면 구성하기

앞서 정의한 목업을 기준으로 마크업을 작성하는 것이다.

여기서 직접 컴포넌트를 만든다.



앞서 얘기한것처럼 마크업을 통짜로 만든다면, 재사용이 어려워진다.

컴포넌트를 하나의 부품이라 생각하고, 이를 조합하면서 큰 기능을 만드는게 복잡성은 증가하지만, 더 많은 변화에 반응할 수 있다.

작은 컴포넌트 -> 부품을 만들어서 조립 -> 조합은 가지수를 폭발적으로 늘릴 수 있는 전형적인 방법.



이 중 하나가 아토믹 디자인 패턴이다.

원소의 조합으로 가지수를 늘린 것 처럼, 멘탈 모델로써 컴포넌트를 원자 단위부터 세부적으로 쪼개고 합쳐서 하나의 기능, 모듈, 템플릿, 레이아웃을 만드는 것이다.

아토믹 디자인의 모든 것을 따라하기 보다는 개념적 모델을 차용하는것이다.



정적 컴포넌트로 만들다보면, 컴포넌트 내의 코드가 길어지거나, 하나의 액터만 책임 지지 않거나 하는 등의 느낌이 들면 컴포넌트로 분리하는 것이 좋다.- - 액터란 시스템이 동일한 방식으로 변경되기를 원하는 사용자 집단이다.

컴포넌트를 사용하는 사람의 대해서 조금 더 거시적으로 실제 사용자가 쓴다고 생각하면 분리 하는게 효율적이고, 단일하게 책임지는것이 낫다고 느껴진다.

여기서 컴포넌트를 나누는 기준과 더불어 Props의 중요성이 나타난다.

컴포넌트가 제공하는 사용 방법이며, Props를 어떻게 제공해줄지에 따라 천차만별이다.



Tooltip 컴포넌트가 있다고 하면,

props로 text, customClassName 만 받아서 조금 더 사용자에게 많은 권한을 줄 수 있다 하면

props로 theme, type, position 등을 받아서 컴포넌트의 대한 권한은 제한하되, 편리성은 증가시킬 수 있다.



<figure><img src="../.gitbook/assets/스크린샷 2023-11-01 오후 9.51.53.png" alt=""><figcaption></figcaption></figure>

검색 영역의 대해서도 하나의 SearchBar로 묶어서 나눌 수 있지만

* TextInput
* CheckedField

의 조합으로도 만들 수 있다.

Checkbox가 기본 스타일이 아니라 디자인적으로 다양하다고 하면,

* TextInput
* CheckedField
  * CheckedInput
  * label

로 조합할 수 있다.



나누는 기준에 대해서는 보편적이거나 특수하다.

특수하다 하면, ValidatePhone (인증번호) 같은 경우나 Step By Step 의 경우에는 특수하게 묶여야할 것 같다.



얼마나 잘 쪼개고, 쪼갠 컴포넌트 사이에서 데이터를 어떻게 잘 나눌지 제공할지가 관건이다.





