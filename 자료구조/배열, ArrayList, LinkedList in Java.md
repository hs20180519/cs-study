# 01. Array, ArrayList, LinkedList 구현
### Array
연속된 메모리 공간에 할당

### ArrayList

**(1) 배열로 생성**

<img src="https://github.com/user-attachments/assets/784577ec-dd42-4032-bf44-22ab0a8077c8" width="600" />

<br>
**(2) 사이즈 초과시 자동 grow**

<img src="https://github.com/user-attachments/assets/b412af1e-4ab6-4e72-b500-c0b889dfae56" width="600" />

<br>
**(3) grow** 

<img src="https://github.com/user-attachments/assets/784577ec-dd42-4032-bf44-22ab0a8077c8" width="600" />

새로운 Object[] 배열을 생성하고 기존 요소를 복사하는 방식으로 크기를 동적으로 조정


### LinkedList

LinkedList는 ArrayList와 달리 배열이 아닌 **노드(Node)** 를 기반으로 구현되어 있다. 
각 노드는 데이터와 함께 이전 노드와 다음 노드에 대한 참조를 가지고 있어, **양방향 연결 리스트(Double Linked List)** 형태로 구성된다

따라서 ArrayList는 조회에 유리하고, LinkedList는 삽입/삭제 작업에 유리한 구조로 설계되어 있다.


| 자료 구조	조회 | (Access) |	삽입 (Insert) |	삭제 (Delete) |
|- |- |- |- |
| Array |	O(1) |	O(N) (중간에 삽입) |	O(N) (중간에 삭제) |
| ArrayList |	O(1) |	O(N) (중간에 삽입) |	O(N) (중간에 삭제) |
| LinkedList |	O(N) |	O(1) (앞/뒤에서) |	O(1) (앞/뒤에서) |


``` java

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Random;

public class ListOperationTest {
    public static void main(String[] args) {
        int N = 100000; // 테스트할 요소 수
        Integer[] array = new Integer[N];
        ArrayList<Integer> arrayList = new ArrayList<>(N);
        LinkedList<Integer> linkedList = new LinkedList<>();

        Random random = new Random();
        
        // Array 초기화
        for (int i = 0; i < N; i++) {
            array[i] = i;
            arrayList.add(i);
            linkedList.add(i);
        }
        
        // 조회 테스트
        System.out.println("=== 조회 시간 측정 ===");
        
        long startTime = System.nanoTime();
        for (int i = 0; i < N; i++) {
            Integer value = array[i];
        }
        long endTime = System.nanoTime();
        System.out.println("Array 조회 시간: " + (endTime - startTime) + " ns");

        startTime = System.nanoTime();
        for (int i = 0; i < N; i++) {
            Integer value = arrayList.get(i);
        }
        endTime = System.nanoTime();
        System.out.println("ArrayList 조회 시간: " + (endTime - startTime) + " ns");

        startTime = System.nanoTime();
        for (int i = 0; i < N; i++) {
            Integer value = linkedList.get(i);
        }
        endTime = System.nanoTime();
        System.out.println("LinkedList 조회 시간: " + (endTime - startTime) + " ns");

        // 중간 삽입 테스트
        System.out.println("\n=== 중간 삽입 시간 측정 ===");

        startTime = System.nanoTime();
        arrayList.add(N / 2, random.nextInt());
        endTime = System.nanoTime();
        System.out.println("ArrayList 중간 삽입 시간: " + (endTime - startTime) + " ns");

        startTime = System.nanoTime();
        linkedList.add(N / 2, random.nextInt());
        endTime = System.nanoTime();
        System.out.println("LinkedList 중간 삽입 시간: " + (endTime - startTime) + " ns");

        // 중간 삭제 테스트
        System.out.println("\n=== 중간 삭제 시간 측정 ===");

        startTime = System.nanoTime();
        arrayList.remove(N / 2);
        endTime = System.nanoTime();
        System.out.println("ArrayList 중간 삭제 시간: " + (endTime - startTime) + " ns");

        startTime = System.nanoTime();
        linkedList.remove(N / 2);
        endTime = System.nanoTime();
        System.out.println("LinkedList 중간 삭제 시간: " + (endTime - startTime) + " ns");
    }
}

```


# 02. 정렬되지 않은 자료의 순차탐색 vs 정렬된 자료의 순차탐색

