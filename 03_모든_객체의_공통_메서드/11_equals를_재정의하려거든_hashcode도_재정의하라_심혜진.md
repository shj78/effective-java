
# Effective Java

Day 5

### Item 11 : Always override hashCode when overriding equals()

### equals()를 재정의 한다면 hashCode()도 재정의 하자
> 같이 정의 하지 않는 다면 잘못된 코드이다
#### 왜 그런가?
1. equals()에서 사용하는 값이 변경되지 않는다면 hashCode()는 몇번 실행되더라도 같은 값을 리턴해야 한다.하지만 hashcode()를 재정의 하지 않으면 다른 값을 리턴한다.
2. equals()에서 같은 객체로 나온다면 두 객체의 hashCode()값은 같아야한다. 하지만 성능을 고려해 다른 객체면 다른 객체일 경우 다른 hashCode()를 리턴하자.(0(1)>0(n)이 됨)
3. 다른 객체일 경우 같은 hashCode()가 나오는 경우도 있다. 이 경우에는 해시 충돌이 일어나게 되는데 HashMap의 경우 해시 충돌이 일어나면 HashMap의 버킷의 객체들은 모두 LinkedList로 변하게 된다. 이때 개발자의 의도대로 자료구조가 사용되지 않는다.

#### 그러면 어떻게 쓰는 게 올바른 방법인가?
- 올바른 재정의의 예
```java 
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "area code");
        this.prefix   = rangeCheck(prefix,   999, "prefix");
        this.lineNum  = rangeCheck(lineNum, 9999, "line num");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max)
            throw new IllegalArgumentException(arg + ": " + val);
        return (short) val;
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
    private volatile int hashCode; //0으로 초기화

    @Override public int hashCode() {
        if (this.hashCode != 0) {
            return hashCode;
        }

        synchronized (this) {
            int result = hashCode;
            if (result == 0) {
                result = Short.hashCode(areaCode);
                result = 31 * result + Short.hashCode(prefix);
                result = 31 * result + Short.hashCode(lineNum);
                this.hashCode = result;
            }
            return result;
        }
    }

    public static void main(String[] args) {
        Map<PhoneNumber, String> m = new HashMap<>();
        m.put(new PhoneNumber(010, 7101, 9999), "deepsea");
        System.out.println(m.get(new PhoneNumber(010, 7101, 9999)));
    }
}

```
1. 위 코드는 핵심적인 필드를 가지고 hashCode를 재정의 한 코드이다.
2. Primitive타입이라면 해당 Wrapper타입의 hashCode()를 사용해서 hashCode를 구하고 Reference타입이라면 해당 래퍼가 Reference타입이 가지고 있는 hashCode메서드를 사용하여 hashCode값을 구한다.
- Primitive 타입
    - int, long, double, float, boolean, byte, short, char 등 정수, 실수, 문자, 논리 리터럴 등의 실제 데이터 값을 저장하는 타입
- Wrapper 타입
    - Integer, Long, Double, Float, Boolean, Byte, Short, Char등 객체를 참조하는 타입으로 메모리 주소값을 통해 객체를 참조하는 타입
3. 31을 곱해주는 이유는 해시충돌이 적은 통상적인 숫자이며, 짝수보다 위험성이 적은 홀수를 사용해야 하기 때문이다.


