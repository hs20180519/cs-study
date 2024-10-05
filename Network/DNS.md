## 00. 도메인 네임과 네임 서버
- 사용자는 상대 호스트를 특정하기 위해 IP 주소보다는 도메인 네임을 사용한다.
- 도메인 네임은 호스트의 IP주소와 대응되는 문자열 형태의 호스트 특정 정보
- www.example.com
- 도메인 네임과 IP 주소는 네임 서버에서 관리한다.
- 도메인 네임을 관리하는 네임서버를 DNS서버라고 한다.   
<img width="655" alt="image" src="https://github.com/user-attachments/assets/62a69cf2-498d-4782-8798-fd5b9b3b8faa">
</br>
사용자가 네임서버에 도메인 주소를 전달, 네임 서버는 도메인 주소에 맞는 IP주소를 응답, 사용자는 IP주소를 통해 사이트 접속 가능

</br>

## 01. 도메인 네임
- 도메인 네임은 점(.)을 기준으로 계층적으로 분류된다.
- 최상단에 루트 도메인, 그 다음 단계인 최상위 도메인이 있다.   
<img width="573" alt="image" src="https://github.com/user-attachments/assets/c7e8c9ff-b35a-42cf-b826-32ef1a1c1734">

- 도메인 네임의 마지막 부분을 최상위 도메인, 줄여서 TLD라고 한다.
- www.example.com의 최상위 도메인은 'com'이다. (kr, jp, us)
- 루트 도메인도 도메인 네임의 일부이다.
- www.google.com. (일반적으로는 루트 도메인을 생략한다)
- 2단계 도메인 example
- 전체 주소 도메인 네임 Fully-Qualified Domain Name(FQDN)
- 마지막 단계까지 고려하면 호스트를 식별할 수 있는 도메인 네임을 얻을 수 있다. 이를 호스트 네임 www이라고 한다. (때로는 FQDN 자체를 가리키기도 한다)

</br>

## 02. DNS
- 이렇게 계층적이고, 분산된 도메인 네임에 대한 관리 체계를 도메인 네임 시스템, 줄여서 DNS라고 부른다.
- DNS는 호스트가 이러한 도메인 네임 시슽메을 이용할 수 있도록 하는 애플리케이션 계층 프로토콜
- IP 주소를 모르는 상태에서 도메인 네임에 대응되는 IP 주소를 알아내는 과정을, 도메인 네임을 풀이한다 = RESOLVING
- 로컬 네임 서버
- 루트 네임 서버
- TLD(최상위 도메인) 네임 서버
- 책임 네임 서버

### 로컬 네임 서버
- 클라이언트와 맞닿아 있는 네임 서버로, 클라이언트가 도메인 네임을 통해 IP 주소를 알아내고자 할 때 가장 먼저 찾게 되는 서버
- 일반적으로 ISP에서 할당 (ISP : 인터넷 서비스 제공자, KT, SKT, ..)
- 공개 DNS 서버를 이용할 수도 있음(구글의 8.8.8.8. 8.8.4.4, ..)
- 클라이언트가 로컬 네임 서버에게 특정 도메인 네임에 대응되는 IP 주소를 질의했을 때, 알고 있다면 응답. 하지만 모른다면 ?

### 루트 네임 서버
- 이 경우, 루트 네임 서버로 해당 도메인 네임을 질의하게 된다.
- 루트 네임 서버는 루트 도메인을 관장하는 네임서버로, TLD 네임 서버의 IP 주소를 반환한다.
- [로컬 네임 서버] www.example.com의 IP 주소 ? -> com(루트 네임)을 관리하는 TLD 네임 서버의 주소 반환 [루트 네임 서버]

### TLD 네임 서버
- TLD, 최상위 도메인을 관리하는 서버
- TLD 네임 서버는 질의에 대해 TLD의 하위 도메인 네임을 관리하는 네임 서버 주소를 반환한다.
- 하위 도메인 네임을 관리하는 네임 서버는 또 그보다 하위 도메인 네임을 관리하는 네임 서버 주소를 반환한다.
- [로컬 네임 서버] www.example.com의 IP 주소 ? -> example.com을 관리하는 네임 서버의 주소 반환 [TLD 네임 서버]

