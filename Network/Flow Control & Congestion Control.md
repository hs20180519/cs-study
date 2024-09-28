# 01. 흐름 제어

송신 측이 수신 측을 과도한 데이터로 부담시키지 않도록 보장하는 기술이다.

### 수신 버퍼와 버퍼 오버플로(buffer overflow)

- 수신 버퍼: 수신된 세그먼트가 처리되기 전에 임시로 저장되는 공간으로, 일정한 길이로 할당되어 있음.
- 버퍼 오버플로: 송신 측이 흐름 제어를 고려하지 않고 수신 버퍼의 크기보다 많은 데이터를 전송하여 버퍼가 넘치는 상황

→ 흐름 제어: 이러한 문제 상황을 방지하고자 송신 측이 수신 측의 처리 속도를 고려하며 송수신 속도를 균일하게 유지하는 것

## Stop-and-Wait

- 흐름 제어의 가장 단순한 형태
- 확인 응답이 오기 전까지 추가적인 세그먼트를 전송하지 않는 방식

1. 송신 측이 세그먼트 전송한다.
2. 수신 측이 세그먼트를 받으면, 수신한 세그먼트에 대한 확인 응답(ACK)을 보내서 추가적인 세그먼트를 수신할 준비가 되었음을 알린다.
3. 송신 측은 다음 세그먼트를 보내기 전에 확인 응답을 받을 때까지 기다린다.

- 세그먼트를 하나씩만 보낼 수 있음 → 비효율적

## 슬라이딩 윈도우(Sliding Window)

- 확인 응답을 받지 않고 윈도우의 크기만큼 한번에 전송하는 기법이다.
- 윈도우: 송신 호스트가 파이프라이닝할 수 있는 최대량
- 윈도우의 크기와 한 번에 전송할 수 있는 데이터의 양은 비례

### 동작과정

- full-duplex(전이중)으로 연결되어 있는 노드 A(송신 측), B(수신 측)가 있다고 가정
- B는 세그먼트 W개를 받을 수 있는 버퍼를 가지고 있음
- 따라서, A는 B에게 ACK를 기다리지 않고 W개의 세그먼트를 보낼 수 있음
    - B는 초기에 A에게 윈도우 크기(W)를 통보함
- 어떤 세그먼트들이 ACK를 받았는지 추적하기 위해, 각 세그먼트는 일련번호가 매겨져 있음
- B는 다음으로 기대되는 세그먼트의 일련번호가 포함된 ACK를 보냄으로써 세그먼트를 받았음을 알리는데, 이는 여러 프래임을 한 번에 확인하는 데에도 사용할 수 있음
    
    **예시:** B가 4번 세그먼트가 도착할 때까지 1~4번 세그먼트를 ACK 없이 받았다면, B는 일련번호 5를 포함한 ACK를 보내면서 1~4번 세그먼트를 받았음을 확인한다.
    

### 예시

1~4번째 세그먼트를 전송한 상황
<img width="683" alt="image" src="https://github.com/user-attachments/assets/d5585d43-9fe0-4f21-b11c-140aee863f90">


B로부터 1번째 세그먼트에 대한 ACK를 받았다면, 윈도우가 한 칸 움직인다.

<img width="670" alt="image (1)" src="https://github.com/user-attachments/assets/c4970c2b-4019-4a85-ad9c-8df4194e5957">

- A는 송신 윈도우를, B는 수신 윈도우를 관리

<img width="713" alt="image (2)" src="https://github.com/user-attachments/assets/3fa1b7a6-d428-4915-9286-2a7fbd6fd5cc">

# 02. 혼잡 제어

- 네트워크 내 패킷 수를 네트워크의 성능이 급격히 저하되는 수준 이하로 유지하기 위함

## 혼잡이란?

- 많은 트래픽으로 인해 패킷의 처리 속도가 늦어지거나 유실될 우려가 있는 네트워크 상황
- 한 라우터에 데이터가 몰려서 모든 데이터를 처리할 수 없는 상황이 되면, 송신지는 데이터 손실이 일어났다고 간주하고 계속 재전송하여 네트워크를 더욱 혼잡하게 만든다.

→ 혼잡 제어: 송신 측의 전송 속도(네트워크 내의 패킷 수)를 적절히 조절하는 것

## 혼잡 윈도우

- 혼잡 없이 전송할 수 있을 법한 데이터양
- ACK가 오기 전까지 혼잡 윈도우만큼만 전송할 수 있다.
- 혼잡 윈도우가 크면 한 번에 전송할 수 있는 세그먼트 수가 많음을 의미
- 혼잡 윈도우가 작으면 네트워크가 혼잡한 상황이므로 한 번에 전송할 수 있는 세그먼트 수가 적음을 의미
- 혼잡 윈도우의 적당한 크기는 혼잡 제어 알고리즘을 통해 결정할 수 있다.

## 혼잡 제어 알고리즘

### AIMD(Additive Increase/Multiplicative Decrease)

- 합으로 증가, 곱으로 감소
- 혼잡이 감지되지 않는다면 혼잡 윈도우를 RTT마다 1씩 선형적으로 증가시키고,
    - RTT(Round-trip time):  데이터 패킷이 송신지부터 수신지까지 왕복하는 데 걸리는 시간
- 혼잡이 감지되면 혼잡 윈도우를 절반으로 떨어트리는 동작을 반복
- 선형적으로 혼잡 윈도우를 증가시키므로 초기 전송 속가 확보되지 않음

### 느린 시작(Slow start) 알고리즘

