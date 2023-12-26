---
title: Important Study - Very Important Transaction
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

<hr>

### Transaction 이란?
 - 데이터베이스에서 수행되는 하나의 논리적인 작업 단위를 의미한다.


<br>
<br>

### ACID
 - 원자성 ( Atomicity )
 
	- 트랜잭션이 속한 하나의 메서드를 하나의 단위로 취급하여, 완전히 Commit 되거나, 완전히 RollBack 되거나 둘 중 하나를 일으킨다.
 
 - 일관성 ( Consistency )

	- 모든 트랜잭션은 일관성 있는 DB 상태를 유지한다. (ex: DB의 무결성 제약 조건 항상 만족)
	
	- 트랜잭션이 시작되기 전과 완료된 후의 데이터베이스 상태는 일관성을 유지해야 한다.
 
 - 고립성 ( Isolation )
 
	- 여러 트랜잭션이 동시에 실행될 때, 각 트랜잭션은 다른 트랜잭션에게 영향을 미치지 않도록 격리되어야 한다.
	
	- 한 트랜잭션이 수행 중인 데이터에 대한 변경 사항은 다른 트랜잭션이 완료되기 전까지는 다른 트랜잭션에게 보이지 않아야 한다.
 
 - 영속성/지속성 ( Durability )
 
	- 트랜잭션이 성공적으로 끝나면 그 결과는 항상 기록되어야 한다.
	
	- 시스템의 장애 또는 기타 문제가 발생하더라도 트랜잭션이 성공적으로 커밋되었으면 그 결과는 지속되어야 한다.
	
<br>
<br>

### 논리적 트랜잭션 & 물리적 트랜잭션
 - 물리적 트랜잭션은 실제로 **DB에서 Commit 되거나 RollBack 되는 트랜잭션**이다.
 
 - 논리적 트랜잭션은 **@Transactional 단위 하나로 스프링이 트랜잭션 매니저를 통해 트랜잭션을 처리하는 단위**이다.
 
 - 논리적 트랜잭션 중 하나라도 문제가 생긴다면, 물리적 트랜잭션은 RollBack 이 일어나고, 논리적 트랜잭션이 전부 정상 작동 되어야지만 물리적 트랜잭션의 Commit 이 일어난다.
 
 - 예시로는 A메서드에 @Transactional 이 걸려있고, 그 안에서 B C 메서드를 호출하고, 이후 B C에서 얻은 결과로 추가 로직을 수행하는 코드가 있는데, B C 메서드에도 @Transactional 이 걸려있다면, A B C 모두 스프링이 트랜잭션 매니저를 통해 트랜잭션을 처리하지만, A B C 총 3개의 논리적 트랜잭션이 동작하는 것이고, 물리적 트랜잭션은 A 하나만 존재한다. 그래서 B 나 C 혹은 B C 이후 로직 중 하나라도 문제가 생긴다면 RollBack 이 일어나고, 전부 문제 없이 동작해야 Commit 이 된다. 그리고 A 없이 단순이 B만 동작한다면, B는 하나의 물리적트랜잭션이자 동시에 논리적트랜잭션이 된다.
 
 - 그걸 조작하기 위해 @Transactional 에는 propagation 옵션이 존재한다.
 
	- **propagation = Propagation.REQUIRED**
	
		- 가장 기본이다. propagation = Propagation.REQUIRED 을 굳이 적지 않아도 되며, 안적으면 이 코드로 Default 동작을 한다.
		
		- 기존 트랜잭션이 없으면 생성하고, 기존 트랜잭션이 있으면 기존 트랜잭션에 참여한다.
 
	- **propagation = Propagation.SUPPORTS**
	
		- 기존 트랜잭션이 있으면 지원하고, 없으면 트랜잭션 없이 진행된다.
		
	- **propagation = Propagation.MANDATORY**
	
		- 기존 트랜잭션이 반드시 있어야 한다.
		
		- 기존 트랜잭션이 없으면 예외가 발생하고, 있으면 기존 트랜잭션에 참여한다.
		
	- **propagation = Propagation.REQUITES_NEW**
	
		- 기존 트랜잭션이 없으면 신규 트랜잭션을 생성한다.
		
		- 기존 트랜잭션이 있으면 그 트랜잭션을 잠시 보류하고, 신규 트랜잭션을 생성한다. ( 하나의 물리적 트랜잭션 안에서 새로운 물리적 트랜잭션을 만드는 것이다. )
		
		- 근데 여기서 중요한건, 자식 트랜잭션에서 문제(예외)가 생기면, 부모 트랜잭션은 롤백을 한다.
		
		- 그러나 부모 트랜잭션이 자식 트랜잭션을 성공시키고, 이후 로직에서 문제가 생긴다면, 그 자식 트랜잭션은 commit 되고, 부모 트랜잭션만 롤백된다. 그 개념을 잘 알아야 한다.
			
		- 그 이유는 예외가 발생하였을 때 catch 등으로 처리해주지 않으면 콜 스택을 하나씩 제거하면서 최초 호출한 곳까지 예외가 전파되기 때문이다.
		
		- 고로 독립적인 물리적 트랜잭션을 새로 생성한다는 의미보다는, 별도의 트랜잭션을 생성한다고 보는게 좋다.
		
		- 대신 try catch 로 예외처리를 해준다면, 자식 트랜잭션만 롤백시키고, 부모 트랜잭션은 정상 작동을 시킬 수 있다.
		
		- Propagation.REQUIRED 랑 다를게 뭐냐? 이 경우 Propagation.REQUIRED 를 썼다면, catch 로 예외설정을 잘 해주어도 부모트랜잭션이 롤백을 해버린다.
	
	- **propagation = Propagation.NOT_SUPPORTS**

		- 트랜잭션을 지원하지 않음.
		
		- 기존 트랜잭션이 없으면 그냥 트랜잭션 없이 진행하고,
		
		- 기존 트랜잭션이 있으면 그 트랜잭션을 보류하고 트랜잭션 없이 진행함.
		
	- **propagation = Propagation.NEVER**
	
		- 트랜잭션을 사용하지 않음
		
		- 기존 트랜잭션이 없으면 트랜잭션 없이 진행하고
		
		- 기존 트랜잭션이 있으면 예외를 발생시킴
		
	- **propagation = Propagation.NESTED**
	
		- 자식 트랜잭션을 만듬
		
		- 기존 트랜잭션이 없으면 새 트랜잭션을 생성함
		
		- 기존 트랜잭션이 있으면 자식 트랜잭션을 만듦.
		
