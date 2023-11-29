---
title: Java - Multithread Synchronization Issues Basic
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

* 우리가 멀티스레드의 동기화 문제를 알기 전에, 먼저 멀티 스레드가 뭔지를 알아야한다.

* 자세하고 깊은 내용은 추후 멀티스레드에 공부할 때 깊이 공부할 예정이며, 우선 오늘은 기초적인 개념만 이해하고 공부하였다.

* 정말 쉬운 웹 프로그래밍을 예시로 들면, 한 사용자만의 동작을 허용하는걸 단일 스레드라고 하며, 여러 사용자가 동시에 해당 프로그램을 사용할 수 있다면 그게 멀티 스레드이다.

* 스프링을 활용해서 웹 프로그래밍을 하면, 아파치를 많이 사용할텐데 아파치는 기본적으로 멀티 프로세스로 구현되어있고, 설정에 따라 멀티 스레드를 같이 운용할 수 있다. 그래서 위와 같은 병행 실행이 가능한데, 여러 사용자가 하나의 static에 접근한다거나, 같은 객체에 접근하게 될 경우 발생할 수 있는 문제가 바로 **[동기화 문제]**이다

* 기본적인 예시코드이다
	```java
	public class SynchronizationExample {
		public static void main(String[] args) {
			SharedResource sharedResource = new SharedResource();

			Thread thread1 = new Thread(() -> {
				for (int i = 0; i < 1000; i++) {
					sharedResource.increment();
				}
			});

			Thread thread2 = new Thread(() -> {
				for (int i = 0; i < 1000; i++) {
					sharedResource.increment();
				}
			});

			thread1.start();
			thread2.start();

			try {
				thread1.join();
				thread2.join();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}

			System.out.println("Final Count: " + sharedResource.getCount());
		}
		}

		class SharedResource {
		private int count = 0;

		public void increment() {
			count++;
		}

		public int getCount() {
			return count;
		}
	}
	```
* 이렇게 될 경우, 스레드 1과 2에서 동시에 해당 객체에 접근을 하게 되고, 각자 1000번씩 숫자를 증가시킨다.

* 저렇게 동시에 한 객체에 접근하게 될 때 문제가 발생하게 된다.( 물론 스레드별로 각자 인스턴스를 만든다면 문제는 없다. )

* 이런 경우는 저렇게 객체 뿐아니라, 싱글턴 패턴에서도 발생할 수 있다.

* 해당 문제를 해결하기 위해서는 동시에 접근할 수 있는 메서드에 synchronized 를 붙여서 동기화 작업을 해줘야 한다.
	```java
	public class SynchronizationExample {
		public static void main(String[] args) {
			SharedResource sharedResource = new SharedResource();

			Thread thread1 = new Thread(() -> {
				for (int i = 0; i < 1000; i++) {
					sharedResource.increment();
				}
			});

			Thread thread2 = new Thread(() -> {
				for (int i = 0; i < 1000; i++) {
					sharedResource.increment();
				}
			});

			thread1.start();
			thread2.start();

			try {
				thread1.join();
				thread2.join();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}

			System.out.println("Final Count: " + sharedResource.getCount());
		}
	}

	class SharedResource {
		private int count = 0;

		public void synchronized increment() {
			count++;
		}

		public int getCount() {
			return count;
		}
	}
	```
* 이렇게 increment 라는 메서드에 synchronized를 붙여줌으로써, 동시에 접근할 경우 하나의 스레드가 작업을 마치는 동안 다른 스레드들은 순차적으로 기다려야 한다.

* 다만 synchronized를 너무 많이 사용하게 되면 그만큼 많은 사용자들이 오래 기다려야 할 수 있으므로, 너무 남발하는 것은 좋은 방법이 아니다. 자세한 건 추후 멀티 스레드를 공부하며 더 작성하겠지만 java.util.concurrent 를 사용하는 것도 좋은 방법이다. ( HashMap 대신 ConcurrentHashMap을 사용하는 것과 같은 맥락 )

<br>
<br>
**결론**
* 결론은 우리가 자바를 통해 프로그래밍을 할 때, 클래스를 만들고 static을 만들고 할 것이다.
* 이 때 멀티 스레드, 병렬 프로그래밍을 염두하고 코드를 작성해야한다.
* 동기화 문제 라는 것이 무엇인지를 정확히 알고 있어야 한다.


<br>
<br>
<br>
<br>
<br>