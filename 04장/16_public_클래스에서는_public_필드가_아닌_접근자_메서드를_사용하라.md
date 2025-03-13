### 인스턴스 필드만 모아둔 클래스 예시

- getter/setter 없어서 내부 표현을 바꿀 수 없음
- 불변성 보장도 못 함

```java
public class Point {
    public double x;
    public double y;
}
```

### 객체지향적인 클래스 작성하려면 캡슐화 하자

- 필드 private, getter 추가

    ```java
    public class Point {
        public double x;
        public double y;
    
        public Point(final double x, final double y) {
            this.x = x;
            this.y = y;
        }
    
        public double getX() {
            return x;
        }
    
        public void setX(final double x) {
            this.x = x;
        }
    
        public double getY() {
            return y;
        }
    
        public void setY(final double y) {
            this.y = y;
        }
    }
    ```

### 예외 : package-private 클래스 혹은 private 중첩 클래스의 경우

- 데이터 필드 노출해도 괜찮

    ```java
    public class ColorPoint {
        private static class Point{
            public double x;
            public double y;
        }
    
        public Point getPoint(){ // 클라이언트 코드가 클래스 내부에 묶인다.
            Point point = new Point(); 
            point.x = 1.2;
            point.y = 5.3; 
    
            return point; 
        }
    }
    ```

- ColorPoint 외부에서는 Point 클래스 내부 조작 불가능함.
- 패키지 바깥 코드 손 안 대고 Point 클래스 내부 조작이 가능함!
