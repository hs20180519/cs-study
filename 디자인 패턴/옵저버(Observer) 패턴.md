# 01. 옵저버(Observer) 패턴
**개념**
- 옵저버 패턴은 객체 간 일대다(1:N) 관계를 정의하여, 한 객체의 상태가 변경될 때 이를 의존하는 다른 객체(Observer)들에게 자동으로 알리고 갱신할 수 있도록 하는 디자인 패턴이다.
- 주로 이벤트 기반 시스템에서 사용되며, 게시-구독(Pub-Sub) 모델과 유사하다.  

**예시**
- 유튜브 채널 발행자(Subject)와 구독자들 관찰자(Observer)
- 유튜버가 영상을 올리면 -> 여러명의 구독자들은 모두 영상이 올라왔다는 알림을 받음
- 구독자들은 해당 채널을 구독함으로써 채널에 어떠한 변화(영상을 올리거나 커뮤니티에 글을 쓰거나)가 생기게 되면 바로 연락을 받는 것


# 02. 옵저버(Observer) 패턴 구조와 흐름  
옵저버 패턴은 크게 **Subject(주체)** 와 **Observer(관찰자)** 로 구성된다.  

### 1. 주요 구성 요소  
<img src="https://madplay.github.io/img/post/2018-04-20-observer-pattern-1.jpg">

- **ISubject (인터페이스/추상 클래스)**  
  - 관찰 대상자가 구현해야 하는 인터페이스를 정의한다.  
  - 옵저버를 등록/삭제하는 메서드를 제공한다.  
  - 상태가 변경될 때 옵저버들에게 알리는 메서드를 포함한다.  

- **ConcreteSubject (구체적인 관찰 대상, 발행자, 게시자)**  
  - 상태를 관리하는 실제 객체이다.  
  - 상태 변경이 발생하면 등록된 옵저버들에게 변경 사항을 알린다.  

- **IObserver (인터페이스/추상 클래스)**  
  - 관찰자가 구현해야 하는 인터페이스를 정의한다.  
  - `update()` 메서드를 통해 상태 변경을 반영할 수 있도록 한다.  

- **ConcreteObserver (구체적인 관찰자, 구독자)**  
  - 특정 Subject 를 구독하며, Subject 의 상태 변경을 감지한다.  
  - `update()` 메서드를 통해 변화된 상태를 반영한다.  



### 2. 옵저버 패턴의 흐름  
1. **Observer(구독자)** 가 **Subject(게시자)** 에게 자신의 존재를 등록한다.  
2. **Subject** 의 상태가 변경되면, 등록된 모든 **Observer** 에게 변경 사항을 알린다.  
3. **Observer** 들은 **update()** 메서드를 실행하여 변경된 상태를 반영한다.  


# 03. 장점 / 단점  

### 장점  
- **객체 간 결합도(Dependency) 감소**  
  - Subject 와 Observer 가 직접적으로 강하게 결합되지 않기 때문에 유연성이 높다.  
- **자동 상태 동기화**  
  - Subject 의 상태 변경이 자동으로 모든 Observer 에게 반영된다.  
- **확장성이 뛰어남**  
  - 새로운 Observer 를 추가해도 기존 코드를 수정할 필요가 거의 없다.  

### 단점  
- **예측이 어려운 흐름**  
  - 여러 개의 Observer 가 존재할 경우, 특정 Observer 가 예상치 못한 방식으로 동작할 가능성이 있다.  
- **Observer 가 많아질수록 성능 문제 발생 가능**  
  - Subject 가 상태 변경 시 모든 Observer 에게 알리므로 성능 저하가 발생할 수 있다.  
- **메모리 누수 가능성**  
  - Subject 가 더 이상 필요하지 않은 Observer 를 해제하지 않으면 메모리 누수가 발생할 수 있다.  


# 04. 예제 (Java)  
```java
import java.util.ArrayList;
import java.util.List;

// Subject 인터페이스
interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// ConcreteSubject (관찰 대상)
class NewsPublisher implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;

    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (Observer o : observers) {
            o.update(news);
        }
    }
}

// Observer 인터페이스
interface Observer {
    void update(String news);
}

// ConcreteObserver (구독자)
class NewsSubscriber implements Observer {
    private String name;

    public NewsSubscriber(String name) {
        this.name = name;
    }

    @Override
    public void update(String news) {
        System.out.println(name + " received news: " + news);
    }
}

// 실행 코드
public class ObserverPatternExample {
    public static void main(String[] args) {
        NewsPublisher publisher = new NewsPublisher();
        
        Observer subscriber1 = new NewsSubscriber("User A");
        Observer subscriber2 = new NewsSubscriber("User B");

        publisher.registerObserver(subscriber1);
        publisher.registerObserver(subscriber2);

        publisher.setNews("Breaking News: Observer Pattern in Java!");
    }
}
```
### 실행 결과  
```
User A received news: Breaking News: Observer Pattern in Java!
User B received news: Breaking News: Observer Pattern in Java!
```

---

# 05. JAVA Observable  

<img src="https://t1.daumcdn.net/cfile/tistory/9924E63359FDBF4B35">

### 사용 예제 (Deprecated 주의)  
```java
import java.util.Observable;
import java.util.Observer;

class NewsPublisher extends Observable {
    private String news;

    public void setNews(String news) {
        this.news = news;
        setChanged();
        notifyObservers(news);
    }
}

class NewsSubscriber implements Observer {
    private String name;

    public NewsSubscriber(String name) {
        this.name = name;
    }

    @Override
    public void update(Observable o, Object arg) {
        System.out.println(name + " received news: " + arg);
    }
}

public class ObservableExample {
    public static void main(String[] args) {
        NewsPublisher publisher = new NewsPublisher();
        Observer subscriber1 = new NewsSubscriber("User A");
        Observer subscriber2 = new NewsSubscriber("User B");

        publisher.addObserver(subscriber1);
        publisher.addObserver(subscriber2);

        publisher.setNews("Breaking News: Observable in Java!");
    }
}
```

