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
상속은 **부모 클래스(슈퍼클래스)** 의 속성과 메서드를 **자식 클래스(서브클래스)** 가 물려받는 객체지향 프로그래밍(OOP)의 중요한 개념입니다. 이를 통해 코드 재사용성을 높이고 기능 확장을 용이하게 합니다.

### 장점

#### 1. 코드 재사용성 증가
- 부모 클래스에서 정의한 코드(속성 및 메서드)를 자식 클래스에서 그대로 사용 가능.
- 중복 코드를 줄이고 유지보수를 용이하게 만듦.

```swift
class Animal {
    func eat() {
        print("Eating food")
    }
}

class Dog: Animal {
    func bark() {
        print("Barking")
    }
}

// 사용 예시
let dog = Dog()
dog.eat() // 부모 클래스 메서드 호출
dog.bark() // 자식 클래스 메서드 호출
```

<br>

#### 2. 확장성
- 자식 클래스에서 부모 클래스의 기능을 확장하거나, 필요한 경우 메서드를 재정의(Overriding)하여 새로운 기능 추가 가능.

```swift
class Animal {
    func sound() {
        print("Some generic sound")
    }
}

class Cat: Animal {
    override func sound() {
        print("Meow") // 메서드 재정의
    }
}

// 사용 예시
let cat = Cat()
cat.sound() // 출력: Meow
```

<br>

#### 3. 다형성 활용 가능
- 부모 클래스의 참조 변수로 자식 클래스를 참조하여 다형성을 구현 가능.
- 다양한 객체를 동일한 방식으로 처리할 수 있음.

```swift
class Animal {
    func sound() {
        print("Some generic sound")
    }
}

class Dog: Animal {
    override func sound() {
        print("Bark")
    }
}

class Cat: Animal {
    override func sound() {
        print("Meow")
    }
}

// 사용 예시
let animals: [Animal] = [Dog(), Cat()]
for animal in animals {
    animal.sound() // 각각 Bark와 Meow 출력
}
```

<br>

### 단점

#### 1. 높은 결합도
- 자식 클래스가 부모 클래스에 의존적이므로, 부모 클래스를 수정하면 자식 클래스에도 영향을 미침.
- 클래스 간 결합도가 높아 유지보수가 어려워질 수 있음.

#### 예시:
- 모 클래스의 메서드를 수정하면, 이를 사용하는 자식 클래스에서 의도치 않은 버그가 발생할 가능성.

<br>

#### 2. 복잡성 증가
- 다단계 상속(상속 체계가 깊어질 경우)은 코드의 가독성을 저하시킬 수 있음.
- 클래스 구조를 이해하기 어려워지고 디버깅이 복잡해질 수 있음.

```swift
class A {
    func method() {
        print("Class A")
    }
}

class B: A {
    override func method() {
        print("Class B")
    }
}

class C: B {
    override func method() {
        print("Class C")
    }
}

// 사용 예시
let obj = C()
obj.method() // 상속 계층이 깊을수록 어떤 메서드가 호출되는지 추적이 어려움
```

<br>

#### 3. 다중 상속 지원 불가
- Swift는 다중 상속을 지원하지 않으므로, 클래스 간 복잡한 관계를 표현하기 어려움.
- Swift에서는 다중 프로토콜 채택으로 이를 해결.

#### 예시:
- 두 개 이상의 부모 클래스를 상속받으려는 경우 Swift에서 허용되지 않음.

```swift
// 다중 상속 불가
class Parent1 {}
class Parent2 {}
// class Child: Parent1, Parent2 {} // 오류 발생
```

<br>

### 정리
<img src="https://github.com/user-attachments/assets/e4549059-9e33-479f-ab97-d20d7e2cc0c2">

<br>

### 결론:

- 상속은 코드 재사용성과 확장성을 높이지만, 높은 결합도와 복잡성을 주의해야 합니다.
- 상속보다는 **구성(Composition)**과 프로토콜 채택을 고려하는 것이 더 유연한 설계가 될 수 있습니다.

<br>
<br>

## 2.3 다형성(Polymorphism)을 활용하는 예시를 들어주세요.

**다형성(Polymorphism)** 은 동일한 메서드나 인터페이스를 통해 여러 형태의 객체를 처리할 수 있는 객체지향 프로그래밍(OOP)의 핵심 개념입니다.
Swift에서는 메서드 오버라이딩과 프로토콜 준수를 통해 다형성을 구현합니다.

<br>

### 1. 메서드 오버라이딩을 사용한 다형성

#### 설명
부모 클래스의 메서드를 자식 클래스에서 재정의하여 각 객체의 동작을 다르게 정의할 수 있습니다.

```swift
class Animal {
    func sound() {
        print("Some generic sound")
    }
}

class Dog: Animal {
    override func sound() {
        print("Bark")
    }
}

class Cat: Animal {
    override func sound() {
        print("Meow")
    }
}

// 사용 예시
let animals: [Animal] = [Dog(), Cat(), Animal()]

for animal in animals {
    animal.sound() // 각각 Bark, Meow, Some generic sound 출력
}
```

<br>

### 2. 프로토콜 준수를 사용한 다형성

#### 설명
다양한 클래스가 동일한 프로토콜을 준수하고, 동일한 인터페이스를 통해 다르게 동작하도록 구현할 수 있습니다.

```swift
protocol Flyable {
    func fly()
}

class Bird: Flyable {
    func fly() {
        print("Bird is flying")
    }
}

class Airplane: Flyable {
    func fly() {
        print("Airplane is flying")
    }
}

// 사용 예시
let flyingObjects: [Flyable] = [Bird(), Airplane()]

for object in flyingObjects {
    object.fly() // 각각 Bird is flying, Airplane is flying 출력
}
```

<br>

### 3. 실무 예시: UIView의 다형성

#### 설명
iOS의 UIView와 그 하위 클래스들은 다형성을 활용하여 다양한 형태의 뷰를 처리할 수 있습니다.
UIView를 참조 타입으로 사용해 버튼, 레이블, 이미지 뷰 등 다양한 뷰를 공통적으로 처리 가능합니다.

```swift
import UIKit

class MyViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        let label = UILabel()
        label.text = "Hello"
        
        let button = UIButton()
        button.setTitle("Press me", for: .normal)
        
        let views: [UIView] = [label, button] // UIView를 참조 타입으로 사용
        
        for view in views {
            print(type(of: view)) // 각각 UILabel, UIButton 출력
        }
    }
}
```

### 4. 프로토콜을 활용한 다형성
#### 설명

