# 01. 인덱스 개념
인덱스(Index)는 데이터 **검색** 성능의 향상을 목적으로 테이블의 열에 적용되는 자료구조이다.  
데이터베이스를 책에 비유하면, 인덱스는 **책의 색인**과 같은 역할을 한다.

# 02. 인덱스 사용의 장단

## 1. Full Table Scan vs Index Range Scan

### Full Table Scan
풀 스캔(Full Scan)은 데이터베이스에서 **전체 데이터를 순차적으로 읽는 방식**을 말한다.  
특정 조건에 해당하는 데이터를 찾을 때 사용되지만, 전체 데이터를 읽어야 하므로 **대규모 데이터베이스에서는 성능이 저하**될 수 있다.  
특히, **인덱스가 없는 경우**에는 풀 스캔을 수행해야 한다.

### Index Range Scan
인덱스로 특정 컬럼을 기준으로 정렬하여 미리 저장해두고, 인덱스를 통해 특정 컬럼의 값을 빠르게 찾는 방식이다. 
보통 **B-Tree**을 사용해 구현된다.  
**특정 조건에 해당하는 데이터만** 읽을 수 있으므로 풀 스캔보다 효율적이다.

DBMS는 인덱스를 사용하는 것이 빠를지, 전체 테이블을 검색하는 것이 빠를지 판단하여 **빠른 쪽을 선택**한다.

### Table Full Scan 
![title](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbthdjU%2FbtsAszMsbvY%2Fwy61Tz1j5abVpx1jKnk4Hk%2Fimg.png)   

시퀀셜 액세스와 **Multiblock I/O** 방식으로 디스크 블록을 읽는다.  
한 블록에 속한 모든 레코드를 한 번에 읽고, 캐시에서 못 찾으면 **한 번의 I/O 호출로 인접한 여러 블록을 한꺼번에 I/O**하는 방식이다.

### Index Range Scan
![title](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbI8Z0o%2FbtsArgGBVPC%2FelpHL8IncGVrUtJoAWTHi1%2Fimg.png)
**랜덤 액세스**와 **Single Block I/O** 방식으로 디스크 블록을 읽는다.  
캐시에서 블록을 찾지 못하면, **레코드 하나를 읽기 위해 매번 I/O를 호출**하는 메커니즘이다.  
따라서 **많은 데이터를 읽을 때는 Table Full Scan보다 불리**하며, 프로세스 대기 시간 때문에 성능이 저하될 수 있다.

# 03. 인덱스 사용의 단점
- 인덱스는 물리적 디스크에 저장되기 때문에 **테이블 크기의 약 10%의 추가 용량**을 차지한다.
- 
- **INSERT, DELETE, UPDATE** 작업이 자주 발생할 경우 성능이 저하될 수 있다.  
  인덱스를 **항상 최신 상태로 유지**해야 하기 때문에 **추가적인 연산**이 발생하기 때문이다.
  
즉, 인덱스는 **삽입/수정/삭제의 성능 저하를 감수하고, 조회 성능을 향상**시키기 위한 수단이다.

# 04. 인덱스의 종류

## 1. 클러스터형 인덱스 (Clustered Index)
![title]( https://hudi.blog/static/bb7fd0469e3eb38aa3cbacb8d3c27e13/02d09/clustered-index.png )
- **기본 키(PK)**로 설정하면 자동으로 생성된다.
- **물리적으로 데이터가 정렬**되므로 조회 속도가 빠르다.
- 테이블당 **1개**만 생성할 수 있다.
- **데이터 입력, 수정, 삭제 시 정렬 상태**를 유지해야 하므로 속도가 느리다.
- **리프 페이지가 곧 데이터**이므로, 별도의 인덱스 페이지가 없다.

## 2. 비클러스터형 인덱스 (Non-Clustered Index)
![title](https://hudi.blog/static/1fd850943125d0ea5ae38595465673dd/02d09/non-clustered-index.png)

- 테이블 당 여러 개의 **비클러스터형 인덱스**를 생성할 수 있다.
- 레코드 원본은 정렬되지 않으며, **인덱스 페이지만 정렬**된다.
- **리프 페이지는 데이터 주소(RowId)를 가리키기** 때문에, 클러스터형보다 검색 속도가 느리다.
- **데이터의 물리적 재배열이 없으므로** 변경 작업 속도가 빠르다.
- **추가적인 저장 공간**이 필요하다.

## 3. 고유 인덱스 (Unique Index)
- 값이 **중복되지 않는 인덱스**로, **기본 키나 고유 키**로 지정된 컬럼에서 자동으로 생성된다.

# 05. B-Tree와 B+Tree

## 1. B-Tree
![title](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fy3nFx%2FbtrJp91qiHq%2FVkiki8GPrL2bbgBnsoJpoK%2Fimg.jpg)
B-Tree는 자식 노드가 **2개 이상**인 트리로, **노드 내에 데이터가 여러 개** 존재할 수 있다.  
예를 들어, 노드 내 데이터가 2개일 경우 **2차 B-Tree**, 3개일 경우 **3차 B-Tree**라 부른다.

### B-Tree 성립 조건
1. **노드의 데이터가 n개라면, 자식 노드의 개수는 n+1개**여야 한다.
2. 노드 내 데이터는 **반드시 정렬된 상태**여야 한다.
3. **작은 값은 왼쪽, 큰 값은 오른쪽** 서브트리에 위치해야 한다.
4. 루트 노드를 제외한 모든 노드는 **최소 M/2개의 데이터를** 갖고 있어야 한다.
5. **Leaf 노드로 가는 경로의 길이는 모두 같아야** 하며, **중복된 데이터는 허용되지 않는다**.

## 2. B+Tree
![title](https://velog.velcdn.com/images%2Femplam27%2Fpost%2Fbcbce100-d475-4cda-aebe-946d1813949c%2FB%ED%94%8C%EB%9F%AC%EC%8A%A4%20%ED%8A%B8%EB%A6%AC%20%EA%B8%B0%EB%B3%B8%20%ED%98%95%ED%83%9C.jpg)
B+Tree는 **B-Tree의 변형**으로, **효율적인 삽입, 검색, 삭제**를 추구하는 자료구조다.  
- **삽입과 삭제 연산**은 **단말 노드에서만** 이루어진다.
- 단말 노드끼리 **연결 리스트**로 연결되어 있어 **순차 탐색에 유리**하다.
