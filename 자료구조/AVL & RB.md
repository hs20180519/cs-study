## 00. **자가 균형 이진 탐색 트리**

**자가 균형 이진 탐색 트리**

이진 탐색 트리에서의 최악의 경우(한쪽으로 치우쳐 길이가 n인 리스트처럼 되는 상황)를 방지하기 위해 삽입과 삭제 시 트리의 균형을 유지하는 트리 구조. 

대표적인 예로 **AVL 트리**와 **레드-블랙 트리**가 있다.


**회전(Rotation)**

자가 균형 트리에서는 삽입이나 삭제 후 트리의 불균형을 해소하기 위해 **회전 작업**을 수행한다. 

회전은 트리의 높이를 낮춰 균형을 유지하게 하며, 단순 회전(오른쪽 회전, 왼쪽 회전)과 이중 회전(왼쪽-오른쪽, 오른쪽-왼쪽 회전)이 있다.


## 01. AVL 트리

### 정의

AVL 트리(Adelson-Velsky and Landis Tree)는 각 노드의 왼쪽 서브트리와 오른쪽 서브트리의 높이 차이가 1 이하로 유지되도록 하는 자가 균형 이진 탐색 트리

### 특징

- 균형을 엄격하게 유지한다(높이 차이 1 이하)
- **검색**
    - 엄격하게 정렬되어 있기 때문에, 검색에 최적화되었다
    - O(log n)
- **삽입,** **삭제**
    - **Rotation**의 영향으로 균형 유지에 드는 비용이 크다
    - O(log n)
- 검색 성능이 중요한 데이터베이스 및 인덱싱 시스템에서 주로 사용
- 회전의 구현이 비교적 복잡하다.(단순회전, 이중회전)

### 회전

이중 회전이 필요한 경우가 있어 회전의 복잡도가 높은 편이다.

- **단일 회전**:
오른쪽 회전(Right Rotation): 왼쪽 서브트리의 높이가 오른쪽보다 높을 때 사용. 트리가 왼쪽으로 치우친 경우 균형을 맞추기 위해 노드를 오른쪽으로 회전.
왼쪽 회전(Left Rotation): 오른쪽 서브트리의 높이가 왼쪽보다 높을 때 사용. 트리가 오른쪽으로 치우친 경우 균형을 맞추기 위해 노드를 왼쪽으로 회전.

- **이중 회전**:
왼쪽-오른쪽 회전(Left-Right Rotation): 노드의 왼쪽 자식이 오른쪽으로 치우친 경우 사용합니다. 먼저 왼쪽 자식 노드를 왼쪽으로 회전한 후, 현재 노드를 오른쪽으로 회전합니다.
오른쪽-왼쪽 회전(Right-Left Rotation): 노드의 오른쪽 자식이 왼쪽으로 치우친 경우 사용합니다. 먼저 오른쪽 자식 노드를 오른쪽으로 회전한 후, 현재 노드를 왼쪽으로 회전합니다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FyJoqj%2FbtqGzXJbFHd%2FQnWfSCLVOzUib1dBT84CA0%2Fimg.png">


### 삽입

```java
class AVLTree {
    private class Node {
        int key, height;
        Node left, right;

        Node(int d) {
            key = d;
            height = 1;
        }
    }

    private Node root;

    private int height(Node N) {
        return (N == null) ? 0 : N.height;
    }

    private int getBalance(Node N) {
        return (N == null) ? 0 : height(N.left) - height(N.right);
    }

    private Node rotateRight(Node y) {
        Node x = y.left;
        Node T2 = x.right;

        x.right = y;
        y.left = T2;

        y.height = Math.max(height(y.left), height(y.right)) + 1;
        x.height = Math.max(height(x.left), height(x.right)) + 1;

        return x;
    }

    private Node rotateLeft(Node x) {
        Node y = x.right;
        Node T2 = y.left;

        y.left = x;
        x.right = T2;

        x.height = Math.max(height(x.left), height(x.right)) + 1;
        y.height = Math.max(height(y.left), height(y.right)) + 1;

        return y;
    }

    public void insert(int key) {
        root = insertRec(root, key);
    }

    private Node insertRec(Node node, int key) {
        if (node == null)
            return new Node(key);

        if (key < node.key)
            node.left = insertRec(node.left, key);
        else if (key > node.key)
            node.right = insertRec(node.right, key);
        else
            return node;

        node.height = 1 + Math.max(height(node.left), height(node.right));
        int balance = getBalance(node);

        if (balance > 1 && key < node.left.key)
            return rotateRight(node);
        if (balance < -1 && key > node.right.key)
            return rotateLeft(node);
        if (balance > 1 && key > node.left.key) {
            node.left = rotateLeft(node.left);
            return rotateRight(node);
        }
        if (balance < -1 && key < node.right.key) {
            node.right = rotateRight(node.right);
            return rotateLeft(node);
        }

        return node;
    }
}

```