<br>
<br>

### Transaction 과 Proxy

 - @Transactional 이 붙은 메서드가 있다면, 그 클래스는 프록시 객체를 만든다.
 
 - Proxy 패턴이란 대상 객체의 기능을 대신 수행하는 대리 객체를 사용하는 패턴이다.
 
 - 실제 객체가 아닌 임의의 객체를 생성하여 주입하며, 앞이나 뒤에 공통적인 로직을 삽입하고 싶을 때 사용한다.
 
 - JDK Proxy 와 CGLib Proxy 가 있다.
 
	- JDK Proxy 는 Spring AOP Default 프록시 방식이며 인터페이스가 없으면 안됨.
	
	- CGLib Proxy 는 Class의 byte code를 조작하여 프록시 객체를 만들며 Extends 방식으로 구현한다. JDK 방식보다 성능이 좋으며 Spring Boot 의 기본 AOP 전략으로 사용된다.
	
	| 특징               | JDK Proxy                         | CGLib Proxy                         |
	|-------------------|-----------------------------------|-------------------------------------|
	| 프록시 생성 방식     | 인터페이스를 구현한 클래스에 대한 프록시 생성 | Class의 byte code를 조작하여 프록시 객체 생성 (상속) |
	| 인터페이스 필요 여부 | 인터페이스가 있어야 프록시 생성 가능      | 인터페이스 없이도 클래스에 대한 프록시 생성 가능     |
	| 성능                | 일반적으로 상대적으로 덜 성능적인 특징   | 일반적으로 높은 성능을 가짐               |
	| 사용 예시            | Spring AOP의 기본 프록시 방식            | Spring Boot의 기본 AOP 전략, 인터페이스 없이도 프록시 생성 가능 |
	| 제약 사항           | 인터페이스를 구현한 클래스에 대한 프록시 생성 | 상속이 불가능한 final 클래스에 대한 프록시 생성이 어려움 |
	
 <br>
 
 - **@Transactional 과 AOP의 동작**
 
	- 1. target에 대한 호출이 들어오면 AOP proxy가 이를 가로채서(intercept) 가져온다.
	
	- 2. AOP proxy에서 Transaction Advisor가 commit 또는 rollback 등의 트랜잭션 처리를 한다.
	
	- 3. 트랜잭션 처리 외에 다른 부가 기능이 있을 경우 해당 Custom Advisor에서 그 처리를 한다.
	
	- 4. 각 Advisor에서 부가 기능 처리를 마치면 Target Method를 수행한다.
	
	- 5. interceptor chain을 따라 caller에게 결과를 다시 전달한다.
	
 <br>
 
 - **트랜잭션도 AOP를 사용하여 프록시 객체로 만들어 진다.**
 
	- 이는 해당 메서드의 앞 뒤에 startTransaction( );과 endTransaction( ); 를 통해 커밋 혹은 롤백을 수행해주는 기능을 추가한다.
	
	- 그렇게 Proxy 형태로 동작하기 때문에 외부에서 접근 가능한 접근제어자의 메서드만 설정할 수 있다.
	
	- AOP 에도 @Brfore 와 @After 로 특정 메서드의 앞 뒤에 동작하는 메서드를 만들 수 있다. 그러듯 트랜잭션 앞 뒤에 시작점과 끝점을 알 수 있는 메서드를 넣어서 전체 커밋을 하거나, 롤백을 시킬 수 있는 것이다.

