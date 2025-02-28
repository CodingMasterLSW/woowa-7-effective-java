# 싱글턴에서의 private 생성자

- 싱글턴을 만드는 방법은 아래 2가지이다.
- **무슨 방법을 쓰던 간에 상관없이 생성자는 private으로 감추고, public static 멤버를 통해 접근하도록 한다.**

## public 멤버 변수 타입

```java
// 싱글턴 유형 1 : public 멤버 변수
public class Hello1 {
    public static final Hello1 INSTANCE = new Hello1();

    // private으로 생성자를 감춘다.
    private Hello1() {
        //...
    }
}
```

- 해당 클래스가 싱글턴임이 API를 통해 잘 드러난다.
- 또한 간결하다.

## public 정적 팩터리 메서드 타입

```java
// 싱글턴 유형 2 : public 정적 팩터리 메서드
public class Hello1 {
    private static final Hello1 INSTANCE = new Hello1();

    // private으로 생성자를 감춘다.
    private Hello1() {
        //...
    }

    public static Hello1 getInstance() {
        return INSTANCE;
    }
}
```

- 필요한 경우 싱글턴이 아니도록 변경할 수 있다.
- 필요한 경우 **제네릭 싱글턴 팩토리**로 만들 수 있다.
- 정적 팩터리 메서드를 Supplier처럼 취급할 수 있다.
