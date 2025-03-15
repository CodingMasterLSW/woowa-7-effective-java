# 아이템54 null이 아닌, 빈 컬렉션이나 배열을 반환하라

```java
List<Chesse> cheeseList;

public List<Cheese> getCheeseList(){
    return cheeseList.isEmpty() ? null : new ArrayList<>(cheeseList);
}
```

이 코드처럼 null을 반환한다면, 클라이언트는 이 null 상황을 처리해주는 코드를 추가로 작성해야 한다.

```JAVA
if(cheeseList != null){
    system.out.println("재고 있음");
}
else {
    어쩌고 저쩌고...    
}
```

컬렉션이나 배열 같은 컨테이너가 비었을 때 null을 반환하는 메서드를 사용할때면 이렇게 방어코드를 넣어줘야한다.
실제로 객체가 0개일 가능성이 거의 없는 상황에서는 수년 뒤에야 오류가 발생하기도 한다.

null을 반환 하려면 반환하는 쪽에서도 이 상황을 특별히 취급해야해서 코드가 복잡해진다.
때로는 빈 컨테이너를 할당하는데도 비용이 드니 null을 반환하는 쪽이 낫다는 주장도 있긴하다.

```javva
public List<Cheese> getCheese(){
    return new ArrayList<>(cheesesInStock);
}
```
빈 컬렉션을 안전하게 반환하는 코드이다. 만약 이러한 할당이 비용 문제로 싫다면

```JAVA
import java.util.ArrayList;
import java.util.Collections;

public List<Cheese> getCheese() {
    return cheeseList.isEmpty() ? Collections.emptyList() : new ArrayList<>(cheeseList);
}
```

매번 똑같은 빈 불변 컬렉션을 반환하는 것이다.

핵심정리
null이 아닌, 빈 배열이나 컬렉션을 반환하라. null을 반환하는 API는 사용이 어렵고 오류 처리 코드가 늘어난다.
성능이 좋은것도 아니다.