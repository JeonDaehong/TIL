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
 - 레이스 컨디션 ( Race Condition )
	* 두 개 이상의 스레드가 동시에 접근하여 결과값에 영향을 줄 수 있는 상태 흔히 말하는 동시성 문제라고 할 수 있다.

 - 교착 상태 ( Deadlock )
	* 공유 자원에 대한 요구가 엉켜서 자원 관리를 잘못하여 프로세스나 스레드가 자원의 락을 획득하기 위해 무한 대기 하는 것
	
 - 기아 상태 ( Starvation )
	* 스레드들에게 우선 순위를 부여하여 공유 자원에 접근할 때, 우선순위가 낮은 스레드가 소외되어 아무일도 하지 못하는 상태
	
 - 라이브락 ( Livelock )
	* 스레드들이 동시에 실행되면서 락의 해제와 획득을 반복적으로 하면서 정상적으로 동작하는 것처럼 보이지만 사실상 아무것도 못하고 무한 동작중인 상황
 
 
<br>
<br>
<br>
<br>
<br>