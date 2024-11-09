# 레벨 2

## 1. Swift의 동시성(Concurrency) 프로그래밍에 대해 설명해주세요.
- 동시성 프로그래밍은 여러 작업을 동시에 실행하여 애플리케이션 성능을 최적화하는 프로그래밍 기법입니다.
- Swift에서는 동시성 프로그래밍을 위해 GCD(Grand Central Dispatch), OperationQueue, Swift Concurrency(async/await) 등의 도구를 제공합니다.


<br>
<br>

## 1.1 Grand Central Dispatch(GCD)의 주요 개념과 사용 방법을 설명해주세요.
GCD는 Apple에서 제공하는 저수준 API로, 멀티스레드 작업을 간단히 처리할 수 있습니다. 큐(Queue) 기반으로 작업을 관리하며, 비동기적으로 작업을 실행합니다.

### GCD의 주요 개념
#### 1.	큐(Queue):
- 직렬 큐(Serial Queue): 하나의 작업이 완료된 후 다음 작업이 실행.
- 동시 큐(Concurrent Queue): 여러 작업이 동시에 실행될 수 있음.
#### 2.	글로벌 큐(Global Queue):
- 시스템에서 제공하는 동시 큐로, QoS(Quality of Service)에 따라 작업 우선순위를 설정 가능.
#### 3.	메인 큐(Main Queue):
- UI 작업을 처리하는 직렬 큐.
#### 4.	QoS(Quality of Service):
- 작업의 우선순위를 지정하여 시스템 리소스를 효율적으로 사용할 수 있도록 도움.

<br>

### GCD 사용 방법
#### 비동기 작업 실행 예시:

```swift
DispatchQueue.global(qos: .background).async {
    print("Background Task")
    DispatchQueue.main.async {
        print("UI Task on Main Thread")
    }
}
```

<br>

#### 동기 작업 실행 예시:

```swift
DispatchQueue.global().sync {
    print("Synchronous Task")
}
```

<br>

#### 직렬 큐 사용:

```swift
let serialQueue = DispatchQueue(label: "com.example.serialQueue")
serialQueue.async {
    print("Task 1")
}
serialQueue.async {
    print("Task 2")
}
```

<br>

#### 동시 큐 사용:

```swift
let concurrentQueue = DispatchQueue(label: "com.example.concurrentQueue", attributes: .concurrent)
concurrentQueue.async {
    print("Task 1")
}
concurrentQueue.async {
    print("Task 2")
}
```

<br>
<br>

## 1.2 OperationQueue와 DispatchQueue의 차이점은 무엇인가요?
<img src="https://github.com/user-attachments/assets/3f062e13-822e-4980-bd09-a711f506f56e">

#### OperationQueue 예시
```swift
let queue = OperationQueue()
queue.maxConcurrentOperationCount = 2

let operation1 = BlockOperation {
    print("Operation 1")
}
let operation2 = BlockOperation {
    print("Operation 2")
}
operation2.addDependency(operation1) // 작업 간 의존성 추가

queue.addOperations([operation1, operation2], waitUntilFinished: false)
```


<br>
<br>

## 1.3 동시성 프로그래밍에서 발생할 수 있는 문제(Race Condition, Deadlock 등)와 해결 방법은 무엇인가요?

### 1. Race Condition
- 문제:
- 두 개 이상의 스레드가 동시에 동일한 데이터에 접근 및 수정하려고 할 때 발생.
- 데이터 무결성이 깨질 수 있음.

#### 예시:
```swift
var counter = 0
DispatchQueue.concurrentPerform(iterations: 100) { _ in
    counter += 1 // Race Condition 발생
}
print(counter) // 예상값과 다를 수 있음
```

<br>

#### 해결방법:
- 동기화(Synchronization): DispatchSemaphore, NSLock 사용.

```swift
let lock = NSLock()
var counter = 0

DispatchQueue.concurrentPerform(iterations: 100) { _ in
    lock.lock()
    counter += 1
    lock.unlock()
}
print(counter) // 정확한 값
```

<br>

### 2. Deadlock
- 문제:
  - 두 스레드가 서로 다른 자원을 대기하면서 영원히 종료되지 않는 상태.

#### 예시:

```swift
let serialQueue = DispatchQueue(label: "com.example.serialQueue")
serialQueue.sync {
    serialQueue.sync { // Deadlock 발생
        print("Nested Sync")
    }
}
```

#### 해결방법:
- 중첩 동기 호출을 피함.
- 작업을 비동기로 실행:

