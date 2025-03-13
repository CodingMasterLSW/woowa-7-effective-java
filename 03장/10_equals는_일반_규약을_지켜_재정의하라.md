- Object에서 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)는 모두 재정의를 염두에 두고 설계된 것
- 기본적으로는 equals 메서드는 메모리 주소를 비교해서 두 객체가 동일한 메모리 주소를 가지고 있을 때만 true를 반환함.
    - 기본적으로 `==`와 같지만 오버라이드 하면 객체의 특정 필드를 기준으로 비교 가능(논리적 동치성;;)

    ```java
        @Override
        public boolean equals(final Object o) {
            if (this == o) {
                return true;
            }
            if (o == null || getClass() != o.getClass()) {
                return false;
            }
            final Crew crew = (Crew) o;
            return Objects.equals(getName(), crew.getName());
        }
    ```

- equals 재정의할 때 지켜야 할 것들
    - 대칭성
    - x, y x.equals(y) y.equals(x)
        - CaseInsensitiveString equals 오버라이드 한 경우

        ```java
        CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
        String s = "polish";
        
        cis.eqals(s) // true
        s.equals(cis); // false
        ```

    - 추이성
        - x.equals(y), y.equals(z) true → x.equals(z) true 여야함.
        - ColorPoint equals 오버라이드 한 경우

        ```java
            public static void main(){
              ColorPoint p1 = new ColorPoint(1,2, Color.RED);
              SmellPoint p2 = new SmellPoint(1,2);
              ColorPoint p3 = new ColorPoint(1,2, Color.BLUE);
              p1.equals(p2); // true
              p2.equals(p3); // true
              p1.equals(p3); // false 
            }
        ```

- equals의 규약을 지키면서 구체 클래스의 하위 클래스에 값을 추가할 수 있도록 우회하는 방법
    - 상속 말고 컴포지션 사용
    - 상속

        ```java
        class ColorPoint extends Point {
            private final String color;
        
            public ColorPoint(int x, int y, String color) {
                super(x, y);
                this.color = color;
            }
        
            @Override
            public boolean equals(Object o) {
                if (!(o instanceof ColorPoint)) return false;
                ColorPoint cp = (ColorPoint) o;
                return super.equals(o) && cp.color.equals(color);
            }
        }
        ```

    - 컴포지션

        ```java
        class ColorPoint {
            private final Point point;
            private final String color;
        
            public ColorPoint(int x, int y, String color) {
                this.point = new Point(x, y);
                this.color = color;
            }
        
            public Point asPoint() {
                return point;  // 일반 Point 객체로 변환하는 뷰 메소드
            }
        
            @Override
            public boolean equals(Object o) {
                if (o instanceof ColorPoint) {
                    ColorPoint cp = (ColorPoint) o;
                    return point.equals(cp.point) && color.equals(cp.color);
                }
                if (o instanceof Point) {
                    return point.equals(o);
                }
                return false;
            }
        }
        
        ```

- 총정리 :양질의 equals 메소드 구현 방법

    1. **`==`** 연산자를 이용해 자기 자신의 참조인지 확인한다.

  성능 최적화용!

    2. `instanceof` 연산자로 입력이 올바른 타입인지 확인한다. 그렇지 않다면 false를 반환한다.

  만약 특정 인터페이스로 구현한 서로 다른 클래스끼리 비교하고 싶다면, equals에서 해당 인터페이스를 사용해야 한다.

  ex. Set, List, Map, Map.Entry 등의 컬렉션 인터페이스

    3. 입력을 올바른 타입으로 형변환한다.

  2번에서 `instanceOf` 검사를 했기 때문에 100프로 성공

    4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.

  만약 인터페이스를 사용했다면, 입력의 필드 값을 가져올 때도 그 인터페이스의 메소드를 사용해야 한다.
    5. equals를 재정의할 땐 hashCode도 반드시 재정의하자 (아이템 11)
    6. 꼭 필요한 경우가 아니면 equals를 재정의하지 말자.
