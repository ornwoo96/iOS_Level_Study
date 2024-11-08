# 레벨 1

## 1. **Swift에서 옵셔널(Optional)이란 무엇이며, 언제 사용해야 하나요?**
**옵셔널(Optional)** 은 Swift에서 값이 있을 수도 있고 없을 수도 있는 변수를 표현하는 타입입니다. 옵셔널을 사용하면 변수가 nil(값이 없음)을 가질 수 있으며, 값이 존재하지 않는 경우를 안전하게 처리할 수 있습니다.

<br>

### 옵셔널의 정의

옵셔널은 ?를 사용하여 선언하며, 이는 해당 변수에 값이 없을 경우 nil이 할당될 수 있음을 나타냅니다. 타입 뒤에 ?를 붙여 옵셔널을 정의합니다.

```swift
var name: String? = "Alice"  // 옵셔널 String 타입
name = nil  // 유효, name은 nil을 가질 수 있음
```

<br>

### 옵셔널을 사용해야 하는 경우
#### 1.	값이 없을 수 있는 경우
- 사용자가 입력을 제공하지 않은 필드나, 네트워크 요청에서 값이 없을 수 있는 변수 등을 다룰 때 유용합니다.
- 예: 사용자가 필드를 비워두거나 데이터를 입력하지 않은 경우 nil 값을 저장할 수 있습니다.

```swift
var userInput: String?  // 사용자의 입력값이 없을 수 있음
```

<br>

#### 2.	데이터 변환이 실패할 수 있는 경우

- String을 Int로 변환할 때, 변환이 실패하면 nil이 될 수 있습니다. 옵셔널을 사용하여 안전하게 변환할 수 있습니다.

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)  // Int? 타입
```

<br>


#### 3.	비동기 작업의 결과
- 네트워크 요청이나 파일 읽기 같은 비동기 작업은 데이터가 없거나 로딩 실패 시 nil을 반환할 수 있습니다.

<br>

### 옵셔널 해제 (Unwrapping Optionals)
옵셔널 변수에서 값을 안전하게 사용하려면 옵셔널을 해제(Unwrapping) 해야 합니다. 옵셔널 해제 방식에는 여러 가지가 있습니다.

#### 1.	옵셔널 바인딩 (Optional Binding) (if let, guard let)
- 옵셔널 바인딩을 사용하여 옵셔널의 값을 안전하게 추출하고, nil인 경우를 처리할 수 있습니다.

```swift
var name: String? = "Alice"

if let unwrappedName = name {
    print("Hello, \(unwrappedName)")
} else {
    print("No name provided")
}
```

<br>

#### 2.	강제 해제 (Forced Unwrapping) (!)
- 강제 해제를 통해 옵셔널에서 값을 직접 추출할 수 있습니다.
- 하지만, 옵셔널이 nil인 경우 런타임 에러가 발생하므로 조심해서 사용해야 합니다.

```swift
var name: String? = "Alice"
print(name!)  // 강제 해제
```

<br>

#### 3.	Nil-Coalescing Operator (??)
- 옵셔널이 nil일 때 기본값을 제공하는 방식입니다.

```swift
var name: String? = nil
let displayName = name ?? "Guest"  // name이 nil인 경우 "Guest" 할당
```

<br>

### 옵셔널의 장점
옵셔널을 사용함으로써 값이 없는 상황을 안전하게 처리할 수 있으며, 런타임 오류를 줄이고 코드의 안정성을 높일 수 있습니다. Swift는 옵셔널을 통해 nil 값을 명시적으로 처리하도록 강제하여 프로그래머가 모든 경우를 안전하게 고려하도록 돕습니다.

<br>

### 옵셔널 바인딩과 강제 언래핑의 차이점 
<img src="https://github.com/user-attachments/assets/6f8fac57-fcd6-4499-b388-957c5fa27d88">

옵셔널 바인딩은 안전한 방식으로, 옵셔널이 nil인지 여부에 따라 분기 처리가 필요할 때 유용합니다. 반면, 강제 언래핑은 옵셔널이 nil이 아님을 확신할 때 간편하게 사용할 수 있지만, 실수로 nil 값을 강제 언래핑할 경우 앱이 비정상 종료될 위험이 있습니다.

<br>

### 옵셔널 체이닝의 동작 원리는 무엇이며, 어떻게 사용하나요?
**옵셔널 체이닝(Optional Chaining)** 은 옵셔널 타입의 값에 접근하거나 메서드를 호출할 때 값이 존재하면 실행하고, nil이면 nil을 반환하도록 하는 방식입니다. 옵셔널 체이닝을 사용하면 중첩된 옵셔널 속성이나 메서드를 간결하게 호출할 수 있습니다.

<br>

#### 옵셔널 체이닝의 동작 원리
옵셔널 체이닝은 옵셔널에 속한 값에 접근할 때 물음표(?) 연산자를 사용하여 옵셔널 값이 nil인지 검사합니다.

- 값이 존재할 경우: 옵셔널 체이닝이 해제되어 다음 속성이나 메서드로 접근이 가능합니다.
- 값이 nil일 경우: 호출이 실패하고 전체 표현식이 nil을 반환하며, 이후 체이닝은 중단됩니다. 이 경우 런타임 에러 없이 안전하게 nil을 반환합니다.

<br>

#### 옵셔널 체이닝 사용 예시
예를 들어, 다음과 같이 사람이 소유한 집의 주소를 확인한다고 가정해보겠습니다.

```swift
class Address {
    var city: String
    init(city: String) {
        self.city = city
    }
}

class Person {
    var address: Address?
}

let person = Person()
person.address = Address(city: "Seoul")
```

<br>

#### 옵셔널 체이닝 사용 전

```swift
if let address = person.address {
    print(address.city)
} else {
    print("Address is nil")
}
```

<br>


#### 옵셔널 체이닝 사용 후
옵셔널 체이닝을 사용하면 훨씬 간결하게 작성할 수 있습니다.

```swift
print(person.address?.city ?? "Address is nil")  // "Seoul" 출력
```

여기서 person.address가 nil이면 체이닝이 중단되고 nil이 반환됩니다.

<br>

### 옵셔널 체이닝의 활용 예시

#### 1. 중첩된 옵셔널 접근:
- 옵셔널 체이닝은 중첩된 옵셔널 속성에 접근할 때 특히 유용합니다.

```swift
class Company {
    var ceo: Person?
}

let company = Company()
print(company.ceo?.address?.city ?? "City not available")
```

<br>

#### 2.	메서드 호출과 옵셔널 체이닝:
- 옵셔널 체이닝을 통해 메서드를 안전하게 호출할 수도 있습니다. 메서드 호출 시 체이닝을 통해 옵셔널을 사용하면, 메서드가 nil이면 호출되지 않고 전체 표현식이 nil을 반환합니다.

```swift
class Book {
    func printTitle() {
        print("Swift Programming")
    }
}

var myBook: Book? = Book()
myBook?.printTitle()  // "Swift Programming" 출력

myBook = nil
myBook?.printTitle()  // 아무 동작도 수행하지 않음
```

<br>

#### 3.	옵셔널 체이닝과 서브스크립트:
- 서브스크립트에도 옵셔널 체이닝을 사용할 수 있습니다.

```swift
var dictionary: [String: [String]] = ["cities": ["Seoul", "New York"]]
let city = dictionary["cities"]?[0] // "Seoul" 반환
```

<br>

### 암시적 언래핑 옵셔널(Implicitly Unwrapped Optional)은 어떤 경우에 사용해야 하나요?
- **암시적 언래핑 옵셔널(Implicitly Unwrapped Optional)** 은 옵셔널 변수에 값이 반드시 존재함을 확신할 수 있을 때 사용합니다. 
- 암시적 언래핑 옵셔널은 선언 시 타입 뒤에 !를 붙여 정의하며, 일반 옵셔널처럼 nil을 가질 수 있지만, 언래핑 없이도 값을 바로 사용할 수 있는 특징이 있습니다.
- Swift는 암시적 언래핑 옵셔널을 사용하는 변수에 접근할 때 자동으로 언래핑을 수행합니다.

<br>

#### 암시적 언래핑 옵셔널의 정의
```swift
var name: String! = "Alice"  // 암시적 언래핑 옵셔널
print(name)                  // "Alice" 출력
print(name.count)            // 언래핑 없이 직접 사용 가능
```

<br>

#### 사용해야 하는 경우

#### 1.	초기화 시점에 nil일 수 있지만 이후에는 항상 값이 존재할 경우
- 예를 들어, 클래스의 초기화 과정에서 값이 없는 상태로 시작하더라도 초기화 이후에는 반드시 값이 설정되는 경우가 있습니다.
- 이때 암시적 언래핑을 사용하면 값에 접근할 때마다 매번 언래핑하지 않아도 됩니다.

```swift
class ViewController {
    var label: UILabel!
    
    func setup() {
        label = UILabel()
        label.text = "Hello, World!"
    }
}
```

<br>

#### 2.	의존 관계로 인해 나중에 설정될 것이 확실한 경우
- 객체 간에 강한 의존 관계가 있어 필수로 설정되어야 하는 값이 나중에 설정되는 경우에 사용됩니다.
- 예를 들어, 특정 프로퍼티가 다른 객체에 의해 할당될 것이 확실한 경우, 암시적 언래핑을 사용하여 초기화 과정에서 임시로 nil을 가질 수 있도록 설정합니다.

```swift
class Profile {
    var bio: String
    init(bio: String) {
        self.bio = bio
    }
}

class User {
    let name: String
    var profile: Profile!  // 나중에 할당될 것이 확실한 경우에 암시적 언래핑 옵셔널로 선언

    init(name: String) {
        self.name = name
    }

    func printProfile() {
        // profile이 초기화된 후에 이 메서드를 호출한다고 가정
        print("User profile: \(profile.bio)")
    }
}

// User 생성 후, 나중에 Profile을 설정
let user = User(name: "Alice")
user.profile = Profile(bio: "iOS Developer")

// profile이 설정된 후 메서드 호출
user.printProfile()  // 출력: User profile: iOS Developer
```

<br>

#### 3.	아웃렛(Outlet) 연결
- iOS 개발에서 스토리보드나 XIB 파일에서 뷰와 코드를 연결하는 아웃렛 변수는 초기화 시점에는 nil이지만, 인터페이스가 로드되면서 값이 설정됩니다.
- 이런 경우 아웃렛을 암시적 언래핑 옵셔널로 정의하여, 값이 설정된 후에는 매번 언래핑하지 않고 사용할 수 있습니다.

```swift
@IBOutlet var myLabel: UILabel!
```


<br>

#### 주의 사항
- 암시적 언래핑 옵셔널은 기본적으로 안전하지 않은 방식이기 때문에, 옵셔널이 nil일 가능성이 없는 경우에만 사용해야 합니다.
- 만약 암시적 언래핑 옵셔널이 nil인 상태에서 접근하게 되면 런타임 에러가 발생하므로, nil 여부를 확신할 수 없을 때는 일반 옵셔널을 사용하는 것이 안전합니다.


<br>


#### 요약
- 암시적 언래핑 옵셔널은 값이 항상 존재할 것으로 확신할 수 있는 경우에 사용하며, 사용 시 안전하지 않은 방식이므로 초기화 이후 반드시 값이 설정되는 경우에 한해 사용하는 것이 좋습니다.
- iOS 개발에서는 주로 아웃렛(IBOutlet) 연결이나 초기화 이후 필수 값이 할당되는 프로퍼티에서 사용됩니다.


<br>
<br>


## 2. **iOS 앱의 생명주기(App Life Cycle)에 대해 설명해주세요.**

<img src="https://github.com/user-attachments/assets/d91aef0c-593e-4c1b-92bb-b4c16da8a34d">

### Not Running
- 앱이 메모리에 로드되지 않은 상태로, 앱이 처음 실행되거나 사용자가 강제로 종료했을 때 이 상태가 됩니다.
- 가능한 작업 :
    - 이 상태에서는 앱이 실행되지 않으므로 코드가 실행되지 않습니다.
    - 사용자가 앱을 처음 실행할 때 필요한 초기 설정과 리소스를 로드할 준비를 합니다.

<br>

### Inactive
- 앱이 포그라운드에 있지만 이벤트를 처리하지 않는 상태입니다.
- 화면이 잠기거나, 전화 수신 등으로 인해 일시적으로 이벤트 처리가 중단될 수 있습니다.
- 가능한 작업:
    - 진행 중인 작업을 일시적으로 멈추거나, UI 업데이트를 중단할 수 있습니다.
    - 게임의 경우, 일시정지(pause) 상태로 만들어 사용자 활동이 재개될 때까지 대기합니다.
    - 애니메이션 또는 타이머를 중단하여 불필요한 리소스 소비를 막습니다.
 
<br>

### Active
- 앱이 포그라운드에 있으며 사용자와 상호작용하는 상태로, 앱의 모든 기능이 활성화됩니다.
- 가능한 작업:
    - 앱의 주요 기능을 수행하며, UI와 상호작용, 네트워크 요청 등 다양한 작업을 수행합니다.
    - 게임의 경우, 일시정지 상태에서 벗어나 재생 상태로 전환할 수 있습니다.
    - 실시간으로 사용자 입력에 대응하고, 화면 업데이트를 수행합니다.
    - 필요 시 데이터를 주기적으로 업데이트하고, 백엔드와 통신을 지속적으로 유지합니다.

<br>

### Background
- 앱이 백그라운드에서 실행 중이며, 제한된 작업을 수행할 수 있는 상태입니다.
- 백그라운드 상태에 있는 앱은 제한된 시간 동안 작업을 수행할 수 있으며, 주로 데이터 저장이나 종료 준비 작업을 수행합니다.
- 가능한 작업:
    - 앱이 종료될 경우를 대비해 중요한 데이터를 저장합니다. 예를 들어, 사용자 작업 상태, 위치 정보 등을 로컬 저장소나 서버에 저장할 수 있습니다.
    - 네트워크 요청을 완료하거나 다운로드 작업을 계속 수행할 수 있습니다.
    - 백그라운드 모드가 활성화된 경우(예: 위치 업데이트, 음악 재생), 관련 작업을 계속할 수 있습니다.
    - Background Task를 등록하여 작업이 완료될 때까지 수행할 수 있도록 합니다.
 
<br>


### 상태 변화에 따라 호출되는 `AppDelegate` 또는 `SceneDelegate` 메서드는 무엇인가요?
<img src="https://github.com/user-attachments/assets/fe445043-c958-4894-b82f-4e29f742049e">


<br>

### 백그라운드에서 작업을 완료하기 위한 방법은 어떤 것이 있나요?

#### 1. Background Fetch
- 개념:
    - 앱이 백그라운드에서 주기적으로 데이터를 업데이트할 수 있도록 iOS가 호출해주는 기능입니다.
- 사용 방법:
    - setMinimumBackgroundFetchInterval을 설정하여 앱이 특정 시간마다 백그라운드에서 데이터를 가져올 수 있도록 요청합니다.
    - application(_:performFetchWithCompletionHandler:) 메서드를 구현하여 데이터를 업데이트하고 작업이 완료되면 completionHandler를 호출합니다.
- 제한 사항:
    - iOS 시스템이 백그라운드 실행 주기를 최적화하므로 정확한 호출 주기는 보장되지 않습니다. 주로 뉴스 피드나 소셜 미디어 앱에서 데이터 새로고침에 사용됩니다.
 
```swift
// AppDelegate.swift

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    application.setMinimumBackgroundFetchInterval(UIApplication.backgroundFetchIntervalMinimum)
    return true
}

func application(_ application: UIApplication, performFetchWithCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    // 백그라운드에서 데이터를 가져오는 작업
    fetchData { newData in
        if newData {
            completionHandler(.newData)  // 새 데이터가 있음을 알림
        } else {
            completionHandler(.noData)   // 새 데이터가 없음
        }
    }
}

func fetchData(completion: @escaping (Bool) -> Void) {
    // 네트워크 호출 또는 데이터 업데이트 코드
    completion(true)  // 예시로 새 데이터가 있다고 가정
}
```

<br>

#### 2. Background Task
- 개념: 앱이 백그라운드로 이동한 후에도 작업을 계속 수행할 수 있도록 잠시 실행 시간을 부여받는 기능입니다.
- 사용 방법: beginBackgroundTask(withName:expirationHandler:)를 호출하여 작업을 시작합니다. 작업이 완료되면 endBackgroundTask를 호출하여 작업을 종료해야 합니다. 만약 지정된 시간 내에 작업이 끝나지 않으면 시스템이 expirationHandler를 호출하고 작업을 종료합니다.
- 제한 사항: 일반적으로 30초 정도의 시간이 주어지며, 시간이 초과되면 작업이 강제로 종료됩니다. 주로 파일 업로드나 데이터 저장 등 빠르게 완료해야 하는 작업에 사용됩니다.

```swift
// AppDelegate.swift

func applicationDidEnterBackground(_ application: UIApplication) {
    var backgroundTask: UIBackgroundTaskIdentifier = .invalid
    
    backgroundTask = application.beginBackgroundTask(withName: "FinishImportantTask") {
        application.endBackgroundTask(backgroundTask)
        backgroundTask = .invalid
    }
    
    // 백그라운드에서 실행할 작업
    finishImportantTask {
        application.endBackgroundTask(backgroundTask)
        backgroundTask = .invalid
    }
}

func finishImportantTask(completion: @escaping () -> Void) {
    // 데이터를 저장하거나 서버로 전송하는 작업
    completion()
}
```

<br>

#### 3. Silent Push Notifications
- 개념: 푸시 알림을 통해 앱이 백그라운드에서 데이터를 가져오도록 하는 방식입니다. 사용자에게 알림이 표시되지 않으며, 앱만 조용히 알림을 받아 작업을 수행할 수 있습니다.
- 사용 방법: 푸시 알림의 content-available 키를 설정하여 앱에 새 데이터를 가져오도록 합니다. 푸시 알림을 수신하면 application(_:didReceiveRemoteNotification:fetchCompletionHandler:) 메서드가 호출되어 데이터를 가져올 수 있습니다.
- 제한 사항: 시스템 정책에 따라 백그라운드 실행 여부가 제한될 수 있으며, 지나치게 빈번하게 호출될 경우 iOS가 자동으로 제한할 수 있습니다.

```swift
// AppDelegate.swift

func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable: Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    // 백그라운드에서 데이터를 가져오는 작업
    fetchData { newData in
        if newData {
            completionHandler(.newData)  // 새 데이터가 있음을 알림
        } else {
            completionHandler(.noData)   // 새 데이터가 없음
        }
    }
}
```

- 푸시 알림은 content-available: 1을 포함하도록 서버에서 설정해야 합니다.


<br>

#### 4. Background Processing (iOS 13 이상)
- 개념: iOS 13부터 도입된 기능으로, 장기적인 백그라운드 작업(예: 데이터 동기화, 기계 학습 모델 업데이트 등)을 위한 실행 시간을 예약할 수 있습니다.
- 사용 방법: BGTaskScheduler API를 통해 백그라운드 작업을 등록하고 예약합니다. 백그라운드 작업이 수행될 때 handle(_:completionHandler:)를 통해 작업을 처리합니다.
- 제한 사항: iOS가 배터리와 성능을 고려해 최적화된 시간에 작업을 실행하므로, 정확한 실행 시점을 보장할 수 없습니다.

```swift
// AppDelegate.swift

import BackgroundTasks

func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    BGTaskScheduler.shared.register(forTaskWithIdentifier: "com.example.app.refresh", using: nil) { task in
        self.handleAppRefresh(task: task as! BGAppRefreshTask)
    }
    return true
}

func handleAppRefresh(task: BGAppRefreshTask) {
    // 백그라운드에서 작업 수행
    fetchData { newData in
        task.setTaskCompleted(success: newData)
    }
    
    scheduleAppRefresh()  // 다음 작업 예약
}

func scheduleAppRefresh() {
    let request = BGAppRefreshTaskRequest(identifier: "com.example.app.refresh")
    request.earliestBeginDate = Date(timeIntervalSinceNow: 15 * 60) // 15분 후 실행
    
    do {
        try BGTaskScheduler.shared.submit(request)
    } catch {
        print("Unable to schedule app refresh: \(error)")
    }
}
```

<br>

#### 5. Location Updates (위치 업데이트)
- 개념: 앱이 사용자 위치 정보에 접근해야 할 때, 백그라운드에서도 위치 업데이트를 받을 수 있습니다.
- 사용 방법: allowsBackgroundLocationUpdates를 활성화하고, 위치 권한 설정을 통해 위치 업데이트를 수신합니다. 앱이 백그라운드에서도 계속 위치 업데이트를 받도록 설정할 수 있습니다.
- 제한 사항: 위치 관련 기능을 남용할 경우 배터리 소모가 증가할 수 있으며, 사용자의 위치 권한 설정에 따라 기능이 제한될 수 있습니다.

```swift
import CoreLocation

