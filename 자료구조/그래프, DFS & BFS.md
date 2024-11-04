## 01. 그래프란
<img width="795" alt="image" src="https://github.com/user-attachments/assets/ef61a876-cabd-4016-8f19-92e867c2cab2"> 
<br>
- 노드와 엣지로 이루어져 각 노드들이 연결되어 시각적으로 나타낼 수 있으며, 해당 노드 간의 관계를 설명하는 자료구조

## 02. 그래프 종류
### 방향성 / 무방향성
<img width="731" alt="image" src="https://github.com/user-attachments/assets/e2940b3f-92fe-44a3-98f4-e4189c8d0a90">   
<br>
- 방향성 : 첫 노드부터 끝 노드까지 일련의 일관된 방향이 있는 경우 방향성으로 판단하며, 
내부의 노드간 순환의 방향이 한 쪽으로 순환하는지, 양쪽으로 순환하는지에 따라 일방향성과 양방향성으로 나뉜다. <br>
- 무방향성 : 노드 간 연결이 방향성을 띄지 않고, 연결되어 있는 모든 노드가 서로 상호 관계를 갖는다.

### 순환 / 비순환
<img width="788" alt="image" src="https://github.com/user-attachments/assets/e80e3282-5d54-4332-a9ed-cfc44f120495">  
<br>
- 순환의 여부로 그래프를 분류하는 방법 <br>
- 기본적으로 무방향성은 모든 엣지가 양방향을 취하므로 순환 그래프에 속한다. <br>
- 순환 : 내부에서 방문했던 노드를 닷방문하게 되는 경우가 발생 <br>
- 비순환 : 내부에서 재방문이 진행안되는 방향으로 구성됨 <br>

### 가중 그래프
<img width="731" alt="image" src="https://github.com/user-attachments/assets/1854ae05-70a0-4ad8-8750-f4f9dab83b71">   
<br>
- 각각의 엣지에 가중치가 있는 형태 <br>
- 일반적으로 가중 그래프가 아닌 경우는 가중치를 1로 생각하면 되며, 가중 그래프와 같이 각각의 엣지에 가중치가 있는 경우, <br>
해당 가중치를 최대/최소화 하는 결과를 내는 경로를 탐색한다. <br>

## 03. 그래프의 활용방법
그래프를 자료구조로써 이용하기 위해 활용하는 방법은 대표적으로 인접행렬과 인접리스트가 있다.

### 인접행렬 (Adjacency matrix)
<img width="802" alt="image" src="https://github.com/user-attachments/assets/cb882efc-3489-4291-a813-2b9fd6ade929">  
<br>
- 무방향 그래프와 인접 행렬 <br>
- 각각의 정점의 엣지가 있을 경우 이를 표시하는 방법이며, 자기 자신은 항상 0으로 두어 대각 행렬을 0으로 유지한다.

<br>
<img width="789" alt="image" src="https://github.com/user-attachments/assets/66f91a9a-a473-4592-b293-4864f2805d85">   
<br>
- 양방향 순환 그래프와 인접 행렬 <br>
- 무방향에서는 엣지로 연결된 두 노드가 모두 상호작용하므로 행과 열이 서로 대칭되어 있는 반면, 방향 그래프에서는 대칭되지 않는다. <br>
- 단, 메모리 사용이 많아 비효율적일 수 있다. <br>
- 노드의 위치를 행렬상으로 파악하고 있기 때문에 탐색의 시간복잡도가 O(1)으로 빠르다. <br>
- 탐색과 달리 노드의 연관성 있는 노드를 모두 찾는 것은 O(n)이다.

### 인접리스트 (Adjacency List)
<img width="803" alt="image" src="https://github.com/user-attachments/assets/d1f50495-caff-4ed5-bc51-a24e64b9627e">   
<br>
- 그래프의 노드 간 연결된 엣지를 바탕으로 관계를 파악해 연결리스트로 연결하는 방식이다. <br>
<br>

<img width="797" alt="image" src="https://github.com/user-attachments/assets/42d52592-ce06-4474-b78d-76b21c730807">   
<br>
- 인접 행렬에 비해 메모리의 사용이 적다. 필요한 엣지의 개수만큼만 사용하기 때문이다.

## 04. 그래프의 순회
- 그래프 및 트리 구조에서 노드를 한번씩 탐색하는 개념 <br>
- 모든 또는 특정 노드를 방문하는 방법을 찾는 것으로, 노드 간의 연결 사이에서 한번씩 방문하는 방법 <br>
- 트리 구조의 전위순회, 중위순회, 후위순회 개념을 기초로 한다. <br>
- (트리 구조는 그래프 중 방향성 비 순회 그래프 - DAG, Directed Acycling Graph에 속한다)

## 05, 깊이 우선 탐색 - DFS
<img width="572" alt="image" src="https://github.com/user-attachments/assets/8b85a19a-9879-4c63-b99f-3a1f02980240">   
<br>
- 트리 그래프의 왼쪽부터 가면서 전위순회로 진행된다. <br>
- A-B-C-D-E-F-G-H-I <br>
- 루트노드 - 좌측노드 - 우측노드 순으로 더 깊은 계층을 우선 탐색하는 방식 <br>
- 재귀적인 방법을 사용해 시간이 오래 걸릴 수 있지만, 재귀호출하며 저장된 메모리를 삭제하기 때문에 공간복잡도는 BFS보다 좋은 편이다.

### 재귀
<img width="643" alt="image" src="https://github.com/user-attachments/assets/2e196b05-09ab-473f-9fef-4ccc14daa40c">   
<br>

### 스택
<img width="653" alt="image" src="https://github.com/user-attachments/assets/4423ef71-1ee9-4189-bddf-5d6a18572cdc">   
<br>

## 05. 너비 우선 탐색 - BFS
<img width="493" alt="image" src="https://github.com/user-attachments/assets/13f9b4fd-f0ec-40d9-820d-9df72b0462c1">   
<br>
- 같은 계층부터 탐색한 후, 각각의 계층의 노드에 인접한 노드를 탐색한다. <br>
- A-B-G-C-F-H-I-D-E순 <br>
- 루트노드, 좌측노드, 모든 형제노드, 맨 처음 형제노드의 좌측노드 순으로 탐색 진행한다. <br>
- 같은 계층을 먼저 탐색하다보니 형제노드들의 인접노드에 대한 정보들이 계속 Queue에 담겨있게 되어 메모리 효율이 떨어진다. <br>
<img width="668" alt="image" src="https://github.com/user-attachments/assets/b3622659-2a7e-47d3-8ac5-5250494540ee">   