<br>
<br>

### Self-Invocation
 - Transaction 사용시 생길 수 있는 문제가 바로 self-invocation 문제이다.
 
 - @Transactional 이 하나라도 있는 클래스는 프록시 객체로 저장이 된다.
 
 - 그러나, 같은 클래스 안에서 메서드를 호출하는 경우, this.Method( ); 이런 식으로 호출하는 격이 되는데, 이러면 프록시 객체의 메서드를 호출하는 것이 아니라, 원본 객체의 메서드를 호출하게 된다
 
 - 그래서 생기는 대표적인 문제 2가지를 아래에서 설명하고자 한다.
 
 - **self-invocation 의 대표적인 2가지 문제**
 
	- **1. 일반 메서드 안에서 같은 클래스의 @Transactional 메서드를 호출했을 경우**
	
		- 일반 메서드 A 안에서 Transactional 이 붙은 B 메서드를 호출하면, 프록시 객체에서 A를 실행하게 되는데, 
		
		- 이 때 A 안에 있는 B는 this.B( ); 로 호출되어 프록시 객체 속의 B 메서드가 아니라, 원본 객체의 B가 호출된다. 
		
		- 그래서 B는 앞 뒤로 startTransaction( ); 과 endTransaction( ); 이 붙지 않으므로 트랜잭션 처리가 되지 않는다.
		
		- startTransaction( ); 과 endTransaction( ); 가 붙는 건 트랜잭션 처리로 인해 만들어진 프록시 객체 내의 메서드에서만 이루어진다.
	
	- **2. @Transactional 메서드 안에서 같은 클래스의 Propagation.REQUITES_NEW 나, Propagation.NESTED 가 붙은 @Transactional 메서드가 실행되는 경우**
	
		- 1번과 맥락은 비슷하다.
		
		- Transactional 이 동작하는 A 안에서 Transactional Propagation.REQUITES_NEW 이 붙은 새로운 물리 트랜잭션을 만들려고 한다.
		
		- 이걸 동작 시키면, 프록시 객체 내의 A 메서드가 동작하여
		
		- startTransaction( ); --> A( ); --> endTransaction( ); 이런 순서로 동작을 하게 되는데,
		
		- A( ); 안에서 호출되는 B( ); 역시 this.B( ); 로 프록시 객체가 아니라, 원본 객체의 B 메서드를 불러온다.
		
		- 그래서 트랜잭션 처리가 안된다.
		
		- 고로 우리가 원하는 새로운 물리 트랜잭션을 호출하여 별도 커밋하거나, 별도 롤백을 하는 등의 처리에서 예기치 못한 오류를 불러일으킬 수 있다.
	
-  이 뿐만 아니라, 뭔가 전 후에 처리를 해주어야 하는 AOP의 커스텀어노테이션을 만들었다면, @Transactional 처럼 같은 클래스 안에서 부르는 일은 되도록 피하는 것이 좋다.

- 이를 해결하는 방법으로는 AspectJ 를 사용하는 방법이나, 클래스 내 로직을 AOP로 완전히 묶는 방법이 있는데, 둘 다 비추천 하고 가장 좋은 건 self-invocation 방황을 아예 만들지 않는 것이다.

- 그러나 꼭 만들어야만 하겠다면, 최초 실행되는 메서드에만 @Transactional 을 붙이거나, 클래스 자체에 @Transactional 을 붙이고 메서드들에는 붙이지 않는 방법으로 개발하도록 하자.

- 만약 안에서 실행되는 메서드에 꼭 Transactional을 붙여야 한다면.. 가급적 객체의 책임을 분리하여 외부 호출 하는 방법을 추천한다.

- 또 JPA를 쓰는경우 self-invocation으로 인해 트랜잭션이 처리되지 말아야 하는 경우지만, 트랜잭션 처리가 되어 DB에 저장은 되는 경우가 있는데

- 이는 JpaRepository를 상속하면 구현체인 SimpleJpaRepository가 실행되고, SimpleJpaRepository에는 기본적으로 트랜잭션이 붙어있기 때문에 이런 처리가 되는 것이다.

- 이를 헷갈리면 안된다. 결론적으로는 JpaRepository를 제외한 그 앞,뒤에서 일어난 트랜잭션은 처리가 되지 않았을 것이다.

<br>
<br>

