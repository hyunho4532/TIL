## Summary
> 사용자가 앱 내의 여러 콘텐츠를 탐색하고, 그곳에 들어갔다 나올 수 있게 하는 상호작용을 의미한다.

<br>

> Navigation는 단순한 버튼을 클릭해서 좀 더 복잡한 패턴에 이르기까지 여러 가지 탐색을 구현하도록 도와준다.

<br>

### 탐색 구성요소는 세 가지 구성
* 탐색 그래프: 모든 탐색 관련 정보가 하나의 중심 위치에 모여 있는 XML 리소스이다.
* ```NavHost```: 탐색 그래프에서 대상을 표시하는 빈 컨테이너이다.
* ```NavController```: NavHost에서 앱 탐색을 관리하는 객체이다.

<br>

* 앱을 탐색하는 동안 탐색 그래프에서 특정 경로를 따라 이동할 지, 특정 대상으로 직접 이동할지 NavController에게 전달한다.
* 그러면 NavController가 NavHost에 적절한 대상을 표시한다.

<br>

### 대상 구성요소는 다음을 포함한 여러 가지 장점
* 프래그먼트 트랜잭션 처리
* 기본적으로 '위로'와 '뒤로' 작업을 올바르게 처리
* 애니메이션과 전환에 표준화된 리소스 제공
* 딥 링크 구현 및 처리
* 최소한의 추가 작업으로 탐색 UI 패턴(예: 탐색 창, 하단 탐색) 포함
* ViewModel 지원 - 탐색 그래프에 대한 ViewModel을 확인해 그래프 대상 사이에 UI 관련 데이터를 공유

<br>

### Navigation 시작하기
* 프로젝트에 탐색 지원을 포함하려면 앱의 build.gradle 파일에 다음 종속 항목을 추가한다.

```kotlin
dependencies {
  val nav_version = "2.5.3"

  // Java language implementation
  implementation("androidx.navigation:navigation-fragment:$nav_version")
  implementation("androidx.navigation:navigation-ui:$nav_version")

  // Kotlin
  implementation("androidx.navigation:navigation-fragment-ktx:$nav_version")
  implementation("androidx.navigation:navigation-ui-ktx:$nav_version")

  // Feature module Support
  implementation("androidx.navigation:navigation-dynamic-features-fragment:$nav_version")

  // Testing Navigation
  androidTestImplementation("androidx.navigation:navigation-testing:$nav_version")

  // Jetpack Compose Integration
  implementation("androidx.navigation:navigation-compose:$nav_version")
}
```
