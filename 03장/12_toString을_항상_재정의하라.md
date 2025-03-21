# toString을 항상 재정의하라

Object의 기본 toString 메서드가 우리가 작성한 클래스에 적합한 문자열을 반환하는
경우는 거의 없다. toString의 일반 규약에 따르면 '간결하면서 사람이 읽기 쉬운 형태의 유익한 정보'
를 반환해야한다. 또한 모든 하위 클래스에서 이 메서드를 재정의하라고 한다.

equals나 hashCode만큼 중요하진 않지만 toString을 잘 구현한 클래스는
사용하기에 훨씬 즐겁고 디버깅이 쉽다.

실전에선 toString은 그 객체가 가진 주요 정보를 모두 반환하는게 좋다. 이상적으로는
스스로를 완벽히 설명하는 문자열이여야 한다.

toString의 반환값을 파싱하여 데이터를 얻지 말고 접근 제어자를 제공해야한다.
toString의 포맷이 바뀔수도 있기 때문

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

public class Main {
    public static void main(String[] args) {
        Person person = new Person("John", 30);
        System.out.println(person); // 출력: Person{name='John', age=30}
    }
}
```

핵심 정리
모든 구체 클래스에서 Object의 toString을 재정의하자, 상위 클래스에서 이미 알맞게 정의한 경우 예외!
toString은 디버깅을 쉽게 해주고 해당 객체에 관한 명확하고 유용한 정보를 읽기 좋은 형태로 반환한다!