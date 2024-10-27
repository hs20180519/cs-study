# 00. 트리
- 요소(node)들의 집합이 서로 간선(edge)를 통해 연결되어 있는,
- 두 노드 사이에 정확히 하나의 경로만 존재하는 **비선형 자료구조**
- 노드 간 부모-자식 관계를 가지고 있는 **계층형 자료구조**

# 01. 기본 용어

<img width="619" alt="image" src="https://github.com/user-attachments/assets/a2f2f63e-c298-4a7c-96ea-74bb7baa4392">

- 루트 노드: 트리의 가장 최상위 노드, 부모 노드가 없는 노드 (A)
- 부모 노드: 노드의 바로 윗 노드, 자식 노드를 가진 노드 (I, J의 부모 노드는 D)
- 자식 노드: 부모 노드의 하위 노드(D의 자식 노드는 I, J)
- 형제 노드: 같은 부모를 가지는 노드(I,J는 형제 노드)
- 리프(leaf) 노드: 자식 노드가 없는 노드(I, J, K, F, G, H)
- 내부 노드: 자식 노드가 한 개 이상인 노드, 리프 노드가 아닌 노드
- 서브 트리(subTree): 트리의 특정 노드와 그 자손 노드
- 깊이(depth): 루트에서 어떤 노드까지의 간선 수(I의 깊이: 3)
- 레벨(level): 트리의 특정 깊이를 가지는 노드의 집합
- 차수(degree): 자식 노드의 개수(D의 차수는 2)
- 계수(order): 자식 노드들 중 최대 개수(차수의 최댓값)

# 특징

- 하나의 루트 노드와 0개 이상의 하위 트리로 구성되어 있음
- **비순환 무향 그래프**
    
    → 그래프와 트리의 차이
    
- 모든 자식 노드는 하나의 부모 노드만을 가지고 있음
- 노드가 n개인 트리는 항상 n-1개의 간선을 가짐

## 트리가 아닌 경우

<img width="343" alt="image (1)" src="https://github.com/user-attachments/assets/8c2eccac-47a0-4786-bfea-890881a4c8ce">

- 루트 노드가 2개임

<img width="195" alt="image (2)" src="https://github.com/user-attachments/assets/91c7867c-aee0-4fcf-8f77-56fe36253242">

- 순환그래프임

# 02. 종류

## 이진 트리(Binary Tree)

<img width="587" alt="image (3)" src="https://github.com/user-attachments/assets/a02e9bd3-acfc-4e54-983e-82a920eba7ab">

| 종류 | 설명 |
| --- | --- |
| Ternary Tree | 부모 노드가 자식 노드를 최대 3개씩 가지고 있는 트리 |
| Binary Tree | 부모 노드가 자식 노드를 최대 2개씩 가지고 있는 트리 |

## 균형(Balance)

<img width="599" alt="image (4)" src="https://github.com/user-attachments/assets/13b4b771-53d9-4b93-ba36-b23226f59db6">

| 종류 | 설명 |
| --- | --- |
| Balanced(균형) | 지나치게 한쪽으로 치우치지 않았다면 Balanced Tree
Left, Right 노드의 개수가 일치해야 할 필요는 없음 |
| Unbalanced(불균형) | 한쪽으로 지나치게 치우쳤다면 Unbalanced Tree |

### **편향 트리**

- 모든 노드들이 자식 노드를 하나만 가짐
- 자식 노드들의 방향에 따라 왼/오른쪽 편향 트리로 구분됨

<img width="752" alt="image (5)" src="https://github.com/user-attachments/assets/d6f9fe88-6a85-4f6b-a69c-f25f387c93ad">

## Binary Search Tree

<img width="762" alt="image (6)" src="https://github.com/user-attachments/assets/4e366a2e-c664-47e2-9329-411562958e3f">
<img width="311" alt="image (7)" src="https://github.com/user-attachments/assets/a03eb71e-5a6a-4582-a065-e4c3c93eaf37">

| 종류 | 설명 |
| --- | --- |
| Binary tree(이진 트리) | 자식 노드가 가지는 숫자의 기준이 없음(대소관계를 고려하지 않음) |
| Binary Search Tree(이진 탐색 트리) | 왼쪽 자식과 그 이하 노드들의 데이터 < 부모 노드 데이터 < 
오른쪽 자식과 그 이하 노드들의 데이터 |
| Complete Binary Tree(완전 이진 트리) | 마지막 레벨을 제외한 모든 노드가 채워져 있어야 하며,
노드는 왼쪽에서 오른쪽 방향으로 채워져야 함 |
| Full Binary Tree(정이진트리) | 모든 노드의 자식 노드가 2개이거나 0개 |
| Perfect Binary Tree(포화 이진 트리) | 모든 노드의 자식 노드가 2개이고, 리프 노드가 모두 같은 레벨
높이가 h인 포화이진트리의 노드 개수는 2^(k+1)-1 |

## 다원 탐색 트리(Multiway Search Tree)

- 최대 m개의 서브트리(자식 노드)를 갖는 탐색 트리
- 이진 탐색 트리의 확장된 형태
- 트리의 높이를 줄이기 위해 사용

## 균형 트리(Balanced Tree, B-Tree)

- 다원 탐색 트리에서 높이 균형을 유지하는 트리

# 03. 이진 트리의 순회

<img width="624" alt="image (8)" src="https://github.com/user-attachments/assets/c4bd8cc5-73f7-475b-8238-06b5c2e6bf2d">

- root를 방문하는 순서가 언제인지(전, 중, 후)로 구분하면 외우기 편함

## Appendix. code

```java
public class Tree<T> {
		private Node<T> root;
		
		public Tree(T data) {
				root = new Node<T>();
				root.data = data;
				root.children = new ArrayList<Node<T>>();
		}
		
		public static class Node<T> {
				private T data;
				private Node<T> parent;
				private List<Node<T>> children;
		}
}
```

# 04. 자주 나오는 질문

## 트리가 무엇인지 설명해 주세요.

트리는 계층적 비선형 자료구조로, 간선을 통해 노드가 연결되어 있는 형태로 구성되어 있습니다. 트리의 가장 상위 노드는 루트 노드라고 불리며, 각 노드는 자식 노드를 가질 수 있고 자식은 하나의 부모 노드만을 가질 수 있습니다. 하나의 루트 노드와 0개 이상의 하위 트리로 이루어져 있습니다.

## 트리 순회 방법에 대해 설명해 주세요.

트리 순회 방법에는 전위 순회, 중위 순회, 후위 순회가 있습니다.

전위 순회는 루트 노드, 왼쪽 자식 노드, 오른쪽 자식 노드 순으로 순회하는 방식입니다.

중위 순회는 왼쪽 자식 노드, 루트 노드, 오른쪽 자식 노드 순으로 순회하는 방식입니다.

후위 순회는 왼쪽 자식 노드, 오른쪽 자식 노드, 루트 노드 순으로 순회하는 방식입니다.

## 트리와 그래프의 차이에 대해 설명해 주세요.

그래프는 트리보다 더 포괄적인 개념입니다. 트리는 그래프이지만, 그래프는 트리가 아닙니다. 그래프는 각 요소를 나타내는 노드와 노드 사이의 관계를 나타내는 간선으로 나타낼 수 있고, 사이클이 존재할 수 있습니다.

트리는 그래프와 동일하지만, 사이클이 없습니다. 또한 노드 간에 부모 자식 관계가 존재하는 계층적인 구조를 가지고 있습니다.

# 참고자료

https://yunamom.tistory.com/250?category=1001498
