---
title: Important Study - Concurrency issues And Lock
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

<hr>

### 스레드 동기화 & 동시성 이슈 문제들
 - **레이스 컨디션 ( Race Condition )**
	* 두 개 이상의 스레드가 동시에 접근하여 결과값에 영향을 줄 수 있는 상태 흔히 말하는 동시성 문제라고 할 수 있다.


 - **교착 상태 ( Deadlock )**
	* 공유 자원에 대한 요구가 엉켜서 자원 관리를 잘못하여 프로세스나 스레드가 자원의 락을 획득하기 위해 무한 대기 하는 것
	
	
 - **기아 상태 ( Starvation )**
	* 스레드들에게 우선 순위를 부여하여 공유 자원에 접근할 때, 우선순위가 낮은 스레드가 소외되어 아무일도 하지 못하는 상태
	
	
 - **라이브락 ( Livelock )**
	* 스레드들이 동시에 실행되면서 락의 해제와 획득을 반복적으로 하면서 정상적으로 동작하는 것처럼 보이지만 사실상 아무것도 못하고 무한 동작중인 상황

<br>
<br>

### 가시성, 동시성, 그리고 원자성
 - **가시성 ( Visibility )**
	- CPU 안에는 CPU cache 라는 영역이 있다.
	
	- 우리가 선언한 변수의 값은 CPU cache를 거쳐서 메인메모리에 올라가고 불러온다.
	
	- 이는 CPU가 메인 메모리에서 값을 읽고 쓰는 시간을 아끼기 위해서이다.
	
	- 그런데, CPU cache 에서 메인 메모리로 값이 언제 옮겨갈지 모르는다는 것이다.
	
	- 그러한 이유로 하나의 스레드에서 변경한 변수의 값이 다른 스레드에서 보이지 않는 것을 **'가시성 이슈'** 라고 한다.
 
 <br>
 
 - **동시성 ( Concurrency )**
	- 하나의 코어에 여러개의 스레드가 번갈아가며 작업을 실행하는 성질을 뜻한다.
	
	- 동시에 하는 것처럼 보일 수 있지만, 사실은 동시에 작업하는 것처럼 보일만큼 빠르게 작업을 번갈아가며 진행한다.
	
	- 그 때 일어나는 일관성 문제나, 위의 레이스컨디션, 데드락등의 문제가 일어나는 것을 **'동시성 이슈'** 라고 한다.

 <br>
 
 - **원자성 ( Atomicity )**
	- 더 이상 쪼개질 수 없는 성질.
	
	- 원자성을 가지는 작업은 실행되어 진행되다가 종료되지 않는 한, 중간에서 멈출 수 없음. 
	
	- 그리고 해당 명령을 수행 할 때 다른 곳에서 접근할 수 없음.
	
<br>
<br>

### 가시성, 동시성 이슈를 해결하는 방법
 - **Java 에서는 Volatile 과 Synchronized 그리고 Atomic 이렇게 세 가지를 지원한다.**

 - **Volatile**
	- volatile 은 변수에 설정해주는 것인데, 해당 변수에 대해 CPU cache를 사용하는 것이 아닌 메인 메모리에서 바로 올리고 내릴 수 있게 하여, 가시성을 보장해준다.

	- 그러나 원자성을 보장하지 않기 때문에 동시성 이슈는 해결하지 못한다.
	
	- 다음은 Volatile 을 활용한 예시 코드이다.
	
	```java
	public class VolatileExample {
		private static volatile int sharedValue = 0;

		public static void main(String[] args) {
			// 스레드 1: 값을 증가시킴
			Thread thread1 = new Thread(() -> {
				for (int i = 0; i < 5; i++) {
					System.out.println("Thread 1: Incrementing sharedValue");
					sharedValue++;
					sleepForRandomTime();
				}
			});

			// 스레드 2: 값을 읽음
			Thread thread2 = new Thread(() -> {
				for (int i = 0; i < 5; i++) {
					System.out.println("Thread 2: Reading sharedValue: " + sharedValue);
					sleepForRandomTime();
				}
			});

			// 스레드 실행
			thread1.start();
			thread2.start();
		}

		private static void sleepForRandomTime() {
			try {
				Thread.sleep((long) (Math.random() * 1000));
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
	```
	
	- volatile 을 통해 가시성 이슈를 해결 할 수 있다.
	
	- 해당 코드의 결과는 다음과 같이 나온다.
	
	```plaintext
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 1
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 2
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 3
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 4
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 5
	```
	
	- 그러나, 만약 volatile을 사용하지 않았다면 숫자 증가가 메인 메모리에 늦게 올라가기 때문에,
	가시성 이유때문에 아래와 같은 결과가 나오게 된다.
	
	```plaintext
	Thread 1: Incrementing sharedValue
	Thread 1: Incrementing sharedValue
	Thread 1: Incrementing sharedValue
	Thread 1: Incrementing sharedValue
	Thread 1: Incrementing sharedValue
	Thread 2: Reading sharedValue: 0
	Thread 2: Reading sharedValue: 0
	Thread 2: Reading sharedValue: 0
	Thread 2: Reading sharedValue: 0
	Thread 2: Reading sharedValue: 0
	```

