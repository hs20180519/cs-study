# 00. Java의 데이터 타입

Java에서는 크게 두 가지 데이터 타입으로 구분된다.

→ 기본형과 참조형

# 01. Primitive Type (기본형)

- **정의:** 실제 값을 저장하는 기본 데이터 타입
- **종류:**
    - 논리형 - boolean
    - 문자형 - char
    - 정수형 - byte, short, int, long
    - 실수형 - float, double
- **특징:**
    - 스택(Stack) 메모리에 직접 저장
    - null 값을 가질 수 없음
    - 기본값이 있음 (int는 0, boolean은 false 등)
    - boolean을 제외한 나머지 7개의 기본형은 서로 연산과 변환이 가능

## boolean

- true, false 두 가지만 존재
- 기본값은 false
- 자바에서 데이터를 다루는 최소단위가 byte이기에  1byte
    - 두 가지 값만 존재하므로 표현 자체는 1bit만으로 충분함

## char

- 문자가 아니라 ‘문자의 유니코드(정수)’가 저장
- ex) 문자 ‘A’의 유니코드는 65

```java
char ch1 = 'A'; // 문자 'A'를 char타입의 변수 ch에 저장
char ch2 = 65; // 문자의 코드를 직접 변수 ch에 저장
```

## byte

- 주로 이진 데이터를 다루는데 사용

## short

- c언어와의 호환을 위해 사용

## int

- 정수형 중에서 연산 수행에 효율적인 타입
- JVM의 operand stack이 피연산자를 4byte 단위로 저장
    - 4byte보다 작은 자료형도 4 byte로 변환
    - 4byte인 int를 사용하는 것이 효율적

## long

- int의 범위(20억)을 넘어서는 수를 다룰 때 사용
- 초기화 시, 정수 값 뒤에 알파벳 L을 붙여 long타입임을 명시

```java
long l = 2147483648; // 컴파일 에러 발생
long l = 2147483648L;
```

## float, double

- 실수를 가수와 지수 형식으로 저장하는 부동소수점 방식으로 저장
- 각 정밀도는 7자리, 15자리
    - float는 7자리의 10진수를 오차없이 저장할 수 있다.
    형태: a x 10^n (1 ≤ a < 10)
- 실수형에서는 **표현 가능한 값의 범위 + 정밀도**가 중요 요소
- 자바에서 실수의 기본 타입은 double 형
    - float형에 알파벳 F를 붙여 float타입임을 명시

### 참고- 지수표기법

`숫자 E 지수`

- **숫자**: 소수점이 포함될 수 있는 실수.
- **E**: 10의 지수를 나타내는 기호.
- **지수**: 10을 몇 번 곱하거나 나눌지 나타내는 정수.
- `4.9E3` → 4.9×10^3=4900
- `1.2E-2` → 1.2×10^(−2)=0.012

| 데이터 타입 | 메모리의 크기 | 특징 | 기본값 |
| --- | --- | --- | --- |
| byte | 1byte | -2^7 ~ (2^7-1)(※ 2^7 = 128) | 0 |
| boolean | 1byte | true, false | false |
| char | 2byte | 문자 | \u0000 |
| short | 2byte | -2^15 ~ (2^15-1)(※ 2^15 = 32,768) | 0 |
| int | 4byte | -2^31 ~ (2^31-1)(※ 2^31 = 2,147,438,643) | 0 |
| float | 4byte | ±1.4E-45 ~ ±3.4E38, | 0.0 |
| long | 8byte | -2^63 ~ (2^63-1)(※ 2^63 = 9,223,372,036,854,775,808) | 0.0 |
| double | 8byte | ±4.9E-324 ~ ±1.8E308 | 0.0 |

<aside>
💡

int 타입의 변수는 대략 **10자리** 수(약 20억)의 값을 저장할 수 있다.

</aside>

### 데이터 타입별 크기

| 종류\크기 | 1 byte | 2 byte | 4 byte | 8 byte |
| --- | --- | --- | --- | --- |
| 논리형 | boolean |  |  |  |
| 문자형 |  | char |  |  |
| 정수형 | byte | short | int | long |
| 실수형 |  |  | float | double |


# 02. Reference Type (참조형)

- **정의:** 객체의 주소를 저장하는 데이터 타입
- **종류:**
    - 클래스 타입 (String, Integer 등)
    - 배열 타입
    - 인터페이스 타입
    - 열거 타입 (enum type)