### 조회 코드

일반 이진 탐색 트리와 동일하다.

```java
public boolean search(int key) {
    return searchRec(root, key) != null;
}

private Node searchRec(Node node, int key) {
    if (node == null || node.key == key)
        return node;

    if (key < node.key)
        return searchRec(node.left, key);
    else
        return searchRec(node.right, key);
}

```

### 삭제 코드

```java
public void delete(int key) {
    root = deleteRec(root, key);
}

private Node deleteRec(Node root, int key) {
    if (root == null)
        return root;

    if (key < root.key)
        root.left = deleteRec(root.left, key);
    else if (key > root.key)
        root.right = deleteRec(root.right, key);
    else {
        if ((root.left == null) || (root.right == null)) {
            Node temp = (root.left != null) ? root.left : root.right;

            if (temp == null) {
                temp = root;
                root = null;
            } else
                root = temp;
        } else {
            Node temp = minValueNode(root.right);
            root.key = temp.key;
            root.right = deleteRec(root.right, temp.key);
        }
    }

    if (root == null)
        return root;

    root.height = Math.max(height(root.left), height(root.right)) + 1;
    int balance = getBalance(root);

    if (balance > 1 && getBalance(root.left) >= 0)
        return rotateRight(root);
    if (balance > 1 && getBalance(root.left) < 0) {
        root.left = rotateLeft(root.left);
        return rotateRight(root);
    }
    if (balance < -1 && getBalance(root.right) <= 0)
        return rotateLeft(root);
    if (balance < -1 && getBalance(root.right) > 0) {
        root.right = rotateRight(root.right);
        return rotateLeft(root);
    }

    return root;
}

```

## 02. 레드-블랙 트리

### 정의

**레드-블랙 트리(Red-Black Tree)**는 각 노드가 ‘**레드**’ 혹은 ‘**블랙**’으로 색칠된 이진 탐색 트리이다. 

루트 노드부터 리프까지 **블랙 노드의 수가 같도록** 균형을 유지한다.

### 특징

- **검색**
    - AVL 트리에 비해 균형 유지를 덜 엄격하게 하므로, **검색 성능이 AVL 트리보다는 느리다**
    - O(log n)
- **삽입 및 삭제**
    - 트리의 균형을 덜 엄격하게 유지하기 때문에, 연산 속도가 비교적 **빠르다**.
- 회전의 구현이 비교적 단순하다.(단순회전)
- 자바 컬렉션 프레임워크의 `TreeMap`과 `TreeSet`에서 사용된다.
- 삽입과 삭제가 빈번히 일어나면서도 비교적 균형을 유지해야 하는 시스템에서 적합한 자료구조

## 03. 비교

### 장단점

| 트리 종류 | 장점 | 단점 |
| --- | --- | --- |
| AVL 트리 | 검색 연산이 빠르고 균형 유지가 엄격함 | 삽입, 삭제 시 회전이 자주 발생해 연산 비용이 높아질 수 있음 |
| 레드-블랙 트리 | 삽입과 삭제 시 회전이 적고 상대적으로 빠른 삽입, 삭제 가능 | 검색 성능이 AVL 트리보다 약간 낮을 수 있음 |

### 시간복잡도

| 연산 | AVL 트리 | 레드-블랙 트리 |
| --- | --- | --- |
| **삽입** | O(log n) | O(log n) |
| **조회** | O(log n) | O(log n) |
| **삭제** | O(log n) | O(log n) |

## 04. 면접에서 자주 나오는 질문

