# [운영체제 공룡책 강의](https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98)

## 실습을 위한 개발 환경 구축 

- windows 경우는 wsl 설치 후 진행 
- ubuntu 기준으로 다음 커맨드 실행 
  - sudo apt-get update
  - sudo apt-get install build-essential gdb
    - build-essential 로 gcc, g++ 이 설치된다.

## O/S structures

- 정보의 최소 단위: bit(binary digit)
- 정보의 처리: 정보의 상태 변환(0에서 1로, 1에서 0으로)
- 부울 대수(Boolean Algebra): NOT, AND, OR
- 논리 게이트: NOT, AND, OR, XOR, NAND, NOR
- 논리 회로: IC, LSI, VLSI, ULSI, SoC ...
  - 무어의 법칙, 황의 법칙
- 정보의 저장과 전송: 플립-플롭, 데이터 버스, RF
- 컴퓨터는 만능인가?
  - 범용성: universality
    - NOT, AND, OR 게이트만으로 모든 계산을 할 수 있다.
    - NAND 게이트만으로 모든 계산을 할 수 있다.
    - 범용 컴퓨터: general-purpose computer
  - 계산가능성: computability
    - Turing-computable: 튜링 머신으로 계산가능한 것
    - Halting Problem(정지 문제): 튜링 머신으로 풀 수 없는 문제
- 컴퓨터는 누가 만들었나?
  - 컴퓨터의 할아버지
    - Alan Turing - Turing Machine
    - Head, Tape, Turing Machines, **Universal Turing Machine** 구조는 현대적 컴퓨터의 CPU, RAM, Application Programs, **Operating System**** 의 기초가 되었다.
    - Turing Machine 은 일종의 infinity 오토마타이다.
  - 컴퓨터의 아버지
    - John von Neumann - ISA: Instruction Set Architecture
    - **stored-program** computer 를 처음으로 설계했다.
    - 프로그램을 메모리 저장장치에 탑재하고 명령어들을 가져와서 CPU 가 하나씩 수행하는 구조
- 프로그램이란?
  - 프로그램은 명령어들의 집합이다.
  - 컴퓨터 하드웨어가 어떠한 태스크들을 실행하라고 명령하는 것
- 운영체제도 프로그램인가?
  - 운영체제는 컴퓨터에서 항상 실행되는 프로그램이다.
  - 시스템 서비스를 애플리케이션 프로그램에 제공한다.
  - processes, resources, user interfaces 등을 관리한다.
  - 컴퓨터 하드웨어와 유저간의 중개자 역할을 한다.
- modern computer system
  - 하나 이상의 CPU 들
  - 디바이스 컨트롤러들이 bus 를 통해 연결
- bootstrap program
  - 컴퓨터에 전원이 켜지면 처음 실행되는 프로그램
  - 하드디스크에 있는 OS 를 메모리에 로드한다.
- interrupts
  - cpu 와 IO 디바이스들이 통신하는 방식 
  - system bus 를 통해서 cpu 에게 시그널을 보낸다. 
- storage
  - registers -> cache -> main memory(RAM) -> solid-state disk(SSD) -> hard disk(HDD) -> optical disk -> magnetic tapes 순으로 속도가 빠르다.

## Processes

- 프로세스란?
  - 프로세스는 실행중인 프로그램을 말한다.
  - 프로세스는 운영체제의 작업 단위이다.
  - 프로세스는 작업을 수행하기 위해 특정 리소스가 필요하다.
    - CPU time, memory, files, I/O devices ...
- 프로세스의 메모리 레이아웃
  - Text section: 실행가능한 코드
  - Data section: 전역 변수
  - Heap section
    - 프로그램 실행 시간 동안 동적으로 할당되는 메모리
    - c 에서의 malloc, java 에서의 new 와 같은 것들
  - Stack section
    - 함수 호출 시 임시 데이터 저장
    - 함수 매개변수, 반환 주소, 지역 변수 ...