다형성을 활용하면 다양한 클래스에 동일한 동작을 적용할 수 있습니다. 예를 들어, iOS에서 데이터 저장을 다형성을 통해 처리하는 예입니다.

```swift
protocol DataStorable {
    func save(data: String)
}

class DatabaseStorage: DataStorable {
    func save(data: String) {
        print("Saving \(data) to the database")
    }
}

class FileStorage: DataStorable {
    func save(data: String) {
        print("Saving \(data) to a file")
    }
}

// 사용 예시
let storages: [DataStorable] = [DatabaseStorage(), FileStorage()]

for storage in storages {
    storage.save(data: "Example Data")
    // 출력:
    // Saving Example Data to the database
    // Saving Example Data to a file
}
```

<br>

### 5. 다형성의 장점
1. 유연성: 다양한 객체를 공통된 방식으로 처리 가능.
2. 확장성: 새로운 객체를 추가해도 기존 코드를 수정하지 않음.
3. 재사용성: 동일한 인터페이스로 다양한 구현체를 사용할 수 있음.

<br>

### 요약

<img src = "https://github.com/user-attachments/assets/9bc1f74a-381d-4df6-9584-8a6ea1200c16">

다형성은 객체를 “is-a” 관계로 처리하는 핵심 개념으로, 실무에서도 코드의 재사용성과 확장성을 극대화하는 데 매우 유용하게 사용됩니다.

<br>
<br>

## 3. 프로토콜 지향 프로그래밍(POP)이란 무엇이며, 어떤 장점이 있나요?

### 정의
- 프로토콜 지향 프로그래밍(POP)은 프로토콜을 설계의 중심으로 사용하는 프로그래밍 패러다임입니다.
- Swift에서 객체지향 프로그래밍(OOP)의 단점을 보완하기 위해 도입된 방식으로, 클래스 외에도 구조체 및 열거형과 같은 **값 타입(Value Type)**에서도 재사용성과 유연성을 제공하는 것이 특징입니다.

<br>

### POP의 주요 특징
1. 프로토콜 중심 설계: 구현보다는 인터페이스(프로토콜)를 중심으로 설계.
2. 값 타입의 활용: Swift의 구조체 및 열거형과 같은 값 타입을 활용하여 메모리 관리 부담 감소.
3. 프로토콜 확장(Protocol Extension): 프로토콜에 기본 구현을 제공하여 코드 재사용성 증대.
4. 컴포지션 기반 설계: 다중 상속을 허용하지 않는 대신, 다중 프로토콜 채택으로 유연한 설계 가능.

<br>

### POP의 장점
1. 코드 재사용성 증대
- 프로토콜 확장을 통해 공통 기능의 기본 구현 제공.
- 여러 타입에서 동일한 기능을 재사용 가능.
2. 유연성과 확장성
- 다중 상속의 문제를 해결하며, 다중 프로토콜 채택으로 유연한 설계 가능.
- 클래스를 사용하지 않아도 구조체와 열거형에서 동일한 방식으로 동작 구현 가능.
3. 값 타입 지원
- 구조체 및 열거형과 같은 값 타입을 활용하여 안전한 메모리 관리와 성능 최적화 가능.
4. 테스트 용이성
- 의존성 주입과 같은 패턴과 결합하여 Mock 객체를 쉽게 생성 가능.

### POP의 활용 예시
#### 1. 기본 구현 제공
```swift
protocol Flyable {
    func fly()
}

extension Flyable {
    func fly() {
        print("Default flying behavior")
    }
}

struct Bird: Flyable {}
struct Airplane: Flyable {
    func fly() {
        print("Airplane flying differently")
    }
}

// 사용 예시
let bird = Bird()
bird.fly() // 출력: Default flying behavior

let airplane = Airplane()
airplane.fly() // 출력: Airplane flying differently
```

<br>

#### 2. 값 타입 활용

```swift
protocol Drawable {
    func draw()
}

struct Circle: Drawable {
    func draw() {
        print("Drawing a circle")
    }
}

struct Square: Drawable {
    func draw() {
        print("Drawing a square")
    }
}

// 값 타입 사용
let shapes: [Drawable] = [Circle(), Square()]
for shape in shapes {
    shape.draw()
    // 출력:
    // Drawing a circle
    // Drawing a square
}
```

<br>

#### 3. 프로토콜 컴포지션을 활용한 다중 기능

```swift
protocol Flyable {
    func fly()
}

protocol Swimable {
    func swim()
}

struct Duck: Flyable, Swimable {
    func fly() {
        print("Duck is flying")
    }
    
    func swim() {
        print("Duck is swimming")
    }
}

func performActions(entity: Flyable & Swimable) {
    entity.fly()
    entity.swim()
}

// 사용 예시
let duck = Duck()
performActions(entity: duck)
// 출력:
// Duck is flying
// Duck is swimming
```

<br>

#### 4. 프로토콜과 제네릭의 결합

```swift
protocol Shape {
    func area() -> Double
}

struct Rectangle: Shape {
    var width: Double
    var height: Double
    func area() -> Double {
        return width * height
    }
}

struct Circle: Shape {
    var radius: Double
    func area() -> Double {
        return .pi * radius * radius
    }
}

func printArea<T: Shape>(_ shape: T) {
    print("The area is \(shape.area())")
}

// 사용 예시
let rectangle = Rectangle(width: 10, height: 5)
let circle = Circle(radius: 3)

printArea(rectangle) // 출력: The area is 50.0
printArea(circle)    // 출력: The area is 28.27...
```

<br>

### POP VS OOP

<img src="https://github.com/user-attachments/assets/527c5fd7-4e99-409c-9d46-3a454b79ddfd">

<br>

### POP를 선택해야 하는 이유
- 유연성: 프로토콜을 중심으로 설계하면 다중 상속의 문제를 피하면서도 다양한 기능을 결합 가능.
- 안전성: 값 타입을 활용하여 메모리 관리가 용이하고, 데이터의 불변성을 유지하기 쉬움.
- 코드 재사용성: 기본 구현을 통해 공통 코드를 공유하고, 각 타입에서 필요한 부분만 재정의 가능.

<br>

### 결론:
- Swift의 프로토콜 지향 프로그래밍은 OOP의 단점을 보완하며, 특히 구조체와 프로토콜을 통해 값 타입 중심의 설계를 가능하게 합니다.
- POP는 유연성과 재사용성을 강조하는 현대적인 프로그래밍 패러다임으로, Swift의 강력한 기능 중 하나입니다.

<br>
<br>