class LocationManager: NSObject, CLLocationManagerDelegate {
    private let locationManager = CLLocationManager()
    
    override init() {
        super.init()
        locationManager.delegate = self
        locationManager.requestAlwaysAuthorization()  // 항상 위치 권한 요청
        locationManager.allowsBackgroundLocationUpdates = true  // 백그라운드 위치 업데이트 허용
        locationManager.startUpdatingLocation()
    }
    
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        // 위치 업데이트를 처리
        guard let location = locations.last else { return }
        print("Updated location: \(location)")
    }
}
```

- Info.plist에 NSLocationAlwaysUsageDescription 및 NSLocationWhenInUseUsageDescription을 설정하고, UIBackgroundModes에 location 항목을 추가해야 합니다.

<br>

#### 6. Background Audio, VoIP, Bluetooth, etc.
- 개념: 특정 앱 카테고리(오디오 스트리밍, VoIP, Bluetooth 연동 등)에 한해 백그라운드에서 실행이 가능하도록 지원됩니다.
- 사용 방법: UIBackgroundModes 키를 Info.plist에 추가하여 해당 기능을 활성화합니다.
- 제한 사항: 사용 사례가 제한적이며, 시스템에 과부하가 걸릴 경우 iOS가 자동으로 앱을 종료할 수 있습니다.

- 특정 기능을 위한 백그라운드 모드를 설정하는 방법입니다. 오디오 스트리밍 앱을 위한 예시입니다.

```swift
import AVFoundation

func startBackgroundAudio() {
    let session = AVAudioSession.sharedInstance()
    do {
        try session.setCategory(.playback, mode: .default, options: [])
        try session.setActive(true)
        
        // 오디오 재생 코드
        playAudio()
    } catch {
        print("Failed to activate audio session: \(error)")
    }
}

func playAudio() {
    // AVAudioPlayer를 통해 오디오 재생
}
```

- Info.plist에 UIBackgroundModes 키로 audio 항목을 추가해야 합니다.



<br>
<br>

## 3. **Auto Layout을 사용하는 이유와 장점은 무엇인가요?**
Auto Layout은 iOS 앱 개발에서 다양한 화면 크기와 해상도에 맞춰 UI를 유연하게 배치하기 위해 사용하는 레이아웃 시스템입니다. 

<br>

### 1. 다양한 기기 크기에 대응 가능
- iOS 기기는 여러 화면 크기, 해상도, 비율을 가집니다. Auto Layout을 사용하면 이러한 다양한 화면에 적절하게 맞는 UI를 쉽게 만들 수 있습니다.
- 모든 화면에 맞는 레이아웃을 별도로 지정할 필요 없이, 각 기기에 맞춰 UI가 자동으로 조정됩니다.

<br>

### 2. 화면 방향 전환 대응
- Auto Layout을 사용하면 기기가 세로 모드와 가로 모드로 전환될 때 UI 요소가 자동으로 재배치되어 레이아웃이 유지됩니다.
- 예를 들어, 기기가 가로로 전환될 때 특정 요소의 크기나 위치가 조정되는 등의 설정이 가능합니다.

<br>

### 3. 유연한 레이아웃 구성
- Auto Layout은 제약(constraints)을 통해 UI 요소 간의 관계를 정의하여, 위치나 크기를 유연하게 조정할 수 있습니다.
- 각 요소의 크기나 위치가 고정되지 않고 제약 조건에 따라 조정되므로, 다양한 화면 크기에서 일관된 UI를 유지할 수 있습니다.

<br>

### 4. 반응형 디자인 구현
- Auto Layout을 사용하면 특정 조건(예: 화면 크기, 높이, 너비)에 따라 UI가 적절히 반응하게 만들 수 있습니다.
- 예를 들어, 작은 화면에서는 버튼의 크기를 줄이고, 큰 화면에서는 여백을 추가하는 등의 반응형 레이아웃이 가능합니다.

<br>

### 5. 유지 보수 용이성
- Auto Layout은 코드와 스토리보드에서 시각적으로 설정할 수 있어, UI 구조를 이해하고 수정하기가 쉽습니다.
- 제약 조건을 이용하여 코드 내에서 동적으로 UI를 변경할 수 있어, 유지보수가 용이합니다.

<br>

### 6. 다국어 지원에 유리
- Auto Layout은 텍스트 길이가 다를 때에도 UI가 깨지지 않게 유연하게 대응할 수 있습니다.
- 다국어 지원이 필요한 앱에서 각 언어별로 UI를 조정할 필요 없이, 텍스트에 따라 UI가 자연스럽게 조정됩니다.



<br>

### 제약 조건(Constraints)의 우선순위(Priority)는 어떻게 동작하나요?
제약 조건(Constraints)의 우선순위(Priority)는 Auto Layout에서 여러 제약이 충돌할 때 어떤 제약이 우선적으로 적용될지를 결정하는 기준입니다. 우선순위는 1에서 1000 사이의 값으로 설정할 수 있으며, 숫자가 높을수록 우선순위가 높아집니다.

<br>

#### 우선순위(Priority)의 동작 방식
1. 필수 제약 조건(Required Constraints) – 우선순위 1000:
- 기본값으로 설정된 1000은 “필수” 제약 조건을 의미합니다.
- 이 우선순위를 가진 제약 조건은 반드시 만족해야 하며, 그렇지 않으면 레이아웃 오류가 발생합니다.
- 보통 레이아웃의 핵심적인 제약 조건에 사용됩니다.

<br>

#### 2. 선택적 제약 조건(Optional Constraints) – 우선순위 1~999:
- 1000보다 낮은 값의 우선순위는 선택적 제약으로 간주됩니다.
- 이러한 제약 조건은 시스템이 가능한 한 만족하려고 하지만, 필요시 무시될 수 있습니다.
- 예를 들어, 특정 상황에서 제약을 해제하거나 더 유연한 레이아웃을 적용할 때 사용됩니다.

<br>

#### 3. 우선순위 비교를 통한 제약 조건 충돌 해결:
- 여러 제약이 동일한 요소에 대해 충돌하는 경우, 우선순위가 높은 제약이 우선 적용됩니다.
- 예를 들어, 너비가 100pt 이상이어야 하는 제약이 우선순위 750으로 설정되어 있고, 200pt 이상이어야 하는 제약이 우선순위 500으로 설정되어 있다면, 가능한 한 750 우선순위의 제약을 우선적으로 만족시키려고 시도합니다.

<br>

#### 4. 자동 조정:
- 낮은 우선순위 제약은 화면 크기나 내용에 따라 더 유연하게 적용되므로, 디바이스 크기나 화면 방향이 바뀌었을 때 레이아웃이 자동으로 조정됩니다.

<br>

#### 예시 코드
```swift
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false

// 우선순위가 높은 필수 제약 (너비 100pt 이상 필수)
let widthConstraint = view.widthAnchor.constraint(greaterThanOrEqualToConstant: 100)
widthConstraint.priority = .required // 우선순위 1000
widthConstraint.isActive = true

// 우선순위가 낮은 선택적 제약 (너비 200pt 이상 권장)
let preferredWidthConstraint = view.widthAnchor.constraint(greaterThanOrEqualToConstant: 200)
preferredWidthConstraint.priority = UILayoutPriority(750) // 우선순위 750
preferredWidthConstraint.isActive = true
```

- 위 코드에서는 widthConstraint(100pt 이상)과 preferredWidthConstraint(200pt 이상) 두 개의 제약이 설정되어 있습니다.
- **필수 제약 조건(우선순위 1000)** 인 widthConstraint가 우선 적용되어 너비가 최소한 100pt 이상이어야 합니다.
- 그러나, 가능하다면 **선택적 제약 조건(우선순위 750)** 인 preferredWidthConstraint도 만족시켜 너비가 200pt 이상이 되도록 시도합니다.


<br>

### Intrinsic Content Size란 무엇이며, 어떻게 활용되나요?
Intrinsic Content Size는 UI 요소가 그 자체의 콘텐츠를 표시하기 위해 필요한 최소한의 크기를 의미합니다. 즉, UI 요소의 내용에 따라 자동으로 결정되는 크기입니다. 예를 들어, UILabel이나 UIButton 같은 요소들은 내부 텍스트나 이미지의 크기에 따라 고유의 최소 크기를 갖게 됩니다.

<br>

#### Intrinsic Content Size의 활용
[관련 내용](https://babbab2.tistory.com/135)

- 자동 크기 조정: Intrinsic Content Size를 통해 UILabel, UIButton 등 콘텐츠에 따라 크기가 자동으로 결정됩니다. 이 기능을 활용하면, 별도의 너비와 높이 제약 조건을 주지 않아도 요소가 적절한 크기를 갖게 됩니다.
- Auto Layout에서의 활용: Intrinsic Content Size는 Auto Layout에서 중요한 역할을 합니다. Auto Layout은 UI 요소의 고유 크기를 우선적으로 고려하여 레이아웃을 설정합니다. 따라서 Intrinsic Content Size가 있는 요소는 별도의 너비나 높이 제약 조건을 설정하지 않아도, 자동으로 콘텐츠 크기에 맞춰지게 됩니다.
- 우선순위 조정: UILayoutPriority를 활용하여 요소의 Content Hugging Priority와 Compression Resistance Priority를 설정할 수 있습니다. 이를 통해 UI 요소가 다른 요소들과의 레이아웃 충돌 시 콘텐츠 크기에 맞춰 커지거나 압축될지 여부를 조정할 수 있습니다.
    - Content Hugging Priority: 요소가 자신의 Intrinsic Content Size를 유지하려는 우선순위입니다. 이 값이 높을수록 요소가 자신만의 크기를 유지하려고 합니다.
    - Compression Resistance Priority: 요소가 압축되어 크기가 작아지는 것을 방지하려는 우선순위입니다. 이 값이 높을수록 요소가 작아지지 않으려 합니다.
 
```swift
let label = UILabel()
label.text = "Hello, World!"
label.backgroundColor = .lightGray

// Auto Layout을 활성화
label.translatesAutoresizingMaskIntoConstraints = false

// Content Hugging Priority와 Compression Resistance Priority 설정
label.setContentHuggingPriority(.defaultHigh, for: .horizontal)
label.setContentCompressionResistancePriority(.defaultHigh, for: .horizontal)

// 다른 뷰에 제약 조건 추가
view.addSubview(label)
NSLayoutConstraint.activate([
    label.centerXAnchor.constraint(equalTo: view.centerXAnchor),
    label.centerYAnchor.constraint(equalTo: view.centerYAnchor)
])
```

- Content Hugging Priority와 Compression Resistance Priority를 설정합니다.
    - Content Hugging Priority: 요소가 자신의 Intrinsic Content Size(컨텐츠에 맞는 기본 크기)를 유지하려는 우선순위입니다. .defaultHigh로 설정했기 때문에, 다른 제약 조건이 있더라도 가로로 늘어나지 않고 텍스트 크기에 맞게 유지하려고 합니다.
    - Compression Resistance Priority: 요소가 작아지지 않으려는 우선순위입니다. 이 설정으로 인해 레이블의 내용이 잘리지 않도록 크기를 유지하려 합니다.

<br>

#### Intrinsic Content Size를 활용할 때의 이점
1. 레이아웃 코드 간소화: Intrinsic Content Size를 활용하면 명시적으로 너비와 높이를 지정할 필요가 없어, 코드가 간결해집니다.
2. 유연한 레이아웃: 화면 크기나 콘텐츠 양에 따라 UI 요소의 크기가 자동으로 조정되어 유연한 사용자 인터페이스를 제공합니다.
3. 자동 크기 조정: 콘텐츠 양에 따라 자동으로 크기가 조절되므로, 예를 들어 다양한 텍스트 길이를 가진 버튼이나 레이블에 유용하게 사용할 수 있습니다.

<br>

### Ambiguous Layout과 Unsatisfiable Constraints는 무엇이며, 어떻게 해결하나요?
Ambiguous Layout과 Unsatisfiable Constraints는 iOS Auto Layout에서 발생하는 두 가지 일반적인 레이아웃 문제입니다. 각각의 개념과 해결 방법을 살펴보겠습니다.

#### 1. Ambiguous Layout (모호한 레이아웃)
#### 개념 : 
- Ambiguous Layout(모호한 레이아웃)은 Auto Layout이 요소의 위치 또는 크기를 확정할 수 없는 상태를 의미합니다.
- 모호한 레이아웃이 발생하면, 요소가 화면에서 위치를 잡지 못해 원하는 위치에 표시되지 않거나 예기치 않은 위치에 표시될 수 있습니다.

#### 원인 : 
- 위치나 크기에 필요한 제약 조건이 부족할 때 발생합니다.
- 예를 들어, 뷰의 위치는 제약을 통해 설정되어 있지만 가로, 세로 크기를 결정하는 제약이 없는 경우입니다.
- 예시 :

```swift
import UIKit

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let label = UILabel()
        label.text = "Hello, Ambiguous Layout!"
        label.backgroundColor = .lightGray
        label.translatesAutoresizingMaskIntoConstraints = false
        
        // label을 view에 추가
        view.addSubview(label)
        
        // label의 위치는 설정하지만, 너비와 높이를 설정하지 않음
        NSLayoutConstraint.activate([
            label.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            label.topAnchor.constraint(equalTo: view.topAnchor, constant: 100)
        ])
    }
}
```

<br>

#### 해결 방법:
- 필요한 제약 추가: 모든 UI 요소가 위치와 크기를 결정할 수 있도록 충분한 제약 조건을 추가합니다.
- Debugging Options: Xcode의 “Debug View Hierarchy”를 사용해 Ambiguous Layout을 확인하고, 모호한 제약이 발생한 요소를 찾습니다.
- Runtime Check: 개발 중 UIView의 hasAmbiguousLayout() 메서드를 사용하여 특정 뷰의 레이아웃이 모호한지 확인할 수 있습니다. exerciseAmbiguityInLayout()을 사용해 모호한 레이아웃의 다양한 배치 결과를 테스트해 볼 수도 있습니다.

```swift
NSLayoutConstraint.activate([
    label.centerXAnchor.constraint(equalTo: view.centerXAnchor),
    label.topAnchor.constraint(equalTo: view.topAnchor, constant: 100),
    
    // 너비와 높이를 명시적으로 설정하여 모호성을 제거
    label.widthAnchor.constraint(equalToConstant: 200),
    label.heightAnchor.constraint(equalToConstant: 50)
])
```

<br>

#### 2. Unsatisfiable Constraints (충족 불가능한 제약 조건)
#### 개념 : 
- Unsatisfiable Constraints(충족 불가능한 제약 조건)는 두 개 이상의 상충되는 제약 조건으로 인해 레이아웃을 만족시킬 수 없는 상태를 의미합니다.
- 시스템이 제약 조건을 충족시키지 못하는 경우, 충돌하는 제약 조건을 비활성화하거나 무시하려고 시도합니다. 그러나 여전히 문제가 해결되지 않으면 콘솔에 오류가 출력됩니다.

#### 원인 :
- 서로 충돌하는 제약 조건이 설정된 경우, 예를 들어 뷰의 가로 너비를 100pt 이상으로 유지하는 제약과 50pt로 제한하는 제약이 동시에 존재하는 경우입니다.
- 특정 제약 조건이 우선순위(Priority) 설정 문제로 충돌을 일으킬 때 발생할 수도 있습니다.

#### 해결 방법:
- Xcode 콘솔 오류 메시지 확인: Unsatisfiable Constraints가 발생하면 Xcode 콘솔에 관련 오류 메시지가 나타납니다. 이 메시지에는 충돌하는 제약 조건과 뷰의 정보가 표시되므로, 이를 확인하고 문제를 해결할 수 있습니다.
- 제약 조건 우선순위 조정: 서로 상충되는 제약이 있을 경우, 필요에 따라 우선순위를 조정합니다.
- 불필요한 제약 조건 제거: 충돌하는 제약 조건을 삭제하거나 우선순위를 낮춰서 해결할 수 있습니다.
- 코드 예시 :

```swift
// 예시: 충돌하는 제약을 제거하거나 우선순위를 조정
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false

let widthConstraint = view.widthAnchor.constraint(equalToConstant: 100)
widthConstraint.priority = UILayoutPriority(999) // 우선순위를 낮춰 충돌 해결
widthConstraint.isActive = true

let conflictingConstraint = view.widthAnchor.constraint(equalToConstant: 50)
conflictingConstraint.isActive = true // 이 제약 조건이 우선 적용됨
```

<br>

#### 요약
- Ambiguous Layout: 위치와 크기를 결정하기에 충분한 제약 조건이 부족할 때 발생하며, 필요한 제약을 추가하여 해결합니다.
- Unsatisfiable Constraints: 충돌하는 제약 조건이 있을 때 발생하며, 불필요한 제약을 삭제하거나 우선순위를 조정하여 해결합니다.

<br>

### 스토리보드 vs XIB 요약

<img src="https://github.com/user-attachments/assets/b00023b7-471c-4f81-a18e-558c84e5f2cc">

<br>
<br>

## 4. **Swift에서 클로저(Closure)란 무엇이며, 어떻게 사용하나요?**
**클로저(Closure)** 는 코드에서 전달하고 사용할 수 있는 독립적인 코드 블록입니다. Swift에서 클로저는 변수나 상수로 저장하고, 다른 함수에 인자로 전달하거나 반환 값으로 사용할 수 있는 기능을 제공합니다. 클로저는 기본적으로 익명 함수(이름이 없는 함수)로, 코드에서 즉시 사용해야 하거나 나중에 호출할 코드를 캡처하고 저장할 때 유용합니다.

<br>

### 클로저의 특징
1. 함수의 참조 방식과 유사: 클로저는 코드 블록을 참조하고 실행할 수 있다는 점에서 함수와 유사합니다.
2. 변수와 상수를 캡처: 클로저는 주변의 변수와 상수를 캡처하여 저장할 수 있습니다.
3. 간결한 문법: 클로저는 축약된 문법을 제공하며, 특히 Swift의 map, filter, reduce 같은 함수와 함께 사용될 때 유용합니다.

<br>

### 클로저의 기본 형태
클로저는 { (매개변수) -> 반환 타입 in 코드 } 형태를 가지며, in 키워드를 기준으로 매개변수 및 반환 타입 선언과 코드 블록이 나뉩니다.

```swift
{ (parameters) -> ReturnType in
    // 클로저 코드
}
```

#### 예제 : 기본 클로저 구문

```swift
let greetingClosure = { (name: String) -> String in
    return "Hello, \(name)!"
}

// 클로저 호출
let result = greetingClosure("Alice")
print(result)  // 출력: "Hello, Alice!"
```

<br>

#### 클로저를 함수의 인자로 사용하기

클로저는 자주 다른 함수의 인자로 사용됩니다. 예를 들어, sort 메서드는 정렬 방식을 결정하기 위해 클로저를 인자로 받습니다.

```swift
let numbers = [3, 1, 4, 1, 5, 9]
let sortedNumbers = numbers.sorted(by: { (a: Int, b: Int) -> Bool in
    return a < b
})

print(sortedNumbers)  // 출력: [1, 1, 3, 4, 5, 9]
```

<br>

#### 클로저 축약 문법

Swift는 클로저를 작성할 때 불필요한 구문을 줄이는 몇 가지 축약 문법을 제공합니다.

1. 타입 추론: 클로저의 매개변수와 반환 타입을 추론할 수 있는 경우, 명시적으로 작성하지 않아도 됩니다.
2. 단축 인수 이름: $0, $1 등의 이름을 사용하여 매개변수를 표현할 수 있습니다.
3. 후행 클로저(Trailing Closure): 함수의 마지막 인자로 클로저가 오는 경우, 클로저를 함수 호출 소괄호 외부에 작성할 수 있습니다.

#### 축약 예제

```swift
let numbers = [3, 1, 4, 1, 5, 9]

// 후행 클로저 구문과 단축 인수 이름을 활용한 예제
let sortedNumbers = numbers.sorted { $0 < $1 }

