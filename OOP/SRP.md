## Summary
> 객체는 ```단 하나의 책임만 가져야 한다는 원칙```이다.

<br>

##### 여기서 ```책임``` 이라는 의미는 하나의 기능만 담당한다고 생각하면 된다.
##### 즉, ```하나의 클래스```는 하나의 기능만 담당하여 ```하나의 책임```을 수행한다.

<br>

### 코드로 알아보는 단일 책임 원칙
##### 가정: 집안의 각 구성원은 자신의 역할과 책임이 있습니다.
* ###### 가정 구성원들이 각자의 역할을 명확히 이해하고 수행한다.
* ###### 어머니가 요리 책임을 지고, 아버지가 가정 재정을 책임지는 식으로 분리할 수 있다.

<br>

```kotlin

open class FamilyMember(val name: String)

class Mother(name: String) : FamilyMember(name) {
    fun cook(recipe: String) {
        println("$name is cooking $recipe.")
    }
}

class Father(name: String) : FamilyMember(name) {
    fun manageFinances() {
        println("$name is managing the family finances.")
    }
}

class Child(name: String) : FamilyMember(name) {
    fun study(subject: String) {
        println("$name is studying $subject.")
    }
}

fun main() {
    // 각 가족 구성원 생성
    val mom = Mother("Mom")
    val dad = Father("Dad")
    val child = Child("Child")

    // 각 구성원이 자신의 역할 수행
    mom.cook("Spaghetti")
    dad.manageFinances()
    child.study("Math")
}
```

<br>

#### 코드 설명
* ##### FamilyMember 클래스는 상속을 사용하여 각 가족 구성원의 기본 특성을 나타낸다.
* ##### Mother, Father, Child 클래스는 각각의 역할에 맞게 함수를 정의한다.
* ##### 이렇게 함으로서, 위 코드는 단일 책밍 원칙에 맞는 코드를 유지할 수 있다.

<br>

##### 동물: Animal 클래스에서는 동물의 울음소리를 출력하는 cry() 메서드를 구현해보자.

```kotlin
class Animal {

    private var animal: String? = null

    fun setAnimal(animal: String) {
        this.animal = animal
    }

    fun cry() {
        when (animal) {
            "Dog" -> println("bark!")
            "Cat" -> println("meow..")
        }
    }
}
```

<br>

#### 코드 설명
* ##### cry() 메서드는 Animal의 값이 "Cat"이면 고양이 울음소리를, "Dog"면 강아지 울음 소리를 출력한다.
* ##### 두 기능이 분리되어 있지 않고 하나의 메서드가 두 기능을 모두 가지고 있다.
* ##### 이렇게 함으로서, 위 코드는 단일 책밍 원칙에 위반하는 코드이다.

<br>

####  SRP를 적용한 코드
* ##### 추상 클래스를 만들어 각각의 하위 클래스에서 메서드를 상속 받아 사용해보자.

```kotlin
abstract class Animal {
    abstract fun cry();
}

class Dog : Animal() {
    @Override
    fun cry() {
        System.out.println("bark!!!");
    }
}

class Cat : Animal() {
    @Override
    fun cry() {
        System.out.println("Meow...");
    }
}
```

<br>

#### 코드 설명
* ##### 공통기능인 추상 메서드 cry()를 가진 추상 클래스 Animal을 만들었다.
* ##### Dog, Cat 클래스를 만들어 Animal을 상속 받아 각자의 클래스에 자신의 울음소리만을 구현했다.
* ##### 즉, 하나의 클래스에 하나의 기능을 가지고 있으므로, 단일 책임 원칙에 맞는 코드를 유지할 수 있다.

<br>

#### SRP 장점
* ##### 한 책임의 변경에서 다른 책임의 변경의 연쇄작용에서 자유로울 수 있다.
* ##### 코드의 의존성과 결합도를 줄인다.