<br>
 
 - **Synchronized**
	- synchronized 은 메서드에 걸거나, synchronized 블럭을 만들어서 사용한다.
	
	- 해당 메서드 및 블럭에는 한 개의 쓰레드만 접근이 가능한데, 연산에 대하여 쓰레드 하나씩 수행하기 때문에 가시성과 원자성을 모두 보장한다.
	
	- 다만, 이러한 Lock을 거는 방식 자체가 Blocking 방식이기 때문에 병목 현상을 일으키기 쉽고, 데드락 상태가 될 수도 있기 때문에 조심해서 사용해야한다.
	
	- 그리고 JPA나 Spring 환경에서 사용할 때에는 @Transactional 어노테이션을 지우고 사용해야 한다.
	
	- @Transactional 은 프록시 객체로 동작하여 @Transactional 이 붙은 그 메서드 앞 뒤로 startTransaction( ); endTransaction( ); 메서드를 동작시키는데 endTransaction( ) 이 작동하기 전에 다른 스레드가 들어올 수 있어서 동기화가 제대로 이루어지지 않을 수 있다. ( 프록시에 대해서는 추후 포스팅 할 예정이다. )
 
	- synchronized 를 활용하여 동시성 문제를 해결한 예시코드이다.
	
	```java
	public class SynchronizedExample {
		// 공유 변수
		private static int counter = 0;

		// 메서드에 synchronized 키워드 추가
		private synchronized static void incrementCounter() {
			for (int i = 0; i < 1000000; i++) {
				counter++;
			}
		}

		public static void main(String[] args) throws InterruptedException {
			// 두 개의 스레드 생성
			Thread thread1 = new Thread(SynchronizedExample::incrementCounter);
			Thread thread2 = new Thread(SynchronizedExample::incrementCounter);

			// 스레드 시작
			thread1.start();
			thread2.start();

			// 스레드가 종료될 때까지 대기
			thread1.join();
			thread2.join();

			// 결과 출력
			System.out.println("최종 카운터 값: " + counter);
		}
	}
	```
	
	```java
	public class SynchronizedExample {
		private static int counter = 0;
		private static final Object lock = new Object();  // 락 객체 생성

		private static void incrementCounter() {
			for (int i = 0; i < 1000000; i++) {
				// synchronized 블록 사용
				synchronized (lock) {
					counter++;
				}
			}
		}

		public static void main(String[] args) throws InterruptedException {
			Thread thread1 = new Thread(SynchronizedExample::incrementCounter);
			Thread thread2 = new Thread(SynchronizedExample::incrementCounter);

			thread1.start();
			thread2.start();

			thread1.join();
			thread2.join();

			System.out.println("최종 카운터 값 (synchronized block): " + counter);
		}
	}
	```
	
	- 해당 코드의 출력값은 2000000 이 나온다.
	
	- 그러나, synchronized 를 사용하지 않는다면 그것보다 적은 예측할 수 없는 값이 나오게 된다.
 
