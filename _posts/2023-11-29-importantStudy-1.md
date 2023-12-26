---
title: Important Study - Mutex & Semaphore
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

<hr>

### 뮤택스 ( Mutex ) 와 세마포어 ( Semaphore ) 는 왜 중요할까?
 - 동시성 프로그래밍의 가장 큰 숙제는 **공유자원 관리**일 것이다.
 
 - 공유자원을 안전하게 관리하기 위해서는 **상호배제(Mutual exclusion)**를 달성하는 기법이 필요하다.
 
 - 뮤텍스와 세마포어는 이를 위해 고안된 기법으로 서로 다른 방식으로 상호배제를 달성한다.

<br>
<br>

### 뮤택스 ( Mutex ) 란 ?
 - 동기화 대상이 하나일 때 사용하며, **Key**를 기반으로 한다.
 
 - Key를 가진 프로세스, 스레드만이 접근 가능하고 완료 후 다음 스레드에게 key를 넘기기 전까지는 다른 스레드는 접근할 수 없다.
 
 - 즉, 뮤텍스 개체는 두 스레드가 동시에 사용할 수 없다.
 
 - Lock을 가짐 ( 0, 1 상태 뿐이라 )
 
 - 자원을 소유하고 책임을 가진다.
 
 - 소유하고 있는 스레드만이 이 뮤택스를 해제할 수 있다.

 - 프로세스의 범위를 가지며, 프로세스가 종료될 때 자동으로 Clean up 된다.
 
 - **예를들면 다음과 같다.**
	- A라는 사람이 키를 가지고 하나뿐인 화장실을 감. 다음 사람들은 줄을 서있음. A가 볼일을 다 보고, 와서 다음 사람에게 화장실 Key를 넘김. Key를 받은 사람이 화장실을 감.
 
 - ** 뮤택스를 구현한 예시 코드이다.**
 
	```java
	import java.util.concurrent.locks.Lock;
	import java.util.concurrent.locks.ReentrantLock;

	public class MutexExample {
		private final Lock mutex = new ReentrantLock();

		public void performCriticalSection() {
			try {
				// 뮤텍스 획득
				mutex.lock();

				// 크리티컬 섹션 (임계 영역) 코드
				System.out.println("Critical Section: " + Thread.currentThread().getName());

			// 뮤텍스 해제
			} finally {
				mutex.unlock();
			}
		}

		public static void main(String[] args) {
			MutexExample example = new MutexExample();

			// 여러 스레드에서 크리티컬 섹션 접근 시도
			for (int i = 0; i < 5; i++) {
				new Thread(() -> {
					example.performCriticalSection();
				}).start();
			}
		}
	}
	```
	
	- 이 코드에서 ReentrantLock 객체를 생성하고
	performCriticalSection 메서드에서 크리티컬 섹션을 진입하기 전에 lock() 메서드를 호출하여 뮤텍스를 획득하고,
	크리티컬 섹션을 빠져나갈 때에는 unlock() 메서드를 호출하여 뮤텍스를 해제한다. 
	
	- 이렇게 함으로써 한 번에 하나의 스레드만 크리티컬 섹션에 접근할 수 있다.
	
<br>
<br>

### 세마포어 ( Semaphore ) 란?
 - 동기화 대상이 하나 이상일 때 사용한다.
 - 정해진 갯수 만큼의 스레드가 접근할 수 있으며, 하나의 스레드가 접근 할 때마다 갯수의 수가 마이너스 되고, 나오면 플러스가 된다.
 - 자원 소유가 불가능하다.
 - 다른 스레드가 세마포어를 해제할 수 있다.
 - 시스템 범위에 걸쳐있고, 파일 시스템 상의 파일로 존재한다.
 - **예를들면 다음과 같다.**
	- 화장실이 3개가 있고, 한 사람이 들어가면 2개가 남는다. 그리고 그 사람이 나오면 다시 3개가 된다. 3명이 들어가서 화장실 남은 갯수가 0개면, 다음에 오는 사람들은 기다려야 한다.

<br>
<br>

### 뮤택스 vs 세마포어

**Mutex vs Semaphore**

| **속성**          | **Mutex**                  | **Semaphore**                    |
|----------------|------------------------|------------------------------|
| **용도**          | 단일 자원 동기화            | 다중 자원 동기화                |
| **동작 기반**      | Key                    | Count                        |
| **동시 접근 제어**    | 두 스레드 동시 접근 불가        | 정해진 갯수만큼 동시 접근 가능       |
| **자원 소유 및 책임**  | 소유 및 책임이 있음           | 자원 소유 불가능                   |
| **해제 권한**       | 뮤텍스를 소유한 스레드만 해제 가능 | 다른 스레드가 해제 가능              |
| **범위**          | 프로세스 범위               | 시스템 범위                     |
| **Clean up**      | 자동으로 Clean up 됨       | 수동으로 해제 필요 (파일 등)        |

<br>
<br>
<br>
<br>
<br>