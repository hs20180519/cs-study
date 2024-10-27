# 00. 이진 탐색 트리(Binary Search Tree, BST)
- 이진탐색 + 연결 리스트 형태의 자료구조
- 이진 탐색의 효율적인 탐색 성능을 유지하면서도, 빈번한 자료 입력과 삭제가 가능하게끔 고안됨

- 이진 탐색에서 탐색 시간복잡도는 $O(log\ n)$이지만 데이터 삽입/삭제가 불가능
- 연결 리스트에서 데이터 삽입/삭제 시간복잡도는 $O(1)$이나, 탐색 시간복잡도는 $O(n)$

→ 두 개를 합쳐 보자는 아이디어

# 01. 특성

- 각 노드의 왼쪽 서브트리에는 해당 노드의 값보다 작은 값을 가진 노드로 구성
- 각 노드의 오른쪽 서브트리에는 해당 노드의 값보다 큰 값을 가진 노드로 구성
- 왼쪽 서브트리, 오른쪽 서브트리 또한 이진탐색트리
- 이진탐색트리를 순회할 때는 중위순회 방식을 사용
    
    → 트리 내에 있는 모든 값을 정렬된 순서대로 읽을 수 있음
    
- 값이 중복되는 노드가 없음

# 02. 연산

- 검색, 삽입, 삭제, 트리 생성, 트리 삭제

```java
class Node {
		int data;
		Node left;
		Node right;
		
		public Node(int data) {
				this.data = data;
				left = null;
				right = null;
		}
}

class BST {
		Node root;
		
		public BST(Node root) {
				this.root = root;
		}
		
		public void setRoot(int data) {
				root = new Node(data);
		}
}
```

## 검색

- 부모 노드가 왼쪽 자식노드보다 크거나 같고, 오른쪽 자식노드보다 작거나 같다는 점에 기반하여 효율적인 탐색이 가능

### e.g. 10 탐색

<img width="264" alt="image" src="https://github.com/user-attachments/assets/3b2946ad-759e-4503-a360-d2389daf43f4">

1. 루트노드(7)와 비교 → 10보다 작으므로, 왼쪽 서브트리는 고려하지 않음
2. 오른쪽 서브트리의 루트노드(8)와 비교 → 10보다 작으므로, 왼쪽 서브트리 버리기
3. 오른쪽 서브트리의 루트노드(10)와 비교 → 탐색 종료

⇒ 비교하는 값이 7,8,10

- 탐색 연산의 시간복잡도: 트리의 높이가 h일 때, $O(h)$
    
    → 최악의 경우 리프노드까지 탐색해야 하는데, 이 때 h번 비교 연산을 수행하기 때문
    

```java
public boolean find(int data) {
    return findNode(root, data) != null;
}

private Node findNode(Node currentNode, int data) {
    if (currentNode == null) {
        return null;
    } else if (data == currentNode.data) {
        return currentNode;
    } else if (data < currentNode.data) {
        return findNode(currentNode.leftChild, data);
    } else {
        return findNode(currentNode.rightChild, data);
    }
}

```

## 삽입

- 삽입을 위해서는 탐색이 선행되어야 한다.
    1. 같은 데이터를 갖는 노드가 없어야 하고,
    2. 탐색에 실패한 위치가 새로운 노드를 삽입하는 위치가 되기 때문

- 새로운 데이터는 항상 리프 노드에 추가

### e.g. 위 트리에 4 삽입

<img width="265" alt="image (1)" src="https://github.com/user-attachments/assets/3d5d937f-b0db-40a8-890f-91454fa13624">

- 삽입 연산의 시간복잡도: 트리의 높이가 h일 때, $O(h)$
    
    → 삽입할 위치의 리프 노드까지 찾아 내려가는 데 h번 비교 연산이 필요하기 때문
    
    * 삽입 자체의 연산은 시간복잡도가 $O(1)$로, 무시할 만한 수준
    