### synchronized 를 사용하려면 @Transactional 어노테이션을 삭제해야 하는 이유
  - @Transactional 이 붙은 메서드가 있는 클래스의 경우 프록시 객체로 저장되는데, 그 안에서는 원래 객체를 필드로 보유해서 생성자로 받는다.
 
  - 그리고 트랜잭션이 붙어있던 메서드가 앞뒤로 startTransaction( ); 과 endTransaction( ); 이 붙은 상태로 존재한다.
 
  - 즉, startTransaction( ); -> 실행메서드 -> endTransaction( ); 순서로 실행된다.
  
  - 그러나, synchronized 가 붙어있는건 프록시 객체의 메서드가 아니라, 원래 본체 메서드만이므로, 실행메서드에는 하나의 스레드만 접근 가능하지만, @@Transactional 로 인해 프록시 객체 속 메서드가 실행되므로 endTransaction( ); 가 실행되기 전에 새로운 스레드가 접근할 수 있다.
  
  - 그래서 동시성 이슈가 해결되지 않는다.
  
  - 그러므로 @Transactional 을 삭제해주어야 한다. 하지만 그러면 때에따라 원자성이 보장되어야 하는 메서드에서 그렇지 못하게 될 수 있으므로 권장하는 방법은 아니며, 비관적락이나 낙관적락 혹은 레디스를 활용하는 분산락등을 이용하는 것을 추천한다.

<br>
<br>

### DB 격리수준과 Transaction
 - DB의 격리수준은 고립도와 성능의 트레이드 오프 관계를 갖고 조절한다.
 
 - **READ UNCOMMITTED**
 
	- 언제든지 읽기 작업을 할 수 있기 때문에 커밋되지 않은 변경사항도 조회 할 수 있다.
	
	- **Dirty Read** 현상이 발생 할 수 있다.
	
		- A Transaction 이 값을 변경하고, B Transaction 이 그 값을 읽어 옴.
		
		- A Transaction 이 RollBack 을 함.
		
		- B Transaction 은 잘못된 값을 가져온 것이 되어버리는 문제 발생.
	
	- @Transactional(isolation = Isolation.READ_UNCOMMITTED) 를 사용함으로 해당 메서드의 트랜잭션을 설정 할 수 있다.

 <br>
 
 - **READ COMMITTED**
 
	- 커밋된 데이터만 읽어올 수 있음
	
	- 오라클의 기본 격리 수준
	
	- 가장 많이 선택되는 격리 수준이기도 함.
	
	- A 트랜잭션이 값을 변경 할 때 커밋을 하지 않았으면, B 트랜잭션이 접근 할 때 이전 값을 받아가고, A가 커밋을 하면 그 순간부터 B트랜잭션이 변경된 값을 반환 받는다.
	
	- **Non Repeatable Read** 현상이 발생 할 수 있다.
	
		- A Transaction 이 값을 변경 하는 도중, B Transaction 이 연속으로 2번 Read 함.
		
		- 그 사이에 Commit 이 일어났음.
		
		- B Transaction 은 같은 로직을 사용했는데 가져온 값이 다른 문제 발생. 
	
	- @Transactional(isolation = Isolation.READ_COMMITTED) 를 사용함으로 해당 메서드의 트랜잭션을 설정 할 수 있다.

 <br>
 
 - **REPEATABLE READ**
 
	- MySQL 기본 격리 수준
	
	- 변경 전의 레코드를 전부 UNDO 공간에 백업하는데, 거기서 데이터를 세밀하게 제어하고, 읽어오므로 Non Repeatable Read 문제를 해결할 수 있음.
	
	- 즉, 자신보다 먼저 실행된 트랜잭션 데이터 중 최신만을 조회한다.
	
		- UNDO : 최신 데이터를 오래된 데이터로 롤백하기 위한 공간
		
		- REDO : 오래된 데이터를 최신 데이터로 만들기 위한 공간
		
	- **Phantom Read** 문제가 생길 수 있다.
	
		- B Transaction 이 값을 2번 읽어옴
		
		- 그 사이 A Transaction 이 INSERT 문으로 값을 추가해버림.
		
		- 새로운 레코드까지는 막을 수 없으므로, B Transaction 에는 역시 값이 다르게 들어옴.
 
 	- @Transactional(isolation = Isolation.REPEATABLE_READ) 를 사용함으로 해당 메서드의 트랜잭션을 설정 할 수 있다.
 
 
 <br>
 
 - **SERIALIZABLE**
 
	- 읽기,쓰기 다 포함하여 순차적으로 트랜잭션을 진행 시킨다.
	
	- 데이터 부정합 문제가 발생하지 않지만, 성능이 너무 떨어진다.
	
	- @Transactional(isolation = Isolation.SERIALIZABLE) 를 사용함으로 해당 메서드의 트랜잭션을 설정 할 수 있다.

<br>
<br>
<br>
<br>
<br>