- 프로세스의 생명주기
  - New: 프로세스가 생성된 상태
  - Running: 프로세스가 CPU 를 점유해서 명령어를 로드하고 실행하는 상태
  - Waiting: 어떠한 이벤트(I/O 완료 또는 신호 수신)가 발생하기를 기다리는 상태
  - Ready: 프로세스가 할당되기를 기다리는 상태
  - Terminated: 프로세스가 종료된 상태
- PCB(Process Control Block) or TCB(Task Control Block)
  - 각 프로세스가 가져야할 정보를 운영체제에서 PBC 에 저장해놓고 핸들링한다.
  - PCB 가 가지고 있는 정보
    - Process state 
    - Program counter: 프로그램이 다음에 실행할 명령어의 주소를 가리킨다.
    - CPU registers
    - CPU-scheduling information
    - Memory-management information
    - Account information
    - I/O status information
- Context Switch
  - 프로세스의 컨텍스트는 PCB 에 표시된다.
  - interrupt 가 발생하면 프로세스가 다시 재개되었을 때 컨텍스트를 복원할 수 있도록 실행중인 프로세스의 현재 컨텍스트를 저장한다. 
  - context switch task
    - CPU 코어를 다른 프로세스로 전환한다.
    - 현재 프로세스의 상태 저장을 수행 및 다른 프로세스의 상태를 복원한다.
- In UNIX-like O/S
  - 새로운 프로세스는 fork() 라는 system call 에 의해 생성된다.
  - 자식 프로세스의 구성: 부모 프로세스의 주소 공간을 복사
  - 리턴코드가 0 이면 자식 프로세스 
  - 리턴코드가 0이 아닌 pid(OS 가 부여한 pid) 이면 부모 프로세스
- fork() system call
  - 부모 프로세스의 주소 공간은 그대로 복제하고, 부모 프로세스는 자기 할일을 계속 수행한다.
  - 부모 프로세스는 자식 프로세스가 실행중이라면 ready queue 에서 자식 프로세스가 끝날때까지 기다린다.
- 프로세스간 통신(IPC, Inter-Process Communication)
  - 협력하는 프로세스들은 IPC 매커니즘이 필요하다.(데이터를 주고 받는다.)
  - IPC 의 두가지 모델
    - shared memory
    - message passing
  - Producer-Consumer Problem
    - 예시
      - 컴파일러가 어셈블리코드를 생산하고 어셈블러가 소비한다.
      - 웹서버가 HTML 파일을 생산하고 브라우저가 소비한다.
    - 해결방법
      - shared-memory
        - 생산자와 소비자가 동시에 진행된다.
        - 버퍼를 공유하여 사용한다.(생산자는 버퍼에 채우고, 소비자는 버퍼에서 가져간다.)
        - 문제점: 개발자가 직접 메모리에 대한 제어를 다 해야 한다.
      - message-passing
        - OS 가 협력 프로세스를 제공한다.(send / receive)
  - Shared Memory: POSIX shared Memory
    - POSIX: Portable Operating System Interface(for unix)
  - Message Passing: Pipes
    - 유닉스 시스템의 초기 IPC 메커니즘 중 하나
  - client-server 시스템 간의 통신 
    - sockets: 통신을 위한 끝점!(ip + port)
    - RPCs(Remote Procedure Calls): 네트워크 시스템의 프로세스 간의 프로시저 호출을 추상화
      - IPC 는 하나의 서버에서 프로세스간 통신. RPC 는 원격지 서버 프로세스간 통신 

## Thread & Concurrency

