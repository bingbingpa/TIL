# [[우아한테크세미나] 우아한레디스](https://www.youtube.com/watch?v=mPB2CZiAkKM&list=WL&index=14&t=1145s)

### redis 소개 

- In-Memory data structure store
- open source(BSD 3 License)
  - 소스를 수정해서 써도 상관없다. 하지만 레디스 모듈은 라이센스가 달라서 수정하면 소스 공개해야 한다.
- support data structures
  - strings, set, sorted-set, hashes, list
  - hyperloglog, bitmap, geospatial index
  - stream
- only 1 committer
- cache 의 정의
  - 나중에 요청올 결과를 미리 저장해두었다가 빠르게 서비스를 해주는 것

### 왜 Collection 이 중요한가?

- 개발의 편의성
  - 랭킹 서버를 구현한다고 했을때... Redis 의 Sorted Set 을 이용하면 랭킹을 구현 할 수 있다. 
  - 덤으로 Replication 도 가능 
  - 다만 가ㅏ져다 쓰면 거기의 한계에 종속적이 됨 
  - 랭킹에 저장해야할 id 가 1개당 100byte 라고 했을 때 
    - 10명 1K
    - 10000명 1M
    - 10000000명 1G
    - 10000000000명 1TB
- 개발의 난이도
  - 친구 리스트를 key / value 형태로 저장해야 한다면?
  - redis 의 경우 자료구조가 Atomic 하기 때문에, race condition 을 피할 수 있다.
    - 그래도 잘못짜면 발생하긴 한다...
- 외부의 collection 을 잘 이용하는 것으로, 여러가지 개발시간을 단축시키고, 문제를 줄여줄 수 있기 때문에 collection 이 중요하다.
- redis 사용 처
  - remote data store
    - a,b, c... N 대의 서버에서 데이터를 공유하고 싶을 때
  - 한대에서만 필요하다면 전역 변수를 쓰면 되지 않을까?
    - redis 자체가 atomic 을 보장해준다.(싱글 스레드이기 때문에)
  - 주로 많이 쓰는 곳들
    - 인증 토큰 등을 저장(string or hash)
    - ranking 보드로 사용(sorted set)
    - 유저 API limit
    - 좌표(list)

### Redis Collections

- Strings - 단일 key
  - 기본 사용법
    - set \<key> \<value>
    - get \<key>
  - set token:1234567 abcdefghijklmn
  - get token:1234567
- strings - 멀티 key
  - 기본 사용법
    - mset \<key1> \<value1> \<key2> \<value2> ... \<keyN> \<valueN>
    - mget \<key1> \<key2> ..... \<keyN>
  - mset token:1234567 abcdefghijklmn email:1234567 test@gmail.com
  - mget token:1234567 email:1234567
- list: insert 
  - 기본 사용법
    - Lpush 는 맨 앞으로 삽입, Rpush 는 맨 뒤로 삽입
    - Lpush \<key> \<A>
      - key: (A)
    - Rpush \<key> \<B>
      - key: (A, B)
    - Lpush \<key> \<C\>
      - key: (C, A, B)
    - Rpush \<key> \<D, A>
      - key: (C, A, B, D, A)
- list: pop
  - lpop, rpop 로 맨 처음 또는 끝에서 값을 꺼낼 수 있다.
- set: 데이터가 있는지 없는지만 체크하는 용도
  - 기본 사용법
    - SADD \<key> \<Value>
      - value 가 이미 key 에 있으면 추가되지 않는다.
    - SMEMBERS \<key>
      - 모든 value 를 돌려줌
    - SISMEMBER \<key> \<value>
      - value 가 존재하면 1, 없으면 0
  - 특정 유저를 flollow 하는 목록을 저장할 때 유용하다.
- sortes sets: 랭킹에 따라서 순서가 바뀌길 바란다면 
  - 기본 사용법
    - ZADD \<key> \<score> \<value>
      - value 가 이미 key 에 있으면 해당 score 로 변경된다.
    - ZRANGE \<key> \<startIndex> \<endIndex>
      - 해당 index 범위 값을 모두 돌려줌
      - zrange testkey 0-1(0에서 -1 로 주면 모든 범위를 다 가져온다.)
        - 모든 범위를 가져옴
  - 유저 랭킹 보드로 사용할 수 있음
  - **sorted sets 의 score 는 double 타입이기 때문에, 값이 정확하지 않을 수 있다.**
