## 01. SSL과 TLS
- SSL : Secure Sockets Layer
- TLS : Transport Layer Security

</br>

- SSL은 대칭 키 암호화와 공개 키 암호화를 기반으로 동작하는 프로토콜로, 인증과 암호화를 수행한다.
- TLS는 SSL을 계승하며 취약성을 보완한 프로토콜이다.
- SSL 2.0과 SSL 3.0을 거쳐 TLS 1.0, TLS 1.1, TLS 1.2, TLS 1.3이 순차적으로 출시되었다. (SSL 1.0 X)
- SSL/TLS를 사용하는 대표적인 프로토콜은 HTTPS이다.

## 02. HTTPS 메시지의 단계
1. TCP 쓰리 웨이 핸드셰이크
2. TLS 핸드셰이크
3. 암호화된 메세지 송수신

## 03. TLS 핸드셰이크
: 암호화 통신을 위해 키를 교환하고, 인증서 송수신과 검증이 이루어진다.   
<img width="559" alt="image" src="https://github.com/user-attachments/assets/d77180ae-b7ef-4fac-8c22-31699cab7687">

1. Client Hello 클라이언트 헬로
- 클라이언트가 서버에 연결 요청을 보낸다.
- 지원되는 TLS 버전, 사용 가능한 암호와 방식과 해시 함수, 키를 만들기 위해 사용할 클라이언트의 난수 등이 포함되어 있다.
- 이때, 사용 가능한 암호화 방식과 해시 함수를 "암호 스위트"라고 한다.
  예) TLS_AES_128_GCM_SHA256
  
2. ServerHello 서버 헬로
- 서버가 클라이언트의 요청에 응답한다.
- TLS 버전, 암호 스위트 등의 정보, 키를 만들기 위해 사용한 서버의 난수 등이 포함되어 있다.
- ClientHello 메시지와 ServerHello 메시지를 주고받으면 암호화된 통신을 위해 사전 협의해야 할 정보들이 결정된다.

3. Server Certificate 서버 인증   
클라이언트는 서버의 인증서를 검증하여 서버의 신뢰성을 검증한다.

4. Client key exchange 서버 키 교환
- 클라이언트가 세션 키를 생성하고, 이를 서버의 공개 키로 암호화하여 서버에 전송한다.
- 선택된 암호화 방식에 따라 추가적인 키 정보를 서버가 클라이언트에 전송할 수 있다.

5. Server Client Certificate(Optional)   
클라이언트가 자신의 인증서를 서버에 전송한다. 이는 클라이언트 인증이 필요한 경우에만 발생한다.

6. Verify Client Certificate   
서버가 클라이언트의 인증서를 검증하여 클라이언트의 신뢰성을 확인한다.

7. Client Finished   
클라이언트가 핸드셰이크가 완료되었음을 알리기 위해 Finished 메시지를 서버에 보낸다.

8. Server Finished   
서버도 클라이언트의 Finished 메시지를 받고, 자신의 Finished 메시지를 클라이언트에게 보낸다.

## 04. 자주 나오는 질문
1. TLS 핸드셰이크 과정의 주요 단계는?
<img width="743" alt="image" src="https://github.com/user-attachments/assets/e192d9b3-a84d-4ec8-998f-857284df6a0d">

2. 서버 인증서의 역할은?
<img width="750" alt="image" src="https://github.com/user-attachments/assets/dd4f5440-b8f3-4ec9-922e-9a78ecf36838">

3. TLS와 SSL의 차이점은 ?
<img width="754" alt="image" src="https://github.com/user-attachments/assets/e3bcd8de-d309-48ba-898f-c5b52c6be965">