- Amdahl`s Law(암달의 법칙)
  - 코어는 무조건 많을수록 좋은가?
  - serially 하게 수행되어야 하는 작업이 많을수록 코어가 많아도 속도는 그렇게 좋아지지 않는다.
  - for example
    - S: serially 하게 수행되어야 하는 작업 비뮬
    - N: 프로세스 코어의 수
      - S=0.25, N=2, speedup=1.6
      - S=0.25, N=4, speedup=2.28

## CPU Scheduling

- CPU scheduler
  - 실행할 준비가 되어 있는 프로세스에 CPU 를 할당한다.
  - 어떻게 다음 프로세스를 선택할 것인가?
    - Linked List or Binary Tree
    - FIFO Queue
    - Priority Queue
- preemptive(선점형) vs Non-preemptive(비선점형)
  - Non-preemptive scheduling: 프로세스가 끝나거나 스위치하기 전까지 CPU 를 유지한다.
  - Preemptive scheduling: 스케줄러가 선점하고 있는 프로세스를 쫓아낼 수 있다.
- Decision-Making for CPU-scheduling
  - 프로세스가 running to waiting state
  - 프로세스가 running to ready state
  - 프로세스가 waiting to ready state
  - 프로세스가 terminates
  - 1 & 4 : non-preemptive
  - 2 & 3: preemptive or non-preemptive
- dispatcher
  - CPU 코어를 제어하는 모듈, CPU 스케줄러가 선택한 프로세스를 넘겨준다.
- context switch 가 얼마나 자주 일어나는지 확인하기
  - $vmstat 1 3 (1초간격으로 3번 출력)
  - $cat /proc/1/status
    - voluntary_ctxt_switches, nonvoluntary_ctxt_switches
- 스케줄 알고리즘
  - FCFS: First-Come. First-Served
    - FIFO queue 로 구현
    - non-preemptive
    - convoy effect(똥차 효과?, 똥차에 막혀서 슈퍼카가 다 막혀있다...) 로 좋은 효율을 얻을 수 없다.
  - SJF: Shortest Job First(SRTF: Shortest Remaining Time First)
    - 평균 대기 시간 측면에서는 최적의 솔루션이다. 하지만... 구현을 할 수 없다. next CPU burst 타임을 알 수 없기 때문에...
    - 이전 CPU burst 를 보고 예측을 해서 근사적으로 구현한다.  
  - RR: Round-Robin
    - preemptive FCFC with 시분할
    - 시분할은 보통 10 ~ 100 ms 
    - ready queue 는 circular queue 로 처리
    - 시분할 사이즈에 따라 성능이 달라진다.
  - Priority-based
    - 우선순위가 같으면 FCFS 로 
    - 우선순위가 낮으면 무기한 대기하는 프로세스가 생긴다.
      - aging 을 줘서 우선 순위를 높인다.
  - MLQ: Multi-Level Queue
    - 분리된 ready queue 에 우선 순위를 부여한다.
  - MLFQ: Multi-Level Feedback Queue
- 현대적인 OS 에서는 kernel thread 를 사용해서 스케줄링 한다.

## Synchronization Tools

- Race Condition
  - 프로세스 여러개가 실행 될때 공유 데이터에 동시에 접근하고 사용하는 경우  
  - 실행결과는 액세스가 발생하는 특정 순서에 따라 다르다.
- 임계 영역 문제(critical section problem)
  - 여러개의 프로세스가 있을때 공유된 자원에 접근하는 코드 영역을 critical section 이라고 한다.
  - 데이터를 엑세스하고 변경할 때, 이 데이터가 다른 프로세스들과 공유되어 있는 경우
- critical section 을 해결하기 위한 요구 사항
  - mutual exclusion(상호배제): 어떠한 프로세스가 임계 영역에서 실행중일때 다른 프로세스들은 임계 영역에 진입할 수 없다.
  - progress(avoid deadlock): 모든 프로세스가 임계 영역에 접근 할수 없으면 안된다.
  - bounded waiting(starvation(굶주림, 기아)): 다른 프로세스가 임계 영역에 들어갈 수 있는 횟수나 시간에 대한 경계가 있어야 한다.(한정 대기)
- critical section 해결 솔루션
  - peterson`s 알고리즘
    - 두 개의 프로세스를 실행하면서 flag 를 통해 critical section 과 remainder section 을 왔다갔다 한다.
    - 완벽하지는 않다. 그냥 이론적으로만 알고 있자. 
- Atomicity(원자성): 더이상 쪼갤 수 없는(uninterruptible) 작업 단위
- Mutex Lock
  - 2개의 프로세스간의 critical section 해결에 사용
  - 크리티컬 섹션을 보호하고 경쟁 상태를 방지하기 위해 lock(열쇠)을 사용한다.
  - 프로세스는 임계 영역에 들어가기 전에 lock 을 획득해야 한다.
  - 다 사용하고 나면 lock 을 반납한다.
