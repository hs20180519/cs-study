# 01. 문자열 클래스

- string은 참조형 변수(reference type)로 분류

## string은 상수

- 한번 생성하여 할당된 메모리 공간은 변하지 않음을 의미
- string 객체의 값은 변경할 수 없음(불변, **immutable**)
- Heap 메모리 영역에 생성되어, GC의 대상이 됨

❓ 문자열 내용을 변경하면:

→ 새로운 메모리를 할당하여 참조하고 원래의 객체는 GC의 대상이 됨

- 문자열 연산 시에는 새로 객체를 만드는 오버헤드가 발생

```java
String str1 = "abc";
String str2 = "def";
String str3 = str1 + str2; // 새로운 String 객체 생성됨
```

### 왜 불변일까?

**1. JVM**

JVM에서는 String Constant Pool이라는 독립적인 영역이 있는데,

이곳에서 문자열을 상수화하여 다른 변수, 객체들과 공유

이 과정에서 **데이터 캐싱**이 일어나고 그 만큼 성능적 이득을 취할 수 있음

**2. thread-safe**

데이터가 불변하면 멀티스레드 환경에서 동기화 문제가 발생할 가능성이 없어 더욱 안전함

이는 조회 연산에 매우 큰 장점이 됨

**3. 보안**

문자열 값 변경이 가능하다면 해킹 가능성 있고, 원래의 값인지 확신할 수 없음

⇒ 문자열 연산이 적고, 조회가 많은 멀티스레드 환경에 적합

## 주소 할당 방식

### 리터럴

- string constant pool에 존재
- constant pool에 이미 같은 문자열이 있으면 해당 메모리 위치를 가리킴

→ 리터럴 방식으로 할당된 같은 문자열 내용을 가지는 변수는 같은 메모리 주소를 참조한다. 메모리 절약 효과

### new 연산자

- heap 영역에 존재
- 변수를 선언할 때마다 메모리 공간이 할당됨

## 문자열 비교 방식

### ==

- 두 비교대상의 주소값을 비교

### equals()

- 두 비교대상의 값 자체를 비교

```java
String s1 = new String("java");
String s2 = new String("java");
String s3 = "java";
String s3 = "java";

System.out.println(s1 == s2); // false;
System.out.println(s1.equals(s2)); // true

System.out.println(s3 == s4); // true
System.out.println(s3.equals(s4)) // true

```

