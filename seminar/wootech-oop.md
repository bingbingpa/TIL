# [[우아한테크세미나] 우아한객체지향](https://www.youtube.com/watch?v=dJ5C4qRqAgA&list=WL&index=13)

- flow chart 그리기 참고 
  - [mermaid](https://mermaid-js.github.io/mermaid)

### 의존성

- 설계란?
  - 코드를 어떻게 배치할 것인가에 대한 의사 결정
  - **핵심은 변경에 초점을 맞추는 것이 중요하다.**
- 의존성(dependency)
  - 변경에 의한 영향
  - B 가 변경될 때 A 도 함께 변경될 가능성이 있다.
  - ~~~mermaid
    flowchart LR
    A-. dependens-on .-> B
    ~~~
- 클래스 의존성의 종류
  - Association(연관관계)
    - A 에서 B 로 갈수 있는 영구적인 경로가 있는 것
    - ~~~mermaid
      flowchart LR
      A --> B
      ~~~
    - ~~~kotlin
      class A {
          private val b: B
      }
      ~~~
  - Dependency(의존관계)
    - 일시적으로 협력을 하는 어떤 시점에 일시적으로 관계를 맺고 헤어지는 관계
    - ~~~mermaid
      flowchart LR
      A-.-> B
      ~~~
    - ~~~kotlin
      class A {
          fun method(b: B): B {
              return B()
          }
      }
      ~~~
  - Inheritance(상속관계)
    - B 라는 클래스의 구현을 A 가 상속한다. 
    - 구현이 바뀌면 영향을 받는다.
    - ~~~mermaid
      flowchart LR
      A --> B 
      ~~~
    - ~~~kotlin
      class A : B() {
      }
      ~~~
  - Realization(실체화 관계)
    - 인터페이스의 오퍼레이션 시그니처가 변경되었을 때만 영향을 받는다.
    - ~~~mermaid
      flowchart LR
      A-.->B
      ~~~
    - ~~~kotlin
      class A : B {
      }
      ~~~
- 좋은 의존성을 관리하기 위한 몇가지 규칙 
  - 양방향 의존성을 피하라 
    - Bi-Directional(양방향) X
      - ~~~mermaid
        flowchart LR
        A --> B
        B --> A
        ~~~
    - ~~~kotlin
      class A {
          private val b: B
        
          fun setA(b: B) {
              this.b = b
              this.b.setA(this)
          }
      }
      class B {
          private val a: A
        
          fun setA(a: A) {
              this.a = a
          }
      }
      ~~~
    - Uni-Directional(단방향) O
      - ~~~mermaid
        flowchart LR
        A --> B
        ~~~
      - ~~~kotlin
        class A {
            private val b: B
            
            fun setA(b: B) {
                this.b = b
            }
        
        class B
        ~~~
  - 다중성이 적은 방향을 선택하라
    - One-To-Many(일대다) X
      - ~~~mermaid
        flowchart LR
        A:1 --> B:N
        ~~~
      - ~~~kotlin
        class A {
            private val bs: Collection<B>
        }
        
        class B
        ~~~
    - Many-To-One(다대일) O
      - ~~~mermaid
        flowchart RL
        B:N --> A:1
        ~~~
      - ~~~kotlin
        class A
        
        class B {
            private val a: A
        }
        ~~~
  - 의존성이 필요 없다면 제거하라
  - 패키지 사이의 의존성 사이클을 제거하라

### 예제 살펴보기

- [https://github.com/eternity-oop/Woowahan-OO-01-object-reference](https://github.com/eternity-oop/Woowahan-OO-01-object-reference)
- **연관관계**: 협력을 위해 필요한 **영구적인** 탐색 구조
  - = 탐색가능성(navigability)
  - 두 객체 사이에 협력이 필요하고 두 객체의 관계가 영구적이라면 **연관관계**를 이용해 탐색 경로 구현
  - Order 가 뭔지 알면 Order 를 통해 원하는 OrderLineItem 을 찾을 수 있다.
    - ~~~mermaid
      flowchart LR
      Order --> OrderLineItem 
      ~~~
  - **메세지를 결정하고 메소드를 만들어야 한다.**
- **의존관계**: 협력을 위해 **일시적으로** 필요한 의존성(파라미터, 리턴타입, 지연변수)

### 설계 개선하기

- [https://github.com/eternity-oop/Woowahan-OO-02-domain-service](https://github.com/eternity-oop/Woowahan-OO-02-domain-service)
- 설계를 진화 시키기 위한 출발점
  - 코드 작성 후 의존성 관점에서 설계 검토 
  - 의존 관계를 직접 종이에 그려보자.
- 두 가지 문제 
  - 객체 참조로 인한 결합도 상승
    - 성능 문제 
      - 어디까지 조회할 것인가?
      - 객체 그룹의 조회 경계가 모호
    - 수정 시 도메인 규칙을 함께 적용할 경계는?
      - Order 의 상태를 변경할 때 연관된 도메인 규칙을 함께 적용해야하는 객체의 범위는?
      - (다른말로) 트랙잭션 경계는 어디까지인가?
        - 어떤 테이블에서 어떤 테이블까지 하나의 단위로 lock 을 설정할 것인가?
    - 객체 참조의 문제점
      - 모든 객체가 연결돼 있기 때문에 어떤 객체라도 함께 수정 가능
    - 어떤 객체들을 묶고 어떤 객체들을 분리할 것인가?
      - **함께 생성되고 함께 삭제되는 객체들을 함께 묶어라**
      - 도메인 제약사항을 공유하는 객체들을 함께 묶어라
      - 가능하면 분리하라
  - 패키지 의존성 사이클
- 수정 방안
  - 중간 객체를 이용한 의존성 사이클 끊기. 변하지 않는 데이터로 추상적인 중간 객체를 만든다. DIP 의 변형 같은 느낌?
  - 인터페이스나 추상 클래스를 통해 추상화를 하고 의존성을 역전
  - 새로운 패키지 추가

### 의존성과 시스템 분리

- [https://github.com/eternity-oop/Woowahan-OO-03-domain-event](https://github.com/eternity-oop/Woowahan-OO-03-domain-event)
- domain event 를 통한 협력