print(sortedNumbers)  // 출력: [1, 1, 3, 4, 5, 9]
```

<br>

#### 클로저 사용 시 주의사항
- 강한 참조 순환: 클로저가 캡처한 객체를 강한 참조로 유지할 경우, 강한 참조 순환이 발생할 수 있습니다. 이를 방지하기 위해 [weak self] 또는 [unowned self] 키워드를 사용해 약한 참조로 캡처할 수 있습니다.

```swift
class Example {
    var value = 0
    lazy var increment: () -> Void = { [weak self] in
        self?.value += 1
    }
}
```

<br>

### 요약 
- 클로저는 독립적인 코드 블록으로, 변수나 상수처럼 저장하고 전달할 수 있습니다.
- Swift에서는 클로저를 활용해 간결하고 유연한 코드 작성을 할 수 있습니다.
- 클로저는 캡처 기능을 제공하며, 강한 참조 순환 문제에 주의해야 합니다.


<br>


### 클로저의 캡처(Capture) 기능은 무엇인가요?
- 클로저의 캡처 기능은 클로저가 생성된 시점의 주변 변수나 상수를 클로저 내부에서 사용할 수 있도록 저장하는 기능입니다.
- 즉, 클로저는 자신이 선언된 환경(context) 내의 변수와 상수를 참조하거나 저장하여, 클로저가 나중에 호출되더라도 해당 변수나 상수를 사용할 수 있습니다.


<br>

#### 캡처 기능의 동작 방식
클로저가 변수나 상수를 캡처할 때, 해당 변수나 상수는 값이 아닌 참조로 저장됩니다. 따라서 클로저 내부에서 이 변수를 수정하면, 클로저가 선언된 외부의 변수에도 영향을 미칩니다.

#### 캡처 기능 예제
```swift
func makeIncrementer(incrementAmount: Int) -> () -> Int {
    var total = 0  // 외부 변수 total
    let incrementer: () -> Int = {
        total += incrementAmount
        return total
    }
    return incrementer
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)

print(incrementByTwo()) // 2
print(incrementByTwo()) // 4
print(incrementByTwo()) // 6
```

#### 예제 설명
1. makeIncrementer 함수 내부에는 total이라는 변수가 있습니다.
2. incrementer라는 클로저를 정의할 때, total 변수를 캡처합니다.
3. 이 클로저를 반환하여 외부에서 incrementByTwo()를 호출하면, 클로저 내부의 total 값이 증가합니다.
4. 클로저는 total을 참조로 캡처하고 있어, 호출할 때마다 total의 값이 업데이트됩니다.

#### 추가 설명
이 코드에서 { total += incrementAmount; return total } 부분은 클로저이며, 클로저가 total 변수를 캡처하기 때문에 total은 힙 메모리에 저장되어 클로저가 호출될 때마다 사용될 수 있게 됩니다.

#### 요약하자면:
- 클로저는 생성될 때 자신이 참조하는 외부 변수(여기서는 total)를 캡처하여 힙 메모리에 저장합니다.
- 이로 인해 makeIncrementer 함수가 종료된 후에도, total은 클로저의 캡처된 환경으로 힙 메모리에 남아 있게 됩니다.
- 클로저가 살아 있는 동안 total은 힙 메모리에 유지되며, 클로저가 모든 참조에서 벗어나야 ARC에 의해 메모리에서 해제됩니다.


<br>

#### 클로저 캡처의 주요 용도
- 상태 유지: 캡처된 변수의 값을 유지하고 클로저 호출 시 이를 변경함으로써, 클로저가 일정 상태를 관리하도록 할 수 있습니다.
- 비동기 작업: 비동기 작업에서, 클로저가 실행될 때 필요한 변수나 상수를 클로저가 선언된 시점에 캡처하여, 나중에 클로저가 호출될 때도 해당 값에 접근할 수 있습니다.

<br>

#### 클로저 캡처와 메모리 관리
클로저가 참조 타입(예: 클래스 인스턴스)을 캡처할 때 **강한 참조 순환(Retain Cycle)** 이 발생할 수 있습니다. 강한 참조 순환이 발생하면 메모리에서 해제되지 않는 문제가 생기므로, 이를 방지하기 위해 약한 참조(weak)나 비소유 참조(unowned)를 사용해야 합니다.

```swift
class Example {
    var value = 0
    lazy var increment: () -> Void = { [weak self] in
        self?.value += 1
    }
}
```

- 위 코드에서 [weak self]를 사용해 self를 약한 참조로 캡처하여 강한 참조 순환을 방지합니다.


<br>

### @escaping 클로저와 non-escaping 클로저의 차이점은 무엇인가요?
@escaping 클로저와 non-escaping 클로저는 클로저의 생명주기와 메모리 관리 방식에 따라 나뉘며, 두 가지는 함수가 클로저를 어떻게 다루는지에 큰 차이가 있습니다.

#### 1. @escaping 클로저
@escaping 클로저는 **함수의 실행이 종료된 후에도 클로저가 저장** 되어 **나중에 호출** 될 수 있는 클로저입니다. 주로 비동기 작업에서 사용되며, 함수 외부로 클로저를 “탈출”시킨다는 의미에서 **“escaping”** 이라는 이름이 붙습니다.

#### 특징
- 함수 외부로 탈출 가능: @escaping 클로저는 함수가 종료된 후 나중에 호출될 수 있으며, 비동기 작업에서 자주 사용됩니다.
- 캡처한 변수 참조를 힙에 저장: 클로저가 탈출하는 경우 해당 클로저가 캡처한 변수는 힙 메모리에 저장되어 함수 종료 후에도 참조됩니다.
- self 캡처 명시 필요: 클래스 인스턴스의 메서드에서 @escaping 클로저를 사용할 경우 self 캡처를 명시해야 합니다. 이로 인해 참조 순환 문제가 발생할 수 있어 [weak self]나 [unowned self]를 사용하는 것이 좋습니다.

#### 예제 : @excaping 클로저
```swift
func performAsyncTask(completion: @escaping () -> Void) {
    DispatchQueue.global().async {
        // 비동기 작업 수행
        print("Async task started")
        completion() // 함수 외부에서 클로저 호출
    }
}

performAsyncTask {
    print("Async task completed")
}
```

#### 사용 예시: 비동기 작업에서의 @escaping 클로저
비동기 작업에서는 클로저가 나중에 호출될 수 있으므로 탈출 클로저로 선언해야 합니다.

```swift
func performAsyncTask(completion: @escaping () -> Void) {
    DispatchQueue.global().async {
        // 시간이 걸리는 작업 수행
        print("비동기 작업 시작")
        sleep(2)  // 2초 지연
        
        // 작업 완료 후 클로저 호출
        DispatchQueue.main.async {
            completion()
        }
    }
}

// 사용
performAsyncTask {
    print("비동기 작업이 완료되었습니다.")
}
```

- 위 코드에서는 performAsyncTask 함수가 종료된 후 completion 클로저가 비동기 작업이 완료된 시점에 호출됩니다. 이 때문에 @escaping 키워드가 필요합니다.


#### 또 다른 예시: 클로저를 저장해 두고 나중에 사용해야 할 때

```swift
class TaskManager {
    var completionHandlers: [() -> Void] = []

    func addCompletionHandler(handler: @escaping () -> Void) {
        completionHandlers.append(handler) // 클로저를 배열에 저장
    }

    func executeCompletionHandlers() {
        for handler in completionHandlers {
            handler()
        }
    }
}

// 사용
let manager = TaskManager()
manager.addCompletionHandler {
    print("작업이 완료되었습니다 1")
}

manager.addCompletionHandler {
    print("작업이 완료되었습니다 2")
}

manager.executeCompletionHandlers() // 나중에 저장된 클로저를 실행
// 작업이 완료되었습니다 1
// 작업이 완료되었습니다 2
```

- 이 예시에서 addCompletionHandler 메서드는 클로저를 배열에 저장합니다.
- 함수가 종료된 후에도 클로저가 저장되어 나중에 호출될 수 있으므로, @escaping 키워드를 사용해야 합니다.


<br>

#### 2. Non-escaping 클로저
Non-escaping 클로저는 함수 내부에서만 실행되고, 함수가 종료되면 사라지는 클로저입니다. 함수의 파라미터로 전달된 후 함수 내부에서 실행되며, 탈출하지 않으므로 기본적으로 클로저는 non-escaping입니다. 즉, @escaping 키워드를 명시하지 않는 한 클로저는 non-escaping으로 간주됩니다.

#### 특징
- 함수 내부에서만 실행 : non-escaping 클로저는 함수 내부에서만 호출되며, 함수가 종료되면 클로저도 사라집니다.
- 힙 메모리에 저장되지 않음 : 함수 내부에서만 사용되므로 캡처한 변수는 스택 메모리에 저장되며, 함수가 종료되면 자동으로 해제됩니다.
- self 캡처 불필요 : 함수 내부에서만 실행되므로, 클래스 인스턴스의 프로퍼티에 접근할 때도 self를 명시하지 않아도 됩니다.

#### 예제
```swift
func performTask(completion: () -> Void) {
    print("Task started")
    completion() // 함수 내부에서 클로저 호출
    print("Task completed")
}

performTask {
    print("Task in progress")
}
```

#### 사용 예시: map 메서드와 같은 동기 작업
논 탈출 클로저는 주로 동기 작업에서 사용됩니다. 예를 들어 배열의 각 요소를 변환하는 map 메서드는 함수 호출이 종료되기 전에 클로저를 호출하고 완료합니다.

```swift
let numbers = [1, 2, 3, 4, 5]

// map 함수는 클로저를 논 탈출 클로저로 사용
let doubled = numbers.map { (number) in
    return number * 2
}

print(doubled) // 출력: [2, 4, 6, 8, 10]
```

<br>

### 주요 차이점 요약
<img src="https://github.com/user-attachments/assets/10f042e2-159a-4ace-97ab-8e0c98eca64c">

<br>

### 트레일링 클로저(Trailing Closure) 문법은 어떤 경우에 유용한가요?
트레일링 클로저(Trailing Closure) 문법은 함수의 마지막 매개변수로 클로저를 전달할 때, 소괄호 바깥에 클로저를 작성하여 코드 가독성을 높이는 문법입니다.

#### 트레일링 클로저 문법이 유용한 경우
1. 클로저가 길어질 때:
- 클로저를 인자로 전달하는 함수 호출 시, 함수의 소괄호 내에 클로저를 작성하면 코드가 복잡해지고 가독성이 떨어질 수 있습니다.
- 트레일링 클로저를 사용하면 소괄호 바깥에서 클로저를 작성하므로 함수 호출 구조가 깔끔해집니다.
2. Swift 표준 라이브러리 함수 사용 시:
- Swift의 map, filter, reduce와 같은 고차 함수에서 트레일링 클로저를 자주 사용합니다.
- 이 함수들은 코드 가독성을 위해 클로저를 마지막 인자로 받도록 설계되어 있어, 클로저를 소괄호 바깥에 작성하기에 적합합니다.
3. 비동기 작업에서 콜백 클로저 사용 시:
- 네트워크 요청, 애니메이션 처리 등 비동기 작업의 결과를 처리할 때 콜백 클로저를 사용하는 경우, 트레일링 클로저 문법을 사용하면 코드가 더 직관적입니다.

#### 예제 코드
```swift
// 일반 클로저 문법
let numbers = [1, 2, 3, 4, 5]
let doubledNumbers = numbers.map({ (number) in
    return number * 2
})

// 트레일링 클로저 문법
let doubledNumbersTrailing = numbers.map { number in
    return number * 2
}
```

- 위 코드에서 map 함수의 인자로 클로저가 전달되며, 트레일링 클로저 문법을 사용하면 함수 호출의 괄호를 생략하여 코드가 더 깔끔하게 보입니다.

#### 비동기 작업에서의 트레일링 클로저 사용
```swift
func fetchData(completion: () -> Void) {
    // 네트워크 요청 코드
    completion()
}

// 일반 클로저 사용
fetchData(completion: {
    print("Data fetched!")
})

// 트레일링 클로저 사용
fetchData {
    print("Data fetched!")
}
```

- 비동기 작업의 콜백 함수로 클로저를 사용할 때도 트레일링 클로저 문법이 유용합니다. completion이 함수의 마지막 인자이므로, 소괄호를 생략하고 클로저를 함수 호출 뒤에 바로 작성할 수 있습니다.

<br>

### 요약
요약
- 코드 가독성을 높이기 위해, 함수의 마지막 매개변수가 클로저일 때 트레일링 클로저 문법을 사용하면 좋습니다.
- 주로 Swift의 고차 함수(map, filter, reduce)나 비동기 작업의 콜백 클로저에서 많이 사용됩니다.
- 트레일링 클로저를 활용하면 코드가 간결해지고, 클로저의 구조가 더 명확하게 드러나게 됩니

<br>
<br>

## 5. **iOS에서 Delegate 패턴은 무엇이며, 어떤 상황에서 사용되나요?**
- Delegate 패턴은 객체 간의 커뮤니케이션을 위해 한 객체가 자신의 일부 기능을 다른 객체에 위임하는 디자인 패턴입니다.
- iOS 개발에서는 두 객체 간의 상호작용을 캡슐화하고 코드의 결합도를 낮추기 위해 자주 사용됩니다.

### Delegate 패턴의 동작 원리
1. 프로토콜 정의: 특정 작업을 수행하기 위한 메서드들이 포함된 **프로토콜(Interface)** 을 정의합니다.
2. Delegate 프로퍼티: 클래스 A는 Delegate 프로퍼티를 선언하고, 이를 통해 자신이 필요한 작업을 다른 객체에게 위임합니다.
3. 프로토콜 채택: 클래스 B는 클래스 A가 제공한 프로토콜을 채택하고, 필요한 메서드를 구현하여 A의 작업을 대신 수행할 수 있습니다.
4. 위임 객체 등록: 클래스 A는 자신의 Delegate 프로퍼티에 클래스 B를 할당하여, A에서 발생하는 작업을 B에게 전달합니다.

### Delegate 패턴이 유용한 상황
1. UIKit에서 이벤트를 전달할 때:
- 예를 들어, UITableView와 UICollectionView는 데이터 소스와 사용자 상호작용을 처리하기 위해 UITableViewDelegate 및 UITableViewDataSource 프로토콜을 사용합니다.
- 이를 통해 각 셀을 선택하거나 스크롤 이벤트와 같은 작업을 뷰 컨트롤러에서 처리할 수 있습니다.
2. 비동기 작업의 완료를 알릴 때:
- 네트워크 요청, 다운로드 완료, 비동기 작업 등이 끝났을 때 결과를 Delegate 패턴으로 호출자에게 전달할 수 있습니다.
3. View와 Controller 간의 데이터 전달 및 이벤트 처리:
- 커스텀 뷰가 특정 작업을 수행할 때 이를 알리기 위해 Delegate 패턴을 사용하여 뷰 컨트롤러에 이벤트를 전달할 수 있습니다.

<br>
<br>

## 프로토콜을 활용한 Delegate 패턴 구현 방법을 설명해주세요.
### Delegate 패턴 예제
예제: ViewController에서 버튼이 눌린 상태를 알리기 위해 커스텀 MyButtonView에서 Delegate 패턴을 사용

```swift
import UIKit

// 1. Delegate Protocol 정의
protocol MyButtonViewDelegate: AnyObject {
    func didTapButton()
}

class MyButtonView: UIView {
    // 2. Delegate 프로퍼티 선언
    weak var delegate: MyButtonViewDelegate?

    private let button: UIButton = {
        let button = UIButton(type: .system)
        button.setTitle("Tap me!", for: .normal)
        return button
    }()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupButton()
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        setupButton()
    }
    
    private func setupButton() {
        addSubview(button)
        button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
    }
    
    @objc private func buttonTapped() {
        // 3. Delegate 메서드 호출
        delegate?.didTapButton()
    }
}

// 4. ViewController에서 Delegate 프로토콜 채택 및 구현
class ViewController: UIViewController, MyButtonViewDelegate {

    override func viewDidLoad() {
        super.viewDidLoad()

        let myButtonView = MyButtonView(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        myButtonView.delegate = self // Delegate 프로퍼티 설정
        view.addSubview(myButtonView)
    }

    // Delegate 메서드 구현
    func didTapButton() {
        print("Button was tapped in MyButtonView")
    }
}
```

#### 예제 설명
1. MyButtonViewDelegate 프로토콜을 정의하고 버튼이 탭될 때 호출되는 didTapButton() 메서드를 선언합니다.
2. MyButtonView는 delegate 프로퍼티를 선언하여 버튼이 눌리면 이를 delegate에 알립니다.
3. ViewController는 MyButtonViewDelegate를 채택하고, didTapButton() 메서드를 구현하여 버튼 탭 이벤트를 처리합니다.
4. ViewController는 MyButtonView의 delegate로 설정되어, 버튼 탭 이벤트가 발생하면 ViewController의 didTapButton 메서드가 호출됩니다.

<br>

### Delegate 패턴의 장점
- 재사용성: 클래스가 특정 작업을 외부 객체에 위임하여 코드의 재사용성을 높입니다.
- 결합도 감소: 서로 다른 클래스가 서로에 대해 알지 못하면서도 작업을 전달하고 처리할 수 있어 코드가 유연해집니다.
- 캡슐화: 클래스의 내부 구현을 숨기고 인터페이스를 통해 상호작용할 수 있도록 합니다.

### Delegate 패턴 요약
Delegate 패턴은 이벤트 처리와 데이터 전달을 객체 간에 효율적으로 위임할 수 있는 중요한 패턴입니다. iOS 개발에서 Delegate 패턴은 다양한 UI 컴포넌트와 상호작용할 때 유용하며, 코드의 결합도를 낮추고 캡슐화를 유지하면서 데이터와 이벤트를 전달할 수 있게 해줍니다.

<br>

> 캡슐화 : 객체지향 프로그래밍의 중요한 개념 중 하나로, 객체의 데이터(프로퍼티)와 기능(메서드)을 외부에서 직접 접근할 수 없도록 숨기고, 필요한 경우에만 접근할 수 있는 인터페이스(메서드)를 제공하는 것을 말합니다. 이를 통해 데이터 보호와 코드의 독립성을 유지할 수 있습니다.

#### 캡슐화 예시
#### 예제: BankAccount 클래스에서 캡슐화 구현
```swift
class BankAccount {
    // private 접근 수준으로 설정하여 외부에서 balance에 접근하지 못하도록 함
    private var balance: Double = 0.0
    
    // balance에 접근할 수 있는 메서드를 제공하여 읽기만 가능하게 함
    func getBalance() -> Double {
        return balance
    }
    
    // 입금 메서드 - balance 값을 안전하게 변경할 수 있는 메서드 제공
    func deposit(amount: Double) {
        guard amount > 0 else {
            print("Invalid amount")
            return
        }
        balance += amount
    }
    
    // 출금 메서드 - 출금 시, 잔액이 충분한지 확인 후 출금
    func withdraw(amount: Double) {
        guard amount > 0 && amount <= balance else {
            print("Insufficient balance or invalid amount")
            return
        }
        balance -= amount
    }
}
```
#### 예제 설명
- balance 프로퍼티는 private 접근 제어자를 통해 외부에서 직접 접근할 수 없습니다.
- getBalance() 메서드는 balance 값을 외부에서 읽을 수 있도록 제공하지만, 수정은 불가능합니다.
- deposit(amount:)와 withdraw(amount:) 메서드를 통해서만 balance를 변경할 수 있으며, 이를 통해 무결성을 유지하고 오류가 발생하지 않도록 합니다.

#### 사용 예
```swift
let myAccount = BankAccount()
myAccount.deposit(amount: 1000)
print(myAccount.getBalance())  // 출력: 1000.0

myAccount.withdraw(amount: 500)
print(myAccount.getBalance())  // 출력: 500.0

// 외부에서 balance를 직접 접근하려 하면 오류 발생
// myAccount.balance = 2000  // 오류: 'balance' is inaccessible due to 'private' protection level
```
#### 캡슐화의 장점
1. 데이터 보호: 외부에서 직접 데이터를 수정하지 못하도록 하여, 잘못된 접근을 방지합니다.
2. 유지보수성 향상: 객체의 내부 구현을 숨기고 인터페이스만 노출하므로, 내부 로직을 수정하더라도 외부 코드를 수정할 필요가 없습니다.
3. 코드 독립성: 객체가 데이터와 기능을 자체적으로 관리하고 보호하므로, 코드의 결합도를 낮추고 독립성을 유지할 수 있습니다.

#### iOS에서의 캡슐화 활용 예
UIKit의 UITextField는 text 속성에 접근할 수 있는 getter와 setter 메서드를 제공하여, 특정 상태에서만 사용자 입력을 받을 수 있도록 제어할 수 있습니다. 앱이 복잡해지면, 이러한 캡슐화 방식을 통해 내부 데이터를 보호하고 외부 코드와 독립적인 구조를 유지하는 것이 중요해집니다.

<br>

## Delegate 패턴과 Notification, KVO의 차이점은 무엇인가요?
Delegate 패턴, Notification, **KVO (Key-Value Observing)** 는 객체 간의 상호작용을 위한 패턴이지만, 각 방식에는 목적과 사용 방식에 차이가 있습니다.

### 1. Delegate 패턴
Delegate 패턴은 객체가 특정 작업을 다른 객체에 위임하여 1:1 관계로 상호작용을 정의하는 패턴입니다.

#### 특징:
- 1:1 관계: Delegate 패턴은 일반적으로 두 객체 간의 상호작용을 관리합니다.
- 구현 방식: 프로토콜을 정의하고, 해당 프로토콜을 채택한 객체가 특정 작업을 수행하도록 구현합니다.
- 직접 통신: 이벤트가 발생할 때 직접 메서드를 호출하여 처리합니다.
#### 사용 예:
- UITableViewDelegate나 UITextFieldDelegate와 같은 UIKit의 Delegate 패턴은 UI 컴포넌트의 특정 동작을 뷰 컨트롤러에 위임합니다.
#### 장점:
- 코드가 직관적이고 타입 안전성을 제공합니다.
- 서로의 상태나 데이터를 직접 주고받을 수 있어 강력하고 유연한 상호작용이 가능합니다.
#### 단점:
- 1:1 관계로, 한 객체가 하나의 Delegate만 가질 수 있어 여러 객체가 동일한 작업을 수신하는 것이 어렵습니다.

#### Delegate 패턴 예시

```swift
protocol MyDelegate: AnyObject {
    func didFinishTask()
}

class Task {
    weak var delegate: MyDelegate?

