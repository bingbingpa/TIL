# api gateway kong

### 주요 기능 

- Nginx + Cassandra + Lua script 기반
- 밀리 초 미만의 처리 대기 시간 지원 및 높은 처리량, 고성능
- Lua 또는 GoLang 에서 확장성이 좋다.
- 플러그인을 통해 로드밸런싱, 로깅, 인증, 속도제한, 변환등을 제공 
- 모든 플랫폼, 모든 클라우드에서 실행이 가능하고 kubernetes 를 기본적으로 지원한다.
- 모든 기능을 rest interface 로 제공한다.

### 필요한 기능들 

- 설정
  - postgres, cassandra 또는 db 없이 config file 사용
    - db-less mode 는 Kong 1.1 이후 버전에서 사용 가능
      - 장점
        - kong 설정 전체를 단일 YAML(또는 JSON) 파일에 저장하여 관리가 편하다.
        - 설정 파일을 사용함으로서 배포 파이프라인을 자동화 할 수 있다.
        - DB 를 사용하지 않아서 리소스를 절약할 수 있다.
      - 단점
        - 일부 DB 에 의존적인 플러그인을 사용할 수 없다.
          - rest api 를 이용해 kong cluster 를 관리할 수 있는 Admin API 를 사용할 수 없다.(읽기용도로만 사용 가능)
          - 설정을 변경하기 위해서는 설정파일을 수정한 후 재배포해야 한다.
          - acl, basic-auth, hmac-auth, jwt, key-auth 등은 일부 기능이 사용 불가능 하게 되며, DB 기반으로 동작하는 rate-limiting, response-ratelimiting 플러그인은 사용할 수 없게 된다.
      
- 라우팅
- 인증
- 로깅
- 서버 콜시에 프로토콜