# [Kotlin Koans](https://kotlinlang.org/docs/koans.html)

## Introduction

### Hello, world!

~~~
fun start(): String = TODO() 
~~~

<details>
  <summary> start 함수가 "OK" 를 리턴하도록 변경하기 </summary>

~~~ 
fun start(): String = "OK"
~~~  

</details>

- [function syntax](https://kotlinlang.org/docs/basic-syntax.html#functions)
  - 두 개의 Int 파라미터와 Int 값을 리턴하는 경우  
    - ~~~ 
      fun sum(a: Int, b: Int): Int {
          return a + b
      } 
      ~~~
  - 함수 본문은 표현식으로 쓸 수 있고, 리턴 타입은 생략 할 수 있다. 
    - ~~~
      fun sum(a: Int, b: Int) = a + b
      ~~~
  - 반환 타입이 없는 경우는 Unit 을 사용한다.
    - ~~~
      fun printSum(a: Int, b: Int): Unit {
          println("sum of $a and $b is ${a + b}")
      } 
      ~~~
  - Unit 리턴 타입은 생략 할 수 있다.
    - ~~~
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

### Default arguments

### Triple-quoted strings

### Nullable types

### Nothing types

### Lambdas

## Classes

## Conventions

## Collections

## Properties

## Builders

## Generics