## 3.1 프로토콜 확장(Protocol Extension)을 사용하는 이유는 무엇인가요?
**프로토콜 확장(Protocol Extension)** 은 프로토콜에 기본 구현을 추가하여, 프로토콜을 채택한 모든 타입에서 해당 구현을 공유하거나 재정의할 수 있도록 합니다. 이는 코드 재사용성을 높이고, 설계를 유연하게 만들어주는 중요한 기능입니다.

<br>

### 프로토콜 확장을 사용하는 이유
#### 1. 기본 구현 제공
- 프로토콜을 채택한 타입에서 공통적으로 사용할 수 있는 기본 동작을 정의.
- 채택한 타입에서 필요한 경우에만 재정의(Override) 가능.

<br>

#### 2. 코드 재사용성 증가
- 여러 타입에서 동일한 동작을 구현할 필요 없이, 프로토콜 확장에서 한 번만 구현하면 됨.
- 중복 코드를 줄여 유지보수성을 향상.

<br>

#### 3. 타입 확장 가능
- 프로토콜 확장을 통해 기존 타입에 새로운 동작을 추가하거나, 타입에 따라 동작을 구체화 가능.

<br>

#### 4. 유연한 설계
- 프로토콜 확장은 클래스, 구조체, 열거형에 모두 적용 가능.
- 상속 없이 기능을 확장할 수 있어 더 유연한 설계 가능.

### 예시 코드

#### 1. 기본 구현 제공


```swift
protocol Flyable {
    func fly()
}

extension Flyable {
    func fly() {
        print("Flying by default")
    }
}

struct Bird: Flyable {}
struct Airplane: Flyable {
    func fly() {
        print("Flying like an airplane")
    }
}

// 사용 예시
let bird = Bird()
bird.fly() // 출력: Flying by default

let airplane = Airplane()
airplane.fly() // 출력: Flying like an airplane
```

- 이점: Flyable 프로토콜을 채택한 타입에 기본 동작(Flying by default)을 제공.
Airplane는 기본 동작을 재정의하여 자신만의 동작을 구현.

<br>

#### 2. 코드 재사용성을 높이는 경우
```swift
protocol Identifiable {
    var id: String { get }
}

extension Identifiable {
    func identify() {
        print("My ID is \(id)")
    }
}

struct User: Identifiable {
    var id: String
}

struct Product: Identifiable {
    var id: String
}

// 사용 예시
let user = User(id: "user123")
user.identify() // 출력: My ID is user123

let product = Product(id: "product456")
product.identify() // 출력: My ID is product456
```

- 이점: identify 메서드는 Identifiable을 채택한 모든 타입에서 재사용 가능.
User와 Product가 별도로 동일한 메서드를 구현할 필요가 없음.

<br>

#### 3. 기존 타입 확장
```swift
protocol Summable {
    static func +(lhs: Self, rhs: Self) -> Self
}

extension Summable {
    static func sum(_ values: [Self]) -> Self {
        return values.reduce(0, +)
    }
}

// Int와 Double이 Summable 채택
extension Int: Summable {}
extension Double: Summable {}

// 사용 예시
let intSum = Int.sum([1, 2, 3, 4]) // 출력: 10
let doubleSum = Double.sum([1.5, 2.5, 3.5]) // 출력: 7.5
```

- 이점: Summable 프로토콜을 확장하여 모든 Summable 타입(Int, Double 등)에서 sum 메서드 재사용 가능.

<br>

#### 4. 타입별 구체화된 동작

```swift
protocol Drawable {
    func draw()
}

extension Drawable {
    func draw() {
        print("Default drawing")
    }
}

struct Circle: Drawable {}
struct Square: Drawable {
    func draw() {
        print("Drawing a square")
    }
}

// 사용 예시
let shapes: [Drawable] = [Circle(), Square()]
for shape in shapes {
    shape.draw()
    // Circle: Default drawing
    // Square: Drawing a square
}
```

- 이점: Circle은 기본 동작을 사용하고, Square는 자신만의 동작을 구현

<br>

### Protocol Extension과 Class Inheritance의 차이
<img src="https://github.com/user-attachments/assets/3cd1741c-769f-460a-afdb-38ddc412c391">

<br>

### 정리

- 프로토콜 확장은 코드 재사용성, 유연성, 타입 확장성을 제공하여 Swift의 강력한 기능 중 하나입니다.
- 기본 구현 제공을 통해 공통 동작을 한 곳에서 관리하고, 필요할 때만 재정의하여 사용 가능합니다.
- 객체지향 프로그래밍에서 상속의 단점을 보완하며, 구성(Composition) 기반의 설계를 가능하게 합니다.


<br>
<br>

## 3.2 프로토콜 컴포지션(Protocol Composition)은 어떤 경우에 사용하나요?

### 정의

프로토콜 컴포지션(Protocol Composition)은 여러 프로토콜을 결합하여, 특정 타입이 다수의 프로토콜을 준수하도록 요구하는 기능입니다. Swift에서 & 연산자를 사용하여 구현되며, 다중 상속의 복잡성을 피하면서도 유연하게 설계할 수 있는 방법을 제공합니다.

<br>

### 프로토콜 컴포지션이 사용되는 경우

#### 1. 타입이 여러 역할을 수행해야 할 때
- 객체가 다수의 프로토콜을 준수해야 하는 경우, 이를 컴포지션으로 결합하여 설계할 수 있습니다.
- 예: 날 수 있고, 수영할 수 있는 객체를 설계할 때 Flyable과 Swimmable 프로토콜을 조합.

#### 2. 특정 기능을 조합하여 동작을 제한하고 싶을 때
- 메서드의 파라미터나 반환 타입을 특정 프로토콜 조합으로 제한할 때 유용.

#### 3. 다중 상속을 대체하고 싶을 때
- Swift는 클래스의 다중 상속을 지원하지 않으므로, 프로토콜 컴포지션을 사용해 다중 상속과 유사한 동작을 구현.

#### 4. 유연하고 확장 가능한 설계를 구현할 때
- 객체 지향 설계에서 구성(Composition)을 중시하는 방식으로, 여러 기능을 필요에 따라 조합하여 활용 가능.

<br>

### 코드 예시
#### 1. 여러 역할을 수행하는 객체

```swift
protocol Flyable {
    func fly()
}

protocol Swimable {
    func swim()
}

struct Duck: Flyable, Swimable {
    func fly() {
        print("Duck is flying")
    }
    
    func swim() {
        print("Duck is swimming")
    }
}

// 프로토콜 컴포지션 사용
func performActions(entity: Flyable & Swimable) {
    entity.fly()
    entity.swim()
}

// 사용 예시
let duck = Duck()
performActions(entity: duck)
// 출력:
// Duck is flying
// Duck is swimming
```

