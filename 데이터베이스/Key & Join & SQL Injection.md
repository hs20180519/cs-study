# 01. 식별자(Key)

한 Entity 내에서 **인스턴스를 유일하게 구분**할 수 있는 단일 속성 또는 속성 집합

조건에 만족하는 튜플을 찾거나 순서대로 정렬할 때 사용됨

## 특성

- 유일성(uniqueness): 하나의 Entity에서 키로 지정된 속성의 값은 튜플마다 달라야 한다.
    
    → 키 값이 같은 튜플은 존재할 수 없다.
    
- 최소성(minimality): 꼭 필요한 최소한의 속성들로만 키를 구성한다.
    
    → 하나의 속성으로 구성된 키는 당연히 최소성을 만족한다.
    

![image](https://github.com/user-attachments/assets/e9c6d72d-3bf7-4990-8a8f-06099e1bef35)

## 슈퍼키(Super Key)

- 유일성을 만족하는 속성 또는 속성들의 집합
- 슈퍼키가 될 수 없는 속성: 나이, 직업, 등급, 적립금, 고객이름
- 고객아이디 속성은 유일성을 만족하므로, 고객아이디를 포함하는 모든 속성 집합은 슈퍼키가 될 수 있다.

## 후보키(Candidate Key)

- 유일성과 최소성을 만족하는 속성 또는 속성들의 집합
- 후보키 = 슈퍼키 중에서 최소성을 만족하는 것
- 고객아이디는 후보키가 될 수 있지만, (고객아이디, 고객이름)은 후보키가 될 수 없다.
    
    → 고객아이디 속성만으로도 유일성을 만족할 수 있기 때문
    

## 기본키(Primary key)

- 여러 후보키 중에서 기본적으로 사용할 키

![image (1)](https://github.com/user-attachments/assets/5d91f777-1aba-42a7-8145-10a869e474e5)

### 어떤 속성이 기본키로 적합한가?

1. null값을 가질 수 있는 속성이 포함된 후보키는 기본키로 부적합하다
2. 값이 자주 변경될 수 있는 속성이 포함된 후보키는 기본키로 부적합하다
3. 단순한 후보키(e.g. 자리수가 적은 정수, 단순 문자열)를 기본키로 선택한다

## 대체키(Alternate Key)

- 기본키로 선택되지 못한 후보키

![image (2)](https://github.com/user-attachments/assets/383a401d-2c89-4cce-9980-1d7b2959f69e)

## 외래키(Foreign Key)

- 어떤 entity에 소속된 속성 또는 속성 집합이 다른 entity의 기본키가 되는 키
- 다른 entity의 기본키를 그대로 참조하는 속성의 집합
- entity간의 관계를 올바르게 표현하기 위함
- 외래키를 기본키로 사용할 수도 있고, 외래키를 포함하여 기본키를 구성할 수도 있음

![image (3)](https://github.com/user-attachments/assets/dacd24ad-9397-43f7-ac71-08b48ea99bb9)

- 자신의 기본키를 참조하도록 외래키를 정의할 수도 있음
    
![image (4)](https://github.com/user-attachments/assets/97ecb624-3a52-4ee9-b655-e60a5cd6b754)
    

## 복합키(Composite Key)

- 기본키의 속성 개수가 두 개 이상인 경우

## 대리키(Surrogate Key)

- 식별자가 너무 길거나 여러 개의 속성으로 구성되어 있는 경우 인위적으로 추가한 식별자

# 02. Join

## INNER JOIN

- 기본 조인 형식
- 두 개의 테이블의 동일한 속성의 교집합을 추출하는 방법
- 명시적 조인 표현: join과 on 키워드 사용
    
    ```sql
    SELECT *
    FROM employee INNER JOIN department
    ON employee.deptNo = department.deptNo
    ```
    
- 암시적 조인 표현: from절에 조인하고자 하는 테이블 나열
    
    ```sql
    SELECT *
    FROM employee, department
    ON employee.deptNo = department.deptNo
    ```
    
- 테이블 간 교집합

### NATURAL JOIN

- 두 테이블이 가진 동일한 컬럼명을 기준으로 조인 조건문이 암시적으로 일어나는 내부 조인
- 같은 이름을 가진 컬럼은 결과에 한 번만 포함됨

```sql
SELECT * FROM employee NATURAL JOIN department
```

### CROSS JOIN

- 조인되는 두 테이블에서 곱집합을 반환
- 카티션 곱이라고도 표현
- m개의 튜플을 가진 테이블과 n개의 튜플을 가진 테이블을 cross join하면 m*n개의 튜플을 얻을 수 있음

```sql
SELECT * FROM employee, department
```

## OUTER JOIN

- 조인을 했을 때 한쪽의 테이블에 데이터가 없어도 조인 결과에 포함시키는 조인

### LEFT OUTER JOIN

- JOIN 키워드의 왼쪽에 있는 테이블의 모든 데이터가 결과에 포함됨

→ 오른쪽 테이블의 데이터가 null값일 수 있음

### RIGHT OUTER JOIN

- JOIN키워드의 오른쪽에 있는 테이블의 모든 데이터가 결과에 포함됨

→ 왼쪽 테이블의 데이터가 null값일 수 있음

### FULL OUTER JOIN

- 결과에 양쪽 테이블의 모든 데이터가 포함됨
- mysql에서는 지원하지 않음 → UNION 키워드를 사용해서 완전 외부 조인을 구현할 수 있음

```sql
# mysql
SELECT *
FROM A LEFT JOIN B

UNION

SELECT *
FROM A RIGHT JOIN B;
```

![image (10)](https://github.com/user-attachments/assets/6db4c49d-a34a-4796-af6d-2bfb373e95e7)

![image (5)](https://github.com/user-attachments/assets/295ef867-ca9d-463d-8d80-238eb884a765)

# 03. SQL Injection

- 보안상의 취약점을 이용하여, 임의의 SQL문을 주입하고 실행되게 하여 데이터베이스가 비정상적인 동작을 하도록 조작하는 공격 기법.

## 일반적인 SQL Injection

### Case 1. Where 구문 우회(쿼리 조건 무력화)

![image (9)](https://github.com/user-attachments/assets/faf1a797-5411-4546-a4cc-3a8226dd52a8)

![image (7)](https://github.com/user-attachments/assets/7311552d-1fd7-4bcb-b427-8907ef67ffc0)

- 로그인 시 id 입력칸에 `admin’ or ‘1’=’1`을 입력한다면 아래와 같은 쿼리가 생성된다.

```sql
SELECT * FROM users WHERE id='admin' or '1'='1' AND password="anything";
```

![image (8)](https://github.com/user-attachments/assets/d782a40d-4122-4273-93ec-af4327d5f43d)

*참고: AND 연산자는 OR 연산자보다 우선순위가 높다.

- admin 계정이 있다고 가정할 때 조건식 1은 참, 조건식 2는 무조건 참이므로 패스워드가 틀려도 admin 계정으로 로그인에 성공하게 됨
- 문제: 비밀번호 입력칸에 `-- and password=”아무거나”`를 입력하면?

### Case 2. 게시글 조회

- 게시판 기능에서 사용자가 9번 게시글을 클릭하면 상세 조회를 위해 아래와 같은 SQL 쿼리가 사용되고, `/board/view.php?board_no={board_no}` 의 형태로 HTTP 요청을 보낸다고 가정

```sql
SELECT board_no, subject, content, author, post_date
FROM board
WHERE board_no=9;
```

- board_no 매개변수의 값을 `9999;drop table users;`로 조작하면 아래와 같은 SQL 쿼리가 실행되어 users 테이블이 삭제됨

```sql
SELECT board_no, subject, content, author, post_date
FROM board
WHERE board_no=9999;drop table users;
```

## Error Based SQL Injection

- 의도적으로 SQL 구문 오류를 유발하여 웹 애플리케이션에 표시되는 데이터베이스 오류를 기반으로 수행되는 공격 기법

## UNION Based SQL Injection

- UNION 명령어를 이용한 SQL Injection
- 로그인 시 id에 `test’ UNION SELECT 1,1 --` 을 입력하면:
    
    ```sql
    SELECT *
    FROM users
    WHERE ID="test"
    UNION SELECT 1,1 -- AND password="anything"
    ```
    
- users 테이블에 등록된 id와 password 목록을 전부 조회할 수 있게 됨

## Blind SQL Injection

- 웹 브라우저 화면상에 데이터베이스 오류 정보나 데이터가 직접적으로 노출되지 않을 때 이용하는 기법
- 쿼리 결과의 참/거짓으로부터 데이터의 값을 유출
- 스무고개 같은 느낌

### Boolean 기반

- SQL 쿼리의 결과가 참/거짓이냐에 따라 웹 애플리케이션의 응답이 다른 경우 사용됨

```jsx
SELECT *
FROM board
WHERE id = "hello" AND LENGTH(password) = 3;
# hello라는 제목의 게시글이 있다고 가정했을 때,
# 만약 비밀번호의 길이가 3이라면 게시물이 검색될 것
```

### Time 기반

- SQL 쿼리의 결과가 참/거짓이냐에 따라 서버의 응답 시간을 제어할 수 있을 때 사용됨
- e.g. 조건이 참인 경우 서버 5초 대기

```sql
SELECT *
FROM board
WHERE board_no = 5
AND if(LENGTH(password) = 3, sleep(5), false);
# 5번 게시물이 존재한다고 가정했을 때,
# 해당 sql문을 실행했을 때 서버가 5초 대기한다면 비밀번호의 길이가 3임을 알 수 있음
```

## 대응 방안

### 입력값 검사

- SQL 구문으로 해석될 수 있는 문자 또는 SQL injection 공격에 사용되는 SQL 구문의 포함 여부를 검사하고,
- 포함되었다면 요청을 차단하거나 해당 문자를 제거 또는 대체해야 함

### 저장 프로시저 사용

- 웹 애플리케이션에서 데이터베이스에 접속하여 작업을 수행할 때 저장 프로시저를 사용하는 방법
- 하지만 저장 프로시저 내에서도 SQL 쿼리가 사용되기 때문에,
- 저장 프로시저 내에서 안전하지 않은 방식으로 처리된다면 여전히 SQL Injection 공격에 노출될 수 있음

### Prepared Statement 사용

- 반복적으로 사용되는 SQL 쿼리를 데이터베이스에 준비해 두었다가,
- 쿼리 실행에 필요한 값들만 매개변수로 전달하여 실행하는 방식
- 외부에서 쿼리의 구조를 알 수 없고 HTTP url의 어떤 매개변수가 쿼리의 어느 부분에 전달되는지 알 수 없음

# 04. 자주 나오는 질문

## 키의 종류에 대해 설명해 주세요.

키의 종류에는 후보키, 기본키, 대체키, 복합키, 대리키가 있습니다.

후보키는 entity에서 각각의 인스턴스를 유일하게 구분할 수 있는 속성입니다.

기본키는 후보키 중에서 entity 내의 각 인스턴스를 유일하게 식별하기에 가장 적합한 키입니다.

대체키는 후보키 중에서 기본키로 선정되지 않은 속성이며, 복합키는 하나의 속성으로 기본키가 될 수 없는 경우 두 개 이상의 속성을 묶어 식별자로 정의한 것입니다.

대리키는 식별자가 너무 길거나 여러 개의 속성으로 구성된 경우, 인위적으로 추가하는 식별자입니다.

## Inner Join과 Outer Join의 차이를 설명해 주세요.

중복이 없는 속성을 가지고 있는 테이블 A와 B를 join한다고 가정했을 때, inner join은 A와 B의 교집합을 추출하고 outer join은 A와 B의 합집합을 추출하는 방법입니다. inner join은 일치하는 값이 없는 튜플은 결과에 포함시키지 않고, outer join은 일치하지 않는 튜플도 결과에 포함시킨다는 차이가 있습니다.

## SQL Injection이란 무엇인가요?

SQL Injection은 임의의 SQL문을 주입하고 실행되게 하여 데이터베이스가 비정상적인 동작을 하도록 조작하는 공격 기법입니다.

## SQL Injection의 대응 방안에 대해 설명해 주세요.

SQL Injection 공격을 방지하기 위해서는 사전에 입력값을 검증하여 공격 가능성이 있는 구문이나 문자의 포함 여부를 검사하는 방법이 있습니다. 또는, 저장 프로시저를 사용하거나 매개변수화된 구문(Prepared Statement)를 사용하여 방지할 수 있습니다.

# 참고자료

[데이터베이스 개론 : 네이버 지식백과](https://terms.naver.com/list.naver?cid=58430&categoryId=58430)

[SQL 인젝션 기초](https://www.bugbountyclub.com/pentestgym/view/52)

[[웹보안]SQL INJECTION](https://m.mkexdev.net/427)

[SQL Injection 이란? (SQL 삽입 공격)](https://noirstar.tistory.com/264)
