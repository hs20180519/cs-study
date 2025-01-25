# 00. 개요

## 자바(JDK) 버전별 차이와 특징

### **(1) JDK 1.0**
- 최초의 버전

  
... (JDK 1.0 - JDK 1.4)


### **(2) Java 5**
- 주요 변화로 JDK 1.4와의 구분점:
  - **제네릭**: 타입 안정성 확보.  
  - **어노테이션**: 메타데이터 제공.  
  - **Autoboxing/Unboxing**: 기본 자료형 ↔ 객체 자동 변환.  
  - **enum**: 상수 타입 표현.  
  - **가변 매개변수(varargs)**: 가변 인자 처리 가능.  
  - **향상된 for 루프**: 배열/컬렉션 순회 간결화.  
  - **static import**: static 필드 호출.  
  - **concurrent 패키지**: 멀티쓰레드 환경 개선.  
  - **Scanner 클래스**: 입력 처리 간소화.


### **(3) Java 8 (LTS)**
- **Lambda**: 코드 간결화, 익명 클래스 대체.  
- **Stream API**: 멀티코어 활용 최적화, 데이터 처리 간소화.  
- **Optional**: NullPointerException 예방.  
- **새로운 날짜/시간 API(LocalDateTime 등)**: 사용성 개선.  
- **PermGen 영역 삭제**: 메모리 관리 개선.  
- **장점**: 코드 간결성, 멀티코어 활용, 효율적인 날짜/시간 처리.

### **(4) Java 11 (LTS)**
- **LTS(Long Term Support)** 버전.  
- **String 메서드 추가**: `lines()`, `strip()`, `repeat()`.  
- **HTTP Client 표준화**: HTTP 호출 처리 간편화.  
- **장점**: 안정적인 장기 지원, 유용한 문자열 처리.
- **라이센스 변화** : 

### **(5) Java 17 (LTS)**
- **LTS 버전**.  
- **봉인 클래스(Sealed Class)**: 계층 구조 제한.  
- **난수 생성 API 추가**.  
- **장점**: 최신 표준과 안정성 제공.

### **LTS(Long Term Support) 버전**
- **8, 11, 17**이 주요 LTS 버전.  
- 긴 지원 기간으로 보안 및 버그 수정 제공.  

### **왜 Java 8을 사용할까?**
- **긴 지원기간**: 오랜 기간 Support 보장.  
- **기존 프로젝트와의 호환성**: Java 8로 개발된 프로젝트가 많아 안정적으로 유지보수가 가능하다

### **왜 Java 17을 사용할까?**
- **LTS 지원**: 안정적인 장기 사용 가능.  
- **Spring Boot 3.0 이상**에서 Java 17 이상 요구.  

# 02. JAVA의 멀티스레드와 Stream API 개념

## JAVA의 멀티스레드
- 전통적으로 멀티스레드는 `Thread` 클래스 또는 `ExecutorService`를 활용하여 구현한다.
- 동시성 프로그래밍의 핵심으로, 성능 향상을 위해 데이터를 여러 쓰레드에서 나누어 처리할 수 있다.
- 하지만 직접 쓰레드 관리는 복잡하며, 동기화 문제(synchronized)로 인해 오류가 발생할 수 있다.

## Stream API 개념
- Java 8에서 도입된 데이터 처리 라이브러리.
- 컬렉션 데이터를 **선언형 방식**으로 다룰 수 있으며, 내부 반복을 사용해 코드를 간결하게 작성할 수 있다.
- **병렬 스트림**(`parallelStream`)을 활용하면 멀티코어 프로세서 환경에서 효율적인 병렬처리가 가능하다.

# 03. Stream API의 특징

## 중간 연산
- 데이터를 변환하여 새로운 스트림을 반환하는 연산
- ex: map(), filter(), distinct() 등.

## 최종 연산
-  스트림을 소비하고 결과를 반환하거나 부수 작업을 수행하는 연산
-  ex: collect(), forEach(), reduce()
-  지연 평가(lazy evaluation) 방식을 사용하여, 최종 연산이 호출될 때까지 실제로 수행되지 않는다.

## 병렬 스트림(parallelStream)을 사용할 때 주의할 점
- **데이터가 thread-safe한지 확인해야 합니다.**
  - 병렬 스트림은 데이터를 여러 쓰레드에서 동시에 처리하기 때문에, 공유 자원에 접근할 경우 동기화되지 않으면 데이터 무결성 문제가 발생할 수 있다.  
-  **병렬 처리로 인한 오버헤드**
  - 데이터 크기가 작거나 작업이 간단한 경우, 병렬 처리로 인해 성능이 더 저하될 수 있다.  
-  **순서가 중요한 경우**
  - 병렬 스트림은 기본적으로 데이터 순서를 보장하지 않으므로, 순서가 중요한 작업에서는 `forEachOrdered()` 같은 메서드를 사용하는 것이 좋다.  
-  병렬 스트림은 **CPU 바운드 작업에 적합**하며, IO 바운드 작업에서는 오히려 비효율적일 수 있다.  

