## Summary
> Java 및 C++를 포함한 많은 프로그래밍 언어의 기본 프로그래밍 패러다임이다.

<br>

### 객체 지향 프로그래밍의 장점
* 코드 재사용이 용이하다.
* 유지보수가 쉽다.
* 대형 프로젝트에 적합하다.

<br>

### 객체 지향 프로그래밍의 단점
* 처리 속도가 상대적으로 느리다.
* 객체가 많으면 용량이 커질 수 있다.
* 설계시 많은 시간과 노력이 필요하다.

<br>

### 객체 지향 프로그래밍 키워드 5가지
* 클래스와 인스턴스
  * 클래스는 객체를 만들기 위한 일종의 템플릿이라고 생각할 수 있다.
    * 객체는 ```현실 세계의 개체나 개념```을 나타내는데 사용되며, 클래스는 이러한 객체를 만들기 위한 ```설계도```이자 구조를 제공한다.

  * 인스턴스는 클래스를 기반으로 생성된 실제 객체를 의미한다.
    * 클래스는 객체를 만들기 위한 ```일종의 템플릿```이며, 해당 템플릿을 이용하여 실제 데이터를 담은 객체를 생성하면 그것이 인스턴스가 됩니다

<br>

```kotlin
// 자동차 클래스를 가지고 있다.
// 자동차 클래스의 인스턴스는 실제로 존재하는 여러 대의 자동차를 나타낼 것이다.
// 각각의 자동차 인스턴스는 브랜드, 색상, 속도 등과 같은 특정한 속성을 갖게 된다.
// 자동차 클래스에서 정의한 메서드를 이용하여 동작을 수행할 수 있다.
class Car(val brand: String, val color: String) {
    fun accelerate() {
        println("The $color car is accelerating.")
    }
}

// Car 클래스의 인스턴스 생성
val carInstance = Car(brand = "Toyota", color = "Blue")

// 인스턴스의 속성에 접근
println("Brand: ${carInstance.brand}, Color: ${carInstance.color}")

// 인스턴스의 메서드 호출
carInstance.accelerate()
```

<br>
  
* ### 추상화
  * 복잡한 시스템이나 개념을 단순화하여 불필요한 세부 사항을 무시하는 프로그래밍 기법이다.
    * #### 데이터 추상화
      * 데이터의 복잡한 내부 구조를 숨기고, 오직 필요한 부분만을 드러내는 것을 의미한다.
    * #### 프로시저 추상화
      * 프로시저(함수 또는 메서드)의 세부 구현을 감추고 인터페이스를 제공하는 것이다.
    * #### 제어 추상화
      * 복잡한 제어 구조를 숨기고 간단한 제어 인터페이스를 제공하는 것이다.
    * #### 물체 추상화
      * 객체 지향 프로그래밍에서는 객체가 데이터와 그 데이터를 다루는 메서드를 묶어 추상화를 형성한다. 

<br>

* ### 캡슐화
  * 데이터와 그 데이터를 다루는 메서드를 하나로 묶어서 외부에서의 접근을 제어하는 것을 의미한다.
    * #### 데이터 은닉
      * 캡슐화는 객체 내부의 데이터를 외부에서 직접 접근할 수 없도록 제한한다.
    * #### 메서드 제어
      * 객체가 수행하는 동작을 나타내는 메서드도 외부에서 직접 호출되지 않도록 제어한다.
    * #### 안정성과 보안 향상
      * 캡슐화는 객체의 내부 상태를 감춤으로써 안정성을 높이고 보안을 향상시킨다.
    * #### 모듈화 및 유지보수 용이성
      * 객체를 캡슐화하면 객체의 내부 구현이 외부와 독립적으로 존재하게 되므로, 코드를 모듈화하고 유지보수를 쉽게 할 수 있다.

<br>

```kotlin
class Car(private var brand: String, private var model: String) {

    fun getBrand(): String {
        return brand
    }

    fun setModel(newModel: String) {
        model = newModel
    }
}

fun main() {
    // Car 클래스의 인스턴스 생성
    val myCar = Car(brand = "Toyota", model = "Camry")

    // 외부에서는 private 변수에 직접 접근할 수 없음
    // getBrand 메서드를 통해 접근
    println(myCar.getBrand())  // 출력: Toyota

    // setModel 메서드를 통해 private 변수 간접적으로 변경
    myCar.setModel("Corolla")
}
```

<br>
  
* ### 상속
  * 한 클래스가 다른 클래스의 특성(속성과 메서드)을 이어받아 확장하는 것이다.
    * #### 코드의 재사용성을 높이고, 클래스 간에 계층 구조를 형성하여 코드의 구조를 조직화하는 데 도움이 된다.

<br>

```kotlin
// 부모 클래스
open class Animal(val name: String) {
    open fun speak() {
        println("$name is making a sound")
    }
}

// 자식 클래스
class Dog(name: String) : Animal(name) {
    fun bark() {
        println("$name is barking")
    }
}

// 자식 클래스
class Cat(name: String) : Animal(name) {
    fun meow() {
        println("$name is meowing")
    }
}

fun main() {
    // 객체 생성
    val dogInstance = Dog(name = "Buddy")
    val catInstance = Cat(name = "Whiskers")

    // 상속된 메서드 호출
    dogInstance.speak()  // 출력: Buddy is making a sound
    catInstance.speak()  // 출력: Whiskers is making a sound

    // 자식 클래스의 메서드 호출
    dogInstance.bark()   // 출력: Buddy is barking
    catInstance.meow()   // 출력: Whiskers is meowing
}
```

<br>

* ### 다형성
  * 동일한 인터페이스를 사용하여 여러 타입의 객체를 다룰 수 있는 능력이다.
    * #### 다형성은 크게 두 가지 형태로 나눌 수 있다.
      * ##### 컴파일 타임 다형성(Compile-time Polymorphism): 메서드 오버로딩
      * ##### 런타임 다형성(Runtime Polymorphism 또는 Dynamic Polymorphism): 메서드 오버라이딩

<br>
     
```kotlin
// 같은 이름의 메서드가 여러 개 정의되어 있고, 컴파일러가 어떤 메서드를 호출할지 결정한다.
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

<br>

```kotlin
// 여러 클래스가 동일한 메서드를 가질 때, 객체의 실제 타입에 따라 어떤 메서드가 호출될지 실행 시간에 결정된다.
class Animal {
    void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    void makeSound() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    void makeSound() {
        System.out.println("Cat meows");
    }
}
```

<br>

### 개인적인 생각
* 객체지향 프로그래밍은 많은 프로그래밍 언어의 기본 프로그래밍 패러다임이 맞다고 생각한다.
* 공식적으로도 밝혔으니, 객체지향 프로그래밍은 그만큼 많이 쓰이는 것 같고, 코드의 유지보수, 재사용성이 능이하다.
* 물론 단점도 존재하지만, 장점들이 단점들을 씹어먹을 만큼 성능이 좋기 때문에 공부해두면 좋을 것 같다.
