## Summary
> 종속 항목 삽입(DI)은 프로그래밍에 널리 사용되는 기법으로, Android 개발에 적합하다.

<br>

## DI 특징 
  * 코드 재사용 가능
  * 리팩토링 편의성
  * 테스트 편의성

<br>

## 종속 항목 삽입이란?
  * 클래스에는 흔히 다른 클래스 참조가 필요하다.

<br>

#### 예를 들어 Car 클래스는 Engine 클래스 참조가 필요할 때
  * 이처럼 필요한 클래스를 종속 항목이라고 한다.
  * Car 클래스가 실행되기 위해서는 Engine 클래스의 인스턴스가 있어야 합니다.

<br>

#### 클래스가 필요한 객체를 얻는 세 가지 방법
  * 클래스가 필요한 종속 항목을 구성 (Car는 자체 Engine 인스턴스를 생성하여 초기화)
  * 다른 곳에서 객체를 가져온다.
  * 객체를 매개변수로 제공받는다. (Car 생성자는 Engine을 매개변수로 받습니다.)

<br>

```java
// 종속 항목 삽입 없이 코드에서 자체 Engine 종속 항목을 생성하는 Car를 나타내는 예제
class Car {

    private Engine engine = new Engine();

    public void start() {
        engine.start();
    }
}

class MyApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();
    }
}
```

<br>

#### 위의 방식처럼 코드를 작성할 경우 문제가 발생할 수 있다.
  * #### Car와 Engine은 밀접하게 연결되어 있다.
    * ##### Car 인스턴스는 한 가지 유형의 ```Engine```을 사용하므로 서브클래스 또는 대체 구현을 쉽게 사용할 수 없다.
    * ##### Car가 자체 Engine을 구성했다면 Gas 유형의 엔진에 동일한 Car를 재사용하는 대신 두 가지 유형의 Car를 생성해야 한다.

  <br>
    
  * #### Engine의 종속성이 높은 경우 테스트하기가 더욱 어려워진다.
    * ##### Car는 실제 Engine 인스턴스를 사용하므로 다양한 테스트 사례에서 테스트 더블을 사용하여 Engine을 수정할 수 없다.
   
<br>

```java
// 종속 항목 삽입
// Car의 각 인스턴스는 초기화 시 자체 Engine 객체를 구성하는 대신 Engine 객체를 생성자의 매개변수로 받는다.
class Car {

    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
    }
}

class MyApp {
    public static void main(String[] args) {
        Engine engine = new Engine();
        Car car = new Car(engine);
        car.start();
    }
}
```

<br>

#### main 함수에서 Car를 사용합니다.
  * #### Car는 Engine에 종속되므로 Engine 인스턴스를 생성한 후 사용하여 Car 인스턴스를 구성한다.

  * #### DI 기반 접근 방법의 이점
    * #### Car의 재사용 가능 (Engine의 다양한 구현을 Car에 전달할 수 있다)
      * ##### Car에서 사용할 ElectricEngine이라는 새로운 Engine 서브클래스를 정의할 수 있다.
    * #### Car의 테스트 편의성. 테스트 더블을 전달하여 다양한 시나리오를 테스트할 수 있다.
      * ##### FakeEngine이라는 Engine의 테스트 더블을 생성하여 다양한 테스트에 맞게 구성할 수 있다.
     
<br>

#### Android에서 종속 항목 삽입을 실행하는 두 가지 주요 방법
  * ##### 생성자 삽입 (클래스의 종속 항목을 생성자에 전달)
  * ##### 필드 삽입 (종속 항목은 클래스가 생성된 후 인스턴스화)

<br>

```java
class Car {

    private Engine engine;

    public void setEngine(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
    }
}

class MyApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.setEngine(new Engine());
        car.start();
    }
}
```

<br>

## 종속 항목 삽입의 대안
  * 종속 항목 삽입의 대안은 서비스 로케이터를 사용한다.
  * 서비스 로케이터 디자인 패턴도 구체적인 종속 항목으로부터 클래스가 잘 분리되도록 한다.

<br>

```java
class ServiceLocator {

    private static ServiceLocator instance = null;

    private ServiceLocator() {}

    public static ServiceLocator getInstance() {
        if (instance == null) {
            synchronized(ServiceLocator.class) {
                instance = new ServiceLocator();
            }
        }
        return instance;
    }

    public Engine getEngine() {
        return new Engine();
    }
}

class Car {

    private Engine engine = ServiceLocator.getInstance().getEngine();

    public void start() {
        engine.start();
    }
}

class MyApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();
    }
}
```

<br>

### 종속 항목 삽입과 비교 시 어떤 점이 다를까?
  * #### 서비스 로케이터에 필요한 종속 항목 컬렉션은 코드를 테스트하기가 더 어렵다.
    * ##### 모든 테스트가 동일한 전역 서비스 로케이터와 상호작용해야 하기 때문이다.
  * #### 종속 항목은 API 노출 영역이 아닌 클래스 구현에서 인코딩된다.
    * ##### 따라서, 클래스가 외부에서 필요한 것이 무엇인지 알기가 더 어렵다.
  * #### 전체 앱의 기간을 다른 기간으로 범위를 지정하려는 경우 객체의 전체 기간을 관리하기가 더 어렵다.

<br>

### 결론
  * #### 클래스 재사용 가능 및 종속 항목 분리
    * ##### 종속 항목 구현을 쉽게 교체할 수 있다.
  * #### 리팩터링 편의성
    * ##### 종속 항목은 API 노출 영역의 검증 가능한 요소가 되므로 구현 세부정보로 숨겨지지 않는다.
  * #### 테스트 편의성
    * ##### 클래스는 종속 항목을 관리하지 않으므로 테스트 시 다양한 구현을 전달하여 다양한 모든 사례를 테스트할 수 있다.
  
