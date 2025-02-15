# 01. 쿠키와 세션을 사용하는 이유
HTTP 프로토콜은 다음과 같은 특성을 가진다.

- connectionless (비연결 지향)
    - 클라이언트가 서버로 요청을 보내면 서버는 클라이언트에 응답하고 연결을 끊는 특징
- stateless (상태정보 유지 안함)
    - 연결을 끊는 순간 클라이언트와 서버의 통신이 끝나며 상태 정보를 유지하지 않는 특징

즉, 서버와 클라이언트가 통신을 할 때 연속적으로 이어지지 않아 클라이언트가 누구인지 매번 확인해야 한다.

이러한 특성이자 약점을 보완하기 위해 쿠키 및 세션을 사용한다.

# 02. 쿠키
⇒ 서버가 HTTP 응답에서 보내는 데이터 조각
</br>
⇒ 클라이언트의 상태 정보를 쿠키 형태로 로컬에 저장했다가 필요 시 정보 참조 및 재사용

- 클라이언트(브라우저) 로컬에 저장
- key-value 형태로 된 데이터 파일
- 쿠키 이름, 쿠키 값, 만료 시간, 전송할 도메인 명, 전송 경로, 보안 연결 여부, HttpOnly 여부로 구성
- 클라이언트에 300개까지 쿠키 저장 가능, 도메인 당 20개 쿠키 가질 수 있음
- 하나의 쿠키는 4KB 까지 저장 가능
- 사용자가 따로 요청하지 않더라도 브라우저가 Request 시 Request Header를 넣어 자동으로 서버에 전송
- 쿠키의 기한이 정해져 있지 않고 명시적으로 지우지 않으면 반 영구적으로 쿠키가 남아있음

## 쿠키 사용 목적
- 세션 관리
쿠키를 사용하면 서버에 저장해야 할 로그인, 장바구니, 스코어등의 정보를 관리할 수 있다. (예: 자동 로그인, 쇼핑몰 장바구니 등)

- 서비스 개인화
서비스에서 사용자가 커스터마이징 할 수 있는 정보나 테마 등의 세팅 값을 쿠키에 저장할 수 있다. (예: 오늘 더 이상 팝업 보지 않기)

- 트래킹
서비스에서의 사용자의 행동 정보를 기록할 수 있다. (예: 한 번 누른 좋아요 버튼 더 못누르게 하기)