- hash: key 밑에 sub key 가 존재
- **collection 주의 사항**
  - 하나의 컬렉션에 너무 많은 아이템을 담으면 좋지 않음 
    - 10000개 이하 몇천개 수준으로 유지하는게 좋음
  - expire 는 collection 의 item 개별로 걸리지 않고 전체 collection 에 대해서만 걸림
    - 즉 해당 10000 개의 아이템을 가진 collection 에 expire 가 걸려 있다면 그 시간 후에 10000개의 아이템이 모두 삭제 된다.

### Redis 운영

- 메모리 관리를 잘하자
  - redis 는 in-memory data store
  - physical memory 이상을 사용하면 문제가 발생
    - swap 이 있다면 swap 사용으로 해당 메모리 page 접근시 마다 늦어짐
    - swap 이 없다면? OOM 으로 죽을수도 있다.
  - maxMemory 를 설정하더라도 이보다 더 사용할 가능성이 큼
    - maxMemory 는 memory 사용의 임계치를 설정하는 것이다. 
    - 메모리가 꽉 차면 레디스가 알아서 랜덤하게 키를 지우거나 expire 목록에 있는것 중에서 지우고 메모리를 확보한다.
  - RSS 값을 모니터링 해야함
  - 많은 업체가 현재 메모리를 사용해서 swap 을 쓰고 있다는 것을 모를때가 많음
  - **큰 메모리를 사용하는 instance 하나보다는 작은 메모리를 사용하는 instance 여러개가 안전함**
  - write 가 많이 일어나는 인스턴스는 메모리를 최대 2배까지 더 쓸수도 있다.
  - 메모리가 부족할때는?
    - 좀 더 메모리 많은 장비로 migration
    - 메모리가 빡빡하면 migration 중에 문제가 발생할 수도..
    - 있는 데이터 줄이기
      - 데이터를 일정 수준에서만 사용하도록 특정 데이터를 줄임 
      - 이미 swap 를 사용중이라면, 프로세스를 재시작 해야함
  - 메모리를 줄이기 위한 설정
    - 기본적으로 collection 들은 다음과 같은 자료구조를 사용
      - hash -> hashTable 을 하나 더 사용
      - sorted set -> skipList 와 hashTable 을 이용
      - set -> hashTable 사용
      - 해당 자료구조들은 메모리를 많이 사용함
    - zipList 를 이용하자
  - zipList 구조
    - in-memory 특성 상, 적은 개수라면 선형 탐색을 하더라도 빠르다.
    - list, hash, sorted set 등을 zipList 로 대체해서 처리 하는 설정이 존재한다.
- O(N) 관련 명령어는 주의하자
  - redis 는 single threaded: 단순한 get/set 의 경우, 초당 10만 TPS 이상 가능(CPU 속도에 영향을 받는다.)
  - single threaded 의 의미
    - 한 번에 하나의 명령만 수행 가능
    - 그럼 **긴 시간이 필요한 명령을 수행하면? - 망한다....**
      - keys
      - flushall, flushdb
      - delete collections
      - get all collections
  - 대표적인 실수 사례
    - key 가 백만개 이상인데 확인을 위해 keys 명령을 사용하는 경우 
    - 아이템이 몇만개 든 hash, sorted set, set 에서 모든 데이터를 가져오는 경우
  - keys 는 어떻게 대체할 것인가?
    - scan 명령을 사용하는 것으로 하나의 긴 명령을 짧은 여러번의 명령으로 바꿀 수 있다.
  - collection 의 모든 item 을 가져와야 할때는?
    - collection 의 일부만 가져오거나... sorted set
    - 큰 collection 을 작은 여러개의 collection 으로 나눠서 저장
      - 하나당 몇천개 안쪽으로 저장하는게 좋음
- Replication
  - async replication: replication lag 이 발생할 수 있다.
  - replicaof or slaveof 명령으로 설정 가능 
  - dbms 로 보면 statement replication 과 유사
  - replication 설정 과정 
    - secondary 에 replicaof or slaveof 명령을 전달
    - secondary 는 primary 에 async 명령 전달
    - primary 는 현재 메모리 상태를 저장하기 위해 fork
    - fork 한 프로세서는 현재 메모리 정보를 disk 에 dump
    - 해당 정보를 secondary 에 전달
    - fork 이후의 데이터를 secondary 에 계속 전달
  - redis replication 시 주의할 점
    - replication 과정에서 fork 가 발생하므로 메모리 부족이 발생할 수 있다.
    - redis-cli--rdb 명령은 현재 상태의 메모리 스냅샷을 가져오므로 같은 문제를 발생시킴
    - aws 나 클라우드의 redis 는 좀 다르게 구현되어서 좀더 해당 부분이 안정적
    - 많은 대수의 redis 서버가 replica 를 두고 있다면 네트워크 이슈나, 사람의 작업으로 동시에 replication 이 재시도 되도록 하면 문제가 발생할 수 있음
