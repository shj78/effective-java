
# Effective Java 

Day 3



## 아이템 3 생성자나 열거타입으로 싱글턴임을 보증하라

### public static final 필드 + private
#### 장점
1. 간결하고 싱글턴임을 API에 들어낼 수 있다.
```java
public class Elvis implements IElvis{
    public static final Elvis INSTANCE = new Elvis();

    private Elvis() {
    }
```

#### 단점
1. 싱글톤을 사용하는 클라이언트 테스트는 어렵다.
```java
public class Elvis implements IElvis{

    /**
     * 싱글톤 오브젝트
     */
    public static final Elvis INSTANCE = new Elvis();
    private static boolean created;

    private Elvis() {
        if (created) {
            throw new UnsupportedOperationException("can't be created by constructor.");
        }

        created = true;
    }

    public void leaveTheBuilding() {
        System.out.println("Whoa baby, I'm outta here!");
    }

    public void sing() {
        System.out.println("I'll have a blue~ Christmas without you~");
    }

    public static void main(String[] args) {
        Elvis elvis = Elvis.INSTANCE;
        elvis.leaveTheBuilding();
    }

    private Object readResolve() {
        return INSTANCE;
    }

}

public class Concert {

    private boolean lightsOn;

    private boolean mainStateOpen;

    private IElvis elvis;

    public Concert(IElvis elvis) {
        this.elvis = elvis;
    }

    public void perform() {
        mainStateOpen = true;
        lightsOn = true;
        elvis.sing();
    }

    public boolean isLightsOn() {
        return lightsOn;
    }

    public boolean isMainStateOpen() {
        return mainStateOpen;
    }
}


public interface IElvis {

    void leaveTheBuilding();

    void sing();
}

class ConcertTest {

    @Test
    void perform() {
        Concert concert = new Concert(Elvis.INSTANCE);
        concert.perform();

        assertTrue(concert.isLightsOn());
        assertTrue(concert.isMainStateOpen());
    }

}

```
2. 리플렉션 API accessibleObject로 private 호출이 가능해 접근이 가능하다.
> 예외던지게 하면 된다.
3. 역직렬화 할 때 새로운 인스턴스가 무의미하게 새로 생성되어 반환된다.
> readResolve호출 및 필드에 transient로 선언하면서 가짜 인스턴스를 GC로 보낸다.

### private 생성자 + 정적 팩터리 메서드
#### 장점
1. API 변경 없이 싱글턴이 아니게 할 수 있다
2. 원하면 정적 팩터리 메서드를 제네릭 싱글턴으로 가능 
3. 정적 팩터리 메서드 참조를 Supplier로 가능하다.


#### 단점
1. private 생성자를. 사용하기 때문에 위와 같다.(🍏)

#### 제일 좋은 방법
원소가 하나인 열거 타입을 선언한다(Enum)

