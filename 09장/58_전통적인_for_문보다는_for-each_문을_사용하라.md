# 전통적인 for 문보다는 for-each 문을 사용하라

## 전통적인 for 문으로 순회하기

### LottoNumber

```java
public class LottoNumber {
    
    private final int value;
    
    public LottoNumber(final int value) {
        this.value = value;
    }

    @Override
    public String toString() {
        return "LottoNumber[" + value + "]";
    }
}
```

### for문으로 순회하기

```java
public static void main(String[] args) {
    final LottoNumber number1 = new LottoNumber(1);
    final LottoNumber number2 = new LottoNumber(2);
    final LottoNumber number3 = new LottoNumber(3);
    final LottoNumber number4 = new LottoNumber(4);
    
    final List<LottoNumber> numbers = List.of(number1, number2, number3, number4);
    
    for (int i = 0; i < numbers.size(); i++) {
        System.out.println(numbers.get(i));
    }
}

/* 결과
LottoNumber[1]
LottoNumber[2]
LottoNumber[3]
LottoNumber[4]  
 */
```

복잡하다.
`for-each`를 사용하면 더욱더 읽기 좋은 순회를 할 수 있다.

## for-each 문을 사용하여 순회하기

```java
public static void main(String[] args) {
    final LottoNumber number1 = new LottoNumber(1);
    final LottoNumber number2 = new LottoNumber(2);
    final LottoNumber number3 = new LottoNumber(3);
    final LottoNumber number4 = new LottoNumber(4);
    
    final List<LottoNumber> numbers = List.of(number1, number2, number3, number4);

    final LottoNumber[] numbers2 = new LottoNumber[]{number1, number2, number3, number4};
    
    for (LottoNumber number : numbers) {
        System.out.println(number);
    }

    for (LottoNumber number : numbers2) {
        System.out.println(number);
    }
}

/* 결과
LottoNumber[1]
LottoNumber[2]
LottoNumber[3]
LottoNumber[4]

LottoNumber[1]
LottoNumber[2]
LottoNumber[3]
LottoNumber[4]  
 */
```

- 깔끔하다.
- 컬렉션, 배열은 이미 `for-each`를 위한 준비가 되어있다. -> 그냥 바로 사용하면 된다. 

## 만약 내가 새로운 객체에 for-each를 구현하고 싶다면?

### 일급 컬렉션

```java
public class LottoNumbers {
    
    private final List<LottoNumber> lottoNumbers;
    
    public LottoNumbers(final List<LottoNumber> lottoNumbers) {
        this.lottoNumbers = lottoNumbers;
    }
}
```

### 바로 for-each는 안된다.

```java
public static void main(String[] args) {
    final LottoNumber number1 = new LottoNumber(1);
    final LottoNumber number2 = new LottoNumber(2);
    final LottoNumber number3 = new LottoNumber(3);
    final LottoNumber number4 = new LottoNumber(4);

    final LottoNumbers lottoNumbers = new LottoNumbers(number1, number2, number3, number4);

    /* 오류 발생, 당연히 안된다. */
//    for (LottoNumber number : lottoNumbers) {
//        System.out.println(number);
//    }
}
```

### Iterable, Iterator 를 구현해야 한다.

```java
public interface Iterable<T> {
    Iterator<T> iterator();
    
    // 추가 default 메서드들 ...
}

public interface Iterator<E> {
    boolean hasNext();
    E next();

    // 추가 default 메서드들 ...
}
```

### 해당 인터페이스를 구현해보자.

```java
public class LottoNumbers implements Iterable<LottoNumber> {
    
    private final List<LottoNumber> lottoNumbers;
    
    public LottoNumbers(final List<LottoNumber> lottoNumbers) {
        this.lottoNumbers = lottoNumbers;
    }
    
    @Override
    public Iterator<LottoNumber> iterator() {
        return new LottoNumberIterator(lottoNumbers);
    }
    
    private static class LottoNumberIterator implements Iterator<LottoNumber> {
        
        private final Deque<LottoNumber> lottoNumbers;
        
        public LottoNumberIterator(final List<LottoNumber> lottoNumbers) {
            this.lottoNumbers = new ArrayDeque<>(lottoNumbers);
        }
        
        @Override
        public boolean hasNext() {
            return !lottoNumbers.isEmpty();
        }
        
        @Override
        public LottoNumber next() {
            return lottoNumbers.poll();
        }
    }
}
```

### 사실 for-each 는 Iterator 를 돌릴 뿐이다.

```java
public static void main(String[] args) {
    final LottoNumber number1 = new LottoNumber(1);
    final LottoNumber number2 = new LottoNumber(2);
    final LottoNumber number3 = new LottoNumber(3);
    final LottoNumber number4 = new LottoNumber(4);

    final LottoNumbers lottoNumbers = new LottoNumbers(number1, number2, number3, number4);

    Iterator<LottoNumber> iterator = lottoNumbers.iterator();
    while (iterator.hasNext()) {
        LottoNumber value = iterator.next();
        System.out.println(value);
    }
    
    for (LottoNumber lottoNumber : lottoNumbers) {
        System.out.println(lottoNumber);
    }
}
```

### 복잡한 것이 아니라면, 이미 구현된 iterator 를 이용하자.

```java
public class LottoNumbers implements Iterable<LottoNumber> {
    
    private final List<LottoNumber> lottoNumbers;
    
    public LottoNumbers(final List<LottoNumber> lottoNumbers) {
        this.lottoNumbers = lottoNumbers;
    }
    
    @Override
    public Iterator<LottoNumber> iterator() {
        return lottoNumbers.iterator();
    }
}
```
