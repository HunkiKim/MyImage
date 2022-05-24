---
comments: true
date: 2022-05-24
description: "Redis vs Memcached"
subject: blog
category: Database
tags: [ Database ]
title: Redis vs Memcached
---
# Redis, Memcached
- 분산 메모리 캐싱 시스템이다.
- 데이터를 Key-Value 형태로 메모리에 저장한다 (NoSQL, Key Value DB)
  - 디스크 기반 DB보다 빠르게 데이터를 읽는다.
  - 시큐리티에서 Session관련에서 사용할 때 jdbc보다 더 많이 사용되는데 이는 Disk기반보다 빠르게 데이터를 읽기 때문에 많이 사용한다.
  - Session은 Http통신마다 불러오기 때문에 빨리 불러올수록 성능도 향상된다.
> Redis와 같은 in-memory database의 단점은 데이터의 휘발성입니다. 하지만 이 점을 활용할 수 있는 부분이 있습니다. 바로 refresh token입니다. refresh token은 access token과 달리 서버에서 관리하게 되는데, access token과 마찬가지로 유효기간이 있기 때문에 일정 시간이 지나면 데이터가 휘발되는 redis를 사용합니다.
- DB 부하를 줄여 동적 웹 애플리케이션의 속도 개선을 위해 사용한다.
- 데이터 파티셔닝을 통해 많은 데이터를 효과적으로 처리한다.
- 수평확장이다.

## Redis
- 싱글 스레드이다.
- 다양한 자료구조와 API를 지원한다.
- 데이터 스냅샷, AOF 로그로 장애상황시 데이터 복구가 가능해 영속성을 보장한다.
> 데이터 스냡샷이란 메모리에 내용을 Disk에 옮겨 담는 방식이다. Redis의 동작을 멈추고 스냅샷을 저장하는 *SAVE* 방식과 별도 프로세스를 띄워 redis의 동작 중에 스냅샷을 저장하는 *BG SAVE*가 있다.

>AOF 로그란 redis의 모든 write/update 연산을 log 파일에 기록하는 것을 말한다. operation이 발생할 때마다 기록하기 때문에 특정 시점이 아닌 현재 시점까지의 로그를 기록한다. redis를 정지하지 않는 방식이다.
- 트랜잭션을 지원한다.
- 마스터-슬레이브 구조로 여러 개의 복제본을 생성할 수 있다.
  - 이러한 Copy-on-Write 방식으로 사용할 때마다 메모리의 2배가 필요하다.
- 데이터 삭제 정책이 다양하다.
- 세션관리 (Refresh Token), 캐싱에 주로사용한다. 아까 말한것처럼 인메모리 방식에 위에서 설명한 장점들이 있기 때문에 사용하는 것이다.
- 서버 측 복제 및 샤딩을 지원한다.
- 대규모 트래픽에 대한 응답속도가 불안정하다. 많은 데이터가 update되면 메모리 파편화 및 응답속도 저하가 발생된다. 몰론 극단적인 상황에서 발생된다.

## Memcached
- 멀티 스레드이다.
- 문자열을 지원한다.
- 데이터 삭제 정책에는 LRU 알고리즘만 사용한다.

## 차이점
1. 데이터 타입으로 Redis는 String, Set, Sorted Set, Hash, List를 사용하지만 Memcached는 String만 사용한다.
2. 데이터 저장할 때 Memory,Disk 둘 다 사용하지만 후자는 Memory만 사용한다.
3. Redis는 메모리를 재사용할 수 없고 명시적으로만 데이터가 제거 가능한 반면 Memcached는 메모리가 부족하면 LRU 알고리즘으로 데이터 삭제 후 사용한다.
4. Redis는 단일 스레드이고 Memchached는 멀티 스레드이다.
5. 캐싱 용량으로 Redis는 Key, Value모두 512MB인 반면 Memcached는 Key 250byte, value 1MB이다.
6. 메모리 사용량은 위에 특징들만봐도 레디스가 훨씬 많다.
7. 데이터 삭제 정책으로 Redis는 다양한 방식이 있지만 Memcached는 오직 LRU 알고리즘만 사용한다.

## 결론
결국 Redis만 사용하게 되었다. 성능차이도 크지않고 Redis는 메모리가 날라가도 복구가 가능하고 다양한 자료구조를 지원하기 때문이다. 또한 Redis가 커뮤니티 풀도 형성이 되어있기 떄문에 훨씬 좋다.

## Redis 사용법
### 설치 및 시작
1. brew install redis를 통해 redis를 설치한다.
2. brew services start redis 를 통해 레디스를 실행시킨다. 
3. brew services stop redis, brew services restart redis 등 명령어로 멈추고 재시작 할 수 잇다.
4. redis-cli를 통해 CLI를 사용 가능하다.

### 명령어 기본적인 CRUD
- 저장 : set key value 를 통해 데이터를 저장 가능하다. OK라고 뜨면 저장이 잘 된것이다.
- key 조회 : 이제 조회를 해보자. keys [패턴]을 통해 조회가 가능하다. 그러면 key가 나온다.
- value 조회 : get [key 이름]을 통해 value를 조회할 수 있다.
- 삭제 : del key를 사용하면 된다.
- key 수정 : rename key newKey를 통해 이름을 변경할 수 있다.

![img](https://drive.google.com/file/d/1G81ExPMiLPMINdL3TVpFcX8ILaDRuvnL/view?usp=sharing)