결론부터 말하면, 시간복잡도는 같지만 캐시 적중 및 프리패치 매커니즘의 영향으로 유의미한 차이가 존재한다(정렬된 배열이 빠르다).
\
``` java

import java.util.*;

public class ListAccessTest {
	public static void main(String[] args) {
		int N = 10000; // 데이터 크기 설정 
		int[] sortedArray = new int[N];
		int[] unsortedArray = new int[N];
		ArrayList<Integer> sortedArrayList = new ArrayList<>(N);
		ArrayList<Integer> unsortedArrayList = new ArrayList<>(N);
		LinkedList<Integer> sortedLinkedList = new LinkedList<>();
		LinkedList<Integer> unsortedLinkedList = new LinkedList<>();

		// 데이터 초기화 (정렬된 경우와 무작위 경우)
		for (int i = 0; i < N; i++) {
			sortedArray[i] = i;
			sortedArrayList.add(i);
			sortedLinkedList.add(i);
			
			int randomInt = (int)(Math.random() * N);
			unsortedArray[i] = randomInt;
			unsortedArrayList.add(randomInt);
			unsortedLinkedList.add(randomInt);
		}
		// 비정렬 배열 조회 시간 측정
		long startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = unsortedArray[i];
		}
		long endTime = System.nanoTime();
		System.out.println("비정렬 배열 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 배열 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = sortedArray[i];
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 배열 조회 시간: " + (endTime - startTime) + " ns");

		// 비정렬 ArrayList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = unsortedArrayList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("비정렬 ArrayList 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 ArrayList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = sortedArrayList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 ArrayList 조회 시간: " + (endTime - startTime) + " ns");

		// 비정렬 LinkedList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = unsortedLinkedList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("비정렬 LinkedList 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 LinkedList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			int value = sortedLinkedList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 LinkedList 조회 시간: " + (endTime - startTime) + " ns");

	}
}

```
<img src="https://github.com/user-attachments/assets/8a365098-dd51-4e29-a051-5763d0eab889" width="600" />


**(1) 캐시 적중률(Cache Hit Rate)** 

정렬된 데이터는 메모리 상에서 연속적인 위치에 배치될 가능성이 높다.

예를 들어, 정렬된 배열은 메모리의 연속된 공간에 저장되므로, CPU 캐시에 로드된 데이터가 효율적으로 재사용될 수 있다.

반면, 비정렬된 데이터는 메모리 상에서 무작위 위치에 저장되므로, 데이터를 조회할 때 CPU가 캐시에 없는 데이터를 계속해서 불러와야 할 가능성이 높다(Cache Miss). 

**(2) CPU의 프리패치(Pre-fetching)**

CPU는 데이터 접근이 예상되는 연속적인 메모리 위치를 미리 가져오는 "프리패치" 기능을 사용하여 성능을 최적화한다.

정렬된 데이터는 이러한 프리페치의 영향으로 빠르게 데이터를 조회할 수 있다.

**(3) 분기 예측(Branch Prediction)**

비정렬된 데이터의 경우, 특정 조건에 따라 데이터 조회 흐름이 예측되지 않기 때문에 분기 예측 실패(branch misprediction)가 발생할 가능성이 높다.

* 분기 예측(Branch Prediction) : CPU에서 사용되는 기술. 프로그램의 흐름에서 분기 명령(예: if, switch, for 등)을 처리할 때 어떤 경로가 선택될지를 미리 예측하여 실행 성능을 향상시키는 방법


### 정렬된 데이터가 메모리 상에서 연속적인 위치에 배치될 가능성이 높은 이유

**(1) 정렬 알고리즘의 특성**

대부분의 정렬 알고리즘(예: 퀵소트, 머지소트 등)은 데이터를 비교하고 교환하는 방식으로 작동한다. 
이러한 과정에서 배열의 요소들이 서로 가까운 위치에 교환되거나, 이미 정렬된 구간을 기준으로 새로 추가된 데이터가 그 주변에 배치되게 된다. 
결과적으로, 정렬된 데이터는 자연스럽게 연속적인 메모리 공간에 위치하게 된다.

**(2) 캐시 지역성(Locality of Reference)**

CPU 캐시의 효율성을 고려할 때, 정렬된 데이터는 메모리 접근 패턴에서 근접성을 유지하게 된다. 
따라서 데이터가 연속적으로 저장되어 있으면 CPU가 다음에 접근할 데이터를 예측하기 쉬워져 캐시의 성능이 극대화된다. 이러한 캐시 지역성이 정렬된 데이터를 더욱 연속적으로 메모리에서 배치하도록 유도한다.


### ArrayList가 내부적으로 Array라면, 이 상황에서 조회성능에 차이가 나는 이유는 뭘까?

**(1) Integer 참조(포인터) 타입**

ArrayList는 Object[] 배열을 통해 데이터를 관리하기 때문에, 배열의 각 요소가 객체(Object) 참조를 가리키는 포인터이다.

반면, int[] 같은 기본 타입 배열은 실제 값들이 배열에 연속적으로 저장되므로, 메모리 접근이 더 빠르다.

**(2) 자동 박싱/언박싱(Auto-boxing/Unboxing)**

ArrayList<Integer> 같은 경우, 기본 타입인 int를 다룰 때마다 자동으로 Integer 객체로 변환(박싱)이 발생한다. 

반대로 조회 시 Integer 객체가 int로 변환(언박싱)됩니다.

이러한 박싱/언박싱에서의 오버헤드로 인해, 기본 타입 배열(int[])의 조회가 ArrayList<Integer>보다 빠르다

