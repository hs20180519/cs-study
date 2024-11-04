# 01. B+Tree

- 기존의 B-tree는 어느 한 데이터의 검색은 효율적이지만, 모든 데이터를 한 번 순회하는 데에는 트리의 모든 노드를 방문해야 한다는 단점이 있었음
    
    → 이를 개선한 자료구조가 B+Tree
    
- DBMS의 Index 자료구조로 쓰임

# 02. B-Tree와 어떤 점이 다를까?

![image](https://github.com/user-attachments/assets/b48e5881-6a9d-46bd-8ae7-3cb6b218ad34)

1. 모든 key와 data를 leaf node에만 저장하고,
    
    leaf node가 아닌 node에서는 자식의 포인터만 저장한다.
    
2. 모든 leaf node가 연결리스트 형태이다.
3. leaf node의 부모 key는 leaf node의 첫번째 key보다 작거나 같다.

# 03. 장단점

- leaf node를 제외하고는 데이터를 저장하지 않기 때문에 메모리를 확보할 수 있음
    
    → 하나의 node에 더 많은 포인터를 가질 수 있어 트리의 높이가 더 낮아질 수 있고, 검색 속도 또한 높일 수 있음
    
- 옆에 있는 leaf node를 검사하려면 root node부터 다시 탐색해야 하는 B-Tree와 달리, B+Tree는 leaf node에서 선형검사를 수행할 수 있어 시간복잡도가 크게 줄어듬
- B-Tree는 최상의 경우 특정 key를 root node에서 찾을 수 있지만, B+Tree의 경우 반드시 leaf node까지 내려가야 함

# 04. 탐색

- B-Tree와 동일

# 05. 삽입

## Case 1. 분할이 일어나지 않는 경우

== key 수가 최대보다 적은 leaf node에 삽입하는 경우

- B-Tree의 삽입 과정과 동일

## Case 2. 분할이 일어나는 경우

== 노드가 가득찬 경우

1. 분할이 일어나는 노드가 leaf node가 아니라면, 기존 B-Tree와 동일하게 분할
    1. 중간 key를 부모 key로 올리고, 분할한 두 개의 노드를 왼쪽, 오른쪽 자식으로 설정
2. 분할이 일어나는 노드가 leaf node라면:
    1. 중간 key를 부모 key로 올림
    2. 오른쪽 node에 중간 key를 포함하여 분할
    3. 왼쪽 자식노드와 오른쪽 자식 노드를 이어 연결리스트 형태를 유지

![image (1)](https://github.com/user-attachments/assets/7ebc5aa1-b944-4587-932c-e22f82a926f3)

![image (2)](https://github.com/user-attachments/assets/9568dca2-af75-4072-aca5-666a7459e447)

# 06. 삭제

## Case 1. 삭제할 key가 leaf node의 가장 앞에 있지 않은 경우

- B-Tree의 삭제 과정과 동일

## Case 2. 삭제할 key가 leaf node의 가장 앞에 위치한 경우

1. B-Tree와 동일하게 $k$ 삭제
    1. 단, leaf node를 병합할 때는 부모 key를 가져오는 과정은 생략하고 왼쪽 자식과 오른쪽 자식 병합
2. leaf node가 아닌 노드에 존재하는 중복된 key를 해당 노드보다 오른쪽에 있으면서 가장 작은 값(inorder successor)로 갱신

![image (3)](https://github.com/user-attachments/assets/aa040224-a3f4-4543-93c3-70cde840eb7c)

![image (4)](https://github.com/user-attachments/assets/e2d7187f-0fb7-4671-85cf-51c2c20e253c)

# 07. 자주 나오는 질문

## B-Tree와 B+Tree이 무엇인가요?

B-Tree는 이진 트리를 확장해 모든 leaf node들이 같은 높이를 갖도록 하는 트리입니다. 노드는 N개의 정렬된 key를 가질 수 있고, 그 경우 자식 노드는 N+1개가 됩니다. B+Tree는 B-Tree와 유사하지만, leaf node에만 데이터가 있다는 차이가 있습니다.

## B-Tree와 B+Tree의 차이점에 대해 설명해 주세요.

B-Tree는 탐색을 위해 모든 노드를 찾아서 이동해야 한다는 단점이 있었습니다. B+Tree는 이러한 단점을 개선하여 내부 노드에는 key와 pointer만을 저장하고 data는 모두 leaf node에만 저장하도록 구헌된 자료구조입니다. 또한, leaf node들끼리는 연결리스트 형태로 연결되어 있어 탐색에 우수한 성능을 보입니다.

## DBMS index의 자료구조로 해시 테이블이 아닌 B+Tree를 사용하는 이유에 대해 설명해 주세요.

해시 테이블은 동등 연산에는 특화되었지만 부등호와 같은 연속적인 데이터를 위한 순차 검색이 불가능하다는 점에서 적합하지 않습니다.
