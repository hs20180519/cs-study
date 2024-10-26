
## 01. 배열 (Array)

### 개념
- 배열은 같은 타입의 데이터 요소들이 **연속적으로 메모리에 저장**되는 자료구조이다.
- 각 요소는 **인덱스**를 통해 직접 접근이 가능하다.

### 특징
-  O(1) 시간 복잡도로 특정 인덱스의 요소에 접근할 수 있다.
- 배열 요소들은 연속적인 메모리 공간에 저장되어 캐시 효율성이 좋다.
- 고정된 크기를 가지며, 크기 변경이 어렵다.
  

### 장단점
| 장점                               | 단점                             |
|----------------------------------|---------------------------------|
| 빠른 접근 속도 (O(1))            | 크기 고정 (동적 크기 변경 불가) |
| 메모리 연속성으로 인한 캐시 효율성 | 요소 추가/삭제 시 비용이 큼     |


## 02. ArrayList

### 개념
ArrayList는 Java Collections의 일부이며, 크기가 가변적인 배열을 제공한다. 

### 장점
동적 크기: 크기가 자동으로 조정되며, 필요한 경우 새로운 배열을 생성하여 데이터를 복사한다.
인덱스를 통한 빠른 접근: O(1) 시간 복잡도로 특정 인덱스의 요소에 접근할 수 있다.

### 단점
요소 추가/삭제: 배열의 크기를 조정하는 과정이 필요하기 때문에 O(n) 시간이 소요될 수 있다.

``` java
import java.util.ArrayList;

public class ArrayListExample {
    public static void main(String[] args) {
        ArrayList<Integer> arrayList = new ArrayList<>(); // ArrayList 생성
        // 요소 추가
        for (int i = 1; i <= 5; i++) {
            arrayList.add(i); // 1부터 5까지 추가
        }
        // 요소 출력
        for (int value : arrayList) {
            System.out.println(value);
        }
    }
}
```

## 03. LinkedList

LinkedList는 각 요소가 노드로 구성되며, 각 노드는 데이터와 다음 노드에 대한 **포인터**를 포함하는 형태의 자료구조이다.

### 특징
요소 접근: O(n)index 접근 방식이 아니라, 노드를 순회해야 하기 때문에 검색속도는 느리다

요소 추가/삭제: O(1) 배열이 모든 데이터를 한칸씩 이동시켜야하는 것과 다르게, 포인터만 변경하면 된다.

메모리 사용이 비효율적일 수 있다. 각 노드는 데이터와 포인터를 저장해야 하므로, 추가적인 메모리를 사용해야 한다.

``` java
class Node {
    int data;       // 노드의 데이터
    Node next;     // 다음 노드를 가리키는 포인터

    public Node(int data) {
        this.data = data;
        this.next = null; // 기본값으로 null
    }
}

class LinkedList {
    Node head; // LinkedList의 첫 번째 노드를 가리킴

    public LinkedList() {
        this.head = null; // 초기에는 빈 리스트
    }

    // 노드 추가 (맨 뒤에)
    public void add(int data) {
        Node newNode = new Node(data);
        if (head == null) { // 리스트가 비어있으면
            head = newNode;
        } else {
            Node current = head;
            while (current.next != null) { // 마지막 노드까지 이동
                current = current.next;
            }
            current.next = newNode; // 새로운 노드를 추가
        }
    }

    // 노드 삭제
    public void delete(int data) {
        if (head == null) return; // 리스트가 비어있으면 종료

        // 삭제할 노드가 헤드인 경우
        if (head.data == data) {
            head = head.next;
            return;
        }

        Node current = head;
        while (current.next != null) {
            if (current.next.data == data) { // 삭제할 노드 발견
                current.next = current.next.next; // 노드 삭제
                return;
            }
            current = current.next;
        }
    }

    // 리스트 출력
    public void printList() {
        Node current = head;
        while (current != null) {
            System.out.print(current.data + " -> ");
            current = current.next;
        }
        System.out.println("null");
    }
}

public class LinkedListExample {
    public static void main(String[] args) {
        LinkedList linkedList = new LinkedList();

        // 노드 추가
        linkedList.add(1);
        linkedList.add(2);
        linkedList.add(3);
        linkedList.add(4);

        // 리스트 출력
        System.out.print("현재 리스트: ");
        linkedList.printList(); // 1 -> 2 -> 3 -> 4 -> null

        // 노드 삭제
        linkedList.delete(3);
        System.out.print("3 삭제 후 리스트: ");
        linkedList.printList(); // 1 -> 2 -> 4 -> null

        // 헤드 노드 삭제
        linkedList.delete(1);
        System.out.print("1 삭제 후 리스트: ");
        linkedList.printList(); // 2 -> 4 -> null
    }
}


```
