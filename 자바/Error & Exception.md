# 00. 컴파일 오류와 런타임 오류
에러의 종류로, 컴파일할 때 발생할 수 있는 컴파일 오류와 실행 중에 발생하는 런타임 오류 두 종류 존재

## 컴파일 오류
- 소스코드를 .class 파일을 컴파일하는 과정에서 JVM이 던지는 오류
- 대부분 소스코드의 문법적 오류로 인해 발생
- classNotFoundException,IllegalAccessExeption등

## 런타임 오류
- 문법적 오류는 없어 컴파일 시에는 정상적으로 컴파일 되엇지만, 프로그램을 실행하는 과정에서 발생하는 오류
- 개발자가 직접 오류를 확인하여 처리해야 함
- NullPointerException, IndexOutOfBoundsException등

# 01. 오류와 예외(Error & Exception)
- 자바에서는 오류를 오류(Error)와 예외(Exception)으로 나눔
- 오류는 시스템이 종료되어야 할 수준과 같이 수습할 수 없는 심각한 문제(개발자 예측 불가능)
- 예외는 개발자가 구현한 로직에서 발생한 실수나 사용자의 영향에 의해 발생(예측 가능, 예외 처리 필요)


![image](https://github.com/user-attachments/assets/ddc121c6-5adf-4972-9d87-3d7668f885ba)
- 오류와 예외 모두 Object 클래스를 상속받는 Throwable 클래스를 상속 받음
- Throwable 객체는 오류나 예외에 대한 메시지를 담고, 예외가 연결될 때 연결된 예외의 정보를 기록
- 이를 위해 Throwable 클래스에는 getMessage()와 printStrackTrace()함수가 구현되어 있음
  
# 02. 오류
- 시스템이 종료되어야 할 수준과 같이 수습할 수 없는 심각한 문제(개발자 예측 불가능)
<img width="529" alt="image" src="https://github.com/user-attachments/assets/195b87c8-92f0-4266-b0d5-a1e440cec51f" />

- 대표적인 오류로는
- StackOverflowError: 호출의 깊이가 깊어지거나 재귀가 지속되어 stackoverflow 발생시 던져지는 오류
- OutOfMemmoryError: JVM이 할당한 메모리의 부족으로 더 이상 객체를 할당할 수 없을 때 던져지는 오류(Garabe Collector에 의해 추가적인 메모리가 확보되지 못하는 상황)

# 03. 예외
<img width="523" alt="image" src="https://github.com/user-attachments/assets/1b1f3242-146e-4c4d-9e4e-d5af9896b7ef" />

- 대표적인 예외로는
- NullPointerException : 객체가 필요한 경우에 null을 사용하려고 시도할 경우 던져지는/던져질 수 있는 예외
- IllegalArgumentException : 메서드가 허가되지 않거나 부적절한 argument를 받았을 경우에 던져지는/던져질 수 있는 예외

- 예외는 오류와 다르게 개발자가 임의로 예외를 던질 수 있음
- 위 그림에서, 예외는 Runtime Exception과 그 이외의 Exception으로 나누어져 있음

## Checked Exception과 Unchecked Exception
<img width="685" alt="image" src="https://github.com/user-attachments/assets/0d841381-4cd8-4c77-830a-79b1b696afde" />

- Exception은 Checked Exception과 Unchecked Exception으로 나눌 수 있음
- RuntimeException을 상속받는 클래스를 Unchecked Exception, 상속하지 않는 클래스를 Checked Exception으로 분류 가능
- RuntimeException 또한 Exception의 일종이지만, 명시적으로 예외 처리를 하지 않아도 되기 때문에 특별 취급

<img width="679" alt="image" src="https://github.com/user-attachments/assets/93f6a267-6526-450e-a247-7b82920caacf" />

## 예외 처리 방법
- 예외 복구, 예외 회피, 예외 전환

### 예외 복구
- 예외 상황을 파악하고 문제를 해결하여 정상 상태로 돌려놓는 방법
- 예외를 잡아서 일정 시간이나 조건만큼 대기하고 재시도를 반복
- 최대 재시도 횟수를 넘기게 될 경우에 예외 발생
<img width="521" alt="image" src="https://github.com/user-attachments/assets/4baae128-2e98-4016-b844-5cf1951d60a9" />

### 예외 회피
- 예외 처리를 직접 담당하지 않고 호출한 메서드로 위임해 회피하는 방법
- 예외 처리의 필요성이 있다면 어느 정도는 처리하고 위임하는 것이 좋음
<img width="477" alt="image" src="https://github.com/user-attachments/assets/b466e087-1854-48d6-a56b-6034d4069385" />

### 예외 전환
- 예외 회피와 비슷하게 메서드 밖으로 예외를 위임하지만, 그냥 위엄하지 않고 적절한 예외로 전환해서 넘기는 방법
- 조금 더 명확한 의미로 전달하기 위해 적합한 의미를 가지는 예외로 변경
- 예외 처리를 단순하게 만들기 위해 포장할 수도 있음
<img width="526" alt="image" src="https://github.com/user-attachments/assets/114cf01e-5d78-4317-984a-ce3c02a297e1" />
<img width="412" alt="image" src="https://github.com/user-attachments/assets/53fe91c3-d0c6-4fc5-83af-6775d4ab44f6" />

# 04. 자주 나오는 질문
### 1. Error와 Exception의 차이는 ?
<img width="581" alt="image" src="https://github.com/user-attachments/assets/517a92b1-da09-4b2e-9761-b619ce2f26a2" />

### 2. CheckedException과 UncheckedException의 차이는 ?
<img width="575" alt="image" src="https://github.com/user-attachments/assets/406e3b8a-f76a-4e45-9ba8-e27bffee630d" />

### 3. throw와 throws의 차이는 ?
<img width="439" alt="image" src="https://github.com/user-attachments/assets/8c04cdbb-3bcd-4a11-8f82-a12232795e91" />
