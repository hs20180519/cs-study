# 00. 들어가기에 앞서
### 자바에서의 Garbage Collection
- C/C++ 프로그래밍을할 때 메모리 누수를 막기 위해 객체를 생성한 후, 사용하지 않은 객체의 메모리를 직접 해제 했어야 했다.
- 하지만, 자바에서는 JVM이 구성된 JRE(Java Runtime Environment)가 제공되며, 그 구성 요소 중 하나인 Garbage Collection이 자동으로 사용하지 않는 객체를 파괴한다.

### stop-the-world
- GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것
- 어떤 GC 알고리즘을 사용하더라도 stop-the-world는 발생함
- 대개의 경우, GC 튜닝은 이 stop-the-world 시간을 줄이는 것을 목표로 함

### 효율적인 JAVA Application
- GC를 해도 더 이상 사용 가능한 메모리 영역이 없는데 계 메모리를 할당하려고 하면, OutOfMemoryError가 발생하여 WAS가 다운될 수 있다.
- 행(Hang) 즉, 서버가 요청을 처리하지 못하고 있는 상태가 됨
- 따라서, 규모 있는 JAVA 애플리케이션을 효율적으로 개발하기 위해서는 GC에 대해 잘 알아야 한다.
(Hang : https://waspro.tistory.com/140)

# 01. Garbage Collection
- C/C++ 언어와 달리 자바는 개발자가 명시적으로 객체를 해체할 필요가 없다.
- 사용하지 않는 객체를 메모리에서 삭제하는 작업을 GC라고 부르며, JVM에서 이를 수행한다
- 기본적으로 JVM의 메모리는 class, stack, heap, native method, PC로 총 5가지 영역으로 나뉘는데, GC는 힙 메모리만 다룬다.

### GC의 대상
1. 객체가 NULL인 경우(String str = null)
2. 블럭 실행 종료 후, 블럭 안에서 생성된 객체
3. 부모 객체가 NULL인 경우, 포함하는 자식 객체
- GC는 Weak Generational Hypothesis에 기반

# 02. GC의 메모리 해체 과정
### 1. Marking
![image](https://github.com/user-attachments/assets/10af13d7-ec63-43e0-a303-7e9175fce759)
- 프로세스는 마킹을 호출
- 이는, GC가 메모리를 사용되는지 아닌지를 찾아낸다
- 참조되는 객체는 파란색으로, 참조되지 않은 객체는 주황색으로 보여진다
- 모든 오브젝트는 마킹 단계에서결정을 위해 스캔되고, 이는 많은 시간을 소모한다

### 2. Normal Deletion
![image](https://github.com/user-attachments/assets/0786f8e4-e35b-4910-a558-8e36684f7d52)
- 참조되지 않은 객체를 제거하고, 메모리를 반환
- 메모리 Allocator은 반환되어 비어진 블럭의 참조 위치를 저장해두었다가, 새로운 객체가 선언되면 할당되도록 한다.

### 3. Compacting
![image](https://github.com/user-attachments/assets/f4f3651e-5061-44fa-9440-8feb885b7e62)
- 성능을 위해, 참조되지 않은 객체를 제거하고, 또한 남은 참조되어지는 객체를 묶는다
- 이들을 묶음으로서 공간이 생기므로 새로운 메모리 할당시에 더 빠르게 진행이 가능하다

# 03. Generational Garbage Collection 배경
위와 같이 모든 객체를 Marck & Compact하는 JVM은 비효율적이다.
![image](https://github.com/user-attachments/assets/ee576689-f7c2-472f-9338-36f0011c0def)
- X축은 바이트가 할당될 때의 시간, Y축은 할당된 바이트의 수
- 즉, 시간이 갈 수록 적은 객체만이 남는다
- 이에 기반한 것이 Weak Generational Hypothesis이다

# 04. Weak Generational Hypothesis
- 신규로 생성된 객체의 대부분은 금방 사용하지 않는 상태가 되고,
- 오래된 객체에서 신규 객체로의 참조는 매우 적게 존재한다는 가설이다
- 이를 기반으로 자바는 Young 영역과 Old 영역으로 메모리를 기반으로 분할하고,
- Young 영역에는 신규로 생성되는 객체를, Old 영역에는 오랫동안 살아남은 객체를 보관한다

# 05. Generational Garbage Collection
![image](https://github.com/user-attachments/assets/84b5b8e4-a3cd-434e-9aaa-bc639b307d63)
### 1. Young 영역
- 새롭게 생성된 객체들이 대부분 위치
- Eden(대부분의 객체가 처음 생성되는 공간), Survivor Space(Eden에서 살아남은 객체가 이동되는 공간, S0, S1 두 개의 영역으로 나뉨)
- 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 많은 객체가 Young영역에 생성되었다가 금방 사라진다.
- 이 영역에서 객체가  사라질때 Minor GC가 발생한다고 말한다

### 2. Old 영역
- 접근 불가능한 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다
- 대부분 Young 영역보다 크게 할당하여, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다
- 이 영역에서 객체가 사랒리 때 Major GC(혹은 Full GC)가 발생한다고 한다

### 3. Permanent 영역(=Method Area)
- JVM이 클래스들과 메소드들을 설명하기 위해 필요한 메타 데이터들을 포함
- JDK8부터는 Metaspace로 교체되었다

# 06. 자주 나오는 질문
### 1. GC는 무엇이며, 왜 필요한가?
![image](https://github.com/user-attachments/assets/52f3177f-4099-4539-9d0e-bdc581c307fe)

### 2. Java에서 메모리 누수는 어떻게 발생할 수 있나?
![image](https://github.com/user-attachments/assets/a51ed1e8-dd11-45a5-abec-d503b66d6a7c)

### 3. GC의 장점은?
![image](https://github.com/user-attachments/assets/007dfbb5-8486-4039-a743-79d2b433e2c0)