```swift
let serialQueue = DispatchQueue(label: "com.example.serialQueue")
serialQueue.sync {
    serialQueue.async {
        print("Avoid Deadlock")
    }
}
```

<br>

### 3. Priority Inversion
- 문제:
  - 낮은 우선순위 작업이 높은 우선순위 작업보다 먼저 실행되어 리소스가 효율적으로 사용되지 않는 상태.
- 해결 방법:
  - 작업에 적절한 QoS 설정:
```swift
DispatchQueue.global(qos: .userInitiated).async {
    print("High Priority Task")
}
DispatchQueue.global(qos: .background).async {
    print("Low Priority Task")
}
```

<br>

### 요약
1. GCD는 경량 API로 간단한 비동기 작업에 적합.
2. OperationQueue는 작업 의존성 관리, 취소, 상태 추적이 필요한 경우에 적합.
3. 동시성 문제:
- Race Condition → Lock, Semaphore 사용.
- Deadlock → 중첩 동기 호출 피하기.
- Priority Inversion → QoS 설정으로 해결.

### 실무에서 사용 기준:
- 간단한 작업 → GCD
- 복잡한 작업 관리 → OperationQueue
- 최신 Swift → Swift Concurrency (async/await).



<br>
<br>

## 2. 객체지향 프로그래밍(OOP)의 주요 개념에 대해 설명해주세요.
객체지향 프로그래밍(OOP)은 **객체(Object)** 를 중심으로 설계 및 구현하는 프로그래밍 패러다임으로, 코드 재사용성, 유지보수성, 확장성을 높이기 위해 다음의 주요 개념을 사용합니다:

1.	캡슐화(Encapsulation)
2.	상속(Inheritance)
3.	다형성(Polymorphism)
4.	추상화(Abstraction)

<br>

### 1. 캡슐화 (Encapsulation)

캡슐화는 데이터를 보호하고 외부에서는 메서드를 통해서만 접근 가능하도록 만드는 개념입니다. 접근 제어자(private, internal, public 등)를 사용하여 구현됩니다.

```swift
class BankAccount {
    private var balance: Double = 0.0 // 외부에서 직접 접근 불가 (정보 은닉)
    
    func deposit(amount: Double) { // 캡슐화: 데이터 접근은 메서드로만 가능
        if amount > 0 {
            balance += amount
        }
    }
    
    func withdraw(amount: Double) -> Bool {
        if amount > 0 && amount <= balance {
            balance -= amount
            return true
        }
        return false
    }
    
    func getBalance() -> Double {
        return balance
    }
}

// 사용 예시
let account = BankAccount()
account.deposit(amount: 100)
print(account.getBalance()) // 출력: 100.0
account.withdraw(amount: 30)
print(account.getBalance()) // 출력: 70.0
```

<br>

### 2. 상속(Inheritance)
상속은 부모 클래스의 속성과 메서드를 자식 클래스가 물려받는 기능으로, 코드 재사용성과 확장성을 높입니다.

```swift
// 상위 클래스
class Animal {
    func makeSound() {
        print("Some generic sound")
    }
}

// 하위 클래스
class Dog: Animal {
    override func makeSound() { // 메서드 오버라이딩
        print("Bark!")
    }
}

class Cat: Animal {
    override func makeSound() { // 메서드 오버라이딩
        print("Meow!")
    }
}

// 사용 예시
let animal: Animal = Animal()
animal.makeSound() // 출력: Some generic sound

let dog: Animal = Dog()
dog.makeSound() // 출력: Bark!

let cat: Animal = Cat()
cat.makeSound() // 출력: Meow!
```

<br>

### 3. 다형성 (Polymorphism)
다형성은 동일한 인터페이스나 메서드가 다른 방식으로 동작하는 것을 의미합니다.
Swift에서는 메서드 오버라이딩 또는 프로토콜 준수를 통해 구현됩니다.

```swift
class Shape {
    func area() -> Double {
        return 0.0 // 기본 구현
    }
}

class Rectangle: Shape {
    var width: Double
    var height: Double
    
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
    
    override func area() -> Double {
        return width * height
    }
}

class Circle: Shape {
    var radius: Double
    
    init(radius: Double) {
        self.radius = radius
    }
    
    override func area() -> Double {
        return .pi * radius * radius
    }
}

// 다형성 활용
let shapes: [Shape] = [
    Rectangle(width: 5, height: 10),
    Circle(radius: 3)
]

for shape in shapes {
    print("Area: \(shape.area())") // 각 객체의 타입에 따라 적절한 area 메서드 호출
}
```

