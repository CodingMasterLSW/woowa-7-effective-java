# wait과 notify보다는 동시성 유틸리티를 애용하라

일단 `wait()`과 `notify()`가 뭔지 알아보자.

## wait(), notify()

- wait() : 락을 반납하고 대기한다.
- notify() : 락을 반납하고 wait()중인 스레드 하나를 깨운다.

어떻게 사용되는지 예제를 통해 이해해보자.

### Data

```java
public class Data {

    private String message;
    private boolean hasMessage = false;

    public synchronized void put(String message) throws InterruptedException {
        while (hasMessage) {
            wait();
        }
        this.message = message;
        hasMessage = true;
        System.out.println("메시지 추가: " + message);
        notify();  // 소비자를 깨움
    }

    public synchronized String get() throws InterruptedException {
        while (!hasMessage) {
            wait();
        }
        hasMessage = false;
        System.out.println("메시지 반환: " + message);
        notify();  // 생산자를 깨움
        return message;
    }
}
```

### Consumer

```java
public class Consumer extends Thread {
    
    private final Data data;
    
    public Consumer(Data data) {
        this.data = data;
    }
    
    @Override
    public void run() {
        for (int i = 0; i < 3; i++) {
            try {
                this.data.get();
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            
        }
    }
}
```

### Producer

```java
public class Producer extends Thread {
    
    private final Data data;
    
    public Producer(Data data) {
        this.data = data;
    }
    
    @Override
    public void run() {
        String[] messages = new String[]{"message1", "message2", "message3"};
        for (String message : messages) {
            try {
                data.put(message);
                Thread.sleep(300);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            
        }
    }
}
```

### Main

```java
public class Main {
    
    public static void main(String[] args) {
        Data data = new Data();
        new Producer(data).start();
        new Consumer(data).start();
    }
}
```

### 정리

1. `Producer`가 데이터를 생성하려고 들어간다. (message1)
2. 생성한다.
3. `Producer`가 데이터를 생성하려고 들어간다. (message2)
4. 이미 생성되어 있다. wait 상태로 돌입한다.
5. `Consumer`가 데이터를 출력하려고 들어간다.
6. 출력한다. (message1)
7. notify 한다.
8. `Producer`가 데이터를 마저 생성한다. (message2)
9. ...

위와 같은 형태로 락을 들고 있는 객체가 내부에서 행동을 더 진행할 수 없을 때, 일단 락을 반환하고 그곳에서 기다리게 하기 위해 사용하는 메서드들이다.
좀 어렵다. 이거말고 더 쉽게 사용하도록 제공된 객체들이 있으니 그것을 사용하자.

## 실행자 프레임워크

- 스레드 생성 및 실행을 편하게 해준다.
- ExecutorService 등이 대표적이다.

## 동기화 장치

- 스레드 간 작업 조율을 돕는 도구이다.

## 동시성 컬렉션

- 동시성이 고려된 컬렉션들이다.
- `ConcurrentHashMap`, `CopyOnWriteArrayList` 등이 대표적이다.
- 상태 의존적 메서드(`putIfAbsent()` 등)를 제공하여 원자적 연산을 지원한다.

### Member

```java
public class Member {
    
    private final String name;
    
    public Member(final String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
}
```

### MemberRepository

```java
public interface MemberRepository {
    
    void save(Member member);
    
    Optional<Member> getByName(String name);
}

public class LocalMemberRepository implements MemberRepository {

    private final Map<String, Member> members = new ConcurrentHashMap<>();

    @Override
    public void save(final Member member) {
        members.put(member.getName(), member);
    }

    @Override
    public Optional<Member> getByName(final String name) {
        return Optional.ofNullable(members.get(name));
    }
}
```

#### 내부 구현

```java
public V put(K key, V value) {
        return putVal(key, value, false);
    }

final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh; K fk; V fv;
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value)))
                break;                   // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else if (onlyIfAbsent // check first node without acquiring lock
                 && fh == hash
                 && ((fk = f.key) == key || (fk != null && key.equals(fk)))
                 && (fv = f.val) != null)
            return fv;
        else {
            V oldVal = null;
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key, value);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                    else if (f instanceof ReservationNode)
                        throw new IllegalStateException("Recursive update");
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```

복잡하다. 알 필요는 없다. 동시성이 고려되었다는 사실만 인지하자.
