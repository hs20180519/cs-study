# 01. 객체 지향 설계의 5원칙 S.O.L.I.D
- SRT : Single Responsibility Principle 단일 책임 원칙
- OCP : Open Closed Principle 개방 폐쇄 원칙
- LSP : Listov Substitution Principle 리스코프 치환 법칙
- ISP : Interface Segregation Principle 인터페이스 분리 원칙
- DIP : Dependency Inversion Principle 의존 역전 원칙

- SOLID 설계 원칙은 oop의 4가지 특징(추상화, 상속, 다형성, 캡슐화)와 더불어, 객체 지향 프로그래밍의 단골 면접 질문 중 하나이다.
- 또한 디자인 패턴들이 SOLID 설계 원칙에 입각하여 만들어진 것이기 때문에 탄탄하게 알아볼 필요가 있다.
- SOLID 객체 지향 원칙을 적용하면 코드를 확장하고 유지 보수 관리하기가 더 쉬워지며, 불필요한 복잡성을 제거해 리팩토링에 소요되는 시간을 줄임으로써 개발의 생산성을 높일 수 있다.

- SOLID는 특정 프로그래밍 언어 혹은 프레임워크를 위해 만든 원칙이 아니기 때문에, 자유롭게 적용 가능하다.

## 1. 단일 책임 원칙 - SRP
![image](https://github.com/user-attachments/assets/0bded296-84f1-4449-8f65-485730de6053)

- <b>클래스(객체)는 단 하나의 책임감 가져야 한다</b>
- 책임 = 하나의 기능 담당
- 즉, 하나의 클래스는 하나의 기능을 담당하여 하나의 책임을 수행하는데 집중되도록 클래스를 따로따로 여러 개 설계하라는 원칙
- 만약 하나의 클래스에 기능이 여러 개 있다면, 기능 변경이 일어났을 때 수정해야 할 코드가 많아진다.
- 예를 들어,  A를 고쳤더니 B, C를 수정해야하고, C를 수정했더니 다시 A로 돌아가서 수정해야 하는, 마치 책임이 순환되는 형태가 되어버린다.
- 따라서 SRP 원칙을 따름으로써 한 책임의 변경으로부터 다른 책임의 변경으로의 연쇄작용을 극복할 수 있게 된다.
- 최종적으로 SRP의 목적은 "프로그램의 유지보수성을 높이기 위함" 이다.
- (청소기 클래스는 청소 메소드만 구현. 화분에 물을 주고, 드라이할 필요까지 없다)

### SRP 원칙 위반 예제와 수정하기
![image](https://github.com/user-attachments/assets/2e390130-c4d0-43d7-8c42-ff4fef055ed0)

- 직원 정보를 담당하는 EMployee 클래스에는 4가지의 주요 메서드가 존재
1. saveDababase() : 기술팀에서 변경된 정보를 DB에 저장하는 메서드
2. calculatePay() : 회계팀에서 급여를 계산하는 메서드
3. calculateExtraHour() : ch과 근무 시간을 계산하는 메서드(회계팀과 인사팀에서 공유하여 사용)
4. reportHours : 인사팀에서 근무시간을 계산하는 메서드

- 각 메서드들을 각 팀에서 필요할 때 마다 호출한다고 가정
![image](https://github.com/user-attachments/assets/6f37a837-1e08-41d4-9bc7-b686c2115955)

- calculatePay() 메서드와 reportHours() 메서드에서 초과 근무 시간을 계산하기 위해 calculateExtraHour() 메서드를 공유하여 사용하고 있다.
- 그런데 회계팀에서 급여를 계산하는 기존의 방식을 새로 변경하여, 코드에서 초과 근무 시간을 계산하는 메서드 calculateExtraHour()의 알고리즘 업데이트가 필요해졌다.
- 그래서 개발팀에서 회계팀의 요청에 따라 calculateExtraHour() 메서드를 변경하였는데, 변경에 의한 파급 효과로 인사팀에서 사용하는 repoertHours() 메서드에도 영향을 주게 되어버린다.
- 인사팀에서는 이러한 변경 사실을 알지 못하고, 메서드 반환 결과값 데이터가 잘못되었다며 개발 팀에 새로운 요청을 보내게 될 것이다.
- 바로 이러한 상황이 SRP에 위배되는 상황이다.
- Employee 클래스에서 회계팀, 인사팀, 기술팀 이렇게 3개의 액터에 대한 책임을 한꺼번에 가지고 있기 때문이다.
- (액터 : 시스템을 수행하는 역할을 하는 요소로써, 시스템을 이용하는 사용자, 하드웨어 혹은 외부 시스템이 될 수 있음)

- 실제 프로그래밍을 해보면 이런 실수를 종종 하게된다. 하나의 클래스를 참조하는 모든 연관 관계를 보는 것이 불가능하기 때문이다.
- 따라서, 이런 실수를 아예 만들지 않기 위해, 미리 구조를 세우자는 취지에 SRP 설계 원칙이 등장한 것

![image](https://github.com/user-attachments/assets/572bf5d8-5972-48b1-b034-23660a614edd)

![image](https://github.com/user-attachments/assets/5f901444-e71b-4628-8729-24d523b93604)

![image](https://github.com/user-attachments/assets/b8724875-1c14-4d5c-9bc9-b99d1d4fe92c)

- 각 책임(기능 담당)에 맞게 클래스를 분리하여 구성하면 끝
- 회계팀, 인사팀, 기술팀의 기능 담당은 PayCalculator, HourReporter, EmployeeSaver 각기 클래스로 분리하고, 이를 통합적으로 사용하는 클래스인 EmployeeFacade 클래스를 만든다.
- EmployeeFacade 클래스의 메서드에는 사실 아무런 로직이 없다(생성자로 인스턴스를 생성하고, 각 클래스의 메서드를 사용하는 역할만 함)
- 이렇게 되면 EmployeeFacade 클래스는 어떠한 액터도 담당하지 않게 된다.
- 문제가 생기면 각각의 분리된 클래스에서만 수정하면 된다.

- 전과 동일하게 회계팀에서 초과 근무 시간 계산 방법이 변경되었다고 하면, PayCalculator 클래스의 caculateExtraHour() 메서드를 수정하면 된다. (HourReporter 클래스에는 영향X)
- 또한 클래스를 사용하는 입장에서는 PayCalculator, HourReporter, EmployeeSaver 클래스가 어떤 식으로 구성되어있는지 알 필요 없이, EMployeeFacade 클래스를 불러와 메서드만 사용하면 된다
- 이는 캡슐화(정보 은닉)이 잘 된 것

### Facade 패턴
- Facade : 건물의 정면
- 건물의 뒷부분이 어떻게 생겼는지 보여주지 않고 건물의 정면만 보여줌
- EmployeeFacade 클래스는 메서드의 구현이 어떻게 되어있는지는 보여주지 않고 (뒷부분) 어떤 메서드가 있는지 (건물의 정면)만 보여준다.
- 구체적인 메서드의 구현은 각각 PayCalculator, HourReporter, EmployeeSaver 클래스에 위임하기 떄ㅣ문


## 2. 개방 폐쇄 원칙 - OCP
![image](https://github.com/user-attachments/assets/e96b7ec4-eb17-4808-9128-6559d6416382)

- <b>클래스는 확장에 열려있어야 하며, 수정에는 닫혀있어야 한다</b>
- 기능 추가 요청이 오면 클래스를 확장을 통해 손쉽게 구현하면서, 확장에 따른 클래스 수정은 최소화하도록 프로그램을 작성해야 하는 설계 기법
- [확장에 열려 있다] : 새로운 변경사항이 발생했을 때 유연하게 코드를 추가함으로써 큰 힘을 들이지 않고 애플리케이션의 기능을 확장할 수 있음
- [변경에 닫혀 있다] : 새로운 변경사항이 발생했을 때 객체를 직접적으로 수정을 제한함
- 즉, 추상화 사용을 통한 관계 구축을 권장하는 것
- 다형성과 확장을 가능케하는 객체 지향의 장점을 극대화하는 기본적인 설계 원칙이다.
- (추상 클래스와 상속을 통한 클래스 관계 구축을 의미)


## 3. 리스코프 치환 원칙 - LSP
![image](https://github.com/user-attachments/assets/cc49e9f3-6e5f-4dd8-a5cd-c55ee81e37a8)

- <b>서브타입은 언제나 기반(부모)타입으로 교체할 수 있어야 한다</b>
- 다형성 원리를 이용하기 위한 원칙 개념
- 다형성의 특징을 이용하기 위해 상위 클래스 타입으로 객체를 선언하여 하위 클래스의 인스턴스를 받으면, 업캐스팅된 상태에서 부모의 메서드를 사용해도 동작이 의도대로 흘러가야 하는 것 의미
- 따라서 기본으로 LSP 원칙은 부모 메서드의 오버라이딩을 조심스럽게 따져가며 해야 한다.
- 왜냐하면 부모 클래스와 동일한 수준의 선행 조건을 기대하고 사용하는 프로그램 코드에서 예상치 못한 문제를 일으킬 수 있기 때문이다.

- (자바에서는 Collection 인터페이스가 대표적인 예시)
![image](https://github.com/user-attachments/assets/644c12ff-5ffe-4d17-a636-c15e924ad457)

- 자료형을 LinkedList에서 HashSet으로 변경하여도, add()메서드를 실행하는데 있어 원래 의도대로 작동하기 때문
- 다형성 이용을 위해 부모 타입으로 메서드를 실행해도 의도대로 실행되도록 구성을 해줘야 하는 원칙


## 4. 인터페이스 분리 원칙 - ISP
![image](https://github.com/user-attachments/assets/d2f12cea-ac7a-459e-80af-3840f76b514e)

- <b>인터페이스를 각각 사용에 맞게끔 잘게 분리해야 한다</b>
- SRP가 클래스의 단일 책임을 강조한다면, ISP는 인터페이스의 단일 책임을 강조한다.
- 즉, SRP는 클래스 분리를 통해 이루어진다면, ISP는 인터페이스 분리를 통해 설계하는 원칙
- 클라이언트의 목적과 용도에 적합한 인터페이스만을 제공ㅇ하는 것이 목표
- 다만, 한 번 인터페이스를 분리하여 구성해놓고 나중에 무언가 수정사항이 생겨서 또 인터페이스들을 분리하는 행위를 가하지 말아야 한다.
- 인터페이스는 한 번 구성하였으면 웬만하면 변하면 안되는 개념
- (인터페이스는 제약없이 자유롭게 다중 상속이 가능하기 때문에, 분리할 수 있으면 분리하여 각 클래스 용도에 맞게 implements 하라는 설계 원칙)


### ISP 원칙 위반 예제와 수정하기
![image](https://github.com/user-attachments/assets/5c346235-f984-43ce-8c57-d90abc538649)
- 만약 갤럭시 S20이나 S21 클래스를 구현할 때, 객체의 동작 모두가 필요하므로 ISP 원칙을 만족

![image](https://github.com/user-attachments/assets/a6233a61-2891-4e9e-9f5d-8ac60e02df31)
- 그러나, 최신 스마트폰 이외에 구형 기종 스마트폰 클래스도 다뤄야 할 경우 문제가 발생한다
- 그렇다면 추상 메서드 구현 규칙상 오버라이딩은 하되, 메서드 내부는 빈 공간으로 두거나 혹은 예외를 발생토록 구성해야 한다
- 결국, 필요하지 않은 기능을 어쩔 수 없이 구현해야 하는 낭비가 발생될 것이다.

![image](https://github.com/user-attachments/assets/27d16161-94bd-49c3-80f9-752998a7da27)
- 따라서, 각각의 기능에 맞게 인터페이스를 잘게 분리하도록 구성해야 한다.

![image](https://github.com/user-attachments/assets/0c839280-9839-4341-87b1-97ca16658bb5)
![image](https://github.com/user-attachments/assets/d3171d77-30a4-47c7-9186-332f1d550fac)
![image](https://github.com/user-attachments/assets/cf9a5a14-ec7c-40c4-a813-90aad8bccc8a)


### ISP 원칙 적용 주의점
- SRP와 ISP 원칙 사이의 관계
- 인터페이스의 기능에 대한 책임에 맞게 추상 메소드를 구성
- 책임을 준수하더라도, 실무에서는 ISP가 만족되지 않을 수 있는 케이스 존재

![image](https://github.com/user-attachments/assets/56bbb761-61f8-4f0c-b2ad-645b71eab5f0)

- 게시판 인터페이스 (글쓰기, 읽기, 수정, 삭제)가 있을 때, 이들은 모두 게시판에 필요한 기능들이며 SRP에 위배되지 않는다.
- 하지만, 이를 구현하는 일반 사용자 입장에서는 게시글 강제 삭제 기능은 사용할 수 없기 때문에 결국 ISP 위반이다.
- 따라서, 책임을 잘 구성해놓은 것 같지만 실제 적용되는 객체에겐 부합되지 않을 수 있기 때문에 책임을 더 분리해야 한다.
- -> ISP는 SRP를 만족하면 성립되는가 ? 반드시 그렇지는 않다.


## 5. 의존 역전 원칙 - DIP
![image](https://github.com/user-attachments/assets/f5b90b9f-6034-42eb-b367-67a1b7d7a0f9)

- <b> 어떤 클래스를 참조해서 사용해야하는 상황이 생긴다면 그 클래스를 직접 참조하는 것이 아니라 그 대상의 상위 요소(추상 클래스 or 인터페이스)로 참조하라는 원칙 </b>
- 즉, 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻
- 의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것보다는, 변화하기 어려운 것이나 거의 변화가 없는 것에 의존하라는 것
- (각 클래스 간의 결합도를 낮추는 것이 지향점)

# 02. 자주 나오는 질문
### SOLID에 대해 설명해주세요
1. SRP(Single Responsibility Principle, 단일 책임 원칙) : 하나의 클래스는 하나의 책임만 담당하는 원칙
2. OCP(Open Closed Principle, 개방 폐쇄 원칙) : 클래스는 확장에는 개방되어 있어야 하고, 수정에는 폐쇄되어야 한다는 원칙
3. LSP (Liskov Substitution Principle, 리스코프 치환 원칙) : 하위 타입(자식, 구현 클래스)은 언제나 자신의 상위 타입(부모 클래스, 인터페이스)으로 대체될 수 있어야 한다는 원칙
4. ISP (Interface Segregation Principle, 인터페이스 분리 원칙) : 하나의 일반적인 인터페이스 보다는 구체적인 여러 개의 인터페이스를 만들어야 한다는 원칙
5. DIP (Dependency Inversion Principle, 의존 역전 원칙)- 어떤 클래스를 참조해서 사용해야하는 상황이 생긴다면, 그 클래스를 직접 참조하는 것이 아니라 그 대상의 상위 요소(추상 클래스 or 인터페이스)를 참조하라는 원칙

# 03. 참고
https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID
