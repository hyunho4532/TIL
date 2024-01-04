## Summary
> ```LiveData``` 는 관찰 가능한 데이터 홀더 클래스이다.

<br>

### LiveData
* 관찰 가능한 일반 클래스와 달리 LiveData는 수명 주기를 인식한다.
* 즉, 액티비티, 프래그먼트, 서비스 등 다른 앱 구성 요소의 수명 주기를 고려한다.
* LiveData는 활동 수명 주기 상태에 있는 앱 구성요소 관찰자만 업데이트한다.

<br>

### Observer
* ```Observer``` 클래스로 표현되는 관찰자의 수명 주기가 ```STARTED``` 또는 ```RESUMED``` 상태면 관찰자를 활성 상태로 간주한다.
* LiveData는 활성 관찰자에게만 업데이트 정보를 알린다.
* ```LiveData``` 객체를 보기 위해 등록된 비활성 관찰자는 변경사항에 관한 알림을 받지 않는다.

<br>

### LifecycleOwner
* ```LifecycleOwner``` 인터페이스를 구현하는 객체와 페어링된 관찰자를 등록할 수 있다.
* 이 관계를 사용하면 관찰자에 대응되는 Lifecycle 객체의 상태가 DESTROYED로 변경될 때 관찰자를 삭제할 수 있다.
* 이는 특히 액티비티, 프래그먼트에 유용하다.

<br>

## LiveData의 장점
### UI와 데이터 상태의 일치 보장
* LiveData는 기본 데이터가 변경될 때 Observer 객체에 알린다.
* 코드를 통합하여 이러한 Observer 객체에 UI를 업데이트할 수 있습니다.
* 앱 데이터가 변경될 때마다 관찰자가 대신 UI를 업데이트하므로 개발자가 업데이트할 필요가 없다.

<br>

### 메모리 누수 없음
* 관찰자는 Lifecycle 객체에 결합되어 있으며 연결된 수명 주기가 끝나면 자동으로 삭제된다.

<br>

### 중지된 활동으로 인한 비정상 종료 없음
* 관찰자의 수명 주기가 비활성 상태에 있으면 관찰자는 어떤 LiveData 이벤트도 받지 않는다.

<br>

### 수명 주기를 더 이상 수동으로 처리하지 않음
* UI 구성요소는 관련 데이터를 관찰하기만 할 뿐 관찰을 중지하거나 다시 시작하지 않는다.
* LiveData는 관찰하는 동안 관련 수명 주기 상태의 변경을 인식하므로 이 모든 것을 자동으로 관리한다.

<br>

### 최신 데이터 유지
* 수명 주기가 비활성화되면 다시 활성화 될 때 최신 데이터를 수신한다.

<br>

### 적절한 구성 변경
* 기기 회전과 같은 구성 변경으로 인해 활동 또는 프래그먼트가 다시 생성되면 사용 가능한 최신 데이터를 즉시 받게 됩니다.

<br>

### 리소스 공유
* 앱에서 시스템 서비스를 공유할 수 있도록 싱글톤 패턴을 사용하는 LiveData 객체를 확장하여 시스템 서비스를 래핑할 수 있습니다.

<br>

## LiveData 객체 사용 방법
### LiveData 객체를 사용하려면 아래 단계를 따르자.

1. 특정 유형의 데이터를 보유할 LiveData의 인스턴스를 생성한다.
2. ```onChanged()``` 메서드를 정의하는 ```Observer``` 객체를 만든다. (데이터 변경 시 발생하는 작업)
3. ```observe()```  메서드를 사용하여 LiveData 객체에 Observer 객체를 연결한다.

<br>

## LiveData 객체 만들기
> LiveData는 Collections를 구현하는 List와 같은 객체를 비롯하여 모든 데이터와 함께 사용할 수 있는 래퍼이다.
```kotlin
// 처음에는 LiveData 객체의 데이터를 설정하지 않았다.
class NameViewModel : ViewModel() {
  val currentName: MutableLiveData<String> by lazy {
      MutableLiveData<String>()
  }
}
```

<br>

## LiveData 객체 관찰
### 대부분의 경우 앱 구성요소의 onCreate() 메서드는 LiveData 객체 관찰을 시작하기 적합한 장소이다.