- **특징:**
    - 기본형을 제외한 타입들은 모두 참조형
    - 힙(Heap) 메모리에 객체가 저장되고, 스택에는 그 주소가 저장됨
    - garbage collection의 대상이 됨 - 메모리 해제
    - null 값을 가질 수 있음
    - 기본값은 null
    - 힙 메모리에 생성된 인스턴스는 스택 영역의 일종의 포인터를 통해 핸들링함
    
    ![image](https://github.com/user-attachments/assets/740cb485-e506-483e-8797-a8fc1dda9738)
    

## String Class

- **참조형**에 속하나, **기본형**처럼 사용
- 불변(immutable)하는 객체
- 값을 변경 → 실제로는 새로운 String 클래스 객체 생성
- String 객체 간의 비교는 == 가 아닌 .equals() 메소드 사용
- String literal vs. new String() - 스트링 생성 방식
    - 리터럴(literal) 사용: String constant pool에 생성
    - new 연산자 사용: Heap 메모리에 객체 생성
    
    ```java
    String str1 = new String("Hello"); 
    String str2 = "Hello"; 
    String str3 = "Hello";
    ```
    
    ![image 1](https://github.com/user-attachments/assets/99807bf2-e156-4c11-b2d5-f68c0175192f)

    
    ```java
    System.out.println(str1.equals(str2)); // (1) true 
    System.out.println(str1 == str2); // (2) false 
    System.out.println(str2 == str3); // (3) true
    ```
    

# 03. 주요 차이점

| 구분 | Primitive Type | Reference Type |
| --- | --- | --- |
| 메모리 저장 위치 | 스택(Stack) | 힙(Heap) |
| null 가능 여부 | 불가능 | 가능 |
| 크기 | 타입별로 고정 | 객체에 따라 가변적 |
| 복사 방식 | 값 복사 | 주소 복사 |

```java
// Primitive Type 예시
int number = 10;
double decimal = 3.14;

// Reference Type 예시
String text = "Hello";
Integer wrapper = 100;
int[] array = {1, 2, 3};
```

# 04. 자주 나오는 질문 정리

### Primitive Type과 Reference Type의 가장 큰 차이점은 무엇인가요?
    
1. 저장 위치: Primitive Type은 **스택(Stack)** 메모리에 직접 값을 저장하고, Reference Type은 **힙(Heap)** 메모리에 객체를 저장하고 스택에는 그 주소를 저장합니다.
2. null 허용: Primitive Type은 null을 가질 수 없지만, Reference Type은 null을 가질 수 있습니다.
3. 메모리 사용: Primitive Type은 고정된 크기를 가지지만, Reference Type은 객체의 크기에 따라 가변적입니다.
    
### ==와 equals의 차이점은 무엇인가요?
    
==는 두 객체의 메모리 주소를 비교하는 연산자입니다.
두 객체의 참조가 동일한 경우에만 true를 반환합니다.
    
equals() 메서드는 객체들 간의 내용을 비교하는 메서드입니다. 
두 객체의 값이 동일한 경우에 true를 반환합니다. 
equals() 메서드는 객체의 클래스에 따라 재정의할 수 있습니다.
    
### 불변 객체의 정의와 특징에 대해 설명해주세요.
    
불변 객체란 Java에서 Class의 인스턴스가 생성된 이후에 내부 상태를 변경할 수 없는 객체입니다.
대표적인 불변 객체로 String이 존재하며, 이외에도 프로그래머가 커스텀 객체를 생성해 내부 상태가 변경되지 않게 만들면 그것도 불변 객체가 됩니다.
    
불변 객체의 장점은 다음과 같습니다.
1. Thread-safe하여 병렬 프로그래밍에 유용하며, 동기화를 고려하지 않아도 됩니다.
2. Side Effect(부수 효과)를 피해 오류 가능성을 최소화할 수 있습니다.
    

### 참조

- [String 자료](https://yeoonjae.tistory.com/entry/Java-String-literal-%EA%B3%BC-new-String-%EC%9D%98-%EC%B0%A8%EC%9D%B4)

- [질문 자료](https://sunro1994.tistory.com/191#%3D%3D%EC%99%80%20equals%EC%9D%98%20%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%80%3F-1-9)

- [규글](https://gyoogle.dev/blog/computer-language/Java/Primitive%20type%20&%20Reference%20type.html)