```java
public void insert(int data) {
    if (root == null) {
        setRoot(data);
    } else {
        insertNode(root, data);
    }
}

private void insertNode(Node currentNode, int data) {
    if (data <= currentNode.data) {
        if (currentNode.leftChild != null) {
            insertNode(currentNode.leftChild, data);
        } else {
            currentNode.leftChild = new Node(data);
        }
    } else {
        if (currentNode.rightChild != null) {
            insertNode(currentNode.rightChild, data);
        } else {
            currentNode.rightChild = new Node(data);
        }
    }
}

// 루트 노드를 설정하는 메서드 예시
private void setRoot(int data) {
    root = new Node(data);
}

```

## 삭제

### case 1. 삭제할 노드에 자식노드가 없는(리프 노드인) 경우

<img width="160" alt="image (2)" src="https://github.com/user-attachments/assets/b1ecfc8f-4363-44a0-a806-a04a6c94dfdc">

- 위 사진에서 42의 경우
- 그냥 삭제하면 됨

### case 2. 삭제할 노드에 자식노드가 하나 있는 경우

![image (3)](https://github.com/user-attachments/assets/9f14f0e9-6e64-47f4-bb26-088eb95d9ba9)

- 위 사진에서 20의 경우
- 해당 노드를 삭제하고, 자식 노드와 부모 노드를 연결

### case 3. 삭제할 노드에 자식노드가 두 개 있는 경우

![image (4)](https://github.com/user-attachments/assets/0be01a0e-1343-4687-9a10-f3a3cf8a96b0)

- 위 사진에서 16의 경우

1. 삭제할 노드의 오른쪽 서브트리에서 가장 작은 값이나, 왼쪽 서브 트리에서 가장 큰 값을 찾기(사진은 오른쪽 서브트리에서 최솟값을 찾음)
2. 찾은 값을 삭제할 노드에 복사
3. 찾은 노드를 삭제
    1. 이 과정은 case 1이나 case 2에 해당
        
        → BST의 구조상 자식 자식노드가 하나이거나 하나도 존재하지 않기 때문
        

- 삭제 연산의 시간복잡도: 트리의 높이가 h이고, 삭제할 노드의 레벨이 d라고 가정했을 때, $O(h)$

# 03. 한계

- BST의 탐색, 삽입, 삭제의 계산복잡도는 모두 $O(h)$ (h: 트리의 높이)
    
    → 트리의 높이에 의해 수행시간이 결정되는 구조
    
- 편향 트리의 경우 시간복잡도가 $O(N)$이 됨 (N: 노드의 개수)
- 이를 개선한 트리가 AVL Tree, Red-Black Tree

# 04. 자주 나오는 질문

## 이진 트리와 이진 탐색 트리에 대해 설명해 주세요.

이진 트리는 자식 노드가 최대 두 개인 노드들로 구성된 트리이고,

이진 탐색 트리는 이진 탐색과 연결 리스트를 결합한 자료구조입니다.

이진 탐색의 효율적인 탐색 성능을 유지하면서, 빈번한 자료 입력과 삭제가 가능하다는 장점이 있습니다.

또한, 이진 탐색 트리는 왼쪽 트리의 모든 값이 부모 노드보다 작고, 오른쪽 트리의 모든 값이 부모 노드보다 크다는 특징이 있습니다.

## BST의 시간 복잡도는 어떻게 되나요?

트리의 높이가 h일 때, 검색, 삭제, 삽입 시간복잡도는 모두 $O(h)$입니다. 최악의 경우(worst case)는 한쪽으로 치우친 편향 트리가 되었을 때로, 노드의 개수가 n일 때 $O(n)$의 시간복잡도를 가집니다.

## BST의 worst case 해결방법에 대해 설명해 주세요.

자가 균형 이진 탐색 트리(Self-balancing BST)는 알고리즘을 사용해 트리의 높이를 가능한 낮게 유지함으로써 균형을 맞출 수 있습니다. 대표적으로 AVL 트리와 Red-Black tree가 있습니다.

# 05. 참고자료

https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst/
