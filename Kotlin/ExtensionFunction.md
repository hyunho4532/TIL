## Summary
> 간단하게 상속이나 디자인 패턴 없이 클래스를 간단하게 확장할 수 있는 방법이다.

<br>

#### 예를 들어, 수정할 수 없는 타사 라이브러리의 클래스 또는 인터페이스가 있다고 가정해보자.

<br>

#### 확장 함수를 사용하면 아래와 같은 장점들이 존재한다.
* 확장 함수를 사용하면 클래스 또는 인터페이스에 대한 새 함수를 작성할 수 있다.
* 이러한 함수는 마치 원래 클래스의 메서드인 것처럼 일반적인 방법으로 호출할 수 있다.
* 이러한 매커니즘을 <strong>확장 함수</strong>이라고 한다.

<br>

#### 확장 함수
> 확장 함수를 선언하려면 해당 함수의 이름 앞에 수신자 타입(receiver type)을 접두사로 붙인다.

```Kotlin
// MutableList<Int>에 swap 함수를 추가하는 예시
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val temp = this[index1] // 'this'는 현재 확장된 MutableList<Int>를 가리킵니다.
    this[index1] = this[index2]
    this[index2] = temp
}
```

<br><br>

* 확장 함수 내에서의 this 키워드는 수신자 객체(receiver object)를 나타낸다.
* 여기서 수신자 객체란 ```점(dot) 앞에 전달된 객체```를 말한다.

```kotlin
val myList: MutableList<Int> = mutableListOf(1, 2, 3, 4)

// 확장 함수 호출
myList.swap(0, 2)
```

<br>


#### 이 함수는 모든 MutableList<T>에 대해 의미가 있으며, 이를 제네릭으로 만들 수 있다.

```kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}

// 제네릭을 사용할 때, 함수 이름 앞에 일반적인 유형의 매개변수를 선언해야 한다.
// 이렇게 선언한 매개변수는 이후에 수신자 유형 표현식에서 사용 가능하다
```

<br>

#### 확장 함수를 정적으로 해결하는 방법
* 확장(extension)은 실제로 확장하는 클래스를 수정하지 않는다.
* 확장을 정의함으로써 클래스에 새로운 멤버를 삽입하는 것이 아니다.
* 단지 해당 타입의 변수에 점 표기법(dot-notation)을 사용하여 호출할 수 있는 새로운 함수를 만드는 것이다.

<br>

#### 확장 함수는 정적으로 디스패치된다.
* 호출되는 어떤 확장 함수인지는 이미 컴파일 시간에 수신자 타입을 기반으로 알려져 있다.

```kotlin
// Shape을 출력하는 예제
// 호출된 확장 함수는 매개변수 s의 선언된 타입인 Shape 클래스에만 의존하고 있다.
open class Shape
class Rectangle: Shape()

fun Shape.getName() = "Shape"
fun Rectangle.getName() = "Rectangle"

fun printClassName(s: Shape) {
    println(s.getName())
}

printClassName(Rectangle())
```

<br>

```kotlin
// 만약 클래스에 멤버 함수가 있고, 동일한 수신자 타입, 동일한 이름, 그리고 주어진 인자에 적용 가능한 확장 함수가 정의되어 있다면, 멤버 함수가 항상 우선이다.
// 동일한 이름을 가진 멤버 함수와 서로 다른 시그니처를 가진 확장 함수가 오버로드(overload)될 수도 있다.
// 'Class method'를 출력하는 예제
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType() { println("Extension function") }

Example().printFunctionType()
```

<br>

```kotlin
class Example {
    fun printFunctionType() { println("Class method") }
}

fun Example.printFunctionType(i: Int) { println("Extension function #$i") }

Example().printFunctionType(1)
```

<br>

#### nullable 수신자
> 확장 함수는 nullable한 수신자 타입으로 정의될 수 있다

<br>

* 이러한 확장 함수들은 해당 객체 변수의 값이 null이더라도 호출할 수 있다.
* 만약 수신자가 null이라면 'this'도 null이다.
* nullable 확장 함수를 정의할 때, 함수 본문 내에서 'this == null' 체크를 수행하는 것을 권장한다.

```kotlin
// Kotlin에서는 null 체크가 이미 확장 함수 내에서 이루어졌기 때문에 null 확인 없이도 toString()을 호출할 수 있다.
fun Any?.toString(): String {
    if (this == null) return "null"
    return toString()
}
```

<br>

#### Extension 속성
> 코틀린은 함수를 지원하는 방식과 유사하게 확장 속성도 지원한다.

<br>

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1


val House.number = 1 // 오류: 확장 속성에 대한 초기화는 허용되지 않습니다.
```

<br>

#### companion object에 대한 확장
> 클래스에 companion object가 정의되어 있다면, companion object에 대한 확장 함수와 속성도 정의할 수 있다.

<br>

* 일반적인 companion object의 멤버들처럼, 클래스 이름만을 한정자로 사용하여 호출할 수 있다.

```kotlin
class MyClass {
    companion object {

    }
}

fun MyClass.Companion.printCompanion() { println("companion") }

fun main() {
    MyClass.printCompanion()
}
```

<br>

#### Extensions의 범위
> 대부분의 경우, 확장 함수나 속성은 패키지 바로 아래의 최상위 레벨에서 정의한다.

<br>

```kotlin
package org.example.declarations

fun List<String>.getLongestString() {

}
```

<br>

```kotlin
package org.example.usage

import org.example.declarations.getLongestString

fun main() {
    val list = listOf("red", "green", "blue")
    list.getLongestString()
}
```

<br>

#### 확장을 멤버로 선언하기
> 하나의 클래스 안에서 다른 클래스에 대한 확장을 선언할 수 있다.

<br>

* 확장 내부에서는 여러 개의 암시적 수신자가 있습니다. 
* 한정자 없이 접근할 수 있는 멤버를 가진 객체들이다.
* 확장이 선언된 클래스의 인스턴스를 ```디스패치 수신자(dispatch receiver)``` 라고 한다.
* 확장 메서드의 수신자 타입의 인스턴스를 ```확장 수신자(extension receiver)``` 라고 한다.

<br>

```kotlin
class Host(val hostname: String) {
    fun printHostname() { print(hostname) }
}

class Connection(val host: Host, val port: Int) {
    fun printPort() { print(port) }

    fun Host.printConnectionString() {
        printHostname()
        print(":")
        printPort()
    }

    fun connect() {
        host.printConnectionString()
    }
}

fun main() {
    Connection(Host("kotl.in"), 443).connect()
    //Host("kotl.in").printConnectionString()  // error, the extension function is unavailable outside Connection
}
```

<br>

* 디스패치 수신자와 확장 수신자의 멤버 간에 이름 충돌이 발생한 경우, 확장 수신자가 우선한다.
* 디스패치 수신자의 멤버를 참조하려면 한정된 'this' 구문을 사용할 수 있습니다.