    func startTask() {
        // 작업 수행
        delegate?.didFinishTask() // 작업 완료 후 delegate 메서드 호출
    }
}

class ViewController: UIViewController, MyDelegate {
    func didFinishTask() {
        print("Task completed!")
    }
}
```

<br>

### 2. Notification

Notification은 특정 이벤트가 발생했을 때 해당 이벤트를 여러 객체에게 광범위하게 전달할 수 있는 패턴입니다.

#### 특징:
- 1:다 관계: Notification은 하나의 이벤트를 여러 객체가 수신할 수 있습니다.
- 구현 방식: Notification Center를 통해 특정 이벤트를 등록하고, 해당 이벤트가 발생할 때 알림을 받습니다.
- 비동기적: 이벤트가 비동기적으로 전달되어 메서드가 즉시 호출되지 않을 수 있습니다.
#### 사용 예:
- 앱이 백그라운드로 들어가거나 포그라운드로 돌아올 때 이를 수신하기 위해 UIApplication.didEnterBackgroundNotification 등을 사용합니다.
#### 장점:
- 여러 객체가 동일한 이벤트를 수신할 수 있어 이벤트에 대한 광범위한 반응이 가능합니다.
- 이벤트 발생 시, 수신 객체와 발신 객체가 서로를 알 필요가 없으므로 결합도가 낮습니다.
#### 단점:
- 특정 이벤트와 관련된 모든 객체에 알림이 전달되므로 사용 시 주의가 필요합니다.
- 코드가 흩어져 있어 유지보수가 어려울 수 있습니다.

#### Notification 예시

```swift
class Task {
    func startTask() {
        // 작업 수행
        NotificationCenter.default.post(name: .didFinishTask, object: nil) // Notification 전송
    }
}

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        NotificationCenter.default.addObserver(self, selector: #selector(taskCompleted), name: .didFinishTask, object: nil)
    }

    @objc func taskCompleted() {
        print("Task completed via Notification!")
    }
}

extension Notification.Name {
    static let didFinishTask = Notification.Name("didFinishTask")
}
```

<br>
### 3. KVO (Key-Value Observing)
KVO는 특정 객체의 프로퍼티가 변경될 때 자동으로 감지하여 알림을 받는 패턴입니다.

#### 특징:
- 1:다 관계: KVO는 하나의 프로퍼티 변경을 여러 객체가 감지할 수 있습니다.
- 구현 방식: 특정 키 경로에 대해 관찰을 등록하고, 해당 키의 값이 변경되면 알림을 받습니다.
- 자동 감지: 프로퍼티의 변경을 자동으로 감지하고 알림을 보내기 때문에 특정 메서드를 직접 호출할 필요가 없습니다.
#### 사용 예:
- 데이터가 변경될 때 이를 UI에 반영하는 상황에서 사용되며, @objc 속성과 함께 주로 사용됩니다.
- Core Data와 같은 데이터 관리에서 사용됩니다.
#### 장점:
- 프로퍼티가 변경될 때마다 자동으로 감지할 수 있어 편리합니다.
- 여러 객체가 동일한 프로퍼티 변경을 수신할 수 있습니다.
#### 단점:
- 오류 발생 가능성: 설정이 복잡할 수 있고, 관찰이 해제되지 않으면 메모리 누수가 발생할 수 있습니다.
- 유지보수의 어려움: 변경 사항이 많아질 경우 코드를 추적하고 디버깅하기 어렵습니다.

#### KVO 예시
```swift
class Task: NSObject {
    @objc dynamic var progress: Double = 0.0
}

class ViewController: UIViewController {
    var task = Task()
    var observation: NSKeyValueObservation?

    override func viewDidLoad() {
        super.viewDidLoad()
        observation = task.observe(\.progress, options: [.new]) { (task, change) in
            print("Task progress: \(change.newValue ?? 0)")
        }
        task.progress = 0.5  // progress가 변경되면 자동으로 KVO 알림이 발생
    }
}
```

<br>

### 요약 비교
<img src="https://github.com/user-attachments/assets/8aafb5f9-1556-4846-b3e3-03e64b6b1ff5">

- Delegate는 두 객체 간의 직접적인 상호작용이 필요한 경우에 적합하고, Notification과 KVO는 객체가 직접 연결될 필요 없이 변경 사항을 여러 곳에 알릴 때 유용합니다.
- Notification과 KVO는 결합도가 낮아 코드가 유연하지만, 변경 사항을 감지하기 어렵고 추적이 복잡해질 수 있습니다.

<br>
<br>

## 6. **Swift의 기본 데이터 타입과 컬렉션(Collection) 타입에는 어떤 것들이 있나요?**

### 기본 데이터 타입 (Basic Data Types)
#### 1. Int: 정수를 나타내는 타입입니다. Int는 플랫폼에 따라 32비트 또는 64비트로 작동합니다.

```swift
let age: Int = 25
```
<br>

#### 2.	UInt: 양의 정수를 나타내는 타입입니다. 부호가 없는 32비트 또는 64비트 정수입니다.
```swift
let count: UInt = 100
```

<br>


#### 3.	Float: 32비트 부동 소수점 숫자를 표현하는 타입입니다.
```swift
let height: Float = 5.9
```

<br>


#### 4.	Double: 64비트 부동 소수점 숫자를 표현하는 타입입니다. Float보다 더 높은 정밀도를 가집니다.
```swift
let weight: Double = 70.5
```

<br>


#### 5.	Bool: 논리값을 표현하는 타입으로, true 또는 false 값만 가질 수 있습니다.
```swift
let isStudent: Bool = true
```

<br>

#### 6.	Character: 하나의 문자를 표현하는 타입입니다.
```swift
let grade: Character = "A"
```

<br>

#### 7.	String: 문자열을 표현하는 타입으로, 여러 문자의 모음입니다.
```swift
let name: String = "John Doe"
```

<br>

#### 8.	Optional: 값이 있을 수도 있고 없을 수도 있는 타입입니다. 값이 없을 경우 nil이 할당됩니다.
```swift
var age: Int? = nil  // Optional Int 타입
```

<br>

### 컬렉션 타입 (Collection Types)
#### 1.	Array: 동일한 타입의 값들을 순서대로 저장하는 타입입니다. 배열은 인덱스로 접근하며, 요소가 중복될 수 있습니다.

```swift
var numbers: [Int] = [1, 2, 3, 4, 5]
numbers.append(6)
```

<br>


#### 2.	Set: 고유한 값들을 순서 없이 저장하는 타입입니다. 요소가 중복될 수 없으며, 순서가 중요하지 않은 경우에 사용됩니다.

```swift
var uniqueNumbers: Set<Int> = [1, 2, 3, 4, 5]
uniqueNumbers.insert(6)
```

<br>


#### 3.	Dictionary: 키-값 쌍을 저장하는 타입입니다. 각 키는 고유해야 하며, 키를 통해 값에 접근할 수 있습니다.

```swift
var studentGrades: [String: Int] = ["Alice": 85, "Bob": 92]
studentGrades["Charlie"] = 88
```

<br>


#### 4.	Tuple: 서로 다른 타입의 값을 그룹으로 묶는 타입입니다. 고정된 크기를 가지며, 값의 순서와 타입이 정해져 있습니다.

```swift
let person: (name: String, age: Int) = ("Alice", 25)
print(person.name)  // "Alice"
```

<br>

### 요약
- 기본 데이터 타입: Int, UInt, Float, Double, Bool, Character, String, Optional.
- 컬렉션 타입: Array, Set, Dictionary, Tuple.


<br>
<br>

## 값 타입(Value Type)과 참조 타입(Reference Type)의 차이점은 무엇인가요?
**값 타입(Value Type)** 과 **참조 타입(Reference Type)** 의 차이는 객체가 할당되고 복사되는 방식에 있습니다. Swift에서는 구조체(struct), 열거형(enum), 튜플(tuple) 등이 값 타입이며, 클래스(class)와 클로저(closure)가 대표적인 참조 타입입니다.

<br>

### 1. 값 타입 (Value Type)
값 타입은 복사 시 독립적인 복사본이 생성되는 타입입니다. 변수에 값을 할당하거나 함수에 인자로 전달할 때, 새로운 메모리 공간에 값이 복사됩니다.

- 대표적인 값 타입: struct, enum, tuple, 기본 데이터 타입(Int, Double, String 등)
- 특징 :
    - 각 변수는 독립적인 값을 가지며, 서로 영향을 주지 않습니다.
	- Swift의 대부분의 기본 타입과 구조체가 값 타입입니다.
- 예시 :

```swift
struct Point {
    var x: Int
    var y: Int
}

var pointA = Point(x: 5, y: 5)
var pointB = pointA  // 값 복사

pointB.x = 10

print(pointA.x)  // 5 (pointA는 변경되지 않음)
print(pointB.x)  // 10
```

- 위 코드에서 pointB는 pointA의 값을 복사하여 생성되므로, pointB의 x를 변경해도 pointA의 값에 영향을 주지 않습니다.

<br>

### 2. 참조 타입 (Reference Type)
참조 타입은 복사 시 동일한 메모리 주소를 공유하므로, 하나의 객체를 여러 참조 변수가 공유하게 됩니다. 변수에 할당되거나 함수에 전달될 때 새로운 복사본이 아니라, 동일한 객체를 참조하게 됩니다.

- 대표적인 참조 타입: class, closure
- 특징:
    - 여러 변수가 하나의 인스턴스를 공유하며, 그 중 하나가 값을 변경하면 모든 참조가 영향을 받습니다.
    - 메모리 사용이 효율적일 수 있으나, 참조 공유로 인해 의도치 않은 결과가 발생할 수 있습니다.
- 예시 :
```swift
class Person {
    var name: String
    init(name: String) {
        self.name = name
    }
}

var personA = Person(name: "Alice")
var personB = personA  // 참조 복사

personB.name = "Bob"

print(personA.name)  // "Bob" (personA도 변경됨)
print(personB.name)  // "Bob"
```

- 위 코드에서 personB는 personA와 동일한 인스턴스를 참조하므로, personB의 name을 변경하면 personA의 name도 변경됩니다.

<br>

### 요약 
<img src="https://github.com/user-attachments/assets/9c18c79b-760f-4bce-89b1-5bb0360a14ac">

- 값 타입은 각 변수나 상수가 독립적으로 데이터를 관리하기에 코드의 예측 가능성을 높여주며, 참조 타입은 객체 간의 상호작용을 쉽게 하여 메모리 효율성을 제공합니다. Swift에서는 구조체와 열거형이 기본적으로 값 타입이며, 클래스를 통해 참조 타입을 구현할 수 있습니다.

<br>
<br>

## 구조체(Struct)와 클래스(Class)의 사용 시기는 어떻게 구분하나요?
Swift에서 **구조체(Struct)** 와 **클래스(Class)** 는 각각 고유한 특성을 가지며, 상황에 따라 적절한 것을 선택해 사용하는 것이 중요합니다. Swift는 구조체 중심 언어이므로 **구조체 사용을 권장하는 경우(애플에서 직접 추천함)** 가 많습니다. 다음은 구조체와 클래스를 구분하여 사용하는 기준입니다.

<br>

### 구조체(Struct) 사용 시기
1. 값 타입이 적합할 때:
- 구조체는 값 타입이므로, 인스턴스가 복사될 때마다 독립적인 복사본이 필요할 때 유용합니다.
- 데이터가 변경되지 않고, 복사본이 독립적으로 동작해야 할 때 사용합니다.
2. 간단한 데이터 모델일 때:
- 데이터 저장이 주요 역할일 때 사용합니다. 예를 들어, 좌표나 크기 같은 간단한 값을 저장하는 용도로 적합합니다.
3. 상태를 공유할 필요가 없을 때:
- 값 타입은 서로의 상태를 독립적으로 유지하므로, 여러 객체가 상태를 공유할 필요가 없는 경우 구조체를 사용하는 것이 적합합니다.
4. Swift 표준 라이브러리와 일관성 유지:
- Swift 표준 라이브러리의 기본 데이터 타입(Int, Double, Array 등)이 구조체로 구현되어 있으므로, 표준 라이브러리와 일관성을 유지하고 싶다면 구조체를 사용하는 것이 좋습니다.

<br>

### 클래스(Class) 사용 시기
1. 참조 타입이 필요한 경우:
- 클래스는 참조 타입이므로 여러 변수가 동일한 인스턴스를 참조할 수 있습니다.
- 여러 인스턴스가 상태를 공유하거나, 동일한 객체가 변경 사항을 유지해야 할 때 적합합니다.
2. 상속이 필요한 경우:
- 클래스는 상속이 가능하므로, 상속을 통해 코드 재사용과 다형성을 구현해야 할 때 사용합니다.
- 구조체는 상속을 지원하지 않기 때문에, 공통 기능을 상속 계층으로 구성해야 할 경우 클래스가 필요합니다.
3. ARC(Automatic Reference Counting)를 통한 메모리 관리가 필요한 경우:
- 클래스 인스턴스는 ARC로 메모리 관리를 하기 때문에, 여러 인스턴스가 메모리를 공유해야 할 때 적합합니다.
4. iOS의 Cocoa Touch 프레임워크 사용 시:
- UIKit의 UIViewController, UIView 등 많은 프레임워크 컴포넌트가 클래스로 설계되어 있으므로, 프레임워크와 호환성을 유지해야 할 때는 클래스를 사용합니다.

#### 예시 
```swift
class Animal {
    var name: String
    init(name: String) {
        self.name = name
    }
    func makeSound() {
        print("\(name) makes a sound.")
    }
}

class Dog: Animal {
    override func makeSound() {
        print("\(name) barks.")
    }
}

let myDog = Dog(name: "Buddy")
myDog.makeSound()  // 출력: "Buddy barks."
```

<br>

### 구조체와 클래스 사용 기준 요약

<img src="https://github.com/user-attachments/assets/b780a754-ec64-4761-810d-0f3931745033">

- 구조체는 주로 값을 저장하고 독립적으로 관리할 필요가 있는 데이터 모델에 적합합니다.
- 클래스는 상태를 공유하거나, 상속을 통한 기능 확장이 필요한 복잡한 객체 모델에 적합합니다.

<br>
<br>

## 열거형(Enum)의 원시값(Raw Value)과 연관값(Associated Value)은 무엇인가요?
**열거형(Enum)** 에서 **원시값(Raw Value)** 과 **연관값(Associated Value)** 은 각각 열거형의 값을 표현하는 방식입니다. 열거형의 원시값은 각 열거형 케이스에 고정된 값을 할당하는 방식이고, 연관값은 각 케이스에 별도의 추가 정보를 저장하여 동적으로 활용할 수 있게 합니다.

<br>

### 1. 원시값 (Raw Value)
원시값은 열거형의 각 케이스에 고정된 정수, 문자열 등 특정 타입의 값을 할당하여, 열거형이 해당 값을 나타내도록 합니다.

- 사용 목적: 각 케이스에 의미 있는 고정값을 부여하여, 열거형을 통해 코드의 가독성을 높이거나 외부 시스템과 상호작용할 때 사용됩니다.
- 타입: 모든 원시값은 동일한 데이터 타입(예: Int, String 등)이어야 합니다.
- 자동 할당: Int형 원시값은 첫 번째 값 이후로 자동으로 순서대로 증가할 수 있습니다.

#### 예시 : 정수형 원시값을 가지는 열거형
```swift
enum Direction: Int {
    case north = 1
    case south
    case east
    case west
}

print(Direction.north.rawValue)  // 출력: 1
print(Direction.south.rawValue)  // 출력: 2
```

- 위 예제에서 Direction 열거형은 정수형 원시값을 가지며, south, east, west는 순서대로 2, 3, 4가 할당됩니다.

<br>

#### 예시 : 문자열형 원시값을 가지는 열거형
```swift
enum Compass: String {
    case north = "North"
    case south = "South"
    case east = "East"
    case west = "West"
}

print(Compass.north.rawValue)  // 출력: "North"
```

- 위 코드에서 Compass는 문자열형 원시값을 가지고 있으며, 각 케이스는 대응하는 문자열 값으로 나타낼 수 있습니다.

<br>

### 2. 연관값 (Associated Value)
연관값은 열거형의 각 케이스에 추가적인 동적 값을 저장할 수 있도록 하는 기능입니다. 열거형 케이스마다 서로 다른 타입의 값을 가질 수 있으며, 연관값을 통해 열거형을 더 유연하게 사용할 수 있습니다.

- 사용 목적: 열거형 케이스마다 다른 정보를 추가로 저장하고 처리할 수 있습니다.
- 타입: 각 케이스가 다른 타입의 연관값을 가질 수 있으며, 값은 동적으로 설정됩니다.
- 예시 용도: 네트워크 요청 결과에 따른 상태 코드, 추가 데이터가 필요한 이벤트 처리 등에 사용됩니다.

#### 예제 : 연관값을 가지는 열거형
```swift
enum NetworkResult {
    case success(data: String)
    case failure(errorCode: Int)
}

let result1 = NetworkResult.success(data: "User data")
let result2 = NetworkResult.failure(errorCode: 404)

