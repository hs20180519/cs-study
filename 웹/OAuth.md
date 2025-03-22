# 00. Open Authorization

<aside>

💡 사용자 정보를 사용하려는 애플리케이션에 대한 인가(Authorization)를 위한 오픈 표준 프로토콜

</aside>

- 일반적으로 이 프로토콜은 페이스북이나 구글 같은 애플리케이션으로부터 이름, 생년월일, 이메일 등의 사용자 관련 정보를 제 3자 애플리케이션이 접근할 수 있도록 허용함
- 제 3자 애플리케이션은 사용자 비밀번호를 알 필요 없이 해당 정보를 사용할 수 있게 됨

> 인증은 유저가 직접, 권한은 서비스에게
> 

# 01. OAuth 2.0 의 기본 구성 요소

### Resource Owner (자원 소유자)

- 사용자(유저)
- 제3자 애플리케이션이 이 정보에 접근할 수 있도록 권한을 부여

*내 서비스의 사용자*

### Client

- 사용자 정보를 사용하려는 제3자 애플리케이션
- 사용자의 동의를 받은 후, 인가 서버로부터 Access Token을 받아 사용자 정보를 조회

*내 서비스*

### Authorization Server (인가 서버)

- 사용자로부터 권한 부여를 받아 토큰을 발급하는 서버
- 클라이언트 요청에 따라 Authorization Code, Access Token, Refresh Token 등을 발급

*Google OAuth 서버, Kakao 인가 서버 등*

### Resource Server (자원 서버)

- 보호된 사용자 정보를 가지고 있는 서버
- 클라이언트는 인가 서버에서 발급받은 Access Token을 사용해서 이 서버에 접근

*구글의 사용자 프로필 API 서버 등*

**Access Token**

- 인가 서버가 발급한 접근 권한 증명서

**Refresh Token**

- Access Token 만료 시, 다시 발급받기 위한 토큰

# 02. OAuth 동작 흐름

## Authorization Code Grant (인가 코드 방식)

