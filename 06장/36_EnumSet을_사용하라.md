# EnumSet

- 기존에는 여러 열거형 상수의 조합을 나타내기 위해 비트 필드를 사용했다.
  - BOLD(0001), ITALIC(0010), UNDERLINE(0100), H1(1000)
  - BOLD, ITALIC -> 0011
- 딱 봐도 해석하기 어렵다.
- 열거형 상수의 개수가 늘어나면 망한다.
- 이거 말고 `EnumSet` 쓰자.

```java
public class EnumSetDemo {

    enum MarkDownTextStyle {
        BOLD,
        ITALIC,
        H1,
        H2,
        H3,
        UNDERLINE,
        ;
    }

    public static void main(String[] args) {
        EnumSet<MarkDownTextStyle> enumSet = EnumSet.of(BOLD, ITALIC, H3);
    }
}
```

- 매우 간단하게 생성할 수 있고 내부적으로 비트 백터를 통해 최적화되어 있다.
- 읽기도 좋다.