switch result1 {
case .success(let data):
    print("성공: \(data)")
case .failure(let errorCode):
    print("실패: 에러 코드 \(errorCode)")
}
```

- 위 예제에서 NetworkResult 열거형은 success와 failure 케이스에 각각 연관값을 가집니다. success 케이스에는 String 타입의 데이터가 연관값으로 저장되며, failure 케이스에는 Int 타입의 에러 코드가 연관값으로 저장됩니다.

<br>

### 요약
<img src="https://github.com/user-attachments/assets/4e92e894-247e-45e0-9d18-97dd0bfdcfb3">

- 원시값은 열거형의 케이스에 고정된 의미를 부여할 때 유용하고, 연관값은 각 케이스에 추가 데이터를 동적으로 저장하고 처리할 때 유용합니다.

<br>
<br>

## 7. **Xcode에서 디버깅 시 자주 사용하는 기능은 무엇인가요?**
Xcode에서 디버깅할 때 자주 사용하는 기능은 코드의 오류를 찾고 문제를 해결하는 데 큰 도움을 줍니다. 여기에는 브레이크포인트, LLDB 콘솔, 변수 관찰 등 다양한 기능이 포함됩니다. 각 기능의 목적과 활용 방법은 다음과 같습니다.

<br>

<img src="https://github.com/user-attachments/assets/cc67d1e1-d428-4e82-bca3-42d78dfd9ec5">

### 1. 브레이크포인트 (Breakpoints)
브레이크포인트는 코드의 특정 지점에서 실행을 일시 중지하여 코드 상태를 분석할 수 있게 해주는 기능입니다.

- 설정 방법: 코드 왼쪽 라인 번호 영역을 클릭하면 브레이크포인트가 설정됩니다.
- 활용: 코드 실행 중 특정 지점에서 변수의 값을 확인하거나 로직이 제대로 실행되는지 확인할 수 있습니다.
- 종류:
	- 표준 브레이크포인트: 코드 라인에서 실행을 중지합니다.
	- 조건부 브레이크포인트: 특정 조건이 만족될 때만 중지합니다.
	- 예외 브레이크포인트: 예외 발생 시 중지하여 오류의 원인을 추적합니다.

<br>

<img src="https://github.com/user-attachments/assets/94fc53e4-d2ee-49dc-9b78-f85e08db5add">


### 2. LLDB 콘솔 (Debugger Console)
LLDB 콘솔은 코드 실행 중 현재 스택의 상태를 명령어로 조회하거나, 변수를 직접 수정하고 함수 호출을 테스트할 수 있는 기능입니다.

- 활용:
	- po(print object): 객체의 설명을 출력하여 확인합니다.
	- p(print): 변수의 값을 확인하거나 메서드를 호출합니다.
	- expr(expression): 특정 변수를 수정하거나 계산을 수행하여 실행 중에 값 변경을 확인합니다.
- 예시 :
```swift
po someVariable  // someVariable의 값을 콘솔에 출력
p someFunction() // 함수 호출 결과를 출력
```

<br>

<img src="https://github.com/user-attachments/assets/85d69749-12b4-4c4e-987d-b33a501ca6c1">

### 3. 스택 트레이스 (Stack Trace)
스택 트레이스는 현재 호출된 함수의 순서를 추적하여, 어떤 함수들이 순차적으로 호출되었는지를 확인할 수 있습니다.

- 활용: 오류 발생 시, 호출된 함수의 순서를 확인하여 오류의 원인을 추적합니다.
- 예시: 앱이 충돌할 때 스택 트레이스를 확인하여 어떤 함수가 마지막으로 호출되었는지 확인할 수 있습니다.


<br>

<img src="https://github.com/user-attachments/assets/e6239d8b-03f4-4d02-85cc-b4c6690834a2">

### 4. View Debugging (뷰 디버깅)
View Debugging은 UI 요소가 의도한 대로 배치되지 않을 때 앱의 뷰 계층 구조를 시각적으로 디버깅할 수 있는 기능입니다.

- 활용:
	- 앱의 UI 계층을 3D로 분리하여 볼 수 있어, 누락된 뷰나 잘못된 제약 조건을 쉽게 파악할 수 있습니다.
- 설정 방법: 디버깅 중 “Debug View Hierarchy” 버튼을 클릭합니다.


<br>

<img src="https://github.com/user-attachments/assets/2c25c1ec-0214-4c0a-b275-95757bc9d4e0">

### 5. 메모리 그래프 디버거 (Memory Graph Debugger)
메모리 그래프 디버거는 메모리에서 객체의 관계를 시각화하여, 강한 참조 순환(retain cycle) 등을 확인할 수 있는 도구입니다.

- 활용: 메모리 누수, 강한 참조 순환 문제를 해결할 때 유용합니다.
- 설정 방법: 디버깅 중 “Memory Graph Debugger” 버튼을 클릭하여 사용합니다.

<br>

<img src="https://github.com/user-attachments/assets/952cb495-d0eb-4f37-95b5-1b021bd7bef6">

### 6. Runtime Issues (런타임 문제 탐지)
런타임 문제 탐지는 Xcode가 자동으로 런타임 오류를 탐지하여 알려주는 기능입니다. 메모리 누수, 잘못된 메모리 접근 등의 오류를 발견할 때 유용합니다.

- 활용: 실행 중 발생하는 오류를 자동으로 감지하고 경고 메시지를 통해 원인을 파악할 수 있습니다.
- 설정 방법: Xcode의 “Diagnostics” 설정에서 활성화할 수 있습니다.

<br>

<img src="https://github.com/user-attachments/assets/f38c18a7-c71d-48a0-897d-1c977a48e84e">

### 7. Thread Debugging (스레드 디버깅)
멀티스레드 환경에서 스레드의 상태를 확인하여, 특정 스레드가 대기 중인지, 실행 중인지 등을 파악할 수 있는 기능입니다.

- 활용: 멀티스레드 환경에서 교착 상태(deadlock)나 경쟁 조건(race condition)을 파악할 때 유용합니다.
- 설정 방법: 디버깅 중 “Thread” 탭을 통해 스레드 상태를 확인합니다.

<br>

###

<br>

### 요약

<img src="https://github.com/user-attachments/assets/173d4941-0563-4351-a75b-6c3b3d2d2893">


<br>
<br>

## 7.1 중단점(Breakpoint)의 종류와 활용 방법을 설명해주세요.

### 1. 표준 중단점 (Standard Breakpoint)
- 설명: 표준 중단점은 특정 코드 라인에서 프로그램 실행을 중단하도록 설정하는 가장 기본적인 중단점입니다.
- 활용 방법: 코드 왼쪽 번호 라인을 클릭하면 해당 라인에 중단점이 설정됩니다. 코드가 실행될 때 이 라인에서 멈추며, 변수의 값과 상태를 관찰할 수 있습니다.
- 활용 예시: 특정 함수 호출 시 인자 값이 올바른지 확인하거나, 조건문이 의도한 대로 실행되는지 확인할 때 사용합니다.

<br>


### 2. 조건부 중단점 (Conditional Breakpoint)
- 설명: 조건부 중단점은 특정 조건이 만족될 때에만 중단하는 중단점입니다.
- 활용 방법: 표준 중단점에서 오른쪽 클릭하여 “Edit Breakpoint…“를 선택하고, 조건을 추가할 수 있습니다. 조건을 입력하면 해당 조건이 참일 때만 중단합니다.
- 활용 예시: 반복문에서 특정 값이나 조건을 만족하는 경우에만 중단하고 싶을 때 유용합니다. 예를 들어, 배열의 특정 인덱스에서 중단하려면 index == 5와 같은 조건을 설정합니다.

<br>


### 3. 예외 중단점 (Exception Breakpoint)
- 설명: 예외 발생 시 코드가 중단되는 중단점입니다.
- 활용 방법: Xcode의 중단점 네비게이터(좌측 중단점 리스트)에서 + 버튼을 클릭하고 “Exception Breakpoint”를 추가합니다. 예외가 발생하면 예외 중단점이 실행 중지 시점을 포착합니다.
- 활용 예시: 앱 실행 중에 발생하는 크래시 원인을 찾고 해결할 때 유용합니다. 예외가 발생하는 즉시 코드가 중단되어 예외 발생 시점과 상태를 확인할 수 있습니다.

<br>


### 4. 심볼릭 중단점 (Symbolic Breakpoint)
- 설명: 특정 함수나 메서드 이름(심볼)으로 설정하는 중단점입니다. 메서드가 호출될 때마다 중단됩니다.
- 활용 방법: 중단점 네비게이터에서 + 버튼을 클릭하고 “Symbolic Breakpoint”를 선택하여, 중단하려는 함수 이름(예: viewDidLoad)을 입력합니다.
- 활용 예시: 특정 메서드가 언제 호출되는지 추적하고 싶을 때 유용합니다. UIKit 메서드처럼 내가 정의하지 않은 메서드 호출 시점도 확인할 수 있습니다.

<br>


### 5. 글로벌 중단점 (Global Breakpoint)
- 설명: 프로젝트 전체에서 특정 중단점을 모든 파일에 적용하는 중단점입니다.
- 활용 방법: 일반적으로 특정 심볼릭 중단점을 여러 파일에서 사용할 때 적용됩니다. Xcode는 기본적으로 심볼릭 중단점을 전역적으로 사용할 수 있도록 합니다.
- 활용 예시: 프로젝트 내 모든 클래스의 viewDidLoad 호출을 중단하고 싶을 때 유용합니다.

<br>


### 6. 조치 중단점 (Action Breakpoint)
- 설명: 특정 중단점에서 코드 실행을 중단하지 않고, 메시지 출력, 사운드 재생, LLDB 명령어 실행 등의 특정 조치를 수행합니다.
- 활용 방법: 중단점을 설정하고 오른쪽 클릭한 후, “Edit Breakpoint…“에서 “Add Action”을 선택하여 메시지를 출력하거나, 특정 명령어를 추가할 수 있습니다.
 -활용 예시: 중단점에서 프로그램 실행을 멈추지 않으면서 디버깅 로그를 출력하거나, LLDB 명령을 실행하고 싶을 때 사용합니다.

<br>


### 7. 로깅 중단점 (Logging Breakpoint)
- 설명: 중단점에서 실행 중단 없이 로그를 기록하는 중단점입니다.
- 활용 방법: Action Breakpoint에서 메시지를 출력하도록 설정하면 로깅 중단점으로 사용할 수 있습니다.
- 활용 예시: 함수 호출 시마다 특정 변수의 값을 로그로 출력하여 추적하고 싶을 때 사용합니다.


<br>

### 요약 

<img src="https://github.com/user-attachments/assets/a8e79b1b-0a76-4194-88e3-f9af54bd339f">


<br>
<br>

## 7.2 LLDB 콘솔에서 유용한 명령어는 어떤 것이 있나요?

### po (Print Object)

- 설명: 객체의 값을 출력합니다. 주로 String, Int와 같은 값뿐 아니라 객체의 전체 속성 값도 출력할 수 있습니다.
- 사용 예시:

```swift
po myVariable  // myVariable의 값을 콘솔에 출력
```

<br>

### p (Print)

- 설명: 특정 표현식(expression)의 값을 출력합니다. Swift와 Objective-C에서 사용할 수 있으며, 일반적으로 변수의 간단한 값을 확인할 때 사용합니다.
- 사용 예시:

```swift
p myVariable  // myVariable의 값을 출력
p myVariable + 5  // 연산 결과 출력
```

<br>

### expr (Expression)
- 설명: 변수를 변경하거나 특정 표현식을 평가할 때 사용합니다. 변수 값을 조정하거나, Swift 표현식을 실행해보고 결과를 확인할 때 유용합니다.
- 사용 예시:

```swift
expr myVariable = 10  // myVariable의 값을 10으로 설정
expr print("Hello from LLDB")  // 코드 실행 중 콘솔에서 출력
```

<br>

### bt (Backtrace)

- 설명: 현재 스택 트레이스를 출력하여 호출된 함수의 순서를 확인할 수 있습니다. 오류 발생 시 문제의 원인을 추적할 때 유용합니다.
- 사용 예시:


```swift
bt  // 현재 스택 트레이스 출력
```

<br>

### thread 관련 명령어

#### thread list
- 설명: 현재 실행 중인 모든 스레드 목록을 보여줍니다. 여러 스레드의 상태를 확인할 때 사용합니다.
- 사용 예시:

```swift
thread list  // 모든 스레드의 상태 출력
```

<br>

#### thread backtrace
- 설명: 특정 스레드의 스택 트레이스를 확인할 수 있습니다.
- 사용 예시:

```swift
thread backtrace  // 현재 스레드의 스택 트레이스
thread backtrace all  // 모든 스레드의 스택 트레이스
```

<br>

#### thread select
- 설명: 디버깅할 스레드를 선택합니다.
- 설명: 디버깅할 스레드를 선택합니다.

```swift
thread select 2  // 두 번째 스레드를 선택
```

<br>

### breakpoint 관련 명령어

#### breakpoint set
- 설명: 특정 함수나 코드 위치에 브레이크포인트를 설정합니다.
- 사용 예시:

```swift
breakpoint set --name viewDidLoad  // viewDidLoad 함수에 브레이크포인트 설정
breakpoint set --file ViewController.swift --line 42  // 특정 파일의 라인에 설정
```

<br>

#### breakpoint list
- 설명: 현재 설정된 모든 브레이크포인트를 나열합니다.
- 사용 예시:

```swift
breakpoint list  // 모든 브레이크포인트 목록 표시
```

<br>

#### breakpoint delete
- 설명: 설정된 브레이크포인트를 삭제합니다.
- 사용 예시:

```swift
breakpoint delete 2  // 두 번째 브레이크포인트 삭제
breakpoint delete  // 모든 브레이크포인트 삭제
```

<br>

### continue
- 설명: 중단된 코드 실행을 다시 시작합니다. 브레이크포인트에 의해 중단된 위치에서 디버깅을 계속 진행할 때 사용합니다.
- 사용 예시:

```swift
continue  // 코드 실행 재개
```
- 단축키로 command + Y


<br>

### step, next, finish

#### step: 한 줄씩 코드 실행을 진행하며, 함수 안으로 들어가서 실행합니다.

```swift
step  // 함수 내부로 진입하여 한 줄 실행
```

<br>

#### next: 한 줄씩 코드 실행을 진행하지만, 함수 내부로 들어가지 않고 현재 함수의 다음 라인으로 이동합니다.

```swift
next  // 한 줄 실행 (함수 내부로는 들어가지 않음)
```

<br>

#### finish: 현재 함수의 나머지를 모두 실행한 뒤, 호출한 위치로 돌아옵니다.

```swift
finish  // 현재 함수 종료 후 다음 라인으로 이동
```

<br>

### watchpoint
- 설명: 변수의 값이 변경될 때 중단되도록 설정하는 워치포인트를 설정합니다. 변수 값 변경을 추적할 때 유용합니다.
- 사용 예시:

```swift
watchpoint set variable myVariable  // myVariable이 변경될 때 중단
```

<br>

### help
- 설명: LLDB 콘솔에서 사용할 수 있는 명령어와 옵션을 안내합니다.
- 사용 예시:

```swift
help  // 모든 명령어 목록 보기
help breakpoint  // 특정 명령어에 대한 도움말 보기
```

<br>

### 요약
<img src="https://github.com/user-attachments/assets/3ec9845d-1c2e-4fc0-b12a-85af85ccbe15">


<br>
<br>

## 8. **iOS 앱에서 데이터를 저장하는 방법에는 어떤 것들이 있나요?**
iOS 앱에서 데이터를 저장하는 방법 중 UserDefaults, Keychain, Core Data, SQLite에 대해 자세히 설명하고, 각 방법의 사용 시 주의사항과 적합한 사용 시점에 대해 알아보겠습니다.

<br>

### 1. UserDefaults
UserDefaults는 간단한 키-값 쌍 데이터를 저장하는 방법으로, 사용자 설정이나 앱의 상태를 저장하는 데 적합합니다.

- 사용 시 주의할 점:
	- 대량 데이터 저장은 피해야 합니다. UserDefaults는 간단한 설정값, 사용자 선호도 등 작은 데이터를 저장하는 데 적합하며, 대용량 데이터를 저장하면 성능이 저하될 수 있습니다.
	- 보안이 필요한 데이터를 저장하지 않습니다. UserDefaults는 암호화되지 않기 때문에, 비밀번호와 같은 민감한 정보를 저장하기에 부적합합니다.
	- 파일 동기화 주의: iCloud 동기화 대상에서 제외되는 데이터도 있으므로, 기기 간 데이터 동기화가 필요한 경우 적절한 사용을 고려해야 합니다.


<br>

### 2. Keychain
Keychain은 iOS에서 민감한 데이터를 안전하게 저장하는 데 사용하는 암호화된 저장소입니다.

- 적합한 데이터:
	- 보안이 중요한 데이터(예: 비밀번호, API 토큰, 인증서 등)를 저장하기에 적합합니다.
	- 앱 삭제 후에도 데이터 유지가 필요한 경우에도 사용할 수 있습니다.
	- 여러 앱 간 데이터 공유가 필요할 때 App Groups와 함께 사용할 수 있습니다.
- 주의할 점:
	- iCloud 동기화 설정: 기본적으로 iCloud와 동기화되지 않으며, 설정을 통해 iCloud Keychain에 저장할 수 있습니다.
	- 접근 권한 관리: 민감한 데이터를 다루기 때문에 Keychain 접근에 대해 사용자에게 충분히 안내해야 합니다.

<br>

### 3. Core Data
Core Data는 Apple이 제공하는 객체형 데이터 관리 프레임워크로, 데이터를 객체 모델로 관리할 수 있도록 ORM(Object-Relational Mapping)을 지원합니다.

- 특징:
	- 복잡한 데이터 관계, 대량 데이터 관리에 적합합니다.
	- 데이터베이스 연동을 위한 SQL 작성이 필요하지 않으며, 객체형으로 데이터를 다룰 수 있어 직관적입니다.
	- SQLite를 기본 저장소로 사용하지만, 비관계형 데이터도 관리할 수 있습니다.
	- 트랜잭션 및 데이터 변경 사항을 자동으로 관리하므로 데이터 일관성 유지를 위해 유용합니다.
- 사용 예:
	- 관계형 데이터 모델이 필요한 앱(예: 메모, 할일 목록, 대량 데이터 관리가 필요한 앱).
	- UI와 연동이 필요한 앱에서, 데이터 변경 시 UI에 자동으로 반영되도록 할 수 있습니다(NSFetchedResultsController 등).
- 주의할 점:
	- 상대적으로 초기 설정이 복잡합니다.
	- 성능 최적화를 위해 페칭(Fetching), 인덱싱, 프리디케이트(Predicate) 사용에 유의해야 합니다.
	- 데이터 저장소 크기가 커지면 성능 이슈가 발생할 수 있으며, Core Data의 메모리 관리에 유의해야 합니다.

<br>

### 4. SQLite
SQLite는 SQL 기반 경량 데이터베이스로, 직접 SQL 쿼리를 사용하여 데이터를 관리합니다.

- 특징:
	- 파일 기반의 데이터베이스로, Core Data보다 낮은 레벨에서 데이터를 다룰 수 있습니다.
	- 데이터를 SQL 쿼리로 관리하기 때문에 구조화된 데이터를 다루기 좋으며, 빠르고 효율적입니다.
	- 경량 데이터베이스로 빠른 속도를 자랑하며, 복잡한 데이터 모델을 처리할 때 적합합니다.
- 사용 예:
	- Core Data보다 SQL을 통한 세부 쿼리 조작이 필요한 경우.
	- 관계형 데이터 구조가 필요하지만, 객체 모델보다 SQL이 더 익숙한 경우.
	- 대량의 구조화된 데이터를 저장하고 검색할 때 효율적입니다.
- 주의할 점:
	- SQL 쿼리를 직접 작성해야 하므로, 쿼리 작성에 익숙하지 않은 경우 사용하기 어려울 수 있습니다.
	- Core Data처럼 데이터 객체 모델링 및 자동화된 데이터 관리가 없기 때문에, 추가적인 데이터 관리 코드 작성이 필요합니다.

<br>

### Core Data와 SQLite의 차이점과 사용 시기
<img src="https://github.com/user-attachments/assets/a2a5a563-444d-435d-a13f-1fd4c55cde81">

- Core Data는 데이터 모델링과 객체 간 관계가 필요한 경우, 특히 UI와 실시간 연동이 필요한 앱에서 적합합니다.
- SQLite는 SQL 쿼리로 데이터 접근을 관리할 때, 또는 Core Data의 복잡함이 필요하지 않은 경우 사용하기 좋습니다.


<br>
<br>

## 9. **Swift에서 프로토콜(Protocol)이란 무엇이며, 어떻게 활용하나요?**
**프로토콜(Protocol)** 은 특정 기능이나 속성을 요구하는 청사진 역할을 하며, 클래스, 구조체, 열거형이 특정 역할을 수행하도록 하기 위한 약속입니다. 프로토콜을 채택하는 타입은 프로토콜이 요구하는 속성이나 메서드를 반드시 구현해야 합니다.

#### 예시 :
```swift
protocol Drivable {
    var maxSpeed: Int { get }
    func drive()
}