<br>

### 4. 추상화 (Abstraction)

추상화는 필요한 기능만 공개하고 세부 구현은 감추는 것을 의미합니다.
Swift에서는 프로토콜과 추상 클래스를 통해 구현됩니다.

```swift
protocol Drawable {
    func draw()
}

class Line: Drawable {
    func draw() {
        print("Drawing a line")
    }
}

class Circle: Drawable {
    func draw() {
        print("Drawing a circle")
    }
}

// 추상화 활용
let shapes: [Drawable] = [Line(), Circle()]
for shape in shapes {
    shape.draw() // 각 클래스의 구현에 따라 다르게 동작
}
```
<br>

### 요약 

<img src="https://github.com/user-attachments/assets/0030d1da-9f66-4772-bfa8-6f432d13a417">

## 2.1 캡슐화(Encapsulation)와 정보 은닉(Information Hiding)의 차이점은 무엇인가요?
- 캡슐화(Encapsulation): 데이터를 보호하고, 해당 데이터에 접근할 수 있는 인터페이스(메서드)를 제공하는 OOP의 원칙.
- 정보 은닉(Information Hiding): 클래스 외부에서 내부의 세부 구현을 알 필요가 없도록 숨기는 것을 의미.

이 두 가지는 서로 밀접하게 관련이 있으며, 캡슐화를 통해 정보 은닉을 실현할 수 있습니다.

### 1. 캡슐화: 데이터를 메서드로만 조작 가능

캡슐화는 데이터에 접근 가능한 방법을 제한하여 데이터 무결성을 보장하고, 잘못된 사용을 방지합니다.

#### 예시: 사용자 계정 관리

```swift
class UserAccount {
    private var password: String // 정보 은닉: 외부에서 직접 접근 불가
    
    init(password: String) {
        self.password = password
    }
    
    // 캡슐화: 데이터를 수정하거나 가져갈 때 반드시 메서드를 사용
    func changePassword(currentPassword: String, newPassword: String) -> Bool {
        guard currentPassword == password else {
            print("Current password is incorrect")
            return false
        }
        
        password = newPassword
        print("Password updated successfully")
        return true
    }
    
    func isPasswordValid(inputPassword: String) -> Bool {
        return inputPassword == password
    }
}

// 사용 예시
let userAccount = UserAccount(password: "secure123")

// 캡슐화를 통해 안전하게 데이터 조작
userAccount.changePassword(currentPassword: "secure123", newPassword: "newSecure456") // 성공
userAccount.changePassword(currentPassword: "wrongPassword", newPassword: "fail456") // 실패
```

<br>

### 2. 정보 은닉: 내부 구현 세부사항 숨기기
정보 은닉은 클래스 내부의 세부 사항(예: 데이터 저장 방식, 비즈니스 로직)을 외부에서 알지 못하도록 합니다.

#### 예시: 결제 처리
```swift
class PaymentProcessor {
    private var paymentHistory: [String] = [] // 정보 은닉: 외부에서 결제 이력을 알 수 없음
    
    func processPayment(amount: Double) -> Bool {
        guard amount > 0 else {
            print("Invalid payment amount")
            return false
        }
        
        // 정보 은닉: 결제 처리 로직은 외부에서 알 필요 없음
        let transactionID = UUID().uuidString
        paymentHistory.append(transactionID)
        print("Payment of \(amount) processed successfully. Transaction ID: \(transactionID)")
        return true
    }
    
    func refundPayment(transactionID: String) -> Bool {
        if let index = paymentHistory.firstIndex(of: transactionID) {
            paymentHistory.remove(at: index)
            print("Refund successful for transaction ID: \(transactionID)")
            return true
        } else {
            print("Transaction ID not found")
            return false
        }
    }
}

// 사용 예시
let processor = PaymentProcessor()
processor.processPayment(amount: 100.0)
processor.processPayment(amount: -10.0) // 실패
// 내부 결제 이력은 외부에서 직접 접근할 수 없음 (정보 은닉)
```

<br>


### 캡슐화와 정보 은닉의 차이 요약
<img src="https://github.com/user-attachments/assets/b50ae48a-4ff4-4366-8ba1-8faea884725f">

#### 결합 예시: 사용자 인증 시스템
캡슐화와 정보 은닉을 모두 활용

