
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

### 구현 코드
```java
public class ArrayExample {
    public static void main(String[] args) {
        int[] array = new int[5]; // 크기가 5인 정수형 배열 생성
        // 요소 초기화
        for (int i = 0; i < array.length; i++) {
            array[i] = i + 1; // 1부터 5까지 초기화
        }
        // 요소 출력
        for (int value : array) {
            System.out.println(value);
        }
    }
}
```

## 02. ArrayList

개념
ArrayList는 Java Collections의 일부이며, 크기가 가변적인 배열을 제공한다. 

장접
동적 크기: 크기가 자동으로 조정되며, 필요한 경우 새로운 배열을 생성하여 데이터를 복사한다.
인덱스를 통한 빠른 접근: O(1) 시간 복잡도로 특정 인덱스의 요소에 접근할 수 있다.

단점
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

특징
요소 접근: O(n) 시간 복잡도로 특정 인덱스의 요소에 접근할 수 있다 (노드를 순회해야 하기 때문).
요소 추가/삭제: O(1)로 빠름	

``` java
import java.util.LinkedList;

public class LinkedListExample {
    public static void main(String[] args) {
        LinkedList<Integer> linkedList = new LinkedList<>(); // LinkedList 생성
        // 요소 추가
        for (int i = 1; i <= 5; i++) {
            linkedList.add(i); // 1부터 5까지 추가
        }
        // 요소 출력
        for (int value : linkedList) {
            System.out.println(value);
        }
    }
}

```