struct Car: Drivable {
    var maxSpeed = 150
    func drive() {
        print("Driving at max speed of \(maxSpeed) km/h")
    }
}
```

<br>

### 1. 프로토콜의 요구사항
프로토콜은 특정 타입이 준수해야 할 속성, 메서드, 이니셜라이저 등을 요구할 수 있습니다. 프로토콜 자체에는 구현이 없으며, 이를 준수하는 타입이 요구사항을 직접 구현해야 합니다.

<br>

#### 속성 요구사항: 
프로퍼티를 읽기 전용({ get })으로 요구하거나 읽기와 쓰기 가능({ get set })으로 요구할 수 있습니다.

```swift
protocol Identifiable {
    var id: String { get }
}
```

<br>

#### 메서드 요구사항: 
메서드를 정의하되, 실제 구현은 프로토콜을 채택한 타입에서 구현합니다.

```swift
protocol Flyable {
    func fly()
}
```

<br>

#### 이니셜라이저 요구사항: 
특정 이니셜라이저를 요구할 수 있으며, 클래스에서 사용할 때는 required 키워드가 필요합니다.

```swift
protocol Initializable {
    init(name: String)
}
```

> required 키워드 :
> - required 이니셜라이저는 클래스가 특정 프로토콜을 준수할 때, 프로토콜에서 요구된 이니셜라이저가 상속 관계에서도 반드시 구현되도록 강제하는 역할을 합니다.
> - 상속받은 모든 자식 클래스에서 이니셜라이저를 반드시 구현해야 하는 상황에서 유용합니다.

<br>

### 2. 프로토콜 확장(Protocol Extension)
**프로토콜 확장(Protocol Extension)** 은 프로토콜에 직접 메서드 구현을 추가할 수 있는 기능으로, 프로토콜을 채택한 모든 타입에 기본 구현을 제공할 수 있습니다. 이 방식은 코드의 중복을 줄이고, 프로토콜의 기능을 확장하여 더 유연하게 사용할 수 있게 합니다.

<br>


#### 이유:
- 공통 기능을 기본 구현으로 제공하여, 프로토콜을 채택한 각 타입에서 중복 구현을 피할 수 있습니다.
- 메서드와 속성을 확장하여 모든 채택 타입에 추가적인 기능 제공이 가능해집니다.
- 특정 메서드를 구현하지 않아도 기본 구현을 활용할 수 있어 더 간편하게 프로토콜을 사용할 수 있습니다.

#### 예시:

```swift
protocol Greetable {
    func greet()
}

extension Greetable {
    func greet() {
        print("Hello!")
    }
}

struct Person: Greetable {}
let person = Person()
person.greet()  // 출력: Hello!
```

<br>

### 3. 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)
**프로토콜 지향 프로그래밍(Protocol-Oriented Programming, POP)** 은 타입 간 공통 기능을 프로토콜과 프로토콜 확장을 통해 제공하여, 상속이 아닌 구성과 역할 분리를 추구하는 방식입니다. Swift에서는 프로토콜을 중심으로 코드의 유연성과 재사용성을 높이고자 하는 POP 스타일을 권장합니다.

<br>

#### 장점:
- 다중 상속의 문제 해결: Swift는 다중 상속을 지원하지 않으나, 다중 프로토콜 채택을 통해 여러 역할을 쉽게 결합할 수 있습니다.
- 유연한 설계: 클래스, 구조체, 열거형 모두 프로토콜을 채택할 수 있어, 특정 기능을 여러 타입에 일관되게 적용할 수 있습니다.
- 코드 재사용성 증가: 프로토콜 확장 덕분에 기본 구현을 제공하여, 각 타입에서 중복된 구현을 피하고 공통 기능을 재사용할 수 있습니다.
- 의존성 낮춤: 상속 기반 설계에서 발생할 수 있는 강한 의존성을 줄이고, 역할과 책임을 명확히 분리할 수 있습니다.

#### 예시:
```swift
protocol Drawable {
    func draw()
}

extension Drawable {
    func draw() {
        print("Drawing a shape")
    }
}

struct Circle: Drawable {}
struct Square: Drawable {}

let shapes: [Drawable] = [Circle(), Square()]
for shape in shapes {
    shape.draw()  // Circle과 Square 모두 "Drawing a shape" 출력
}
```

- 이 예시에서 Circle과 Square는 공통된 기능을 기본 구현으로 제공받아 코드의 간결성과 일관성을 유지할 수 있습니다. 이처럼 프로토콜과 프로토콜 확장은 객체 간 역할을 명확히 하면서, 코드 재사용성과 유연성을 높이는 데 큰 도움이 됩니다.

<br>
<br>

## 10. **Swift의 접근 제어자(Access Control Levels)에 대해 설명해주세요.**
- Swift에서 접근 제어자는 코드의 접근 범위와 가시성을 제어하여 모듈이나 클래스 내에서 보안을 강화하고, 코드의 의도하지 않은 사용을 방지하기 위해 사용됩니다.
- 접근 제어자를 통해 타입, 속성, 메서드 등에 대한 접근 범위를 설정할 수 있으며, 이를 통해 코드의 구조를 보다 명확하게 유지할 수 있습니다.

<br>

### Swift의 접근 제어자 종류와 설명

#### 1.	open: 외부 모듈에서 접근 및 상속이 가능하며, 오버라이딩도 허용됩니다.
- 사용 예: 모듈을 공개하고, 외부 모듈이 해당 클래스를 상속하고 메서드를 오버라이딩할 수 있도록 할 때 사용합니다.
- 주로 라이브러리와 프레임워크에서 사용되며, 외부 사용자에게 API를 제공할 때 유용합니다.

<br>

#### 2.	public: 외부 모듈에서 접근할 수 있지만, 상속 및 오버라이딩은 허용하지 않습니다.
- 사용 예: 외부 모듈에 접근이 필요하지만 상속이나 오버라이딩을 막아야 할 때 사용합니다.
- 외부에서 클래스의 인스턴스나 메서드를 사용할 수는 있지만, 기능을 변경하지 못하도록 제한합니다.

<br>

#### 3.	internal (기본): 같은 모듈 내에서만 접근 가능합니다. 모듈 외부에서는 접근할 수 없습니다.
- 사용 예: 같은 모듈 내에서만 사용하고, 외부 모듈에는 노출하지 않으려는 기능에 사용됩니다.
- Swift의 기본 접근 수준으로, 명시하지 않으면 internal로 설정됩니다.


<br>

#### 4.	fileprivate: 같은 파일 내에서만 접근 가능합니다.
- 사용 예: 같은 파일 내에서만 사용되는 구현 세부 사항을 숨기고 싶을 때 사용합니다. 여러 타입이 협력해야 하지만, 외부에서는 접근하지 못하도록 제한할 수 있습니다.


<br>

#### 5.	private: 같은 선언(클래스, 구조체 등) 내에서만 접근 가능합니다.
- 사용 예: 특정 타입 내부에서만 접근해야 하는 속성이나 메서드에 사용하여 가장 높은 수준의 캡슐화를 제공합니다.
- 코드의 무결성을 유지하며, 외부 코드에 의해 잘못 사용되는 것을 방지합니다.

<br>

### open과 public의 차이점
- open: 외부 모듈에서 접근, 상속, 오버라이딩이 모두 가능합니다. 클래스와 클래스 멤버에만 사용할 수 있습니다.
- public: 외부 모듈에서 접근은 가능하지만, 상속 및 오버라이딩은 불가능합니다. 외부 모듈이 코드의 세부 사항을 변경하지 못하도록 제한할 때 사용됩니다.

<br>

### internal, fileprivate, private의 사용 시기
- internal: 기본 접근 제어 수준으로, 모듈 내에서 자유롭게 접근 가능한 상태로 설정됩니다. 외부에는 노출할 필요가 없지만, 모듈 내부에서는 접근이 필요할 때 주로 사용됩니다.
- fileprivate: 같은 파일 내에서만 접근할 수 있으며, 타입 간의 협력이 필요한 경우에 사용합니다. 특히, 같은 파일 내의 다른 타입이나 익스텐션과 데이터를 공유해야 하는 경우 유용합니다.
- private: 가장 제한적인 접근 수준으로, 특정 선언 내에서만 사용할 수 있습니다. 속성, 메서드 등이 외부에서 조작되는 것을 방지하며, 완전한 캡슐화가 필요할 때 사용합니다.

<br>

### 접근 제어자를 사용하는 이유
1. 캡슐화: 클래스, 구조체, 열거형 등의 구현 세부 사항을 숨겨 외부에서 수정하거나 접근하지 못하도록 보호합니다.
2. 코드 안정성: 다른 객체나 외부 모듈이 내부 구조에 영향을 주지 않도록 하여, 코드의 안정성을 높입니다.
3. 의도한 접근만 허용: 외부 모듈이나 객체가 필요하지 않은 기능을 호출하지 못하도록 하여, 코드의 의도에 맞는 사용만을 강제합니다.
4. 모듈화: 코드의 분리를 통해 유지 보수성을 높이고, 코드가 의도한 용도로만 사용될 수 있도록 보장합니다.

<br>

<img src="https://github.com/user-attachments/assets/1efe5c61-325d-4324-aa08-34d8b6004499">

<br>
<br>

## 11. **iOS 앱에서 네트워크 통신을 하는 방법에는 어떤 것들이 있나요?**
iOS 앱에서 네트워크 통신을 하는 방법에는 기본 프레임워크인 URLSession과 서드파티 라이브러리인 Alamofire, Moya 같은 옵션이 있습니다. 이들을 사용하여 서버와 데이터 송수신을 할 수 있습니다.

<br>

### 1. URLSession을 사용한 네트워크 통신

URLSession은 iOS에서 네트워크 요청을 보내고 데이터를 받아오는 작업을 담당하는 클래스입니다. HTTP, HTTPS 프로토콜을 통한 데이터 송수신, 파일 다운로드, 업로드 등의 다양한 네트워크 작업을 수행할 수 있습니다.

<br>

#### URLSession의 기본 사용 방법
1. URL 설정: 요청을 보낼 URL을 설정합니다.
2. URLSession 인스턴스 생성: URLSession의 shared 인스턴스 또는 커스텀 설정을 적용한 URLSession을 생성합니다.
3. Data Task 생성 및 실행: 요청을 보내고 응답을 받아 처리할 dataTask를 생성하여 실행합니다.
4. 응답 처리: 서버의 응답 데이터를 받아 처리합니다.

#### 예시 코드:
```swift
import Foundation

// 1. URL 설정
guard let url = URL(string: "https://jsonplaceholder.typicode.com/todos/1") else { return }

// 2. URLSession 인스턴스 생성
let session = URLSession.shared

// 3. Data Task 생성 및 실행
let task = session.dataTask(with: url) { data, response, error in
    // 4. 에러 처리
    if let error = error {
        print("Error occurred: \(error)")
        return
    }
    
    // 응답 데이터 처리
    if let data = data {
        do {
            let jsonObject = try JSONSerialization.jsonObject(with: data, options: [])
            print("Received data:", jsonObject)
        } catch {
            print("JSON Parsing Error: \(error)")
        }
    }
}

task.resume()  // 네트워크 요청 시작
```

<br>

#### 2. 네트워크 요청 시 에러 처리
네트워크 요청은 여러 원인(네트워크 문제, 서버 오류, 클라이언트 오류 등)으로 인해 실패할 수 있습니다. 일반적으로 URLSession의 completion handler에서 에러 객체를 확인하거나 HTTP 상태 코드로 에러를 처리합니다.

<br>

#### 에러 처리 방법:
1. error 객체 확인: dataTask의 completion handler에서 전달되는 error 객체가 nil이 아닌 경우 네트워크 요청 실패로 간주합니다.
2. HTTP 응답 코드 확인: response의 상태 코드(예: 200 성공, 404 오류, 500 서버 오류 등)를 통해 서버 응답의 성공 여부를 판단합니다.
3. 데이터 파싱 에러 처리: JSON이나 XML 데이터 파싱 중에 발생하는 에러를 do-catch 문을 통해 처리합니다.

#### 예시 코드:
```swift
let task = session.dataTask(with: url) { data, response, error in
    // 1. 네트워크 에러 확인
    if let error = error {
        print("Network error: \(error)")
        return
    }

    // 2. HTTP 응답 코드 확인
    if let httpResponse = response as? HTTPURLResponse, httpResponse.statusCode != 200 {
        print("HTTP error: \(httpResponse.statusCode)")
        return
    }

    // 3. 데이터 파싱 에러 처리
    if let data = data {
        do {
            let jsonObject = try JSONSerialization.jsonObject(with: data, options: [])
            print("Data received:", jsonObject)
        } catch {
            print("JSON parsing error: \(error)")
        }
    }
}

task.resume()
```

<br>

#### 3. 서드파티 라이브러리(예: Alamofire)를 사용하는 이유
Alamofire와 같은 서드파티 네트워크 라이브러리는 URLSession을 더 쉽게 다루고, 네트워크 요청에 필요한 다양한 기능을 제공하여 편리하게 사용할 수 있습니다.

<br>

#### Alamofire의 주요 장점
1. 간단하고 가독성 높은 코드: Alamofire는 URLSession보다 간단한 문법으로 네트워크 요청을 작성할 수 있어 코드 가독성이 높습니다.
2. 편리한 응답 핸들링: JSON 응답 파싱이 내장되어 있어, JSON 데이터를 보다 쉽게 처리할 수 있습니다.
3. 추가 기능: API 요청과 응답에 대한 로깅, 네트워크 상태 모니터링, 이미지 업로드 및 다운로드 기능 등을 제공합니다.
4. 에러 처리와 리트라이 기능: 에러에 대해 자동으로 재시도하는 기능과, HTTP 상태 코드에 따른 에러 처리를 쉽게 관리할 수 있습니다.

#### Alamofire 예시 코드:
```swift
import Alamofire

Alamofire.request("https://jsonplaceholder.typicode.com/todos/1").responseJSON { response in
    switch response.result {
    case .success(let value):
        print("Received data:", value)
    case .failure(let error):
        print("Error occurred:", error)
    }
}
```

<br>

#### Moya의 주요 특징
1. 코드 가독성 향상: API 엔드포인트를 프로토콜로 정의하여 직관적이고 간결하게 작성할 수 있습니다.
2. 타입 안전성: 요청마다 타입을 지정할 수 있어 코드의 안정성과 유지보수성을 높여줍니다.
3. 네트워크 테스트 및 Mocking 용이: 테스트용 가짜 응답을 쉽게 생성하여 네트워크 테스트가 가능합니다.
4. Alamofire 통합: Alamofire 위에 구성되어 있어 Alamofire의 기능을 그대로 활용할 수 있습니다.

<br>

#### Moya의 기본 사용 방법
Moya에서는 API의 각 엔드포인트를 TargetType 프로토콜로 정의하여 관리합니다. TargetType에는 API 요청에 필요한 정보(기본 URL, 경로, HTTP 메서드, 파라미터 등)를 설정하고, 각 엔드포인트를 코드로 구분할 수 있습니다.

<br>

#### 1. TargetType 설정
Moya에서는 API 엔드포인트를 TargetType이라는 프로토콜로 정의합니다. TargetType 프로토콜의 주요 속성들은 다음과 같습니다:

- baseURL: 기본 URL을 정의합니다.
- path: API 경로를 정의합니다.
- method: HTTP 메서드를 정의합니다 (GET, POST 등).
- task: 요청 타입과 파라미터를 정의합니다 (예: .requestParameters).
- headers: 요청에 필요한 HTTP 헤더를 설정합니다.

#### TargetType 예시

```swift
import Moya

// 1. API 엔드포인트를 정의하는 TargetType 프로토콜
enum MyAPI {
    case getUser(id: Int)
    case createUser(name: String, age: Int)
}

// 2. TargetType 프로토콜 구현
extension MyAPI: TargetType {
    var baseURL: URL { return URL(string: "https://jsonplaceholder.typicode.com")! }

    var path: String {
        switch self {
        case .getUser(let id):
            return "/users/\(id)"
        case .createUser:
            return "/users"
        }
    }

    var method: Moya.Method {
        switch self {
        case .getUser:
            return .get
        case .createUser:
            return .post
        }
    }

    var task: Task {
        switch self {
        case .getUser:
            return .requestPlain
        case .createUser(let name, let age):
            return .requestParameters(parameters: ["name": name, "age": age], encoding: JSONEncoding.default)
        }
    }

    var headers: [String: String]? {
        return ["Content-Type": "application/json"]
    }
}
```
<br>

#### 2. MoyaProvider 인스턴스 생성
MoyaProvider는 요청을 보내는 데 사용하는 클래스입니다. 이 클래스의 인스턴스를 생성하고, 정의한 API 엔드포인트를 호출합니다.

```swift
let provider = MoyaProvider<MyAPI>()
```

<br>

#### 3. 네트워크 요청 보내기
Moya에서는 요청을 쉽게 작성할 수 있습니다. provider.request 메서드에 API 엔드포인트를 지정하고, 응답을 처리하는 클로저에서 결과를 확인할 수 있습니다.

```swift
// 사용 예시
provider.request(.getUser(id: 1)) { result in
    switch result {
    case .success(let response):
        do {
            let data = try response.mapJSON()
            print("Data received:", data)
        } catch {
            print("JSON parsing error:", error)
        }
    case .failure(let error):
        print("Request failed:", error)
    }
}
```

#### 네트워크 테스트 및 Mocking 지원
Moya는 테스트용으로 Mock 데이터를 쉽게 설정할 수 있어 실제 네트워크 없이도 요청을 테스트할 수 있습니다. 이를 통해 네트워크 응답이 없는 경우에도 테스트가 가능해집니다.

```swift
// MoyaProvider를 Stub 형식으로 생성하여 Mock 데이터 사용
let provider = MoyaProvider<MyAPI>(stubClosure: MoyaProvider.immediatelyStub)

provider.request(.getUser(id: 1)) { result in
    switch result {
    case .success(let response):
        let data = try? response.mapJSON()
        print("Mock data received:", data)
    case .failure(let error):
        print("Request failed:", error)
    }
}
```

<br>

#### Moya와 Alamofire 비교

<img src="https://github.com/user-attachments/assets/72e386aa-9862-40f2-a1d3-5b82c37f3063">

<br>

#### Moya의 장점 요약
- 간결하고 구조화된 코드 작성: API 요청을 구조화하여 코드의 가독성과 유지보수성을 높일 수 있습니다.
- 타입 안전한 네트워크 요청: TargetType을 통한 명확한 API 엔드포인트 정의로 타입 안전성을 강화합니다.
- 테스트 지원: Stub 기능을 통해 네트워크 테스트 및 Mock 데이터 생성을 쉽게 할 수 있어, 실제 네트워크 없이도 요청 테스트가 가능합니다.

Moya는 Alamofire의 확장으로, RESTful API 요청이 많은 프로젝트에서 API 관리와 테스트를 용이하게 하기 위한 최적의 라이브러리입니다.

<br>
<br>

## 12. **의존성 관리 도구(CocoaPods, Carthage, Swift Package Manager)의 종류와 차이점은 무엇인가요?**

iOS에서 의존성 관리 도구는 외부 라이브러리를 프로젝트에 쉽게 추가하고 관리할 수 있도록 도와줍니다. 대표적인 의존성 관리 도구로는 CocoaPods, Carthage, **Swift Package Manager(SPM)** 가 있습니다. 각 도구는 사용 방법과 관리 방식에 차이가 있으며, 필요에 따라 적합한 도구를 선택할 수 있습니다.

<br>

### 1. CocoaPods
CocoaPods는 가장 널리 사용되는 iOS 의존성 관리 도구 중 하나로, 루비 젬을 통해 설치하여 사용할 수 있습니다.

<br>

#### 사용 방법:
1. 터미널에서 sudo gem install cocoapods로 CocoaPods를 설치합니다.
2. 프로젝트 루트에 Podfile을 생성하고, 필요한 의존성을 추가합니다.

```ruby
platform :ios, '13.0'
target 'MyApp' do
    pod 'Alamofire'
