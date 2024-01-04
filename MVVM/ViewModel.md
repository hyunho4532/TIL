## Summary
> ViewModel 클래스는 비즈니스 로직 또는 화면 수준 상태 홀더이다.

<br>

### ViweModel의 특징
1. UI에 상태를 노출하고 관련 비즈니스 로직을 캡슐화한다.
2. 상태를 캐시하여 구성 변경에도 이를 유지한다.
3. 활동 간에 이동하거나 구성 변경을 따를 때 UI가 데이터를 다시 가져올 필요가 없다.

<br>

### ViewModel의 이점
1. UI 상태를 유지할 수 있습니다.
2. 비즈니스 로직에 대한 액세스 권한을 제공합니다.

<br>

### 지속성
> ViewModel은 ViewModel이 보유하는 상태와 ViewModel이 트리거하는 작업에서 모두 지속성을 허용한다.

<br>

### 범위
* ViewModel을 인스턴스화할 때는 ViewModelStoreOwner 인터페이스를 구현하는 객체를 전달한다.
* ViewModel의 범위가 ViewModelStoreOwner의 수명 주기로 지정됩니다
* ViewModelStoreOwner가 영구적으로 사라질 때까지 메모리에 남아 있는다.

<br>

#### 클래스 범위는 ViewModelStoreOwner 인터페이스의 ```직접``` 또는 ```간접 서브클래스``` 입니다.

<br>

* 직접 서브 클래스
  *  ComponentActivity
  *  Fragment
  *  NavBackStackEntry

* 간접 서브 클래스
  * ViewModelStoreOwner
 
<br>

### SavedStateHandle
> SaveStateHandle을 사용하면 구성 변경, 프로세스 재생성 전반에 걸쳐서도 데이터를 유지할 수 있습니다.

<br>

### 비즈니스 로직에 액세스
> 대부분의 비즈니스 로직이 데이터 레이어에 있지만 UI 레이어에도 비즈니스 로직이 포함할 수 있다.

<br>

* 화면 UI 상태를 만들기 위해 여러 저장소의 데이터를 결합한다.
* 특정 유형의 데이터에 데이터 레이어가 필요하지 않은 경우에 사용한다.

<br>

### Jetpack Compose
> Jetpack Compose를 사용할 때 ViewModel은 화면 UI 상태를 컴포저블에 노출하는 기본 수단이다.

<br>

#### Compose와 ViewModel을 사용할 때 유의해야 할 점은 ViewModel의 범위를 컴포저블로 지정할 수 없다.

##### 컴포저블이 ViewModelStoreOwner가 아니기 때문이다.

<br>

### ViewModel 구현

```kotlin
// 사용자가 주사위를 굴릴 수 있는 화면을 나타내는 ViewModel의 구현 예제
data class DiceUiState(
    val firstDieValue: Int? = null,
    val secondDieValue: Int? = null,
    val numberOfRolls: Int = 0,
)

class DiceRollViewModel : ViewModel() {

    private val _uiState = MutableStateFlow(DiceUiState())
    val uiState: StateFlow<DiceUiState> = _uiState.asStateFlow()

    fun rollDice() {
        _uiState.update { currentState ->
            currentState.copy(
                firstDieValue = Random.nextInt(from = 1, until = 7),
                secondDieValue = Random.nextInt(from = 1, until = 7),
                numberOfRolls = currentState.numberOfRolls + 1,
            )
        }
    }
}
```

<br>

```kotlin
// 다음과 같이 활동에서 ViewModel에 액세스할 수 있다.

import androidx.activity.viewModels

class DiceRollActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {

        val viewModel: DiceRollViewModel by viewModels()
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.uiState.collect {
                    // Update UI elements
                }
            }
        }
    }
}
```

<br>

### ViewModel의 수명 주기
> ViewModel의 수명 주기는 범위와 직접 연결됩니다.

<br>

#### ViewModel은 범위로 지정된 ViewModelStoreOwner가 사라질 때까지 메모리에 남아 있다.

![image](https://github.com/hyunho4532/TIL/assets/118269278/50b83eb1-8063-416b-8098-17183fd92ac9)

<br>

> 일반적으로 시스템에서 활동 객체의 onCreate() 메서드를 처음 호출할 때 ViewModel을 요청한다.

> ViewModel은 처음 ViewModel를 요청한 시점부터 활동이 끝나고 소멸될 때까지 존재한다.

<br>

### ViewModel 종속 항목 삭제
> ViewModel은 수명 주기에서 ViewModelStoreOwner에 의해 ViewModel이 소멸될 때 onCleared 메서드를 호출한다.

<br>

```kotlin
// 해당 예제는 viewModelScope의 대안을 보여준다.
// viewModelScope은 ViewModel의 수명 주기를 자동으로 따르는 내장 CoroutineScope 이다.
// ViewModel은 이를 사용하여 비즈니스 관련 작업을 트리거한다.
class MyViewModel(
    private val coroutineScope: CoroutineScope =
        CoroutineScope(SupervisorJob() + Dispatchers.Main.immediate)
) : ViewModel() {

    // Other ViewModel logic ...

    override fun onCleared() {
        coroutineScope.cancel()
    }
}
```
<br>

```kotlin
// 수명 주기 버전 2.5 이상부터 ViewModel 인스턴스가 삭제될 때 자동으로 닫히는 Closeable 객체를 한 개 이상 ViewModel 생성자에 전달할 수 있다.
class CloseableCoroutineScope(
    context: CoroutineContext = SupervisorJob() + Dispatchers.Main.immediate
) : Closeable, CoroutineScope {
    override val coroutineContext: CoroutineContext = context
    override fun close() {
        coroutineContext.cancel()
   }
}

class MyViewModel(
    private val coroutineScope: CoroutineScope = CloseableCoroutineScope()
) : ViewModel(coroutineScope) {
    // Other ViewModel logic ...
}
```
