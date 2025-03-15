# 01. REST

REST(Representational State Transfer): 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는 모든 것

1. HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)를 표시하고,
2. HTTP Method를 통해 해당 자원(URI)에 대한 행위를 규정하여
3. 그 결과를 받는 것을 말함

## 구성 요소

1. 자원(Resource): HTTP URI
2. 자원에 대한 행위(Verb): HTTP Method
3. 자원에 대한 행위의 내용(Representations): HTTP Message Payload

## 특징

1. 서버-클라이언트(Server-Client) 구조
2. 무상태(Stateless)
3. 캐시 처리 가능(Cacheable)
4. 계층화(Layered System)
5. 인터페이스 일관성(Uniform Interface)

## 장단점

### 장점

- HTTP 프로토콜의 인프라를 그대로 사용하기 때문에, REST API를 위한 별도의 인프라가 필요 없음
- HTTP 표준 프로토콜을 따르는 모든 플랫폼에서 사용 가능
- 의도하는 바를 명확하게 나타내 쉽게 파악 가능
- 서버와 클라이언트의 역할을 명확히 분리함

### 단점

- 표준이 없어 정의가 필요함
- HTTP Method의 형태가 제한적
- 브라우저를 통해 테스트할 경우가 많은 서비스의 경우, Header 정보 값 처리가 요구됨
- 구형 브라우저에서 호환되지 않음

# 02. REST API

REST의 원리를 따르는 API

## 설계 규칙

- URI는 정보의 자원을 표현해야 한다.
- 자원에 대한 행위는 HTTP Method르 표현한다.

1. URI는 동사보다는 **명사**를, 대문자보다는 **소문자** 사용
    
    ```
    Bad: http://domain.com/Running
    Good: http://domain.com/run
    ```
    
    - 컨트롤 자원을 의미하거나 명사로 표현하기 어려운 경우에는 예외적으로 동사 허용
        
        *컨트롤 자원: 다른 자원의 행동을 조작하거나 제어하는 데 사용되는 특별한 자원*
        
    
    ```
    Bad: http://domain.com/posts/duplicating
    Good: http://domain.com/posts/duplicate
    ```
    
2. 마지막에 슬래시(/)를 포함하지 않음
    
    ```
    Bad: http://domain.com/test/
    Good: http://domain.com/test
    ```
    
3. 언더바 대신 **하이픈**을 사용
    
    ```
    Bad: http://domain.com/test_blog
    Good: http://domain.com/test-blog
    ```
    
4. 파일 확장자는 URI에 포함하지 않음
    
    ```
    Bad: http://domain.com/photo.jpg
    Good: http://domain.com/photo
    ```
    
5. 행위는 URL이 아닌 HTTP Method를 통해 전달
    
    ```
    Bad: POST http://domain.com/delete-post/1
    Good: DELETE http://domain.com/post/1
    ```
    

### 리소스간 관계 표현

**REST 리소스 간에는 연관 관계가 있는 경우(일반적으로 소유 ‘has’ 관계)**

```
/리소스명/리소스 ID/관계가 있는 다른 리소스명

GET http://domain.com/users/{userId}/devices
```

**관계명이 복잡하거나 애매한 경우**

```
e.g. 사용자가 좋아하는 디바이스 목록 조회
GET http://domain.com/users/{userId}/likes/devices
```

### 자원의 표현 방법: Collection, Document

- **Document:** 단순 문서, 하나의 객체 개념
- **Collection**: document의 집합
- document와 collection은 모두 리소스로 표현할 수 있으며, URI에 표현됨

```
http://domain.com/sports/soccer
```

collection: sports

document: sports

```
http://domain.com/sports/soccer/players/13
```

collection: sports, players

document: soccer, 13

⇒ collection은 복수로 사용

# 03. RESTful

- REST의 원리를 따르는 시스템을 의미하나,
- REST를 사용했다고 해서 모두 RESTful한 것은 아니며
- REST API의 설계 올바르게 지킨 시스템을 RESTful하다고 할 수 있음

- REST API 설계 규칙을 올바르기 지키지 못한 시스템은 REST API를 사용하였지만 RESTful하다고 할 수 없음
    - e.g. 모든 CRUD기능을 POST로 처리

# 04. 자주 나오는 질문

## REST에 대해 설명해주세요.

REST는 자원을 이름으로 구분해 해당 자원의 정보를 주고받는 방식의 모든 것을 의미합니다. 기본적으로 HTTP 프로토콜을 활용하며, 리소스, 메소드, 메세지로 구성되어 있습니다.

URI로 자원을 구성하고, HTTP Method로 자원에 대한 행위를 표현합니다. 일반적으로 JSON이나 XML을 통해 서버와 클라이언트가 데이터를 주고받습니다.

## RESTful API의 설계 규칙에는 어떤 것들이 있을까요?

RESTful API는 REST의 특징을 기반으로 서비스 API를 구성한 것입니다. 

REST API의 설계 규칙으로는, URI는 가급적 명사를 사용해야 하며 마지막 문자로 슬래시(/)를 포함하지 않아야 합니다. 또한, 행위는 HTTP Method를 통해 표현해야 하는 것 등이 있습니다.

# 05. 참고자료

https://devuna.tistory.com/77

https://devuna.tistory.com/79

https://sanghaklee.tistory.com/57

https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-REST-API-%EC%A0%95%EB%A6%AC#%EB%A6%AC%EC%86%8C%EC%8A%A4_%EA%B0%84%EC%9D%98_%EA%B4%80%EA%B3%84%EB%A5%BC_%ED%91%9C%ED%98%84%ED%95%98%EB%8A%94_%EB%B0%A9%EB%B2%95