```swift
class AuthService {
    private var users: [String: String] = [:] // 정보 은닉: 유저 데이터 저장 방식 숨기기
    
    // 캡슐화: 외부에서 메서드를 통해서만 데이터를 조작
    func registerUser(username: String, password: String) -> Bool {
        guard users[username] == nil else {
            print("Username already exists")
            return false
        }
        users[username] = password
        print("User \(username) registered successfully")
        return true
    }
    
    func login(username: String, password: String) -> Bool {
        guard let storedPassword = users[username], storedPassword == password else {
            print("Invalid username or password")
            return false
        }
        print("User \(username) logged in successfully")
        return true
    }
}

// 사용 예시
let authService = AuthService()
authService.registerUser(username: "john_doe", password: "password123")
authService.login(username: "john_doe", password: "password123") // 성공
authService.login(username: "john_doe", password: "wrongpassword") // 실패
```

- 캡슐화: 메서드(registerUser, login)를 통해서만 데이터를 조작.
- 정보 은닉: 유저 데이터 저장 방식(users 딕셔너리)은 외부에서 접근 불가.
- 캡슐화를 하면 불필요한 정보를 감출 수 있기 때문에, 정보은닉을 할 수 있다는 특징을 가집니다.
- 고객이 식당에 와서 밥을 먹을 때 계란의 유통 구조와 삶는 법을 알 필요가 없을 것입니다.
- 고객 입장에서는 식당 이용 방법, 즉 public으로 정의된 속성만 알면 되는 것입니다.

<br>

### 캡슐화와 정보 은닉의 관계
1.	캡슐화(Encapsulation)
- 데이터와 그 데이터를 조작하는 메서드를 하나의 단위(캡슐)로 묶는 것을 의미합니다.
- 데이터를 보호하고 외부에서 접근할 수 없도록 하며, 필요한 경우 메서드를 통해 데이터를 다룰 수 있도록 합니다.
- 주안점: 클래스 설계와 데이터 접근 방식.
2.	정보 은닉(Information Hiding)
- 외부에서 알 필요가 없는 내부 구현을 숨기는 것을 의미합니다.
- 내부의 세부 구현(예: 데이터 저장 방식, 내부 변수 등)이 변경되더라도 외부 인터페이스(API)에 영향을 주지 않도록 설계합니다.
- 주안점: 인터페이스(API)와 내부 구현의 분리.

<br>
<br>

## 2.2 상속(Inheritance)의 장단점은 무엇인가요?

<br>
<br>

## 2.3 다형성(Polymorphism)을 활용하는 예시를 들어주세요.

<br>
<br>


## 3. 프로토콜 지향 프로그래밍(POP)이란 무엇이며, 어떤 장점이 있나요?
- 프로토콜 확장(Protocol Extension)을 사용하는 이유는 무엇인가요?
- 프로토콜 컴포지션(Protocol Composition)은 어떤 경우에 사용하나요?
- 프로토콜과 제네릭(Generic)을 함께 사용하면 어떤 이점이 있나요?

<br>
<br>

## 4. iOS 앱의 메모리 관리는 어떻게 이루어지나요?
- ARC(Automatic Reference Counting)의 동작 원리를 설명해주세요.
- 강한 참조(Strong Reference)와 약한 참조(Weak Reference)의 차이점은 무엇인가요?
- 순환 참조(Retain Cycle)가 발생하는 경우와 해결 방법을 설명해주세요.
- 강한 참조, 약한 참조, 미소유 참조의 차이점을 설명해주세요.

<br>
<br>

## 5. Swift의 문자열(String) 다루기와 관련된 주요 기능은 무엇이 있나요?
- 서브스트링(Substring)과 문자열의 차이점은 무엇인가요?
- 문자열 보간법(String Interpolation)을 사용하는 방법과 주의 사항을 설명해주세요.
- 정규식(Regular Expression)을 사용하여 문자열을 다루는 방법을 설명해주세요.

<br>
<br>

## 6. Codable 프로토콜은 무엇이며, 어떻게 사용하나요?
- Encodable과 Decodable 프로토콜의 역할은 무엇인가요?
- JSON 데이터를 커스텀 객체로 디코딩하는 방법을 설명해주세요.
- Codable 프로토콜을 채택한 타입에서 인코딩/디코딩 키를 커스터마이징하는 방법은 무엇인가요?

<br>
<br>