<br>
 
 - **Atomic**
	- Atomic 이 붙은 클래스.. 예를 들어 AtomicInteger, AtomicLong 과 같은 클래스를 통해 사용할 수 있다.
	
	- Lock 을 거는 synchronized 방식과 다르게 CAS 알고리즘을 사용한다.
	
	- CAS 알고리즘 : 현재 스레드가 존재하는 CPU의 Cache 메모리와 Main 메모리의 값을 비교하여, 일치하는 경우 새로운 값으로 교체하고, 일치 하지 않으면 기존 교체를 실패처리하고 재시도 하는 방식 
 
	- 내부 코드를 확인해보면 volatile 처리가 된 value를 가지고 있어서 가시성도 보장이 된다. 
	
	- 그리고 CAS 알고리즘을 적용한 메서드 호출을 통해 원자성도 보장해준다.
	
	- Atomic은 직접적으로 Lock 을 거는게 아니기 때문에 synchronized 보다 성능이 좋다.
	
	- 다음은 Atomic 을 사용한 예시 코드이다.
	
	```java
	import java.util.concurrent.atomic.AtomicInteger;

	public class AtomicExample {
		private static AtomicInteger counter = new AtomicInteger(0);

		private static void incrementCounter() {
			for (int i = 0; i < 1000000; i++) {
				counter.incrementAndGet(); // Atomic 연산: 현재 값에 1을 더하고 그 값을 반환
			}
		}

		public static void main(String[] args) throws InterruptedException {
			Thread thread1 = new Thread(AtomicExample::incrementCounter);
			Thread thread2 = new Thread(AtomicExample::incrementCounter);

			thread1.start();
			thread2.start();

			thread1.join();
			thread2.join();

			System.out.println("최종 카운터 값 (Atomic): " + counter.get());
		}
	}
	```
	
	- **추가 상식**
		- Map 을 보면 HashTable과 HashMap 그리고 ConcurrentHashMap 이 있는데, 이 중 thread-safe 하지 않은 HashMap 은 동기화가 안되니 제외하고, HashTable은 내부적으로 메서드 자체에 synchronized 를 걸기에 성능이 다소 떨어진다.
		
		- 그리고 ConcurrentHashMap 은 내부적으로 synchronized 블럭과 CAS 알고리즘을 채택하여, 특정 버킷에 대해서만 Lock 을 걸기때문에 성능이 우수하다.
		
		- 멀티스레드 환경에서는 CAS 알고리즘을 사용하는 Atomic 이나 ConcurrentHashMap 을 사용하면 좋다.
 
<br>
 
 - 그러나 Volatile, Synchronized, Atomic 위의 세 가지 방법은 서버와 DB가 모두 1개만 있을 경우에 사용 가능한 방법이다.
애플리케이션 서버가 여러개일경우에는 위 방식을 사용하면 안된다. 애플리케이션 단에서 동시성을 보장할 방법이 없기 때문이다.
 
 - 그럴 경우 이제 비관적 락이나, 낙관적 락, 유저 락을 걸어 해결해야 한다.
 
<br>
<br>

### 비관적 락 & 낙관적 락 & 유저 락
 - **비관적 락 ( Pessimistic Lock )**
	- DB의 Lock 기능을 이용한다. ( 공유락, 배타락 )
		
		- 공유락 : 동시에 Read는 가능하지만, Write는 불가능
		
		- 배타락 : 트랜잭션이 끝나는 동안 Read/Write 둘 다 불가능
	
	- 레코드 단에 Lock을 걸어서 하나의 스레드만 접근 할 수 있게 잠금을 거는 방식이다. 다만 성능적 측면에서 손실이 생길 수 있다.
	
	- 충돌( 동시에 update 하는 일 )이 빈번하게 일어날 것 같은 로직에는 비관적 락을 거는 것이 좋다.
	
	- 충돌이 빈번하게 일어나는 로직에 낙관적 락을 걸게되면 계속 재시도 로직이 일어나서 오히려 성능이 좋지 않을 수 있다. ( 데드락 가능성도 생긴다 )
 
	- JPA에서는 Repository 단에서 PESSIMISTIC_READ 으로 공유락을, PESSIMISTIC_WRITE 으로 배타락을 거는데.. 보통은 배타락으로 많이 건다.
 
	- **추가 상식**
		- MySQL의 기본적인 Update, Delete 문에는 베타락이 걸려있는데,
		- 그래서 게시판의 조회수 증가 로직 같이 레코드를 Update 해주는 경우 동시에 많은 대상이 몰려 조회수를 Update 할 경우 베타락 때문에 게시글을 수정하는 Update문도 기다려야되는 성능 악화가 일어날 수 있다.
		- 그래서 조회수 증가는 잘 고민해서 로직을 생각해야한다. ( 조회수 DB를 따로 만들어서 배치를 돌리거나, 레디스에 저장한 후 배치를 돌리거나 등 )

<br>
 
 - **낙관적 락 ( Optimistic Lock )**
	- Application 단에서 로직으로 해결
	
	- JPA의 @Version 과 같이 버전을 관리해주는 컬럼을 만들어서.. 커밋을 하기 전에 엔티티 버전과 DB 버전이 같은지를 확인하고, 버전이 같으면 커밋을.. 버전이 다르면 롤백 후 재시도를 해주는 방식이다.
	
	- 동시 요청에 대한 처리 성능이 좋으나, 롤백 로직을 직접 구현해줘야하고, 빈번한 충돌이 있는 서비스에 사용할 경우 롤백 로직이 자주 동작하여 성능에 문제가 생길 수 있다.
	
	- 기가 잦아 성능적으로 중요할 때 사용하며, 어쩌다 한 번씩 일어날 충돌을 대비해야 하는 로직에 사용하는게 좋다.
 
<br>
<br>
<br>
<br>
<br>