## 쿠키 작동 과정
![image2](https://github.com/user-attachments/assets/c369f484-19f9-4829-9316-500ff9776a23)
1. 클라이언트가 페이지를 요청한다 (웹사이트에 접속).
2. 웹 서버는 쿠키를 생성하고 HTTP 응답 헤더에 포함시켜 전송한다.
3. 클라이언트는 받은 쿠키를 로컬에 저장하고, 이후 해당 서버에 요청을 보낼 때마다 쿠키를 함께 전송한다.

## 쿠키의 종류

| 종류                | 설명                                                                                   |
|---------------------|----------------------------------------------------------------------------------------|
| **세션 쿠키**        | 브라우저가 열려 있는 동안만 유지되며, 브라우저를 닫으면 자동으로 삭제되는 쿠키.           |
| **지속 쿠키(Persistent)**        | 만료 기간이 설정된 쿠키로, 브라우저를 닫아도 유지되며, 설정된 만료 기간까지 남아 있는 쿠키. |
| **서드파티 쿠키**       | 사용자가 방문한 웹사이트가 아닌, 제3자가 설정한 쿠키로, 광고나 분석 목적으로 사용됨.        |
| **보안 쿠키 (Secure)**| HTTPS 연결에서만 전송되는 쿠키로, 보안이 강화된 환경에서 사용되는 쿠키.                     |
| **HttpOnly 쿠키**    | 자바스크립트를 통해 접근할 수 없고, 오직 서버와의 HTTP(S) 요청에서만 사용 가능한 쿠키.      |
| **SameSite 쿠키**    | 동일 사이트에서만 쿠키가 전송되도록 설정된 쿠키로, CSRF 공격 방지에 유용.                  |


# 03. 세션
⇒ 브라우저가 종료되기 전까지 클라이언트의 요청을 유지하게 해주는 기술
</br>
- 사용자 정보 파일을 서버에서 저장 및 관리
- 서버에서 클라이언트를 구분하기 위해 세션 ID를 부여함
- 사용자 정보를 서버에서 관리하므로 쿠키보다 보안이 좋음
- 사용자가 많아질 수록 서버 메모리를 많이 차지
- 웹 브라우저 종료 시까지 인증 상태 유지
- 웹 서버에 저장되는 쿠키라고 볼 수 있음

## 세션 작동 방식
![image3](https://github.com/user-attachments/assets/08fe4425-37e6-4573-85aa-fef57d61b3bc)
1. 클라이언트가 서버에 접속하면 서버가 세션 ID를 생성한다.
2. 서버는 생성한 세션 ID를 클라이언트에게 전송한다.
3. 클라이언트는 세션 ID를 쿠키에 저장하고, 서버에 요청할 때마다 이 세션 ID를 함께 전송한다.

# 04. 쿠키와 세션 차이
- 정보 저장 위치
    - 쿠키: 클라이언트의 웹 브라우저가 지정하는 메모리 또는 하드디스크
    - 세션: 서버의 메모리
- 용량 제한
    - 쿠키: 한 도메인 당 20개, 한 개의 쿠키 당 4KB로 제한
    - 세션: 클라이언트가 접속하면 서버에 의해 생성되므로 개수나 용량 제한 없음
- 만료 시점
    - 쿠키: 저장 시 expire 속성 설정하여 삭제할 날짜 지정 가능
    - 세션: 클라이언트가 로그아웃, 서버에서 설정한 시간 동안 반응 없으면 무효화되어 정확한 시점 알 수 없음
- 리소스
    - 쿠키: 클라이언트에 저장되고 클라이언트의 메모리를 사용하기 때문에 서버 자원 사용하지 않음
    - 세션: 서버에 저장되고, 서버 메모리로 로딩되기 때문에 세션이 생길 때마다 리소스를 차지함
- 보안
    - 쿠키: 클라이언트 측에서 저장되기 때문에 조작되거나 탈취될 가능성이 있음. 민감한 정보는 암호화하거나 저장하지 않는 것이 좋음.
    - 세션: 서버에 저장되기 때문에 비교적 안전. 하지만 세션 ID가 탈취될 경우 보안 문제가 발생할 수 있음.
- 사용 예시
    - 쿠키: 로그인 유지, 방문 기록 저장, 사용자 선호 설정 등 장기적인 상태 유지
    - 세션: 로그인 처리, 쇼핑몰 장바구니, 일시적인 사용자 상태 관리
</br>

| 구분            | 쿠키(Cookie)                                            | 세션(Session)                                        |
|-----------------|---------------------------------------------------------|------------------------------------------------------|
| **정의**        | 사용자의 브라우저에 저장되는 작은 데이터 파일              | 서버에서 관리되는 사용자 상태 정보                    |
| **저장 위치**   | 클라이언트(브라우저)                                      | 서버(세션 ID만 브라우저에 저장됨)                     |
| **수명**        | 만료 기간에 따름 (설정된 기간까지 유지)                   | 브라우저 종료 시 소멸                                 |
| **보안**        | 클라이언트 측에서 저장되어 상대적으로 덜 안전함             | 서버에 저장되어 상대적으로 안전함                     |
| **용량 제한**   | 4KB 이하 (브라우저별로 저장 개수와 크기에 제한 있음)       | 서버 자원 사용 (용량 제한이 거의 없음)                |
| **성능**        | 클라이언트 처리, 네트워크 트래픽이 증가할 수 있음           | 서버 자원 사용, 트래픽은 적지만 서버에 부하가 있을 수 있음 |
| **사용 예시**   | 로그인 상태 유지, 사용자 선호 설정 등                     | 로그인 처리, 쇼핑몰 장바구니 등                       |
| **예시 코드**   | `document.cookie = "username=John Doe; ...";` (JavaScript) | `session.setAttribute("username", "John Doe");` (Java)|

# 05. 자주 나오는 질문 정리

### 1. 쿠키와 세션의 차이점은 무엇인가요?

- **쿠키**는 사용자의 브라우저에 저장되는 작은 데이터 파일로, 클라이언트 측에서 정보를 저장하고 유지하는 방식입니다. 반면, **세션**은 서버에서 사용자 상태를 관리하는 방식으로, 세션 ID만이 클라이언트에 저장되고 나머지 정보는 서버에 저장됩니다.
- 쿠키는 주로 장기적인 상태 저장에 사용되며, 만료 기간을 설정할 수 있습니다. 세션은 브라우저가 종료되면 소멸하는 단기적인 상태 저장에 적합합니다.

### 2. 쿠키와 세션 중 어떤 것이 더 보안에 적합한가요?

- **세션**이 쿠키보다 보안에 더 적합합니다. 쿠키는 클라이언트의 브라우저에 저장되기 때문에 조작되거나 탈취될 가능성이 있습니다. 반면, 세션은 서버에서 관리되므로 민감한 정보가 클라이언트에 저장되지 않아 상대적으로 안전합니다.


### 3. 세션은 서버 자원을 소모한다고 하는데, 다수의 사용자가 접속하면 어떻게 관리할 수 있나요?

- 다수의 사용자가 접속할 때 서버의 세션 관리를 위해 **세션 스토리지** 전략을 사용합니다. 이를 위해 서버 메모리가 아닌 **데이터베이스**나 **디스크**에 세션 데이터를 저장하여 자원 소모를 줄이기도 합니다. 또한, 세션 만료 시간을 적절히 설정하거나, **세션 클러스터링**을 통해 여러 서버에 분산 저장함으로써 서버 부하를 관리할 수 있습니다.

### 4. 세션과 관련된 보안 문제를 해결하는 방법은 무엇이 있나요?

- **HTTPS**를 사용해 세션 ID가 암호화된 통신을 통해 전달되도록 해야 합니다.
- 세션 ID를 자주 **갱신**하고, 세션 하이재킹을 방지하기 위해 **만료 시간**을 설정하는 것이 중요합니다.
- **SameSite** 속성을 설정해 쿠키가 동일한 도메인에서만 전송되도록 하거나, **HttpOnly** 속성을 설정해 자바스크립트로 쿠키를 조작하지 못하게 할 수 있습니다.

### 5. 쿠키와 세션을 함께 사용하는 이유는 무엇인가요?

- 쿠키와 세션을 함께 사용하면 **효율적인 상태 관리**가 가능합니다. 예를 들어, 세션 ID를 쿠키에 저장하여 서버에서 세션 상태를 관리하고, 쿠키를 통해 사용자의 선호도나 비로그인 상태에서도 유지가 필요한 정보를 저장할 수 있습니다.
- 세션이 쿠키에 비해 보안이 높지만 서버에 저장되고 서버의 자원을 사용하기 때문에 속도가 느려질 수 있습니다. 따라서 자원 관리 차원에서 쿠키와 세션을 병행 사용하여 서버 자원의 낭비를 방지하며 웹 사이트의 속도를 높일 수 있습니다.

# 06. 추가 보너스 - 세션 하이재킹(Session Hijacking)

세션 하이재킹은 사용자가 인증된 세션을 가로채서 공격자가 해당 사용자의 권한을 무단으로 사용하는 공격 기법입니다. 사용자가 웹 애플리케이션에 로그인하면 서버는 사용자에게 세션 ID를 부여하며, 이 세션 ID는 사용자의 활동을 추적하는 데 사용됩니다. 공격자는 다양한 방법으로 이 세션 ID를 탈취하여 피해를 일으킬 수 있습니다.

### 세션 하이재킹 공격 기법
- **세션 고정(Session Fixation):** 공격자가 미리 세션 ID를 설정한 뒤, 피해자가 해당 세션 ID로 로그인하도록 유도하는 방식.
- **네트워크 패킷 스니핑(Packet Sniffing):** 네트워크를 통해 전송되는 세션 ID를 가로채는 방식. 특히 HTTPS를 사용하지 않는 경우 쉽게 발생할 수 있습니다.
- **크로스사이트 스크립팅(XSS):** 악성 스크립트를 이용해 세션 ID를 탈취하는 방법.

### 예방 방법
- **HTTPS 사용:** 데이터를 암호화하여 전송 중에 세션 ID가 탈취되는 것을 방지합니다.
- **세션 타임아웃 설정:** 일정 시간 동안 비활성화된 세션을 자동으로 종료하여 세션 ID가 오래 유지되지 않도록 합니다.
- **HTTPOnly 및 Secure 플래그 설정:** 쿠키에 세션 ID를 저장할 때 `HTTPOnly`와 `Secure` 플래그를 설정하여 스크립트로 접근하거나 암호화되지 않은 연결에서 세션 ID가 노출되지 않도록 합니다.
- **세션 재생성:** 로그인 또는 중요한 액션 후에 새로운 세션 ID를 생성하여 기존 세션 ID를 무효화합니다.

=> 세션 하이재킹은 웹 애플리케이션에서 매우 위험한 공격이므로, HTTPS 사용과 세션 관리 방식을 철저히 하여 이를 방지해야 합니다.



## 참조
- https://learn.microsoft.com/ko-kr/aspnet/web-api/overview/advanced/http-cookies
- [https://velog.io/@octo__/쿠키Cookie-세션Session](https://velog.io/@octo__/%EC%BF%A0%ED%82%A4Cookie-%EC%84%B8%EC%85%98Session)
