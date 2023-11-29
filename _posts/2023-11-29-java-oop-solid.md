---
title: Java - OOP and SOLID
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

* Java의 객체 지향의 특징과, SOLID에 대하여 공부한 내용이다.

* 우선 자바는 객체지향 언어임을 알아야 한다.

<br>
<hr>

### 절차지향 프로그래밍과 객체지향형 프로그래밍
#### 1. 절자치향 언어 ( POP )
   - **장점** : 
		- 초기 프로그래밍 언어로, 컴퓨터 처리구조와 비슷해 실행속도가 빠르다.
<br>
   - **단점** : 
		- 유지보수가 어렵다
        - 하나가 고장나면 시스템 전체가 고장이난다.
        - 디버깅이 힘들다
        - 순서가 엄격히 정해져있어 비효율적이고 생산성이 하락한다.
        - 과도한 전역변수의 사용으로 값을 잘못 설정하게 될 확률이 높다.
		  
#### 2. 객체지향 언어 ( OOP )
   - **장점** : 
		- 코드 재활용성이 높다
        - 생산성이 높다
        - 모델링이 쉽다 ( 실제모습의 구조가 객체에 녹아들어 있기 때문에 있는 그대로를 구현하기 쉬움 )
        - 유지보수가 좋다
<br>
   - **단점** : 
		- 절차지향 언어를 사용하는 것보다 코드 난이도가 높음
        - 절차지향 언어에 비해 실행 속도가 느림.	   
		
<br>
<br>
<br>

### OOP의 특징
#### 1. 캡슐화
- 실제로 구현한 부분을 외부에 드러나지 않도록 정보를 은닉하는 특징
- 변수와 메서드를 하나로 묶음
- 데이터를 외부에서 직접 접근하지 않고, 함수를 통해서만 접근 ( 변수는 private로 묶고, getter와 setter로 접근 )

<br>

#### 2. 추상화
- 핵심적인 개념과 기능만 추려내어 클래스를 만들고, 자식 클래스들이 상속을 받아 구현하도록 하는 특징
- 공통된 변수나 공통된 기능을 묶을 수 있어 중복된 코드를 만들지 않아도 됨.
- 자바에 추상클래스와 인터페이스가 존재하는 이유.
- 보통 우리는 추상화라는 개념에 대해 '구체적이지 않은' 정도의 의미로 느슨하게 알고만 있다. 하지만 '그래디 부치(Grady Booch)'에 의하면 '추상화란 다른 모든 종류의 객체로부터 식별될 수 있는 객체의 본질적인 특징'이라고 정의한다.
- 즉, 추상 메서드 설계에서 적당한 추상화 레벨을 선택함으로써, 어떠한 행위에 대한 본질적인 정의를 서브 클래스에 전파함으로써 관계를 성립되게 하는 것이다.
	
<br>	

#### 3. 상속
- 부모 클래스에 정의된 변수 및 메서드를 자식 클래스에서 extends로 상속받아 사용하는 것.
-  코드의 재사용으로 시간과 비용을 줄일 수 있음.
	* ex) Animal 이라는 클래스를 Lion이라는 클래스가 extends로 받음.
	
<br>

#### 4. 다형성
- 같은 이름의 함수가 클래스 혹은 객체에 따라 다르게 동작하는 것.
 같은 이름의 함수가 구현체에 따라 다르게 동작하는 것.
- 우리가 같은 Discount라는 인터페이스에 RateDiscount라는 구체 클래스로 생성하느냐, FixDiscount라는 구체 클래스로 생성하느냐에 따라 같은 이름의 메서드더라도 다른 기능을 함.
- 오버로딩, 오버라이딩이 해당 됨.
	
<br>
<br>
<br>

### 객체지향의 5원칙 SOLID
#### 1. 단일 책임 원칙 ( SRP : Single Responsibility Principle )
- 하나의 클래스는 하나의 책임만 가져야 한다.
	```java
	public class Animal {
		private String animal;
		public void bark() {
			if ( animal.equals(“Dog”) ) {
				System.out.println(“Bark !”);
			} else if ( animal.equals(“Cat”) ) {
				System.out.println(“Meow ...”);
			}
		}
	}
	```
