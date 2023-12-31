# Singleton Pattern?

## Singleton Pattern

***

싱글톤 패턴은 클래스의 인스턴스화를 단일 인스턴스로 제한하고 해당 인스턴스에 대한 전역 액세스 지점을 제공하는 디자인 패턴이다.

## 주요 특징

***

### 단일 인스턴스

* 클래스의 인스턴스가 하나만 존재하도록 보장한다.

### 전역 액세스

* 단일 인스턴스는 전역적으로 액세스할 수 있으므로 애플리케이션의 모든 부분에서 액세스할 수 있다.

### 지연 로딩(선택 사항)

* 싱글톤 인스턴스가 실제로 필요할 때까지 인스턴스화를 지연시킬 수 있다.

## 예제

***

```java
public class Singleton {
    private static Singleton instance;

    // 클래스 외부에서 인스턴스화를 방지하는 비공개 생성자
    private Singleton() {}

		// 싱글톤 인스턴스를 가져오는 메서드이다.
		// 인스턴스가 존재하지 않으면 인스턴스를 생성한다.
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

		// 싱글톤 클래스의 기타 메서드 및 프로퍼티.
}
```

## 장점

***

### 단일 제어 지점

* 특정 리소스나 기능에 대한 단일 제어 지점을 제공
* 여러 인스턴스가 있으면 충돌이나 일관되지 않은 동작이나 발생할 수 있는 시나리오에서 유용하다.

### 글로벌 액세스

* 애플리케이션의 여러 부분에서 개체에 액세스해야 할 때 유용할 수 있으며 인스턴스를 매개변수로 전달할 필요가 없다.

### 지연 로딩

* 싱글톤 인스턴스가 처음 요청 될 때만 생성된다는 의미이다.
* 특히 싱글톤과 관련된 리소스를 초기화하는 데 비용이 많이 드는 시나리오에서 성능을 향상 시킨다.

### 리소스 공유

* 구성 관리자, 로깅 서비스 또는 연결 풀 과 같은 공통 리소스를 공유해야 할 때 자주 사용된다.
* 애플리케이션의 모든 부분이 이러한 리소스의 동일한 인스턴스를 사용하도록 보장한다.

### 메모리 효율성

## 요약

***

싱글톤을 과도하게 사용하면 긴밀한 결합, 전역 상태, 테스트의 어려움과 같은 문제가 발생할 수 있다.

애플리케이션의 특정 요구 사항을 고려하고 싱글톤 패턴을 사용하는 것이 주어진 시나리오에 적합한지 판단하는 것이 중요하다.

많은 경우 종속성 주입이나 다른 디자인 패턴과 같은 대안이 더 적합할 수 있음.
