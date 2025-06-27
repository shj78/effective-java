
# Effective Java

Day 5

### Item 14 : Consider implementing Comparable
> 순서를 고려해야하는 클래스를 사용해야한다면 Comparable을 사용해서 정렬, 검색, 사용을 쉽게 해보자.
### 왜 써야 하는가?
- 보통 Primitive 타입의 경우 자바 자체에서 비교 연산을 지원하기 때문에 비교할 수 있다. 하지만 여러 필드값을 가진 객체를 비교하는 경우 각 필드값의 우선순위나 어떤 필드로 비교할 지 자바에서 제공해주지 않는다. 그렇기때문에 Comparable을 사용한다.
### Comparable이란?
#### 1. 객체를 정렬하는데 사용하는 compareTo() 메서드를 정의하는 인터페이스
#### 2. 같은 타입 인스턴스를 비교해야하는 클래스는 모두 Comparable 인터페이스를 구현하고 있다. 그렇기 때문에 Boolean을 제외한 Wrapper 클래스나 String, Time, Date와 같은 클래스의 인스턴스는 모두 정렬 가능하다. 거의 모든 클래스와 Enum 타입이 Comparable을 구현하고 있다고 보아도 무방하다.

### compareTo()의 특징
- 동치성
```java
x.compareTo(x) == x.compareTo(x) == 0
```
- 추이성
```java
w.compareTo(x) > 0
x.compareTo(y) > 0
//...
y.compareTo(z) >0
```
- 대칭성
```java
x.compareTo(y) > 0 // 양수
y.compareTo(x) < 0 // 음수
```

cf) Compareable 인터페이스를 구현하지 않아 내부 정렬이 안되는 경우 equals() 메서드를 이용해서 비교를 하게 되는데 비교하는 객체에 따라 값이 다르게 나올 수 있다. 
cf) compareTo()메서드를 이용해서 비교를 할 때 return값이 정수오버플로우, 부동소수점 문제로 에러가 나지 않도록 주의한다
