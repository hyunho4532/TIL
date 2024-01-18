## Summary
> WorkManager는 지속적인 작업에 권장되는 솔루션이다.

<br>

* 앱이 다시 시작되거나 시스템이 재부팅될 때 작업이 예약된 채로 남아 있으면 그 작업은 유지된다.
* 백그라운드 처리는 지속적인 작업을 통해 가장 잘 처리되므로 WorkManager는 백그라운드 처리에 권장하는 기본 API이다.

<br>

### 지속적인 작업의 유형
> WorkManager가 처리하는 지속적인 작업의 유형은 세 가지이다.

<br>

* 즉시: 즉시 시작하고 곧 완료해야 하는 작업 (신속하게 처리)
* 장기 실행: 더 오래(10분 이상이 될 수 있음) 실행될 수 있는 작업
* 지연 가능: 나중에 시작하며 주기적으로 실행될 수 있는 예약된 작업

![image](https://github.com/hyunho4532/TIL/assets/118269278/8b5003d6-ac02-4bec-9dd0-fc8d62a0f02d)

<br>

|유형|주기성|액세스 방법|
|------|---|---|
|즉시|1회|OneTimeWorkRequest 및 Worker|
|장기 실행|1회 또는 주기적|모든 WorkRequest 또는 Worker|
|지연 기능|1회 또는 주기적|PeriodicWorkRequest 및 Worker|

<br>

### 특징
> WorkManager는 더 간단하고 일관성 있는 API를 제공할 뿐만 아니라 여러 가지 중요한 이점을 제공한다.

<br>

* ### 작업 제약 조건
  * 작업 제약 조건을 사용하여 작업을 실행하는 데 최적인 조건을 선언적으로 정의한다.
  * 제약 조건은 최적의 조건이 충족될 때까지 작업이 지연되도록 한다.

<br>

|제약 조건|설명|
|------|---|
|NetworkType|작업을 실행하는 데 필요한 네트워크 유형을 제한한다.|
|BatteryNotLow|true로 설정하면 기기가 배터리 부족 모드인 경우 작업이 실행되지 않는다.|
|RequiresCharging|true로 설정하면 기기가 충전 중일 때만 작업이 실행된다.|
|DeviceIdle|true로 설정하면 작업이 실행되기 전에 사용자 기기가 유휴 상태여야 한다.|
|StorageNotLow|true로 설정하면 사용자의 기기 저장공간이 너무 부족한 경우 작업이 실행되지 않는다.|

<br>
 
* ### 강력한 예약 관리
  * WorkManager를 사용하면 가변 일정 예약 기간을 통해 한 번 또는 반복적으로 실행할 작업을 예약할 수 있다.
  * 작업에 태그 및 이름을 지정하여 고유 작업 및 대체 가능한 작업을 예약하고 작업 그룹을 함께 모니터링하거나 취소할 수 있다.

<br>

* ### 신속 처리 작업
  * WorkManager를 사용하여 백그라운드에서 즉시 실행할 작업을 예약할 수 있다.
  * 사용자에게 중요하고 몇 분 내에 완료되는 작업에는 신속 처리 작업을 사용해야 한다.

<br>

* ### 유연한 재시도 정책
  * 경우에 따라 작업이 실패하면. WorkManager는 구성 가능한 지수 백오프 정책을 비롯해 유연한 재시도 정책을 제공한다.

<br>

* ### 작업 체이닝

```kotlin
// 복잡한 관련 작업의 경우 직관적인 인터페이스를 사용하여 개별 작업을 함께 체이닝하면 순차적으로 실행할 작업과 동시에 실행할 작업을 제어할 수 있다.
val continuation = WorkManager.getInstance(context)
    .beginUniqueWork(
        Constants.IMAGE_MANIPULATION_WORK_NAME,
        ExistingWorkPolicy.REPLACE,
        OneTimeWorkRequest.from(CleanupWorker::class.java)
    ).then(OneTimeWorkRequest.from(WaterColorFilterWorker::class.java))
    .then(OneTimeWorkRequest.from(GrayScaleFilterWorker::class.java))
    .then(OneTimeWorkRequest.from(BlurEffectFilterWorker::class.java))
    .then(
        if (save) {
            workRequest<SaveImageToGalleryWorker>(tag = Constants.TAG_OUTPUT)
        } else /* upload */ {
            workRequest<UploadWorker>(tag = Constants.TAG_OUTPUT)
        }
    )
```

<br>

* ### 내장 스레딩 상호 운용성
> WorkManager는 코루틴 및 RxJava와 원활하게 통합되며 자체 비동기 API를 연결할 수 있는 유연성을 제공한다.

<br>

* ### 안정적인 작업에 WorkManager 사용하기
> 사용자가 화면을 벗어나 이동, 앱이 종료, 기기가 다시 시작되더라도 안정적으로 실행되어야 하는 작업을 대상으로 설계했다.

<br>

* WorkManager는 앱 프로세스가 사라지더라도 안전하게 종료될 수 있는 진행 중인 백그라운드 작업을 위한 것이 아님!
* 즉각적인 실행이 필요한 모든 작업을 위한 일반적인 솔루션도 아님!
* 요구사항에 맞는 적합한 솔루션을 알아보려면 백그라운드 처리 가이드를 검토하자.

<br>

* ### 다른 API와의 관계
> 코루틴은 특정 사용 사례에 권장되는 솔루션이지만, 지속적인 작업에 사용해서는 안된다.

> 코루틴은 동시 실행 ```프레임워크```이고 WorkManager는 지속적인 작업을 위한 ```라이브러리```이다.

<br>

|API|추천 대상|WorkManager와의 관계|
|------|--------|------|
|코루틴|지속적이지 않은 모든 비동기 작업|코루틴은 Kotlin에서 기본 스레드를 벗어나는 표준 방식이지만, 앱이 종료된 후에는 메모리를 남긴다.|
|AlarmManager|알람에만 사용|WorkManager와 달리 AlarmManager는 잠자기 모드인 기기의 절전 모드를 해제한다.|

<br>

* ### 일련의 제약 조건을 만들고 이를 일부 작업과 연결하기
  * Contraints.Builder()를 사용하여 Constraints 인스턴스를 만들어 WorkRequest.Builder()에 할당한다.

<br>
 
```kotlin
// 사용자의 기기가 충전 중이고 Wi-Fi에 연결되어 있을 때만 실행되는 작업 요청을 빌드하는 예제
val constraints = Constraints.Builder()
   .setRequiredNetworkType(NetworkType.UNMETERED)
   .setRequiresCharging(true)
   .build()

val myWorkRequest: WorkRequest =
   OneTimeWorkRequestBuilder<MyWork>()
       .setConstraints(constraints)
       .build()
```

<br>

* ### 신속 처리 작업 예약하기
  * WorkManager는 시스템에 더 효율적인 리소스 액세스 제어 권한을 부여하면서 중요한 작업을 실행할 수 있다.

<br>

* ### 신속 처리 작업 특성
  * 중요도: 신속 처리 작업은 사용자에게 중요하거나 사용자가 시작한 작업에 적합하다.
  * 속도: 신속 처리 작업은 즉시 시작되어 몇 분 안에 끝나는 짧은 작업에 가장 적합하다.
  * 할당량: 포그라운드 실행 시간을 제한하는 시스템 수준 할당량에 따라 신속 처리 작업의 시작 가능 여부가 결정된다.
  * 전원 관리: 절전 모드, 잠자기와 같은 전력 관리 제한사항은 신속 처리 작업에 영향을 미칠 가능성이 적다.
  * 지연 시간: 시스템의 현재 워크로드로 처리가 가능한 경우 시스템은 신속 처리 작업을 즉시 실행한다.

<br>
 
* ### 재시도 및 백오프 정책
  * WorkManager에서 작업을 다시 시도해야 하는 경우 작업자에서 Result.retry()를 반환한다.

<br>
 
* #### 백오프 지연과 정책에 따라 작업이 다시 예약된다.
  * 백오프 지연은 첫 번째 시도 후 작업을 다시 시도하기 전에 대기해야 하는 최소 시간을 지정한다.
  * 백오프 정책은 다음 재시도의 백오프 지연이 시간 경과에 따라 증가하는 방식을 정의한다.
 
<br>

```kotlin
// 백오프 지연 및 정책을 맞춤설정하는 예시
// 이 예시에서는 최소 백오프 지연이 최소 허용 값 10초로 설정된다.
// 정책이 LINEAR이므로 재시도 간격은 새로 시도할 때마다 약 10초씩 증가한다.
val myWorkRequest = OneTimeWorkRequestBuilder<MyWork>()
   .setBackoffCriteria(
       BackoffPolicy.LINEAR,
       OneTimeWorkRequest.MIN_BACKOFF_MILLIS,
       TimeUnit.MILLISECONDS)
   .build()
```

