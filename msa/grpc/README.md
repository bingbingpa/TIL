# IPC(Inter-Process Communication) 관련 비교 분석하기 

- 프로세스간 통신하는 방법론을 통칭하여 IPC(Inter Process Communication) 라고 한다.
- 내부 서버끼리 통신에 rest api 가 아닌 다른 것을 도입하기 위해 이것저것 조사해서 정리해본다.

### gRPC(google Remote Procedure Call)

- rpc 란?
  - 이름 그대로 네트워크로 연결된 서버 상의 프로시저(함수, 메서드 등) 를 원격으로 호출할 수 있는 기능이다.
  - 네트워크 통신을 위한 작업 하나하나 챙기기 귀찮으니 통신이나 call 방식에 신경쓰지 않고 원격지의 자원을 내 것처럼 사용하는 것이다.
  - IDL(Interface Definication Language) 기반으로 다양한 언어를 가진 환경에서도 쉽게 확장이 가능하다.
    - 지원 언어: C++, Java, Python, Ruby, Node.js, C#, Go, PHP, Objective-C …
  - RPC 의 핵심 개념 Stub(스텁)
    - 서버와 클라이언트는 서로 다른 주소 공간을 사용 하므로, 함수 호출에 사용된 매개 변수를 꼭 변환해줘야 하는데 이걸 담당하는게 스텁이다.
    - client stub 은 함수 호출에 사용된 파라미터의 변환(Marshalling, 마샬링) 및 함수 실행 후 서버에서 전달 된 결과의 변환을 담당한다.
    - server stub 은 클라이언트가 매개 변수의 역변환(Unmarshalling, 언마샬링) 및 함수 실행 결과 변환을 담당하게 된다.
  - ![rpc](rpc.png)
    - `1)` IDL 을 사용하여 호출 규약을 정의한다.
      - 함수명, 인자, 반환값에 대한 데이터형이 정의된 IDL 파일을 rpcgen 으로 컴파일하면 stub code 가 자동으로 생성된다.
    - `2)` Stub Code 에 명시된 함수는 원시코드의 형태로, 상세 기능은 server 에서 구현된다.
      - 만들어진 stub 코드는 클라이언트/서버에 함께 빌드한다.
    - `3)` client 에서 stub 에 정의된 함수를 사용할 때,
    - `4)` client stub 은 RPC runtime 을 통해 함수 호출하고
    - `5)` server 는 수신된 procedure 호출에 대한 처리 후 결과 값을 반환한다.
    - `6)` 최종적으로 Client 는 Server 의 결과 값을 반환 받고, 함수를 Local 에 있는 것처럼 사용할 수 있다.
  - 구현의 어려움/지원 기능의 한계 등으로 제대로 활용되지 못했다.
- gRPC(google Remote Procedure Call)
  - google 에서 개발한 오픈소스 RPC 프레임워크
  - PB 기반 Serializer 에 HTTP/2 를 결합하여 만든 프레임워크
  - **REST 와 비교 했을때 가장 두드러진 차이점은 HTTP/2 를 사용한다는 것과 프로토콜 버퍼로 데이터를 전달한다는 점이다.**
    - Proto File 만 배포하면 환경과 프로그램 언어에 구애받지 않고 서로 간의 데이터 통신이 가능하다.
  - HTTP/2
    - http/1.1 은 기본적으로 클라이언트의 요청이 올때만 서버가 응답을 하는 구조로 매 요청마다 connection 을 생성해야 한다. 
    - cookie 등 많은 메타 정보들을 저장하는 무거운 header 가 요청마다 중복 전달되어 비효율적이고 느린 속도를 보여준다.
    - http/2 에서는 한 connection 으로 동시에 여러 개 메시지를 주고 받으며, header 를 압축하여 중복 제거 후 전달하기에 version1 에 비해 훨씬 효율적이다.
    - 필요 시 클라이언트 요청 없이도 서버가 리소스를 전달할 수도 있기 때문에 클라이언트 요청을 최소화 할 수 있다.
      ![http2](http2.png)
  - ProtoBuf(Protocol Buffer)
    - google 에서 개발한 구조화된 데이터를 직렬화(Serialization) 하는 기법이다.
    - 직렬화 된 protocol buffer 는 필드 번호, 필드 유형 등을 1byte 로 받아서 식별하고, 주어진 length 만큼만 읽도록 하기 때문에 text 기반인 JSON 보다 더 작은 byte 만 있으면 된다. 
      ![protoBuf](protoBuf.png)
- [gRPC vs RSocket 벤치마크](https://dzone.com/articles/rsocket-vs-grpc-benchmark)

### 참고

- [시대의 흐름, gRPC 깊게 파고들기](https://medium.com/naver-cloud-platform/nbp-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EC%8B%9C%EB%8C%80%EC%9D%98-%ED%9D%90%EB%A6%84-grpc-%EA%B9%8A%EA%B2%8C-%ED%8C%8C%EA%B3%A0%EB%93%A4%EA%B8%B0-1-39e97cb3460)


