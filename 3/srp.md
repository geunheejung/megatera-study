# 흔히 쓰이는 SRP를 위한 수단

#### 아주 흔히 쓰이는 SRP를 위한 수단이다.

* 변화의 크기(영향 범위)를 제약한다.

#### Extract Function

```javascript
// A
function printOwing(invoice) {
  printBanner();
  let outstanding  = calculateOutstanding();

  //print details
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);  
}

// B
function printOwing(invoice) {
  printBanner();
  let outstanding  = calculateOutstanding();
  printDetails(outstanding);
  
  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
}
```

1. 일단 코드를 길게 작성하고, 적절히 자를 수 있는 부분이 보일 때 "함수로 추출" 한다.&#x20;
2. 또는 코드를 작성하기 어려운 상황에 직면했을 때 함수로 추출 한다.

### Inline Function

```javascript
// A
function getRating(driver) {
  return moreThanFiveLateDeliveries(driver) ? 2 : 1;
}

function moreThanFiveLateDeliveries(driver) {
  return driver.numberOfLateDeliveries > 5;
}


// B
function getRating(driver) {
  return (driver.numberOfLateDeliveries > 5) ? 2 : 1;
}
```

코드를 작성하기 어려운 상황에 직면했을 때 함수로 추출.

* 바로 다른 파일로 만들어야 한다고 생각하지 않아도 된다.
* 컴포넌트 나누는 기준이 애매하면 다시 하나의 컴포넌트로 합쳤다가(Inline Method) 다시 나눠줘도 된다.