<br>

1. 시스템이 활동이나 프래그먼트의 onResume() 메서드에서 중복 호출을 하지 않도록 하기 위해서
2. 활동이나 프래그먼트에 활성 상태가 되는 즉시 표시할 수 있는 데이터가 포함되도록 하기 위함

```kotlin
// LiveData 객체 관찰을 시작하는 방법
// 코드 설명: nameObserver를 매개변수로 전달하여 observe()를 호출하면 onChanged()가 즉시 호출되어 mCurrentName에 저장된 최신 값을
class NameActivity : AppCompatActivity() {

    // 특정 유형의 데이터를 보유할 LiveData의 인스턴스 생성
    private val model: NameViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Observer 객체 생성
        val nameObserver = Observer<String> { newName ->
            nameTextView.text = newName
        }

        // observe() 메서드를 사용하여 LiveData 객체에 Observer 객체를 연결
        model.currentName.observe(this, nameObserver)
    }
}
```

<br>

## LiveData 객체 업데이트
> LiveData에는 저장된 데이터를 업데이트하는 데 공개적으로 사용할 수 있는 메서드가 없습니다.

```kotlin
button.setOnClickListener {
    val anotherName = "John Doe"
    model.currentName.setValue(anotherName)
}
```

<br>

#### 위의 예에서 setValue(T)를 호출하면 관찰자는 John Doe 값과 함께 onChanged() 메서드를 호출

모든 경우에 setValue() 또는 postValue()를 호출하면 관찰자가 트리거되고 UI가 업데이트됩니다.

<br>

## 앱 아키텍처의 LiveData 
> LiveData는 수명 주기를 인식하여 활동과 프래그먼트 등 항목의 수명 주기를 따릅니다

<br>

1. LiveData를 사용하여 수명 주기 소유자와 ViewModel 객체 등 수명이 다른 객체 간에 통신할 수 있다.
2. ViewModel은 기본적으로 UI 관련 데이터를 로드하고 관리하는 역할을 하므로 LiveData 객체를 보유하는 데 적합하다.
3. LiveData 객체를 ViewModel에 만들고 이를 사용하여 UI 레이어에 상태를 노출합니다.

```kotlin
// Repository에 LiveData를 보유하여 기본 스레드를 차단하는 방식을 보여주는 방법
class UserRepository {

    fun getUsers(): LiveData<List<User>> {
        ...
    }

    fun getNewPremiumUsers(): LiveData<List<User>> {
        return getUsers().map { users ->
            users
                .filter { user ->
                  user.isPremium
                }
          .filter { user ->
              val lastSyncedTime = dao.getLastSyncedTime()
              user.timeCreated > lastSyncedTime
                }
    }
}
```

<br>

## LiveData 확장
> 관찰자의 수명 주기가 STARTED 또는 RESUMED 상태이면 LiveData는 관찰자를 활성 상태로 간주한다.

```kotlin
// LiveData 클래스를 확장하는 방법
class StockLiveData(symbol: String) : LiveData<BigDecimal>() {
    private val stockManager = StockManager(symbol)

    // LiveData 인스턴스의 값을 업데이트하고 모든 활성 상태의 관찰자에게 변경사항을 알립니다.
    private val listener = { price: BigDecimal ->
        value = price
    }

    // LiveData 객체에 활성 상태의 관찰자가 있을 때 호출된다. 즉, 이 메서드에서 주가 업데이트 관찰을 시작해야 한다.
    override fun onActive() {
        stockManager.requestPriceUpdates(listener)
    }

    // LiveData 객체에 활성 상태의 관찰자가 없을 때 호출된다.
    override fun onInactive() {
        stockManager.removeUpdates(listener)
    }
}
```

```kotlin
// StockLiveData 클래스를 사용하는 방법
public class MyFragment : Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        val myPriceListener: LiveData<BigDecimal> = ...

        // observe() 메서드는 프래그먼트 뷰와 연결된 LifecycleOwner를 첫 번째 인수로 전달
        myPriceListener.observe(viewLifecycleOwner, Observer<BigDecimal> { price: BigDecimal? ->
            // Update the UI.
        })
    }
}
```
