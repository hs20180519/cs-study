# 01. 직렬화란
직렬화(Serialization)는 자바에서 객체를 네트워크로 전송하거나 파일에 저장할 수 있는 형식으로 변환하는 과정 


직렬화된 객체는 바이트 스트림으로 변환되며, 이를 통해 객체의 상태(필드 값)가 유지된다.


변환된 바이트 스트림은 저장 또는 전송된 이후 역직렬화(Deserialization)를 통해 원래의 객체로 복원될 수 있다.


- 운영체제마다 다른 가상 메모리 주소 공간을 갖기 때문에 Reference Type의 데이터들은 인스턴스를 전달할 수 없다.
- 따라서 주소 값이 아닌 Byte형태로 직렬화된 객체 데이터를 전달해야 한다.
- 직렬화된 데이터들은 모두 Primitive Type(기본형)이 되고, 이는 파일 저장이나 네트워크 전송 시 파싱이 가능한 유의미한 데이터가 된다.
- 따라서 직렬화란, "전송 및 저장이 가능한 데이터로 만들어주는 것"

<img width="401" alt="image" src="https://github.com/user-attachments/assets/9db98ffa-9311-4b55-8bcb-1d68876739f6" />

# 02. 직렬화 조건
- java.io.Serializable 인터페이스 구현으로 직렬화/역직렬화가 가능
- 역직렬화 : 정렬화된 데이터를 받는 쪽에서 다시 객체 데이터로 변환하기 위한 작업
- 직렬화 대상 : 인터페이스 상속 받은 객체, Primitive 타입의 데이터
- Primitive 타입이 아닌 Reference 타입처럼 주소값을 지닌 객체들은 바이트로 변환하기 위해 Serializable 인터페이스를 구현해야 한다.

# 03. 직렬화 필요성
- 네트워크 전송 : 객체를 다른 시스템(클라이언트 -> 서버)으로 전송할 때 객체 상태를 보존하기 위해 직렬화 필요
- 파일 저장 : 객체를 파일에 저장하여 이후 해당 객체를 다시 읽어올 수 있도록 상태 유지
- 캐시 : 객체를 캐시에 저장하고 필요 시 복원하기 위해 사용

# 04. 직렬화 구현
```
import java.io.*;

class Person implements Serializable {
    private static final long serialVersionUID = 1L; // 직렬화 버전 UID

    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + '\'' + ", age=" + age + '}';
    }
}

public class SerializationExample {
    public static void main(String[] args) {
        Person person = new Person("Alice", 25);

        // 직렬화
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person.ser"))) {
        // Person 객체를 직렬화하여 바이트 스트림으로 변환한 결과가 person.ser에 저장됨
        // 이 파일은 사람이 읽을 수 없는 바이너리 데이터
oos.writeObject(person);
            System.out.println("객체가 직렬화되었습니다: " + person);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 역직렬화
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.ser"))) {
            Person deserializedPerson = (Person) ois.readObject();
            System.out.println("객체가 역직렬화되었습니다: " + deserializedPerson);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### 고려해야 할 사항
1. serialVersionUID
   - 직렬화된 객체의 클래스 버전 식별에 사용.
   - 클래스의 구조가 변경되면 UID가 달라져 역직렬화 과정에서 InvalidClassException 발생
   - 명시적으로 UID를 정의하여 버전 간 호환성을 유지하는 것이 좋다.
2. Transient 키워드
   - 직렬화 과정에서 특정 필드를 제외하려면 transient 키워드를 사용할 수 있다. (비밀번호나 민감한 정보)
   - private String username;
   - private transient String password; // 직렬화 제외
3. 직렬화 성능
   - 직렬화는 비용이 큰 작업이기 때문에, 대규모 데이터의 경우 JSON, XML 또는 프로토콜 버퍼와 같은 더 효율적인 대안들을 고려
4. 객체의 깊은 참조
   - 객체 내부에 다른 객체가 포함되어 있을 경우, 해당 객체도 직렬화 가능해야 한다.
   - 그렇지 않으면 NotSerializableException 발생
  
# 05. 직렬화의 단점과 대안
### 단점
1. 속도와 메모리 비용 : 비효율적
2. 클래스 의존성 : 직렬화된 객체를 복원하려면 동일한 클래스가 있어야 하므로 클래스 변경에 취약

### 대안
1. JSON/GSON : 텍스트 기반 직렬화로 가독성과 언어 간 호환성이 뛰어남
2. Protocol Buffers : 구글이 개발한 이진 직렬화 포맷으로 빠르고 효율적
3. XML : 데이터의 계층적 표현을 제공하며, 언어와 플랫폼 간의 호환성이 좋음

# 06. 자주 나오는 질문
### 1. 직렬화란 무엇인가?
직렬화는 자바 객체를 바이트 스트림으로 변환하여 파일에 저장하거나 네트워크를 통해 전송할 수 있도록 하는 과정입니다. 역직렬화는 이 바이트 스트림을 다시 객체로 복원하는 과정입니다.

### 2. Serializable 인터페이스란?
Serializable은 자바에서 객체를 직렬화할 수 있도록 만드는 마커 인터페이스입니다. 이를 구현한 클래스는 직렬화 가능하며, 특별한 메서드를 구현할 필요는 없습니다.

### 3. 직렬화의 단점은?
성능 이슈: 직렬화는 CPU와 메모리 사용량이 높습니다.
클래스 의존성: 직렬화된 데이터를 복원하려면 동일한 클래스가 필요합니다.
보안 문제: 직렬화된 데이터는 쉽게 읽을 수 있어 민감한 정보가 노출될 수 있습니다.

+) 직렬화 vs json


현대 개발에서는 JSON이나 Protocol Buffers 같은 대안이 더 선호되지만, 자바 생태계 내에서 간단한 내부 데이터 전송이나 레거시 시스템 유지보수에서는 직렬화가 여전히 유효합니다. 그러나 새로 설계되는 시스템에서는 JSON, Protocol Buffers, 또는 Avro 같은 현대적인 대안을 선택하는 것이 더 바람직합니다.
