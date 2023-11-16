# Given-When-Then

## 테스트는 왜 하는 걸까?

***

버그를 찾기 위해서 이다. 버그는 무엇인가? 의도한 것과 다른 일이 벌어졌을 때, 버그가 발생했다고 한다. 명세와 다른 프로그램 또는 명세가 불완전해서 알 수 없는 방식으로 작동하는 프로그램이 버그가 발생하는 프로그램이다.

올바른 명세가 있다면, 그걸 확인하는 테스트 코드를 작성하는 건 쉬울 것이다. 바로 확인할 수 있는 명세가 있다면, 빠진 부분이 있는지 확인하는 것도 비교적 쉬울 것이다.

명세는 코드가 아니다. 그래서 모호함이 숨어있을 수 있다. 그걸 더 명료하게 만드는 방법은 예제를 활용하는 것.

## Given - When - Then

***

* Given : 이 시나리오에서 지정하는 동작을 시작하기 전 시점의 상태를 설명한다. 테스트의 전제 조건이라 생각하면 된다.
* When : 지정하는 동작이다.
* Then : 지정된 동작으로 인해 예상되는 변경 사항을 설명한다.

### **예제**

```ruby
Feature: 사용자 주식 거래
	시나리오: 사용자가 거래 마감 전에 매도를 요청함
		Given: 
			MSFT 주식 100주, APPL 주식 150 를 보유하고 있고,
			시간은 거래 마감 전 이라 가정한다.
		When: MSFT 주식 20주 매도를 요청하는 경우
			그러면 MSFT 주식 80주를 보유해야 한다.
			그러면 APPL 주식 150주를 보유해야 한다.
			그리고 MSFT 주식 20주에 대한 매도 주문이 실행되었어야 한다.
```

모든 종류의 테스트에 `Given-When-Then` 스타일을 사용할 수 있다. 어떤 사람들은 단위 테스트 내부의 비공식 블록을 표시하기 위해 Given-When-Then 을 주석으로 넣는 것을 좋아한다. 비공식적인 산문을 구성하는 데에도 이 규칙을 사용하는 것을 봄.

이 접근 방식에서는 각 절 내에서 여러 표현식을 결합하는 데 “and”를 사용하는 것이 일반적이다.

Given의 대해 전제 조건 상태에 대한 설명으로 표현한 것은 마틴 파울러가 선호하는 방식이다. 테스트 프레임워크는 Given의 대하여 When 명령을 실행하기 전에 테스트 대상 시스템을 올바른 상태로 만들기 위한 일련의 명령으로 해석한다.(그래서 다른 명명 규칙에서는 이를 “설정”이라고 부르기도 함.) 테스트 프레임워크는 Then 명령에 대한 다양한 쿼리 메서드를 제공하며, 이러한 메서드에는 부작용이 없어야 한다.

Given-When-Then 스타일은 BDD의 특징이지만, 기본 아이디어는 예제로 테스트나 사양을 작성할 때 매우 일반적이다. 메사로스는 이 패턴을 4단계 테스트라고 설명한다.

1. 설정(Given)
2. 실행(When)
3. 검증(Then)
4. 분해(Teardown)
   1. 테스트를 구현할 때 분해가 항상 필요한 것은 아니며, 예를 들어 사양의 커뮤니케이션 측면에 많은 것을 추가 하지도 않는다. BDD 스타일에서 누락되는 것으로 보는 것이 합리적이다.

### 무슨 일이 벌어지나

***

```jsx
// 명세
전자렌지에 3분만 돌리면 완성!

// When
전자렌지에 3분만 돌리면
// Then
완성
```

### 준비가 필요

***

전자렌지를 먼저 준비하는 게 빠짐.

```jsx
// Given
전자렌지 = 700와트 짜리 전자렌지

// When
음식 = 전자렌지에 넣고 3분 조리

// Then
음식 완성!
```

### 상황을 더 눈에 띄게

***

어떤 상황인지 약간 더 눈에 띄게 바꿔보자.

> Given : 700와트 전자렌지를 준비하고, 3분이란 시간이 주어졌을 때 When : 주어진 시간동안 전자렌지를 돌리면 Then : 요리가 완성된다.

```jsx
// Given
와트 = 700
시간 = 3분
전자렌지 = 전자렌지(와트)

// When
음식 = 전자렌지.조리(시간)

// Then
음식 완성!
```

테스트에 사용 되는 재료들의 대하여 더 눈에 띄게 표현해줬다.

### 태세전환

***

만약 1분만 돌리면 어떻게 될까?

```ruby
decribe 'Stove#cook' do
  context 'with enough time' do
    before(:each) do
      # Given
      @power = 700.watts
      @time = 3.minutes
      stove = Stove.new(@power)
    end

    it 'returns a complete food' do
      # When
      food = stove.cook(@time)

      # Then
      assert food.complete?
    end
  end

  context 'with not enough time' do
    before(:each) do
      # Given
      @power = 700.watts
      @time = 1.minute
      stove = Stove.new(@power)
    end

    it 'returns an incomplete food' do
      # When
      food = stove.cook(@time)

      # Then
      assert !food.complete?
    end
  end
end
```

1분만 돌릴 경우 요리가 완성되지 않도록 의도 하였다. 그리고 테스트는 우리가 의도한대로 동작 하는지 확인해준다. 만약 5분을 돌리면 어떻게 될까?

자꾸 떠오르는 생각이 있을 거고, 바로 실행해서 확인하면 된다.

### let

***

```ruby
decribe 'Stove#cook' do
  let(:stove) { Stove.new(power) }

  subject { stove.cook(time) }

  context 'with enough time' do
    let(:power) { 700.watts }
    let(:time) { 3.minutes }
    it { is_expected.to be_complete }
  end

  context 'with not enough time' do
    let(:power) { 700.watts }
    let(:time) { 1.minute }
    it { is_expected.not_to be_complete }
  end
end
```

중복을 제거함으로써, 코드에 대한 부담이 줄어들고 의도가 좀 더 명확하게 잘 보이니 전자렌지를 바꾼다든지의 여러 시도를 더 쉽게 할 수 있게 된다.

## 정리

***

When - Then, 또는 확장된 Given - When - Then 은 테스트 코드를 먼저 작성하는 작업을 명세를 명확히 하는 작업으로 바꿔준다. 테스트 코드를 어떻게 써야 할지 모르는 게 아닌, 하려는 작업을 올바르게 이해하고 있지 못한 게 아닐까..
