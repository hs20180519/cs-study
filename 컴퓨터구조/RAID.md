# 01. RAID란?
- Redundant Array of Independent Disk(독립된 디스크의 복수 배열) 혹은
- Redundant Array of Inexpensive Disk(저렴한 디스크의 복수 배열)의 약자
- 여러 개의 하드 드라이브를 결합하여 성능을 향상시키거나 데이터를 보호하기 위한 기술
  
### 기대 효과
- 대용량의 단일 볼륨을 사용하는 효과
- 디스크 I/O 병렬화로 인한 성능 향상(RAID 0, RAID 5, RAID 6)
- 데이터 복제로 인한 안정성 향상(RAID 1)

### 특징
- RAID는 컴퓨터를 구성하는 여러 부품중 기계적인 특성 때문에 상대적으로 속도가 느린 하드디스크를 보완하기 위해 만든 기술
- RAID를 구성하는 디스크의 개수가 같아도, 구성 방식에 따라 성능, 용량이 바뀌게 된다.
- 이 구성 방식을 RAID Level이라 부른다.

# 02. Standard RAID Level
- 기본적으로 RAID 0 ~ RAID 6까지 있지만, 최근에 2,3,4는 사용되지 않는 편이다.

### RAID 0
<img width="224" alt="image" src="https://github.com/user-attachments/assets/afb39c9e-a0de-4568-a2d1-8c3da38479d8" />

- Striping(스트라이핑) : 데이터를 작은 블록으로 분할한 뒤, 각 블록을 여러 개의 디스크에 병렬로 저장하는 기술
- RAID를 구성하는 모든 디스크에 데이터를 분할하여 저장
- RAID 0을 구성하기 위해서 최소 2개의 디스크가 필요
- 전체 디스크를 모두 동시에 사용하기 때문에 성능은 단일 디스크의 성능의 N배, 용량도 N배(N은 RAID를 구성하는 디스크의 개수)
- 장점 : 성능과 용량 좋음
- 단점 : 하나의 디스크라도 문제가 발생할 경우 전체 RAID에 문제가 생김. 안정성이 1/N으로 줄어듦

### RAID 1
<img width="204" alt="image" src="https://github.com/user-attachments/assets/dc2a5afc-b0a2-4021-a2d7-d31b61b1190f" />

- Mirroring(미러링) : 데이터를 복제하여 동일한 복사본을 두 개 이상의 디스크에 저장하는 기술
- 모든 디스크에 데이터를 복제하여 기록
- 장점 : 안정성이 높다
- 단점 : 비용 문제

### RAID 2 ~ 4
- 거의 사용되지 않음

### RAID 5
<img width="404" alt="image" src="https://github.com/user-attachments/assets/1e3731af-bdeb-4ad0-8767-c06d7acad3a8" />

- 가장 사용 빈도가 높은 RAID Level
- Block 단위로 striping하고, error correction 위해 패리티를 1개의 디스크에 저장
- 패리티 저장 디스크를 고정하지 않고, 매번 다른 디스크에 저장
- 1개의 디스크 에러시 복구 가능(2개 이상의 디스크 에러시 복구 불가능)
- RAID 0에서 성능, 용량을 조금 줄이는 대신 안정성을 높임

* 패리티 : 데이터 보호를 위해 RAID에서 사용하는 오류 검출 및 복구 기법(검증용 정보를 추가로 생성하여 다른 디스크에 저장하는 것)

### RAID 6
![image](https://github.com/user-attachments/assets/cb7a9fee-eba6-495e-9683-a11911644478)

- RAID 5에서 성능, 용량을 줄이고, 안정성을 높임
- Block 단위로 striping하고, error correctino을 위해 패리티를 2개의 디스크에 저장
- 패리티 저장 디스크를 고정하지 않고, 매번 다른 디스크에 저장

# 03. Nested RAID (중첩 RAID)
- Standard RAID를 여러 개 중첩하여 사용
- RAID 01 / RAID 10
![image](https://github.com/user-attachments/assets/79e59b0a-3eb1-4e8e-9dc2-e0827eec406f)

# 04. 자주 나오는 질문
### 01. RAID 5와 RAID 6의 차이점 ?
- RAID 5는 1개의 패리티 블록으로 1개의 디스크 장애를 복구 가능
- RAID 6는 2개의 피리티 블록으로 2개의 디스크 장애까지 복구 가능

### 02. RAID 5와 RAID 6를 쓰기 가장 적절한 상황은?
- RAID 5는 비교적 적은 디스크와 가용 용량이 중요한 환경에 적합
- RAID 6는 안정성이 더 중요한 환경(중요 데이터 저장)에 적합

# 05. 참고
- https://ilovestorage.tistory.com/16
- https://12bme.tistory.com/286
