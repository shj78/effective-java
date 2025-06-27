
# Effective Java

Day 4

## Item 9 : Prefer try-with-resources to try-finally

### try-with-resources를 쓰자
> 부제: try-finally는 쓰지말자
#### 1. try-with-resources를 쓰자는 이유
- 코드 가독성이 좋아진다.
  - AutoClosable을 구현하는 자원을 사용만 하면 close 메서드를 적을 필요가 없다!
  - 아래 첫번째 글머리 코드와 비교
  ```java
  public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;

    static void copy(String src, String dst) throws IOException {
        try (InputStream   in = new FileInputStream(src); OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        }
    }

    public static void main(String[] args) throws IOException {
        String src = args[0];
        String dst = args[1];
        copy(src, dst);
    }
  }
  ```
  - 자원을 한 개 사용할 때의 깔끔한 코드
  - 아래 두번째 글머리 코드와 비교
  ```java
  public class TopLines {
    static String firstLineOfFile(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        }
    }

    public static void main(String[] args) throws IOException {
        String path = args[0];
        System.out.println(firstLineOfFile(path));
    }
  }
  ```
- 예외를 순차적으로 처리해준다.
  - try-finally와 달리 readLine()과 close() 모두에서 예외가 발생했다면 스택 트레이스에는 readLine의 예외가 먼저 표시되며, 그 이후 숨겨졌다는 뜻인 ```java Suppressed: java.io. ~```와 같은 에러로 출력된다.
#### 2. try-finally를 쓰지 말아야 하는 이유
- 코드 가독성이 나쁘다.
  - 자원이 두개 이상일 경우의 예
  ```java
    public class Copys{
      private static final int BUFFER_SIZE = 8 * 1024;
  
      static void copys(String src, String dist) throws IOException{
          InputStream in = new FileInputStream(dist);
          try{
              OutputStream out = new FileOutputStream(dist);
              try{
                  byte[] buf = new byte[BUFFER_SIZE];
                  int n;
                  while ((n = in.read(buf))>=0) out.write(buf, 0, n);
              }finally {
                  out.close();
              }
          }finally {
              in.close();//여기서 자원이 많아진다면 코드의 중복으로 지저분한 코드가 된다
          }
      }
      
      public static void main(String[] args) throws IOException {
          String src = args[0];
          String dst = args[1];
          copys(src, dst);
      }
  }
  ```
- 예외는 try와 catch 모두에서 일어날 수 있는데 try의 예외를 무시해버릴 수 있다.
  - 디버깅이 어렵다
  ```java
  public class TopLines {
      static String firstLineOfFile(String path) throws IOException {
          BufferedReader br = new BufferedReader(new FileReader(path));
          try {
              return br.readLine();
          } finally {
              br.close();//이 곳에서 try문 에러는 무시된다!
          }
      }
  
      public static void main(String[] args) throws IOException {
          String path = args[0];//🍏
          System.out.println(firstLineOfFile(path));
      }
  }
  ```
