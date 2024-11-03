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



    - 앱의 각 상태(`Not Running`, `Inactive`, `Active`, `Background`, `Suspended`)에서 가능한 작업은 무엇인가요?
    - 상태 변화에 따라 호출되는 `AppDelegate` 또는 `SceneDelegate` 메서드는 무엇인가요?
    - 백그라운드에서 작업을 완료하기 위한 방법은 어떤 것이 있나요?

<br>
<br>

## 3. **Auto Layout을 사용하는 이유와 장점은 무엇인가요?**
    - 제약 조건(Constraints)의 우선순위(Priority)는 어떻게 동작하나요?
    - Intrinsic Content Size란 무엇이며, 어떻게 활용되나요?
    - Ambiguous Layout과 Unsatisfiable Constraints는 무엇이며, 어떻게 해결하나요?

<br>
<br>

## 4. **Swift에서 클로저(Closure)란 무엇이며, 어떻게 사용하나요?**
    - 클로저의 캡처(Capture) 기능은 무엇인가요?
    - @escaping 클로저와 non-escaping 클로저의 차이점은 무엇인가요?
    - 트레일링 클로저(Trailing Closure) 문법은 어떤 경우에 유용한가요?

<br>
<br>

## 5. **iOS에서 Delegate 패턴은 무엇이며, 어떤 상황에서 사용되나요?**
    - Delegate 패턴과 Notification, KVO의 차이점은 무엇인가요?
    - 프로토콜을 활용한 Delegate 패턴 구현 방법을 설명해주세요.

<br>
<br>

## 6. **Swift의 기본 데이터 타입과 컬렉션(Collection) 타입에는 어떤 것들이 있나요?**
    - 값 타입(Value Type)과 참조 타입(Reference Type)의 차이점은 무엇인가요?
    - 구조체(Struct)와 클래스(Class)의 사용 시기는 어떻게 구분하나요?
    - 열거형(Enum)의 원시값(Raw Value)과 연관값(Associated Value)은 무엇인가요?

<br>
<br>

## 7. **Xcode에서 디버깅 시 자주 사용하는 기능은 무엇인가요?**
    - 중단점(Breakpoint)의 종류와 활용 방법을 설명해주세요.
    - LLDB 콘솔에서 유용한 명령어는 어떤 것이 있나요?

<br>
<br>

## 8. **iOS 앱에서 데이터를 저장하는 방법에는 어떤 것들이 있나요?**
    - `UserDefaults`의 사용 시 주의할 점은 무엇인가요?
    - Keychain은 어떤 데이터를 저장하기에 적합한가요?
    - Core Data와 SQLite의 차이점은 무엇이며, 각각 언제 사용하면 좋나요?

<br>
<br>

## 9. **Swift에서 프로토콜(Protocol)이란 무엇이며, 어떻게 활용하나요?**
    - 프로토콜의 요구사항은 무엇인가요?
    - 프로토콜 확장(Protocol Extension)을 사용하는 이유는 무엇인가요?
    - 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)의 장점은 무엇인가요?

<br>
<br>

## 10. **Swift의 접근 제어자(Access Control Levels)에 대해 설명해주세요.**
    - `open`과 `public`의 차이점은 무엇인가요?
    - `internal`, `fileprivate`, `private`의 사용 시기는 어떻게 결정하나요?
    - 접근 제어자를 사용하는 이유는 무엇인가요?

<br>
<br>

## 11. **iOS 앱에서 네트워크 통신을 하는 방법에는 어떤 것들이 있나요?**
    - `URLSession`의 기본 사용 방법을 설명해주세요.
    - 네트워크 요청 시 에러 처리는 어떻게 하나요?
    - 서드파티 라이브러리(예: Alamofire)를 사용하는 이유는 무엇인가요?

<br>
<br>

## 12. **의존성 관리 도구(CocoaPods, Carthage, Swift Package Manager)의 종류와 차이점은 무엇인가요?**
    - 각 도구의 사용 방법과 장단점을 설명해주세요.
    - 의존성 관리를 통해 얻을 수 있는 이점은 무엇인가요?

<br>
<br>

## 13. **Swift의 고차 함수(Higher-Order Functions)에 대해 설명해주세요.**
    - `map`과 `flatMap`의 차이점은 무엇인가요?
    - `filter`, `reduce` 함수는 어떤 경우에 사용하나요?
    - `compactMap`은 어떤 역할을 하나요?

<br>
<br>

## 14. **Git에서 브랜치(Branch)를 사용하는 이유와 장점은 무엇인가요?**
    - 브랜치를 병합(Merge)하는 방법에는 어떤 것들이 있나요?
    - 브랜치 전략(예: Git Flow, GitHub Flow)에 대해 설명해주세요.
    - 충돌(Conflict)이 발생했을 때 해결 방법은 무엇인가요?

<br>
<br>

## 15. **Swift의 에러 처리 방법에 대해 설명해주세요.**
    - `throws`, `try`, `catch` 키워드의 사용 방법은 무엇인가요?
    - 옵셔널을 사용한 에러 처리와 `do-catch`를 사용하는 에러 처리의 차이는 무엇인가요?
    - 에러를 전파하는 방법은 무엇인가요?

<br>
<br>

## 16. **메모리 관리에서 강한 참조(Strong Reference)와 약한 참조(Weak Reference)의 차이점은 무엇인가요?**
    - 순환 참조(Retain Cycle)가 발생하는 경우와 해결 방법은 무엇인가요?
    - 클로저에서 `[weak self]`와 `[unowned self]`의 차이는 무엇인가요?

<br>
<br>

## 17. **iOS 앱에서 Multi-threading을 구현하는 방법은 무엇인가요?**
    - `DispatchQueue`와 `OperationQueue`의 차이점은 무엇인가요?
    - 동시성 프로그래밍에서 Race Condition을 방지하는 방법은 무엇인가요?
    - 메인 스레드에서 UI 업데이트를 해야 하는 이유는 무엇인가요?

<br>
<br>

## 18. **UIKit에서 TableView와 CollectionView의 차이점은 무엇인가요?**
    - 셀(Cell)의 재사용(Reusability)은 어떻게 구현되나요?
    - 동적인 셀 높이(Dynamic Cell Height)를 설정하는 방법은 무엇인가요?
    - CollectionView의 레이아웃을 커스터마이징하는 방법은 무엇인가요?

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