#### 설명:
- Duck은 Flyable과 Swimable을 모두 준수.
- 함수 performActions는 Flyable & Swimable을 준수하는 객체만 허용.

<br>

#### 2. 특정 기능을 조합하여 동작 제한
```swift
protocol Drivable {
    func drive()
}

protocol Floatable {
    func float()
}

struct AmphibiousCar: Drivable, Floatable {
    func drive() {
        print("Driving on land")
    }
    
    func float() {
        print("Floating on water")
    }
}

// 특정 프로토콜 조합을 요구하는 함수
func operate(vehicle: Drivable & Floatable) {
    vehicle.drive()
    vehicle.float()
}

// 사용 예시
let car = AmphibiousCar()
operate(vehicle: car)
// 출력:
// Driving on land
// Floating on water
```

#### 설명:

- operate 함수는 Drivable와 Floatable을 모두 준수하는 객체만 허용.
- 프로토콜 컴포지션으로 특정 타입의 동작을 제한.

<br>

#### 3. 파라미터 타입에 대한 제약

```swift
protocol Identifiable {
    var id: String { get }
}

protocol Persistable {
    func save()
}

struct User: Identifiable, Persistable {
    var id: String
    func save() {
        print("Saving user with id \(id)")
    }
}

func saveEntity(entity: Identifiable & Persistable) {
    print("Saving entity with id \(entity.id)")
    entity.save()
}

// 사용 예시
let user = User(id: "user123")
saveEntity(entity: user)
// 출력:
// Saving entity with id user123
// Saving user with id user123
```

#### 설명:
- saveEntity 함수는 Identifiable과 Persistable을 모두 준수하는 객체만 처리.

<br>

#### 4. 반환 타입에 대한 제한
```swift
protocol Readable {
    func read() -> String
}

protocol Writeable {
    func write(data: String)
}

struct File: Readable, Writeable {
    func read() -> String {
        return "File content"
    }
    
    func write(data: String) {
        print("Writing data: \(data)")
    }
}

// 특정 반환 타입을 요구하는 함수
func openFile() -> Readable & Writeable {
    return File()
}

// 사용 예시
let file = openFile()
print(file.read()) // 출력: File content
file.write(data: "New data") // 출력: Writing data: New data
```

#### 설명:
- openFile 함수는 Readable과 Writeable을 모두 준수하는 객체를 반환.

<br>

### 프로토콜 컴포지션의 장점

#### 1.	유연성 증가
- 객체가 여러 역할을 수행하도록 설계 가능.
- 특정 프로토콜 조합만 허용하여 타입 안전성 확보.
#### 2.	다중 상속 대체
- 클래스의 단일 상속 제한을 극복하고, 다중 상속의 복잡성을 피하면서 기능 확장 가능.
#### 3.	구성 기반 설계
- 객체를 역할(프로토콜) 단위로 분리하여, 설계를 더 직관적이고 유지보수 가능하게 만듦.

<br>

### 실무 활용 예시: Delegate 패턴
iOS 개발에서 Delegate 패턴을 사용할 때 프로토콜 컴포지션을 활용할 수 있습니다.

```swift
protocol UITableViewDelegate: AnyObject {
    func didSelectRow(at indexPath: IndexPath)
}

protocol UITableViewDataSource: AnyObject {
    func numberOfRows(in section: Int) -> Int
    func cellForRow(at indexPath: IndexPath) -> UITableViewCell
}

class TableViewHandler: UITableViewDelegate & UITableViewDataSource {
    func didSelectRow(at indexPath: IndexPath) {
        print("Row selected at \(indexPath)")
    }
    
    func numberOfRows(in section: Int) -> Int {
        return 10
    }
    
    func cellForRow(at indexPath: IndexPath) -> UITableViewCell {
        return UITableViewCell()
    }
}
```


#### 설명:

- TableViewHandler는 UITableViewDelegate와 UITableViewDataSource를 모두 준수.
- 이를 통해 하나의 객체에서 Delegate와 DataSource 역할을 모두 처리 가능.

<br>

### 정리

<img src="https://github.com/user-attachments/assets/9d3d4be0-c32d-4ebe-8e0f-604389d772c6">

프로토콜 컴포지션은 타입을 더 명확히 정의하고, 역할 기반의 설계를 가능하게 하여 코드의 유지보수성과 유연성을 높이는 데 기여합니다.

<br>
<br>

## 3.4 프로토콜과 제네릭(Generic)을 함께 사용하면 어떤 이점이 있나요?
프로토콜과 제네릭을 결합하면 특정 프로토콜을 준수하는 타입만을 제네릭 타입이나 함수에서 허용할 수 있습니다. 이를 통해 코드의 유연성과 재사용성을 극대화하면서도 타입 안전성을 유지할 수 있습니다.

<br>

### 이점

#### 1. 타입 안전성

- 특정 프로토콜을 준수하는 타입만 허용하여, 런타임 오류를 방지하고 컴파일 타임에 타입 검사를 수행.

#### 2. 재사용성

- 동일한 로직을 여러 타입에 대해 적용 가능하며, 중복 코드 작성 없이 다양한 타입을 처리 가능.

#### 3. 유연성

- 클래스, 구조체, 열거형 모두 프로토콜을 채택할 수 있어, 특정 동작이 필요한 모든 타입을 처리 가능.

#### 4. 가독성과 유지보수성 향상

- 제네릭과 프로토콜을 조합하여 더 명확한 타입 제약 조건을 제공하고, 읽기 쉽고 관리하기 쉬운 코드를 작성.

<br>

### 코드 예시
#### 1. 특정 프로토콜을 준수하는 타입만 허용

```swift
protocol Shape {
    func area() -> Double
}

struct Rectangle: Shape {
    var width: Double
    var height: Double
    func area() -> Double {
        return width * height
    }
}

struct Circle: Shape {
    var radius: Double
    func area() -> Double {
        return .pi * radius * radius
    }
}

func printArea<T: Shape>(_ shape: T) {
    print("The area is \(shape.area())")
}

// 사용 예시
let rectangle = Rectangle(width: 10, height: 5)
let circle = Circle(radius: 3)

printArea(rectangle) // 출력: The area is 50.0
printArea(circle)    // 출력: The area is 28.274333882308138
```

- 이점: Shape를 준수하는 타입만 제네릭 함수에서 허용하여, 타입 안전성과 유연성을 확보.

<br>

#### 2. 제약 조건 추가하기

