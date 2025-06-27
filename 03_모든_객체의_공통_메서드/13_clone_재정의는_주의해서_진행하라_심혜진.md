
# Effective Java

Day 5

### Item 13 : Override clone() judiciously
> clone()은 잘(judiciously) 써야한다.

### clone()란?
#### - Cloneable 인터페이스를 구현해야만 사용할 수 있는 메서드
### Cloneable이란?
#### 1. Object에 정의되어있는 protected clone()의 행위를 관장한다.
#### 2. Cloneable을 구현하지 않고 사용한다면 CloneNotSupportedException이라는 예외를 만날 수 있다.

### 그러면 어떻게 잘 clone()을 사용하는 가? 
#### - 1. Cloneable을 구현하는 클래스 에서 supoer.clone()을 사용해서 clone하자
#### - 2. clone 규약을 지키자.
  - clone 규약
    - 서로 주소값이 다른 객체여야 한다.
    - getClass()를 사용했을 때 같은 클래스여야 한다.
    - equals()를 사용했을 때 다를 수 있다.
      - clone()을 했더라도 객체를 식별하는 값은 변경할 수 있기 때문이다.

#### 불변객체일 때 clone() 사용의 올바른 예
```java 
public final class PhoneNumber implements Cloneable {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix   = rangeCheck(prefix,   999, "프리픽스");
        this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
        System.out.println("constructor is called");
    }

    public PhoneNumber(PhoneNumber phoneNumber) {
        this(phoneNumber.areaCode, phoneNumber.prefix, phoneNumber.lineNum);
    }

    // 코드 13-1 가변 상태를 참조하지 않는 클래스용 clone 메서드 (79쪽)
    @Override
    public PhoneNumber clone() {
        try {
            return (PhoneNumber) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();  // 일어날 수 없는 일! 사실 try catch를 안썼어도 된다.
        }
    }

    public static void main(String[] args) {
        PhoneNumber pn = new PhoneNumber(707, 867, 5309);
        Map<PhoneNumber, String> m = new HashMap<>();
        m.put(pn, "제니");
        PhoneNumber clone = pn.clone();
        System.out.println(m.get(clone));

        System.out.println(clone != pn); // 반드시 true
        System.out.println(clone.getClass() == pn.getClass()); // 반드시 true
        System.out.println(clone.equals(pn)); // true가 아닐 수도 있다.
    }

    @Override public boolean equals(Object o) {
        if (o == this)
            return true;
        if (!(o instanceof PhoneNumber))
            return false;
        PhoneNumber pn = (PhoneNumber)o;
        return pn.lineNum == lineNum && pn.prefix == prefix
                && pn.areaCode == areaCode;
    }

```
### 하지만 가변객체라면 말이 달라진다. 
 - 아래와 같은 Stack일 경우 다른 Stack1, Stack2... clone들이 생기더라도 같은 elements를 공유하기 때문에 clone 규칙이 깨지게 된다. 
```java 
public class Stack implements Cloneable {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size ==0;
    }


    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```
- 그러니 위와 같은 클래스 내 배열이 있을 경우 배열도 함께 clone해주는 메서드를 작성하여 가변 상태를 보장해주자.
```java 
public class Stack implements Cloneable {
    ...
    @Override public Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone();
            return result;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
    ...
}
```
### 더 알아볼 것
- elements 배열이 final일 경우 위와 같은 방법이 적용되지 않기 때문에 해당 문제에 대해서 고민해볼 것