### 책임 네일 서버
- 특정 도메인 영역을 관리하는 네임 서버
- 자신이 관리하는 도메인 영역의 질의에 대해서는 다른 네임 서버에 떠넘기지 않고 곧바로 답할 수 있음
- 로컬 네임 서버가 마지막으로 질의하는 네임 서버

</br>

## 03. RESOLVING(리졸빙)
### 1. 재귀적 질의
- 클라이언트가 로컬 네임 서버에게 도메인 네임을 질의하면, 로컬 네임 서버가 루트 네임 서버에게 질의하고,
- 루트 네임 서버가 TLD 네임 서버에 질의하고, TLD 네임 서버가 다음 단계에 질의하는 과정을 반복하며 최종 응답 결과를 역순으로 전달받음
<img width="608" alt="image" src="https://github.com/user-attachments/assets/a8986544-1f0e-46ec-b347-63ff5c74cfbc">
</br>

### 2. 반복적 질의
- 클라이언트가 로컬 네임 서버에게 IP 주소를 알고 싶은 도메인 네임을 질의하면,
- 로컬 네임 서버는 루트 도메인 서버에게 질의하여 다음으로 질의할 네임 서버의 주소를 응답받고,
- 다음으로 TLD 네임 서버에게 질의해서 다음으로 질의할 네임 서버의 주소를 응답받는 과정을 반복하다가
- 최종 응답 결과를 클라이언트에게 알려주는 방식
<img width="596" alt="image" src="https://github.com/user-attachments/assets/eb97f9ee-479c-4ae4-9a9d-a8a90523dd13">

</br>

## 04. DNS 캐시
- 하나의 도메인 네임을 리졸빙하기 위해서 8단계를 거쳐야하기 때문에 시간이 오래 걸리고, 네트워크 상의 메시지 수가 지나치게 늘어난다.
- 과부화 !!
- 실제로는 네임 서버들이 기존에 응답받은 결과를 임시로 저장했다가 추후 같은 질의에 이를 활용한다. => DNS 캐시
- 더 짧은 시간 안에 원하는 IP 주소를 얻을 수 있다.
- TTL과 함께 저장되어 캐시될 시간에 따라 저장된다.

</br>

## 05. URI
- 자원 : 네트워크상의 메시지를 통해 주고받는 대상(이미지 파일, 텍스트 파일 등)
- HTTP를 기반으로 이루어지므로, 자원은 'HTTP 요청 메시지의 대상'이라고도 불림
- 자원을 식별할 수 있는 정보가 URI(Uniform Resource Identifier)
- 위치를 이용해 자원을 식별하면 URL(Uniform Resource Locator)
- 이름을 이용해 자원을 식별하면 URN(Uniform Resource Name)

### URL
<img width="616" alt="image" src="https://github.com/user-attachments/assets/15ad2f6d-f5d4-4c0a-87d6-304ce32e3075">   
</br>
- schema : 자원에 접근하는 방법, http://, https://
- authority : 호스트를 특정할 수 있는 정보, IP 주소, 도메인 네임 (+포트 번호)
- path : 자원이 위치한 경로
- query : 쿼리 문자열 <query=value>
- fragment : 자원의 한 조각을 가리키기 위한 정보

### URN
- 자원의 위치가 변경되면 URL로는 자원을 식별할 수 없는 문제가 발생함
- URN은 고유한 이름을 붙이기 때문에 위치와 무관하게 식별할 수 있다
- urn:isbn:03819513
- 다만 널리 채택된 방식은 아니다.

</br>

## 06. 자주 나오는 질문
1. DNS란?
<img width="664" alt="image" src="https://github.com/user-attachments/assets/025353d3-912c-429e-b53c-07c3a578074d">

2. DNS 조회 과정은?
<img width="654" alt="image" src="https://github.com/user-attachments/assets/84fba9ff-9553-43df-8f6c-dedea644630a">

3. DNS 재귀와 반복 차이는?
<img width="662" alt="image" src="https://github.com/user-attachments/assets/259bb20e-ba2b-4f87-b491-ddca642021a7">

4. DNS 캐싱이란 무엇이며 장단점은?
<img width="675" alt="image" src="https://github.com/user-attachments/assets/bae5afc8-d3bc-4579-b1da-845f82adab92">