```swift
protocol ComparableShape: Shape {
    func isLarger(than other: Self) -> Bool
}

struct Square: ComparableShape {
    var side: Double
    func area() -> Double {
        return side * side
    }
    func isLarger(than other: Square) -> Bool {
        return self.area() > other.area()
    }
}

func compareShapes<T: ComparableShape>(_ first: T, _ second: T) {
    if first.isLarger(than: second) {
        print("First shape is larger")
    } else {
        print("Second shape is larger")
    }
}

// 사용 예시
let square1 = Square(side: 5)
let square2 = Square(side: 3)

compareShapes(square1, square2) // 출력: First shape is larger
```

- 이점: ComparableShape 프로토콜을 준수하는 타입만을 비교하도록 제한하여 타입 안정성을 유지.


<br>

#### 3. 제네릭 타입과 프로토콜 결합

```swift
protocol Storable {
    var id: String { get }
}

struct User: Storable {
    var id: String
    var name: String
}

struct Product: Storable {
    var id: String
    var price: Double
}

struct Storage<T: Storable> {
    private var items: [T] = []
    
    mutating func addItem(_ item: T) {
        items.append(item)
    }
    
    func getItem(by id: String) -> T? {
        return items.first { $0.id == id }
    }
}

// 사용 예시
var userStorage = Storage<User>()
userStorage.addItem(User(id: "1", name: "Alice"))

if let user = userStorage.getItem(by: "1") {
    print("Found user: \(user.name)") // 출력: Found user: Alice
}

var productStorage = Storage<Product>()
productStorage.addItem(Product(id: "101", price: 29.99))

if let product = productStorage.getItem(by: "101") {
    print("Found product with price: \(product.price)") // 출력: Found product with price: 29.99
}
```

- 이점: Storable 프로토콜을 준수하는 타입만 Storage에 저장 가능, 유연하면서도 안전한 데이터 저장소를 구현.

<br>

#### 4. 제네릭 메서드와 프로토콜의 결합

```swift
protocol Flyable {
    func fly()
}

struct Bird: Flyable {
    func fly() {
        print("Bird is flying")
    }
}

struct Airplane: Flyable {
    func fly() {
        print("Airplane is flying")
    }
}

func performFlying<T: Flyable>(_ entity: T) {
    entity.fly()
}

// 사용 예시
let bird = Bird()
let airplane = Airplane()

performFlying(bird)      // 출력: Bird is flying
performFlying(airplane)  // 출력: Airplane is flying
```

- 이점: Flyable 프로토콜을 준수하는 모든 타입에 대해 유연한 메서드 호출 가능.

<br>

### 제네릭과 프로토콜 결합의 장점
<img src="https://github.com/user-attachments/assets/b4e34926-e60b-4de2-aaca-10ed9dc60c48">

<br>

### 실무 활용 예시
#### 1. 네트워크 요청과 디코딩

```swift
import Foundation

protocol DecodableModel: Decodable {}

struct User: DecodableModel {
    let id: Int
    let name: String
}

struct Product: DecodableModel {
    let id: Int
    let price: Double
}

func fetch<T: DecodableModel>(from url: String, as type: T.Type, completion: @escaping (T?) -> Void) {
    guard let url = URL(string: url) else {
        completion(nil)
        return
    }
    
    URLSession.shared.dataTask(with: url) { data, _, _ in
        guard let data = data else {
            completion(nil)
            return
        }
        
        let decoded = try? JSONDecoder().decode(T.self, from: data)
        completion(decoded)
    }.resume()
}

// 사용 예시
fetch(from: "https://example.com/user", as: User.self) { user in
    if let user = user {
        print("Fetched user: \(user.name)")
    }
}
```

- 이점: DecodableModel을 준수하는 모델만 허용하여 타입 안전한 네트워크 요청 처리 가능.

<br>

### 정리

#### 프로토콜과 제네릭 결합의 핵심 이점:

1. 타입 안정성과 유연성: 특정 타입 제약을 통해 런타임 오류를 방지.
2. 재사용성 증가: 공통 로직을 여러 타입에서 쉽게 재사용 가능.
3. 확장성: 구조체, 열거형, 클래스 모두 지원.
4. 가독성: 제약 조건을 명확히 하여 코드의 의도를 분명히 표현.

실무에서는 네트워크 요청 처리, 데이터 저장소 설계, UI 컴포넌트 재사용 등 다양한 시나리오에서 활용됩니다.

<br>
<br>

## 3.3 Swift에서 is와 as - 타입 캐스팅 (Type Casting)
**타입 캐스팅(Type Casting)**은 객체의 타입을 확인하거나 변환하는 방법입니다. Swift는 타입 캐스팅을 위해 is와 as 키워드를 제공합니다.

<br>

### 1. is 키워드
- 역할: 객체가 특정 타입인지 확인할 때 사용.
- 리턴 값: Boolean (true 또는 false).

#### 사용 예시

```swift
class Animal {}
class Dog: Animal {}
class Cat: Animal {}

let myPet: Animal = Dog()

// 타입 확인
if myPet is Dog {
    print("This is a Dog")
} else if myPet is Cat {
    print("This is a Cat")
} else {
    print("Unknown animal")
}

// 출력: This is a Dog
```

#### 설명:
- is는 객체가 특정 타입(Dog)인지 검사.
- 결과는 true 또는 false로 반환.

<br>

### 2. as 키워드
- 역할: 객체를 특정 타입으로 변환할 때 사용.
- 종류:
1.	as? (조건부 캐스팅, Optional):
    - 캐스팅이 성공하면 옵셔널 값 반환, 실패하면 nil 반환.
2.	as! (강제 캐스팅, Forced):
	- 캐스팅이 실패하면 런타임 에러 발생.

<br>

#### 2.1 as? (조건부 캐스팅)

```swift
let myPet: Animal = Dog()

// 조건부 캐스팅
if let dog = myPet as? Dog {
    print("This is a Dog")
} else {
    print("This is not a Dog")
}

// 출력: This is a Dog
```
#### 설명:
- as?는 캐스팅이 실패하면 nil을 반환하므로 안전하게 사용 가능.
- 옵셔널 바인딩(if let)과 함께 사용하여 캐스팅 여부를 확인.

<br>

#### 2.2 as! (강제 캐스팅)

```swift
let myPet: Animal = Dog()

// 강제 캐스팅
let dog = myPet as! Dog
print("This is definitely a Dog")

// 출력: This is definitely a Dog
```

#### 설명:
- 캐스팅이 실패하면 런타임 에러 발생.
- 타입이 확실하다고 판단되는 경우에만 사용.

<br>