- Semaphore(신호장치, 신호기)
  - n 개의 프로세스간의 critical section 해결에 사용
  - 두 개의 표준 원자 연산을 통해서만 액세스한다.
  - wait(), signal() 또는 P(), V()
  - N 개의 S 라는 변수를 초기화(S=M) 시켜놓고, S=0 이 되면 더이상 진입이 불가능하게 한다.
  - semaphore 의 범위가 0,1(binary) 이면 결국 mutex lock 과 비슷하다.
- monitor
  - mutex lock, semaphore 보다 상위 수준의 동기화 tool 
  - 프로그래머가 정의한 하나의 구조체 또는 클래스
  - 자바에서는 monitor-lock 또는 intrinsic-lock 이라고 불리는 monitor 를 제공한다.
    - synchronized
      - 임계영역에 해당하는 코드 블록을 선언할 때 사용하는 자바 키워드
      - 해당 코드 블록에는 모니터락을 획득해야 진입 가능 
      - 모니터락을 가진 객체 인스턴스를 지정할 수 있음
      - 메소드에 선언하면 메소드 코드 블록 전체가 임계영역으로 지정된다. 이때, 모니터락을 가진 객체 인스턴스는 this 객체 인스턴스다.
    - wait(), notify() method
      - java.lang.Object 클래스에 선언됨
      - 쓰레드가 어떤 객체의 wait() 메소드를 호출하면 해당 객체의 모니터락을 획득하기 위해 대기 상태로 진입
      - 쓰레드가 어떤 객체의 notify() 메소드를 호출하면 해당 객체 모니터에 대기중인 쓰레드 하나늘 깨운다.
      
## Deadlocks

- 프로세스 집합의 모든 프로세스가 대기 중인 상태
- deadlock 의 네가지 조건
  - mutual exclusion: 하나 이상의 리소스가 non-sharable mode 로 유지된다.
  - hold and wait(점유 대기): 스레드가 적어도 하나의 리소스를 보유하고 다른 스레드가 보유한 추가 리소스 획득을 기다린다.
  - no preemption: 자원을 선점할 수 없을 때
  - circular wait: 순환 대기 상태
- 데드락을 방지하는것은 비효율적이다. 회피하도록 하자. 
- 데드락 회피
  - 시스템이 각 요청에 대해 미래의 교착 상태를 피하기 위해 기다려야 할지 여부를 결정해야 한다.
  - 그러기 위해서는 리소스가 어떻게 요청되는지에 대한 추가적인 정보가 더 필요하다.
  - 다음과 같은 사전 정보가 주어지면 데드락 회피를 위한 알고리즘을 구성하는 것이 가능하다.
    - 필요한 각 유형의 최대 리소스 수
    - 사용 가능하고 할돵된 리소스 수
    - 스레드의 최대 요구 수
- Safe State
  - 시스템이 각 스레드에 리소스를 최대값까지 할당할 수 있는 경우
  - 스레드의 실행 순서가 있다면 데드락을 회피할 수 있다.
  - safe sequence 가 있다면 시스템은 safe state 이다.
