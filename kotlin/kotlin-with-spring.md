# spring 에서 kotlin 사용하기

- 필드 주입 방법
  - 다음의 코드에서 필드 주입은 동작하지 않는다. 필드 타입들이 null 이 될 수 없기 때문에 컴파일 타임에서 에러가 발생한다.
    - ~~~kotlin
      @Controller
      class ArticleController {
          @Autowired
          private var articleService : ArticelService
       
          @Autowired
          private var tagService : TagService
      }
      ~~~
  - 생성자 주입으로 변경. 변수는 val 로 해야 한다. val 는 변경 불가능한(immutable) 참조를 지정하는 변수다.
    - ~~~Kotlin
      @Controller
      class ArticleController(
          private val articleService : ArticelService
          private val tagService : TagService) {
      }
      ~~~
  - 필드 주입이 필요하면 지연 초기화를 사용 한다. lateinit 변경자를 붙이면 프로퍼티를 나중에 초기화 할 수 있다. 이때 이 프로퍼티는 항상 var 여야 한다.
    - ~~~kotlin 
      @Controller
      class ArticleController {
          @Autowired
          private lateinit var articleService : ArticelService

          @Autowired
          private lateinit var tagService : TagService
      }
      ~~~
- @RequestParam
  - @RequestParam 의 필수 파라미터를 나타낼 때 required 속성을 사용하지 않음
    - ~~~ kotlin
      @RequestMapping("/")
      @Controller
      class SearchController {
          @GetMapping
          fun search(@RequestParam(required = true) keyword : String?) {
              return "/result"
          }
      ~~~
  - 위의 keyword 라는 값을 필수 값으로 만들기 위해서는 null 이 불가능한 타입으로 바꿔줘야 한다.
    - ~~~ kotlin
      @RequestMapping("/")
      @Controller
      class SearchController {
          @GetMapping
          fun search(@RequestParam keyword : String) {
              return "/result"
          }
      ~~~
- java 의 static 키워드 대체하기
  - companion object 를 사용하여 정적 필드와 메소드를 정의
    - ~~~java
      public class Article {
          public static final String DEFAULT_THUMBNAIL = "..";
         
          public static String thumbnail() {
              ...
          }
      ~~~
    - ~~~kotlin
      class Article {
          companion object {
              val DEFAULT_THUMBNAIL = ".."
              
              fun thumbnail(): String {
                  ...
              }
          }
      ~~~
    - kotlin 코드를 자바에서 참조해야 한다면 다음과 같이 const, @JvmStatic 키워드를 추가해야 한다.
    - ~~~kotlin
      class Article {
          companion object {
              const val DEFAULT_THUMBNAIL = ".."
              
              @JvmStatic
              fun thumbnail(): String {
                  ...
              }
          }
      ~~~
-   

### 참조 사이트

- [PAYCO 매거진 서버 Kotlin 적용기](https://www.youtube.com/watch?v=wiJqu7xoH58)
- [어디 가서 코프링 매우 알은 체하기! : 9월 우아한테크세미나](https://www.youtube.com/watch?v=ewBri47JWII&list=PLgXGHBqgT2TtGi82mCZWuhMu-nQy301ew)