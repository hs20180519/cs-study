# 세마포어 & 뮤텍스

여러 프로세스들이 공유된 자원에 동시에 접근할 때 Critical Section 문제가 발생한다.

이를 해결하기 위해 공유 자원에 대한 접근을 제한해야 하는데, 이러한 대표적인 동기화 도구에는 세마포어와 뮤텍스가 있다.

- Race Condition 경쟁 조건
  두 개 이상의 프로세스 혹은 스레드가 공유 자원을 병행적으로 읽거나 쓸 때, 접근 순서가 결과값에 영향을 줄 수 있는 상태이다.
  둘 이상의 thread가 동시에 공유 자원에 접근 할 때 발생한다.
- Critical Section 임계 구역
  병렬 컴퓨팅에서 두 개 이상의 프로세스 또는 스레드가 동시 접근이 허용되지 않는 공유 자원에 접근하는 코드의 블록이다.
  thread가 공유 자원의 Mutual Exclusion(상호 배제)를 보장 받기 위해 임계 구역에 들어가거나 나올 때를 위한 다양한 동시성 제어 매커니즘이 제공된다.

<aside>
💡Race Condition 문제 발생 → Critical Section에 대한 동시성 제어 → 세마포어 등 동기화 도구 활용

</aside>
<br>

# 01. 세마포어란?

> 정수형 변수와 두 개의 함수로 멀티프로그래밍 환경에서 공유 자원에 대한 접근을 제한하는 방법

- 에츠허르 다익스트라 씨가 고안한 방법
- 사전적 정의 : n. (철도의) 까치발 신호기, 시그널; (군대의) 수기 신호
- 구조 : 정수형 변수 + 두개의 동작(P, V)

