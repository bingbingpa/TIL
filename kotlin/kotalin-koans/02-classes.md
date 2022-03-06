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

### 11. Sealed classes

### 12. Rename on import

### 13. Extension functions