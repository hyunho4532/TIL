## Summary
> 오픈소스 데이터베이스 관리시스템(DBMS) 이며 모바일 환경 데이터베이스이다.

<br>

### Realm의 특징
* Realm은 매우 작은 리소스를 사용하고 사용하기 쉽다.
* 빠르게 데이터와 상호 작용 가능하다.
* NoSQL 데이터베이스를 지향하며 rawSQL을 사용할 수 없어 Realm API를 통해서 실행된다.
* 복잡한 Entity에 대한 매핑을 처리해야할 문제가 없다.
* 메모리 상의 객체를 디스크로 빠르게 가져올 수 있다.

<br>

### Realm vs Sqlite
* Realm은 다른 플랫폼과의 연동이 가능하다 (크로스 플랫폼 지원)
* SQlite는 작고 가벼워 전체 데이터 베이스를 하나의 디스크 파일에 저장할 수 있다.

<br>

### 시작하기
> 프로젝트 레벨의 gradle 파일에 아래와 같이 class path를 추가한다.

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "io.realm:realm-gradle-plugin:10.11.0"
    }
}
```

<br>

> 애플리케이션 레벨의 gradle 파일에 플러그인을 추가한다.

```
plugins {
  id 'com.android.application'
  id 'org.jetbrains.kotlin.android'
  id 'kotlin-kapt'
  id 'realm-android'
}
```

<br>

### Realm 초기화
> Application 클래스에서 Realm 을 초기화한다.

```kotlin
class RealmDBApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        Realm.init(this)
    }
}
```

<br>

> AndroidManifest.xml에 Application class를 등록한다.

```
<application
  android:name=".RealmDBApplication"
  ...
/>
```

<br>

### Realm 설정하기
> Realm 을 설정하기 위해 ```RealmCongiguration``` 객체를 사용한다.

```kotlin
val config = RealmConfiguration.Builder().build()
```

<br>

```kotlin
// Realm을 설정하는 근본적인 방법

val realmSettingConfig: RealmConfiguration = RealmConfiguration.Builder()
        .allowWritesOnUiThread(true)     // UI Thread에서도 realm에 접근할 수 있도록 한다.
        .deleteRealmIfMigrationNeeded()  // 데이터베이스의 틀에 변경사항이 생기면 저장되어있던 내용들을 모두 삭제
        .build()

// Use the config
val realm = Realm.getInstance(realmSettingConfig)
```

<br>

### RealmConfiguration을 Default로 설정하기
> Custom Application class에서 RealmConfiguration을 default로 설정할 수 있다.

```kotlin
class RealmDBApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        Realm.init(this)

        val realmSettingConfig: RealmConfiguration = RealmConfiguration.Builder()
            .name("test.realm")
            .allowWritesOnUiThread(true)
            .deleteRealmIfMigrationNeeded()
            .build()

        Realm.setDefaultConfiguration(config)
    }
}

class MainActivity : Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val realm = Realm.getDefaultInstance() // opens "test.realm"

        try {
            // ... Do something ...
        } finally {
            realm.close()
        }
    }
}
```

<br>

### Realm 모델 생성하기
> RealmObject class 를 상속받아 구현한다.

```kotlin
// 모델을 생성할 때 class 앞에 open으로 정의하여 클래스를 열어주자!

open class User (
    var email: String = "",
    var password: String = "",

    @Ignore
    var sessionId: Int = 0
): RealmObject()
```

<br>

#### Required fields
* 필드에서 null을 허용하지 않을꺼면 @Require를 필드에 추가한다.

<br>

#### 속성 인덱싱
##### * @Index annotation을 추가해주면 그 필드를 기준으로 인덱싱된다.
##### * ```데이터 추가```가 느려지며 ```파일이 커지는 단점```이 있다. 대신 ```query```가 빨라진다.
##### * String, Byte, Shore, Int, Long, Boolean, Date 필드에서만 사용 가능하다.

<br>

#### Primary Key
##### * @PrimaryKey annotation 을 사용해서 기본키를 설정한다.
##### * @PrimaryKey 는 @Index annotation을 포함하고 있다.

<br>

#### @Ignore
##### * Realm 필드를 저장하고 싶지 않을 때 사용한다.
##### * static, trasient은 항상 @Ingnore로 설정된다.

<br>

### 데이터 저장하기 (INSERT)
> 데이터를 저장하기 위해, Realm.Transaction 인터페이스를 구현한 클래스를 만들어야 한다.

```kotlin
// 회원가입 데이터 저장하기
realm.executeTransaction { realm ->
  val user: User = realm.createObject(
        User::class.java)
  
  user.email = email
  user.password = password
}
```

<br>

### 데이터 가져오기 (SELECT)
> findFirst를 통해 데이터를 가져온다.

```kotlin
val user = realm.where(User::class.java).equalTo("email", email).findFirst()
```

<br>

#### 데이터를 가져오기 위해 다양한 함수들을 제공한다.
* ##### findAll()
* ##### findAllSorted(String fieldName)
* ##### findAllSorted(String[] fieldNames, Sort[] sortOrders)
* ##### findAllSorted(String fieldName, Sort sortOrder)
* ##### findAllSorted(String fieldName1, Sort sortOrder1, String fieldName2, Sort sortOrder2)

<br>

### 데이터 삭제 (DELETE)

```kotlin
val user = realm.where(User::class.java).equalTo("email", email).findFirst()
user.deleteFromRealm()
```

<br>

### 모든 데이터 삭제 (DROP)

```kotlin
realm.delete(User.class)
```

<br>

### 결론
* ##### 현재 모바일에서 가장 많이 쓰이고 개발자들이 애용하는 로컬 DB 이다.
* ##### NoSQL이라서, 사용하기 편하다.
* ##### 성능과 빠른 로딩을 제공한다. 