![image](https://github.com/user-attachments/assets/d147db4e-ec34-4177-a4f7-4845265a330f)


1. **사용자가 클라이언트에서 로그인 시도**
- *구글로 로그인 클릭*

2. **클라이언트 → 인가 서버에 인가 요청**
- 클라이언트가 브라우저를 통해 인가 서버로 리다이렉션
- 이 때 포함되는 정보
    - `client_id` : 클라이언트 앱 ID
    - `redirect_uri` : 사용자 인가 후 다시 돌아올 URL
    - `response_type=code`
    - `scope` : 요청할 정보 범위 : 이메일, 프로필

3. **사용자가 인가 서버에 로그인 및 동의**
- *구글 로그인 및 권한 허용*

4. **인가 서버 → 클라이언트에 인가 코드 전달**
- 사용자가 동의하면, 인가 서버는 인가 코드(Authorization Code)를 생성해서
클라이언트의 `redirect_uri`로 리디렉션하면서 코드를 전달함

---

5. **클라이언트 → 인가 서버에 토큰 요청**
- 클라이언트는 받은 인가 코드와 함께 **서버 간 통신**으로 인가 서버에 토큰 요청
- `client_secret`(비밀키)도 함께 전송

6. **인가 서버 → 클라이언트에 Access Token 발급**

---

7. **클라이언트 → 자원 서버에 사용자 정보 요청**
- 받은 Access Token을 사용해서 자원 서버에 사용자 정보 요청

8. **자원 서버 → 사용자 정보 응답**

![image](https://github.com/user-attachments/assets/ba1144a0-1625-4bee-b1e0-e6a92bea6236)


## 기타 다른 Grant Type 종류

### Device Authorization Grant

- TV, 콘솔 등 입력장치가 제한된 기기에서 사용
- 스마트 TV, IoT 기기 등
- TV에서 코드를 보여줌 → 사용자가 휴대폰이나 PC에서 인증

Implicit Grant (현재는 거의 사용 안 함)

Resource Owner Password Credentials Grant (신뢰된 앱에서 사용)

Client Credentials Grant (서버 간 통신)

# 03. 자주 나오는 질문 정리

### OAuth 가 뭔데요?

OAuth는 사용자의 비밀번호를 직접 공유하지 않고, 제3자 애플리케이션이 사용자 자원에 접근할 수 있도록 권한을 위임하는 프로토콜입니다. 

### OAuth 2.0 흐름에 대해 간단히 설명해주세요.

- 사용자가 클라이언트(이하 클라)에게 사용 요청을 보낸다.
- 클라는 권한 서버에 권한 부여 승인 코드 요청(response_type=code로 지정하여 요청)을 보낸다.
- 이후 클라는 권한 서버에서 제공하는 로그인 페이지를 띄워 사용자에게 보여준다.
- 사용자가 로그인 하면 권한 서버는 권한 부여 승인 코드 요청에 전달받은 redirect_url로 Authorization Code를 전달한다.
- Authorization Code는 권한 서버에서 제공하는 API를 통해 Access Token으로 교환된다.

### OAuth 1.0 과 OAuth 2.0의 차이에 대해 설명해주세요.

OAuth는 사용자의 비밀번호를 직접 공유하지 않고, 제3자 애플리케이션이 사용자 자원에 접근할 수 있도록 권한을 위임하는 프로토콜입니다. 

OAuth 2.0은 그 후속 버전으로, 기존 OAuth 1.0의 복잡한 서명 방식을 없애고 HTTPS 기반으로 보안을 처리하여 구현을 단순화했습니다. 

또한 다양한 인가 방식(Authorization Code, Client Credentials 등)을 지원해 모바일, 웹 등 여러 환경에 대응할 수 있도록 확장되었습니다. 

현재 대부분의 서비스는 OAuth 2.0을 기반으로 동작하고 있으며, 보안성과 유연성 면에서 사실상 표준처럼 사용되고 있습니다.

### Authorization Code Grant 방식이 가장 많이 사용되는 이유는?

Authorization Code Grant 방식이 가장 많이 사용되는 이유는 **보안성과 유연성** 때문입니다.

이 방식은 인가 코드와 Access Token을 **서로 다른 단계**에서 주고받기 때문에, 토큰이 노출될 위험이 적습니다. 특히 토큰 발급은 서버 간 통신(백엔드)에서 이루어지기 때문에 클라이언트 측에서 민감한 정보가 노출되지 않아 안전합니다. 

또한, PKCE를 함께 사용하면 모바일 앱이나 SPA처럼 보안이 약한 환경에서도 사용할 수 있어, **웹, 모바일을 아우르는 범용적인 방식**으로 자리잡았습니다. 

이처럼 다양한 환경을 지원하면서도 높은 보안 수준을 유지할 수 있다는 점에서 가장 널리 사용되고 있습니다.

# 04. PKCE (Proof Key for Code Exchange) - 픽시

https://devtalk.kakao.com/t/flutter-user/137532

<aside>
💡 OAuth 2.0의 **Authorization Code Grant**를 사용하는 과정에서 인가 코드 탈취 공격(Code Interception Attack)을 막기 위한 **보안 확장 기능**

</aside>

### ⚠️ 공격 시나리오 (PKCE 없을 때)

1. 사용자가 인가 서버로부터 Authorization Code를 받음
2. 이 코드를 도중에 누군가 탈취
3. 탈취자는 클라이언트로 가장해서 Access Token을 받아냄

> → 이걸 방지하려면 클라이언트가 “진짜 나야!”라는 걸 증명할 수 있어야 함
> 

핵심 메커니즘: `code_verifier` 로 `code_challenge`를 증명

→ 암호화 된 문자열(`code_challenge`)을 만들고, 그걸 인가 코드 발급 때 보냄

→ 액세스 토큰 발급 때 `code_verifier` 보내서, 서버 쪽에서는 code_verifier를 해싱한 뒤 앞서 받은 `code_challenge`과 비교