end
```

3. pod install 명령을 실행하여 의존성을 설치합니다.
4. .xcworkspace 파일을 열어 프로젝트를 진행합니다.

<br>

#### 장점:
- 사용이 간편하고, 잘 알려진 도구로 커뮤니티와 문서가 풍부합니다.
- 설치한 라이브러리 버전 관리를 Podfile.lock으로 지원합니다.
- 자동 업데이트와 함께 의존성 충돌 문제를 해결하는 기능이 있습니다.

#### 단점:
- 프로젝트에 .xcworkspace 파일이 생성되어, CocoaPods가 프로젝트를 관리하게 됩니다.
- 모든 라이브러리가 동적으로 연결되기 때문에 빌드 속도가 다소 느릴 수 있습니다.

<br>

### 2. Carthage
Carthage는 간단한 설정 파일로 의존성 관리를 최소화하면서 필요한 라이브러리만 관리하는 도구로, 명령줄 기반 관리를 지원합니다.

#### - 사용 방법:
1. 터미널에서 brew install carthage로 Carthage를 설치합니다.
2. 프로젝트 루트에 Cartfile을 생성하고, 필요한 의존성을 추가합니다.

```swift
github "Alamofire/Alamofire" ~> 5.0
```

3. carthage update 명령을 실행하여 의존성을 설치합니다.
4. 설치된 라이브러리의 .framework 파일을 수동으로 프로젝트에 추가하여 링크합니다.

<br>

#### 장점:
- Carthage는 프로젝트 구조에 영향을 주지 않으며, 설치된 라이브러리만 관리합니다.
- 빌드 속도가 상대적으로 빠르며, 모듈식으로 프레임워크를 관리할 수 있습니다.
- 동적/정적 프레임워크를 지원하여 빌드 효율성을 높입니다.

#### 단점:
- 설치와 관리가 수동적이라, 수동으로 .framework 파일을 추가해야 합니다.
- 자동 의존성 해결 기능이 없으며, 의존성 충돌 문제를 수동으로 해결해야 합니다.

<br>

### 3. Swift Package Manager (SPM)
**Swift Package Manager(SPM)** 는 애플에서 제공하는 공식 의존성 관리 도구로, Swift 3.0부터 통합되었고 Xcode 11부터 iOS 프로젝트와 통합되었습니다.

#### 사용 방법:
1. Xcode 프로젝트에서 File > Swift Packages > Add Package Dependency...를 선택합니다.
2. 원하는 패키지의 Git URL을 입력하고, 의존성을 추가합니다.
3. 추가된 라이브러리는 자동으로 프로젝트 내에서 사용이 가능합니다.

<br>

#### 장점:
- Xcode와 통합되어 사용이 간편하며, 별도의 설치가 필요 없습니다.
- 정적/동적 라이브러리를 자동으로 선택하여 빌드 속도를 최적화합니다.
- Swift와 iOS의 공식 지원 도구로서, 업데이트와 호환성 문제에 강점이 있습니다.

#### 단점:
- Swift 기반 라이브러리만 지원하여 Objective-C 기반 라이브러리는 사용할 수 없습니다.
- CocoaPods나 Carthage에 비해 아직 사용 가능한 패키지가 적을 수 있습니다.
- Xcode에 의존하기 때문에, Xcode 버전과의 호환성 문제를 신경 써야 합니다.

<br>

### 각 도구의 비교
<img src="https://github.com/user-attachments/assets/3eb46694-7761-4948-89ac-4c644aebb636">

<br>

### 의존성 관리를 통해 얻을 수 있는 이점
1. 자동화된 라이브러리 관리: 코드베이스와 외부 라이브러리 간의 호환성을 유지하며, 업데이트와 설치가 자동으로 이루어집니다.
2. 코드 재사용성 향상: 여러 프로젝트에서 동일한 라이브러리를 재사용하여 일관된 개발 환경을 유지할 수 있습니다.
3. 버전 관리: 의존성 관리 도구는 특정 버전의 라이브러리를 고정할 수 있어, 팀 전체가 동일한 버전을 사용하도록 돕고, 예상치 못한 충돌을 방지합니다.
4. 시간 절약: 필요한 라이브러리를 손쉽게 프로젝트에 추가할 수 있어, 개발 초기 설정 시간을 절약하고, 코드에 집중할 수 있습니다.
5. 의존성 충돌 방지: CocoaPods과 SPM은 의존성 간 충돌을 자동으로 해결하여 개발자가 직접 해결할 필요를 줄여줍니다.

<br>

### 결론
- CocoaPods는 간편한 설치와 커뮤니티 지원이 필요할 때 적합하며, 자동화된 의존성 관리가 필요한 경우 유용합니다.
- Carthage는 프로젝트 구조의 영향을 줄이면서 모듈화된 관리를 원하는 경우 적합합니다.
- Swift Package Manager는 Xcode와의 통합이 장점이며, Swift 기반 프로젝트와의 호환성이 높습니다.


<br>
<br>

## 13. **Swift의 고차 함수(Higher-Order Functions)에 대해 설명해주세요.**
- Swift의 **고차 함수(Higher-Order Functions)**는 다른 함수를 인자로 전달받거나 반환할 수 있는 함수를 말합니다. 
- Swift에서는 컬렉션 타입(Array, Dictionary, Set 등)에 대해 고차 함수인 map, flatMap, filter, reduce, compactMap 등을 사용하여 간결하고 효율적인 코드를 작성할 수 있습니다.

<br>

### 1. 고차 함수의 종류와 사용법

#### map
map은 컬렉션의 각 요소에 동일한 연산을 적용하여 새로운 배열을 반환하는 함수입니다. 배열의 각 요소를 변환하고자 할 때 주로 사용됩니다.

#### 사용 예시
```swift
let numbers = [1, 2, 3, 4, 5]
let squaredNumbers = numbers.map { $0 * $0 }
print(squaredNumbers)  // 출력: [1, 4, 9, 16, 25]
```

<br>

#### flatMap
flatMap은 중첩된 컬렉션을 **하나의 컬렉션으로 평탄화(flatten)**합니다. 이중 배열에서 모든 요소를 하나의 배열로 합치거나, 중첩된 옵셔널을 제거하는 경우에 사용됩니다.

#### 사용 예시
```swift
let nestedNumbers = [[1, 2, 3], [4, 5], [6]]
let flatNumbers = nestedNumbers.flatMap { $0 }
print(flatNumbers)  // 출력: [1, 2, 3, 4, 5, 6]
```

<br>

#### filter
filter는 컬렉션의 각 요소를 조건에 따라 걸러낸 새로운 배열을 반환합니다. 특정 조건을 만족하는 요소들만 추출하고 싶을 때 사용됩니다.

#### 사용 예시
```swift
let numbers = [1, 2, 3, 4, 5]
let evenNumbers = numbers.filter { $0 % 2 == 0 }
print(evenNumbers)  // 출력: [2, 4]
```

<br>

#### reduce
reduce는 컬렉션의 모든 요소를 하나의 값으로 합산하는 함수로, 초기값을 주고 이를 기반으로 컬렉션의 요소를 순차적으로 누적하여 결과값을 생성합니다. 합산, 곱셈, 문자열 연결 등 여러 연산에 유용하게 사용됩니다.

#### 사용 예시

```swift
let numbers = [1, 2, 3, 4, 5]
let sum = numbers.reduce(0) { $0 + $1 }
print(sum)  // 출력: 15
```

<br>

#### compactMap
compactMap은 컬렉션에서 nil 값을 제거하고 옵셔널을 해제한 배열을 반환합니다. 옵셔널 값을 포함한 배열에서 nil을 제거하고 싶을 때 사용됩니다.

#### 사용 예시
```swift
let numbers = ["1", "2", "three", "4"]
let validNumbers = numbers.compactMap { Int($0) }
print(validNumbers)  // 출력: [1, 2, 4]
```

<br>

### 2. map과 flatMap의 차이점
- map: 각 요소에 연산을 적용하여 변환된 새로운 배열을 반환합니다.
- flatMap: 각 요소에 연산을 적용한 후, 중첩 배열을 평탄화하여 하나의 배열로 반환합니다. 중첩된 배열을 처리하거나 옵셔널의 중첩을 제거할 때 사용됩니다.
- 💥 map + 반환되는 배열을 flat하게 만들어주는 기능이 추가된 메서드입니다.

```swift
let numbers = [1, 2, nil, 4]
let mapped = numbers.map { $0 }      // 출력: [Optional(1), Optional(2), nil, Optional(4)]
let flatMapped = numbers.flatMap { $0 }  // 출력: [1, 2, 4]
```

<br>

### 3. filter, reduce 함수의 사용 예
#### filter: 컬렉션에서 특정 조건을 만족하는 요소들만 추출할 때 사용됩니다.

```swift
let names = ["Alice", "Bob", "Charlie", "Dave"]
let shortNames = names.filter { $0.count <= 4 }
print(shortNames)  // 출력: ["Bob", "Dave"]
```

<br>

#### reduce: 컬렉션의 모든 요소를 하나의 값으로 합산할 때 유용합니다. 초기값을 설정하고, 각 요소를 누적하는 방식으로 작동합니다.

```swift
let expenses = [100, 250, 370]
let totalExpense = expenses.reduce(0) { $0 + $1 }
print(totalExpense)  // 출력: 720
```

<br>

### 4. compactMap의 역할
- 옵셔널 해제와 nil 제거: 컬렉션 내 옵셔널 요소들을 해제하고, nil 값을 제거한 배열을 반환합니다.
- 유용한 경우: 옵셔널 배열에서 유효한 값들만 추출하고 싶을 때 사용됩니다.

```swift
let numbers = ["1", "2", "three", "4"]
let validNumbers = numbers.compactMap { Int($0) }
print(validNumbers)  // 출력: [1, 2, 4]
```

<br>

### 요약

<img src="https://github.com/user-attachments/assets/2fbe07f6-6aab-4820-a10b-62332cc7e815">

- Swift의 고차 함수는 코드의 간결성과 효율성을 높여주며, 컬렉션 내 데이터의 변환, 필터링, 누적 등에 유용하게 활용됩니다.

<br>
<br>

## 14. **Git에서 브랜치(Branch)를 사용하는 이유와 장점은 무엇인가요?**
**브랜치(Branch)** 는 Git에서 프로젝트의 개별 작업을 독립적으로 진행할 수 있도록 분리된 작업 공간을 제공합니다. 새로운 기능 추가, 버그 수정, 실험 작업 등 다른 작업에 영향을 주지 않고 코드를 수정할 수 있게 합니다.

<br>

### 브랜치의 장점
- 독립적인 작업 공간 제공: 각각의 브랜치가 독립적이기 때문에, 코드베이스의 안정성을 유지하면서 새로운 기능 개발이나 버그 수정을 동시에 진행할 수 있습니다.
- 버전 관리 및 추적 용이: 각 브랜치는 분기 시점을 기준으로 작업의 변경 이력을 추적할 수 있어, 프로젝트의 진행 상황을 명확하게 파악할 수 있습니다.
- 협업 용이: 팀원들이 개별 브랜치에서 작업을 진행한 후 병합하여, 코드 충돌을 최소화하며 공동 작업이 가능합니다.


<br>

## 14.1 브랜치를 병합(Merge)하는 방법에는 어떤 것들이 있나요?
Git에서 브랜치를 병합하는 방법에는 Merge와 Rebase가 있습니다.

<br>

### Merge
Merge는 한 브랜치의 커밋 내역을 다른 브랜치에 통합하여, 두 브랜치를 병합합니다. Merge는 원래의 커밋 히스토리를 유지하며, 추가적인 병합 커밋이 생성됩니다.

- 장점: 원래의 커밋 이력을 그대로 유지하여 작업 흐름을 쉽게 파악할 수 있습니다.
- 단점: 병합 커밋이 추가되기 때문에, 복잡한 히스토리가 생성될 수 있습니다.
```bash
git checkout main
git merge feature-branch
```

<br>

### Rebase
Rebase는 한 브랜치의 커밋을 다른 브랜치의 최신 커밋 뒤에 재정렬하여 병합합니다. 이 방법은 선형적인 커밋 히스토리를 유지할 수 있습니다.

- 장점: 병합 커밋 없이 깨끗하고 선형적인 히스토리를 유지할 수 있습니다.
- 단점: 작업 흐름이 바뀌어 충돌 발생 시 해결이 어려울 수 있으며, 협업 중 다른 사람의 커밋을 임의로 변경하지 않도록 주의가 필요합니다.

```bash
git checkout feature-branch
git rebase main
```

<br>
<br>

## 14.2 브랜치 전략(예: Git Flow, GitHub Flow)에 대해 설명해주세요.
프로젝트의 규모와 팀의 협업 방식을 고려해 여러 브랜치 전략을 사용할 수 있습니다. 대표적인 브랜치 전략에는 Git Flow와 GitHub Flow가 있습니다.

<br>

### Git Flow
Git Flow는 복잡한 워크플로우를 관리하기 위해 장기 브랜치와 단기 브랜치를 사용하며, 주로 다음과 같은 브랜치를 포함합니다:

- main: 항상 배포 가능한 상태를 유지하는 브랜치입니다.
- develop: 새로운 기능 개발이 이루어지는 브랜치로, 통합 작업을 수행하여 최종적으로 main에 병합됩니다.
- feature 브랜치: 새로운 기능 개발을 위해 분기되며, 작업 완료 후 develop에 병합됩니다.
- release 브랜치: 배포 준비를 위한 브랜치로, 버그 수정이 이루어진 후 main에 병합됩니다.
- hotfix 브랜치: 긴급 수정이 필요할 때 생성되며, main과 develop에 병합됩니다.

<br>

### GitHub Flow
GitHub Flow는 더 간단한 구조로, 보통 main과 feature 브랜치만을 사용합니다.

- main: 항상 배포 가능한 상태를 유지하며, 직접적으로 코드 수정이 이루어지지 않습니다.
- feature 브랜치: 각 작업별로 분리하여 생성하며, 작업 완료 후 Pull Request를 통해 main에 병합됩니다.

<br>

### 14.3 충돌(Conflict) 발생 시 해결 방법
병합 시 같은 파일의 같은 부분이 서로 다른 브랜치에서 수정되었다면, 충돌이 발생할 수 있습니다. 이 경우 Git은 자동으로 병합하지 못하고 사용자가 직접 해결해야 합니다.

<br>

### 충돌 해결 방법
1. 충돌 확인: Git에서 충돌이 발생하면 해당 파일에 충돌 마커(<<<<<<<, =======, >>>>>>>)가 표시됩니다.
2. 충돌 해결: 충돌된 부분을 확인하여, 원하는 코드로 수정합니다.
3. 변경 사항 커밋: 충돌을 해결한 파일을 추가(git add)하고, 병합을 완료하기 위해 커밋합니다.

```swift
# 충돌이 발생한 파일을 수정한 후
git add <file>
git commit -m "Resolve merge conflict"
```

<br>

### 요약
<img src="https://github.com/user-attachments/assets/5dec38f1-0340-4c8c-92b5-c0d724b25cde">

Git에서 브랜치를 사용하는 것은 협업의 필수 요소로, 독립적인 작업 공간을 제공하며 작업 내용을 명확하게 관리할 수 있도록 도와줍니다. 각 프로젝트에 맞는 브랜치 전략을 사용하여 효율적인 개발 환경을 유지할 수 있습니다.


<br>
<br>

## 15. **Swift의 에러 처리 방법에 대해 설명해주세요.**
Swift에서 에러 처리는 프로그램 실행 중 발생할 수 있는 에러 상황에 대처하여 안정적인 코드 흐름을 유지하는 것을 의미합니다. Swift의 에러 처리는 throws, try, catch를 통해 이루어지며, 에러를 발생시키거나 전파하고, 처리하는 구조로 구성됩니다.

<br>

## 15.1 에러 처리의 기본 키워드
### 1. throws 키워드
- 함수가 에러를 발생시킬 가능성이 있을 때 함수 선언에 throws 키워드를 사용합니다.
- throws를 사용한 함수는 호출 시 반드시 try와 함께 사용해야 합니다.

```swift
enum NetworkError: Error {
    case badURL
}

func fetchData(from url: String) throws -> String {
    guard url.starts(with: "https") else {
        throw NetworkError.badURL
    }
    return "Data from \(url)"
}
```

<br>

### 2. try 키워드
- 에러가 발생할 수 있는 코드 앞에 붙여 사용하며, 에러를 발생시키는 함수 호출 시 사용됩니다.
- try에는 세 가지 형태가 있습니다:
	- try: 일반적인 에러 처리 방법으로 do-catch와 함께 사용합니다.
	- try?: 에러 발생 시 nil을 반환하여 옵셔널로 에러를 처리합니다.
	- try!: 에러가 발생하지 않을 것이라 확신할 때 사용하며, 에러 발생 시 런타임 에러가 발생합니다.

```swift
do {
    let data = try fetchData(from: "http://example.com")
    print(data)
} catch {
    print("Failed to fetch data:", error)
}
```

<br>

### 3. catch 키워드
- catch는 do 블록에서 발생한 에러를 처리하는 데 사용합니다.
- catch 블록에서 에러를 특정 타입으로 분류하여 세분화된 에러 처리를 할 수도 있습니다.

```swift
do {
    let data = try fetchData(from: "http://example.com")
    print(data)
} catch NetworkError.badURL {
    print("Invalid URL provided.")
} catch {
    print("Unexpected error:", error)
}
```

- 옵셔널을 사용한 에러 처리와 do-catch를 사용하는 에러 처리의 차이

<br>

## 15.2 옵셔널을 사용한 에러 처리와 `do-catch`를 사용하는 에러 처리의 차이는 무엇인가요?
### 1. 옵셔널을 사용한 에러 처리 (try?)
- try?를 사용하면 에러가 발생할 경우 nil을 반환하여 에러를 옵셔널로 처리합니다.
- 에러의 구체적인 내용을 알 필요가 없거나, 에러 발생 시 nil로 대체해도 되는 경우에 유용합니다.

```swift
let data = try? fetchData(from: "http://example.com")
print(data)  // 에러 발생 시 nil 반환
```

<br>

### 2.	do-catch를 사용하는 에러 처리
- do-catch 구문은 에러를 명확히 캡처하여 구체적으로 처리할 수 있습니다.
- 에러의 원인이나 종류에 따라 여러 catch 블록을 만들어 구체적인 에러 처리가 가능하며, 에러 정보를 출력하거나 사용자에게 안내할 때 유용합니다.

```swift
do {
    let data = try fetchData(from: "http://example.com")
    print(data)
} catch {
    print("An error occurred:", error)
}
```

<br>
<br>

## 15.3 에러를 전파하는 방법은 무엇인가요?
- 에러를 전파하는 방법은 함수에서 발생한 에러를 호출한 곳으로 전달하여, 최종적으로 적절한 위치에서 에러를 처리할 수 있도록 하는 것입니다.
- 이를 위해 함수 선언에 throws를 명시하고, 함수 호출 시 try를 사용합니다.

```swift
func loadData() throws {
    try fetchData(from: "http://example.com")
}

do {
    try loadData()
} catch {
    print("Error while loading data:", error)
}
```
에러를 전파하면, 최종적으로는 do-catch 블록에서 에러를 처리하거나, 프로그램 전체의 흐름을 중단할 수 있습니다.

<br>

### 요약

<img src="https://github.com/user-attachments/assets/e3bb5e39-d9b7-464d-ae0f-d26970643b20">


<br>
<br>

## 16. **메모리 관리에서 강한 참조(Strong Reference)와 약한 참조(Weak Reference)의 차이점은 무엇인가요?**
### 강한 참조 (Strong Reference)
- 기본적인 참조 방식으로, 객체가 강한 참조를 가질 경우 **참조 카운트(Reference Count)**가 증가합니다.
- 참조 카운트가 0이 될 때까지 메모리에서 해제되지 않으므로, 객체가 메모리에 유지됩니다.
- 일반적으로 강한 참조는 객체 간의 의존 관계를 명확하게 유지하는 데 사용됩니다.
 
### 약한 참조 (Weak Reference)
- 약한 참조는 객체의 참조 카운트를 증가시키지 않습니다.
- weak 키워드를 사용하여 정의되며, 참조하는 객체가 해제되면 자동으로 nil이 할당됩니다.
- 순환 참조를 방지할 때 사용되며, optional로 선언되어야 합니다.

<br>
<br>

## 16.1 순환 참조(Retain Cycle)가 발생하는 경우와 해결 방법은 무엇인가요?

### 순환 참조(Retain Cycle)

**순환 참조(Retain Cycle)** 는 두 객체가 서로를 강하게 참조할 때 발생합니다. 두 객체 모두 참조 카운트가 0이 되지 않으므로, 메모리에서 해제되지 않고 **메모리 누수(Memory Leak)**가 발생하게 됩니다.

#### 순환 참조 예시
```swift
class Person {
    var pet: Pet?
}

class Pet {
    var owner: Person?
}

let person = Person()
let pet = Pet()

person.pet = pet
pet.owner = person  // 순환 참조 발생
```
이 예시에서는 Person과 Pet 객체가 서로를 강하게 참조하고 있어, 두 객체 모두 참조 카운트가 0이 되지 않아 메모리에서 해제되지 않습니다.

<br>

### 순환 참조 해결 방법
#### 1.	약한 참조 (weak) 사용: 

weak 키워드를 사용하여 참조 카운트를 증가시키지 않도록 하여, 순환 참조를 방지할 수 있습니다.

```swift
class Person {
    var pet: Pet?
}

class Pet {
    weak var owner: Person?  // 약한 참조
}
```

<br>

#### 2.	비소유 참조 (unowned) 사용: 
- unowned는 참조 카운트를 증가시키지 않으면서, 참조 대상이 해제될 것으로 확신하는 경우에 사용합니다.
- nil 할당이 허용되지 않아, 참조 대상이 해제되면 런타임 오류가 발생할 수 있습니다.



<br>
<br>

## 16.2 클로저에서 `[weak self]`와 `[unowned self]`의 차이는 무엇인가요?
클로저 내에서 self에 대한 강한 참조가 발생할 경우, 클로저와 객체가 서로를 강하게 참조하여 순환 참조가 발생할 수 있습니다. 이를 방지하기 위해, [weak self]나 [unowned self]를 사용하여 강한 참조를 약한 참조로 변경할 수 있습니다.

### [weak self]
- weak는 옵셔널로 선언되어, 참조 대상이 해제될 경우 자동으로 nil이 할당됩니다.
- 참조 대상이 해제될 수 있는 상황에 적합하며, self가 해제될 가능성이 있는 경우 안전하게 사용됩니다.
- 사용 시, 옵셔널 언래핑(self?)이 필요합니다.

```swift
class ViewController {
    var name = "ViewController"