- 혼잡 윈도우를 1부터 시작하여, 문제 없이 수신된 ACK 세그먼트 하나 당 1씩 증가시키는 방법
- 네트워크가 이미 혼잡하지 않은지 확인하기 위해, 송신자가 천천히 시작해서 네트워크 상태를 파악하는 역할을 한다.
- 송신 윈도우를 기하급수적으로 확장하여 혼잡하지 않을 때는 효율적으로 데이터 전송량을 증가시킴

<img width="523" alt="image (3)" src="https://github.com/user-attachments/assets/e6e0fbf8-a219-4f0b-8e84-94c5a745a226">

**혼잡 윈도우는 언제까지 증가할까?**

- 느린 시작 알고리즘을 사용할 때 느린 시작 임계치(slow start threshold) 값을 함께 사용한다.
- 다음 세 가지 상황을 통해 혼잡이 감지되면 각 방법을 수행

| 상황 | 방법 |
| --- | --- |
| 타임아웃 발생 | 혼잡 윈도우 값을 1로 초기화, 느린 시작 임계치를 혼잡이 감지된 시점의 혼잡 윈도우 값의 절반으로 초기화, 느린 시작 재개 |
| 혼잡 윈도우 ≥ 느린 시작 임계치 | 느린 시작 종료, 혼잡 윈도우를 절반으로 줄인 뒤 혼잡 회피 수행 |
| 세 번의 중복 ACK 발생 | 빠른 재전송 후 빠른 회복 수행 |

**타임아웃(Timeout)**

- TCP 세그먼트를 송신하는 호스트는 모두 재전송 타이머라는 값을 유지한다.
- **타임아웃**: 세그먼트를 전송할 때마다 재전송 타이머를 시작하는데, 타이머가 끝난 상황
- 타임아웃이 될 때까지 ACK를 받지 못하면 세그먼트가 정상적으로 도착하지 않은 것으로 간주하여 세그먼트를 재전송

**빠른 재전송(Fast Retransmit)**

- 중복 ACK를 3개 이상 받은 경우, 송신 측에서 설정한 타임아웃 시간이 지나지 않았어도 바로 해당 패킷을 재전송하는 방법
- 빠른 재전송률을 유지할 수 있음

<img width="526" alt="image (4)" src="https://github.com/user-attachments/assets/81c7b17a-3eda-42fc-baaf-0a4a56bc7cb4">

### 혼잡 회피(Congestion avoidance) 알고리즘

- RTT마다 혼잡 윈도우를 1MSS(Maximum Segment Size)씩 증가시키는 방법
- 느린 시작 임계치를 넘어선 시점부터는 혼잡이 발생할 우려가 있기 때문에 선형적으로 증가시킴
- 타임아웃이 발생할 경우 혼잡 윈도우 크기를 1로 초기화, 느린 시작 임계치는 혼잡이 감지된 시점의 혼잡 윈도우 값의 절반으로 초기화
- 세 번의 중복 ACK가 발생하면 혼잡 윈도우 값과 느린 시작 임계치를 대략 절반으로 초기화한 후 빠른 회복 수행

### 빠른 회복 알고리즘

- 세 번의 중복 ACK 세그먼트를 수신한 경우 느린 시작은 건너뛰고 혼잡 회피를 수행하는 방법
- 타임아웃이 발생하면 혼잡 윈도우 크기를 1로, 느린 시작 임계치를 혼잡이 감지된 시점의 절반으로 초기화한 후 다시 느린 시작 수행

<img width="557" alt="image (5)" src="https://github.com/user-attachments/assets/0898220d-2e3e-4701-b8d4-91923f71d2f5">

![image (6)](https://github.com/user-attachments/assets/f4572ef7-0f58-4ba0-b376-763f5b1ec9e7)

# 03. 예상질문

## TCP/IP 통신에서 흐름 제어 기법이란?

송신 측과 수신 측 간의 데이터 처리 속도 차이를 해결하기 위해, 데이터를 보내는 속도와 받는 속도를 조절하는 것을 말합니다.

## TCP/IP 흐름 제어 기법에는 어떤 것이 있는지?

Stop and Wait과 Sliding Window 기법이 있습니다.<br>

Stop and Wait은 전송한 하나의 패킷에 대한 확인 응답을 받으면 다음 패킷을 전송하는 기법이고, Sliding Window는 수신 측에서 설정한 윈도우 크기만큼 확인 응답 없이 패킷을 전송하는 기법입니다. Sliding window 기법은 패킷을 보다 효율적으로 전송할 수 있다는 장점이 있습니다.

## TCP/IP 혼잡 제어 기법은 왜 사용되는가?

수신 측이 받는 데이터의 양이 처리할 수 있는 양을 넘어서면 라우터는 초과된 데이터를 처리하지 못합니다. 송신 측은 처리되지 못한 데이터에 대해서 손실된 것으로 간주하고 계속 재전송하여 네트워크에 더욱 혼잡을 초래합니다.

이러한 혼잡을 예방하기 위해 송신 측의 데이터 전송 속도를 조절하는 혼잡 제어 기법이 사용됩니다.

# 04. 참고
강민철, 혼자 공부하는 네트워크, 한빛미디어, 2024<br>
[[네트워크] TCP/IP 흐름제어 & 혼잡제어](https://nayoungs.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPIP-%ED%9D%90%EB%A6%84%EC%A0%9C%EC%96%B4-%ED%98%BC%EC%9E%A1%EC%A0%9C%EC%96%B4)