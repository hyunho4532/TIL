## Summary
> together를 뜻하는 co와 작업들의 집합을 뜻하는 Routine이 합쳐져 만들어진 단어이다.

<br>

### 코루틴의 특징

* 개념적으로는 스레드와 유사하다고 볼 수 있다.
* 코드 블록을 실행하는 데 사용되며 나머지 코드와 동시에 작동하는 기능을 가지고 있다.
* 특정 스레드에 제한을 받지 않는다.
* 실행을 한 스레드에서 일시 중단하고 다른 스레드에서 다시 시작할 수 있다.

<br>

#### 코루틴은 가벼운 스레드라고 생각할 수 있지만, 실제 사용에서 스레드와는 다른 차이점이 있다.
아래 코드를 확인해보자.

```kotlin
fun main() = runBlocking {
    launch {
        delay(1000L)
        println("World!")
    }
    println("Hello")
}

결과값:
Hello
World!
```

<br>

```launch```는 ```코루틴 빌더(coroutine builder)``` 중 하나이다. <br>
코루틴 빌더에 대해 모르는 사람들이 있을 수 있기 때문에 개념을 파악해보자!

<br>

### 코루틴 빌더
> 코루틴을 생성하고 실행하는 데 사용되는 구조나 함수입니다.

<br>

* launch는 비동기적으로 코드 블록을 실행하는 새로운 코루틴을 만든다.
* 코드 블록은 별도의 스레드나 백그라운드에서 실행할 수 있다.
* launch는 백그라운드에서 실행되는 "fire-and-forget" 스타일의 코루틴을 만들 때 사용한다.

<br>

### delay
> ```delay```는 특수한 일시 중단 함수이다. (코루틴을 특정 시간 동안 일시 중단) <br>

<br>

* 코루틴을 일시 중단하는 것은 기본 스레드를 차단하지 않는다.
* 다른 코루틴이 실행되어 기본 스레드를 사용할 수 있게 한다.


<br>

### runBlocking
> fun main()에서 코루틴이 사용된 코드와 코루틴 없는 일반적인 코드 간의 연결 역할을 한다.

<br>

* 위 코드에서 runBlocking을 제거하거나 빼먹으면, launch 호출 시 에러가 발생한다.
* 해당 메소드를 실행하는 스레드가 호출이 완료될 때까지 차단된다는 것이다.
*  runBlocking { ... } 내부의 모든 코루틴이 실행을 완료할 때까지이다.

<br>

### 구조적 동시성
> 코루틴은 구조적 동시성의 원칙을 따른다.

<br>

* 구조적 동시성은 새로운 코루틴이 특정 CoroutineScope 내에서만 시작될 수 있다.
* 해당 CoroutineScope가 코루틴의 수명을 제한한다는 것을 의미한다.
* 위의 예제에서 보듯이, runBlocking은 해당하는 스코프를 설정하였다.
* 이전 예제에서는 1초의 지연 후에 "World!"가 인쇄될 때까지 기다린 후에 종료된다.


<br>

### 함수 추출 리팩터링
> 코드 리팩터링 중 하나로, 코드 내에서 특정 부분을 분리하여 새로운 함수로 추출하는 과정이다.

<br>

#### launch { ... } 내부의 코드 블록을 별도의 함수로 추출해 보자.
```kotlin
// 이 코드에 '함수 추출' 리팩터링을 수행하면, suspend 수식자를 가진 새로운 함수가 생성됩니다.
fun main() = runBlocking {
    launch { doWorld() }
    println("Hello")
}

suspend fun doWorld() {
    delay(1000L)
    println("World!")
}
```

<br>

### 스코프 빌더
> coroutineScope 빌더를 사용하여 사용자 정의 스코프를 선언할 수 있다.

<br>

#### runBlocking과 coroutineScope의 차이점은?
* runBlocking 메소드는 현재 스레드를 차단하여 대기한다.
* coroutineScope는 단순히 일시 중단되어 기존 스레드를 다른 용도로 사용할 수 있게 한다.
* runBlocking은 일반 함수로 간주되고, coroutineScope는 일시 중단 함수로 간주한다.

<br>

```kotlin
// Hello와 World를 동시에 출력하는 부분을 suspend fun doWorld() 함수로 이동하는 예제
fun main() = runBlocking {
    doWorld()
}

suspend fun doWorld() = coroutineScope {  // this: CoroutineScope
    launch {
        delay(1000L)
        println("World!")
    }
    println("Hello")
}
```

<br>


### 스코프 빌더와 동시성

> 일시 중단 함수에서든 여러 개의 동시 작업을 수행하는 데 사용할 수 있다.

<br>

```kotlin
// launch { ... } 블록 내의 코드 두 부분은 동시에 실행되며, 시작 후 1초 후에 'World 1'이 먼저 출력된다.
// 그 후 2초 후에 'World 2'가 출력된다.
// doWorld 함수 내의 coroutineScope는 두 작업이 모두 완료된 후에만 완료된다.
// 그로 인해 doWorld 함수가 반환되고 그 이후에 'Done' 문자열이 출력된다.
fun main() = runBlocking {
    doWorld()
    println("Done")
}

suspend fun doWorld() = coroutineScope {
    launch {
        delay(2000L)
        println("World 2")
    }
    launch {
        delay(1000L)
        println("World 1")
    }
    println("Hello")
}
```

<br>

### 명시적 작업
> launch 코루틴 빌더는 실행된 코루틴에 대한 핸들인 Job 객체를 반환한다.

<br>

```kotlin
// 자식 코루틴의 완료를 기다리고 나서 "Done" 문자열을 출력하는 예제
val job = launch { // launch a new coroutine and keep a reference to its Job
    delay(1000L)
    println("World!")
}
println("Hello")
job.join() // wait until child coroutine completes
println("Done")
```

<br>

### 코루틴은 가벼운 구조
* 코루틴은 JVM 스레드보다 자원을 덜 소비한다.
* 코루틴을 사용하여 자원 제한에 도달하지 않고 표현할 수 있다.

```kotlin
// 각각 5초 동안 기다린 후에 점 ('.')을 출력하는 50,000개의 독립적인 코루틴을 시작
// 매우 적은 메모리를 소비하는 예제

import kotlinx.coroutines.*

fun main() = runBlocking {
    repeat(50_000) { // launch a lot of coroutines
        launch {
            delay(5000L)
            print(".")
        }
    }
}
```

<br>

### 결론
* 코루틴은 스레드에 비해 가벼운 구조를 가지며, 더 적은 메모리 리소스를 사용한다.
* 비동기 작업을 간편하게 처리할 수 있다.
* 코루틴은 비동기 코드를 작성할 때 코드 가독성을 향상시킨다.