#### 2.3 업캐스팅 (Upcasting)
- 역할: 서브클래스를 부모 클래스 또는 프로토콜 타입으로 캐스팅.
- 자동 수행: 명시적인 as 키워드 필요 없음.

```swift
class Animal {}
class Dog: Animal {}

let dog = Dog()
let animal: Animal = dog // 업캐스팅 (명시적 `as` 생략 가능)

print("Upcasted to Animal")
```

- 설명:
    - 업캐스팅은 항상 안전하므로 명시적인 as 키워드가 필요하지 않음.
 
<br>

#### 2.4 다운캐스팅 (Downcasting)
- 역할: 부모 클래스를 서브클래스로 캐스팅.
- 수동 수행: 반드시 as? 또는 as! 키워드 필요.

```swift
let animal: Animal = Dog()

// 조건부 다운캐스팅
if let dog = animal as? Dog {
    print("This is a Dog")
} else {
    print("This is not a Dog")
}

// 강제 다운캐스팅
let dog = animal as! Dog
print("This is definitely a Dog")
```

<br>

### 3. 사용 사례

#### 3.1 프로토콜 타입 확인

```swift
protocol Pet {
    var name: String { get }
}

class Dog: Pet {
    var name: String
    init(name: String) { self.name = name }
}

class Cat: Pet {
    var name: String
    init(name: String) { self.name = name }
}

let pets: [Pet] = [Dog(name: "Buddy"), Cat(name: "Mittens")]

for pet in pets {
    if let dog = pet as? Dog {
        print("Dog's name is \(dog.name)")
    } else if let cat = pet as? Cat {
        print("Cat's name is \(cat.name)")
    }
}

// 출력:
// Dog's name is Buddy
// Cat's name is Mittens
```

<br>

#### 3.2 프로토콜 준수 여부 확인

```swift
protocol Flyable {
    func fly()
}

class Bird: Flyable {
    func fly() {
        print("Bird is flying")
    }
}

class Fish {}

let bird = Bird()
let fish = Fish()

print(bird is Flyable) // 출력: true
print(fish is Flyable) // 출력: false
```

<br>

#### 3.3 제네릭과 함께 사용

```swift
protocol Shape {
    func area() -> Double
}

class Circle: Shape {
    var radius: Double
    init(radius: Double) { self.radius = radius }
    func area() -> Double {
        return .pi * radius * radius
    }
}

class Rectangle: Shape {
    var width: Double
    var height: Double
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
    func area() -> Double {
        return width * height
    }
}

func printShapeArea(_ shape: Shape) {
    if let circle = shape as? Circle {
        print("Circle area: \(circle.area())")
    } else if let rectangle = shape as? Rectangle {
        print("Rectangle area: \(rectangle.area())")
    }
}

let circle = Circle(radius: 5)
let rectangle = Rectangle(width: 10, height: 5)

printShapeArea(circle)    // 출력: Circle area: 78.53981633974483
printShapeArea(rectangle) // 출력: Rectangle area: 50.0
```

<br>

### 주의 사항

#### 1.	as! 강제 캐스팅은 신중하게 사용:
- 실패 시 앱이 크래시하므로, 반드시 타입이 확실할 때만 사용.
- 가능하면 as?와 옵셔널 바인딩(if let)을 사용.
#### 2.	타입 확인은 런타임 비용이 발생:
- is와 as?는 런타임에 타입을 확인하므로, 빈번한 타입 캐스팅은 성능에 영향을 줄 수 있음.
#### 3.	업캐스팅은 필요하면 사용:
- 대부분의 경우 자동으로 수행되므로 명시적으로 작성하지 않아도 됨.

<br>

### 요약

<img src="https://github.com/user-attachments/assets/c3febb07-94a8-419b-b429-f36b1eb74db5">

is와 as는 타입 검사와 타입 변환을 위해 필수적으로 사용되며, Swift의 타입 시스템에서 강력한 도구로 활용됩니다.

<br>
<br>

## 4. iOS 앱의 메모리 관리는 어떻게 이루어지나요?
iOS는 앱의 메모리를 관리하기 위해 **ARC(Automatic Reference Counting)** 를 사용합니다. ARC는 객체의 **참조 카운트(Reference Count)** 를 추적하여, 더 이상 사용되지 않는 객체를 자동으로 메모리에서 해제합니다. 아래는 ARC의 동작 원리와 메모리 관리를 효율적으로 하기 위한 다양한 참조 방식에 대한 설명입니다.

<br>
<br>

## 4.1 ARC(Automatic Reference Counting)의 동작 원리를 설명해주세요.
1. 객체가 생성되면 참조 카운트가 1로 설정됩니다.
2. 새로운 변수가 해당 객체를 참조하면 참조 카운트가 증가합니다.
3. 참조가 제거되면 참조 카운트가 감소합니다.
4. 참조 카운트가 0이 되면 객체는 메모리에서 해제됩니다.

#### 코드 예시

```swift
class Person {
    let name: String
    
    init(name: String) {
        self.name = name
        print("\(name) is initialized")
    }
    
    deinit {
        print("\(name) is deinitialized")
    }
}

// 객체 생성 및 참조
var person1: Person? = Person(name: "Alice") // 참조 카운트: 1
var person2: Person? = person1               // 참조 카운트: 2

person1 = nil                                // 참조 카운트: 1
person2 = nil                                // 참조 카운트: 0, 메모리 해제
```

#### 출력

```
Alice is initialized
Alice is deinitialized
```

<br>
<br>

## 4.2 강한 참조(Strong Reference)와 약한 참조(Weak Reference)의 차이점은 무엇인가요?

<img src="https://github.com/user-attachments/assets/8a893512-84d6-42f2-8d76-c5e7c9a0b19a">

#### 코드 예시

```swift
class Person {
    var pet: Pet?
}

class Pet {
    weak var owner: Person? // 약한 참조
}

let john = Person()
let fido = Pet()

john.pet = fido
fido.owner = john // 약한 참조로 인해 순환 참조 방지
```


<br>
<br>

## 4.3 순환 참조(Retain Cycle)가 발생하는 경우와 해결 방법을 설명해주세요.

### 순환 참조 발생

두 객체가 서로를 강한 참조로 소유할 경우, 참조 카운트가 0이 되지 않아 메모리에서 해제되지 않는 문제가 발생합니다.

#### 코드 예시: 순환 참조 발생

```swift
class Person {
    var name: String
    var pet: Pet?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) is deinitialized")
    }
}

class Pet {
    var owner: Person? // 강한 참조로 인한 순환 참조 발생
    deinit {
        print("Pet is deinitialized")
    }
}

var john: Person? = Person(name: "John")
var fido: Pet? = Pet()

john?.pet = fido
fido?.owner = john

john = nil
fido = nil // 순환 참조로 인해 객체가 해제되지 않음
```