**(3) 추가적인 메서드 호출로 인한 오버헤드**

ArrayList의 get(i) 메서드를 호출할 때마다 인덱스 범위 검사가 실행되는데, 이것이 오버헤드로 작용할 수 있다.

반면, Array는 배열 인덱스로 직접 접근하므로 범위 검사가 따로 수행되지 않아서 더 빠르다.

``` java

import java.util.*;

public class ListAccessTest {
	public static void main(String[] args) {
		int N = 10000; // 데이터 크기 설정 
		Integer[] sortedArray = new Integer[N];
		Integer[] unsortedArray = new Integer[N];
		ArrayList<Integer> sortedArrayList = new ArrayList<>(N);
		ArrayList<Integer> unsortedArrayList = new ArrayList<>(N);
		LinkedList<Integer> sortedLinkedList = new LinkedList<>();
		LinkedList<Integer> unsortedLinkedList = new LinkedList<>();

		// 데이터 초기화 (정렬된 경우와 무작위 경우)
		for (int i = 0; i < N; i++) {
			sortedArray[i] = i;
			sortedArrayList.add(i);
			sortedLinkedList.add(i);
			
			Integer randomInt = (int)(Math.random() * N);
			unsortedArray[i] = randomInt;
			unsortedArrayList.add(randomInt);
			unsortedLinkedList.add(randomInt);
		}
		// 비정렬 배열 조회 시간 측정
		long startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = unsortedArray[i];
		}
		long endTime = System.nanoTime();
		System.out.println("비정렬 배열 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 배열 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = sortedArray[i];
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 배열 조회 시간: " + (endTime - startTime) + " ns");

		// 비정렬 ArrayList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = unsortedArrayList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("비정렬 ArrayList 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 ArrayList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = sortedArrayList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 ArrayList 조회 시간: " + (endTime - startTime) + " ns");

		// 비정렬 LinkedList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = unsortedLinkedList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("비정렬 LinkedList 조회 시간: " + (endTime - startTime) + " ns");

		// 정렬된 LinkedList 조회 시간 측정
		startTime = System.nanoTime();
		for (int i = 0; i < N; i++) {
			Integer value = sortedLinkedList.get(i);
		}
		endTime = System.nanoTime();
		System.out.println("정렬된 LinkedList 조회 시간: " + (endTime - startTime) + " ns");

	}
}

```

<img src="https://github.com/user-attachments/assets/8a365098-dd51-4e29-a051-5763d0eab889" width="600" />



# 03. 배열 횡우선검색 vs 열우선검색
   

``` java

public class ArrayTest {

	static int N = 10000;

	public static void main(String[] args) {
		System.out.println("=== 행우선순서 ===");
		long result = averageRow(new int[N][N]);
		System.out.println("행 우선 탐색 순서 결과 : " + result + " ns");
		System.out.println("=== 열우선순서 ===");
		result = averageCol(new int[N][N]);
		System.out.println("열 우선 탐색 순서 결과 : " + result + " ns");
	}

	// 행 우선 순서
	private static long averageRow(int[][] mat) {
		long startTime = System.nanoTime();

		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				int value = mat[i][j];
			}
		}

		long endTime = System.nanoTime();
		return endTime - startTime;
	}

	// 열 우선 순서
	private static long averageCol(int[][] mat) {
		long startTime = System.nanoTime();

		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				int value = mat[j][i];
			}
		}

		long endTime = System.nanoTime();
		return endTime - startTime;
	}
}

```

<img src="https://github.com/user-attachments/assets/db0b8e73-8fb7-42a7-af70-e46cd94f81a7" width="600" />


### 캐시 적중률로 인한 오버헤드 감소

시간 복잡도는 행 우선 탐색과 열 우선 탐색 모두 동일하게 O(N×M)이다. 
하지만 실제 실행 시간에서는 메모리 캐시 구조의 영향으로 행 우선 탐색이 더 빠르게 실행될 가능성이 높다

**1. 행 우선 탐색 (Row-major order)**

JAVA 기준, 이차원 배열은 메모리 상에서 행(row) 단위로 연속적으로 저장된다. 

즉, 배열이 메모리에 배치될 때 [0][0], [0][1], [0][2] … [1][0], [1][1] 같은 순서로 저장된다.

따라서 행 우선 탐색은 연속된 메모리 영역에 접근하기 때문에 캐시 적중률이 높다. 이는 캐시의 공간 지역성을 활용하여 더 빠르게 데이터를 가져올 수 있게 한다.

**2. 열 우선 탐색 (Column-major order)**

반면에 열 우선 탐색은 각 열을 먼저 순회하므로 메모리 상에서 불연속적인 주소에 접근하게 된다. 즉, [0][0], [1][0], [2][0] 같은 순서로 접근한다.

이때는 캐시 적중률이 낮아지고, 캐시 미스가 발생하는 경우가 많아지기 때문에 실질적인 실행 시간이 느려질 수 있다.

# 04. 참고
https://huilife.tistory.com/15