### 1. **AVL 트리와 레드-블랙 트리의 차이점에 대해 설명해 주세요.**

AVL 트리와 레드-블랙 트리는 모두 자가 균형 이진 탐색 트리로, 삽입 및 삭제 시 트리의 균형을 유지하여 검색 효율을 높이는 구조입니다. 주요 차이점은 균형 유지 방식에 있습니다.

- **AVL 트리**는 각 노드의 왼쪽과 오른쪽 서브트리의 높이 차이를 1 이하로 유지하려고 하며, 이에 따라 엄격하게 균형을 맞춥니다. 이로 인해 검색 속도가 빠르지만, 삽입이나 삭제 시 균형을 유지하기 위해 더 많은 회전이 발생할 수 있습니다.
- **레드-블랙 트리**는 노드의 색상(레드 또는 블랙)을 이용하여 트리의 균형을 맞추며, 모든 경로에서 블랙 노드의 수가 동일하도록 유지합니다. AVL 트리보다는 덜 엄격하게 균형을 유지하여 삽입과 삭제가 더 효율적이지만, 검색 속도는 약간 느릴 수 있습니다.

### 2. **각 트리에서 균형을 맞추기 위한 회전 방법에 대해 설명해 주세요.**

- **AVL 트리**는 불균형 상태가 네 가지 경우로 나뉘며, 상황에 맞춰 **오른쪽 회전, 왼쪽 회전, 왼쪽-오른쪽 회전, 오른쪽-왼쪽 회전**을 사용해 균형을 맞춥니다. 각 불균형 케이스에 따라 적절한 회전을 적용하여 높이 차이를 1 이하로 조정합니다.
- **레드-블랙 트리**에서는 삽입 시 연속적인 레드 노드가 발생하거나 삭제 후 블랙 노드 수가 맞지 않을 때 회전과 색상 변환을 사용하여 균형을 유지합니다. **오른쪽 회전**과 **왼쪽 회전**을 주로 사용하며, 색상 조정과 함께 이루어지기 때문에 연산이 간단해지고, 균형을 유지하는 데 더 적은 회전을 필요로 합니다.

### 3. **레드-블랙 트리가 자바 컬렉션 프레임워크에서 선호되는 이유는 무엇인가요?**

레드-블랙 트리는 삽입 및 삭제 시 적은 회전으로 효율적인 균형 유지가 가능하기 때문에, Java의 `TreeMap`이나 `TreeSet`과 같은 컬렉션 프레임워크에서 주로 사용됩니다. 이 트리는 삽입과 삭제에서 비교적 적은 회전 횟수로 균형을 맞추며, **O(log n)** 시간 복잡도를 보장합니다. AVL 트리보다 균형 조건이 덜 엄격하여 실무에서 삽입, 삭제가 빈번한 상황에서 효율적이기 때문에, 컬렉션 프레임워크에서도 성능이 중요한 부분에 사용됩니다.

### 4. **AVL 트리에서 회전 연산이 빈번하게 발생할 경우의 장단점은 무엇인가요?**

- **장점**: AVL 트리는 항상 트리의 균형을 엄격하게 유지하므로 검색 연산이 빠르고, 최악의 경우에도 O(log n) 시간 복잡도를 보장합니다. 따라서 **검색 연산이 빈번한 경우** AVL 트리가 효율적입니다.
- **단점**: 삽입과 삭제 연산 시 균형을 유지하기 위해 **빈번한 회전**이 발생할 수 있으며, 이로 인해 삽입과 삭제의 비용이 증가할 수 있습니다. 삽입, 삭제가 빈번한 환경에서는 이러한 회전 작업이 성능에 부정적인 영향을 줄 수 있습니다.

### 05. 참고
[이진 탐색 트리와 자가 균형 이진 탐색 트리](https://lgphone.tistory.com/90)

[자료구조 9주차](https://medium.com/@leeminhee3457/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-9%EC%A3%BC%EC%B0%A8-04c51c63b145)

[Red Black tree 그리고 avl tree와의 비교](https://ebongzzang.github.io/algorithm/Red-Black-tree-%EA%B7%B8%EB%A6%AC%EA%B3%A0-AVL-tree%EC%99%80%EC%9D%98-%EB%B9%84%EA%B5%90/)