#### 출력

```
(출력 없음, 메모리 누수 발생)
```

#### 해결 방법: 약한 참조(Weak Reference) 사용

```swift
class Pet {
    weak var owner: Person? // 약한 참조로 변경
}
```

#### 출력 (수정 후)

```
John is deinitialized
Pet is deinitialized
```


<br>
<br>

## 4.4 강한 참조, 약한 참조, 미소유 참조의 차이점을 설명해주세요.

<img src="https://github.com/user-attachments/assets/d893bb1a-f23c-4650-951e-12d563c072de">

#### 미소유 참조 사용 예시

```swift
class Customer {
    var card: CreditCard?
    deinit {
        print("Customer is deinitialized")
    }
}

class CreditCard {
    unowned let owner: Customer // 미소유 참조
    init(owner: Customer) {
        self.owner = owner
    }
    deinit {
        print("CreditCard is deinitialized")
    }
}

// 사용 예시
var customer: Customer? = Customer()
customer?.card = CreditCard(owner: customer!)
customer = nil // 순환 참조 없이 메모리 해제
```

#### 출력

```
CreditCard is deinitialized
Customer is deinitialized
```

<br>

### 요약

<img src="https://github.com/user-attachments/assets/54bc5a24-18f2-4510-8d0b-719ccd01a4cf">

### 실무 활용

#### 1.	강한 참조
- 일반적인 객체 소유권 관리에 사용.
- 예: 뷰 컨트롤러와 모델 객체 간의 관계.
#### 2.	약한 참조
- Delegate 패턴 사용 시 순환 참조 방지.
- 예: tableView.delegate.
#### 3.	미소유 참조
- 항상 존재하는 객체 간의 관계에서 사용.
- 예: 부모-자식 관계 (예: UIView와 UIViewController).
#### 4.	순환 참조 방지
- 클로저에서 [weak self] 또는 [unowned self] 사용.
- 두 객체가 서로 강한 참조를 가지지 않도록 설계.

ARC는 Swift의 강력한 메모리 관리 도구지만, 참조 타입 설계와 순환 참조 방지에 대한 이해가 필수적입니다.


<br>
<br>

## 5. Swift의 문자열(String) 다루기와 관련된 주요 기능은 무엇이 있나요?
- Swift의 문자열은 강력하고 효율적인 API를 제공합니다.
- 문자열 처리에 있어 서브스트링, 문자열 보간법, 정규식과 같은 기능은 실무에서 자주 사용됩니다. 아래 각 기능의 특징과 사용법을 설명합니다.


<br>
<br>

## 5.1 서브스트링(Substring)과 문자열의 차이점은 무엇인가요?
### 차이점

#### 1.	메모리 공유:
- Substring은 원래 문자열의 메모리를 공유합니다. 새로운 메모리를 할당하지 않으므로 효율적입니다.
- 하지만 오랜 기간 사용해야 할 경우 Substring을 String으로 변환해야 합니다.

#### 2.	사용 목적:
- Substring은 문자열의 특정 부분을 임시로 다룰 때 사용합니다.
- String은 독립적으로 저장하고 사용할 때 사용합니다.

#### 코드 예시
```swift
let fullString = "Hello, World!"
let substring = fullString.prefix(5) // "Hello"

// Substring 사용
print(substring) // 출력: Hello

// Substring을 String으로 변환
let newString = String(substring)
print(newString) // 출력: Hello
```

<br>

### 주의 사항
- Substring을 장기간 유지하면 원래 문자열의 메모리를 계속 점유하게 됩니다. 이 경우 **String(substring)** 으로 변환하여 독립적인 메모리 공간을 할당해야 합니다.

<br>
<br>

## 5.2 문자열 보간법(String Interpolation)을 사용하는 방법과 주의 사항을 설명해주세요.
### 정의
- 문자열 보간법은 문자열 안에 변수 또는 표현식의 값을 삽입하는 방식입니다.
- Swift는 \(expression) 구문을 통해 값을 문자열에 포함할 수 있습니다.

#### 코드 예시

```swift
let name = "Alice"
let age = 25

let message = "My name is \(name) and I am \(age) years old."
print(message) // 출력: My name is Alice and I am 25 years old.

// 계산식도 삽입 가능
let sumMessage = "The sum of 3 and 5 is \(3 + 5)."
print(sumMessage) // 출력: The sum of 3 and 5 is 8.
```

### 주의 사항
1. 복잡한 표현식:
	- 복잡한 계산이나 조건문을 보간법에 직접 사용하지 말고, 가독성을 위해 별도 변수에 저장 후 삽입합니다.
2. 성능:
	- 보간법은 효율적이지만, 과도하게 사용하면 문자열 연산이 복잡해질 수 있습니다.

<br>
<br>

## 5.3 정규식(Regular Expression)을 사용하여 문자열을 다루는 방법을 설명해주세요.

