# 01. 개요

- **C**: 절차적 프로그래밍 언어로, 시스템 프로그래밍, 임베디드 시스템, 운영체제 개발 등에 사용됨
- **C++**: C를 확장한 객체 지향 프로그래밍(OOP) 언어로, 고성능 애플리케이션, 게임 개발, 대형 소프트웨어 개발 등에 사용됨
- **C#**: 마이크로소프트가 개발한 언어로, .NET 프레임워크를 기반으로 한 객체 지향 언어. 주로 윈도우 애플리케이션, 게임(Unity), 웹 개발 등에 사용됨

# 02. 비교

### 객체 지향 프로그래밍(OOP) 지원 여부

- **C**: 객체 지향 개념이 없음
- **C++**: 클래스, 상속, 다형성, 템플릿 등 객체 지향 기능을 지원
- **C#**: C++보다 더 강력한 객체 지향 언어로, .NET 프레임워크를 활용

### **메모리 관리**

- **C**: `malloc()`과 `free()`를 사용하여 수동 관리
- **C++**: `new`와 `delete` 연산자 및 스마트 포인터(예: `std::shared_ptr`)로 메모리 관리 가능
- **C#**: 가비지 컬렉터(GC)가 자동으로 메모리 해제

### **포인터 사용**

- **C**: 포인터 필수
- **C++**: 포인터 사용 가능하지만, STL의 `vector`나 `shared_ptr`을 활용하면 최소화 가능
- **C#**: 기본적으로 포인터 사용 불가(unsafe 키워드로 사용 가능)

### **플랫폼 독립성**

- **C, C++**: OS 및 하드웨어에 종속적이며, 프로그램을 실행하려면 해당 플랫폼에 맞는 컴파일이 필요
- **C#**: 기본적으로 Windows에서 실행되지만, .NET Core를 이용하면 리눅스 및 Mac에서도 실행 가능

### **라이브러리 및 프레임워크**

- **C**: 표준 라이브러리(C Standard Library)만 제공
- **C++**: 표준 라이브러리 + STL(자료구조 및 알고리즘) 지원
- **C#**: 방대한 .NET 라이브러리 사용 가능

# 03. 정리

- **C**: 시스템 프로그래밍에 적합한 절차적 언어 → 운영체제, 드라이버, 임베디드 개발
- **C++**: 객체 지향 기능을 추가하여 다양한 응용이 가능한 언어 → 게임 개발, 성능이 중요한 애플리케이션
- **C#**: 마이크로소프트 생태계에 최적화된, 강력한 객체 지향 언어 → 웹 어플리케이션, 윈도우 앱, Unity 게임 개발
