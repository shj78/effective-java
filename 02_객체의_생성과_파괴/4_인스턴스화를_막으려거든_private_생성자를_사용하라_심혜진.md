
# Effective Java 

Day 3



## 아이템 4 인스턴스화를 막으려거든 private 생성자를 사용하라

### 유틸리티성 클래스(java.util.)는 인스턴스화를 목적이 아닌 정적 팩터리 메서드를 사용하도록 만들어져있다. 
> 인텔리제이에서도 유틸리티성 클래스의 인스턴스생성은 권장하지 않기 때문에 자동완성으로 도와주지도 않는다. 하지만 문법으로 문제가 되지는 않는다.
그럼에도 불구하고 필요없는 코드가 생겨나기 때문에 인스턴스화를 막아주는 것이 올바른 방법이다.
### 어떻게 인스턴스화를 막는가?
1. 추상클래스로 만든다
- 그런데, 추상클래스로 만들더라도 인스턴스화할 수 있다. 상속을 하면 되기 때문이다.
-  이 방법이 최선인가?
- 오히려 추상클래스로 했다가 “아 이건 상속해서 쓰는건가?” 하고 헷갈릴 수 있다.
2. private 생성자를 쓰자. (🍏)
  - 제일 좋은 방법이다.
  - 하지만 이게 Spring에도 잘 적용이 안되어있다. 
  - 추상클래스를 써서 인스턴스화를 막고 있다. Ex) AnnotationConfigUtils 
  - 근데 또 보이는 데 못쓰도록 private 생성자를 왜 쓰냐? 하고 의아할 수 있기 때문에 주석을 달자. 
  - Ex) 이 클래스는 인스턴스를 만들 수 없습니다
