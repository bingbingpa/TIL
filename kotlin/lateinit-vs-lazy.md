# kotlin lateinit 와 lazy 정리 

- kotlin 에서의 늦은 초기화 방법
  - 가장 단순하게 
    - ~~~kotlin
      var str: String? = null
      ~~~
    - 이렇게 하면 null 이 가능한 타입을 사용해야 하는 위험이 있다. 
  - lateinit
    - ~~~kotlin
      lateinit var str: String
      ...
      str = "test"
      ~~~
    - lateinit 는 변수를 var 로 선언해야 한다. 그렇기 때문에 늦은 초기화 이후에도 값이 계속하여 바뀔 수 있다.
    - 만약 lateinit 을 사용하고 늦은 초기화조차 하지 않은 경우는 컴파일 단계에서 오류가 발생한다.
    - **lateinit 의 경우에는 무조건 var 를 사용해야 하며, Primitive type 에는 사용할 수 없다.**
  - by lazy
    - ~~~kotlin
      lateinit var text: String
      val textLength: Int by lazy { text.length }
      ...
      text  = "ABC"
      println(textLength)
      ~~~
    - by lazy 는 선언 당시에는 초기화를 할 방도가 없지만, 이후에 의존하는 값들이 초기화 된 이후에 값을 채워 넣고 싶을 때 사용한다.
    - 즉, 호출시 이를 어떻게 초기화를 해줄 지에 대하여 정의할 수 있는 구문이다.
- 비교
  - 값 변경 가능 여부
    - lateinit: 가능(var 사용)
    - by lazy: 불가능(val 사용)
  - 용법 구분
    - lateinit: 초기화 이후에 계속하여 값이 바뀔 수 있을 때
    - by lazy: 초기화 이후에 읽기 전용 값으로 사용할 때