# 01. Call by Value
- 값을 복사하여 처리 (자바의 경우, 주소값을 복사)
- 변수의 복사본이 전달되며, 원래 값이 수정되지 않는다
- 실제 인수는 다른 메모리 위치에 생성된다.

## 자바의 Call by Value 동작 방식
- 자바의 데이터 타입은 원시 타입과, 참조 타입이 있다.
- 원시 타입(primitive type) - Numeric Type(byte, short, int, float, long, double, char), Boolean Type(boolean)
- 참조 타입(reference type) - Class Type, Interface Type, Array Type, Enum Type, 기타 참조 타입(String 등)
- 따라서, 메소드 파라미터로 원시 타입을 전달하는 것과, 참조 타입을 전달하는 것에는 동작 방식에 차이가 있다

### 원시 타입(primitive type) 전달 방식
![image](https://github.com/user-attachments/assets/2a83b4d2-49d8-4a6e-9291-e1d98da9a7d7)
- 위 코드는 int 타입(원시 타입) 변수 v를 add 함수를 통해 1을 더해주고 있다.
- 하지만 결과 값은 여전히 10이다.
- primitiveTest()의 v 변수와 add()의 num은 연관 없는 변수이기 때문이다.

<img width="625" alt="image" src="https://github.com/user-attachments/assets/b3eeb640-5ff7-45e9-8052-f5110ff15785" />

- 자바에서 변수를 선언하면 기본적으로 Stack 메모리 영역에서 할당된다.
- Stack 영역 내부에 primitiveTest()와 add()의 영역이 각각 나뉘어 있고, 서로 다른 변수가 존재한다.
- 따라서 num 값에 1을 더해도 v 변수에는 영향을 끼치지 않는다.
- 이처럼 원시 타입의 전달은 값을 복사해서 전달하는 Call by Value 방식으로 동작함을 알 수 있다.

### 참조 타입(reference type) 전달 방식
<img width="370" alt="image" src="https://github.com/user-attachments/assets/99d0ae33-c880-43b9-bae3-f6e8d037f6ee" />

- 변수의 참조(주소) 값을 복사해서 전달한다.
- referenceTest()의 영역과 add() 영역의 변수들은 모두 동일한 객체의 주소(0x001)를 바라보고 있다.
<img width="487" alt="image" src="https://github.com/user-attachments/assets/5c552282-b2a5-4fe8-a677-62b973a01bdf" />

- add()의 변수 또한 같은 주소 값을 참조하기 때문에 값이 변경되었다.

+) 참고
- 결국 주소 값을 넘기고 원본 값이 변경되니까 Call by Reference 아닌가 싶지만
- 자바의 참조와 Call by Refereced의 참조는 약간 다른 의미를 가진다.
- 자바에서 참조의 의미는 "객체가 힙에 저장된 위치를 가리키는 메모리 주소"이다.
- 실제 객체에 대한 참조가 아니라 객체에 접근하고 조작하는 방법이라고 볼 수 있다.
- Call by Reference는 참조 자체를 넘기기 때문에 새로운 객체를 할당하면 원본 변수도 영향을 받는다.

<img width="417" alt="image" src="https://github.com/user-attachments/assets/2d5296c6-c037-4261-bada-687d4729a211" />

- 다음의 코드에서 전달 받은 값을 새로운 객체로 변경해도 원본 변수는 변하지 않는다.
- 따라서 참조 타입 역시 Call by Value 방식으로 동작함을 보여준다.
- newArr()가 종료되고, 사용되지 않는 객체(0x002)는 자바의 가비지 콜렉터에 의해 수거될 것이다.

<img width="636" alt="image" src="https://github.com/user-attachments/assets/dc89f44b-6966-4a01-b152-6d8cc91fdb7c" />

- 결국 자바는 항상 Call by Value 방식으로 데이터를 전달하고,
- 핵심은 호출자 변수와 수신자 파라미터는 Stack 영역 내에서 각각 독립적으로 존재하는 다른 변수라는 것

# 02. Call by Reference
- 값의 주소를 참조하여 직접 값에 영향을 준다.
- 변수 자체가 전달되며, 원래 값이 수정된다.
- 실제 인수는 같은 메모리 위치에 생성된다.

# 03. 자주 나오는 질문
### 1. c++에서 call by value vs reference
![image](https://github.com/user-attachments/assets/1d51534a-6213-41f2-9754-14e5089a3e07)

답 : a는 modifyValue 함수로 전달되며, call by value 방식이므로, 함수 안에서 x가 변경되어도 원래 변수 a는 변하지 않는다.


b는 modifyReference 함수로 전달되며 call by reference 방식이므로, 함수에서 x를 변경하면 원래 변수 b도 변한다.


출력 결과 : a : 5 b : 10

### 2. 자바에서 객체 전달
<img width="527" alt="image" src="https://github.com/user-attachments/assets/44177fae-5e95-4e98-97c1-472dca2c4bbb" />

1. modifyValue 함수는 객체의 멤버 변수 value를 수정한다. 자바는 객체의 참조롤 값으로 전달하기 때문에 함수 내부에서 객체 멤버를 수정하면 원래 객체에도 영향을 준다. (배열과 동일)
2. reassignReference 함수는 obj를 새로운 객체로 재할당하지만, 원래 호출자의 참조에는 영향을 주지 않는다. 이는 자바가 참조를 call by value로 전달하기 때문이다.

### 3. call by value와 call by reference의 차이점
<img width="565" alt="image" src="https://github.com/user-attachments/assets/d6c3ea68-7e61-4d42-972b-812b9b50594c" />

### 4. 자바는 call by value인가 call by reference인가?
자바는 call by value만 지원되지만, 객체의 참조가 값으로 전달되므로 객체 상태는 변경될 수 있다.

### 참고
- https://gyoogle.dev/blog/computer-language/Java/Call%20by%20value%20&%20Call%20by%20reference.html
- https://dev-coco.tistory.com/189