## 7. iOS 앱에서 의존성 주입(Dependency Injection)은 어떤 목적으로 사용되나요?
- 의존성 주입의 세 가지 유형(Initializer Injection, Property Injection, Method Injection)을 설명해주세요.
- 의존성 주입 컨테이너(Dependency Injection Container)란 무엇인가요?
- 의존성 주입을 사용함으로써 얻을 수 있는 이점은 무엇인가요?

<br>
<br>

## 8. 델리게이션 패턴(Delegation Pattern)과 클로저의 차이점은 무엇인가요?
- 델리게이션 패턴에서 메모리 누수가 발생할 수 있는 경우와 해결 방법을 설명해주세요.
- 클로저의 캡처 리스트(Capture List)는 어떤 역할을 하나요?
- 델리게이션 패턴과 클로저를 함께 사용하는 경우의 장단점은 무엇인가요?

<br>
<br>

## 9. UIKit에서 테이블 뷰(UITableView)와 컬렉션 뷰(UICollectionView)의 차이점은 무엇인가요?
- 테이블 뷰와 컬렉션 뷰에서 셀을 재사용하는 이유와 방법을 설명해주세요.
- 테이블 뷰와 컬렉션 뷰의 데이터 소스(Data Source)와 델리게이트(Delegate)의 역할은 무엇인가요?
- 컬렉션 뷰에서 사용할 수 있는 레이아웃(Layout)의 종류와 특징을 설명해주세요.

<br>
<br>

## 10. iOS 앱 아키텍처 패턴 중 MVC, MVVM, VIP, MVI의 차이점은 무엇인가요?
- MVC의 장점은 무엇인가요?
- 각 아키텍처 패턴의 구성 요소와 책임을 설명해주세요.
- MVVM 패턴에서 Binding은 어떤 역할을 하나요?
- VIP 패턴에서 Presenter의 역할은 무엇인가요?
- MVI 패턴에서 Intent의 역할은 무엇인가요?

<br>
<br>

## 11. Swift에서 옵셔널(Optional)을 사용할 때 주의할 점은 무엇인가요?
- 강제 언래핑(Force Unwrapping)을 사용하면 안 되는 이유는 무엇인가요?
- 옵셔널 바인딩(Optional Binding)과 옵셔널 체이닝(Optional Chaining)의 차이점을 설명해주세요.
- 암시적 언래핑 옵셔널(Implicitly Unwrapped Optional)은 어떤 경우에 사용하나요?

<br>
<br>

## 12. iOS 앱에서 코어 애니메이션(Core Animation)을 사용하는 방법은 무엇인가요?
- CALayer의 주요 속성과 메서드를 설명해주세요.
- 애니메이션 그룹(Animation Group)은 어떤 경우에 사용하나요?
- 키 프레임 애니메이션(Keyframe Animation)과 스프링 애니메이션(Spring Animation)의 차이점은 무엇인가요?

<br>
<br>

## 13. Swift에서 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)을 활용하는 방법은 무엇인가요?
- 프로토콜 확장(Protocol Extension)을 통해 기본 구현을 제공하는 방법을 설명해주세요.
- 프로토콜 상속(Protocol Inheritance)은 어떤 경우에 사용하나요?
- 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)에서 제네릭(Generic)을 함께 사용하면 어떤 이점이 있나요?

<br>
<br>

## 14. iOS 앱에서 네트워크 요청 시 응답 캐싱(Response Caching)을 하는 방법은 무엇인가요?
- URLCache는 어떤 역할을 하나요?
- 응답 캐싱의 장단점은 무엇인가요?
- 응답 캐싱을 커스터마이징하는 방법을 설명해주세요.

<br>
<br>

## 15. Combine 프레임워크란 무엇이며, 어떤 기능을 제공하나요?
- Publisher와 Subscriber의 역할은 무엇인가요?
- Operator의 종류와 사용 방법을 설명해주세요.
- Combine과 RxSwift의 차이점은 무엇인가요?

<br>
<br>

## 16. Swift의 제네릭(Generic)에 대해 설명해주세요.
- 제네릭을 사용하는 이유는 무엇인가요?
- 제네릭 타입 파라미터(Generic Type Parameter)와 제네릭 타입 제약(Generic Type Constraint)은 무엇인가요?
- 제네릭을 사용할 때 주의할 점은 무엇인가요?

<br>
<br>

## 17. iOS 앱에서 로컬 푸시 알림(Local Push Notification)을 구현하는 방법은 무엇인가요?
- 로컬 푸시 알림과 원격 푸시 알림(Remote Push Notification)의 차이점은 무엇인가요?
- 푸시 알림의 콘텐츠(Content)와 트리거(Trigger)는 어떤 역할을 하나요?
- 사용자가 푸시 알림을 탭했을 때 앱의 동작을 처리하는 방법을 설명해주세요.