- 권장 설정 tip
  - maxClient 설정 50000
  - RDB/AOF 설정 off
  - 특정 commands disable
    - keys
    - aws 의 elasticCache 는 이미 하고 있음
  - 전체 장애의 90% 이상이 keys 와 save 설정을 사용해서 발생
  - 적절한 zipList 설정

### Redis 데이터 분산

- 데이터의 특성에 따라서 선택할 수 있는 방법이 달라진다
  - cache 일때는 우아한 redis?
  - persistent 해야 한다면 안 우아한 redis...
- 데이터 분산 방법
  - application
    - consistent hashing
      - hash 값을 이용해서 자기보다 크지만 가장 가까운 서버로 이동한다.
      - 서버가 추가되거나 제거되어도 해당 서버에서만 변동이 일어난다.
      - twemproxy 를 사용하는 방법으로 쉽게 사용 가능
    - sharding
      - 데이터를 어떻게 나눌 것인가? = 데이터를 어떻게 찾을것인가?
      - range
        - 그냥 특정 range 를 정의하고 해당 range 에 속하면 거기에 저장
        - 서버의 상황에 따라 놀고 있는 서버와 부하가 걸리는 서버가 극명하게 나뉜다.
      - modular
        - n % k 로 서버의 데이터를 결정
        - range 보다 데이터를 균등하게 분배할 가능성이 높다.
      - indexed
        - 해당 key 가 어디에 저장되어야 할지 관리하는 서버가 따로 존재
  - redis cluster
    - hash 기반으로 slot 16384 로 구분
    - 장점
      - 자체적인 primary, secondary failOver
      - slot 단위의 데이터 관리
    - 단점
      - 메모리 사용량이 더 많음
      - migration 자체는 관리자가 시점을 결정해야 함
      - library 구현이 필요함

### Redis FailOver

- coordinator 기반 failOver
  - zookeeper, etcd, consul 등의 coordinator 사용
  - coordinator 기반으로 설정을 관리한다면 동일한 방식으로 관리가 가능
  - 해당 기능을 이용하도록 개발이 필요하다.
- vip/dns 기반 failOver
  - 클라이언트에 추가적인 구현이 필요 없다.
  - vip 기반은 외부로 서비스를 제공해야 하는 서비스 업자에 유리(예를 들어 클라우드 업체)
  - dns 기반은 dns cache ttl 을 관리해야함
    - 사용하는 언어별 dns 캐싱 정책을 잘 알아야함
    - 툴에 따라서 한번 가져온 dns 정보를 다시 호출 하지 않는 경우도 존재
- redis cluster 사용
- monitoring factor
  - redis info 를 통한 정보
    - RSS: 물리 메모리를 얼마나 쓰고 있는지
    - used memory: 실제 redis 가 쓰고 있는 메모리
    - connection 수
    - 초당 처리 요청수
  - system
    - cpu
    - disk
    - network rx/tx
- cpu 가 100%를 칠 경우
  - 처리량이 매우 많다면?
    - 좀 더 cpu 성능이 좋은 서버로 이전
    - 실제 cpu 성능에 영향을 받음
      - 그러나 단순 get/set 은 초당 10만 이상 처리 가능
    - O(N) 계열의 특정 명령이 많은 경우
      - monitor 명령을 통해 특정 패턴을 파악하는 것이 필요
      - monitor 잘못쓰면 부하로 해당 서버에 더 큰 문제를 일으킬 수도 있음(짧게 쓰는게 좋음)

### 결론

- 기본적으로 redis 는 매우 좋은 툴
- 그러나 메모리를 빡빡하게 쓸 경우, 관리하기 어려움
  - 32기가 장비라면 24기가 이상 사용하면 장비 증설을 고려하는 것이 좋음
  - write 가 heavy 할 때는 migration 도 매우 주의해야 함
- redis as cache
  - cache 일 경우는 문제가 적게 발생
  - redis 가 문제가 있을 때 db 등의 부하가 어느정도 증가하는지 확인 필요
  - consistent hashing 도 실제 부하를 아주 균등하게 나누지는 않음. adaptive consistent hashing 을 이용해 볼 수도 있음
- redis as persistent store
  - 무조건 primary / secondary 구조로 구성이 필요함
  - 메모리를 절대로 빡빡하게 사용하면 안됨
    - 정기적인 migration 이 필요
    - 가능하면 자동화 툴을 만들어서 이용
  - RDB/AOF 가 필요하다면 secondary 에서만 구동