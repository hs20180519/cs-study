# 01. HTTP(Hyper Text Transfer Protocol)

- 응용 계층에서 정보를 주고받는 데 사용되는 프로토콜

## 특성

### 요청과 응답 기반 프로토콜

- http는 클라이언트와 서버가 서로 http 요청 메세지와 http 응답 메세지를 주고받는 구조로 동작
    
    → 같은 http 메시지일지라도, http 요청 메세지와 http 응답 메세지는 형태가 다름
    

### 미디어 독립적 프로토콜

- 클라이언트는 http 요청 메세지를 통해 서버의 자원을 요청
- 서버는 http 응답 메세지를 통해 요청받은 자원에 대해 응답
- http는 주고받는 자원의 특성과 상관없이, 자원을 주고받는 수단의 역할만을 수행함

**미디어 타입(media type)/MIME 타입**

- http에서 메세지로 주고받는 자원의 종류
- ‘type/subtype’ 형식으로 구성됨
    - type: 데이터의 유형
    - subtype: 주어진 타입에 대한 세부 유형

<img width="571" alt="image" src="https://github.com/user-attachments/assets/d18c0c54-7a32-4ae3-a91f-3c1235cc588f">

- *: 여러 미디어 타입을 통칭
- */*: 모든 미디어 타입
- 부가적인 설명을 위해 선택적으로 매개변수가 포함될 수도 있다.
    - type/subtype;parameter=value

### 무상태(stateless) 프로토콜

- 서버가 http 요청을 보낸 클라이언트와 관련된 상태를 기억하지 않음
    
    → 클라이언트의 모든 http 요청은 기본적으로 독립적인 요청으로 간주됨
    
- 요청 메세지를 여러 번 전송해도 서버는 각기 다른 요청으로 간주
    
    → 클라이언트도 같은 응답 메세지를 여러 번 받을 수 있다.
    

**stateless이 왜 효율적일까?**

- 서버는 일반적으로 많은 클라이언트와 동시에 상호작용함
    
    → 동시에 처리해야 할 요청 메세지의 수가 많음
    
- 이런 상황에서 모든 클라이언트의 상태 정보를 유지하는 것은 서버에 큰 부담이 된다.
- 만약 http가 상태를 유지하는 프로토콜이었다면:
    - client는 자신의 상태를 기억하는 특정 서버하고만 상호작용할 수 있게 됨
        
        → 특정 클라이언트가 특정 서버에 종속
        
        → 한 서버에 문제가 발생하면 종속된 클라이언트에도 문제 발생
        

**확장성(scalability)과 견고성(robustness)**

- stateless 특성은 특정 클라이언트가 특정 서버에 종속되지 않도록 하며, 서버 추가나 문제 발생 시 대처가 용이하도록 함
- 언제든 쉽게 서버를 추가할 수 있음
    
    → 확장성
    
- 서버 중 하나에 문제가 생겨도 다른 서버로 쉽게 대체할 수 있음
    
    → 견고성
    

### 지속 연결 프로토콜

- 초기의 http 버전(http 1.0 이하)는 비지속 연결 방식으로 동작했다.
    - 3-way handshake를 통해 TCP 연결을 수립한 후, 요청에 대한 응답을 연결을 종료
    - connectionless 특성을 가짐

**지속 연결**(persistent connection, keep-alive)

- 하나의 TCP 연결상에서 여러 개의 요청-응답을 주고받을 수 있는 기술
- 최근 대중적으로 사용되는 http 버전(1.1 이상)은 지속 연결 기술을 제공
- 비지속 연결에 비해 더 빠르게 여러 http 요청과 응답을 처리할 수 있음

<img width="697" alt="image (1)" src="https://github.com/user-attachments/assets/59365bc5-3164-43d6-aa40-04547180b002">

## HTTP 메세지 구조

- start line, headers, body

![image (2)](https://github.com/user-attachments/assets/55357d3b-e3c4-4925-94ac-5315957ff857)

<img width="490" alt="image (3)" src="https://github.com/user-attachments/assets/7bdb7d77-9ae5-4cb1-b110-024b855ee4bc">

### 시작 라인(start line)

- 요청 메세지일 경우 요청 라인,
- 응답 메세지일 경우 상태 라인이 된다.

- 요청 라인 구조: `[메서드] [요청 대상] [HTTP 버전]`
- 상태 라인 구조: `[HTTP 버전] [상태 코드] [이유 구문]`
    - 상태 코드: 요청에 대한 결과
    - 이유 구문: 상태 코드에 대한 문자열 형태의 설명
    - e.g. HTTP/1.1 200 OK

### 필드 라인(field line, header line)

- 0개 이상의 http 헤더가 명시됨
- http header: http 통신에 필요한 부가 정보
- 구조: `[헤더 이름]: [헤더 값]`

**요청 메세지**

- Host: 요청을 보낼 호스트. 보통 도메인 네임
- User-Agent: 요청을 시작하는 클라이언트 측의 프로그램
- Referer: 클라이언트가 요청을 보낼 때 머무르고 있던 URL
- Authorization: 인증 정보

![image (4)](https://github.com/user-attachments/assets/0bd169e1-f6b2-4b6e-a540-0cc201e4368c)

**응답 메세지**

- Server: 요청을 처리하는 서버 측의 소프트웨어 관련 정보
- Allow: 클라이언트에게 허용된 http 메서드 목록. 여기에 없는 메서드로 요청을 보내면 405 에러
- Retry-After: 자원을 사용할 수 있는 날짜나 시각
- Location: 클라이언트에게 자원의 위치를 알려줌
- WWW-Authenticate: 자원에 접근하기 위한 인증 방식을 설명

![image (5)](https://github.com/user-attachments/assets/ed81f725-e7fd-4501-ae26-b2742afc937e)

### 메세지 본문(message-body)

- 다양한 콘텐츠 타입이 사용되거나, 없을 수도 있다.

## HTTP 메서드

| HTTP 메서드 | 설명 |
| --- | --- |
| **GET** | **자원을 습득하기 위한 메서드** |
| **POST** | **서버가 특정 작업을 처리하게끔 하는 메서드** |
| **PUT** | **자원을 대체하기 위한 메서드(덮어쓰기)** |
| **DELETE** | **자원을 삭제하기 위한 메서드** |
| **PATCH** | **자원에 대한 부분적 수정을 위한 메서드(일부 수정)** |
| **HEAD** | **GET과 동일하나, 헤더만을 응답받는 메서드** |
| CONNECT | 자원에 대한 양방향 연결을 시작하는 메서드 |
| OPTIONS | 사용 가능한 메서드 등 통신 옵션을 확인하는 메서드 |
| TRACE | 자원에 대한 루프백 테스트를 수행하는 메서드 |

## HTTP 상태 코드

<img width="697" alt="image (6)" src="https://github.com/user-attachments/assets/5ca28b36-75df-4504-8dc8-76ad6865e481">
<img width="697" alt="image (7)" src="https://github.com/user-attachments/assets/56db7a9b-e920-4cbe-ad41-2343860562cd">
<img width="697" alt="image (8)" src="https://github.com/user-attachments/assets/1431c763-bac3-4695-889b-5564839111c2">
<img width="697" alt="image (9)" src="https://github.com/user-attachments/assets/1790d4cf-5104-4eb5-bbf5-80039aaaccd2">

인증 vs. 인가(권한 부여)

- 인증: 자신이 누구인지 증명하는 것
- 인가: 인증된 주체에게 작업을 허용하는 것

⇒ 인증되지 않음(Unauthorized), 인가되지 않음(Forbidden)

<img width="697" alt="image (10)" src="https://github.com/user-attachments/assets/d3e2e81e-f836-4148-9a41-0e4578d88861">

# 02. HTTPS(Hyper Text Transfer Protocol Secure)

- http에 데이터 암호화가 추가된 프로토콜
- SSL/TLS 프로토콜을 통해 암호화 수행

## 동작과정

1. TCP 3-way handshake
2. TLS handshake: 암호화된 통신을 위한 사전작업
    1. 클라이언트가 ClientHello 메세지(암호화 통신을 위해 양측이 협의할 정보들) 전송
    2. 그에 대한 응답으로 서버가 ServerHello 메세지(제시된 정보를 선택) 전송
    3. 서버가 Certificate(인증서), CertificateVerify(검증을 위한 디지털 서명) 메세지 전송
    4. Finished 메세지 주고받음
3. 암호화된 메세지 송수신

# 03. HTTP vs. HTTPS

- 주요 차이점은 **보안**

## HTTP

1. 암호화 없음, 보안 레벨 낮음
    - HTTP는 데이터를 평문 형태로 전송한다. 때문에 데이터의 민감한 정보가 노출될 수 있고, 중간에서 데이터를 가로챌 위험이 있다.
2. 포트번호 80
3. 설정 및 운영이 간단함
    - HTTPS에 비해 구현과 운영이 단순함

## HTTPS

1. 암호화 있음, 보안 레벨 높음
    - 데이터를 암호화하여 전송하기 때문에 중간에서 데이터를 가로채 읽거나 수정하는 것을 방지할 수 있다.
2. 포트 번호 443
3. 인증서 필요
    - 서버의 신원을 확인하는 SSL/TLS 인증서가 필요함 → 사용자에게 신뢰성 제공
4. 성능 오버헤드
    - 암호화/복호화 과정에서 성능 오버헤드 발생할 수 있으나, 현대 하드웨어에서는 큰 문제가 되지 않음

# 05. 자주 나오는 질문

## http에 대해 설명해 주세요.

응용 계층에서 서버와 클라이언트가 자원을 주고받을 때 사용되는 통신 규약입니다. 요청과 응답 메세지를 주고받는 구조로 동작하며, 클라이언트와 서버에서 상태 정보를 저장하지 않는다는 무상태 프로토콜이라는 특성이 있습니다. (+ http 1.0 이하 버전에서는 비지속 연결 망식으로 동작하여 무연결(connectionlesss) 특성을 함께 가지고 있었으나, 최근에는 지속 연결을 지원합니다.)

## http와 https의 차이점에 대해 설명해주세요.

가장 큰 차이점은 데이터 전송 시 암호화의 여부입니다. http는 평문 형태로 데이터를 전송하며, https는 SSL/TLS 프로토콜을 사용해 데이터를 암호화하여 전송합니다. 따라서 https가 중간 공격을 방지할 수 있어 http보다 보안성이 높습니다.