<br>
<br>

## 18. iOS 앱에서 SwiftUI와 UIKit을 함께 사용하는 방법은 무엇인가요?
- SwiftUI 뷰에서 UIKit 뷰 컨트롤러를 사용하는 방법을 설명해주세요.
- UIKit 뷰 컨트롤러에서 SwiftUI 뷰를 호스팅하는 방법은 무엇인가요?
- SwiftUI와 UIKit을 함께 사용할 때 주의할 점은 무엇인가요?

<br>
<br>

## 19. Swift에서 키 경로(Key Path)란 무엇이며, 어떻게 사용하나요?
- 키 경로 표현식(Key Path Expression)의 문법과 사용 예시를 설명해주세요.
- 런타임에 키 경로를 사용하여 속성에 접근하는 방법은 무엇인가요?
- 키 경로와 KVO(Key-Value Observing)의 관계를 설명해주세요.

<br>
<br>

## 20. iOS 앱에서 Deep Link와 Universal Link의 차이점은 무엇인가요?
- Deep Link를 구현하는 방법과 주의 사항을 설명해주세요.
- Universal Link의 동작 원리와 설정 방법은 무엇인가요?
- Deep Link와 Universal Link를 함께 사용하는 경우의 장점은 무엇인가요?

<br>
<br>

## 21. Swift의 Result 타입과 에러 처리 방식에 대해 설명해주세요.
- Result 타입을 사용하는 이유와 장점은 무엇인가요?
- 에러 처리 시 do-catch 문과 Result 타입을 함께 사용하는 방법을 설명해주세요.

<br>
<br>

## 22. iOS 앱에서 Thread Sanitizer를 사용하여 동시성 문제를 탐지하고 해결하는 방법을 설명해주세요.

<br>
<br>

## 23. Swift의 Sequence와 Collection 프로토콜에 대해 설명해주세요.
- Sequence와 Collection 프로토콜의 차이점과 요구 사항을 설명해주세요.
- 사용자 정의 Sequence와 Collection을 구현하는 방법과 사용 예시를 들어주세요.

<br>
<br>

## 24. UIKit의 AdaptiveLayout과 Size Classes에 대해 설명해주세요.
- AdaptiveLayout의 개념과 사용 목적을 설명해주세요.
- Size Classes를 활용하여 다양한 기기에 적응적인 UI를 구현하는 방법을 예시와 함께 설명해주세요.

<br>
<br>

## 25. Swift의 커스텀 연산자(Custom Operator)에 대해 설명해주세요.
- 커스텀 연산자를 정의하는 방법과 주의 사항은 무엇인가요?
- 커스텀 연산자를 활용한 코드 가독성 향상 방안을 제시해주세요.

<br>
<br>

## 26. Swift의 생성자(Initializer)와 관련된 고급 개념에 대해 설명해주세요.
- 지정 생성자(Designated Initializer)와 편의 생성자(Convenience Initializer)의 차이점은 무엇인가요?
- 필수 생성자(Required Initializer)와 실패 가능한 생성자(Failable Initializer)는 어떤 경우에 사용하나요?


<br>
<br>

## 27. Combine 프레임워크에서 Scheduler의 역할과 종류에 대해 설명해주세요.
- Scheduler를 사용하여 작업을 특정 큐(DispatchQueue)에서 실행하는 방법을 설명해주세요.
- 백그라운드에서 작업을 수행하고 메인 큐에서 UI를 업데이트하는 패턴을 Combine으로 구현하는 방법을 설명해주세요.

<br>
<br>


## 28. UIKit의 `UIView`는 클래스 기반으로 구현되어 있지만, SwiftUI에서 `View` 프로토콜을 준수하는 타입은 보통 구조체를 사용합니다. 그 이유는 무엇일까요?
- `View` 프로토콜을 준수하는 구조체의 주요 특징은 무엇이며, 이는 어떻게 SwiftUI의 성능 및 사용성에 영향을 미치나요?
- SwiftUI의 `View`가 구조체임에도 불구하고, 상태(state)를 어떻게 관리하고 업데이트하나요?
- SwiftUI의 구조체 기반 `View` 생성과 업데이트 사이클은 어떻게 UIKit의 클래스 기반 `UIView`와 다른가요?


<br>
<br>
