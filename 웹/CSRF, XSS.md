

## 01. CSRF (Cross-Site Request Forgery)
<img src="https://blog.kakaocdn.net/dn/oODAt/btq3n89umk7/AzC6DkwK18MsJqkvyWhR31/img.png">

### 개념
- **서버가 사용자를 신뢰해서 생기는 문제** 
- 사용자가 의도하지 않은 요청을 특정 웹사이트에 보내게 하는 공격 기법  
- 공격자는 피해자의 브라우저가 인증된 상태라는 점을 악용하여 악성 요청을 보냄  
- 피해자는 공격 사이트에 방문하는 것만으로도 공격에 노출될 수 있음  


### 방어 방법
- **CSRF 토큰 사용**: 요청마다 고유한 토큰을 포함하여 검증 
- **SameSite 쿠키 설정**: 쿠키가 외부 사이트에서 전송되지 않도록 설정  
- **Referer / Origin 검증**: 요청 출처가 신뢰할 수 있는지 확인  
- **CORS 정책 강화**: 신뢰할 수 있는 도메인만 요청을 허용  


## 02. XSS (Cross-Site Scripting)

<img src="https://velog.velcdn.com/images/nathan29849/post/3e6f8019-0a55-494d-816c-ef4c880652fe/image.png">

<img src="https://velog.velcdn.com/images/nathan29849/post/618383d3-e7f2-44d9-8dc8-3436c0aca06d/image.png">

### 개념
- **사용자가 사이트를 신뢰해서 생기는 문제** 
- 공격자가 악성 스크립트를 웹사이트에 삽입하여 사용자 브라우저에서 실행되도록 하는 공격  
- 일반적으로 입력 필드, URL 파라미터 등을 통해 자바스크립트를 주입함  
- 공격 유형  
  - **Stored XSS**: 악성 스크립트가 DB에 저장됨 (게시글, 댓글 등)  
  - **Reflected XSS**: 악성 스크립트가 즉시 실행됨 (URL 파라미터, 검색창 등)  
  - **DOM-based XSS**: 클라이언트 측에서 DOM 조작을 통해 발생  

### 방어 방법
- **입력값 검증 (Input Validation)**  
- **출력 시 HTML 이스케이프 처리 (Output Encoding)**  
- **Content Security Policy (CSP) 적용**  
- **JavaScript 내 `innerHTML` 사용 지양**  


## 03. CSRF vs XSS
| 비교 항목  | CSRF  | XSS  |
|------------|------|------|
| 공격 대상 | 서버 (사용자의 권한을 악용) | 클라이언트 (사용자의 브라우저에서 실행) |
| 주요 원인 | 브라우저의 인증된 상태를 악용 | 입력값 검증 미흡 |
| 공격 방식 | 사용자의 인증 정보를 이용해 악의적 요청 수행 | 악성 스크립트를 삽입하여 실행 |
| 방어 기법 | CSRF 토큰, SameSite 쿠키, Referer 검증 | 입력 검증, HTML 이스케이프, CSP |


## 04. JWT 이중 토큰 저장 위치
### 액세스 토큰 vs 리프레시 토큰 저장 위치 비교

| 저장 방식 | 액세스 토큰 저장 | 리프레시 토큰 저장 | 장점 | 단점 |
|-----------|----------------|----------------|----------------|----------------|
| **로컬 스토리지** | O | X | 간편함 | XSS 공격에 취약 |
| **세션 스토리지** | O | X | 브라우저 종료 시 자동 삭제 | XSS 공격에 취약 |
| **쿠키 (HttpOnly, Secure)** | O | O | XSS 공격 방어 가능 | CSRF 방어 필요 |

### 추천 방식
- 액세스 토큰: **메모리(React의 상태 관리, Redux 등) 또는 HttpOnly 쿠키**  
- 리프레시 토큰: **HttpOnly + Secure 쿠키 (서버에서 관리)**  


## 05. 면접에서 자주 나오는 질문

### 1. CSRF와 XSS의 차이점은?
CSRF는 사용자의 인증된 상태를 악용하여 서버에 요청을 보내는 공격이며, XSS는 클라이언트 측에서 악성 스크립트를 실행하는 공격입니다. CSRF는 CSRF 토큰과 SameSite 쿠키로 방어하고, XSS는 입력 검증과 CSP 적용으로 방어할 수 있습니다.

### 2. JWT를 로컬 스토리지에 저장하면 안 되는 이유는?
로컬 스토리지는 XSS 공격에 취약하기 때문에, JWT가 탈취될 위험이 큽니다. 보안 강화를 위해 HttpOnly 쿠키에 저장하는 것이 일반적입니다.