## 장점
- **간결한 코드**: 선언형 프로그래밍 방식으로 복잡한 작업을 간단하게 표현할 수 있다.
- **병렬 처리**: `parallelStream`을 통해 멀티스레드 환경에서 데이터를 쉽게 병렬로 처리 가능하다.
- **최적화 가능**: 내부 반복과 중간 연산 병합을 통해 효율적인 데이터 처리가 가능하다.

## 단점
- **디버깅 어려움**: 스트림의 선언적 특성 때문에 **중간 연산 과정의 디버깅이 어렵다**.
- **병렬 스트림 주의**: 병렬 스트림 사용 시 **동기화** 문제가 발생할 수 있으며, 데이터 크기와 CPU 코어 수에 따라 성능 저하의 가능성이 있다다
- **비용 문제**: 스트림 생성 및 처리에 **추가적인 오버헤드**가 발생할 수 있다.

# 04. Stream API 사용 예제
## 1. **map() 예제**
```java
List<String> names = Arrays.asList("Sehoon", "Songwoo", "Chan");

names.stream()
    .map(name -> name.toUpperCase())
    .forEach(System.out::println);
// 출력: SEHOON, SONGWOO, CHAN
```

## 2. **filter() 예제**
```java
List<String> startsWithS = names.stream()
    .filter(name -> name.startsWith("S"))
    .collect(Collectors.toList());
System.out.println(startsWithS);
// 출력: [Sehoon, Songwoo]
```

## 3. **reduce() 예제**
```java
Stream<Integer> numbers = Stream.of(1, 2, 3, 4, 5);
int sum = numbers.reduce(0, Integer::sum);
System.out.println("Sum: " + sum);
// 출력: Sum: 15
```

## 4. **collect() 예제**
```java
String result = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.joining(", "));
System.out.println(result);
// 출력: SEHOON, SONGWOO, CHAN
```




# 05. 면접에서 자주 나오는 질문
## **1. Stream API와 Collection의 차이점은 무엇인가요?**

Stream API와 Collection은 각각 목적과 사용 방식이 다릅니다.  
**Collection**은 데이터를 **저장**하는 데 중점을 둔 인터페이스입니다. 주로 데이터를 외부 반복(for-each, Iterator)을 사용하여 처리하며, 데이터 조작과 저장이 주요 목적입니다.
반면 **Stream API**는 데이터를 처리하는 데 보다 중점을 둔 기능으로, 선언적 방식으로 데이터를 처리합니다. 또한 내부 반복을 통해 처리 효율성을 높이며, 병렬 처리도 지원합니다.  
예를 들어, Collection은 데이터를 수집하거나 저장하는 데 적합하고, Stream API는 데이터를 필터링하거나 매핑하는 복잡한 작업을 간결하게 작성할 수 있습니다.  


## **2. Stream의 중간 연산과 최종 연산 차이점은 무엇인가요?**

Stream의 연산은 **중간 연산**과 **최종 연산**으로 나뉩니다.  
**중간 연산**은 스트림을 변환하여 새로운 스트림을 반환하는 연산입니다. 대표적인 예로는 `filter()`, `map()` 등이 있습니다. 중간 연산은 **lazy evaluation** 방식을 사용하기 때문에, 최종 연산이 호출되기 전까지 실행되지 않습니다.  
반면, **최종 연산**은 스트림을 닫고 결과를 반환하거나 부수 작업을 수행합니다. 예로는 `collect()`, `forEach()` 등이 있습니다. 최종 연산이 호출되면 스트림 파이프라인이 실행됩니다.  
두 연산의 가장 큰 차이는 중간 연산은 스트림을 반환하며, 파이프라이닝이 가능하지만, 최종 연산은 스트림을 닫고 결과를 반환한다는 점입니다.  

## **3. Java 8버전과 11버전의 가장 큰 차이는 무엇인가요?**
Java 8에서는 **람다 표현식**과 **Stream API**가 도입되어 코드의 간결성과 선언적 데이터 처리가 가능해졌습니다. 또한, NullPointerException 방지를 위한 **Optional 클래스**와 새로운 **날짜/시간 API(LocalDate, LocalDateTime 등)**가 추가되었습니다.  
반면, Java 11은 **LTS(Long Term Support)** 버전으로 안정성을 제공합니다. 
11 버전에서는 HTTP 호출을 위한 **HTTP Client API**가 표준화되었으며, 문자열 관련 편의 메서드(`lines()`, `strip()`, `repeat()` 등)가 추가되어 생산성이 더욱 향상되었습니다.  
가장 큰 차이는 Java 8은 선언적 프로그래밍의 도입에 중점을 둔 반면, Java 11은 개발 편의성과 안정성을 강화한 점이라고 볼 수 있습니다.

# 참고 문헌
https://velog.io/@songheechoi/%EC%9E%90%EB%B0%94-%EB%B2%84%EC%A0%84%EB%B3%84-%EC%B0%A8%EC%9D%B4-81ebwlu0#6-java-5