### 주의사항
- `java.util.Observable` 과 `java.util.Observer` 는 Java 9 부터 Deprecated 됐다.
- java.util.Observable은 클래스이기 때문에 구현에 제한이 있다.

### 프론트엔드의 여러 상태관리(Redux, Vuex, MobX)도 옵저버 패턴! 
- **Redux (React)**: `store.subscribe(listener)` 로 상태 변경을 감지하는 구조  
- **Vuex (Vue)**: `watch()` 를 활용하여 상태 변경 감지  
- **MobX**: `autorun()` 을 활용한 반응형 상태 관리  

# 07 옵저버 패턴 vs 퍼블리셔-구독자 패턴
1. 옵저버(Observer) 패턴
- 개념
옵저버 패턴은 일대다(1:N) 관계 를 가지며, 한 객체(Subject) 의 상태가 변경되었을 때, 여러 객체(Observer) 들이 이를 감지하고 자동으로 업데이트된다.

- 동작 방식
Subject(발행자, 게시자)는 Observer(구독자, 감시자)들의 목록을 관리한다.
Observer들은 Subject에 직접 등록(attach(), addObserver())하여 변경 사항을 받을 준비를 한다.
Subject의 상태가 변경되면, Subject는 등록된 모든 Observer 에게 직접 notify() 를 호출하여 변경 사항을 알린다.
Observer들은 update() 메서드를 실행하여 변경된 상태를 반영한다.

- 옵저버 패턴의 주요 특징
✔ Subject(발행자)와 Observer(구독자)가 직접 연결된다.
✔ Subject가 상태 변경 시, 모든 Observer에게 직접 알림을 준다.
✔ Observer는 자신이 어떤 Subject를 구독하고 있는지 알고 있어야 한다.

## 퍼블리셔-구독자(Publisher-Subscriber) 패턴
- 개념
퍼블리셔-구독자 패턴은 중앙 브로커(Event Bus 또는 Message Queue) 를 통해 메시지를 전달하는 방식이다.

- 동작 방식
Publisher(발행자) 는 Event Broker(이벤트 버스, 메시지 큐) 에 메시지를 보낸다.
Subscriber(구독자) 는 특정 유형의 이벤트를 Event Broker에 등록한다.
Event Broker는 Publisher와 Subscriber를 직접 연결하지 않고, 이벤트를 구독자에게 전달한다.
Publisher는 Subscriber가 몇 명인지, 누구인지 전혀 알지 못하며, 단순히 메시지를 전송한다.

- 퍼블리셔-구독자 패턴의 주요 특징
✔ Publisher와 Subscriber는 서로 알지 못하며, 중앙 브로커를 통해 간접적으로 연결된다.
✔ 비동기 메시징이 가능하며, 분산 시스템에서도 유용하게 사용된다.
✔ 확장성이 뛰어나며, 여러 개의 서비스(마이크로서비스) 간 통신에도 적합하다.

- 예제 (Java - EventBus 활용)
``` java
import com.google.common.eventbus.EventBus;
import com.google.common.eventbus.Subscribe;

// 이벤트 클래스
class EventMessage {
    private final String message;

    public EventMessage(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}

// Subscriber (구독자)
class EventListener {
    @Subscribe
    public void handleEvent(EventMessage event) {
        System.out.println("Received event: " + event.getMessage());
    }
}

public class PubSubExample {
    public static void main(String[] args) {
        EventBus eventBus = new EventBus();

        // Subscriber 등록
        EventListener listener = new EventListener();
        eventBus.register(listener);

        // Publisher가 메시지를 전송 (Subscriber가 직접 알지 못함)
        eventBus.post(new EventMessage("Hello, Pub-Sub!"));
    }
}
```
➡ Publisher는 Subscriber를 직접 호출하지 않고, EventBus가 이벤트를 전달함.

### 결론 
- 옵저버 패턴 은 직접적인 1:N 관계 로, 한 객체의 변경 사항을 여러 객체에게 즉시 알리는 데 적합하다.
- 퍼블리셔-구독자 패턴 은 중앙 브로커를 통해 간접적으로 연결되며, 비동기 이벤트 처리 및 분산 시스템에 적합하다.
- GUI나 작은 범위의 이벤트 처리 에는 옵저버 패턴을, 대규모 시스템이나 마이크로서비스 에는 퍼블리셔-구독자 패턴을 사용하는 것이 좋다.


# 06. 면접에서 자주 나오는 질문 / 답변  

### Q. 옵저버 패턴과 이벤트 기반 프로그래밍의 차이점은?  
옵저버 패턴은 명시적으로 `Observer` 를 등록해야 하며, `notify()` 를 호출하여 수동으로 변경 사항을 알린다.  
반면 이벤트 기반 프로그래밍(예: JavaScript 이벤트 리스너)은 **비동기적이며, 보다 유연한 트리거 시스템을 제공**한다.  


### Q. 옵저버 패턴과 퍼블리셔-구독자 패턴의 차이는?  
- 옵저버 패턴은 **Subject 가 직접 Observer 목록을 관리**한다.  
- 퍼블리셔-구독자 패턴은 **중앙 이벤트 브로커(Event Bus)** 를 통해 통신하므로 더욱 느슨한 결합이 가능하다.  

---
출처: https://inpa.tistory.com/entry/GOF-💠-옵저버Observer-패턴-제대로-배워보자 [Inpa Dev 👨‍💻:티스토리]

