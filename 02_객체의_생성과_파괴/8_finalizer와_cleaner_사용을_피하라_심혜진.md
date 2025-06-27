
# Effective Java

Day 4

## Item 8 : Avoid finalizers and cleaners

### finallizer와 cleaner는 재앙을 일으킬 수 있다.

#### 1. 이유
   - 수행 시점 예측 불가
   - 수행 여부 예측 불가
   - 끔찍한 성능
   - final이 아닌 클래스의 경우 finalizer를 이용했다면 보안 취약
#### 2. 그러면 뭐를 쓰라는 말인가?
   - AutoCloseable을 구현해주고, 객체를 쓴 뒤 close()를 호출하자
   - cf) 통상적으로는 try-resources를 사용
> 인스턴스를 사용할 때는 인스턴스의 열림/닫힘을 추적하자. 그렇게 close()를 적절하게 사용해주면서 IlegalStateException으로 예외처리가 된다.
#### 3. 그럼 저걸 왜 쓰는 건데?
   - close() 호출하지 않았을 때의 안전빵
      - cf) 우리가 자주 사용하는 FileInputStream, FileOutputStream, ThreadPoolExecutor 클래스 내부는 안전빵의 역할로 finalizer를 제공한다.
      - 안하는 것 보다 나중에라도 하는 게 나으니까!
   - 네이티브 피어일 때 효과
      - 네이티브 피어는 일반 자바 객체가 네이티브 메서드로 기능을 위임한 네이티브 객체이다.
      - GC는 네이티브 피어를 회수하지 못한다.
      - 그렇기에 적절할 수 있지만 단, 성능 저하나 중요 자원이 아닐 때만 사용한다.
```java
// 코드 8-1 cleaner를 안전망으로 활용하는 AutoCloseable 클래스 (44쪽)
public class Room implements AutoCloseable {
private static final Cleaner cleaner = Cleaner.create();

    // 청소가 필요한 자원. 절대로 Room을 참조해서는 안 된다! 순환 참조가 생겨 Room인스턴스를 회수하려다가 OutOfMemory하며 어플리케이션이 죽어버릴 것이다.
    // 그렇기 때문에 State 클래스에 static이 사용됐다.
    // static인 이유는 static이 아닌 내부 클래스는 자동으로 외부 클래스의 참조를 갖게 되기 때문이다.
    private static class State implements Runnable {
        int numJunkPiles; // Number of junk piles in this room

        // 방안의 쓰레기 수, 즉 수거할 자원이며 현실적으로는 네이티브 피어를 가리키는 포인터를 담은 final long 변수여야 한다.
        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        // close 메서드나 (바라건대) cleaner가 호출한다.
        @Override public void run() {
            System.out.println("Cleaning room");
            numJunkPiles = 0;
        }
    }

    // 방의 상태. cleanable과 공유한다.
    private final State state;

    // cleanable 객체. 수거 대상이 되면 방을 청소한다.
    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override public void close() {
        cleanable.clean();
    }
}
```
