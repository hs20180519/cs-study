## 00. TCP 연결 수립과 종료
TCP는 통신 이전에는 연결을 수립하고, 통신 이후에는 연결을 종료한다.

</br>

## 01. 연결 수립 : 3 way handshake
세 개의 단계로 이루어진 TCP의 연결 수립 과정

<img width="438" alt="image" src="https://github.com/user-attachments/assets/e3e24c78-8689-4bd8-8dab-aa7fad133e6c">

### 1. 연결 시작합니다.  
host a -> b : SYN 세그먼트 (호스트 a의 초기 순서 번호, 1로 설정된 SYN 비트)
### 2. 네 확인했습니다. 연결 시작해요!
host b -> a : SYN + ACK 세그먼트 (호스트 b의 초기 순서 번호, 호스트 a가 전송한 세그먼트에 대한 확인 응답 번호, 1로 설정된 SYN 비트, 1로 설정된 ACK 비트)
### 3. 네 확인했습니다.
host a -> b : ACK 세그먼트 (호스트 a의 다음 순서 번호, 호스트 b가 전송한 세그먼트에 대한 확인 응답 번호, 1로 설정된 ACK 비트)


1. 액티브 오픈 : 처음 연결을 시작하는 호스트의 연결 수립 과정이다. 주로 서버 - 클라이언트 관계에서 클라이언트에 의해 수행된다. (a)
2. 패시브 오픈 : 연결 요청을 받고 나서 요청에 따라 연결을 수립해주는 호스트의 연결 수립 과정이다. 서버에 의해 수행된다. (b)

- SYN-SENT : 액티브 오픈 호스트가 SYN 세그먼트를 보낸 후 그에 대한 응답인 SYN+ACK 세그먼트를 기다리는 상태(연결 요청을 보낸 뒤 대기하는 상태)
- SYN-RECEIVED : 패시브 오픈 호스트가 SYN+ACK 세그먼트를 보낸 뒤 그에 대한 ACK 세그먼트를 기다리는 상태
- ESTABLISHED : 연결이 확립되었음을 나타내는 상태. 두 호스트가 마지막 ACK 세그먼트를 주고 받으면 ESTABLISHED 상태가 된다.




</br>

## 02. 연결 종료 : 4 way handshake
네 단계로 이루어진 TCP의 연결 종료 과정   
송수신 호스트가 각자 한 번씩 FIN과 ACK를 주고 받으며 이루어진다.

<img width="477" alt="image" src="https://github.com/user-attachments/assets/b659d7dd-0967-4e58-b6ea-20c43c826e31">

### 1. 연결 끊을게요.
host a -> b : FIN 세그먼트 (1로 설정된 FIN 비트)
### 2. 네 확인했습니다.
host b -> a : ACK 세그먼트 (호스트 a가 전송한 세그먼트에 대한 확인 응답 번호, 1로 설정된 ACK 비트)
### 3. 이제 연결 끊어요.
host b -> a : FIN 세그먼트 (1로 설정된 FIN 비트)
### 4. 네 확인했습니다.
host a -> b L ACK 세그먼트 (호스트 b가 전송한 세그먼트에 대한 확인 응답 번호, 1로 설정된 ACK 비트)

1. 액티브 클로즈 : 먼저 연결을 종료하려는 호스트에 의해 수행된다. (a)
2. 패시브 클로즈 : 연결 종료 요청을 받아들이는 호스트에 의해 수행된다. (b)


- FIN-WAIT-1 : 일반적인 TCP 연결 종료 과정에 있어 연결 종료의 첫 단계이다. FIN 세그먼트로서 연결 종료 요청을 보낸 액티브 클로즈 호스트는 FIN-WAIT-1상태로 접어들게 된다.
- CLOSE-WAIT : 종료 요청인 FIN 세그먼트를 받은 패시브 클로즈 호스트가 그에 대한 응답으로 AKC 세그먼트를 보낸 후 대기하는 상태이다.
- FIN-WAIT-2 : FIN-WAIT-1 상태에서 ACK 세그먼트를 받게 되면 FIN-WAIT-2 상태가 된다. 상대 호스트의 FIN 세그먼트를 기다리는 상태이다.
- LAST-ACK : CLOSE-WAIT 상태에서 FIN 세그먼트를 전송한 뒤 이에 대한 ACK 세그먼트를 기다리는 상태이다.
- TIME-WAIT : 액티브 클로즈 호스트가 FIN 세그먼트를 수신한 뒤, 이에 대한 ACK 세그먼트를 전송한 뒤 접어드는 상태이다. 패시브 클로즈 호스트가 마지막 ACK 세그먼트를 수신하면
- CLOSE상태로 전이하는 반면, TIME-WAIT 상태에 접어든 액티브 클로즈 호스트는 일정 시간을 기다린 뒤 CLOSED 상태로 전이한다.
- (마지막 ACK 세그먼트가 올바르게 전송되지 않았을 경우를 대비하여 기다린다)

## 03. 참고
1. ACK : 세그먼트의 승인을 나타내기 위한 비트
2. SYN : 연결을 수립하기 위한 비트
3. FIN : 연결을 종료하기 위한 비트

## 04. 문제 풀이
<img width="667" alt="image" src="https://github.com/user-attachments/assets/3cace832-4306-4b50-8702-a1c4cca2cefd">

함께 풀어봐용

<img width="562" alt="image" src="https://github.com/user-attachments/assets/512e4109-66d5-43b0-a10a-c21fbf90d7f3">

SYN Flooding : DDos(Distributed Denial of Service)의 일종으로, TCP 3-way handshake 과정에서 발생하는 취약점을 악용한 공격 방식이다. 주로 서버가 많은 SYN 요청을 처리할 수 없게 만들어 정상적인 사용자에게 서비스를 제공하지 못하도록 하는 공격이다.   

### SYN Flooding 공격 방식
1. 공격자가 다수의 SYN 패킷을 전송 (가짜 IP주소)
2. 서버가 SYN 요청에 응답
3. ACK 응답이 오지 않음. 자원을 과도하게 할당하여 새로운 연결 처리가 어려워짐

### SYN Flooding 해결 방식
1. SYN 쿠키 사용 : 서버가 SYN-ACK 패킷을 보낼 때 클라이언트와의 연결 정보를 유지하지 않고, 쿠키 형태로 인코딩된 정보를 전송하는 방법
2. SYN 요청 수 제한
3. 방화벽 및 네트워크 장비 설정
4. SYN-ACK 재전송 횟수 줄이기
5. IP 블랙리스트 사용 : 공격 시도 IP 주소 차단

