---
title: Important Study - DB Lock
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

<hr>

### Lock 이란?

 - 무엇인가 열리지 않도록 막는 장치
 
 - DB에서는 레코드, 테이블, DB 전체에 관련된 락을 걸 수 있다.
 
 - Lock 이 없다면, 아래와 같은 문제가 생길 수 있다.
 
	- A 가 B 에게 100만원을 보낼 때
	
	- A는 100만원이 사라지는데, B에게는 100만원이 안올 수도 있다.
	
	- 즉, 무결성이 깨질 수 있다.


<br>
<br>

### Lock의 종류

 - **배타 잠금 ( Exclusive Locks / X Lock )**
 
	- Exclusive Lock은 특정 Row를 변경(write)하고자 할 때 사용된다.
	
	- 특정 Row에 Exclusive Lock이 해제될 때까지, 다른 트랜잭션은 읽기 작업을 위해 Shared Lock을 걸거나, 쓰기 작업을 위해 Exclusive Lock을 걸 수 없다.
	
	- Exclusive Lock은 SELECT ... FOR UPDATE나 UPDATE, DELETE 등의 수정 쿼리를 날릴 때 각 Row에 걸리는 Lock이다.
	
 - **공유 장금 ( Shared Locks / S Lock )**
 
	- Shared Lock은 특정 Row를 읽을(read) 때 사용되어지는 Lock이다.
	
	- Shared Lock끼리는 동시에 접근이 가능하다. 즉, 하나의 Row를 여러 트랜잭션이 동시에 읽을 수 있다는 것이다.
	
	- 하지만 Shared Lock이 설정된 Row에 Exclusive Lock을 사용할 수는 없다.
	
	- 즉, 특정 Row를 누가 읽고 있음으로써 Shared Lock이 설정되어 있는데, 다른 사용자가 그 데이터에 쓰기 작업을 하기 위해 Exclusive Lock을 걸 수 없다는 뜻이다.
 
	- 일반적인 SELECT 쿼리는 Lock을 사용하지 않고 DB를 읽어 들인다.
	
	- 하지만 SELECT ... FOR SHARE 등 일부 SELECT 쿼리는 특정 Row를 읽을 때 InnoDB각 각 Row에 Shared Lock을 건다.
	
 - **레코드 락 (Record Lock)**
 
	- Record Lock은 Row가 아니라 DB의 index record에 걸리는 Lock이다.
	
	- 여기도 row-level lock과 마찬가지로 Shared Lock과 Exclusive Lock이 있다.
	
 - **갭 락 (Gap Lock)**
 
	- Gap Lock은 DB index record의 gap에 걸리는 Lock이다.
	
	- 여기서 gap이란 index 중 DB에 실제 record가 없는 부분이다.
	
	- Record Lock은 이미 존재하는 Row가 변경되지 않도록 보호하는 반면, Gap Lock은 조건에 해당하는 새로운 Row가 추가되는 것을 방지하기 위함이다.
	
 - **업데이트 락**
 
	- 업데이트 락은 데이터를 수정하기 위해 베타 락(X)을 걸기 전, 데드 락을 방지하기 위해 사용된다.
	
	- 일반적으로 업데이트 락은 UPDATE 쿼리의 필터(WHERE)가 실행되는 과정에서 적용된다.
	
	- 업데이트락은 데이터를 수정할 때 배타락으로 전환하며 읽을 때는 공유락으로 전환하여 업데이트중에 데이터를 읽도록 허락한다.
	
	- MySQL 에서 레코드 단위로 자동적으로 걸려있는 Lock 이다.

<br>
<br>
<br>
<br>
<br>