- Banker`s 알고리즘
  - 데이터 구조
    - n 을 시스템의 스레드 수, m 을 자원 타입의 수라고 했을 때
    - available: 사용 가능한 리소스 타입의 수를 가지고 있는 vector
    - max: 각 스레드가 앞으로 요청할 리소스 인스턴스의 최대 수 
    - allocation: 현재 각 스레드에 할당된 리소스 수
    - need: 각 스레드에 필요한 나머지 리소스 수

## Main Memory

- 메모리란?
  - 각각 고유한 주소를 가진 거대한 바이트 배열
  - CPU 는 프로그램 카운터를 사용하여 메모리에서 명령을 가져오고, 명령어는 메모리에서 로드 및 저장을 한다.
- 주소 바인딩
  - 프로그램은 바이너리 실행 파일로 디스크에 상주하고, 프로그램을 실행하려면 메모리로 가져와야 한다.
  - 소스 코드 상에서의 메모리 주소 바인딩은 컴파일러가 알아서 한다.
  - 링커 또는 로더가 차례로 바인딩한다.
- 논리 vs 물리 주소 공간
  - 논리 주소: CPU 에서 생성한 주소
  - 물리 주소: 메모리 유닛이 보는 주소
- MMU(Memory Management Unit): 논리적 주소에서 물리적 주소로 매핑하는 하드웨어 장치
- DLLs(Dynamically Linked Libraries): 프로그램이 실행될 때 사용자 프로그램에 연결된 시스템 라이브러리
- 페이징의 기본 방법
  - 물리적 메모리를 고정 크기 블록(프레임)으로 나눈다.
  - 논리적 메모리를 동일한 크기(페이지)의 블록으로 나눈다.
  - 논리적 주소 공간은 물리적 주소 공간과 완전히 분리된다.

## Virtual Memory

- 가상메모리란?: 어떠한 프로세스의 실행을 메모리에 다 올리지 않아도(물리적 메모리보다 큰 경우에도)  실행 할 수 있도록 해주는 기술
- 가상 주소 공간
  - 논리적인 프로세스의 메모리 구조
  - 파일과 메모리의 공유가 쉬워진다.(page sharing 을 통해서)
- demand paging: 필요할때만 페이징을 요청한다.
  - free frame 이 없다면?: 페이지 교체 알고리즘을 사용(LRU)

## Storage Management

- HDD 스케줄링 목표
  - 액세스 시간을 최소화
  - 데이터 전송 대역폭을 최대화
  - seek time 감소
- Boot Block
  - 전원이 켜졌을 때 컴퓨터가 실행을 시작하려면 초기 프로그램이 있어야 한다.
  - 부트스트랩 로더는 NVM 플래시 메모리에 저장되며, 알려진 메모리 위치에 매핑된다.
- RAID(Redundant Arrays of Independent Disks)
  - 다양한 디스크 구성 기술의 모음
  - 데이터를 읽거나 쓸 수 있는 속도를 개선한다.
  - 데이터 저장의 신뢰성을 향상시킨다.
  • 하나의 드라이브에 장애가 발생해도 데이터 손실을 방지할 수 있다.
- RAID levels
  - mirroring(미러링): 매우 안정적이지만 너무 비싸다.
  - striping(스트라이핑): 효율성이 높지만 안정성이 떨어진다.
  - parity bit(패리티 비트)
    - 1로 설정: 바이트의 비트 수가 짝수
    - 0으로 설정: 바이트의 비트 수가 홀수
    - 비트 중 하나가 손상된 경우 오류를 감지
- I/O 의 3가지 타입 
  - polling or busy-waiting: busy 비트가 지워질 때까지 상태 레지스터를 반복적으로 읽는다.
  - interrupt
    - CPU 가 interrupt-request line 이라는 하드웨어적인 선을 가지고 있다. 
    - CPU 가 interrupt 를 디텍션하고, ISR(Interrupt Service Routine) 에게 인터럽트 제어권을 넘겨준다.
  - DMA: Direct Memory Access
    - 프로그래밍된 I/O(한 번에 1바이트)를 방지하는 데 사용
    - 대용량 데이터 전송을 처리하는 데 유용하다.
- Blocking I/O vs Non-blocking I/O
  - Blocking I/O
    - 스레드가 일시 중단된다.
    - running 상태에서 waiting 상태로 간다.
  - Non-blocking I/O
    - 스레드 실행을 중단하지 않는다.
    - 사용 가능한 만큼 반환
  - 비동기 호출: 스레드는 계속해서 코드를 실행한다.
- non-blocking and asynchronous system call 차이점 
  - non-blocking read() call
    - 사용 가능한 모든 데이터와 함께 즉시 반환
  - asynchronous read() call
    - transfer 요청을 하고 바로 자기 할일을 한다.
    
## Security & Protection

- 두 가지 주요 유형의 암호화 알고리즘
  - 대칭키
    - 암호화 및 복호화에 동일한 키가 사용
    - 키는 꼭 보호 되어야 한다.
    - DES, AES
  - 비대칭
    - 암호화 및 복호화에 다른 키를 사용한다. 공개 키와 개인 키
    - 예) 은행 계좌번호와 비밀번호
    - RSA