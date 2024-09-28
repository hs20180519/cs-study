# 01. OSI 모델

- 국제 표준화 기구(ISO)에서 만든 네트워크 참조 모델
- 통신 단계를 **7단계**로 나눴음
- OSI 7계층이라고도 부름
- 최하위 : 1 ~ 최상위 : 7

![image](https://github.com/user-attachments/assets/ab0b705a-14b0-484b-8d49-568ea0e90322)

### 물리 계층
![image 1](https://github.com/user-attachments/assets/23683af1-c40d-4ed4-8115-14f82c02d070)

- 최하단 계층
- 0과 1로 표현되는 비트 신호를 주고 받음
- 가장 근원적인 통신이 이루어지는 계층
- 0과 1로 표현된 비트 데이터가 다양한 통신 매체를 통해 전기, 빛, 전파 등의 신호로 운반됨

### 데이터 링크 계층
![image 2](https://github.com/user-attachments/assets/4dfbbac3-2885-4804-8081-a6c21c8078ff)

- 네트워크 내 주변 장치 간의 정보를 올바르게 주고받기 위한 계층
- 물리 계층을 통해 주고받는 정보에 오류가 없는지 확인하고, 전송 과정 중 발생가능한 충돌 문제 해결(인트라 네트워크 통신 내에서만)
- MAC 주소라는 주소 체계를 통해 네트워크 내 송수신지를 특정 가능함
    - 프레임에 Mac 주소를 부여

### 네트워크 계층
![image 3](https://github.com/user-attachments/assets/7cbb07e4-edea-4937-8ee9-cbed4518279e)

- 메시지를 다른 네트워크에 속한 수신지까지 전달하는 계층
    - c.f) 데이터 링크 계층 : **동일한** 네트워크 내 장치 간의 통신, 네트워크 계층: **서로 다른** 네트워크 간의 통신
- 즉, 인터넷을 가능하게 하는 계층
- IP 주소라는 주소 체계 사용
- 라우팅: 통신하고자 하는 수신지의 호스트와 네트워크를 식별하고 수신지까지의 최적 경로를 결정
- 네트워크 계층 프로토콜: IP, ICMP, IGMP, IPse

### 전송 계층
![image 4](https://github.com/user-attachments/assets/5eb14c97-de4b-457d-b786-2cb0a714fe14)

- 신뢰성 있고 안전성 있는 전송을 해야 할 때 필요한 계층
- 패킷의 흐름 제어, 전송 오류 점검
    - 흐름 제어: 연결 속도가 빠른 송신자가 연결 속도가 느린 수신자를 압도하지 않도록 최적의 전송 속도를 결정
    - 오류 제어: 수신된 데이터가 완료되었는지 확인하고 수신되지 않은 경우 재전송을 요청함
- 포트 정보를 통해 실행 중인 응용 프로그램의 식별이 이루어지도록 함
- 전송 계층 프로토콜: TCP(전송 제어 프로토콜), UDP(사용자 데이터그램 프로토콜)
    - TCP: 신뢰성, 연결지향적
    - UDP: 비신뢰성, 비연결성, 실시간

### 세션 계층
![image 5](https://github.com/user-attachments/assets/b924dda6-55b3-40f8-ba8e-3a57a193e7b0)

- 세션을 관리하기 위해 존재하는 계층
    - 세션: 통신을 주고받는 호스트의 응용 프로그램 간 연결 상태
- 연결 상태를 생성하거나 유지하고, 종료 시 끊어 주는 역할

### 표현 계층
![image 6](https://github.com/user-attachments/assets/3e093ef3-20e0-4ce8-acf1-8e67bd64b27b)

- 데이터의 변환, 암호화, 압축 담당
- (like 번역가 역할) 애플리케이션 계층이 사용할 수 있도록 데이터를 프레젠테이션한다.

### 응용 계층
![image 7](https://github.com/user-attachments/assets/ac7bb72e-308a-4858-bc25-3dd54e96ca1d)

- OSI 참조 모델 최상단에 있는 계층
- 사용자의 응용 프로그램과 가장 밀접히 맞닿아 있는 계층
- 응용 계층은 응용 프로그램에 다양한 네트워크 서비스를 제공함
    - ex) 웹 브라우저 프로그램에 웹 페이지 제공 - HTTP 프로토콜
    - ex) 이메일 클라이언트 프로그램에 송수신된 이메일을 제공 - SMTP 프로토콜
    - 타 계층에 비해 응용 계층에 속한 프로토콜이 많음

# 02. OSI 모델이 중요한 이유

OSI 모델은 주로 네트워크를 이론적으로 기술하고 이해할 때 사용된다.

그 목적이 ‘이상적 설계’에 가깝다고 볼 수 있다.

따라서 현대 인터넷은 OSI 모델을 엄격하게 따르기 보다 더 단순한 인터넷 프로토콜 제품군을 따르고 있다.

그럼에도 불구하고 OSI 모델은 네트워크 문제를 해결하는 데 유용하기 때문에 중요하다. 네트워크 문제를 분석하고 문제의 원인을 분리하는 데 도움을 준다는 측면에서, 불필요한 많은 작업을 피할 수 있게 한다.

OSI 모델과 같이, 통신 과정을 굳이 계층으로 나눠 표현한 이유는 다음과 같다.

1. 네트워크 구성과 설계가 용이하다.
2. 네트워크 문제 진단과 해결이 용이하다.

# 03. 자주 나오는 질문 정리

### OSI 7계층에 대해 설명해주세요.

OSI 모델은 컴퓨터 네트워크 프로토콜과 통신을 일곱 개의 계층으로 나눈 모델입니다. 이 계층은 아래에서 위로 다음과 같이 나뉩니다:

- 물리 계층 (Physical Layer): 전기적, 기계적 특성을 다루며 비트 스트림을 전송합니다.
- 데이터 링크 계층 (Data Link Layer): 물리 계층과 네트워크 계층 사이에서 프레임을 전송하고 오류 검사와 수정을 수행합니다.
- 네트워크 계층 (Network Layer): 경로 선택 및 라우팅을 다루며 패킷을 목적지까지 전달합니다.
- 전송 계층 (Transport Layer): 종단 시스템 간 연결, 흐름 제어, 오류 복구를 제공합니다.
- 세션 계층 (Session Layer): 통신 세션 관리와 동기화를 담당합니다.
- 표현 계층 (Presentation Layer): 데이터 형식 변환, 암호화, 압축 등 데이터 표현을 다룹니다.
- 응용 계층 (Application Layer): 최종 사용자와 상호작용하는 응용 프로그램을 지원합니다.

### 참조

- [https://www.cloudflare.com/ko-kr/learning/ddos/glossary/open-systems-interconnection-model-osi/](https://www.cloudflare.com/ko-kr/learning/ddos/glossary/open-systems-interconnection-model-osi/)
- 혼자 공부하는 네트워크
- [https://yjym33.tistory.com/144](https://yjym33.tistory.com/144)