    func doSomething() {
        DispatchQueue.global().async { [weak self] in
            guard let self = self else { return }
            print(self.name)
        }
    }
}
```

<br>

### [unowned self]
- unowned는 비옵셔널로 선언되어, 참조 대상이 해제되지 않을 것으로 확신할 때 사용합니다.
- 참조 대상이 클로저와 동일한 생명 주기를 가지거나, 확실히 해제되지 않는다고 보장되는 경우에 적합합니다.
- 해제된 객체에 접근 시 런타임 오류가 발생할 수 있습니다.

```swift
class ViewController {
    var name = "ViewController"

    func doSomething() {
        DispatchQueue.global().async { [unowned self] in
            print(self.name)
        }
    }
}
```

<br>

### 요약
<img src="https://github.com/user-attachments/assets/0db707d8-c706-4b31-a5dc-b1cd03a4a93b">

- 순환 참조 해결: weak 또는 unowned 참조를 통해 순환 참조를 방지할 수 있습니다.
- 클로저 내 weak self와 unowned self:
	- weak는 옵셔널로 선언되어 안전하며, 해제될 가능성이 있는 객체에 적합합니다.
	- unowned는 비옵셔널로, 객체가 해제되지 않을 것이라 확신하는 경우에 사용합니다.

Swift에서는 강한 참조, 약한 참조, 비소유 참조를 적절히 사용하여 메모리 관리의 안정성을 높일 수 있습니다.


<br>
<br>

## 17. **iOS 앱에서 Multi-threading을 구현하는 방법은 무엇인가요?**
iOS에서 멀티스레딩을 구현하는 주요 방법으로 **DispatchQueue** 와 **OperationQueue** 가 있습니다. 이를 통해 앱에서 작업을 병렬로 처리하고, 비동기적인 코드 실행을 통해 성능을 최적화할 수 있습니다.


<br>
<br>

## 17.1 `DispatchQueue`와 `OperationQueue`의 차이점은 무엇인가요?
### DispatchQueue:
- **GCD(Grand Central Dispatch)** 에서 제공하는 경량의 작업 스케줄러로, 주로 비동기적인 작업을 처리하는 데 사용됩니다.
- global(), main 등의 시스템 제공 큐와 커스텀 큐를 사용할 수 있습니다.
- 간단한 작업에서 성능이 좋고 코드가 간결하여 빠르게 비동기 작업을 처리할 때 유용합니다.
- 작업을 직렬(Serial) 또는 동시(Concurrent)로 실행할 수 있습니다.

```swift
DispatchQueue.global().async {
    // 비동기 작업 수행
}
DispatchQueue.main.async {
    // 메인 스레드에서 UI 업데이트
}
```

<br>

### OperationQueue:
- NSOperation과 함께 제공되며, 작업을 객체로 캡슐화하여 작업 간의 의존성 설정, 우선순위 조절 등이 가능합니다.
- 작업을 재사용하거나 작업 사이의 의존성 관계를 설정할 수 있어, 복잡한 작업 흐름에 적합합니다.
- 동기 및 비동기 작업 처리가 모두 가능하며, 취소할 수도 있습니다.

```swift
let queue = OperationQueue()
let operation = BlockOperation {
    // 비동기 작업 수행
}
queue.addOperation(operation)
```

<br>

### 비교 요약

<img src="https://github.com/user-attachments/assets/e51d1cd9-4299-454e-a758-aa4379613a31">

<br>



<br>
<br>

## 17.2 동시성 프로그래밍에서 Race Condition을 방지하는 방법은 무엇인가요?
Race Condition은 여러 스레드가 동시에 동일한 자원에 접근할 때 발생하며, 데이터의 불일치나 충돌이 생기는 문제입니다. 이를 방지하기 위해 스레드 안전성을 보장해야 하며, 여러 방법이 있습니다.

### 동기화(Synchronization):
- DispatchQueue의 serial queue를 사용해 작업을 직렬화하여 한 번에 하나의 작업만 실행하도록 합니다.

<br>

### NSLock 또는 synchronized 사용:
- NSLock이나 objc_sync_enter/objc_sync_exit을 사용하여 특정 코드 블록에 lock을 걸어 한 번에 하나의 스레드만 접근하도록 제한합니다.

<br>

### DispatchSemaphore:
- 특정 자원에 접근하는 작업 수를 제어하는 세마포어를 사용해 경쟁 조건을 방지할 수 있습니다.

```swift
let semaphore = DispatchSemaphore(value: 1)
semaphore.wait()
// 작업 수행
semaphore.signal()
```

<br>
<br>

## 17.3 메인 스레드에서 UI 업데이트를 해야 하는 이유는 무엇인가요?
메인 스레드는 앱의 UI 처리와 사용자 입력을 담당하는 스레드입니다. UIKit은 메인 스레드에서만 안전하게 작동하도록 설계되어 있으므로, UI 업데이트는 메인 스레드에서만 수행해야 합니다.

<br>

### 이유:
- UIKit과 View 계층은 메인 스레드에서만 안전하게 동작하도록 만들어졌습니다. 이 외의 스레드에서 UI를 업데이트하면 예측 불가능한 결과가 발생할 수 있습니다.
- 메인 스레드에서 실행하지 않으면 앱이 충돌하거나 레이아웃 갱신이 올바르게 이루어지지 않는 문제가 발생할 수 있습니다.

### 해결 방법:
- UI 작업을 다른 스레드에서 수행하려고 할 때는 반드시 메인 스레드에서 실행되도록 DispatchQueue.main.async를 사용합니다.

```swift
DispatchQueue.main.async {
    // UI 업데이트 작업
    self.label.text = "Updated Text"
}
```

<br>

### 요약
<img src="https://github.com/user-attachments/assets/55571237-ad5c-4c59-b959-47f3e703fcb8">

iOS에서 멀티스레딩은 동시성을 관리하며 성능을 최적화할 수 있는 중요한 요소입니다. DispatchQueue와 OperationQueue를 적절히 사용하고, Race Condition을 방지하여 안정적이고 효율적인 동시성 프로그래밍을 구현할 수 있습니다.

<br>
<br>

## 18. **UIKit에서 TableView와 CollectionView의 차이점은 무엇인가요?**
UITableView와 UICollectionView는 iOS에서 리스트 형태의 UI를 구현하는 데 사용됩니다. 두 가지 모두 셀 재사용을 통해 효율성을 높일 수 있지만, 사용하는 방식과 목적에는 차이가 있습니다.

<br>
### UITableView
- 단일 열로 구성된 세로 스크롤형 리스트입니다.
- 주로 리스트 형태의 단순한 데이터 표시를 위해 사용되며, 스크롤 방향은 세로로만 설정할 수 있습니다.
- 섹션과 행으로 구성되며, 각 셀의 높이를 다양하게 지정할 수 있습니다.

### UICollectionView
- 그리드 형태나 커스텀 레이아웃이 가능하여, 다양한 형태의 데이터 배치가 가능합니다.
- 세로와 가로로 모두 스크롤할 수 있으며, 여러 개의 열을 구성할 수 있어 레이아웃이 유연합니다.
- 커스텀 레이아웃을 적용하여 유연하고 복잡한 배치가 가능하므로 이미지 갤러리와 같은 UI에 적합합니다.

<br>


## 18.1 셀(Cell)의 재사용(Reusability)은 어떻게 구현되나요?
셀 재사용은 화면에 보이는 셀만 메모리에 로드하여 메모리 사용을 최소화하는 기법으로, UITableView와 UICollectionView 모두 셀을 큐에 저장하고 재사용하는 방식으로 구현됩니다.

<br>

### 셀 등록
- register 메서드를 사용해 셀을 등록하여 큐에 미리 로드합니다.

```swift
tableView.register(UITableViewCell.self, forCellReuseIdentifier: "cell")
collectionView.register(UICollectionViewCell.self, forCellWithReuseIdentifier: "cell")
```

<br>

### 셀 재사용
- dequeueReusableCell 메서드를 사용해 큐에서 셀을 가져옵니다. 기존 셀이 큐에 있다면 재사용하고, 없다면 새로 생성합니다.

```swift
// TableView에서 셀 재사용
let cell = tableView.dequeueReusableCell(withIdentifier: "cell", for: indexPath)

// CollectionView에서 셀 재사용
let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "cell", for: indexPath)
```

<br>
<br>

## 18.2 동적인 셀 높이(Dynamic Cell Height)를 설정하는 방법은 무엇인가요?
동적인 셀 높이는 콘텐츠 크기에 따라 셀의 높이를 자동으로 조정하는 방법입니다.

<br>

### UITableView에서 동적 셀 높이 설정:
- tableView.rowHeight = UITableView.automaticDimension와 estimatedRowHeight를 설정하여 셀의 높이가 자동으로 조정되도록 합니다.

```swift
tableView.rowHeight = UITableView.automaticDimension
tableView.estimatedRowHeight = 100  // 예상 높이
```

<br>

- UICollectionView에서는 셀의 높이를 유동적으로 지정하려면 UICollectionViewFlowLayoutDelegate의 sizeForItemAt 메서드를 구현하여 각 셀의 크기를 동적으로 반환할 수 있습니다.
```swift
extension ViewController: UICollectionViewDelegateFlowLayout {
    func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize {
        return CGSize(width: collectionView.frame.width / 2, height: 100)  // 예시로 각 셀의 높이를 지정
    }
}
```


<br>
<br>

## 18.3 CollectionView의 레이아웃을 커스터마이징하는 방법은 무엇인가요?
UICollectionView는 레이아웃을 커스터마이징할 수 있는 다양한 옵션을 제공합니다.

<br>

### 1. UICollectionViewFlowLayout:
- 기본 레이아웃으로, 아이템을 그리드 형식으로 배치합니다.
- itemSize, minimumInteritemSpacing, minimumLineSpacing 등을 설정하여 셀 간 간격을 조정할 수 있습니다.
- 아래 코드에서는 CustomFlowLayout을 만들어, 각 셀의 높이를 임의의 값으로 설정하여 워터폴 레이아웃을 생성합니다.

```swift
import UIKit

func createWaterfallLayout(with heights: [CGFloat]) -> UICollectionViewCompositionalLayout {
    return UICollectionViewCompositionalLayout { (sectionIndex, layoutEnvironment) -> NSCollectionLayoutSection? in
        
        // 화면 너비에 따라 열 수를 동적으로 설정
        let columns = layoutEnvironment.container.effectiveContentSize.width > 600 ? 3 : 2
        
        // 아이템 크기 설정 (너비는 비율, 높이는 이후에 그룹에서 설정)
        let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0), heightDimension: .fractionalHeight(1.0))
        let item = NSCollectionLayoutItem(layoutSize: itemSize)
        item.contentInsets = NSDirectionalEdgeInsets(top: 5, leading: 5, bottom: 5, trailing: 5) // 아이템 간 간격 설정

        // 섹션 내 각 아이템의 높이를 동적으로 설정
        var sectionItems: [NSCollectionLayoutGroup] = []
        
        // heights 배열의 각 값을 사용해 그룹 생성
        for height in heights {
            let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0 / CGFloat(columns)), heightDimension: .absolute(height))
            let group = NSCollectionLayoutGroup.vertical(layoutSize: groupSize, subitems: [item])
            sectionItems.append(group)
        }
        
        // 전체 그룹들을 포함하는 섹션 생성
        let layoutSection = NSCollectionLayoutSection(group: NSCollectionLayoutGroup.vertical(layoutSize: .init(widthDimension: .fractionalWidth(1.0), heightDimension: .absolute(sectionItems.reduce(0) { $0 + $1.layoutSize.heightDimension.dimension })), subitems: sectionItems))
        
        layoutSection.contentInsets = NSDirectionalEdgeInsets(top: 10, leading: 10, bottom: 10, trailing: 10)
        layoutSection.interGroupSpacing = 10
        
        return layoutSection
    }
}
```

<br>

### 2.	UICollectionViewCompositionalLayout:
- iOS 13부터 제공되는 레이아웃으로, 더 복잡하고 다양한 구성의 레이아웃을 만들 수 있습니다.
- NSCollectionLayoutItem, NSCollectionLayoutGroup, NSCollectionLayoutSection을 조합하여 레이아웃을 구성합니다.

```swift
// 각 아이템의 크기를 설정하는 NSCollectionLayoutSize를 생성
// 너비는 컬렉션 뷰의 50%이고, 높이는 그룹의 전체 높이로 설정
let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(0.5), heightDimension: .fractionalHeight(1))

// 아이템 레이아웃을 설정, 해당 레이아웃이 itemSize를 기반으로 아이템을 배치
let item = NSCollectionLayoutItem(layoutSize: itemSize)

// 그룹의 크기를 설정하는 NSCollectionLayoutSize를 생성
// 너비는 컬렉션 뷰의 전체 너비(100%)이며, 높이는 컬렉션 뷰의 20%로 설정
let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1), heightDimension: .fractionalHeight(0.2))

// 그룹을 가로 방향으로 설정하고, 각 그룹에 포함할 아이템을 지정
// 이 그룹은 item을 포함하며, item을 그룹 크기 내에 배치
let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize, subitems: [item])

// 그룹을 포함하는 섹션을 생성
let section = NSCollectionLayoutSection(group: group)

// 최종적으로 섹션을 포함하는 UICollectionViewCompositionalLayout을 생성
// 컬렉션 뷰에 해당 레이아웃을 적용하여 구성
let layout = UICollectionViewCompositionalLayout(section: section)
collectionView.collectionViewLayout = layout
```


<br>
<br>

## 18.4 diffableDatasource는 뭔가요?
- **diffable data source** 는 UITableView와 UICollectionView에서 데이터 관리 및 업데이트를 더 쉽게 하고 성능을 높여주는 새로운 데이터 소스입니다.
- iOS 13부터 도입된 UICollectionViewDiffableDataSource와 UITableViewDiffableDataSource를 통해 기존의 데이터 소스 방식보다 간단하고 안전하게 UI를 업데이트할 수 있습니다.

<br>

### diffable data source의 주요 특징
#### 1.	자동으로 변경 사항을 계산:
- 기존의 데이터 소스에서는 데이터가 변경될 때 삽입, 삭제, 이동, 업데이트 같은 변화를 수동으로 관리해야 했습니다.
- diffable data source는 이를 자동으로 계산해, **기존 데이터와 새 데이터 간의 차이점(diff)** 을 알아내고 필요한 변경 사항만을 적용합니다.
- 예를 들어, 새 데이터를 제공하면 diffable data source가 애니메이션과 함께 자동으로 셀을 추가, 삭제, 이동합니다.

#### 2.	스냅샷(Snapshot) 기반의 데이터 관리:
- diffable data source는 데이터를 직접 관리하는 대신, 스냅샷을 사용하여 데이터 상태를 표현합니다. 스냅샷은 현재 데이터의 일종의 사진처럼 작동하며, 스냅샷을 생성하고 이를 적용하는 방식으로 UI를 업데이트합니다.
- 이로 인해 변경 사항을 더 직관적이고 쉽게 관리할 수 있습니다.

#### 3.	데이터 불변성:
- 스냅샷이 적용되는 시점의 데이터 상태가 반영되기 때문에, 데이터는 불변성을 유지합니다. 데이터의 일관성이 높아져 다중 스레드 상황에서도 안전하게 사용할 수 있습니다.

<br>

### diffable data source의 사용 예시
- 아래는 UICollectionView에 diffable data source를 사용하는 간단한 예시입니다.
```swift
import UIKit

class ViewController: UIViewController {
    enum Section {
        case main
    }
    
    var collectionView: UICollectionView!
    var dataSource: UICollectionViewDiffableDataSource<Section, Int>!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 컬렉션 뷰 설정
        collectionView = UICollectionView(frame: view.bounds, collectionViewLayout: createLayout())
        view.addSubview(collectionView)
        
        // 셀 등록
        collectionView.register(UICollectionViewCell.self, forCellWithReuseIdentifier: "cell")
        
        // diffable data source 설정
        dataSource = UICollectionViewDiffableDataSource<Section, Int>(collectionView: collectionView) { (collectionView, indexPath, item) -> UICollectionViewCell? in
            let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "cell", for: indexPath)
            cell.contentView.backgroundColor = .systemBlue
            return cell
        }
        
        // 초기 데이터 로드
        applySnapshot(items: [1, 2, 3, 4, 5])
    }
    
    // 레이아웃 생성
    func createLayout() -> UICollectionViewLayout {
        let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0), heightDimension: .fractionalHeight(1.0))
        let item = NSCollectionLayoutItem(layoutSize: itemSize)
        let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0), heightDimension: .absolute(44))
        let group = NSCollectionLayoutGroup.vertical(layoutSize: groupSize, subitems: [item])
        let section = NSCollectionLayoutSection(group: group)
        return UICollectionViewCompositionalLayout(section: section)
    }
    
    // 스냅샷을 적용하여 UI 업데이트
    func applySnapshot(items: [Int], animatingDifferences: Bool = true) {
        var snapshot = NSDiffableDataSourceSnapshot<Section, Int>()
        snapshot.appendSections([.main])
        snapshot.appendItems(items)
        dataSource.apply(snapshot, animatingDifferences: animatingDifferences)
    }
}
```

<br>
<br>

## 19. **ARC(Automatic Reference Counting)의 동작 원리는 무엇인가요?**
    - Retain Cycle이 발생하지 않도록 방지하는 방법은 무엇인가요?
    - `deinit` 메서드는 언제 호출되며, 어떤 역할을 하나요?

<br>
<br>

## 20. **상속(Inheritance)과 프로토콜(Protocol)의 차이점은 무엇인가요?**
    - 클래스 상속을 사용할 때의 장단점은 무엇인가요?
    - 다중 상속(Multiple Inheritance)이 불가능한 이유는 무엇인가요?
    - 프로토콜 준수(Conformance)를 통해 다형성을 구현하는 방법은 무엇인가요?

<br>
<br>

## 21. **사용자 인터페이스(UI) 테스트와 단위(Unit) 테스트의 차이점은 무엇인가요?**
    - XCTest 프레임워크를 사용하여 테스트를 작성하는 방법은 무엇인가요?
    - 테스트 주도 개발(TDD)의 장점은 무엇인가요?
    - 의존성 주입(Dependency Injection)을 활용하여 테스트 가능한 코드를 작성하는 방법은 무엇인가요?

<br>
<br>

## 22. **Xcode에서 Instruments를 사용하여 앱의 성능을 분석하는 방법은 무엇인가요?**
    - Time Profiler를 사용하여 성능 이슈를 찾는 방법을 설명해주세요.
    - Allocations Instrument를 사용하여 메모리 누수를 탐지하는 방법은 무엇인가요?
    - Leaks Instrument를 사용하여 메모리 누수를 찾는 방법은 무엇인가요?

<br>
<br>

## 23. **Swift의 제네릭(Generic)에 대해 설명해주세요.**
    - 제네릭을 사용하는 이유는 무엇인가요?
    - 제네릭 타입 파라미터와 제약 조건을 설정하는 방법은 무엇인가요?
    - 제네릭을 사용할 때의 장점과 주의할 점은 무엇인가요?

<br>
<br>

## 24. **Swift의 클로저와 함수의 차이점은 무엇인가요?**
    - 클로저가 일급 객체(First-Class Citizen)인 이유는 무엇인가요?
    - 함수형 프로그래밍 패러다임에서 클로저가 어떻게 활용되나요?

<br>
<br>

## 25. **동시성 프로그래밍에서 동기(Synchronous)와 비동기(Asynchronous)의 차이점은 무엇인가요?**
    - iOS에서 비동기 작업을 처리하는 방법은 무엇인가요?
    - 세마포어(Semaphore)와 뮤텍스(Mutex)의 차이점은 무엇인가요?



<br>
<br>

## 26. **GCD(Grand Central Dispatch)의 주요 개념과 사용 방법을 설명해주세요.**
    - 직렬(Serial) 큐와 동시(Concurrent) 큐의 차이는 무엇인가요?
    - 글로벌 큐(Global Queue)와 메인 큐(Main Queue)는 어떻게 다르나요?
    - DispatchWorkItem을 사용하는 방법은 무엇인가요?

<br>
<br>
