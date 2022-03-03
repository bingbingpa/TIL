# [Kotlin Koans](https://kotlinlang.org/docs/koans.html)

- 온라인으로 학습하기 https://play.kotlinlang.org/koans/overview 
- intellij 플러그인으로 학습하기
  [EduTools 플러그인 설치](https://www.jetbrains.com/help/education/install-edutools-plugin.html?section=IntelliJ%20IDEA)
  및 [Kotlin Koans 과정 선택](https://www.jetbrains.com/help/education/learner-start-guide.html?section=Kotlin%20Koans)

<br>

## I. Introduction

### 1. Hello, world!

~~~kotlin
fun start(): String = TODO() 
~~~

<details>
  <summary> start 함수가 "OK" 를 리턴하도록 변경하기 </summary>

~~~kotlin 
fun start(): String = "OK"
~~~  

</details>

- [function syntax](https://kotlinlang.org/docs/basic-syntax.html#functions)
  - 두 개의 Int 파라미터와 Int 값을 리턴하는 경우  
    - ~~~kotlin
      fun sum(a: Int, b: Int): Int {
          return a + b
      } 
      ~~~
  - 함수 본문은 표현식으로 쓸 수 있고, 리턴 타입은 생략 할 수 있다. 
    - ~~~kotlin
      fun sum(a: Int, b: Int) = a + b
      ~~~
  - 반환 타입이 없는 경우는 Unit 을 사용한다.
    - ~~~kotlin
      fun printSum(a: Int, b: Int): Unit {
          println("sum of $a and $b is ${a + b}")
      } 
      ~~~
  - Unit 리턴 타입은 생략 할 수 있다.
    - ~~~kotlin
      fun printSum(a: Int, b: Int) {
          println("sum of $a and $b is ${a + b}")
      }
      ~~~

### 2. Named arguments

~~~kotlin
fun joinOptions(options: Collection<String>) =
        options.joinToString(TODO())
~~~

<details>
  <summary> 두 개의 인수를 지정하여 함수 joinOptions() 가 JSON 포맷(예: [a, b, c])으로  반환하기  </summary>

~~~kotlin 
fun joinOptions(options: Collection<String>) =
    options.joinToString(
        separator = ", ",
        prefix = "[",
        postfix = "]"
    )
~~~  

</details>

- [Named arguments](https://kotlinlang.org/docs/functions.html#named-arguments)
  - 함수를 호출할 때 하나 이상의 인수에 이름을 지정 할 수 있다.
  - 함수 호출에서 인수를 사용할 때 나열된 순서를 자유롭게 변경 할 수 있으며, 인수를 생략하면 기본값을 사용한다.
  - ~~~kotlin
    fun reformat(
        str: String,
        normalizeCase: Boolean = true,
        upperCaseFirstLetter: Boolean = true,
        divideByCamelHumps: Boolean = false,
        wordSeparator: Char = ' ',
    ) { /*...*/ }
    ~~~
  - 위의 함수를 호출할 때 모든 인수의 이름을 지정할 필요는 없다.
    - ~~~kotlin
      reformat(
          "String!",
          false,
          upperCaseFirstLetter = false,
          divideByCamelHumps = true,
          '_'
      )
      ~~~
  - 기본값으로 여러개의 항목을 skip 할 수 있다.
    - ~~~kotlin
      reformat("This is a long String!")
      ~~~
  - 특정 인수를 모두 생략하는 대신 기본값을 사용할 수 있다. **그러나 건너뛴 첫 번째 인수 뒤에는 모든 후속 인수의 이름을 지정해야 한다.**
    - ~~~kotlin
      reformat("This is a short String!", upperCaseFirstLetter = false, wordSeparator = '_')
      ~~~
  - spread 연산자를 사용하여 이름과 함께 가변 개수의 인수를 전달 할 수 있다.
    - ~~~kotlin
      fun foo(vararg strings: String) { /*...*/ }

      foo(strings = arrayOf("a", "b", "c"))
      ~~~

### 3. Default arguments

~~~kotlin
fun foo(name: String, number: Int, toUpperCase: Boolean) =
        (if (toUpperCase) name.uppercase() else name) + number

fun useFoo() = listOf(
        foo("a"),
        foo("b", number = 1),
        foo("c", toUpperCase = true),
        foo(name = "d", number = 2, toUpperCase = true)
)
~~~
~~~java
public String foo(String name, int number, boolean toUpperCase) {
    return (toUpperCase ? name.toUpperCase() : name) + number;
}
public String foo(String name, int number) {
    return foo(name, number, false);
}
public String foo(String name, boolean toUpperCase) {
    return foo(name, 42, toUpperCase);
}
public String foo(String name) {
    return foo(name, 42);
}
~~~

<details>
  <summary> 위의 자바로 작성된 4개의 메소드 오버로드를 대체하는 foo 함수 작성하기  </summary>

~~~kotlin 
fun foo(name: String = "a", number: Int = 42, toUpperCase: Boolean = false) =
        (if (toUpperCase) name.uppercase() else name) + number
~~~  

</details>

- [default arguments](https://kotlinlang.org/docs/functions.html#default-arguments) 
  - 함수 매개변수는 해당 인수를 건너뛸 때 사용되는 기본값을 가질 수 있다.
    - ~~~kotlin
      fun read(
          b: ByteArray,
          off: Int = 0,
          len: Int = b.size,
      ) { /*...*/ }
      ~~~
  - override 메서드는 항상 기본 메서드와 동일한 기본 매개 변수 값을 사용한다.
  - **기본 매개변수 값이 있는 메서드를 override 할 때는 기본 매개변수 값은 생략되어야 한다.**
    - ~~~kotlin
      open class A {
          open fun foo(i: Int = 10) { /*...*/ }
      }

      class B : A() {
          override fun foo(i: Int) { /*...*/ }  // No default value is allowed.
      }
      ~~~

### 4. Triple-quoted strings

~~~kotlin
const val question = "life, the universe, and everything"
const val answer = 42

val tripleQuotedString = """
    #question = "$question"
    #answer = $answer""".trimIndent()

fun main() {
    println(tripleQuotedString)
}
~~~

<details>
  <summary> 아래 처럼 출력이 되도록 수정하기 <br> question = "life, the universe, and everything" <br> answer = 42  </summary>

~~~kotlin 
const val question = "life, the universe, and everything"
const val answer = 42

val tripleQuotedString = """
    question = "$question"
    answer = $answer""".trimIndent()

fun main() {
    println(tripleQuotedString)
}
~~~  

</details>

- [different string literals and string templates](https://kotlinlang.org/docs/basic-types.html#string-literals)
  - 문자열 리터럴(String literals)
    - 코틀린에는 두 가지 유형의 문자열 리터럴이 있다.
      - 개행문자를 포함하는 문자열
        - ~~~kotlin
          val s = "Hello, world!\n"
          ~~~
      - 삼중 따옴표(""")로 구분되며, 개행문자를포함하지 않고, 줄바꿈 및 기타 문자를 포함할 수 있다.
        - ~~~kotlin
          val text = """
              for (c in "foo")
                  print(c)
          """
          ~~~
        - trimMargin() 메서드로 공백을 제거 할수 있다. default 는 '|' 를 사용하고 메서드의 인자로 다른 문자를 사용하도록 할 수 있다.
          - ~~~kotlin
            val text = """
                |Tell me and I forget.
                |Teach me and I remember.
                |Involve me and I learn.
                |(Benjamin Franklin)
                """.trimMargin()
            ~~~
  - 문자열 템플릿(String templates)
    - 문자열 템플릿은 템플릿 표현식을 포함할 수 있다. 템플릿 표현식은 달러 기호($) 로 시작한다.
      - ~~~kotlin
        val i = 10
        println("i = $i") // prints "i = 10"
        ~~~
      - ~~~kotlin
        val s = "abc"
        println("$s.length is ${s.length}") // prints "abc.length is 3"
        ~~~


### 5. String templates

삼중 따옴표로 묶인 문자열은 여러 줄 문자열뿐만 아니라 정규식 패턴을 만들때에도 유용하다.
~~~kotlin
  val month = "(JAN|FEB|MAR|APR|MAY|JUN|JUL|AUG|SEP|OCT|NOV|DEC)"

  fun getPattern(): String = TODO()
~~~

<details>
  <summary> 
    month 변수를 이용해 정규식을 작성해서 다음과 같은 패턴을 체크할 수 있도록 만들기 <br>
    13 JUN 1992 (two digits, one whitespace, a month abbreviation, one whitespace, four digits).  
  </summary>

~~~kotlin 
val month = "(JAN|FEB|MAR|APR|MAY|JUN|JUL|AUG|SEP|OCT|NOV|DEC)"
fun getPattern(): String = """\d{2} $month \d{4}"""

// 정규식 확인 
println(Regex(getPattern()).matches("13 JUN 1992")) // true
~~~  

</details>

### 6. Nullable types

~~~kotlin
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
) {
    TODO()
}

class Client(val personalInfo: PersonalInfo?)
class PersonalInfo(val email: String?)
interface Mailer {
    fun sendMessage(email: String, message: String)
}
~~~

~~~java
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if (client == null || message == null) return;

    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;

    String email = personalInfo.getEmail();
    if (email == null) return;

    mailer.sendMessage(email, message);
}
~~~

<details>
  <summary> if 표현식을 한번만 사용 위의 자바 코드를 만족하도록 TODO() 부분 작성하기  </summary>

~~~kotlin 
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
) {
    val email = client?.personalInfo?.email
    if(email != null && message != null) {
        mailer.sendMessage(email,message)
    }
}
~~~  

</details>

- [null safety and safe calls](https://kotlinlang.org/docs/null-safety.html#nullable-types-and-non-null-types)
  - 코틀린의 타입 시스템은 [The Billion Dollar Mistake](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)라고도 하는 null 참조의 위험을 제거하는 것을 목표로 한다.
  - 코틀린에서 NPE(NullPointerException) 가 발생하는 경우
    - NullPointerException() 을 throw 하는 경우
    - !! 연산자(not-null assertion)를 사용하는 경우
      - 느낌표를 이중(!!)으로 사용하면 어떤 값이든 널이 될 수 없는 타입으로 강제로 바꿀 수 있다.
      - 실제 널에 대해 !!를 적용하면 NPE 가 발생한다.
    - 다음과 같이 초기화와 관련된 데이터가 불일치 할 경우
      - 생성자에서 사용할 수 있는 초기화 되지 않은 this 가 전달되어 어딘가에서 사용되는 경우 
      - 슈퍼 클래스의 생성자가 실행될 때 파생 클래스에서 선언되거나 재정의된 속성이 아직 초기화 되지 않은 경우 
    - 코틀린에서는 null 을 참조할 수 있는 타입과 참조할 수 없는 타입을 구별한다.
      - ~~~kotlin
        var a: String = "abc" // Regular initialization means non-null by default
        a = null // compilation error 
        ~~~
      - null 을 참조하려면 다음과 같이 ? 을 써줘야 한다.
        - ~~~kotlin
          var b: String? = "abc" // can be set to null
          b = null // ok
          print(b)
          ~~~
      - 메소드를 호출할 때 null 을 허용하지 않는 타입을 사용한다면 NPE 가 발생하지 않도록 보장 되므로 다음은 안전하다고 할 수 있다.
        - ~~~kotlin
          val l = a.length
          ~~~
      - 위의 예에서 b 는 null 을 허용하는 타입이기 때문에 컴파일 에러가 발생한다.(b?.length 와 같이 써야한다.)
        - ~~~kotlin
          val l = b.length // error: variable 'b' can be null
          ~~~

### 7. Nothing types

항상 예외를 throw 하는 함수의 반환 유형으로 Nothing 타입을 사용할 수 있다.
~~~kotlin
import java.lang.IllegalArgumentException

fun failWithWrongAge(age: Int?) TODO() {
    throw IllegalArgumentException("Wrong age: $age")
}

fun checkAge(age: Int?) {
    if (age == null || age !in 0..150) failWithWrongAge(age)
    println("Congrats! Next year you'll be ${age + 1}.")
}

fun main() {
    checkAge(10)
}
~~~

<details>
  <summary> 
    TODO() 부분을 채워서 코드가 컴파일 되도록 하기 <br>
  </summary>

Nothing 타입이 없으면 컴파일러가 age 가 null 일 수 있다고 가정하기 때문에 checkAge 함수가 컴파일 되지 않는다.
~~~kotlin 
import java.lang.IllegalArgumentException

fun failWithWrongAge(age: Int?): Nothing {
    throw IllegalArgumentException("Wrong age: $age")
}

fun checkAge(age: Int?) {
    if (age == null || age !in 0..150) failWithWrongAge(age)
    println("Congrats! Next year you'll be ${age + 1}.")
}

fun main() {
    checkAge(10)
}
~~~  

</details>

### 8. Lambdas

~~~kotlin
fun containsEven(collection: Collection<Int>): Boolean =
        collection.any { TODO() }
~~~

<details>
  <summary> 컬렉션에 짝수가 포함되어 있는지 확인하는 람다 작성하기 </summary>

~~~kotlin 
fun containsEven(collection: Collection<Int>): Boolean =
        collection.any { it % 2 == 0 }
~~~  

</details>

- [lambdas](https://kotlinlang.org/docs/lambdas.html#lambda-expressions-and-anonymous-functions)
  - 람다 표현식 문법
    - ~~~kotlin
      val sum: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
      ~~~
    - 람다 표현식은 항상 중괄호로 둘러 싸여 있다.
    - 매개 변수 선언은 중괄호 안에 들어 간다.
    - 본문 부분은 -> 후에 쓴다.
    - 만약 람다의 반환 타입이 Unit 이 아니라면 람다 본문 마지막 식이 반환 값으로 리턴된다. 
    - 위 구문은 다음과 같이 바꿔 쓸 수 있다.
      - ~~~kotlin
        val sum = { x: Int, y: Int -> x + y }
        ~~~
  - 후행 람다 전달
    - 마지막 매개변수가 함수이면 해당 인수로 전달된 람다 표현식을 괄호 외부에 배치할 수 있다.
      - ~~~kotlin
        val product = items.fold(1) { acc, e -> acc * e }
        ~~~
    - 람다가 해당 호출의 유일한 인수인 경우 괄호를 완전히 생략할 수 있다.
      - ~~~kotlin
        run { println("...") }
        ~~~
  - it: 단일 매개변수의 암시적 이름
    - 람다의 파라미터가 하나뿐이고 그 타입을 컴파일러가 추론할 수 있는 경우 it 을 바로 쓸 수 있다.
    - 람다 파라미터로 이름을 따로 지정하지 않은 경우에만 it 이라는 이름이 자동으로 만들어진다.
    - 람다 안에 람다가 중첩되는 경우 각 람다의 파라미터를 명시하는 편이 낫다. 파라미터를 명시하지 않으면 각각의 it 이 가리키는 파라미터가 어떤 람다에 속했는지 파악하기 어려울 수 있다.
      - ~~~kotlin
        ints.filter { it > 0 }
        ~~~
  - 사용하지 않는 매개변수는 _ 를 사용할 수 있다.
    - ~~~kotlin
      map.forEach { _, value -> println("$value!") }
      ~~~

<br>

## II. Classes

### 9. Data classes

~~~kotlin
class Person

fun getPeople(): List<Person> {
    return listOf(Person("Alice", 29), Person("Bob", 31))
}

fun comparePeople(): Boolean {
    val p1 = Person("Alice", 29)
    val p2 = Person("Alice", 29)
    return p1 == p2  // should be true
}
~~~

<details>
  <summary> 컴파일 에러가 해결되도록 Person 클래스 수정하기 </summary>

~~~kotlin 
data class Person(val name: String, val age: Int)

fun getPeople(): List<Person> {
    return listOf(Person("Alice", 29), Person("Bob", 31))
}

fun comparePeople(): Boolean {
    val p1 = Person("Alice", 29)
    val p2 = Person("Alice", 29)
    return p1 == p2  // should be true
}
~~~  

</details>

- [classes](https://kotlinlang.org/docs/classes.html)
- [properties](https://kotlinlang.org/docs/properties.html)
- [data classes](https://kotlinlang.org/docs/data-classes.html)

### 10. Smart casts

### 11. Sealed classes

### 12. Rename on import

### 13. Extension functions

<br>

## III. Conventions

### 14. Comparison

### 15. Ranges

### 16. For loop

### 17. Operators overloading

### 18. Invoke

<br>

## IV. Collections

### 19. Introduction

### 20. Sort

### 21. Filter map

### 22. All Any and other predicates

### 23. Associate

### 24. GroupBy

### 25. Partition

### 26. FlatMap

### 27. Max min

### 28. Sum

### 29. Fold

### 30. Compound tasks

### 31. Sequences

### 32. Getting used to new style

<br>

## V. Properties

### 33. Properties

### 34. Lazy property

### 35. Delegates examples

### 36. Delegates how it works

<br>

## VI. Builders

### 37. Function literals with receiver

### 38. String and map builders

### 39. The function apply

### 40. Html builders

### 41. Builders how it works

### 42. Builders implementation

<br>

## VII. Generics

### 43. Generic functions