- 해당 코드는 Dog와 Cat 두 가지 책임을 가지고 있기 때문에 SRP 원칙에 위반한다.
- 수정을 하면
	```java
	abstract class Animal {
		abstract void bark();
	}
	class Dog extends Animal {
		@Override
		void bark() {
			System.out.println(“Bark !”);
		}
	}
	class Cat extends Animal {
		@Override
		void bark() {
			System.out.println(“Meow ...”);
		}
	}
	```
- 이렇게 하나의 클래스는 하나의 책임만 갖게끔 설계하는 것이 SRP 특징을 잘 살려 설계하는 것이다.

<br>
#### 2. 개방 폐쇄의 원칙 ( OCP : Open/Closed Principle )
- 확장에는 열려(Open) 있으나, 변경에는 닫혀(Closed)있어야 한다.
- 즉, 기존 코드를 변경하지 않으면서 기능을 추가할 수 있게 해줘야 한다.
- 한마디로 추상화 사용을 통한 관계 구축을 권장하는 것을 의미한다.
```java
class Animal {
	String type;
    
    Animal(String type) {
    	this.type = type;
    }
}

// 동물 타입을 받아 각 동물에 맞춰 울음소리를 내게 하는 클래스 모듈
class HelloAnimal {
    void hello(Animal animal) {
        if(animal.type.equals("Cat")) {
            System.out.println("냐옹");
        } else if(animal.type.equals("Dog")) {
            System.out.println("멍멍");
        }
    }
}

public class Main {
    public static void main(String[] args) {
        HelloAnimal hello = new HelloAnimal();
        
        Animal cat = new Animal("Cat");
        Animal dog = new Animal("Dog");

        hello.hello(cat); // 냐옹
        hello.hello(dog); // 멍멍
    }
}
```
- 해당 코드는 OCP 원칙을 위반한다. 왜냐하면, 여기서 새로운 동물의 울음소리를 추가하려면...
```java
class HelloAnimal {
	// 기능을 확장하기 위해서는 클래스 내부 구성을 일일히 수정해야 하는 번거로움이 생긴다.
    void hello(Animal animal) {
        if (animal.type.equals("Cat")) {
            System.out.println("냐옹");
        } else if (animal.type.equals("Dog")) {
            System.out.println("멍멍");
        } else if (animal.type.equals("Sheep")) {
            System.out.println("메에에");
        } else if (animal.type.equals("Lion")) {
            System.out.println("어흥");
        }
        // ...
    }
}
```
- 이렇게 클래스를 수정해야 할 수 밖에 없게 설계하였기 때문이다. 이러면 OCP를 위반하는 것이다.
- 하지만 아래와 같이 코드를 작성하면 OCP를 위반하지 않는다.
```java
// 추상화
abstract class Animal {
    abstract void speak();
}

class Cat extends Animal { // 상속
    void speak() {
        System.out.println("냐옹");
    }
}

class Dog extends Animal { // 상속
    void speak() {
        System.out.println("멍멍");
    }
}

class HelloAnimal {
    void hello(Animal animal) {
        animal.speak();
    }
}

public class Main {
    public static void main(String[] args) {
        HelloAnimal hello = new HelloAnimal();

        Animal cat = new Cat();
        Animal dog = new Dog();

        hello.hello(cat); // 냐옹
        hello.hello(dog); // 멍멍
    }
}
```
- 이러면 동물 울음소리를 추가하고 싶을 때, 아래와 같은 클래스만 새로 생성하면 될 뿐 클래스를 수정할 필요가 없다.
```java
class Lion extends Animal {
    void speak() {
        System.out.println("어흥");
    }
}
```


<br>
#### 3. 리스코프 치환 원칙 ( LSP : Liskov Substitution Principle )

<br>
#### 4. 인터페이스 분리 원칙 ( ISP : Interface Segregation Principle )

<br>
#### 5. 의존 역전 원칙 ( DIP : Dependency Inversion Principle )
	


