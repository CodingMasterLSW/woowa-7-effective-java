# 아이템 42 익명 클래스보다는 람다를 사용하라

예전에는 자바에서 함수 타입을 표현할 때 추상 메서드를 하나만 담은 인터페이스를 사용했다. 이런 인터페이스의 인스턴스를 함수 객체라고 하여
특정 함수를 나타내는 데 썼다. 이후 함수 객체를 만드는 주요 수단은 **익명 클래스**가 되었다.
  
```
interface MyFunctionalInterface {
    void execute();
}
 MyFunctionalInterface myFunc = () -> System.out.println("Hello, World!");
```

```
Collections.sort(words,new Comparator<String>(){
    public int comapre(String s1, String s2){
        return Integer.compare(s1.length(), s2.length());
    }
}
```

익명 클래스 방식은 코드가 너무 길기 때문에 자바는 함수형 프로그래밍에 적합하지 않았다.

자바 8에와서 추상메서드 하나짜리 인터페이스는 특별한 의미를 인정받아 특별한 대우를 받게 되었다. 지금은 함수형 인터페이스라고 부르는
이 함수형 인터페이스들의 람다식을 사용해 만들 수 있게 된 것이다. 람다는 함수나 익명 클래스와 개념은 비슷하지만 코드는 훨씬 간결하다.

다음은 익명 클래스를 사용한 앞의 코드를 람다 방식으로 바꾼 것이다.
```
Collections.sort(words,
    (s1,s2) -> Integer.compare(s1.length(),s2.length());
```

매개변수와 반환값에 대한 타입은 코드에서 언급이 없다. 컴파일러가 문맥을 살펴 타입을 추론해준 것이다. 상황에 따라 컴파일러가 타입을
결정하지 못할 수도 있는데, 그럴 때는 프로그래머가 직접 명시해야 한다. 티입 추론 규칙은 챕터 하나를 통째로 차지할 만큼 복잡하여, 다 이해하는
프로그래머는 거의 없다. 잘 알지 못한다 해도 상관없다. 타입을 명시해야 하는 코드가 더 명확할 때만 제외하고는, **람다의 모든 매개변수
타입은 생략하자.** 컴파일러가 "타입을 알 수 없다"라는 오류를 낼 때만 해당 타입을 명시하면 되고 아주 드물 것이다.

## 참고
- :: (메서드 참조)

  ```::``` 연산자는 기존 메서드를 람다식 대신 간결하게 사용할 때 사용합니다.

    ```
    Function<String, Integer> func = Integer::parseInt; //람다식 (s -> Integer.parseInt(s))와 동일
    ```
    기존 메서드나 생성자를 직접참조 할 때 사용합니다.

- -> (람다 표현식)
  ```->``` 는 익명 함수 정의할때 사용하는 람다 표현식 입니다.
  ```
  Comparator<Person> comparator1 = (p1, p2) -> p1.getName().compareTo(p2.getName());
  people.sort(comparator1);
  ```
  익명 함수를 직접 정의할때 사용합니다.


람다 자리에 비교자 생성 메서드를 사용하면 이 코드를 더 간결하게 만들 수 있다.
```
Collections.sort(words,comparingInt(String::length));
```

더 나아가 List 인터페이스에 추가된 sort 메서드를 이용하면 더욱 짧아진다
```
words.sort(comparingInt(String::length());
```

```
  public static void main(String[] args) {
      Operation operation = Operation.PLUS;
      operation.apply(1,2);

  }

  public enum Operation{
      PLUS("+"){
          @Override
          public double apply(double x, double y) {
              return x + y;
          }
      }
      ,
      MINUS("-"){
          @Override
          public double apply(double x, double y) {
              return x - y;
          }
      },
      TIMES("*") {
          @Override
          public double apply(double x, double y) {
              return x * y;
          }
      };

      private final String symbol;
      Operation(String symbol){
          this.symbol = symbol;
      }

      public abstract double apply(double x, double y);
  }
```

# 참고
- {} 위치에 대하여
  {}는 각 열거형 상수를 별도의 익명 클래스(인스턴스)로 취급하게 하기 때문에 {} 내부는 각 객체에서 메서드를 오버라이딩하는 익명 내부 클래스 역활을 한다.


람다를 이용하면 열거형 타입의 인스턴스 필드를 이용하는 방식으로 상수별로 다르게 동작하는 코드를 쉽게 구현할 수 있다. 단순히 각 열거 타입 상수의 동작을
람다로 구현해 생성자에 넘기고, 생성자는 이 람다를 인스턴스 필드로 저장해둔다.

```
  public static void main(String[] args) {
      Operation op = Operation.PLUS;
      op.apply(1,2);
  }

  public enum Operation{
      PLUS("+", (x,y) -> x + y),
      MINUS("-", (x,y) -> x - y),
      TIMES("*", (x,y) -> x * y);
      
      private final String symbol;
      private final DoubleBinaryOperator op;

      Operation(String symbol, DoubleBinaryOperator op) {
          this.symbol = symbol;
          this.op = op;
      }
      
      public double apply(double x, double y){
          return op.applyAsDouble(x,y);
      }
  }
```

# 참고
  DoubleBinaryOperator는 다양한 함수 인터페이스 중 double 타입 인수를 2개 받아 double 타입 결과를 돌려준다.

람다는 메서드나 클래스와 달리, 이름이 없고 문서화도 못 한다. 따라서 코드 자체로 동작이 명확한 설명이 되지 않거나 코드의
줄 수가 많아지면 람다를 쓰지 말아야 한다. 람다는 한 줄 일 때 가장 좋고 길어야 세 줄 안에 끝내는게 좋다.
열거 타입 생성자에 넘겨지는 인수들의 타입도 컴파일타입에 추론된다. 따라서 인스턴스는 런타임에 만들어지기 때문에 람다는 열거 타입의
인스턴스 멤버에 접근할 수 없다.

람다의 시대가 열리면서 익명 클래스는 설 자리가 크게 좁아진게 사실이다. 하지만 람다로 대체할 수 없는 곳이 있다. 람다는 함수형 인터페이스
에서만 사용된다. 추상 클래스의 인스턴스를 만들 때 람다를 쓸 수 없으며, 람다는 자기 자신을 참조할 수 없다. 람다에서의 this 키워드는 바깥
인스턴스를 가리킨다. 그래서 함수 객체가 자기 자신을 참조해야 한다면 반드시 익명 클래스를 써야한다.

# 핵심 정리
익명 클래스는 (함수형 인터페이스가 아닌)타입의 인스턴스를 만들 때만 사용하라.