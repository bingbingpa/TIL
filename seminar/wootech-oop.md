# [[우아한테크세미나]우아한객체지향](https://www.youtube.com/watch?v=dJ5C4qRqAgA&list=WL&index=13)

- 우아한 객체지향 샘플
  - [https://github.com/eternity-oop/Woowahan-OO-01-object-reference](https://github.com/eternity-oop/Woowahan-OO-01-object-reference)
  - [https://github.com/eternity-oop/Woowahan-OO-02-domain-service](https://github.com/eternity-oop/Woowahan-OO-02-domain-service)
  - [https://github.com/eternity-oop/Woowahan-OO-03-domain-event](https://github.com/eternity-oop/Woowahan-OO-03-domain-event)

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

### 설계 개선하기

### 의존성과 시스템 분리