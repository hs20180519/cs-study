# 01. SQL의 종류

## DDL(Data Definition Language, 데이터 정의어)

- 데이터베이스 구조를 정의, 수정, 삭제하는 언어
- ALTER, CREATE, DROP, TRUNCATE

## DML(Data Manipulation Language, 데이터 조작어)

- 데이터베이스 내의 레코드를 조회, 수정, 삭제하는 언어
- SELECT, INSERT, UPDATE, DELETE

## DCL(Data Control Language, 데이터 제어어)

- 데이터베이스에 대한 접근 권한을 제어하는 언어
- GRANT, REVOKE

## TCL(Transaction Control Language, 트랜잭션 제어어)

- 트랜잭션을 컨트롤하는 언어
- TCL도 DCL로 분류하는 경우도 있음
- COMMIT, ROLLBACK, SAVEPOINT

# 02. SQL 쿼리의 수행순서

- FROM, ON, JOIN > WHERE, GROUP BY, HAVING > SELECT > DISTINCT > ORDER BY > LIMIT

# 03. 더 알아보기

## DROP vs. TRUNCATE vs. DELETE

|  | DELETE | TRUNCATE | DROP |
| --- | --- | --- | --- |
| 명령어 종류 | DML | DDL | DDL |
| 처리속도 | 느림 | 빠름 | 빠름 |
| COMMIT | 사용자가 직접 | 자동 | 자동 |
| ROLLBACK 가능여부 | COMMIT 이전에는 가능 | 불가 | 불가 |
| 삭제 방식 | 테이블 내 데이터만 삭제 | 테이블의 모든 데이터 삭제 | 테이블까지 모두 제거 |

## HAVING vs. WHERE

- 공통점: 조건을 지정하는 데 사용되는 키워드

|  | WHERE | HAVING |
| --- | --- | --- |
| 수행 시점 | GROUP BY 수행 전 | GROUP BY 수행 후 |
| 조건 적용 대상 | 개별 행 단위 | 그룹핑된 데이터 단위 |
| 집계함수 사용 가능 여부 | 불가능 | 가능 |

# 04. 자주 나오는 질문

## SQL의 종류를 설명해 주세요.

종류에는 DDL, DML, DCL, TCL이 있습니다.

DDL은 데이티 정의어로 데이터베이스 구조를 정의, 수정, 삭제하는 언어입니다. DML은 데이터 조작어로 데이터베이스 내의 레코드를 조회, 삽입, 변경, 삭제하는 언어입니다.

DCL은 데이터 제어어로, 데이터베이스에 대한 접근 권한을 제어하는 언어입니다. 

TCL은 트랜잭션 제어어로 트랜잭션을 제어하는 데 사용되는 언어입니다.

## DELETE, TRUNCATE, DROP의 차이가 무엇인지 설명해 주세요.

DELETE는 테이블 내에서 특정 행을 삭제합니다. WHERE 절을 사용하여 특정 조건에 맞는 행만 삭제할 수 있습니다. 삭제 시 테이블의 구조와 데이터는 그대로 유지되며, COMMIT 이전에는 롤백이 가능합니다.

TRUNCATE는 테이블 내 모든 행을 삭제하되 테이블 구조는 남깁니다. DELETE보다 속도가 빠르며, 롤백이 불가능합니다.

DROP은 테이블 내 데이터를 포함하여 테이블 자체를 완전히 제거합니다. 세 키워드 중 가장 빠르며, 롤백이 불가능합니다.

## HAVING과 WHERE의 차이를 설명해 주세요.

having은 그룹을, where은 개별 행을 필터링하는 데 사용됩니다. having절은 집계함수를 사용할 수 있지만 where절은 group by보다 먼저 수행되기 때문에 집계함수를 사용할 수 없습니다.
