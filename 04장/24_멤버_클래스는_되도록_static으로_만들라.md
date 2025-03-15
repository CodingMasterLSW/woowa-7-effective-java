# Item 24 멤버 클래스는 되도록 static으로 만들라

[중첩 클래스(nested class) 정의]
다른 클래스 안에 정의된 클래스를 말한다.

중첩 클래스는 자신을 감싼 바깥 클래스에서만 사용되어야 한다.
중첩 클래스가 바깥 클래스 이외의 쓰임새가 있다면 톱레벨 클래스로 만들어야 한다.
인스턴스를 중첩 클래스로 포장하여 불필요한 노출을 줄여 캡슐화를 할 수 있고 하나의 객체로 볼 수 있음
가독성 좋고 유지보수하기 좋은 코드를 작성할 수 있다.

[중첩 클래스 종류]
정적 멤버 클래스
(비정적) 멤버 클래스
이 중 첫번째를 제외한 나머지 클래스들은 내부 클래스에 해당한다.

[정적 멤버 클래스]
정적 멤버 클래스와 비정적 멤버 클래스를 구분하는 기준은 
static 키워드가 함께 작성되었는지 여부로 판단할 수 있다.

```java
public class OuterClass {
    private String message = "Hello from OuterClass";
    // 정적 내부 클래스
    static class StaticInnerClass {
        public void display() {
            // 정적 내부 클래스는 외부 클래스의 인스턴스를 참조할 수 없습니다.
            System.out.println(message); // 에러: 외부 클래스의 인스턴스 변수에 접근 불가
            OuterClass.this.message;
            OuterClass outerClass = new OuterClass();
            System.out.println(OuterClass.message);
            System.out.println("Inside Static Inner Class");
        }
    }

    // 비정적 내부 클래스
//    class NonStaticInnerClass {
//        public void display() {
//            // 비정적 내부 클래스는 외부 클래스의 인스턴스를 암묵적으로 참조합니다.
//            // 외부 클래스의 인스턴스 변수에 접근할 수 있습니다.
//            System.out.println("Message from OuterClass: " + message);
//            System.out.println("Inside Non-Static Inner Class");
//        }
//    }

    public void createClasses() {
        // 정적 내부 클래스는 외부 클래스 인스턴스 없이 생성 가능
        OuterClass.StaticInnerClass staticInner = new OuterClass.StaticInnerClass();
        staticInner.display();  // 출력: Inside Static Inner Class

//        // 비정적 내부 클래스는 외부 클래스의 인스턴스가 필요
//        OuterClass.NonStaticInnerClass nonStaticInner = new OuterClass().new NonStaticInnerClass();
//        nonStaticInner.display();  // 출력: Inside Non-Static Inner Class
    }
}
```

이처럼 static 키워드와 함께 작성된 InnerClass는 정적 내부 클래스이다.
이런 정적 내부 클래스는 외부 클래스의 private 멤버에도 접근할 수 있다.

외부 클래스의 private 멤버에 접근할 수 있다는점만 빼면 일반 클래스와 똑같다.

개념상 중첩 클래스의 인스턴스가 바깥 인스턴스와 독립적으로 
존재할 수 있다면 정적 멤버 클래스로 만들어야한다.
static이 붙은 정적 멤버 클래스와 비정적 멤버 클래스의 차이는 
외부 인스턴스없이 내부 인스턴스를 바로 생성할 수 있다는 점이다.

[비정적 멤버 클래스]
static 키워드가 없이 작성된 클래스는 비정적 내부 클래스이다.

[비정적 멤버 클래스 선언]

```java
class OuterClass {
private int a = 100;

    class InnerClass {
        private int b;
    }
}
```
static 키워드에 따라 정적 그리고 비정적 멤버 클래스의 생성 방법이 달라진다.
비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다.
비정적 멤버 클래스는 this 키워드로 바깥 인스턴스의 메서드를 호출하거나 참조를 가져올 수 있다.
이때 정규화된 this를 사용하는데 정규화된 this란 클래스명.this 형태로 바깥 클래스의 이름을 명시하는 용법을 말한다.
비정적 멤버 클래스는 바깥 클래스의 존재 없이 존재할 수 없다. 따라서 비정적 멤버 클래스를 독립적으로 사용하려면 정적 멤버 클래스로 만들어야 한다.


[비정적 멤버 클래스 바깥 클래스 메서드 호출 - 암묵적 연결]

```java
class OuterClass {
    private int a = 100;

    public void createClass() {
        System.out.println("OuterClass method called");
    }

    class InnerClass {
        private int b;

        void accessOuterClass() {
            // 바깥 클래스의 메서드를 호출하는 방법
            OuterClass.this.createClass(); // 바깥 클래스의 createClass 메서드 호출
            System.out.println("InnerClass accessing OuterClass method");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        // 외부 클래스 인스턴스 생성
        OuterClass outer = new OuterClass();

        // 외부 클래스 인스턴스를 사용하여 비정적 내부 클래스 인스턴스 생성
        OuterClass.InnerClass inner = outer.new InnerClass();

        // 비정적 내부 클래스의 메서드 호출
        inner.accessOuterClass();
    }
}
```

비정적 멤버 클래스 활용
비정적 멤버 클래스는 어댑터를 정의할 때 자주 사용된다.

어댑터 패턴이란?
어떤 클래스의 인스턴스를 감싸 마치 다른 클래스의 인스턴스처럼 보이게하는 뷰로 사용하는 것.


[어댑터 정의 - 비정적 멤버 클래스 사용(반복자 구현)]

```java
public class MySet<E> extends AbstractSet {
    @Override
    public Iterator<E> iterator() {
        return new MyIterator(); // MyIterator는 비정적 멤버 클래스
    }

    private class MyIterator implements Iterator<E> {
        // 반복자 구현
    }
}
```
비정적 멤버 클래스로 어댑터 구현시

바깥 인스턴스로부터 숨은 외부 참조를 갖게 된다.
이 참조를 저장하려면 시간과 공간이 소비된다.
GC(가비지 컬렉션)이 바깥 클래스의 인스턴스를 수거하지 못하는 메모리 누수가 생길 수 있다.

참조가 눈에 보이지 않아 문제의 원인을 찾기 어렵다.
따라서, 멤버 클래스에서 바깥 클래스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.

[어댑터 정의 - 정적 멤버 클래스 사용(반복자 구현)]

```java
public class MySet<E> extends AbstractSet {
    @Override
    public Iterator<E> iterator() {
        return new MyIterator(); // MyIterator는 정적 멤버 클래스
    }

    private static class MyIterator implements Iterator<E> {
        // 반복자 구현
    }
}
```
정적 멤버 클래스인 MyIterator는 외부 클래스의 인스턴스를 참조하지 않습니다. 
정적 클래스는 클래스 수준에서 동작하기 때문에 외부 클래스의 인스턴스와 독립적입니다. 
그래서 MyIterator는 외부 클래스의 인스턴스를 참조하지 않으므로, 
암묵적인 참조가 존재하지 않아서 메모리 누수를 방지할 수 있습니다.