![image](https://github.com/user-attachments/assets/c636cf1f-ca64-4d0e-b575-7029a607e40f)
## P, V란 어떤 동작일까

P는 [임계 구역](https://ko.wikipedia.org/wiki/%EC%9E%84%EA%B3%84_%EA%B5%AC%EC%97%AD)에 들어가기 전에 수행되고, V는 임계 구역에서 나올 때 수행된다.

- P: Proveren(try)
- V: Verhogen(increment)

P는 임계 구역에 들어가기 전에 TRY,

V는 임계 구역에서 나오면서 S 변수를 INCREMENT 수행

이때 변수 값을 수정하는 연산은 모두 [원자성](https://ko.wikipedia.org/wiki/%EC%9B%90%EC%9E%90%EC%84%B1)을 만족해야 한다.
다시 말해, 한 프로세스(또는 스레드)에서 세마포어 값을 변경하는 동안 다른 프로세스가 동시에 이 값을 변경해서는 안 된다.

이 때 P, V 연산은 wait, signal 혹은 down, up 으로도 불린다.
<br>

### 구현 방법1 - busy waiting

```jsx
P(S) {
        while S <= 0; // 아무것도 하지 않음
        S--;
}

V(S) {
        S++;
}
```

P 연산에서 임계 구역 들어갈 때 까지 빈 반복문을 수행하므로 CPU 시간을 낭비하게 된다.

이러한 방식을 바쁜 대기(busy waiting) 또는 스핀 락(spin lock)이라고 한다.

바쁜 대기는 자원 사용 측면에서 비효율적이지만, 문맥 전환(context switch)의 오버헤드가 없어 짧은 대기 시간에는 유용할 수 있다.

### 구현 방법2 - waiting list

```jsx
function wait(S):
    if S > 0:
        S--;
        return
    else:
        // 이 프로세스를 waiting list에 추가
        sleep

```

```jsx
function signal(S):
    if there are processes sleeping on S:
        // waiting list에서 프로세스 제거 (깨우기)
        select a process to wake up
        wake up the selected process
        return
    else:
        // 기다리는 프로세스가 없다면
        S++;
        return
```

<br>

## 세마포어의 종류

세마포어의 종류는 이진 세마포어와 카운팅 세마포어로 나눌 수 있다.

- **카운팅 세마포어는 제한되지 않은 범위의 정수 값을 가질 수 있다.** 이를 사용하여 자원 할당과 같은 동기화 문제를 해결할 수 있다.
- **이진 세마포어는 0 또는 1의 두 가지 정수 값만 가질 수 있다.** 구현이 더 간단하고 상호 배제를 제공한다. 이진 세마포어를 사용하여 임계 구역 문제를 해결할 수 있다.

일부는 이진 세마포어를 [뮤텍스](https://www.baeldung.com/cs/what-is-mutex)와 혼동할 수 있다. 이들을 서로 바꿔 사용할 수 있다는 흔한 오해가 있지만 실제로 세마포어는 신호 메커니즘인 반면, 뮤텍스는 잠금 메커니즘이다. **따라서 이진 세마포어가 뮤텍스가 아니라는 점을 알아야 한다.**
<br>

## 세마포어의 활용

### 임계 구역 문제 Critical Section Problem

![image](https://github.com/user-attachments/assets/3b7076f9-c62b-44c6-b4fc-2686af931edd)

세마포어를 활용하여 임계 영역에 대한 접근을 제한한다.

### 순서대로 실행 In Order Execution

![image](https://github.com/user-attachments/assets/f35de2b4-b938-450f-a6c0-92929812734c)

만약 S1 코드를 S2 전에 실행하고 싶을 때, 세마포어를 활용할 수 있다.

![image](https://github.com/user-attachments/assets/a28368f7-281c-4cc2-acf5-bc38beb18566)

- S2 진행 전 `wait()` , Process 1 대기에 들어감
- Process 0의 S1 실행 및 종료 후 `signal()`
- 대기 중이던 Process 1 실행 → S2 진행
  <br>

# 02. 뮤텍스란?

**Mut**ual **Ex**clusion

> 공유 자원에 대한 접근을 하나의 프로세스 혹은 스레드로 제한하는 것을 말한다.

![image](https://github.com/user-attachments/assets/af89ad7e-622e-4b6b-ab55-e7a38cd39276)

뮤텍스의 흔한 구현 방식은 Lock인데, 임계 구역에 들어가기 위해서 락을 얻어야 하고 임계 구역에 나갈 때 락을 반납하는 방식이다.

lock에 해당하는 어떤 객체가 있고, 이 객체를 소유한 스레드/프로세스만이 공유 자원에 접근할 수 있다.

## Lock, Unlock

뮤텍스는 자원을 잠그고 (`lock`) 해제하는 (`unlock`) 방식으로 동작한다.

- **lock()**: 자원이 사용 중인 경우 다른 스레드는 접근할 수 없으며, 자원이 사용 가능해질 때까지 대기한다.
- **unlock()**: 자원 사용이 끝났음을 표시하고, 대기 중인 다른 스레드가 자원에 접근할 수 있게 한다.
  <br>

## 뮤텍스 알고리즘

**상호 배제**(Mutex, 뮤텍스)는 멀티 프로그래밍에서 공유 불가능한 자원의 동시 사용을 피하기 위해 사용되는 알고리즘으로, 임계 구역(critical section)으로 불리는 코드 영역에 의해 구현된다.

### 데커 알고리즘

flag과 turn 두 개의 플래그(각각 임계영역에 들어갈 의향, 두 프로세스 사이의 우선권을 나타낸다)를 사용하여 구현할 수 있다.

이 알고리즘은 두 프로세스가 동시에 임계 영역에 들어가려고 할 때 하나만 들어가도록 한다. 한 프로세스가 이미 임계 영역에 있다면, 다른 프로세스는 전 프로세스가 끝나기를 기다려야 한다.

```cpp
while(true) {
    flag[i] = true; // 프로세스 i가 임계 구역 진입 시도
    while(flag[j]) { // 프로세스 j가 현재 임계 구역에 있는지 확인
        if(turn == j) { // j가 임계 구역 사용 중이면
            flag[i] = false; // 프로세스 i 진입 취소
            while(turn == j); // turn이 j에서 변경될 때까지 대기
            flag[i] = true; // j turn이 끝나면 다시 진입 시도
        }
    }
}
// ------- 임계 구역 ---------

turn = j; // 임계 구역 사용 끝나면 turn을 넘김
flag[i] = false; // flag 값을 false로 바꿔 임계 구역 사용 완료를 알림
```

### 피터슨 알고리즘

데커와 유사하지만, 상대방 프로세스/스레드에게 진입 기회를 양보한다.

```cpp
while(true) {
    flag[i] = true; // 프로세스 i가 임계 구역 진입 시도
    turn = j; // 다른 프로세스에게 진입 기회 양보
    while(flag[j] && turn == j) { // 다른 프로세스가 진입 시도하면 대기
    }
}

// ------- 임계 구역 ---------

flag[i] = false; // flag 값을 false로 바꿔 임계 구역 사용 완료를 알림
```

### 베이커리 알고리즘

여러 프로세스/스레드에 대한 처리가 가능한 알고리즘.

가장 작은 수의 번호표를 가진 프로세스가 먼저 임계 구역에 진입한다.

```cpp
// Shared variables
boolean choosing[n] = {false};   // 모든 프로세스에 대해 선택 중인지 여부
int number[n] = {0};             // 모든 프로세스에 대해 번호표

// Process i (i는 프로세스의 인덱스)
while (true) {
    // 1. 번호표 선택 단계
    choosing[i] = true;           // 번호표 선택 시작
    number[i] = max(number[0], number[1], ..., number[n-1]) + 1;  // 현재 가장 큰 번호 + 1
    choosing[i] = false;          // 번호표 선택 완료

    // 2. 임계 구역 진입 단계
    for (int j = 0; j < n; j++) {
        // 다른 프로세스가 번호를 고르는 중이라면 대기
        while (choosing[j]) { /* busy wait */ }

        // 다른 프로세스가 번호표를 가졌고, 그 프로세스가 먼저 대기해야 한다면 대기
        while (number[j] != 0 && (number[j] < number[i] ||
              (number[j] == number[i] && j < i))) { /* busy wait */ }
    }

    // 3. 임계 구역 (Critical Section)
    // 자원 접근

    // 4. 임계 구역 종료 후
    number[i] = 0;  // 번호표를 내려놓음

    // Non-critical section (임계 구역이 아닌 부분)
    // 나머지 작업 수행
}

```

<br>

# 03. 정리

세마포어와 뮤텍스는 모두 멀티 프로그래밍 환경에서 경쟁 조건을 방지하는 데 유용한 동기화 도구이다.

둘의 차이점은 다음과 같다.

- 세마포어는 여러 프로세스가 공유 자원에 접근할 수 있게 하는 신호 메커니즘인 반면, 뮤텍스는 한 번에 하나의 프로세스만 자원에 접근할 수 있도록 하는 잠금 메커니즘이다.
- 뮤텍스는 기본적으로 1로 설정된 세마포어와 유사하게 동작하지만, **소유권**(ownership)을 가지며, 잠금을 획득한 스레드만이 잠금을 해제할 수 있다. 세마포어는 카운터의 값이 여러 스레드에 의해 증가하거나 감소할 수 있다.

### 한계점

세마포어와 뮤텍스 모두 데드락의 문제를 꼽을 수 있다.

여러 세마포어를 사용할 때, 스레드가 서로 다른 순서로 세마포어를 획득하려고 하면 데드락이 발생할 수 있다. 이는 스레드가 서로의 자원을 기다리면서 무한히 대기하는 상태로 빠지게 된다.

- Deadlock(교착 상태)
  상호 배제 과정에서 자원 접근 권한 획득과 자원 접근 권한 반환 관계의 꼬임으로 발생하는 상황.

### 참고 사이트

[https://www.baeldung.com/cs/semaphore](https://www.baeldung.com/cs/semaphore)

[https://www.baeldung.com/cs/what-is-mutex](https://www.baeldung.com/cs/what-is-mutex)

[https://ko.wikipedia.org/wiki/세마포어](https://ko.wikipedia.org/wiki/%EC%84%B8%EB%A7%88%ED%8F%AC%EC%96%B4)

[https://heeonii.tistory.com/14](https://heeonii.tistory.com/14)

[https://gyoogle.dev/blog/computer-science/operating-system/Semaphore & Mutex.html](https://gyoogle.dev/blog/computer-science/operating-system/Semaphore%20&%20Mutex.html)
