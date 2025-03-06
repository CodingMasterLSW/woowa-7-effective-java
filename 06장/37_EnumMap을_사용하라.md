# EnumMap

- 어떤 열거형 상수를 통해 원하는 값을 찾고 싶을 때 어떻게 하면 좋을까?
- 우테코 사람들을 직위에 따라 분류하여 저장해보자.

## ordinal + 배열 사용

```java
enum WootecoCategory {
    크루,
    코치,
    캡틴,
}

static class WootecoUser {
    private final String name;
    private final WootecoCategory category;

    public WootecoUser(final String name, final WootecoCategory category) {
        this.name = name;
        this.category = category;
    }
}

public static void main(String[] args) {
    Set<WootecoUser>[] wootecoUserOrganizedByCategory = (Set<WootecoUser>[]) new Set[WootecoCategory.values().length];
    for (int i = 0; i < WootecoCategory.values().length; i++) {
        wootecoUserOrganizedByCategory[i] = new HashSet<>();
    }

    WootecoUser[] users = new WootecoUser[]{
            new WootecoUser("돔푸", WootecoCategory.크루),
            new WootecoUser("포라", WootecoCategory.크루),
            new WootecoUser("젠슨", WootecoCategory.크루),
            new WootecoUser("가이온", WootecoCategory.크루),
            new WootecoUser("리사", WootecoCategory.코치),
            new WootecoUser("네오", WootecoCategory.코치),
            new WootecoUser("포비", WootecoCategory.캡틴)
    };

    for (WootecoUser user : users) {
        wootecoUserOrganizedByCategory[user.category.ordinal()].add(user);
    }
}
```

- 윽 더럽다.
- 첫번째로 배열을 생성할 때 제네릭과 호환되지 않기에, 비검사 형변환을 해야 한다.
- 또한 정확한 정수값(ordinal)을 사용한다는 것을 개발자가 직접 보증해야 한다.
- 이것 말고 `EnumMap` 을 쓰자.

## EnumMap 사용

```java
WootecoUser[] users = new WootecoUser[]{
        new WootecoUser("돔푸", WootecoCategory.크루),
        new WootecoUser("포라", WootecoCategory.크루),
        new WootecoUser("젠슨", WootecoCategory.크루),
        new WootecoUser("가이온", WootecoCategory.크루),
        new WootecoUser("리사", WootecoCategory.코치),
        new WootecoUser("네오", WootecoCategory.코치),
        new WootecoUser("포비", WootecoCategory.캡틴)
};

// 방법 1
EnumMap<WootecoCategory, Set<WootecoUser>> wootecoUserOrganizedByCategory = new EnumMap<>(WootecoCategory.class);

for (WootecoUser user : users) {
    wootecoUserOrganizedByCategory.putIfAbsent(user.category, new HashSet<>());
    wootecoUserOrganizedByCategory.get(user.category).add(user);
}

// 방법 2
EnumMap<WootecoCategory, Set<WootecoUser>> result2 = Arrays.stream(users).collect(Collectors.groupingBy(
        user -> user.category,
        () -> new EnumMap<>(WootecoCategory.class), Collectors.toSet()
));
```

- 아주 깔끔하다. 👍
- 내부 구현은 배열이므로 성능은 동일하다. (빠르다)
- 훨씬 타입 안정적이다.
