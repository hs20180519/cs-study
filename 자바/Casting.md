# 00. 캐스팅

### 개념

하나의 데이터 타입을 다른 타입으로 바꾸는 것을 타입 변환 혹은 **형변환(캐스팅)**이라고 한다.

```java
int a = 0.1 // O, double -> int 가능
int b = (int) true // X, boolean은 int로 캐스팅 불가
```

### 필요성

1. 서로 다른 데이터 타입 간 변환: 기본형 타입 간 연산이나 값 할당 시 타입을 맞추기 위해 캐스팅이 필요함.
2. 업캐스팅/다운캐스팅: **상속 관계**에서 부모 클래스와 자식 클래스 간 타입 변환을 위해 사용됨.
3. 타입 호환성 해결: 제네릭이나 외부 라이브러리 사용 시 타입 불일치를 해결하기 위해 필요함.
4. **다형성** 지원: 객체 지향에서 다형성을 구현할 때 객체 타입을 적절히 변환해 메서드를 다르게 동작시킬 수 있음.
5. 박싱/언박싱: 기본형과 래퍼 클래스 간 자동 변환을 통해 객체와 기본형 간 값을 주고받을 수 있음.
6. 연산자 우선순위: 서로 다른 타입이 포함된 연산에서 타입을 맞추기 위해 자동으로 캐스팅이 이루어짐.

⇒ 이 때, 상속 관계의 부모 자식 **클래스** 간에 형변환이 가능한데, 이를 **참조형** 캐스팅(업 캐스팅/다운 캐스팅)이라고 함

# 01. Up-casting

= 묵시적 형변환 = Implicit Casting

```java
class Parent {
	String name;
    int age;
}

class Child extends Parent {
	/*
    String name;
    int age;
    */
	int number;
}

Parent p = new Parent(); 
Child c = new Child();

```

```java
Parent p2 = (Parent)c; // 업캐스팅 - 자식에서 부모로
```

- 캐스팅이 자동으로 발생
- 연산자 괄호 생략 가능
    - `Parent p2 = new Child();`  처럼 타입을 따로 명시하지 않아도 됨
- 업캐스팅을 하고 메소드를 실행할때, 만일 자식 클래스에서 오버라이딩한 메서드가 있을 경우, 부모 클래스의 메서드가 아닌 오버라이딩 된 메서드가 실행되게 된다.

### 주의점

1. 업캐스팅 하면 멤버 개수가 제한되어 자식 클래스에만 있는 멤버는 사용할 수 없다.
2. 업캐스팅 했지만 오버라이딩 된 메서드는 자식 클래스의 메서드로 실행이 된다.

# 02. Down-casting

= 명시적 형변환 = Explicit Casting

```java
Child c2 = (Child)p2; // 다운캐스팅 - 부모에서 자식으로
```

- 연산자 괄호 생략 불가능
- 업캐스팅한 객체를 다시 자식 클래스 타입의 객체로 되돌리는데 목적을 둠
- 단순한 업캐스팅의 반대 개념이 아님!

다운캐스팅은 곧 사용할 수 있는 객체 멤버 증가를 의미함

실제 참조변수가 가리키는 객체가 무엇인지 알 수 없기 때문에 안전하지 않음

따라서, 반드시 형변환 괄호를 기재하여 클래스를 명시하는 것

### 주의점

1. 업캐스팅한 객체를 되돌리는 목적이기 때문에, 애초에 생 부모 객체인 경우 다운캐스팅이 불가능하다.
    1. 이 때, 컴파일 에러 X, 런타임 에러 O 이기 때문에 실행 중 프로그램이 죽을 수 있음
2. 형제 클래스 끼리는 서로 캐스팅 불가능하다.
    1. = 같은 부모 클래스를 상속하는 클래스

# 03. instanceof 쓰세요

객체 변수가 어느 클래스 타입인지 판별해 true / false 반환

다만, 참조형 타입에만 사용 가능함

# 04. 문제

```java
class Parent {
	int age;

	Parent() {}

	Parent(int age) {
		this.age = age;
	}

	void printInfo() {
		System.out.println("Parent Call!!!!");
	}
}

class Child extends Parent {
	String name;

	Child() {}

	Child(int age, String name) {
		super(age);
		this.name = name;
	}

	@Override
	void printInfo() {
		System.out.println("Child Call!!!!");
	}

}

public class test {
    public static void main(String[] args) {
        Parent p = new Child();

        p.printInfo(); // 문제1 : 출력 결과는?
        Child c = (Child) new Parent(); //문제2 : 에러 종류는?
    }
}
```

<details>
  <summary>문제1 출력 결과는?</summary>
Child Call!!!!
자바에서는 오버라이딩된 함수를 동적 바인딩하기 때문에, Parent에 담겼어도 Child의 printInfo() 함수를 불러오게 된다.
</details>

<details>
  <summary>문제2 에러 종류는?</summary>
    Runtime Error

컴파일 과정에서는 데이터형의 일치만 따진다. 
프로그래머가 따로 (Child)로 형변환을 해줬기 때문에 컴파일러는 문법이 맞다고 생각해서 넘어간다. 
하지만 런타임 과정에서 Child 클래스에 Parent 클래스를 넣을 수 없다는 것을 알게 되고, 런타임 에러가 나오게 되는것!
  
</details>



### 출처

[https://inpa.tistory.com/entry/JAVA-☕-업캐스팅-다운캐스팅-한방-이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%97%85%EC%BA%90%EC%8A%A4%ED%8C%85-%EB%8B%A4%EC%9A%B4%EC%BA%90%EC%8A%A4%ED%8C%85-%ED%95%9C%EB%B0%A9-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)

https://gyoogle.dev/blog/computer-language/Java/Casting.html
