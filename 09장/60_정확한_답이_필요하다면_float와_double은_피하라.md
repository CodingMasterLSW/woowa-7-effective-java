# Item 60 정확한 답이 필요하다면 float와 double을 피하라

[Java의 double과 float]
Java에는 실수 표현을 위해 기본적으로 제공되는 자료형이 있다.

float - 4 Byte
double - 8 byte
이러한 float와 double은 기본적으로 부동 소수점 방식을 활용한다.

[부동 소수점 방식]
Java의 실수 표현을 위한 double과 float의 경우 IEEE 754 부동 소수점 방식을 사용하고 있다. 
이러한 부동 소수점 방식은 표현의 범위는 넓지만 약간의 오차를 가지고 있다.

[약간의 오차?]
이러한 부동 소수점 방식을 활용한 실수 표현은 오차를 동반한다. 실수를 표현할 때 정확한 표현이 아닌 근사치를 표현하고 있다.
아래는 오차를 확인하기 위한 간단한 예시이다.

```java
public class FloatingPointTest {

    @Test
    void floatingPointTest() {
        // given
        double coin = 1.015;

        // when
        double result = coin - 0.13;

        // then
        assertThat(result).isEqualTo(0.885);
    }
}

/// 0.88499999999999999
```

[올바른 방안]
위 같은 문제를 올바르게 해결하기 위해서는 정수를 이용하여 실수를 표현한 BigDecimal이나 int, long을 사용해야 한다.

```java
public class BigDecimalTest {
    @Test
    void bigDecimalTest() {
        // given
        BigDecimal coin = new BigDecimal("1.015");

        // when
        BigDecimal result = coin.subtract(new BigDecimal("0.13"));

        // then
        assertThat(result).isEqualTo(new BigDecimal("0.885"));
    }
}
```
한 가지 주의해야 할 점은 문자열이 아닌 double 타입을 그대로 전달할 경우 
근사치가 전달되기 때문에 오차가 생길 가능성이 있다.


[단점]
하지만 이러한 BigDecimal도 단점은 존재한다.

기본 타입보다 쓰기 불편하다.
기본 타입보다 느리다.

만약 다뤄야 하는 숫자의 범위에 맞게 int 또는 long을 사용할 수도 있다.

[핵심정리]
정확한 답이 필요한 경우 float나 double을 피해야 한다.
금융 계산에서는 반드시 BigDecimal, int 혹은 long을 사용해야한다.
소수점 관리 및 성능 저하를 신경 쓰지 않는 다면 BigDecimal의 사용은 좋은 대안이 된다.

하지만 성능이 중요하고 숫자가 너무 크지 않으며 소수점을 직접 관리할 자신이 있다면 
int나 long 사용을 고려할 수 있다.