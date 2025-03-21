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
