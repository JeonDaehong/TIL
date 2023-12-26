---
title: Important Study - Redis Cache & Update Strategy
author: Daehong
date: 2023-11-29
category: Jekyll
layout: post
mermaid: true
---

<hr>

### Redis 를 활용하여 캐싱을 하는 기준
 - 자주 사용되면서 자주 변경되지 않는 데이터를 캐싱하는 것이 좋다.
 
<br>
<br>

### 읽기 전략
 - **Look Aside ( Cache Aside ) 패턴** 
 
	- 우선 캐시에서 검색
	
		- 있으면 클라이언트에 바로 반환
		
		- 없으면 DB에서 조회 --> 조회 후 Redis에 캐싱하고 클라이언트에 반환
		
	- 반복적인 읽기가 많은 호출에 적합
	
	- 단 건 호출에는 적합하지 않음.
	
	- 캐시와 DB가 분리되어 가용되기 때문에 원하는 데이터만 별도로 구성하여 캐시에 저장
	
	- 캐시와 DB가 분리되어 가용되기 때문에 캐시 장애 대비 구성이 되어있음.
	
	- 만일 redis가 다운 되더라도 DB에서 데이터를 가져올수 있어 서비스 자체는 문제가 없음.
	
	- 대신에 캐시에 붙어있던 connection이 많았다면, redis가 다운된 순간 순간적으로 DB로 몰려서 부하 발생하는 단점이 있음.
	
	- DB 갱신이 일어나면, 그 때 캐싱에서 지움.
	
	- 조회전에 미리 캐시로 데이터를 밀어넣어주는 작업도 있는데, 그걸 Cache Warming 이라고 함.
	
<br>

 - **Read Through 패턴**
 
<br>
<br>
<br>
<br>
<br>