[정규표현식 (Regex) 정리](https://hamait.tistory.com/342)

### 정의
- 정규식은 패턴 매칭을 통해 문자열 검색, 수정, 검증 등의 작업을 수행하는 데 사용됩니다.
- Swift는 iOS 16 이상에서 Regex API를 제공합니다.
- 이전 버전에서는 NSRegularExpression을 사용합니다.

#### Swift의 Regex API (iOS 16 이상)
```swift
let text = "apple, banana, cherry"

// 정규식 패턴 정의
let pattern = #"\b\w{6}\b"#

// 정규식 매칭
if let match = text.firstMatch(of: try! Regex(pattern)) {
    print("Matched word: \(match.0)") // 출력: banana
}
```

#### NSRegularExpression (iOS 16 미만)

```swift
import Foundation

let text = "apple, banana, cherry"

// 정규식 생성
let pattern = "\\b\\w{6}\\b" // 6글자로 이루어진 단어
let regex = try! NSRegularExpression(pattern: pattern)

// 정규식 매칭
let range = NSRange(location: 0, length: text.utf16.count)
if let match = regex.firstMatch(in: text, options: [], range: range) {
    let matchRange = match.range
    let startIndex = text.index(text.startIndex, offsetBy: matchRange.location)
    let endIndex = text.index(startIndex, offsetBy: matchRange.length)
    let matchedString = String(text[startIndex..<endIndex])
    print("Matched word: \(matchedString)") // 출력: banana
}
```

<br>

### 정규식 활용 예시

#### 1.	이메일 검증:

```swift
let email = "example@test.com"
let emailPattern = #"^[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$"#

if email.range(of: emailPattern, options: .regularExpression) != nil {
    print("Valid email")
} else {
    print("Invalid email")
}
```

<br>

#### 2.	특정 문자 치환:

```swift
var text = "Hello 12345 World"
text = text.replacingOccurrences(of: "\\d", with: "#", options: .regularExpression)
print(text) // 출력: Hello ##### World
```

<br>

### 요약
<img src="https://github.com/user-attachments/assets/bae81070-9e58-46f0-9438-d043441d46dc">

Swift의 문자열 처리 기능은 강력하고 효율적입니다. 적절한 기능을 활용하여 복잡한 문자열 작업도 간결하게 처리할 수 있습니다


<br>
<br>

## 6. Codable 프로토콜은 무엇이며, 어떻게 사용하나요?
Codable은 Swift의 프로토콜로, Encodable과 Decodable을 모두 포함하는 프로토콜입니다. 이를 통해 객체를 쉽게 JSON 같은 데이터 포맷으로 인코딩(Encoding) 및 디코딩(Decoding) 할 수 있습니다.

<br>
<br>

## 6.1 Encodable과 Decodable 프로토콜의 역할은 무엇인가요?

### 1.	Encodable:
- 객체를 JSON, Property List 등으로 **변환(인코딩)** 하기 위해 사용.
- JSONEncoder 같은 인코더가 객체를 직렬화(serialization)할 때 사용됩니다.

### 2.	Decodable:
- JSON, Property List 등을 객체로 **복원(디코딩)** 하기 위해 사용.
- JSONDecoder 같은 디코더가 데이터를 객체로 역직렬화(deserialization)할 때 사용됩니다.



<br>
<br>

## 6.2 JSON 데이터를 커스텀 객체로 디코딩하는 방법을 설명해주세요.

#### JSON 데이터 예제

```json
{
  "name": "Alice",
  "age": 25,
  "isDeveloper": true
}
```

<br>

#### 모델 정의

```swift
struct User: Codable {
    let name: String
    let age: Int
    let isDeveloper: Bool
}
```

<br>

#### 디코딩 예시

```swift
let jsonData = """
{
    "name": "Alice",
    "age": 25,
    "isDeveloper": true
}
""".data(using: .utf8)!

do {
    let user = try JSONDecoder().decode(User.self, from: jsonData)
    print("Name: \(user.name), Age: \(user.age), Is Developer: \(user.isDeveloper)")
} catch {
    print("Decoding failed: \(error)")
}
```

<br>

#### 출력

```
Name: Alice, Age: 25, Is Developer: true
```


<br>
<br>

## 6.3 Codable 프로토콜을 채택한 타입에서 인코딩/디코딩 키를 커스터마이징하는 방법은 무엇인가요?
#### JSON 데이터
```json
{
  "user_name": "Alice",
  "user_age": 25,
  "is_developer": true
}
```

<br>

#### 모델 정의
- JSON 키와 Swift 변수명이 다를 경우, CodingKeys 열거형을 사용하여 매핑합니다.

```swift
struct User: Codable {
    let name: String
    let age: Int
    let isDeveloper: Bool

    enum CodingKeys: String, CodingKey {
        case name = "user_name"
        case age = "user_age"
        case isDeveloper = "is_developer"
    }
}
```

<br>

#### 디코딩 예시

```swift
let jsonData = """
{
    "user_name": "Alice",
    "user_age": 25,
    "is_developer": true
}
""".data(using: .utf8)!

do {
    let user = try JSONDecoder().decode(User.self, from: jsonData)
    print("Name: \(user.name), Age: \(user.age), Is Developer: \(user.isDeveloper)")
} catch {
    print("Decoding failed: \(error)")
}
```

<br>

#### 출력

```
Name: Alice, Age: 25, Is Developer: true
```

<br>
<br>

## 6.4 인코딩(Encoding) 예시

#### 모델 정의

```swift
struct User: Codable {
    let name: String
    let age: Int
    let isDeveloper: Bool
}
```

<br>

#### JSON 데이터 생성

```swift
let user = User(name: "Alice", age: 25, isDeveloper: true)

do {
    let jsonData = try JSONEncoder().encode(user)
    if let jsonString = String(data: jsonData, encoding: .utf8) {
        print(jsonString)
    }
} catch {
    print("Encoding failed: \(error)")
}
```

<br>

#### 출력

```
{"name":"Alice","age":25,"isDeveloper":true}
```

<br>

## 6.5  전략(Key Strategy)

JSONEncoder와 JSONDecoder는 키 변환 전략을 제공합니다.

#### 스네이크 케이스(Snake Case) 변환

```swift
let decoder = JSONDecoder()
decoder.keyDecodingStrategy = .convertFromSnakeCase

let encoder = JSONEncoder()
encoder.keyEncodingStrategy = .convertToSnakeCase
```

<br>

#### 사용 예시

```swift
let jsonData = """
{
    "user_name": "Alice",
    "user_age": 25,
    "is_developer": true
}
""".data(using: .utf8)!

struct User: Codable {
    let userName: String
    let userAge: Int
    let isDeveloper: Bool
}

let decoder = JSONDecoder()
decoder.keyDecodingStrategy = .convertFromSnakeCase

do {
    let user = try decoder.decode(User.self, from: jsonData)
    print("Name: \(user.userName), Age: \(user.userAge), Is Developer: \(user.isDeveloper)")
} catch {
    print("Decoding failed: \(error)")
}
```

#### 출력

```
Name: Alice, Age: 25, Is Developer: true
```

<br>

## 6.6 Codable의 장점과 주의사항
### 장점

1. 간단한 직렬화/역직렬화: 데이터를 쉽게 JSON이나 다른 포맷으로 변환 가능.
2. 타입 안정성: 컴파일 타임에 타입 체크를 제공.
3. 커스터마이징 가능: 키 매핑, 키 전략 등을 활용하여 유연하게 설계 가능.

<br>

### 주의사항
1. JSON 키와 Swift 변수명 불일치:
- 반드시 CodingKeys로 매핑 필요.
2. 복잡한 JSON 구조:
- 중첩된 JSON은 수동 구현이 필요할 수 있음.
3. 선택적 데이터 처리:
- 옵셔널 타입으로 선언하여 누락된 데이터 처리.

Codable은 iOS 개발에서 데이터를 처리할 때 강력하고 간결한 API를 제공합니다. 이를 통해 JSON 같은 데이터 포맷을 쉽게 변환하고, 유지보수성이 높은 코드를 작성할 수 있습니다.


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