![Image](https://github.com/user-attachments/assets/bfb8e592-2732-4da6-b612-3efd94e99bec)

## intern()

- 해당 문자열이 string constant pool에 존재하면 해당 참조를 반환하고,
- 존재하지 않으면 해당 문자열을 string constant pool에 등록

### intern()을 활용하여 문자열 비교 성능 높이기

- string constant pool에 저장된 문자열은 동일한 내용이라면 동일한 참조를 가짐
- intern()으로 문자열 참조를 string constant pool에서 통일한 후, == 연산자를 사용하여 성능을 높이고 메모리를 줄일 수 있음

```java
String str1 = new String("hello");
String str2 = new String("hello");

String internedStr1 = str1.intern();
String internedStr2 = str2.intern();

System.out.println(internedStr1 == internedStr2); // true
System.out.println(internedStr1.equals(internedStr2)); // true

```

## StringBuilder vs. StringBuffer

### 공통점

- 주로 문자열을 동적으로 변경하거나 조작하는 용도로, mutable하게 설계
- 연산 중 기존 객체의 공간이 부족하면 내부적으로 가진 바이트 버퍼 공간을 확장하여 유연하게 동작
    
    → 새로운 인스턴스를 만들지 않아도 문자열 변경 가능
    
- 제공하는 메서드 동일

### 차이점

- StringBuffer는 `synchronized` 키워드로 동기화 지원
    
    → thread-safe
    
    → 단일 스레드에서는 동기화로 인한 불필요한 성능 저하가 있음
    

⇒ 문자열 연산이 잦은 멀티 스레드 환경이라면 StringBuffer, 문자열 연산이 잦은 단일 스레드 환경이라면 StringBuilder

## Compact String (Java 9~)

- 영어의 경우 문자 하나당 1byte로 처리 가능한데, 이전까지 자바는 UTF-16를 사용하면서 2byte의 공간을 차지하고 있었음
- 이를 Latin 1로 변경(1byte)

→ 영어는 Latin 1, 나머지 언어는 UTF-16으로 처리

- 한글의 경우에는 약 2byte를 차지하여 Compact String을 적용할 수 없음

# 02. java.lang.Object

- 자바의 모든 객체의 최상위 클래스
- 클래스를 선언할 때 extends 키워드로 다른 클래스를 상속하지 않으면 암시적으로 object 클래스를 상속하게 됨

    → 자바의 모든 클래스는 object 클래스의 자식이거나 자손 클래스

- 필드 없음. 메소드로 구성되어 있음

## 메서드

- `toString()`: 객체의 정보를 string으로 바꿔 사용할 때
- `equals()`: 두 인스턴스의 주소값을 비교. 재정의하여 논리적으로 동일한지 비교 가능
- `hashCode()`: 인스턴스의 주소값 저장

<br>
  
💡 두 인스턴스가 동일하다:

- 두 인스턴스의 equals() 값이 true
- 두 인스턴스의 hashCode() 값이 동일

  ⇒ 같은 값을 가진 객체라도 해시값이 다를 수 있으므로, haseCode()를 재정의하여 hashTable에서 같은 값을 찾도록 해야 함

  [[Java] 객체비교시 왜 equals()와 hashCode() 둘 모두를 재정의해야 하는가?](https://velog.io/@mooh2jj/equals%EC%99%80-hashCode%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80)

<br>

- `wait()`: 갖고 있는 고유 lock을 해제하고 thread를 잠들게 함
- `notify()`: 잠들어 있던 thread 중 임의의 하나를 깨움
- `notifyAll()`: 잠들어 있던 thread를 모두 깨움

⇒ 호출하는 thread가 반드시 고유 락을 갖고 있어야 하며, synchronized 블록 내에서 실행되어야 한다.

- clone()
    - 현재 객체의 원본을 복제
    - 객체의 정보가 동일한 또 다른 인스턴스가 생성되는 것
        
        → OOP에서의 정보 은닉, 객체 보호의 관점에서 위배될 수 있음
        
        → Clonable 인터페이스를 implements 필
        
    - 얕은 복사(주소값 복사)를 수행
        
        → 복사의 대상인 객체가 mutable하다면 문제가 생길 수 있음
        

# 03. 자주 나오는 질문

### String과 StringBuffer은 어떤 차이가 있나요?

String과 StringBuffer는 모두 문자열을 나타내는 클래스입니다. 하지만 String 클래스는 불변적 성질을 가져, 한 번 생성한 객체의 값은 변경할 수 없습니다. 따라서 수정 작업의 경우 새로운 객체를 생성해야 합니다. 반면, StringBuffer는 가변적 성질을 가집니다. 따라서 새로운 객체를 생성할 필요 없이 값의 변경이 가능하여, 수정 작업이 여러 번 필요한 경우에 적합합니다.

### StringBuffer와 StringBuilder의 차이점에 대해 설명해 주세요.

두 클래스의 차이는 동기화 지원 여부에 있습니다. StringBuffer는 동기화를 지원하여 멀티스레드 환경에서 주로 사용되는 반면, StringBuilder는 동기화를 지원하지 않아 단일 스레드 환경에서 주로 사용됩니다.

### ==과 equals의 차이점이 무엇인가요?

==은 두 객체의 참조(메모리 주소)값을 비교하는 연산자로, 두 객체의 참조값이 같을 때만 true값을 반환합니다. equals 메서드는 두 객체가 논리적으로 동등한지 비교하는 메서드입니다. 두 객체가 가지고 있는 값이 동일한 경우에 true값을 반환하며, 객체의 클래스에 따라 재정의가 가능합니다.

### 문자열 변수 선언 방식의 종류와 차이에 대해 설명해 주세요.

문자열 변수는 new 키워드를 사용하거나 리터럴 방식을 사용하여 선언할 수 있습니다. new 키워드를 사용할 경우에는 같은 문자열이 이미 생성되었는지 여부에 상관없이 새로운 객체를 생성하여 heap 메모리 영역에 저장됩니다. 리터럴 방식을 사용하면 string constant pool 영역에 저장되는데, 해당 공간에 이미 같은 문자열이 존재한다면 생성하지 않고 해당 문자열의 참조값을 공유하게 됩니다.

# 04. 참고자료

https://velog.io/@jmjmjmz732002/StringBuilder-%EC%99%9C-%EC%93%B8%EA%B9%8C

https://simple-ing.tistory.com/3

https://nelmm.gitbook.io/nelmm/6./compact-string-string-plus

https://velog.io/@tsi0521/Java%EC%9D%98-Object-Class-%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
