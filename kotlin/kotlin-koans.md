# [Kotlin Koans](https://kotlinlang.org/docs/koans.html)

## Introduction

### Hello, world!

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

### Named arguments

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

### Default arguments

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

### Triple-quoted strings

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

### Nullable types

### Nothing types

### Lambdas

<br>

## Classes

## Conventions

## Collections

## Properties

## Builders

## Generics