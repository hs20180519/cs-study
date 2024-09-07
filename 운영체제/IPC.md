# 01. IPC 가 무엇인가?

IPC = Inter Process Communication(프로세스 간 통신)

프로세스들 사이에 서로 데이터를 주고 받는 행위 또는 그에 대한 방법이나 경로

</br>

# 02. IPC가 필요한 이유

프로세스는 독립적으로 실행되기 때문에, 프로세스 간에 정보를 주고 받을 때는 IPC 통신을 활용한다.

![image](https://github.com/user-attachments/assets/6a92ac28-70de-4532-85af-9cffbe9635d0)

</br>

# 03. IPC 종류 - Shared Memory vs. Message Passing

IPC에는 전통적으로 두 가지 모델이 있다.

→ (a) Shared Memory,  (b) Message Queue

![image](https://github.com/user-attachments/assets/7fd24abf-1dfe-4b90-ada7-f0c01bc25be1)

## Shared Memory 공유 메모리 모델

- 다중 프로세스들이 가상 메모리를 공유하게 하는 기법
- 공유 메모리 영역을 구축할 때만 시스템 콜 필요, 구축 이후에는 커널의 도움이 필요 없음 → 고속 통신
- 프로세스 간 동기화 문제가 발생하여, 충돌 가능성이 있음

## Message Passing 메세지 전달 모델

- 필요한 공유 자원을 그때그때 프로세스 사이에서 전달하는 방식
- 충돌하지 않으나, 적은 양의 데이터 교환 위주
- OS가 프로세스 간 통신 방법 제공하며 전달, 시스템 콜 사용으로 성능이 떨어짐
- 메세지 패싱에 기반을 둔 PIPE, Message queue, Socket, Semaphore, Signal, RPC 방식 존재

</br>

# 04. PIPE

⇒ 여러 개의 프로세스가 공통으로 사용하는 **임시 공간**

⇒ 임시 공간이란 실제로 파일 시스템에 생성되는 임시 파일

⇒ 하나의 프로세스가 파이프에 쓰게 되면, 다른 프로세스는 그 파이프에서 읽는 방식!

⇒ 현 프로세스의  output을 다른 프로세스의 input으로 넣음

⇒ 단방향 통신 (Half-duplex)

⇒ 데이터를 한 번만 읽을 수 있음

`$ cat test.txt | grep “a”` 

1. PIPE(익명 PIPE, Anonymous PIPE)
    - 양방향 통신을 원하는 경우 두 개의 파이프 필요
    - pipe() 함수를 통해 읽기 전용 파이프, 쓰기 전용 파이프를 만들고 한 쪽을 닫아 사용
    
    ![image](https://github.com/user-attachments/assets/99d7f61d-9c17-434a-b452-38184d5cbf6e)
    
    - 파이프는 fork() 함수에 의해 복사되지 않음
        - 자식 프로세스는 부모가 사용하던 변수를 복사함
        - 파이프는 복사 되지 않고 **File Descriptor 를 공유하게 되어** 부모 자식이 같은 파이프를 가리킴
    - 오로지 **부모 자식** 프로세스 간에만 가능
        - 파이프는 운영체제에서 임시로 생성되는 파일이고, 접근 가능한 방법은 File Descriptor를 공유하는 방법만 존재
        - 다른 프로세스는 파이프를 사용하여 통신 불가
        
    
    예시) 자식 프로세스에서 부모 프로세스로 보낼
    
    - 부모는 자식이 준 것을 읽어야 하므로 읽기용 파이프 fd[0] 이용, 쓰기용 파이프 fd[1]는 닫음
    - 자식은 부모에게 써서 줘야하므로 쓰기용 파이프 fd[1] 이용, 읽기용 파이프 fd[0]는 닫음
    
    ![image](https://github.com/user-attachments/assets/d5b5dad9-b8e5-49af-abb5-418a598f3f21)
    
    예시 코드) 
    
    ![image](https://github.com/user-attachments/assets/5b374f24-6206-4815-8912-8461f131597b)
    
    ⇒ 무슨 용도의 코드일지 생각해보기!
    
2. Named PIPE(FIFO)
    - 익명 PIPE의 확장
    - 리눅스에서 Named PIPE 기법을 위해 FIFO라는 특수 파일을 제공
    - 부모 자식간이 아닌 다른 모든 프로세스들 사이에서 통신 가능
        - 지정한 이름으로 파일 디스크립터를 열 수 있음
    
    ![image](https://github.com/user-attachments/assets/c3fb7b1e-23bb-45b9-8d4b-5ea083ddb703)
    

</br>

# 05. 그 외 기법

1. Message Queue(메세지 큐)
    
    ![image](https://github.com/user-attachments/assets/2d9511d2-c1e2-4ab3-a9be-252725fc00cb)
    
    - 입출력 방식은 Named PIPE 와 동일하나, 통신 단위가 메모리 공간이다.
    - 메세지 큐에 쓸 데이터에 번호를 붙임으로써 여러 개의 프로세스가 동시에 데이터를 쉽게 다룰 수 있음
    - 큐는 버퍼의 역할을 하며 커널 레벨에 저장되어 있음
2. Shared Memory(공유 메모리)
    - 통신 설비가 아니라, 데이터 자체를 아예 공유하도록 지원하는 설비
    - 프로세스 간 메모리 영역을 공유해서 사용할 수 있도록 허용
    - 프로세스가 공유 메모리 할당을 커널에 요청하면, 커널은 해당 프로세스에 메모리 공간을 할당해주고 이후 모든 프로세스가 해당 메모리 영역에 접근 가능
    - 중개자 없이 곧바로 메모리에 접근할 수 있기 때문에 IPC 중에서 가장 빠르게 작동
3. Memory Map
    
    ![image](https://github.com/user-attachments/assets/857cd424-364e-4ac2-94e1-ae80ecc17677)
    
    - Shared Memory 와 유사하나, 메모리 맵은 열린 파일은 메모리에 매핑시켜서 공유하는 방식
    - 주로 파일로 대용량 데이터를 공유해야 할 때 사용
4. Socket
    - 네트워크 소켓 통신을 이용하여 데이터를 통신함 - pipe 개념을 네트워크로 확장
    - 다른 IPC와 달리 원격 프로세스와의 통신도 가능하여 위치에 독립적임
        
        ![image](https://github.com/user-attachments/assets/3719e60c-cd51-4696-8475-8f2ed4863d5e)
        
5. Semaphore
    - 타 IPC 설비들이 대부분 프로세스 간 메세지 전송을 목적으로 하는 데 반해, 세마포어는 프로세스 간 데이터를 동기화 및 보호에 목적을 둠

</br>

# 06. 정리

![image](https://github.com/user-attachments/assets/376f46dc-bc70-40af-ba45-7a6526887470)

</br>

# 07. 자주 나오는 질문 정리

❓ IPC 란 무엇인가요?

✅ IPC는 프로세스들 사이에 서로 데이터를 주고 받는 방식, 즉 프로세스 간의 통신을 의미합니다. 각 프로세스는 독립적인 실행 객체이므로 프로세스 간 통신을 하려면 커널이 제공하는 IPC 모델 방식을 사용해서 통신을 해야 합니다.

❓ IPC 모델에 메세지 전달 방식과 공유 메모리 방식이 있는데 두 방식은 각각 언제 쓰이나요?

✅ 공유 메모리 같은 경우에는 프로세스 간에 공유가 되도록 설정해놓은 메모리이며, 모든 프로세스들이 접근이 가능합니다. 
처음 생성할 때만 시스템콜을 사용하기 때문에 속도가 빠릅니다.
그래서 많은 양의 데이터를 전달하는 경우에 많이 사용합니다. 다만, 동기화 문제를 생각해야 합니다.

메세지 전달 방식은 공유 자원을 그때그때 프로세스 사이에서 전달하는 방식입니다. 동기화를 고려하지 않아도 되어 구현하기가 편하다는 장점이 있습니다. 다만 공유 메모리에 비해 속도가 느려서 적은 양의 데이터를 전달할 때 쓰입니다.

</br>

### 참고 사이트

[https://velog.io/@coastby/운영체제-IPC-Inter-Process-Communication-프로세스-간-통신](https://velog.io/@coastby/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-IPC-Inter-Process-Communication-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EA%B0%84-%ED%86%B5%EC%8B%A0)

[https://gyoogle.dev/blog/computer-science/operating-system/IPC.html](https://gyoogle.dev/blog/computer-science/operating-system/IPC.html)

[https://jwprogramming.tistory.com/54](https://jwprogramming.tistory.com/54)

[http://www.ktword.co.kr/test/view/view.php?no=302](http://www.ktword.co.kr/test/view/view.php?no=302)

[https://bluemoon-1st.tistory.com/22](https://bluemoon-1st.tistory.com/22)

[https://asidefine.tistory.com/94](https://asidefine.tistory.com/94)
