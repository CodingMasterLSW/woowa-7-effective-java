자바는 두 가지 객체 소멸자를 제공한다.

finalizer는 예측할 수 없고, 상황에 따라 위험해 일반적으로 불필요하다.

cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.

finalizer와 cleane로는 제때 실행되어야 하는 작업은 절대할 수 없다.

ex) 파일 닫기 (.close()) 같은거 하지 말라는 말임.


이걸 얼마나 신속하게 수행할지는 가비지 컬렉터에 달려있음

그럼 어떻게 파일이나 스레드 등 종료해야 할 자원을 담고 있는 객체의 클래스에서 finalizer 혹은 cleaner를 대신할 수 있을까?

### AutoCloseable을 구현해주고, 인스턴스를 다 쓰고 나면 close 메서드를 호출해라.

```java
public class Sample implements AutoCloseable {
    @Override
    public void close() {
        System.out.println("close");
    }
}
```

- try-with-resources가 다음과 같은 방법을 사용한다.
try - finally 사용 예시

자바에서는 close를 호출해 직접 닫아줘야 하는 자원 존재
ex) InputStream, OutputSteram, sql.Connection ...

```java
public static String inputString() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    String result = br.readLine();
    br.close();
    return result;
}
```


해당 예시코드에서 br.readLine() 에서 Exception 이 터지면 자원을 회수하지 못 하고 종료가 된다.

그렇기에 다음과 같이 try-finally를 통해 자원을 회수해줘야 한다.
```java
public static String inputString() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
```

try-finally는 문제가 없어보이지만, 다음과 같은 상황에서 문제가 있다.

## case 1

여러 자원을 반환해야 할 경우
```java
public static void inputAndWriteString() throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    try {
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        try {
            String line = br.readLine();
            bw.write(line);
        } finally {
            bw.close();
        }
    } finally {
        br.close();
    }
}
```
1. 코드가 더럽다.
2. inputAndWriteString 메서드의 try 블록을 실행하던 도중 기기에 문제가 생긴다면 readLine이 정상적으로 실행되지 못하고 예외를 던지게 되고, 같은 이유로 finally 블록의 close 메서드도 예외를 던지게 된다.


try-with-resources로 고쳐보자

```java
public static String inputString() throws IOException {
    try (BufferedReader br = new BufferedReader(new InputStream(System.in))) {
        return br.readLine();
    }
}
```

간략하다!