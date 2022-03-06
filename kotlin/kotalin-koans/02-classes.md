# II. Classes

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
- data 한정자를 붙이면 toString, equals, hashCode, copy, componentN 과 같은
  함수가 자동 생성 되기 때문에 == 비교가 true 가 나온다.

</details>

- [classes](https://kotlinlang.org/docs/classes.html)
  - kotlin 에서 클래스의 인스턴스를 생성할 때는 자바처럼 new 를 붙일 필요가 없다.
    - ~~~kotlin
      val invoice = Invoice()
      val customer = Customer("Joe Smith")
      ~~~
  - kotlin 의 클래스에는 기본 생성자와 하나 이상의 보조 생성자가 있을 수 있다.
  - 기본 생성자는 클래스 헤더의 일부이며 () 안에 매개 변수로 쓴다. 기본생성자의 constructor 키워드는 생략할 수 있다.
  - 기본 생성자의 매개 변수는 클래스 본문 또는 init 블록에서 사용 할 수 있다.
    - ~~~kotlin
      class Person constructor(firstName: String) { /.*...*/ }
      class Person (firstName: String) { /.*...*/ }
      ~~~
  - 기본 생성자는 코드를 포함할 수 없고, 초기화 코드는 init 블록에 위치 할 수 있다.
  - 초기화 블록은 다음과 같은 순서로 실행된다.
    - ~~~kotlin
      class InitOrderDemo(name: String) { 
          val firstProperty = "First property: $name".also(::println)
      
          init {
              println("First initializer block that prints $name")
          }
          
          val secondProperty = "Second property: ${name.length}".also(::println)
          
          init {
              println("Second initializer block that prints ${name.length}")
          }
      }
      // First property: hello world
      // First initializer block that prints hello world
      // Second property: 11
      // Second initializer block that prints 11
      ~~~
  - secondary constructors
    - 클래스에 기본 생성자가 있는 경우 보조 생성자는 다음과 같은 형태로 사용 할 수 있다. 생성자는 필요에 따라 매개변수를 다르게 하여 여러개 만들 수 있다.
      - ~~~kotlin
        class Person(val name: String) { 
            val children: MutableList<Person> = mutableListOf()
            constructor(name: String, parent: Person) : this(name) { 
                parent.children.add(this)
            }   
        }
        ~~~
    - init 블록은 생성자 블록보다 먼저 실행된다.
      - ~~~kotlin
        class Constructors { 
            init { 
                println("Init block")
            }
        
            constructor(i: Int) {
                println("Constructor $i")
            }
        }
        // Init block
        // Constructor 1
        ~~~
  - **JVM 에서 모든 기본 생성자 매개변수에 기본값이 있는 경우 컴파일러는 기본값을 사용할 매개변수가 없는 추가 생성자를 생성한다.
    그래서 매개변수가 없는 생성자를 통해 클래스 인스턴스를 생성하는 Jackson 또는 JPA 와 같은 라이브러리와 함께 kotlin 을 더 쉽게 사용할 수 있다.**
    - 자바에서 사용하던 lombok 의 @NoArgsConstructor 를 사용할 필요가 없다.
    - ~~~kotlin
      class Customer(val customerName: String = "")
      ~~~
  - 클래스의 상속
    - **kotlin 에서 클래스는 기본적으로 final 이기 때문에 상속이 불가능하다.** 상속을 위해서는 open 이라는 키워드를 붙여 주어야 한다.
    - 추상클래스는 open 이라는 키워드 없이 상속이 가능하며, 추상 클래스는 인스터스를 생성할 수 없다.
      - ~~~kotlin
        open class Base(p: Int)
        class Derived(p: Int) : Base(p)
        ~~~
- [data classes](https://kotlinlang.org/docs/data-classes.html)
  - class 앞에 data 키워드를 붙이면 데이터 클래스가 된다.
    - ~~~kotlin
      data class User(val name: String, val age: Int)
      ~~~
  - 컴파일러는 기본 생성자에 선언된 모든 속성으로 다음과 같은 함수들을 자동으로 만든다.
    - equals() / hashCode()
    - toString() // "User(name=John, age=42)" 이런 형태로 출력한다.
    - [componentN()](https://kotlinlang.org/docs/destructuring-declarations.html)
    - copy()
  - 데이터 클래스는 일관성 있는 동작을 보장하기 위해 다음과 같은 요구 사항을 충족해야 한다.
    - 기본 생성자에는 하나 이상의 매개변수가 있어야 한다.
    - 모든 기본 생성자 매개변수는 val 또는 var 로 명시해야 한다.
      - 기본 클래스에서는 val / var 는 생략 가능하다.
    - 데이터 클래스는 open, abstract, inner 키워드와 함께 사용할 수 없다.
  - 데이터 클래스 또는 데이터 클래스의 슈퍼클래스에 equals(), hashCode(), toString() 이 있는 경우는 있는 것을 그대로 사용한다.
  - componentN(), copy() 함수는 명시적으로 구현하는 것을 허용하지 않는다.
  - 데이터 클래스에서 자동으로 생성되는 함수에서 속성을 제외하려면 클래스 본문 내에 프로퍼티를 선언하면 된다.
    - ~~~kotlin
      data class Person(val name: String) {
          var age = Int = 0
      }
      ~~~
  - 위의 경우에서 컴파일러는 name 이라는 프로퍼티로만 equals(), hashCode(), toString() 을 만들기 때문에 아래의 비교는 같다고 나온다.
    - ~~~kotlin
      val person1 = Person("John")
      val person2 = Person("John")
      person1.age  = 10
      person2.age = 20
      println(person1 == person2) // true
      ~~~
  - copy() 는 이름있는 아규먼트를 활용해서 변경할 수 있다.
    - ~~~kotlin
      val person = Person("John")
      val changedPerson = person.copy(name = "Thor")
      ~~~
  - 데이터 클래스로 생성된 인스턴스는 구조 분해를 사용해 다음과 같이 사용 할 수 있다.
    - ~~~kotlin
      val jane = User("Jane", 35)
      val (name, age) = jane
      println("$name, $age years of age") // "Jane, 35 years of age"
      ~~~

### 10. Smart casts

~~~java
public int eval(Expr expr) {
    if (expr instanceof Num) {
        return ((Num) expr).getValue();
    }
    if (expr instanceof Sum) {
        Sum sum = (Sum) expr;
        return eval(sum.getLeft()) + eval(sum.getRight());
    }
    throw new IllegalArgumentException("Unknown expression");
}
~~~

~~~kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> TODO()
            is Sum -> TODO()
            else -> throw IllegalArgumentException("Unknown expression")
        }

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
~~~

<details>
  <summary> 자바 코드를 start cast 와 when 을 이용해서 코드 완성하기 </summary>

~~~kotlin 
fun eval(expr: Expr): Int =
    when (expr) {
        is Num -> expr.value
        is Sum -> eval(expr.left) + eval(expr.right)
        else -> throw IllegalArgumentException("Unknown expression")
    }

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
~~~  
</details>

- [smart casts](https://kotlinlang.org/docs/typecasts.html#smart-casts)
  - is / !is 연산자
    - is / !is 을 사용해서 객체가 지정된 유형을 준수하는지 여부를 런타임에 체크한다.
      - ~~~kotlin
        if (obj is String) {
            print(obj.length)
        }
        
        if (obj !is String) { // same as !(obj is String)
            print("Not a String")
        } else {
            print(obj.length)
        }
        ~~~
  - smart cast
    - 코틀린에서는 is 를 사용해 어떤 변수가 원하는 타입인지 검사하고 나면 굳이 변수를 원하는 타입으로 캐스팅 하지 않아도 사용 할 수 있다.
    - 실제로는 컴파일러가 캐스팅을 수행해주는데 이를 스마트 캐스트라고 부른다.
      - ~~~kotlin
        fun demo(x: Any) {
            if (x is String) {
                print(x.length) // x is automatically cast to String
            }
        }
        ~~~
    - 스마트 캐스트는 when 또는 while 표현식에서도 작동한다.
      - ~~~kotlin
        when (x) {
            is Int -> print(x + 1)
            is String -> print(x.length + 1)
            is IntArray -> print(x.sum())
        }
        ~~~
  - 안전하지 않은 캐스트 연산자: as
    - 일반적으로 캐스트 연산자는 캐스트가 가능하지 않은 경우 예외를 throw 한다.
  - 안전한 캐스트 연산자: as?
    - as? 연산자는 어떤 값을 지정한 타입으로 캐스트 한다. as? 는 값을 대상 타입으로 변환할 수 없으면 null 을 반환한다.
    - **안전한 캐스트를 사용할 때 일반적인 패턴은 캐스트를 수행한 뒤에 엘비스 연산자를 사용하는 것이다.**

### 11. Sealed classes

~~~kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> TODO()
            is Sum -> TODO()
        }

interface Expr
class Num(val value: Int) : TODO()
class Sum(val left: Expr, val right: Expr) : TODO()
~~~

<details>
  <summary> 10번에서 완성한 코드를 sealed interface 를 이용해서 수정하기 </summary>

~~~kotlin 
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> expr.value
            is Sum -> eval(expr.left) + eval(expr.right)
        }

sealed interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
~~~  
</details>

- [sealed class](https://kotlinlang.org/docs/sealed-classes.html)
  - sealed class / interface 는 상위 클래스 또는 인터페이스를 상속/구현 하는 클래스 정의를 제한 할 수 있다.
  - 어떤 클래스를 상속받는 하위 클래스는 여러 파일에 존재할 수 있기 때문에 컴파일러는 얼마나 많은 하위 클래스들이 있는지 알지 못한다.
  - 하지만 sealed 클래스는 동일 파일에 정의된 하위 클래스 외에 다른 하위 클래스는 존재 하지 않는다는 것을 컴파일러에게 알려주는 것과 같다.
  - **sealed 클래스는 자식 클래스에 대한 선언을 같은 패키지 내로 제한한다.**
  - sealed 클래스는 abstract 이며 직접 인스턴스화 할 수 없다. abstract 멤버는 가질 수 있다.
  - **enum 과 비슷해보이지만 sealed 클래스는 enum 과 다르게 한개 이상의 인스턴스를 생성할 수 있다.**
  - sealed 로 표시된 클래스는 자동으로 open 이기 때문에 별도로 open 변경자를 붙일 필요가 없다.
  - **when 식에서 sealed 클래스의 모든 하위 클래스를 처리 한다면 디폴트 분기(else 분기)가 필요 없다.**
    - when 식에서 사용시 sealed 클래스의 자식 클래스에 대해 모두 처리 하지 않으면 컴파일 에러가 발생해서 미리 오류를 방지 할 수 있다.
    ~~~kotlin
    sealed interface Error
    
    sealed class IOError(): Error
    
    class FileReadError(val file: File): IOError()
    class DatabaseError(val source: DataSource): IOError()
    
    object RuntimeError : Error
    
    fun log(e: Error) = when(e) {
        is FileReadError -> { println("Error while reading file ${e.file}") }
        is DatabaseError -> { println("Error while reading from database ${e.source}") }
        RuntimeError ->  { println("Runtime error") }
        // sealed 클래스의 자식 클래스를 모두 처리 했기 때문에 else 가 필요 없다.
    }
    ~~~
### 12. Rename on import
  
~~~kotlin
// import kotlin.random.Random
// import java.util.Random

fun useDifferentRandomClasses(): String {
    return "Kotlin random: " +
            // KRandom.nextInt(2) +
            " Java random:" +
            // JRandom().nextInt(2) +
            "."
}
~~~

<details>
  <summary> import rename 을 사용해서 주석된 코드 완성하기 </summary>

~~~kotlin 
 import kotlin.random.Random as KRandom
 import java.util.Random as JRandom

fun useDifferentRandomClasses(): String {
    return "Kotlin random: " +
             KRandom.nextInt(2) +
            " Java random:" +
             JRandom().nextInt(2) +
            "."
}
~~~  
</details>

- [import](https://kotlinlang.org/docs/packages.html#imports)
  - 이름 충돌이 있는 경우 as 키워드를 사용하여 충돌 엔티티의 이름을 변경하여 명확하게 할 수 있다.
    - ~~~kotlin
      import org.example.Message 
      import org.test.Message as testMessage 
      ~~~
### 13. Extension functions
    
~~~kotlin
fun Int.r(): RationalNumber = TODO()

fun Pair<Int, Int>.r(): RationalNumber = TODO()

data class RationalNumber(val numerator: Int, val denominator: Int)

// Pair 는 코틀린 표준 라이브러리로 다음과 같이 정의 되어 있다.
data class Pair<out A, out B>(
    val first: A,
    val second: B
)
~~~

<details>
  <summary> 확장 함수를 이용해 Int.r() 및 Pair.r()을 구현하고 Int 및 Pair 를 RationalNumber 로 변환하기 </summary>

~~~kotlin 
fun Int.r(): RationalNumber = RationalNumber(this, 1)

fun Pair<Int, Int>.r(): RationalNumber = RationalNumber(first, second)

data class RationalNumber(val numerator: Int, val denominator: Int)
~~~  
</details>

- [extension functions](https://kotlinlang.org/docs/extensions.html#extension-functions)
  - 확장 함수는 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수다.
  - **확장 함수는 실제로 클래스를 수정하지는 않고, 확장 함수를 선언하면 새 멤버를 클래스에 삽입하지 않고, 해당 유형의 변수에 대해 점(.) 표기법으로 새 함수를 호출할 수만 있게 된다.**
  - 확장 함수 만들기
    - 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙인다.
    - 클래스의 이름을 **수신 객체 타입**(receiver type)이라 부르며, 확장 함수가 호출되는 대상이 되는 값(객체)을 **수신 객체**(receiver object)라고 부른다.
    - 아래에서 String 이 수신객체 타입, this 는 수신 객체
    - 일반 메소드의 본문에서 this 를 사용할 때와 마찬가지로 확장 함수 본문에도 this 를 쓸수도 있고 생략 할 수도 있다.
      - ~~~kotlin
        fun String.lastChar(): char = this.get(this.length - 1)
        println("Kotlin".lastChar())
        ~~~
  - 확장 함수는 정적으로 전달 되며, 함수가 호출되는 표현식의 유형에 따라 결정된다.
  - 아래의 예제는 함수가 Shape 의 getName 을 호출하고 있기 때문에 "Shape" 출력한다.
    - ~~~kotlin
      open class Shape
      class Rectangle: Shape()
      
      fun Shape.getName() = "Shape"
      fun Rectangle.getName() = "Rectangle"
      
      fun printClassName(s: Shape) {
          println(s.getName())
      }
      
      fun main() {
          printClassName(Rectangle())
      }         
      // Shape
      ~~~          
  - 만약 클래스에 멤버 함수가 있고, 동일한 수신자 유형, 동일한 이름을 가진 확장 함수가 정의 되어 있다면 항상 멤버 함수가 호출된다.
    - ~~~kotlin
      class Example {
          fun printFunctionType() { println("Class method") }
      }
      
      fun Example.printFunctionType() { println("Extension function") }
      
      Example().printFunctionType()
      // Class method
      ~~~ 
  - 확장 함수가 이름은 같지만 파라미터가 다른 멤버 함수를 오버로드 것은 예상한대로 동작한다.
    - ~~~kotlin
      class Example {
          fun printFunctionType() { println("Class method") }
      }
      
      fun Example.printFunctionType(i: Int) { println("Extension function #$i") }
      
      Example().printFunctionType(1)
      ~~~
  - 
  
