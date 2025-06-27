
# Effective Java

Day 4

## Item 10 : Obey the general contract when overriding

> equals()는 모든 객체들의 조상, Object에 주소값을 비교하도록 정의되어있는 메서드이다. 우리는 보통 그 메서드를 lombok을 통해 재정의 하지 직접 재정의 하지 않는다. 편하게 도와주는 도구를 이용해서 사용하지만 재정의 할 경우 하지말아야 하는 경우와 하는 경우에 대해서 알아보자

### 재정의 하지 말아야 하는 경우
1. 비교하려는 객체가 싱글톤일 때
2. 논리적 동치성(값 비교)를 할 일이 없을 때
3. package 또는 class가 private라 equals를 호출할 일이 없을 때
4. 상위 클래스에 이미 equals가 정의 되어있을 때 ex)Set과 AbstractSet, List와 AbstractList🍏

### 재정의하는 경우(안하는 게 좋겠지만)
#### ‼️ 지켜야하는 규칙이 있다. 그러니 지켜주자.
1. 반사성 | 자신을 비교했을 때 true가 나오는 가?
```java  A.equals(A) == true ```
2. 일관성 | A,B가 불변 객체일 때 두번 호출해도 값이 같은가?
```java  A.equals(B) == A.equals(B) ```
3. 대칭성 
```java  A.equals(B) == B.equals(A) ```
4. null-test
5. 추이성

