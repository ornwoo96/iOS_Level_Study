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

의존성 주입은 객체 간의 강한 결합도를 줄이고 테스트 가능성을 높이는 디자인 패턴입니다. 객체가 내부에서 직접 의존성을 생성하지 않고, 외부에서 주입받도록 설계합니다. 이를 통해 코드 재사용성과 유지보수성을 높이고, 테스트를 용이하게 만듭니다.

<br>
<br>

## 7.1 의존성 주입의 세 가지 유형(Initializer Injection, Property Injection, Method Injection)을 설명해주세요.

### 1. Initializer Injection

- 의존성을 생성자의 파라미터로 전달받는 방식입니다.
- 객체 초기화 시 반드시 필요한 의존성을 보장합니다.

#### 예시 코드

```swift
class NetworkService {
    func fetchData() {
        print("Fetching data...")
    }
}

class ViewModel {
    private let networkService: NetworkService

    // 생성자를 통해 의존성을 주입
    init(networkService: NetworkService) {
        self.networkService = networkService
    }

    func loadData() {
        networkService.fetchData()
    }
}

// 사용
let service = NetworkService()
let viewModel = ViewModel(networkService: service)
viewModel.loadData()
```

<br>

#### 장점:
- 의존성을 반드시 설정하도록 강제.
- 불변성을 유지.

<br>

### 2. Property Injection
- 의존성을 객체의 프로퍼티를 통해 설정하는 방식입니다.
- 객체 초기화 이후에 의존성을 주입할 수 있습니다.

#### 예시 코드

```swift
class NetworkService {
    func fetchData() {
        print("Fetching data...")
    }
}

class ViewModel {
    var networkService: NetworkService?

    func loadData() {
        networkService?.fetchData()
    }
}

// 사용
let viewModel = ViewModel()
viewModel.networkService = NetworkService()
viewModel.loadData()
```

<br>

#### 장점:
- 유연하게 의존성을 설정 가능.
- 초기화 시점에 의존성을 알 필요 없음.

#### 단점:
- 의존성이 설정되지 않을 위험 존재.

<br>

### 3. Method Injection
- 의존성을 메서드의 파라미터로 전달받는 방식입니다.
- 특정 메서드에서만 필요한 의존성을 주입할 때 사용합니다.

#### 예시 코드
```swift
class NetworkService {
    func fetchData() {
        print("Fetching data...")
    }
}

class ViewModel {
    func loadData(with networkService: NetworkService) {
        networkService.fetchData()
    }
}

// 사용
let service = NetworkService()
let viewModel = ViewModel()
viewModel.loadData(with: service)
```

<br>

#### 장점:
- 메서드 호출 시점에 필요한 의존성을 설정 가능.
- 의존성이 메서드 내부로 제한되어 명확한 의도를 표현.



<br>
<br>

## 7.2 의존성 주입 컨테이너(Dependency Injection Container)란 무엇인가요?
### 정의

의존성 주입 컨테이너는 객체의 생성 및 의존성 관리를 자동화하는 도구입니다. 이를 통해 객체 생성과 의존성 주입을 명확히 분리하여, 복잡한 객체 그래프를 효율적으로 관리할 수 있습니다.

#### 예시 코드 
```swift
class DependencyContainer {
    func makeNetworkService() -> NetworkService {
        return NetworkService()
    }

    func makeViewModel() -> ViewModel {
        let networkService = makeNetworkService()
        return ViewModel(networkService: networkService)
    }
}

// 사용
let container = DependencyContainer()
let viewModel = container.makeViewModel()
viewModel.loadData()
```

<br>

## 7.3 의존성 주입을 사용함으로써 얻을 수 있는 이점은 무엇인가요?

1. 유연한 설계:
- 객체 간의 결합도를 낮춰 모듈성을 높입니다.
- 코드 변경 시 영향을 최소화합니다.

2. 테스트 가능성 향상:
- 의존성을 쉽게 대체(mock)할 수 있어 단위 테스트 작성이 간단해집니다.

#### 테스트 예시

```swift
class MockNetworkService: NetworkService {
    override func fetchData() {
        print("Mock fetch data")
    }
}

// Mock 의존성을 사용하여 ViewModel 테스트
let mockService = MockNetworkService()
let viewModel = ViewModel(networkService: mockService)
viewModel.loadData() // 출력: Mock fetch data
```

3. 코드 재사용성 증가:
- 의존성을 분리하여 재사용 가능.

4. 유지보수 용이:
- 객체 생성과 동작 로직을 분리하여, 코드를 쉽게 이해하고 수정할 수 있습니다.

<br>

### 요약

<img src="https://github.com/user-attachments/assets/78e02620-57f8-424f-84d1-aa40e1860203">

의존성 주입은 iOS 개발에서 코드의 유연성과 유지보수성을 높이는 중요한 도구이며, 특히 테스트 가능성을 강화하는 데 필수적인 패턴입니다.

<br>
<br>

## 8. 델리게이션 패턴(Delegation Pattern)과 클로저의 차이점은 무엇인가요?

<img src="https://github.com/user-attachments/assets/00d7b5a0-9404-4551-b3ce-f1e7b91825a2">

<br>
<br>

## 8.1 델리게이션 패턴에서 메모리 누수가 발생할 수 있는 경우와 해결 방법을 설명해주세요.

### 메모리 누수 발생 조건
#### 순환 참조(Retain Cycle):
- 델리게이트 객체가 delegate 프로퍼티를 강한 참조로 보유하고 있는 경우 발생.
- 강한 참조로 인해 객체가 해제되지 않고 메모리에 남아 있음.

<br>

### 해결 방법: 약한 참조 사용

델리게이트 프로퍼티를 **weak** 으로 선언하여 강한 참조를 방지합니다.

#### 예시 코드

```swift
protocol TaskDelegate: AnyObject {
    func taskDidComplete()
}

class Worker {
    weak var delegate: TaskDelegate? // 약한 참조로 선언
    func doWork() {
        print("Working...")
        delegate?.taskDidComplete()
    }
}

class Manager: TaskDelegate {
    func taskDidComplete() {
        print("Task is complete.")
    }
}

let worker = Worker()
let manager = Manager()

worker.delegate = manager
worker.doWork()
// 출력:
// Working...
// Task is complete.
```


<br>
<br>

## 8.2 클로저의 캡처 리스트(Capture List)는 어떤 역할을 하나요?
### 정의

클로저가 외부 변수 또는 객체를 캡처할 때, 캡처된 객체가 강한 참조로 인해 메모리 누수가 발생하지 않도록 관리하는 리스트입니다.

### 사용 방법
- [weak self] 또는 [unowned self]를 사용하여 캡처된 객체의 참조를 제어.

#### 코드 예시

```swift
class Example {
    var value: String = "Hello"
    
    func performTask() {
        // 캡처 리스트 사용
        let closure = { [weak self] in
            guard let self = self else { return }
            print(self.value)
        }
        closure()
    }
}

var example: Example? = Example()
example?.performTask() // 출력: Hello
example = nil
```

<br>

### 주요 역할
#### 1. 메모리 관리:
- 클로저 내부에서 객체가 해제되지 않는 문제를 방지.
#### 2.	참조 방식 선택:
- weak: 옵셔널로 참조하며, 참조 대상이 해제되면 nil.
- unowned: 비옵셔널로 참조하며, 해제된 객체를 참조하면 런타임 에러 발생.

<br>
<br>

## 8.3 델리게이션 패턴과 클로저를 함께 사용하는 경우의 장단점은 무엇인가요?
### 장점
#### 1.	유연성:
- 델리게이션 패턴은 반복적인 작업 위임에 적합.
- 클로저는 특정 작업을 직접 정의하여 간결한 구현 가능.
#### 2.	명확한 책임 분리:
- 델리게이션으로 큰 작업 구조를 분리하고, 클로저로 세부 동작 정의.

<br>

### 단점
#### 1.	복잡성 증가:
- 델리게이션과 클로저가 함께 사용되면 코드가 복잡해질 수 있음.
#### 2.	순환 참조 관리:
- 두 메커니즘 모두 메모리 관리를 신경 써야 함.

<br>


#### 예시 코드

```swift
protocol TaskDelegate: AnyObject {
    func taskDidStart()
    func taskDidComplete()
}

class Worker {
    weak var delegate: TaskDelegate?
    var onProgress: ((Double) -> Void)? // 클로저를 사용하여 진행 상황 전달

    func doWork() {
        delegate?.taskDidStart()
        
        for i in 1...100 {
            if i % 20 == 0 {
                onProgress?(Double(i) / 100.0)
            }
        }
        
        delegate?.taskDidComplete()
    }
}

class Manager: TaskDelegate {
    func taskDidStart() {
        print("Task started.")
    }
    
    func taskDidComplete() {
        print("Task completed.")
    }
}

// 사용
let worker = Worker()
let manager = Manager()

worker.delegate = manager
worker.onProgress = { progress in
    print("Progress: \(progress * 100)%")
}

worker.doWork()
// 출력:
// Task started.
// Progress: 20.0%
// Progress: 40.0%
// Progress: 60.0%
// Progress: 80.0%
// Progress: 100.0%
// Task completed.
```

<br>

### 요약

<img src="https://github.com/user-attachments/assets/584093b4-5057-4287-b4e5-727694da820b">

델리게이션 패턴과 클로저는 서로 보완적인 관계로, 적절한 상황에서 혼합하여 사용하면 더욱 강력한 코드 설계가 가능합니다.



<br>
<br>

## 9. UIKit에서 테이블 뷰(UITableView)와 컬렉션 뷰(UICollectionView)의 차이점은 무엇인가요?


### 테이블 뷰(UITableView)
- 구조: 단일 열(Column) 레이아웃.
- 용도: 리스트 형태의 데이터를 표시.
- 스크롤 방향: 수직 방향(Vertical) 스크롤만 지원.
- 레이아웃: 제한적(단순한 리스트).
- 예제: 연락처 목록, 설정 메뉴 등.

### 컬렉션 뷰(UICollectionView)
- 구조: 여러 열 및 행의 레이아웃 지원.
- 용도: 더 복잡한 데이터 표현(그리드, 카드 뷰 등).
- 스크롤 방향: 수직(Vertical) 및 수평(Horizontal) 스크롤 모두 지원.
- 레이아웃: 커스터마이징 가능. FlowLayout이나 Compositional Layout 사용.
- 예제: 사진 갤러리, 앱 스토어의 카드 레이아웃 등.

<img src="https://github.com/user-attachments/assets/52a1b1ee-8e46-4fd2-9a88-db67a05e8685">


<br>
<br>

## 9.1 테이블 뷰와 컬렉션 뷰에서 셀을 재사용하는 이유와 방법을 설명해주세요.


### 셀 재사용의 이유
1. 성능 최적화:
	- 스크롤 시 새 셀을 생성하는 대신 기존 셀을 재사용하여 메모리와 성능을 절약.
2. 메모리 관리:
	- 화면에 표시되지 않는 셀은 메모리에서 해제되므로 메모리 사용량을 줄임.

<br>


### 재사용 방법

#### UITableView
- dequeueReusableCell(withIdentifier:)를 사용해 셀을 재사용 큐에서 가져옴.

```swift
let tableView = UITableView()

tableView.register(UITableViewCell.self, forCellReuseIdentifier: "Cell")

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
    cell.textLabel?.text = "Row \(indexPath.row)"
    return cell
}
```

<br>

#### UICollectionView
- dequeueReusableCell(withReuseIdentifier:)를 사용해 셀을 재사용 큐에서 가져옴.

```swift
let collectionView = UICollectionView(frame: .zero, collectionViewLayout: UICollectionViewFlowLayout())

collectionView.register(UICollectionViewCell.self, forCellWithReuseIdentifier: "Cell")

func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
    let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "Cell", for: indexPath)
    cell.backgroundColor = .blue
    return cell
}
```


<br>
<br>

## 9.2 테이블 뷰와 컬렉션 뷰의 데이터 소스(Data Source)와 델리게이트(Delegate)의 역할은 무엇인가요?
### UITableView와 UICollectionView 공통점
#### 1.	데이터 소스(Data Source):
- 데이터와 뷰를 연결하는 역할.
- 필수 메서드:
- numberOfSections: 섹션 수 반환.
- numberOfRowsInSection 또는 numberOfItemsInSection: 섹션당 항목 수 반환.
- cellForRowAt 또는 cellForItemAt: 셀의 콘텐츠 설정.
  
#### 2.	델리게이트(Delegate):
- 사용자 이벤트 및 셀의 동작을 관리.
- 선택적 메서드:
- didSelectRowAt 또는 didSelectItemAt: 셀 선택 시 동작 정의.
- heightForRowAt: 셀 높이 조정(UITableView 전용).

<br>

#### 코드 예시

#### UITableView

```swift
// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 10 // 10개의 행
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        cell.textLabel?.text = "Row \(indexPath.row)"
        return cell
    }
}

// MARK: - UITableViewDelegate
extension MyViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        print("Selected row: \(indexPath.row)")
    }
}
```

<br>

#### UICollectionView

```swift
// MARK: - UICollectionViewDataSource
extension MyCollectionViewController: UICollectionViewDataSource {
    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return 20 // 20개의 아이템
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: "Cell", for: indexPath)
        cell.backgroundColor = .blue
        return cell
    }
}

// MARK: - UICollectionViewDelegate
extension MyCollectionViewController: UICollectionViewDelegate {
    func collectionView(_ collectionView: UICollectionView, didSelectItemAt indexPath: IndexPath) {
        print("Selected item: \(indexPath.item)")
    }
}
```


<br>
<br>

## 9.3 컬렉션 뷰에서 사용할 수 있는 레이아웃(Layout)의 종류와 특징을 설명해주세요.

### 1. UICollectionViewFlowLayout

- 기본 레이아웃:
	- 아이템을 그리드 형식으로 배치.
	- 간격 및 방향 조정 가능.
- 특징:
	- 구현이 단순하며, 기본적인 레이아웃 작업에 적합.

#### 코드 예시

```swift
let layout = UICollectionViewFlowLayout()
layout.itemSize = CGSize(width: 100, height: 100)
layout.minimumLineSpacing = 10
layout.minimumInteritemSpacing = 10
layout.scrollDirection = .vertical

let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
```

<br>

### 2. UICollectionViewCompositionalLayout
- 복잡한 레이아웃:
	- 다양한 섹션과 아이템 크기를 동적으로 설정 가능.
- 특징:
	- 매우 유연하며, 다단형 레이아웃, 그리드, 카드 형식 등에 적합.

#### 코드 예시

```swift
let itemSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(0.3),
                                      heightDimension: .fractionalHeight(1.0))
let item = NSCollectionLayoutItem(layoutSize: itemSize)

let groupSize = NSCollectionLayoutSize(widthDimension: .fractionalWidth(1.0),
                                       heightDimension: .fractionalHeight(0.2))
let group = NSCollectionLayoutGroup.horizontal(layoutSize: groupSize, subitems: [item])

let section = NSCollectionLayoutSection(group: group)
let layout = UICollectionViewCompositionalLayout(section: section)

let collectionView = UICollectionView(frame: .zero, collectionViewLayout: layout)
```

<br>

3. 커스텀 레이아웃(Custom Layout)

- 직접 레이아웃 정의:
	- UICollectionViewLayout을 상속받아 구현.
- 특징:
	- 매우 복잡한 레이아웃을 원하는 경우 사용.

<br>

### 요약

<img src="https://github.com/user-attachments/assets/b468502c-c24c-488f-94cb-1db02cb47655">

UITableView는 단순한 리스트, UICollectionView는 복잡한 데이터 표현에 적합합니다. 컬렉션 뷰는 더 유연한 레이아웃과 구성 옵션을 제공합니다.

<br>
<br>

## 10. iOS 앱 아키텍처 패턴 중 MVC, MVVM, VIP, MVI, TCA의 차이점은 무엇인가요?
<img src="https://github.com/user-attachments/assets/29900868-52e1-47ab-91a2-e6da8714fb2c">


<br>
<br>

## 10.1 MVC의 장점은 무엇인가요?
#### 1.	간단한 구조
- 모든 코드가 세 가지 컴포넌트(Model, View, Controller)로 분리되어 있어 개발 초기 단계에서 구현이 빠릅니다.
#### 2.	빠른 개발 가능
- 소규모 프로젝트나 간단한 UI 구성에 적합하며, 코드가 직관적입니다.
#### 3.	기본 지원
- UIKit이 기본적으로 MVC 기반이기 때문에 학습곡선이 낮습니다.

<br>
<br>

## 10.2 각 아키텍처 패턴의 구성 요소와 책임을 설명해주세요.
### MVC
- Model: 데이터 및 비즈니스 로직
- View: UI 요소와 사용자 입력
- Controller: Model과 View 사이의 중재 역할

<br>

### MVVM
- Model: 데이터 및 비즈니스 로직
- View: UI 요소와 사용자 입력
- ViewModel: Model과 View 사이의 바인딩 및 데이터 처리

<br>

### VIP
- View: UI와 사용자 입력
- Interactor: 비즈니스 로직 및 데이터 처리
- Presenter: View와 Interactor 간의 데이터 전달 및 변환

<br>

### MVI
- Model: 상태와 데이터를 보유
- View: UI와 사용자 입력
- Intent: 사용자 액션 및 상태 업데이트

<br>

### TCA
- State: 현재 상태
- Action: 사용자 액션 및 이벤트
- Reducer: 상태 변화 로직
- Store: 상태와 액션 관리


<br>
<br>

## 10.3 MVVM 패턴에서 Binding은 어떤 역할을 하나요?
### 역할:
- View와 ViewModel 간의 데이터 동기화를 자동으로 처리합니다.
- 데이터 변경 시 View가 자동으로 업데이트되고, 사용자 입력도 ViewModel에 전달됩니다.
  
<br>

### 장점:
- 코드 중복 감소
- UI와 로직 분리로 테스트 용이
- 예시 (SwiftUI에서의 데이터 바인딩):

#### 예시 (SwiftUI에서의 데이터 바인딩):

```swift
class ViewModel: ObservableObject {
    @Published var text = "Hello, MVVM!"
}

struct ContentView: View {
    @StateObject var viewModel = ViewModel()

    var body: some View {
        Text(viewModel.text)
        Button("Update Text") {
            viewModel.text = "Updated Text!"
        }
    }
}
```

<br>
<br>

## 10.4 VIP 패턴에서 Presenter의 역할은 무엇인가요?
### 역할:
- Interactor로부터 받은 데이터를 View에서 표시할 수 있는 형식으로 변환.
- View와 Interactor 간의 직접적인 결합을 방지.

#### 예시:

```swift
protocol PresenterProtocol {
    func presentData(response: String) -> String
}

class Presenter: PresenterProtocol {
    func presentData(response: String) -> String {
        return "Formatted: \(response)"
    }
}
```

<br>
<br>

## 10.5 MVI 패턴에서 Intent의 역할은 무엇인가요?
### 역할:
- 사용자 액션과 상태 변경 요청을 처리.
- 모든 사용자 이벤트를 Intent로 변환하여 처리.


#### 예시:
```swift
// Model
struct CounterState {
    var count: Int = 0
}

// Intent
enum CounterIntent {
    case increment
}

// ViewModel
class CounterViewModel {
    private var state = CounterState()
    var updateView: ((CounterState) -> Void)?

    func processIntent(_ intent: CounterIntent) {
        switch intent {
        case .increment:
            state.count += 1
            updateView?(state)
        }
    }
}

// View
class CounterViewController {
    var viewModel = CounterViewModel()

    init() {
        viewModel.updateView = { [weak self] state in
            // 업데이트 된 상태를 화면에 표시
        }
    }

    @IBAction func incrementButtonTapped(_ sender: Any) {
        viewModel.processIntent(.increment)
    }
}
```


<br>
<br>

## 10.6 TCA 란 무엇인가요?
### TCA (The Composable Architecture):
- 상태 관리와 비즈니스 로직을 명확히 정의하여, 재사용 가능하고 테스트 가능한 앱 아키텍처를 제공.

<br>

### 구성 요소:
- State: 앱의 상태
- Action: 상태를 변경시키는 이벤트
- Reducer: 액션을 기반으로 상태를 변경하는 로직
- Store: 상태와 액션을 관리하는 컨테이너

<br>

#### 예시:

```swift
struct AppState: Equatable {
    var count = 0
}

enum AppAction: Equatable {
    case increment
}

let appReducer = Reducer<AppState, AppAction, Void> { state, action, _ in
    switch action {
    case .increment:
        state.count += 1
        return .none
    }
}

let store = Store(initialState: AppState(), reducer: appReducer, environment: ())

store.send(.increment)
```

### 장점:
- 상태 관리 명확성
- 테스트 용이성
- 기능의 모듈화 및 재사용 가능성


<br>
<br>


## 11. Swift에서 옵셔널(Optional)을 사용할 때 주의할 점은 무엇인가요?

옵셔널은 Swift에서 값이 존재할 수도, 존재하지 않을 수도 있는 상황을 안전하게 처리하기 위해 사용되는 데이터 타입입니다. 잘못된 사용은 런타임 에러를 초래할 수 있으므로 주의가 필요합니다.

### 1. 강제 언래핑(Force Unwrapping)을 피해야 함

- 문제점: 강제 언래핑(!)은 값이 존재한다고 가정하고 값을 추출하지만, 옵셔널 값이 nil인 경우 런타임 에러를 발생시킵니다.
- 해결 방법: 옵셔널 바인딩(if let, guard let)이나 nil 병합 연산자(??)를 사용하여 안전하게 값을 처리합니다.

#### 예시
```swift
let optionalValue: String? = nil

// 잘못된 사용: 강제 언래핑
print(optionalValue!) // 런타임 에러 발생

// 안전한 사용: 옵셔널 바인딩
if let value = optionalValue {
    print(value)
} else {
    print("Value is nil")
}

// 안전한 사용: nil 병합 연산자
print(optionalValue ?? "Default Value")
```

<br>

### 2. 옵셔널 바인딩과 옵셔널 체이닝을 적절히 활용

#### 옵셔널 바인딩(Optional Binding):
- 옵셔널 값을 안전하게 언래핑하고, 값이 있는 경우에만 작업을 수행.
- if let 또는 guard let을 사용.
#### 옵셔널 체이닝(Optional Chaining):
- 옵셔널 값에 연속적으로 접근하며, 중간에 nil이 나오면 연산을 중단하고 nil을 반환.

#### 옵셔널 바인딩 예시

```swift
let optionalName: String? = "John"

if let name = optionalName {
    print("Name is \(name)")
} else {
    print("Name is nil")
}
```

#### 옵셔널 체이닝 예시
```swift
struct Address {
    var city: String
}

struct Person {
    var name: String
    var address: Address?
}

let person = Person(name: "Alice", address: Address(city: "Seoul"))
print(person.address?.city ?? "Unknown") // 출력: Seoul
```

<br>

### 3. 암시적 언래핑 옵셔널(Implicitly Unwrapped Optional)을 신중하게 사용
- 특징: 옵셔널로 선언되지만, 항상 값이 존재할 것으로 보장되는 경우 사용.
- 주의점: 값이 nil이면 런타임 에러가 발생하므로, 반드시 값이 설정될 것을 보장할 수 있을 때만 사용해야 함.

#### 예시

```swift
class ViewController: UIViewController {
    var label: UILabel!

    override func viewDidLoad() {
        super.viewDidLoad()
        label = UILabel() // 나중에 초기화
        label.text = "Hello, World!"
    }
}
```

<br>

### 4. 옵셔널 값의 기본값을 제공하여 안전성을 높임
- Nil-Coalescing Operator (??): 값이 nil일 경우 기본값을 반환.
#### 예시:

```swift
let optionalValue: String? = nil
let value = optionalValue ?? "Default Value"
print(value) // 출력: Default Value
```

<br>

### 5. 옵셔널 값에 대한 불필요한 강제 사용을 피함
- 타입이 옵셔널일 필요가 없는 경우, 옵셔널을 사용하지 않도록 설계.
- 항상 값이 존재하는 경우라면, 옵셔널 대신 기본 타입을 사용.

<br>

### 요약 
<img src="https://github.com/user-attachments/assets/da1dc646-d178-431c-bbe3-ec67f9a383f7">

옵셔널은 Swift의 타입 안정성을 강화하는 중요한 개념이지만, 적절한 사용법을 준수해야 앱의 안전성과 가독성을 유지할 수 있습니다.

<br>
<br>

## 11.1 강제 언래핑(Force Unwrapping)을 사용하면 안 되는 이유는 무엇인가요?

### 개념
- 강제 언래핑 (!)은 옵셔널 타입에서 값을 강제로 추출하는 방식입니다.
- 옵셔널 값이 nil일 경우, 강제 언래핑을 시도하면 런타임 에러가 발생합니다.

<br>

### 사용하지 말아야 하는 이유
- 안전성 문제: nil 상태를 고려하지 않고 값을 강제 언래핑하면 앱이 비정상 종료됩니다.
- 코드 가독성 저하: 강제 언래핑을 남용하면 코드의 안정성이 떨어지고 유지보수가 어려워집니다.

#### 예제

```swift
let optionalValue: String? = nil

// 잘못된 사용: 강제 언래핑
print(optionalValue!) // 런타임 에러 발생: Unexpectedly found nil while unwrapping an Optional value
```

<br>

#### 안전한 대안

#### 1. 옵셔널 바인딩:

```swift
if let value = optionalValue {
    print(value) // 안전하게 사용
} else {
    print("Value is nil")
}
```

#### 2.	Nil-Coalescing 연산자:

```swift
print(optionalValue ?? "Default Value") // 값이 없을 경우 기본값 사용
```

<br>
<br>

## 11.2 옵셔널 바인딩(Optional Binding)과 옵셔널 체이닝(Optional Chaining)의 차이점을 설명해주세요.

<img src="https://github.com/user-attachments/assets/a718acc5-fe78-4a5c-a8e3-ba2d6d46d089">

<br>

#### 옵셔널 바인딩 예제
```swift
let optionalString: String? = "Hello"

if let value = optionalString {
    print(value) // 출력: Hello
} else {
    print("Value is nil")
}
```

<br>

#### 옵셔널 체이닝 예제

```swift
struct Address {
    var city: String
}

struct Person {
    var name: String
    var address: Address?
}

let person = Person(name: "John", address: Address(city: "Seoul"))

// 옵셔널 체이닝을 사용해 안전하게 접근
if let city = person.address?.city {
    print(city) // 출력: Seoul
} else {
    print("City is nil")
}
```


<br>
<br>

## 11.3 암시적 언래핑 옵셔널(Implicitly Unwrapped Optional)은 어떤 경우에 사용하나요?

### 개념
- 암시적 언래핑 옵셔널 (!)은 옵셔널로 선언되지만, 항상 값이 존재할 것이라고 보장할 수 있는 경우에 사용합니다.
- 일반 변수처럼 사용 가능하지만, 값이 nil인 경우 런타임 에러가 발생합니다.

<br>

### 사용하는 경우
#### 1.	의존성이 강한 객체의 초기화
- 특정 프로퍼티나 객체가 나중에 반드시 초기화될 것이 확실한 경우.
#### 2.	초기화 후 항상 값이 있는 상태를 유지할 때
- 값이 설정된 이후에는 nil일 가능성이 없는 경우.

<br>

#### 예제

```swift
class ViewController: UIViewController {
    var label: UILabel! // 나중에 초기화 예정

    override func viewDidLoad() {
        super.viewDidLoad()
        label = UILabel()
        label.text = "Hello, World!" // 안전하게 사용 가능
    }
}
```

#### 주의점
- 초기화 과정에서 값이 설정되지 않거나, 이후에 nil로 설정되면 런타임 에러가 발생할 수 있습니다.
- 반드시 값이 설정될 것을 보장할 수 있는 경우에만 사용해야 합니다.

<br>

### 요약

<img src="https://github.com/user-attachments/assets/a2273cff-10c9-4989-983b-e2b5bd2e5d5b">

옵셔널은 Swift의 강력한 안전 장치이지만, 적절한 사용이 필요합니다. 강제 언래핑보다는 바인딩, 체이닝, 또는 기본값 제공 등을 통해 안전하게 다루는 것이 중요합니다.


<br>
<br>

## 12. iOS 앱에서 코어 애니메이션(Core Animation)을 사용하는 방법은 무엇인가요?
Core Animation은 iOS에서 애니메이션을 효율적으로 처리하기 위해 제공되는 강력한 프레임워크입니다. Core Animation은 애니메이션이 GPU에서 실행되도록 지원하여 CPU의 부하를 줄이고, 매끄럽고 빠른 애니메이션을 제공합니다.

### 코어 애니메이션의 기본 개념
#### 1.	애니메이션 레이어 기반:
- Core Animation은 CALayer를 기반으로 작동합니다. 모든 UIView는 기본적으로 CALayer를 포함하며, Core Animation을 활용하면 UIView의 속성을 변경해 애니메이션을 구현할 수 있습니다.
#### 2.	GPU 활용:
- 애니메이션은 GPU에서 처리되므로 성능이 높고, CPU 부하를 줄입니다.
#### 3.	암시적 애니메이션과 명시적 애니메이션:
- 암시적 애니메이션: 레이어 속성 변경 시 기본 애니메이션이 적용됩니다.
- 명시적 애니메이션: CABasicAnimation, CAKeyframeAnimation 등을 사용해 직접 애니메이션을 설정합니다.

<br>

### 애니메이션 구현
#### CABasicAnimation
단일 속성의 애니메이션을 설정할 때 사용합니다.

```swift
let animation = CABasicAnimation(keyPath: "position")
animation.fromValue = CGPoint(x: 50, y: 50)  // 시작 위치
animation.toValue = CGPoint(x: 250, y: 250)  // 끝 위치
animation.duration = 2.0                    // 애니메이션 지속 시간 (초)

// 애니메이션을 레이어에 추가
layer.add(animation, forKey: "positionAnimation")
```

<br>

#### CAKeyframeAnimation
여러 중간 경로를 따라 애니메이션을 설정합니다.
```swift
// 키 프레임 애니메이션 생성
let keyframeAnimation = CAKeyframeAnimation(keyPath: "position")
// 애니메이션 대상 속성을 "position"으로 설정 (레이어의 위치를 변경)

// 애니메이션 경로의 좌표 설정 (레이어가 이동할 각 위치를 지정)
keyframeAnimation.values = [
    CGPoint(x: 50, y: 50),  // 시작 위치
    CGPoint(x: 150, y: 50), // 오른쪽으로 이동
    CGPoint(x: 150, y: 150), // 아래로 이동
    CGPoint(x: 50, y: 150)  // 왼쪽으로 이동
]

// 애니메이션의 총 지속 시간 설정 (3초 동안 진행)
keyframeAnimation.duration = 3.0

// 레이어에 애니메이션 추가
layer.add(keyframeAnimation, forKey: "keyframeAnimation")
// "keyframeAnimation" 키를 사용해 애니메이션 추가 (참조 및 제거 시 사용 가능)
```

<br>

#### CASpringAnimation
스프링 물리 법칙을 기반으로 자연스러운 애니메이션을 제공합니다.

```swift
// 스프링 애니메이션 생성
let springAnimation = CASpringAnimation(keyPath: "position.y")
// 애니메이션할 레이어의 속성(keyPath)을 "position.y"로 설정 (y축 위치를 변경)

// 애니메이션 시작 값 설정 (y축 위치를 100으로 시작)
springAnimation.fromValue = 100

// 애니메이션 종료 값 설정 (y축 위치를 200으로 끝)
springAnimation.toValue = 200

// 감쇠 비율 설정 (값이 클수록 애니메이션이 더 빨리 멈추고 부드럽게 감쇠)
springAnimation.damping = 5.0

// 초기 속도 설정 (값이 클수록 애니메이션이 더 빠르게 시작)
springAnimation.initialVelocity = 10.0

// 스프링 애니메이션이 안정화되는 지속 시간을 자동으로 계산하여 설정
springAnimation.duration = springAnimation.settlingDuration

// 레이어에 스프링 애니메이션 추가 (키를 사용해 식별 가능)
layer.add(springAnimation, forKey: "springAnimation")
```

<br>

#### CAAnimationGroup

여러 애니메이션을 동시에 실행하거나 결합할 때 사용합니다.

```swift
// 위치 애니메이션 생성
let positionAnimation = CABasicAnimation(keyPath: "position")
// 애니메이션할 속성을 "position"으로 설정 (레이어의 위치를 변경)
positionAnimation.fromValue = CGPoint(x: 50, y: 50) // 시작 위치
positionAnimation.toValue = CGPoint(x: 250, y: 250) // 종료 위치

// 투명도 애니메이션 생성
let opacityAnimation = CABasicAnimation(keyPath: "opacity")
// 애니메이션할 속성을 "opacity"로 설정 (레이어의 투명도를 변경)
opacityAnimation.fromValue = 1.0 // 시작 투명도 (불투명)
opacityAnimation.toValue = 0.0   // 종료 투명도 (완전히 투명)

// 애니메이션 그룹 생성
let animationGroup = CAAnimationGroup()
// 그룹에 두 개의 애니메이션 추가 (위치, 투명도)
animationGroup.animations = [positionAnimation, opacityAnimation]
// 그룹 전체 지속 시간을 설정 (2초)
animationGroup.duration = 2.0

// 레이어에 애니메이션 그룹 추가
layer.add(animationGroup, forKey: "groupAnimation")
```

<br>

### UIView.animate로 Core Animation 활용
UIView의 애니메이션은 내부적으로 Core Animation을 사용하며, 코드가 간결하고 사용하기 쉽습니다.

```swift
UIView.animate(withDuration: 2.0) {
    view.backgroundColor = .blue
    view.frame.origin.y += 100
}
```

<br>

### Core Animation 활용 시 주의사항

#### 1.	뷰의 최종 상태:
- Core Animation은 GPU에서 동작하므로 애니메이션 완료 후 상태를 유지하려면, 속성을 직접 변경해야 합니다.

```swift
// 애니메이션이 종료된 후 상태 유지
animation.isRemovedOnCompletion = false
// 애니메이션이 완료된 후 최종 상태를 유지하도록 설정

// 애니메이션의 fillMode 설정
animation.fillMode = .forwards
// 애니메이션이 완료된 후 레이어가 최종 상태를 유지
```

<br>

#### 2.	레퍼런스 타입:
- CALayer는 레퍼런스 타입이므로 동일한 레이어 객체를 여러 뷰에서 공유하면 문제가 발생할 수 있습니다.

<br>

#### 3.	성능 최적화:
- Core Animation은 GPU에서 동작하지만, 과도한 애니메이션은 메모리와 성능에 영향을 미칠 수 있습니다.

<br>

### 요약

Core Animation은 CALayer를 기반으로 애니메이션을 설정하며, CABasicAnimation, CAKeyframeAnimation, CASpringAnimation 등을 사용해 다양한 애니메이션 효과를 구현할 수 있습니다. 또한 UIView의 animate 메서드는 Core Animation의 간단한 래퍼로 애니메이션을 쉽게 구현할 수 있습니다.

상황에 맞게 CALayer와 UIView 애니메이션을 적절히 활용해 매끄러운 사용자 경험을 제공합니다.

<br>
<br>

## 12.1 CALayer의 주요 속성과 메서드를 설명해주세요.
CALayer는 애니메이션과 시각적 콘텐츠를 관리하는 기본 단위입니다. UIView의 기본 클래스이며, 모든 UIView는 기본적으로 CALayer를 포함합니다.

### 주요 속성

<img src="https://github.com/user-attachments/assets/ff151179-9b56-4c8d-8d85-a09cfb474c86">

<br>

### 주요 메서드

<img src="https://github.com/user-attachments/assets/a0022f78-343e-4c24-b91d-97ecb6566f5b">

<br>

#### CALayer 예제

```swift
// CALayer 객체 생성
let layer = CALayer()

// 레이어의 위치와 크기를 설정 (x: 50, y: 50에 너비 100, 높이 100으로 배치)
layer.frame = CGRect(x: 50, y: 50, width: 100, height: 100)

// 레이어의 배경색을 빨간색으로 설정
layer.backgroundColor = UIColor.red.cgColor

// 레이어의 모서리를 둥글게 설정 (반지름: 10)
layer.cornerRadius = 10

// 레이어에 그림자 색상을 검정색으로 설정
layer.shadowColor = UIColor.black.cgColor

// 레이어의 그림자 불투명도를 설정 (0.0 ~ 1.0, 여기서는 70% 불투명)
layer.shadowOpacity = 0.7

// 레이어의 그림자 위치를 설정 (x: 5, y: 5으로 이동)
layer.shadowOffset = CGSize(width: 5, height: 5)

// 뷰의 레이어에 새로운 서브 레이어를 추가하여 화면에 표시
view.layer.addSublayer(layer)
```


<br>
<br>

## 12.2 애니메이션 그룹(Animation Group)은 어떤 경우에 사용하나요?
애니메이션 그룹은 여러 개의 애니메이션을 동시에 실행하거나 순서대로 실행할 때 사용됩니다.

### 특징
- 병렬 실행: 여러 애니메이션이 동시에 실행됩니다.
- 공통 속성: 같은 duration, timingFunction 등을 공유.

#### 예제 

```swift
// 위치 애니메이션 생성
let positionAnimation = CABasicAnimation(keyPath: "position")
// 애니메이션할 속성(keyPath)을 "position"으로 설정 (레이어의 위치를 변경)
positionAnimation.fromValue = CGPoint(x: 50, y: 50) // 시작 위치
positionAnimation.toValue = CGPoint(x: 250, y: 250) // 종료 위치

// 투명도 애니메이션 생성
let opacityAnimation = CABasicAnimation(keyPath: "opacity")
// 애니메이션할 속성(keyPath)을 "opacity"로 설정 (레이어의 투명도를 변경)
opacityAnimation.fromValue = 1.0 // 시작 투명도 (불투명)
opacityAnimation.toValue = 0.0   // 종료 투명도 (완전히 투명)

// 애니메이션 그룹 생성
let animationGroup = CAAnimationGroup()
// 그룹에 위치 애니메이션과 투명도 애니메이션 추가
animationGroup.animations = [positionAnimation, opacityAnimation]
// 그룹 전체 지속 시간을 설정 (2초)
animationGroup.duration = 2.0

// 레이어에 애니메이션 그룹 추가
layer.add(animationGroup, forKey: "groupAnimation")
```

<br>
<br>

## 12.3 키 프레임 애니메이션(Keyframe Animation)과 스프링 애니메이션(Spring Animation)의 차이점은 무엇인가요?

<img src="https://github.com/user-attachments/assets/f5f9471e-08e8-49dd-a081-f73d44671f19">

<br>

#### 키 프레임 애니메이션 예제

```swift
// 키 프레임 애니메이션 생성 및 초기화
let keyframeAnimation = CAKeyframeAnimation(keyPath: "position")
// 애니메이션할 레이어 속성을 설정 (position: 위치를 애니메이션)

// 애니메이션 경로의 좌표를 설정
keyframeAnimation.values = [
    CGPoint(x: 50, y: 50),  // 시작 위치
    CGPoint(x: 150, y: 50), // 오른쪽으로 이동
    CGPoint(x: 150, y: 150), // 아래로 이동
    CGPoint(x: 50, y: 150),  // 왼쪽으로 이동
    CGPoint(x: 50, y: 50)   // 처음 위치로 돌아옴
]

// 애니메이션의 총 지속 시간을 설정 (3초)
keyframeAnimation.duration = 3.0

// `isAdditive`를 true로 설정하면, 이전 위치를 기준으로 값을 상대적으로 계산
keyframeAnimation.isAdditive = true

// 애니메이션을 레이어에 추가 (키를 사용하여 애니메이션 식별 가능)
layer.add(keyframeAnimation, forKey: "keyframeAnimation")
```


#### 스프링 애니메이션 예제

```swift
// 스프링 애니메이션 생성 및 초기화
let springAnimation = CASpringAnimation(keyPath: "position.y") 
// 애니메이션할 레이어 속성을 설정 (y축의 위치를 변경)

// 애니메이션 시작 값 (y축 위치를 100으로 설정)
springAnimation.fromValue = 100

// 애니메이션 종료 값 (y축 위치를 200으로 설정)
springAnimation.toValue = 200

// 감쇠 비율 설정 (값이 클수록 애니메이션의 진동이 더 빨리 멈춤)
springAnimation.damping = 5.0

// 초기 속도 설정 (값이 클수록 애니메이션이 더 빠르게 시작됨)
springAnimation.initialVelocity = 10.0

// 애니메이션 지속 시간을 자동 계산 (스프링 애니메이션이 안정화되는 시간)
springAnimation.duration = springAnimation.settlingDuration

// 레이어에 애니메이션 추가 (키를 통해 애니메이션 식별 가능)
layer.add(springAnimation, forKey: "springAnimation")
```

<br>

### 요약

Core Animation은 효율적인 애니메이션 구현을 위한 강력한 도구입니다.

- CALayer: 뷰의 기본 요소를 구성하고 애니메이션을 추가.
- Animation Group: 다중 애니메이션을 동시에 실행.
- Keyframe vs Spring: 중간 경로를 따라가는 키 프레임과 자연스러운 물리 효과를 제공하는 스프링 애니메이션은 상황에 맞게 선택하여 사용.


<br>
<br>

## 13. Swift에서 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)을 활용하는 방법은 무엇인가요?
프로토콜 지향 프로그래밍은 프로토콜을 사용하여 코드의 유연성과 재사용성을 극대화하는 Swift의 프로그래밍 패러다임입니다.
구조체, 클래스, 열거형 등 모든 타입이 프로토콜을 준수할 수 있으며, 프로토콜 확장과 기본 구현을 통해 중복 코드를 줄이고 모듈화된 코드를 작성할 수 있습니다.

<br>
<br>

## 13.1 프로토콜 확장(Protocol Extension)을 통해 기본 구현을 제공하는 방법을 설명해주세요.
프로토콜 확장을 사용하면 프로토콜을 채택한 타입에 대해 공통 동작을 기본 구현으로 제공할 수 있습니다.

#### 예제:
```swift
protocol Vehicle {
    var speed: Int { get }
    func description() -> String
}

// 프로토콜 확장을 통해 기본 구현 제공
extension Vehicle {
    func description() -> String {
        return "This vehicle moves at \(speed) km/h."
    }
}

// 프로토콜 채택한 타입들
struct Car: Vehicle {
    var speed: Int
}

struct Bicycle: Vehicle {
    var speed: Int
}

// 기본 구현 활용
let car = Car(speed: 120)
let bicycle = Bicycle(speed: 25)

print(car.description())       // 출력: This vehicle moves at 120 km/h.
print(bicycle.description())  // 출력: This vehicle moves at 25 km/h.
```

<br>

### 장점:
- 모든 Vehicle 타입이 description 메서드를 구현할 필요 없이, 공통 로직을 확장에서 제공받습니다.
- 중복 코드를 줄이고 유지보수를 쉽게 만듭니다.

<br>
<br>

## 13.2 프로토콜 상속(Protocol Inheritance)은 어떤 경우에 사용하나요?
프로토콜 상속은 하나의 프로토콜이 다른 프로토콜을 상속받아 기존 요구사항을 확장할 때 사용됩니다.

#### 예제
```swift
protocol Drawable {
    func draw()
}

protocol Colorable: Drawable {
    var color: String { get }
}

// `Colorable`은 `Drawable`을 상속받으므로, `draw` 메서드와 `color` 속성을 모두 구현해야 함
struct Circle: Colorable {
    var color: String

    func draw() {
        print("Drawing a \(color) circle")
    }
}

let circle = Circle(color: "red")
circle.draw() // 출력: Drawing a red circle
```

### 사용 시기:
- 기본 동작(Drawable)을 확장하여 추가 기능(Colorable)을 정의하고자 할 때.
- 계층 구조를 만들고 공통 동작과 추가 동작을 명확히 분리하고 싶을 때.

<br>
<br>

## 13.3 프로토콜 지향 프로그래밍(Protocol-Oriented Programming)에서 제네릭(Generic)을 함께 사용하면 어떤 이점이 있나요?

제네릭과 프로토콜을 함께 사용하면 타입에 상관없이 프로토콜을 준수하는 타입을 처리할 수 있습니다.
이로 인해 코드 재사용성을 극대화하고, 타입 안정성을 유지할 수 있습니다.

#### 예제

```swift
protocol Summable {
    static func +(lhs: Self, rhs: Self) -> Self
}

extension Int: Summable {}
extension Double: Summable {}
extension String: Summable {}

func sum<T: Summable>(_ a: T, _ b: T) -> T {
    return a + b
}

print(sum(5, 10))         // 출력: 15
print(sum(3.5, 2.5))      // 출력: 6.0
print(sum("Hello, ", "World!")) // 출력: Hello, World!
```

<br>

### 장점:
1. 다양한 타입(Int, Double, String 등)에 대해 동일한 로직을 적용할 수 있습니다.
2. 코드 재사용성을 극대화하고, 타입에 의존하지 않는 범용적인 코드를 작성할 수 있습니다.
3. 컴파일 타임에 타입 체크를 통해 안정성을 보장받을 수 있습니다.

<br>

### 요약
- 프로토콜 확장: 공통 동작을 기본 구현으로 제공하여 코드 중복 제거.
- 프로토콜 상속: 계층 구조를 통해 공통 동작과 추가 동작을 명확히 분리.
- 제네릭과의 조합: 타입에 독립적인 코드 작성으로 재사용성과 안정성을 높임.

<br>
<br>

## 14. iOS 앱에서 네트워크 요청 시 응답 캐싱(Response Caching)을 하는 방법은 무엇인가요?

> 응답 캐싱은 네트워크 요청에 대한 서버의 응답 데이터를 클라이언트 측에 저장하여, 동일한 요청이 다시 발생할 때 네트워크를 통해 데이터를 재요청하지 않고, 저장된 데이터를 재사용하는 기술입니다.

<br>

### 1. URLSession을 이용한 기본 캐싱
- URLSession은 HTTP 요청 및 응답에 대해 기본적으로 URLCache를 사용하여 캐싱을 지원합니다.
- HTTP 헤더(Cache-Control, ETag 등)를 통해 서버에서 캐싱 정책을 설정하고, 클라이언트는 이를 따릅니다.

#### 예제

```swift
import Foundation

// URLSession 구성 시 URLCache 설정
let cache = URLCache(memoryCapacity: 10 * 1024 * 1024,  // 10MB 메모리 캐시
                     diskCapacity: 50 * 1024 * 1024,   // 50MB 디스크 캐시
                     diskPath: "myCache")

let config = URLSessionConfiguration.default
config.urlCache = cache
config.requestCachePolicy = .returnCacheDataElseLoad // 캐시가 없으면 네트워크 요청

let session = URLSession(configuration: config)

let url = URL(string: "https://api.example.com/data")!

let task = session.dataTask(with: url) { data, response, error in
    if let error = error {
        print("Error:", error)
        return
    }
    if let data = data, let response = response {
        print("Data:", data)
        print("Response:", response)
    }
}

task.resume()
```

<br>

### 2. URLCache의 직접 활용
- URLCache를 사용하면 특정 요청에 대한 응답을 수동으로 저장하거나 조회할 수 있습니다.

#### 예제

```swift
// 캐시에 직접 응답 저장
let cache = URLCache.shared
let request = URLRequest(url: url)

let data = Data("Cached response".utf8)
let response = URLResponse(url: url,
                           mimeType: "text/plain",
                           expectedContentLength: data.count,
                           textEncodingName: nil)

// 캐시 저장
let cachedResponse = CachedURLResponse(response: response, data: data)
cache.storeCachedResponse(cachedResponse, for: request)

// 캐시에서 데이터 가져오기
if let cachedData = cache.cachedResponse(for: request)?.data,
   let cachedString = String(data: cachedData, encoding: .utf8) {
    print("Cached Data:", cachedString)
}
```

<br>

### 3. HTTP 헤더를 통한 캐싱 정책
- 서버와 클라이언트 간의 캐싱 정책은 HTTP 헤더로 정의됩니다.

#### 주요 HTTP 헤더
#### 1.	Cache-Control:
- 예: Cache-Control: public, max-age=3600
- public: 캐시 가능.
- max-age: 캐시 유효 기간(초 단위).

<br>

#### 2.	ETag:
- 데이터의 고유 식별자.
- 클라이언트는 If-None-Match 헤더로 서버에 변경 여부를 요청.

#### 예졔

```swift
var request = URLRequest(url: url)
request.addValue("application/json", forHTTPHeaderField: "Accept")
request.cachePolicy = .reloadIgnoringLocalCacheData // 캐시 무시하고 새 데이터 요청

let task = URLSession.shared.dataTask(with: request) { data, response, error in
    if let httpResponse = response as? HTTPURLResponse,
       let etag = httpResponse.allHeaderFields["ETag"] as? String {
        print("ETag:", etag)
    }
}

task.resume()
```

<br>


### 4. 캐시 정책 설정 옵션

URLRequest.CachePolicy를 사용하여 요청의 캐싱 정책을 정의할 수 있습니다:

- useProtocolCachePolicy (기본값): 서버의 캐싱 정책을 따릅니다.
- reloadIgnoringLocalCacheData: 캐시를 무시하고 항상 네트워크 요청.
- returnCacheDataElseLoad: 캐시가 있으면 캐시 사용, 없으면 네트워크 요청.
- returnCacheDataDontLoad: 캐시가 없으면 요청하지 않음.

<br>

### 5. 서드파티 라이브러리 활용
- Alamofire와 같은 라이브러리를 사용하여 캐싱을 보다 세밀하게 관리할 수 있습니다.

#### 예제: Alamofire에서 캐싱

```swift
import Alamofire

let url = "https://api.example.com/data"

AF.request(url, cachePolicy: .returnCacheDataElseLoad)
    .response { response in
        if let data = response.data {
            print("Data:", data)
        }
    }
```

<br>

### 요약
1. 기본적으로 URLSession과 URLCache를 활용하여 간단한 캐싱 구현 가능.
2. Cache-Control 및 ETag와 같은 HTTP 헤더를 통해 서버와 클라이언트 간 캐싱 정책을 관리.
3. 필요에 따라 URLCache를 직접 관리하거나 서드파티 라이브러리를 활용.


<br>
<br>

## 14.1 URLCache는 어떤 역할을 하나요?
URLCache는 iOS 네트워크 요청의 응답 데이터를 캐싱하기 위해 사용됩니다.
이는 네트워크 응답 데이터를 메모리와 디스크에 저장하여 동일한 요청이 반복될 때 네트워크 요청을 생략하고 캐싱된 데이터를 반환함으로써 성능을 최적화합니다.

<br>

### 주요 역할
#### 1.	네트워크 응답 데이터 저장:
- HTTP 요청/응답 헤더에 따라 캐싱 가능 여부와 유효 기간을 결정합니다.
#### 2.	캐싱된 데이터 반환:
- 동일한 요청이 있을 경우, 서버로 요청을 보내지 않고 로컬 캐시 데이터를 반환하여 성능을 향상시킵니다.
#### 3.	메모리와 디스크 관리:
- 메모리와 디스크 용량을 설정하여 캐싱 데이터를 효율적으로 관리합니다.

<br>

#### 예제 코드

```swift
let cache = URLCache(memoryCapacity: 10 * 1024 * 1024,  // 메모리 캐시 크기: 10MB
                     diskCapacity: 50 * 1024 * 1024,   // 디스크 캐시 크기: 50MB
                     diskPath: "urlCache")

let config = URLSessionConfiguration.default
config.urlCache = cache
config.requestCachePolicy = .returnCacheDataElseLoad // 캐시 사용 정책

let session = URLSession(configuration: config)

let url = URL(string: "https://api.example.com/data")!
let task = session.dataTask(with: url) { data, response, error in
    if let data = data {
        print("Received data: \(data)")
    }
}
task.resume()
```


<br>
<br>

## 14.2 응답 캐싱의 장단점은 무엇인가요?
### 장점
#### 1.	성능 향상:
- 네트워크 요청 횟수를 줄여 응답 속도를 개선.
- 사용자 경험(UX) 향상.
#### 2.	네트워크 비용 절감:
- 불필요한 네트워크 요청 제거로 데이터 전송량 감소.
#### 3.	서버 부하 감소:
- 서버로의 요청을 줄여 서버 리소스 절약.
#### 4.	오프라인 사용 가능:
- 네트워크가 끊긴 경우에도 캐싱된 데이터를 사용하여 기본적인 기능 제공.

<br>

### 단점

#### 1.	데이터 최신성 문제:
- 캐싱된 데이터가 오래된 경우, 최신 데이터가 아닌 잘못된 데이터를 반환할 수 있음.
#### 2.	메모리 및 디스크 사용량 증가:
- 캐시 데이터를 저장하기 위해 추가적인 저장 공간 필요.
#### 3.	복잡한 캐싱 정책:
- Cache-Control, ETag 등 캐싱 정책 설정이 올바르게 구현되지 않으면 비효율적일 수 있음.

<br>
<br>

## 14.3 응답 캐싱을 커스터마이징하는 방법을 설명해주세요.

### 1. URLCache 설정 커스터마이징

URLCache의 메모리 및 디스크 크기, 경로를 설정하여 캐싱 데이터를 효율적으로 관리할 수 있습니다.

```swift
let customCache = URLCache(memoryCapacity: 20 * 1024 * 1024,  // 메모리 캐시 크기: 20MB
                           diskCapacity: 100 * 1024 * 1024, // 디스크 캐시 크기: 100MB
                           diskPath: "customCachePath")

URLCache.shared = customCache
```

<br>

### 2. 요청 캐싱 정책 설정

URLRequest의 cachePolicy를 사용하여 캐싱 동작을 제어할 수 있습니다.

```swift
var request = URLRequest(url: URL(string: "https://api.example.com/data")!)
request.cachePolicy = .reloadIgnoringLocalCacheData // 캐시 무시하고 항상 네트워크 요청
```

<br>
### 캐싱 정책 옵션:
- .useProtocolCachePolicy: 기본값, 서버 정책을 따름.
- .reloadIgnoringLocalCacheData: 캐시 무시하고 네트워크 요청.
- .returnCacheDataElseLoad: 캐시가 있으면 캐시 사용, 없으면 네트워크 요청.
- .returnCacheDataDontLoad: 캐시만 사용, 네트워크 요청하지 않음.

<br>

### 3. 수동 캐싱 관리
캐싱 데이터를 수동으로 저장하거나 가져올 수 있습니다.

```swift
// 저장
let cache = URLCache.shared
let response = CachedURLResponse(response: response, data: data)
cache.storeCachedResponse(response, for: request)

// 가져오기
if let cachedResponse = cache.cachedResponse(for: request) {
    print("Cached Data:", cachedResponse.data)
}
```

<br>

### 4. 서버와 클라이언트의 캐싱 정책 협의

서버에서 HTTP 헤더를 사용해 캐싱 정책을 설정하고 클라이언트는 이를 따릅니다.

- Cache-Control: Cache-Control: public, max-age=3600
- ETag: 데이터 고유 식별자를 사용하여 최신 여부 확인.
- Last-Modified: 데이터 마지막 수정 시간 제공.

<br>

### 요약

1. URLCache는 네트워크 요청의 성능 최적화를 위해 사용되며, 메모리 및 디스크 캐싱을 관리합니다.
2. 응답 캐싱은 성능 향상 및 비용 절감의 장점이 있지만, 데이터 최신성 문제(와 추가 메모리 사용량의 단점이 있습니다.
3. URLCache와 캐싱 정책을 커스터마이징하여 캐싱 동작을 세밀하게 제어할 수 있습니다.

<br>
<br>

## 캐싱 최신데이터 관리는 어떻게 하나?
iOS에서 데이터를 최신 상태로 유지하는 문제를 해결하기 위한 방법은 ETag뿐만 아니라 여러 전략이 존재합니다. 각각의 방법은 특정 상황과 요구 사항에 적합하며, 서버와의 효율적인 통신과 사용자 경험 향상을 목표로 합니다.

<br>

### 1. ETag를 사용하는 방법
- ETag는 서버와 클라이언트가 데이터를 동기화하는 가장 일반적인 방식 중 하나입니다.
- 변경되지 않은 데이터에 대해서는 304 Not Modified 응답을 통해 데이터 본문을 생략하므로 네트워크 트래픽 감소.

<br>

#### 장점:
- 데이터 변경 여부만 확인 가능.
- 모든 HTTP 기반 네트워크 요청에 적용 가능.

<br>

#### 단점:
- 네트워크 요청은 여전히 필요.
- 최신 데이터 여부 확인까지 지연 시간이 발생할 수 있음.

<br>

### 2. Last-Modified 헤더
- Last-Modified 헤더는 데이터가 마지막으로 변경된 시간을 클라이언트에 전달합니다.
- 클라이언트는 If-Modified-Since 헤더를 통해 서버에 마지막으로 확인한 시간을 전달합니다.

#### 서버 응답:
- 데이터가 변경되지 않은 경우: 304 Not Modified.
- 데이터가 변경된 경우: 200 OK와 새로운 데이터.

```http
Request:
GET /data HTTP/1.1
If-Modified-Since: Tue, 03 Oct 2023 10:15:00 GMT

Response:
HTTP/1.1 304 Not Modified
```

#### 장점:
- 구현이 간단하며 모든 HTTP 클라이언트에서 지원.
- ETag와 동일한 최적화를 제공.

#### 단점:
- 시간 동기화 문제(서버-클라이언트 시간 차이 발생 가능).
- 변경된 데이터인지 확인하는 정확도는 ETag보다 낮음.

<br>

### 3. HTTP 캐싱 정책 (Cache-Control)

Cache-Control 헤더를 사용해 데이터의 유효 기간을 설정합니다.

- max-age=N: 캐시 데이터가 유효한 최대 시간(초).
- no-cache: 항상 서버에서 데이터를 유효성 검사 후 사용.
- must-revalidate: 캐시 데이터가 만료되면 서버 확인 필요.

#### 사용 예시:

```http
Cache-Control: max-age=3600, must-revalidate
```

<br>

#### iOS에서 적용:
- URLSession의 기본 캐시 동작은 서버에서 제공하는 Cache-Control 헤더를 따릅니다.

#### 장점:
- 특정 시간 동안 서버 요청을 생략 가능.
- 사용자 경험 개선(빠른 응답).

#### 단점:
- 데이터가 만료되기 전까지는 최신 데이터가 보장되지 않음.

<br>

### 4. WebSocket을 활용한 실시간 업데이트
- WebSocket은 서버-클라이언트 간 양방향 통신을 제공하여 실시간 데이터 업데이트를 지원합니다.
- 데이터가 변경되면 서버에서 클라이언트로 즉시 푸시.

#### iOS에서 구현 예시:
```swift
import Foundation

let url = URL(string: "wss://example.com/socket")!
let webSocketTask = URLSession.shared.webSocketTask(with: url)

webSocketTask.resume()

func listenForMessages() {
    webSocketTask.receive { result in
        switch result {
        case .success(let message):
            switch message {
            case .string(let text):
                print("Received text: \(text)")
            default:
                break
            }
        case .failure(let error):
            print("Error: \(error)")
        }
        listenForMessages() // Keep listening
    }
}

listenForMessages()
```

<br>

### 5. 서버 푸시(Notification Center) -> 이건 쫌? 
- 서버는 데이터가 변경될 때 클라이언트로 푸시 알림을 전송.
- 푸시 알림을 통해 클라이언트는 최신 데이터 요청을 실행.

#### 장점:
- 불필요한 주기적 네트워크 요청 제거.
- 실시간 데이터 갱신 가능.

#### 단점:
- 푸시 알림 설정 필요(Apple Push Notification Service 사용).
- 푸시 알림이 사용자 환경(기기 상태, 네트워크)에 따라 수신되지 않을 수 있음.

<br>

### 6. GraphQL 구독(Subscriptions) -> 네트워크와 데이터를 아끼려다 WebSocket까지..??
- GraphQL의 Subscription 기능을 사용하면, 데이터 변경 시 서버가 클라이언트로 실시간 데이터를 전송.
- WebSocket 기반으로 동작.

#### 장점:
- 실시간 데이터 변경 전파.
- 필요한 데이터만 요청(Over-fetching 방지).

#### 단점:
- GraphQL 서버 설정 필요.
- WebSocket 유지 비용 발생.

<br>

### 7. 서버-클라이언트 동기화

전략: 클라이언트에서 로컬 데이터를 저장(Core Data, SQLite 등)하고, 서버와 데이터를 정기적으로 동기화.

```swift
// 로컬 데이터 사용
func fetchCachedData() -> [Model] {
    // Core Data or SQLite에서 데이터 로드
}

// 서버 동기화
func syncWithServer() {
    fetchFromServer { serverData in
        // 로컬 데이터와 비교 후 업데이트
    }
}
```

#### 장점:
- 오프라인 모드에서도 데이터 제공.
- 최신 데이터 동기화 가능.

#### 단점:
- 충돌 해결 로직 필요(서버와 로컬 데이터 차이 발생 시).

<br>

### 최신성 문제를 해결하는 가장 일반적인 방법

#### 조합 전략
- ETag + 로컬 캐싱: 불필요한 네트워크 트래픽 감소.
- WebSocket or Push Notification: 실시간 데이터 업데이트.
- HTTP 캐싱 정책: 유효 기간 동안 최신성 확인 생략.
- 서버 동기화: 로컬 데이터 기반 오프라인 모드 지원.

<br>

#### 최적의 선택
1. 정적 데이터: ETag 또는 Cache-Control로 효율적인 네트워크 사용.
2. 자주 변경되는 데이터: WebSocket이나 Push Notification으로 실시간 동기화.
3. 오프라인 지원: 로컬 캐싱(Core Data, SQLite)과 동기화 전략 결합.

이 모든 전략은 상황에 따라 조합하여 사용하는 것이 일반적입니다. iOS에서는 URLSession과 URLCache를 활용한 기본 캐싱부터, WebSocket 및 로컬 데이터 동기화까지 다양한 방식으로 최신 데이터를 유지할 수 있습니다.

<br>
<br>

## 15. Combine 프레임워크란 무엇이며, 어떤 기능을 제공하나요?
Combine은 Apple이 제공하는 선언적 프레임워크로, 비동기 작업과 이벤트 스트림을 처리하는 데 사용됩니다.
Publisher와 Subscriber 간의 관계를 통해 데이터를 비동기적으로 처리하고, Operator를 사용하여 데이터 변환과 조작을 제공합니다.


<br>
<br>

## 15.1 Publisher와 Subscriber의 역할은 무엇인가요?

### Publisher
#### Publisher의 역할
- Publisher는 데이터를 발행하는 역할을 담당합니다.
- 이벤트나 데이터의 흐름(Stream)을 생성하고, 이를 Subscriber에게 전달합니다.
- 데이터를 전달하는 중 발생할 수 있는 완료(Completion) 또는 오류(Error) 상태도 관리합니다.

<br>

#### Publisher의 주요 특징
1. 데이터 제공:
- 연속적 또는 단일 데이터를 제공합니다.
2. 구독 관리:
- Subscriber가 구독을 시작하면 데이터를 스트리밍합니다.
3. 중단 처리:
- 정상적으로 완료되거나 에러가 발생하면 데이터 스트리밍을 중단합니다.

<br>

### Subscriber
#### Subscriber의 역할
- Subscriber는 Publisher에서 발행된 데이터를 수신하고 처리하는 역할을 합니다.
- 데이터를 받고 나서 화면 업데이트, 로직 처리, 에러 처리 등을 수행합니다.

#### Subscriber의 주요 특징
1. 데이터 수신:
- Publisher가 발행한 데이터를 받아와 처리합니다.
2. 완료/에러 처리:
- Publisher로부터 전달된 완료 이벤트 또는 에러를 처리합니다.
3. 구독 해지:
- 구독을 중단하여 Publisher와의 연결을 끊을 수 있습니다.

<br>

### Publisher와 Subscriber 간의 관계
- Publisher가 데이터를 제공하면, Subscriber는 이를 처리합니다.
- Publisher는 데이터 스트림을 제어하고, Subscriber는 해당 데이터를 구독하며 필요한 작업을 수행합니다.

#### 예제: Combine을 사용한 Publisher와 Subscriber

```swift
import Combine

// 1. Publisher 생성
let myPublisher = ["Hello", "Combine", "Framework"].publisher

// 2. Subscriber 생성
let mySubscriber = myPublisher
    .sink(receiveCompletion: { completion in
        // 데이터 스트리밍이 완료되거나 에러 발생 시 처리
        switch completion {
        case .finished:
            print("Stream finished!")
        case .failure(let error):
            print("Error occurred: \(error)")
        }
    }, receiveValue: { value in
        // 데이터를 수신하여 처리
        print("Received value: \(value)")
    })

// 출력:
// Received value: Hello
// Received value: Combine
// Received value: Framework
// Stream finished!
```

<br>

#### Publisher와 Subscriber의 동작 과정
1. Publisher 생성:
	- 데이터를 발행하기 위한 Publisher 객체를 생성.
2. Subscriber 등록:
	- sink 또는 assign 메서드를 통해 Subscriber를 등록.
3. 데이터 전달:
	- Publisher가 데이터를 Subscriber에 스트리밍.
4. 완료 또는 에러 처리:
	- 모든 데이터를 발행하면 .finished, 에러가 발생하면 .failure로 알림.

<br>

### Publisher와 Subscriber의 상호작용을 요약하면
- Publisher: 데이터를 발행.
- Subscriber: 데이터를 수신하여 로직 수행.
- Combine을 통해 데이터를 간단하고 효율적으로 스트리밍 및 처리 가능.

<br>

## Publisher와 Subscriber의 종류 및 설명

### Publisher의 종류

Combine 프레임워크에서 제공하는 Publisher는 여러 가지 유형으로 나뉩니다. 대표적인 종류는 다음과 같습니다:

### 1. Built-in Publisher (내장된 Publisher)

#### 1. Just
- 단일 값을 한 번만 발행하고 완료합니다.
- 데이터 흐름이 단순한 경우 사용됩니다.

예시: 
```swift
let publisher = Just("Hello Combine")
publisher.sink { value in
    print(value)
}
// 출력: Hello Combine
```

<br>

#### 2.	Future
- 단일 값 또는 에러를 비동기로 발행합니다.
- 주로 네트워크 요청 같은 비동기 작업에 사용됩니다.
#### 예시:

```swift
let future = Future<String, Error> { promise in
    DispatchQueue.global().async {
        promise(.success("Data Loaded"))
    }
}
future.sink(
    receiveCompletion: { print($0) },
    receiveValue: { print($0) }
)
// 출력: Data Loaded
```

<br>

#### 3.	PassthroughSubject
- 직접 데이터를 발행할 수 있는 Publisher.
- 다른 객체가 데이터를 발행해야 할 때 사용됩니다.
#### 예시:

```swift
let subject = PassthroughSubject<String, Never>()
subject.sink { print($0) }
subject.send("Hello")
// 출력: Hello
```

<br>

#### 4.	CurrentValueSubject
- 초기 값을 가지며, 현재 값과 새로운 값을 발행할 수 있습니다.
- 주로 상태 관리에 유용.
#### 예시:

```swift
let subject = CurrentValueSubject<Int, Never>(0)
subject.sink { print($0) }
subject.send(1)
// 출력: 0, 1
```

<br>

#### 5.	Empty
- 데이터를 발행하지 않고 즉시 완료됩니다.
- 기본 값이 없는 상황에서 주로 사용.
#### 예시:
```swift
let emptyPublisher = Empty<Int, Never>()
emptyPublisher.sink(
    receiveCompletion: { print("Completed") },
    receiveValue: { print($0) }
)
// 출력: Completed
```

<br>

#### 6.	Deferred
- Publisher를 구독 시점에서 생성.
- 주로 동적 데이터가 필요한 경우 사용.
#### 예시:

```swift
let deferredPublisher = Deferred {
    Just("Deferred Data")
}
deferredPublisher.sink { print($0) }
// 출력: Deferred Data
```

<br>

#### 7.	Timer
- 일정한 간격으로 값을 발행하는 Publisher.
#### 예시:

```swift
let timer = Timer.publish(every: 1.0, on: .main, in: .default)
let cancellable = timer.sink { _ in
    print("Timer fired")
}
timer.connect()
```

<br>

### 2. Custom Publisher
직접 커스텀 Publisher를 만들 수 있습니다.

#### 예시:
```swift
struct CustomPublisher: Publisher {
    typealias Output = String
    typealias Failure = Never

    func receive<S>(subscriber: S) where S : Subscriber, Never == S.Failure, String == S.Input {
        subscriber.receive(subscription: Subscriptions.empty)
        _ = subscriber.receive("Custom Publisher")
        subscriber.receive(completion: .finished)
    }
}

let customPublisher = CustomPublisher()
customPublisher.sink { print($0) }
// 출력: Custom Publisher
```

<br>

### Subscriber의 종류
Combine에서 Subscriber는 특정 값을 수신하고 이를 처리하는 역할을 합니다. 직접 Subscriber를 작성하거나 기본 제공 메서드를 사용할 수 있습니다.

### 1. Built-in Subscriber (내장된 Subscriber)

#### 1.	sink
- 가장 많이 사용되는 Subscriber로 데이터를 수신하고 처리합니다.
#### 예시:
```swift
let publisher = Just("Hello Combine")
publisher.sink { value in
    print(value)
}
// 출력: Hello Combine
```

<br>

#### 2.	assign
- 데이터를 특정 프로퍼티에 바인딩합니다.
- 주로 ViewModel이나 UI 업데이트에 사용됩니다.
#### 예시:

```swift
class ViewModel: ObservableObject {
    @Published var message: String = ""
}

let viewModel = ViewModel()
Just("Hello Combine").assign(to: \.message, on: viewModel)
print(viewModel.message)
// 출력: Hello Combine
```

<br>

### 2. Custom Subscriber
직접 Subscriber를 구현할 수 있습니다.

#### 예시: 
```swift
struct CustomSubscriber: Subscriber {
    typealias Input = String
    typealias Failure = Never

    func receive(subscription: Subscription) {
        subscription.request(.unlimited) // 요청할 데이터 수 설정
    }

    func receive(_ input: String) -> Subscribers.Demand {
        print("Received value: \(input)")
        return .none
    }

    func receive(completion: Subscribers.Completion<Never>) {
        print("Completed")
    }
}

let publisher = Just("Custom Subscriber Example")
let subscriber = CustomSubscriber()
publisher.subscribe(subscriber)
// 출력: Received value: Custom Subscriber Example
// 출력: Completed
```

<br>

### Publisher와 Subscriber의 관계 요약
1. Publisher는 데이터를 발행하고, Subscriber는 이를 구독하여 처리합니다.
2. Built-in Publisher와 Subscriber를 통해 대부분의 작업을 처리할 수 있습니다.
3. 필요할 경우 Custom Publisher와 Custom Subscriber를 구현해 특정 요구사항을 충족할 수 있습
니다.

<br>
<br>

## 15.2 Operator의 종류와 사용 방법을 설명해주세요.

Combine의 Operator는 Publisher에서 발행된 데이터를 변환, 필터링, 결합, 스케줄링하는 데 사용됩니다. Operator는 체이닝 방식으로 데이터를 처리하며 선언적 프로그래밍 스타일을 제공합니다.

### 1. Transforming Operators (데이터 변환)

#### 1.	map
- 발행된 데이터를 변환합니다.

#### 예시: 
```swift
let numbers = [1, 2, 3].publisher
numbers
    .map { $0 * 2 } // 데이터를 2배로 변환
    .sink { print($0) }
// 출력:
// 2
// 4
// 6
```

<br>

#### 2.	flatMap
- 새로운 Publisher로 데이터를 변환합니다.
- flatMap은 배열, 옵셔널, 혹은 여러 레벨의 데이터 스트럭처를 평평하게 만들어주는 역할

#### 예시: 
```swift
let nestedArrays = [[1, 2, 3], [4, 5, 6]].publisher

nestedArrays
    .flatMap { $0.publisher } // 각 중첩된 배열을 펼쳐서 단일 Publisher로 변환
    .sink { print($0) }

// 출력:
// 1
// 2
// 3
// 4
// 5
// 6
```

<br>

#### 3.	compactMap
- nil 값을 제거하고 나머지 값을 전달합니다.

#### 예시: 
```swift
let strings = ["1", "2", "Swift", "3"].publisher
strings
    .compactMap { Int($0) } // 문자열을 정수로 변환, 실패 시 nil 제거
    .sink { print($0) }
// 출력:
// 1
// 2
// 3
```

<br>

### 2. Filtering Operators (데이터 필터링)

#### 1.	filter
- 특정 조건을 만족하는 값만 전달.
#### 예시:

```swift
let numbers = [1, 2, 3, 4, 5].publisher
numbers
    .filter { $0 % 2 == 0 } // 짝수만 전달
    .sink { print($0) }
// 출력:
// 2
// 4
```

<br>

#### 2.	removeDuplicates
- 연속된 중복 값을 제거.
#### 예시:

```swift
let numbers = [1, 1, 2, 2, 3, 3].publisher
numbers
    .removeDuplicates()
    .sink { print($0) }
// 출력:
// 1
// 2
// 3
```

<br>

### 3. Combining Operators (데이터 결합)

#### 1.	merge
	•	여러 Publisher를 결합해 순서에 상관없이 데이터를 발행.
#### 예시:

```swift
let pub1 = [1, 2, 3].publisher
let pub2 = [4, 5, 6].publisher
pub1.merge(with: pub2).sink { print($0) }
// 출력:
// 1
// 2
// 3
// 4
// 5
// 6
```

<br>

#### 2.	combineLatest
- 두 Publisher의 최신 값을 결합.

#### 예시

```swift
let pub1 = CurrentValueSubject<Int, Never>(1)
let pub2 = CurrentValueSubject<String, Never>("A")
pub1.combineLatest(pub2).sink { print("Value: \($0), \($1)") }
pub1.send(2)
pub2.send("B")
// 출력: Value: 1, A
// 출력: Value: 2, A
// 출력: Value: 2, B
```

<br>

#### 3. zip
- 두 Publisher 의 데이터를 짝 지어 발행.

#### 예시:
```swift
let pub1 = [1, 2, 3].publisher
let pub2 = ["A", "B", "C"].publisher
pub1.zip(pub2).sink { print("\($0), \($1)") }
// 출력: 1, A
// 출력: 2, B
// 출력: 3, C
```

<br>

### 4. Error Handling Operators (에러 처리)

#### 1. catch
- 에러가 발생하면 다른 Publisher로 대체.

#### 예시:

```swift
let failingPublisher = Fail<Int, Error>(error: NSError(domain: "", code: -1, userInfo: nil))
failingPublisher
    .catch { _ in Just(0) } // 에러 발생 시 0을 발행
    .sink { print($0) }
// 출력: 0
```

<br>

#### 2. retry
- 에러 발생 시 작업을 재시도

#### 예시:

```swift
var attempt = 0
let retryPublisher = Future<Int, Error> { promise in
    attempt += 1
    if attempt < 3 {
        promise(.failure(NSError(domain: "", code: -1, userInfo: nil)))
    } else {
        promise(.success(42))
    }
}
.retry(3)

retryPublisher.sink(
    receiveCompletion: { print($0) },
    receiveValue: { print($0) }
)
// 출력: 42
```

<br>

### 5. Scheduling Operators (스레드 관리)

#### 1.	subscribe(on:)
- 데이터 생성의 실행 스레드를 지정.
#### 예시:

```swift
Just("Hello Combine")
    .subscribe(on: DispatchQueue.global()) // 백그라운드 스레드에서 데이터 생성
    .sink { print($0) }
// 출력: Hello Combine (백그라운드에서 생성)
```

<br>

#### 2.	receive(on:)
- 데이터 처리를 수행할 스레드를 지정.
#### 예시:
```swift
Just("UI Update")
    .receive(on: DispatchQueue.main) // 메인 스레드에서 UI 업데이트
    .sink { print($0) }
// 출력: UI Update (메인 스레드에서 처리)
```

<br>

### Combine Operator 사용의 장점
- 데이터를 직관적이고 효율적으로 변환, 필터링, 결합 가능.
- 코드가 간결하며, 체이닝 방식으로 복잡한 데이터 흐름을 쉽게 처리 가능.
- 다양한 내장 Operator를 활용해 비동기 작업 처리에 강력한 유연성을 제공.


<br>
<br>

## 15.3 Combine과 RxSwift의 차이점은 무엇인가요?
Combine과 RxSwift는 모두 **반응형 프로그래밍(Reactive Programming)** 을 지원하는 프레임워크입니다. 하지만, 두 프레임워크는 철학, 설계, 그리고 플랫폼 통합 측면에서 차이가 있습니다. 아래에서 주요 차이점을 설명하고, 각각의 사용 사례를 제시합니다.

<br>

### 1. Combine과 RxSwift의 철학과 설계
<img src="https://github.com/user-attachments/assets/2498eac2-9d89-4295-a746-f4db2d223204">

<br>

### 2. 주요 차이점
#### a. 플랫폼 의존성
- Combine: Apple에서 제공하므로 iOS 13+, macOS 10.15+ 등 Apple 생태계에서만 사용 가능.
- RxSwift: 오픈소스이므로 iOS, Android, Web 등 다양한 플랫폼에서 사용 가능.

<br>

#### b. 내장 연산자 (Operators)
- Combine: 필요한 주요 연산자 중심으로 제공.
- map, filter, merge, combineLatest, flatMap 등.
- RxSwift: 훨씬 더 많은 연산자를 제공하며, 복잡한 데이터 흐름을 처리하기 위한 고급 연산자 지원.
- 예: withLatestFrom, amb, retryWhen 등.

<br>

#### c. 메모리 관리
- Combine: Cancellable 객체를 통해 구독을 관리하며, 기본적으로 ARC와 잘 통합됨.
- RxSwift: DisposeBag을 통해 메모리 관리를 수동으로 처리해야 함.

#### 예시 (Combine):

```swift
let publisher = Just("Hello, Combine!")
let subscription = publisher.sink { value in
    print(value)
}
// subscription이 자동으로 ARC에 의해 관리됨
```

#### 예시 (RxSwift):
```swift
let disposeBag = DisposeBag()
let observable = Observable.just("Hello, RxSwift!")
observable.subscribe(onNext: { value in
    print(value)
}).disposed(by: disposeBag)
// DisposeBag에 의해 메모리 관리
```

<br>

#### d.학습 곡선
- Combine: Swift 표준 라이브러리의 설계를 따르며, API가 직관적.
- RxSwift: API가 강력하지만, 다양한 연산자와 Rx의 개념을 이해하는 데 시간이 더 걸림.

<br>

### 3. 사용 사례

#### Combine 사용 사례

- iOS 13+ 이상에서 새로운 프로젝트를 개발하는 경우.
- Apple의 생태계와 밀접하게 통합된 기능 사용.
- 예: SwiftUI, URLSession, NotificationCenter, Core Data.

#### Combine 예제:
```swift
import Combine

let publisher = ["Apple", "Google", "Microsoft"].publisher
publisher
    .filter { $0.hasPrefix("A") }
    .sink { print($0) }
// 출력: Apple
```

<br>

#### RxSwift 사용 사례
- 다양한 플랫폼에서 일관된 API로 반응형 프로그래밍을 구현해야 할 때.
- 복잡한 데이터 흐름이 필요한 경우, 고급 연산자 활용 가능.

#### RxSwift 예제:

```swift
import RxSwift

let observable = Observable.of("Apple", "Google", "Microsoft")
observable
    .filter { $0.hasPrefix("A") }
    .subscribe(onNext: { print($0) })
    .disposed(by: DisposeBag())
// 출력: Apple
```

<br>

### 4. Combine과 RxSwift의 선택 기준

#### Combine을 선택할 때
- 프로젝트가 iOS 13+ 이상을 타겟팅.
- Apple 생태계와 강하게 연동.
- 간결한 API와 Swift 표준 통합을 선호.

#### RxSwift를 선택할 때

- 멀티플랫폼 지원이 필요.
- 복잡한 데이터 흐름과 고급 연산자가 요구됨.
- 레거시 프로젝트에 통합하거나 Swift 5 이전 프로젝트.

<br>

### 결론
- Combine은 Apple 생태계에 최적화된 최신 기술로, Swift와의 통합성과 단순성이 강점.
- RxSwift는 더 많은 기능과 유연성을 제공하며, 다양한 플랫폼에서 활용 가능.
- 프로젝트의 요구사항에 따라 적합한 프레임워크를 선택하여 사용하면 됩니다.

<br>
<br>

## 16. Swift의 제네릭(Generic)에 대해 설명해주세요.

### 개념

제네릭은 특정 타입에 의존하지 않고, 여러 타입에서 동작할 수 있는 코드 작성 방법입니다. 코드 중복을 줄이고, 타입 안정성을 보장하며, 더 유연하고 재사용 가능한 코드를 작성할 수 있게 합니다.

### 장점
1. 코드 중복 제거: 같은 로직을 여러 타입에 대해 작성할 필요가 없습니다.
2. 타입 안정성: 컴파일러가 타입 체크를 수행하여 런타임 오류를 줄입니다.
3. 유연성: 다양한 타입에서 동작 가능.

#### 예시

#### 제네릭 사용 전
```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let temp = a
    a = b
    b = temp
}

// 두 함수는 로직이 동일하지만 타입이 달라 중복 코드 발생.
```

#### 제네릭 사용 후
```swift
// 제네릭으로 하나의 함수로 다양한 타입을 처리
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

var int1 = 10, int2 = 20
swapTwoValues(&int1, &int2)
print("int1: \(int1), int2: \(int2)") // 출력: int1: 20, int2: 10

var str1 = "Hello", str2 = "World"
swapTwoValues(&str1, &str2)
print("str1: \(str1), str2: \(str2)") // 출력: str1: World, str2: Hello
```


<br>
<br>

## 16.1 제네릭 타입 파라미터(Generic Type Parameter)와 제네릭 타입 제약(Generic Type Constraint)은 무엇인가요?

### 1. 제네릭 타입 파라미터
- 함수, 구조체, 클래스 등에서 사용할 수 있는 임의의 타입을 나타냅니다.
- 제네릭 타입 파라미터는 보통 T로 표기하지만, 의미를 나타내기 위해 더 구체적인 이름도 사용 가능합니다.

#### 예시:
```swift
func display<T>(value: T) {
    print("Value: \(value)")
}

display(value: 42)         // 정수 전달
display(value: "Swift")    // 문자열 전달
```

<br>

### 2. 제네릭 타입 제약
- 제네릭 타입에 특정 프로토콜이나 타입을 만족해야 한다는 조건을 설정합니다.
- where 문을 사용해 추가 제약을 설정할 수도 있습니다.

#### 예시: Comparable 프로토콜 제약

```swift
func findMaximum<T: Comparable>(in array: [T]) -> T? {
    guard let first = array.first else { return nil }
    return array.reduce(first) { $0 > $1 ? $0 : $1 }
}

let numbers = [1, 3, 2, 5, 4]
if let max = findMaximum(in: numbers) {
    print("Maximum: \(max)") // 출력: Maximum: 5
}

let strings = ["apple", "banana", "cherry"]
if let max = findMaximum(in: strings) {
    print("Maximum: \(max)") // 출력: Maximum: cherry
}
```

<br>

#### 예시: where 절로 추가 제약
```swift
// where 절로 제네릭 타입을 Equatable로 제약 사항 추가 함.
func areAllEqual<T>(array: [T]) -> Bool where T: Equatable { 
    guard let first = array.first else { return true }
    return array.allSatisfy { $0 == first }
}

print(areAllEqual(array: [1, 1, 1]))       // 출력: true
print(areAllEqual(array: [1, 2, 1]))       // 출력: false
```


<br>
<br>

## 16.2 제네릭을 사용할 때 주의할 점은 무엇인가요?

### 1.	불필요한 제네릭 사용 피하기
- 제네릭을 사용할 필요가 없는 경우에는 오히려 코드가 복잡해질 수 있습니다.
- 예시:

```swift
// 불필요한 제네릭 사용
func printValue<T>(_ value: T) {
    print(value)
}
// 단순 타입으로 작성 가능
func printValue(_ value: Any) {
    print(value)
}
```

<br>

### 2.	제네릭 타입 제약 설정
- 제네릭으로 모든 타입을 허용하면 런타임 오류 가능성이 커질 수 있습니다. 타입 제약을 설정해 컴파일 타임에 체크하도록 설계.

<br>

### 3.	코드 가독성
- 지나치게 복잡한 제네릭 타입 설계는 코드의 이해를 어렵게 할 수 있습니다.

<br>

### 4.	타입 추론 오류
- 제네릭을 사용할 때 Swift의 타입 추론이 실패하는 경우, 명시적으로 타입을 지정해야 합니다.

<br>

### 주의점 예시

#### 제네릭 타입 제약 없이 잘못된 코드
```swift
func add<T>(_ a: T, _ b: T) -> T {
    return a + b // 오류: '+' 연산자가 정의되지 않음
}
```

<br>

#### 타입 제약 추가 후

```swift
func add<T: Numeric>(_ a: T, _ b: T) -> T {
    return a + b
}

let intSum = add(5, 3)         // 8
let doubleSum = add(3.5, 2.5)  // 6.0
```

<br>

### 제네릭 사용 요약
1. 제네릭 타입 파라미터로 다양한 타입을 처리.
2. 제네릭 타입 제약으로 안전하고 유연한 설계.
3. 필요 이상의 제네릭 사용은 피하고, 가독성을 고려해 작성.

Swift의 제네릭은 코드 재사용성과 타입 안전성을 높이며, 다양한 상황에서 유연하게 활용됩니다.


<br>

### 제네릭 선언부에서 제약 VS where 절을 사용하는 경우 
- 간결함과 가독성을 위해 단일 제약 조건은 제네릭 선언부에 작성.
- 확장성과 복잡성을 고려해야 한다면 where 절 사용.
- 두 방식 모두 Swift에서 기능적으로 동일하며, 상황에 맞게 선택하면 됩니다.

<br>
<br>

## 17. iOS 앱에서 로컬 푸시 알림(Local Push Notification)을 구현하는 방법은 무엇인가요?
로컬 푸시 알림은 서버를 사용하지 않고 사용자 디바이스에서 직접 알림을 예약 및 전송하는 방식입니다. 로컬 푸시 알림은 주로 리마인더, 일정 알림, 타이머 알림 등에 사용됩니다.

### 1. 로컬 푸시 알림의 주요 단계
1. 권한 요청: 사용자가 알림을 받을 수 있도록 권한을 요청.
2. 알림 생성: UNNotificationRequest 객체로 알림을 구성.
3. 알림 스케줄링: UNUserNotificationCenter를 사용해 알림 예약.
4. 알림 처리: 알림을 클릭하거나 수신했을 때 처리하는 동작 정의.

<br>

### 2. 구현 방법
#### a. 권한 요청
```swift
import UserNotifications

func requestNotificationPermission() {
    let center = UNUserNotificationCenter.current()
    center.requestAuthorization(options: [.alert, .sound, .badge]) { granted, error in
        if let error = error {
            print("Notification permission error: \(error.localizedDescription)")
        } else {
            print("Permission granted: \(granted)")
        }
    }
}

// 앱 실행 시 호출
requestNotificationPermission()
```

<br>

#### b. 로컬 푸시 알림 생성 및 스케줄링

```swift
func scheduleLocalNotification() {
    // 1. 알림 콘텐츠 생성
    let content = UNMutableNotificationContent()
    content.title = "로컬 푸시 알림"
    content.body = "알림 내용을 여기에 작성합니다."
    content.sound = .default
    content.badge = 1

    // 2. 알림 트리거 설정 (예: 5초 뒤에 알림)
    let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 5, repeats: false)

    // 3. 알림 요청 생성
    let request = UNNotificationRequest(identifier: "LocalNotification", content: content, trigger: trigger)

    // 4. 요청을 UNUserNotificationCenter에 추가
    UNUserNotificationCenter.current().add(request) { error in
        if let error = error {
            print("Error scheduling notification: \(error.localizedDescription)")
        } else {
            print("Notification scheduled successfully!")
        }
    }
}

// 로컬 푸시 알림 스케줄링
scheduleLocalNotification()
```

<br>

#### c. 알림 클릭 처리

```swift
import UserNotifications

class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // UNUserNotificationCenter 델리게이트 설정
        UNUserNotificationCenter.current().delegate = self
        return true
    }

    // 알림을 클릭했을 때 호출되는 메서드
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        print("User clicked the notification: \(response.notification.request.identifier)")
        completionHandler()
    }
}
```
<br>

### 3. 코드 설명
#### UNUserNotificationCenter:
- 로컬 및 원격 알림을 관리하는 주요 API.
- 알림 권한 요청, 알림 추가 및 제거, 알림 처리 등 수행.

<br>

#### UNNotificationRequest:
- 알림 요청을 나타내는 객체.
- content와 trigger를 포함.

<br>

#### UNMutableNotificationContent:
- 알림의 제목, 본문, 소리, 배지 등을 정의.

<br>

#### Trigger 종류:
- UNTimeIntervalNotificationTrigger: 특정 시간 간격 뒤에 알림을 트리거.
- UNCalendarNotificationTrigger: 날짜/시간 기반 알림.
- UNLocationNotificationTrigger: 위치 기반 알림 (사용자 위치 도달 시).

<br>

### 4. 사용 사례
#### 리마인더 알림
- 사용자가 설정한 일정에 따라 알림을 스케줄링.

<br>

#### 타이머 알림
- 타이머 종료 후 사용자에게 알림.

<br>


#### 위치 기반 알림
- 특정 장소 근처에 도달했을 때 알림.

```swift
let triggerDate = DateComponents(calendar: Calendar.current, hour: 8, minute: 0)
let calendarTrigger = UNCalendarNotificationTrigger(dateMatching: triggerDate, repeats: true)
```

<br>

### 5. 주의 사항
1. 권한 확인: 사용자가 알림 권한을 거부하면 알림을 보낼 수 없습니다.
2. 알림 식별자 관리: 동일한 식별자를 사용하면 이전 알림이 대체됩니다.
3. 백그라운드 실행 제한: 앱이 종료되어도 스케줄링된 알림은 시스템에서 관리합니다.
4. 배터리 및 리소스 사용: 너무 잦은 알림은 사용자 경험을 해칠 수 있습니다.

로컬 푸시 알림은 서버와 통신하지 않아 네트워크가 필요 없는 간단한 알림 작업에 유용합니다.

<br>
<br>

## 17.1 로컬 푸시 알림과 원격 푸시 알림(Remote Push Notification)의 차이점은 무엇인가요?

<img src="https://github.com/user-attachments/assets/ec902905-c1de-4e67-bb4f-3ff956d0b42c">


<br>
<br>

## 17.2 푸시 알림의 콘텐츠(Content)와 트리거(Trigger)는 어떤 역할을 하나요?
### 1. 콘텐츠(Content)

푸시 알림의 내용을 정의합니다.

#### 주요 속성:
- title: 알림의 제목.
- body: 알림의 본문.
- sound: 알림 소리.
- badge: 앱 아이콘에 표시될 배지 번호.
- userInfo: 알림과 함께 전달할 추가 데이터.

#### 예시
```swift
let content = UNMutableNotificationContent()
content.title = "새로운 메시지!"
content.body = "친구가 보낸 메시지를 확인하세요."
content.sound = .default
content.badge = 1
content.userInfo = ["chatID": "12345"] // 사용자 정의 데이터
```

<br>

### 2. 트리거(Trigger)

알림이 발생하는 조건을 정의합니다.

#### 주요 종류:
1. 시간 기반 트리거 (UNTimeIntervalNotificationTrigger)
- 특정 시간 간격 후에 알림을 발생.
- 예시: 10초 후 알림 트리거.
```swift
let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 10, repeats: false)
```

<br>

2. 캘린더 기반 트리거 (UNCalendarNotificationTrigger)
- 특정 날짜와 시간에 알림을 발생.
- 예시: 매일 오전 8시에 알림 트리거.

```swift
let dateComponents = DateComponents(hour: 8, minute: 0)
let trigger = UNCalendarNotificationTrigger(dateMatching: dateComponents, repeats: true)
```

<br>

3. 위치 기반 트리거 (UNLocationNotificationTrigger)
- 사용자가 특정 지역에 도착하거나 떠날 때 알림을 발생.
- 예시:

```swift
let center = CLLocationCoordinate2D(latitude: 37.7749, longitude: -122.4194)
let region = CLCircularRegion(center: center, radius: 500, identifier: "SFRegion")
region.notifyOnEntry = true
region.notifyOnExit = false
let trigger = UNLocationNotificationTrigger(region: region, repeats: false)
```

<br>
<br>

## 17.3 사용자가 푸시 알림을 탭했을 때 앱의 동작을 처리하는 방법을 설명해주세요.

### 1. AppDelegate에서 처리

AppDelegate의 메서드를 통해 푸시 알림을 클릭했을 때의 동작을 정의할 수 있습니다.

```swift
import UserNotifications

@main
class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        UNUserNotificationCenter.current().delegate = self // Delegate 설정
        return true
    }

    // 사용자가 푸시 알림을 클릭했을 때 호출
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        let userInfo = response.notification.request.content.userInfo
        if let chatID = userInfo["chatID"] as? String {
            print("Navigating to chat with ID: \(chatID)")
            // 알림 클릭 시 채팅 화면으로 이동 로직 추가
        }
        completionHandler()
    }
}
```

<br>

### 2. 사용자 정의 데이터 처리

푸시 알림의 userInfo에 포함된 데이터를 통해 앱 내 특정 동작을 수행할 수 있습니다.

#### 예시:

```swift
let content = UNMutableNotificationContent()
content.title = "새로운 이벤트!"
content.body = "지금 확인해보세요!"
content.userInfo = ["eventID": "event123"] // 특정 화면 이동에 필요한 데이터
```

알림을 클릭하면 eventID를 기반으로 특정 화면을 띄울 수 있습니다:

```swift
func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
    if let eventID = response.notification.request.content.userInfo["eventID"] as? String {
        print("Navigating to event with ID: \(eventID)")
        // 해당 이벤트 화면으로 이동
    }
    completionHandler()
}
```

<br>

### 요약
1. 로컬 푸시는 디바이스 내부에서 발생하며, 원격 푸시는 서버에서 관리.
2. Content는 알림의 제목, 본문, 소리 등 사용자에게 표시되는 부분.
3. Trigger는 알림이 발생할 조건을 정의.
4. 사용자가 알림을 클릭하면, AppDelegate 또는 UNUserNotificationCenterDelegate에서 해당 이벤트를 처리하여 화면 이동이나 작업을 수행할 수 있습니다.


<br>
<br>

## 18. iOS 앱에서 SwiftUI와 UIKit을 함께 사용하는 방법은 무엇인가요?

SwiftUI와 UIKit은 각기 다른 방식으로 UI를 구성하지만, 상호 운용이 가능하도록 설계되었습니다. 이를 통해 SwiftUI와 UIKit을 같은 프로젝트에서 혼합하여 사용할 수 있습니다.

### 1. SwiftUI에서 UIKit 뷰를 사용하는 방법

#### a. UIViewControllerRepresentable 사용
- UIKit의 UIViewController를 SwiftUI에서 사용하기 위한 프로토콜.

#### 예시: UIImagePickerController를 SwiftUI에서 사용

```swift
import SwiftUI
import UIKit

struct ImagePicker: UIViewControllerRepresentable {
    @Binding var selectedImage: UIImage?
    @Environment(\.presentationMode) var presentationMode

    func makeUIViewController(context: Context) -> UIImagePickerController {
        let picker = UIImagePickerController()
        picker.delegate = context.coordinator
        return picker
    }

    func updateUIViewController(_ uiViewController: UIImagePickerController, context: Context) {}

    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    class Coordinator: NSObject, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
        let parent: ImagePicker

        init(_ parent: ImagePicker) {
            self.parent = parent
        }

        func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey: Any]) {
            if let image = info[.originalImage] as? UIImage {
                parent.selectedImage = image
            }
            parent.presentationMode.wrappedValue.dismiss()
        }
    }
}
```

#### SwiftUI에서 사용

```swift
struct ContentView: View {
    @State private var selectedImage: UIImage?

    var body: some View {
        VStack {
            if let image = selectedImage {
                Image(uiImage: image)
                    .resizable()
                    .scaledToFit()
            }
            Button("Pick an Image") {
                // Show the image picker
            }
            .sheet(isPresented: .constant(true)) {
                ImagePicker(selectedImage: $selectedImage)
            }
        }
    }
}
```

<br>

#### b. UIViewRepresentable 사용
- UIKit의 UIView를 SwiftUI에서 사용하기 위한 프로토콜.

#### 예시: UIActivityIndicatorView 사용

```swift
import SwiftUI

struct ActivityIndicator: UIViewRepresentable {
    var isAnimating: Bool

    func makeUIView(context: Context) -> UIActivityIndicatorView {
        return UIActivityIndicatorView(style: .large)
    }

    func updateUIView(_ uiView: UIActivityIndicatorView, context: Context) {
        if isAnimating {
            uiView.startAnimating()
        } else {
            uiView.stopAnimating()
        }
    }
}
```

#### SwiftUI에서 사용

```swift
struct ContentView: View {
    @State private var isLoading = false

    var body: some View {
        VStack {
            ActivityIndicator(isAnimating: isLoading)
            Button("Toggle Loading") {
                isLoading.toggle()
            }
        }
    }
}
```

<br>

### 2. UIKit에서 SwiftUI를 사용하는 방법

#### a. UIHostingController 사용
- SwiftUI의 뷰를 UIKit에서 사용하는 컨테이너.

#### 예시: UIKit 프로젝트에서 SwiftUI 뷰 사용

```swift
import SwiftUI
import UIKit

struct SwiftUIView: View {
    var body: some View {
        Text("This is a SwiftUI View")
            .font(.largeTitle)
            .padding()
    }
}

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        let swiftUIView = SwiftUIView()
        let hostingController = UIHostingController(rootView: swiftUIView)

        addChild(hostingController)
        hostingController.view.frame = view.bounds
        view.addSubview(hostingController.view)
        hostingController.didMove(toParent: self)
    }
}
```

<br>

### 3. SwiftUI와 UIKit의 혼합 사용 사례

#### a. 프로젝트의 점진적 전환
- 기존 UIKit 프로젝트에 SwiftUI를 도입하거나, SwiftUI 프로젝트에서 특정 기능에 UIKit을 사용.

#### b. 고급 사용자 인터페이스
- SwiftUI에서 구현하기 어려운 복잡한 UIKit 뷰를 SwiftUI에 통합.

#### c. 기존 서드파티 라이브러리
- UIKit 기반 라이브러리를 활용할 때 UIViewRepresentable 또는 UIViewControllerRepresentable로 통합.

<br>

### 4. 주의 사항
#### 1.	상태 관리:
- SwiftUI의 @State나 @Binding과 UIKit의 데이터 모델 간 동기화를 신경 써야 함.
#### 2.	생명 주기 차이:
- SwiftUI와 UIKit의 생명 주기가 다르므로 이벤트 및 상태 변화를 조율해야 함.
#### 3.	애니메이션:
- SwiftUI와 UIKit에서 제공하는 애니메이션 API가 다르므로 혼합 사용 시 일관성을 유지해야 함.

SwiftUI와 UIKit을 함께 사용하면 기존 코드 재사용 및 점진적 전환이 가능하며, 각 기술의 강점을 살려 유연한 UI 개발을 할 수 있습니다.

<br>
<br>

## 18.1 SwiftUI와 UIKit을 함께 사용할 때 주의할 점은 무엇인가요?
SwiftUI와 UIKit을 함께 사용할 경우, 각 기술의 차이점과 생명 주기를 이해하고 조화롭게 사용하는 것이 중요합니다. 아래는 주요 주의 사항입니다:

<br>


### 1. 상태 관리
- SwiftUI는 상태 기반 프로그래밍을 사용하여 뷰를 업데이트합니다(@State, @Binding, @EnvironmentObject 등).
- UIKit은 명령형 프로그래밍을 기반으로 상태를 수동으로 업데이트합니다.

<br>

#### 문제:
- SwiftUI 상태와 UIKit의 상태가 불일치할 수 있습니다.
- 예: SwiftUI의 상태 변경이 UIKit의 상태에 반영되지 않을 때.

<br>

#### 해결 방법:
- 데이터 동기화를 위해 ObservableObject와 Combine 사용.
- 필요에 따라 SwiftUI와 UIKit 간 데이터를 직접 매핑.

<br>

#### 예시:

```swift
import SwiftUI

class CounterModel: ObservableObject {
    @Published var count = 0
}

struct CounterView: View {
    @ObservedObject var model: CounterModel

    var body: some View {
        VStack {
            Text("Count: \(model.count)")
            Button("Increment") {
                model.count += 1
            }
        }
    }
}

// UIKit에서 SwiftUI 사용
class CounterViewController: UIViewController {
    let model = CounterModel()

    override func viewDidLoad() {
        super.viewDidLoad()

        let swiftUIView = CounterView(model: model)
        let hostingController = UIHostingController(rootView: swiftUIView)

        addChild(hostingController)
        hostingController.view.frame = view.bounds
        view.addSubview(hostingController.view)
        hostingController.didMove(toParent: self)
    }
}
```

<br>

### 2. 생명 주기 차이
- SwiftUI는 선언형이며, 상태 기반으로 동작하여 업데이트를 자동으로 관리합니다.
- UIKit은 명령형이며, viewDidLoad, viewWillAppear 등의 생명 주기 이벤트를 수동으로 관리합니다.

<br>

#### 문제:
- SwiftUI에서 UIKit의 생명 주기를 다룰 때, 예상치 못한 뷰 업데이트가 발생할 수 있음.

<br>

#### 해결 방법:
- 생명 주기 이벤트는 UIViewControllerRepresentable 및 UIViewControllerRepresentableContext를 활용하여 명시적으로 처리.

<br>

### 3. 레이아웃 관리
- SwiftUI와 UIKit의 레이아웃 시스템이 다르기 때문에 충돌이 발생할 수 있습니다.

<br>

#### 문제:
- SwiftUI의 레이아웃(예: VStack)이 UIKit의 UIView 크기와 잘 맞지 않을 수 있음.

<br>

#### 해결 방법:
- SwiftUI에서 UIViewRepresentable 또는 UIViewControllerRepresentable의 크기를 명시적으로 정의.
- UIKit에서 UIHostingController 사용 시 적절한 프레임 설정.

<br>

#### 예시:
```swift
struct SwiftUIView: View {
    var body: some View {
        Text("Hello, UIKit!")
            .frame(width: 200, height: 100)
    }
}

// UIKit에서 사용
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        let swiftUIView = SwiftUIView()
        let hostingController = UIHostingController(rootView: swiftUIView)

        addChild(hostingController)
        hostingController.view.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(hostingController.view)

        NSLayoutConstraint.activate([
            hostingController.view.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            hostingController.view.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            hostingController.view.widthAnchor.constraint(equalToConstant: 200),
            hostingController.view.heightAnchor.constraint(equalToConstant: 100)
        ])

        hostingController.didMove(toParent: self)
    }
}
```

<br>

### 4. 이벤트 및 사용자 입력 처리
- SwiftUI의 제스처와 UIKit의 제스처 인식기가 충돌할 수 있음.

<br>

#### 문제:
- SwiftUI의 onTapGesture와 UIKit의 UITapGestureRecognizer가 함께 동작하지 않을 수 있음.

#### 해결 방법:
- 제스처 우선순위를 명시적으로 지정하거나, 한쪽만 사용.


#### 예시:

```swift
struct ContentView: View {
    var body: some View {
        Text("Tap Me")
            .onTapGesture {
                print("SwiftUI tapped!")
            }
    }
}
```

<br>

### 5. 애니메이션
- SwiftUI는 선언형 애니메이션, UIKit은 명령형 애니메이션 방식.
#### 문제:
- 두 기술의 애니메이션이 겹칠 때 부자연스러운 결과가 발생할 수 있음.
#### 해결 방법:
- SwiftUI와 UIKit 애니메이션을 구분하여 사용하거나, 한 기술에 통합.

<br>

### 6. 데이터 전달
#### 문제:
- SwiftUI에서 UIKit으로 데이터 전달이 복잡해질 수 있음.
#### 해결 방법:
- ObservableObject나 클로저를 사용하여 상태를 공유.

<br>

#### 예시:
```swift
struct ContentView: View {
    @Binding var count: Int

    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") {
                count += 1
            }
        }
    }
}

// UIKit 사용
class ViewController: UIViewController {
    var count = 0

    override func viewDidLoad() {
        super.viewDidLoad()

        let swiftUIView = ContentView(count: $count)
        let hostingController = UIHostingController(rootView: swiftUIView)

        addChild(hostingController)
        view.addSubview(hostingController.view)
        hostingController.didMove(toParent: self)
    }
}
```

<br>

### 요약

SwiftUI와 UIKit을 함께 사용할 때, 상태 관리, 생명 주기, 레이아웃 관리, 이벤트 처리 등을 명확히 이해하고 설계해야 합니다. 각 프레임워크의 강점을 활용하되, 이들의 차이를 적절히 조율하는 것이 중요합니다.


<br>
<br>

## 19. Swift에서 키 경로(Key Path)란 무엇이며, 어떻게 사용하나요?
**키 경로(Key Path)** 는 Swift에서 타입의 속성에 접근하거나 참조할 수 있는 경로를 표현하는 기능입니다. 키 경로는 타입 안전성을 제공하며, 함수형 프로그래밍 스타일이나 데이터 바인딩에서 유용하게 활용됩니다.

<br>
<br>

## 19.1 키 경로 표현식(Key Path Expression)의 문법과 사용 예시를 설명해주세요.
### 문법:
- 키 경로는 \타입.속성 형태로 작성됩니다.
- 키 경로는 KeyPath 타입으로 표현됩니다.

#### 예시:

```swift
struct Person {
    var name: String
    var age: Int
}

// 키 경로 생성
let nameKeyPath = \Person.name
let ageKeyPath = \Person.age

// 키 경로를 사용하여 속성 읽기
let person = Person(name: "Alice", age: 25)
print(person[keyPath: nameKeyPath]) // 출력: Alice
print(person[keyPath: ageKeyPath])  // 출력: 25

// 키 경로를 사용하여 속성 변경
var mutablePerson = person
mutablePerson[keyPath: nameKeyPath] = "Bob"
print(mutablePerson.name) // 출력: Bob
```

<br>
<br>

## 19.2 런타임에 키 경로를 사용하여 속성에 접근하는 방법은 무엇인가요?
키 경로는 정적으로 생성되지만, 런타임에 동적으로 속성에 접근하거나 수정하는 데 활용할 수 있습니다.

#### 예시:

```swift
struct Product {
    var name: String
    var price: Double
}

func updateProperty<T, Value>(of object: inout T, for keyPath: WritableKeyPath<T, Value>, to newValue: Value) {
    object[keyPath: keyPath] = newValue
}

// 사용
var product = Product(name: "Laptop", price: 999.99)
print(product.name) // 출력: Laptop

updateProperty(of: &product, for: \Product.name, to: "Smartphone")
print(product.name) // 출력: Smartphone
```

<br>
<br>

## 19.3 키 경로와 KVO(Key-Value Observing)의 관계를 설명해주세요.
**Key-Value Observing (KVO)** 는 Objective-C 기반의 프로퍼티 관찰 메커니즘입니다. Swift에서 KVO와 키 경로를 함께 사용할 수 있지만, KVO는 Objective-C 런타임을 필요로 하므로 @objc로 선언된 프로퍼티에서만 동작합니다.

#### 예시:

```swift
import Foundation

class User: NSObject {
    @objc dynamic var name: String
    @objc dynamic var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}

let user = User(name: "Alice", age: 30)

// KVO를 사용하여 프로퍼티 관찰
let observation = user.observe(\.name, options: [.new, .old]) { user, change in
    print("Name changed from \(change.oldValue!) to \(change.newValue!)")
}

// 속성 변경
user.name = "Bob"

// 관찰 종료
observation.invalidate()
```

#### 키 경로와 KVO의 관계:
- 키 경로는 @objc dynamic 프로퍼티의 경로를 정의하여 KVO에서 사용됩니다.
- 키 경로를 통해 프로퍼티 변경을 감지하고 특정 동작을 수행할 수 있습니다.

<br>

### 요약
- 키 경로(Key Path): 타입 안전하게 속성에 접근하거나 수정할 수 있는 경로를 제공.
- 런타임 활용: 키 경로를 통해 속성을 동적으로 수정.
- KVO 연계: 키 경로를 KVO와 함께 사용하여 프로퍼티 변경을 관찰 가능.

키 경로는 Swift의 타입 안전성과 KVO의 유연성을 결합하여 데이터 바인딩, 상태 관리, 런타임 동작에서 강력한 도구로 사용됩니다.


<br>
<br>

## 20. iOS 앱에서 Deep Link와 Universal Link의 차이점은 무엇인가요?

<img src="https://github.com/user-attachments/assets/cf3c1176-0811-4216-9f06-ff38e51665ff">

<br>
<br>

## 20.1 Deep Link를 구현하는 방법과 주의 사항을 설명해주세요.
### 구현 방법
#### 1.	URL Scheme 등록:
- 앱의 Info.plist 파일에 URL Scheme을 등록:

```swift
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>myapp</string>
        </array>
    </dict>
</array>
```

<br>

#### 2.	AppDelegate에서 URL 처리:
- application(_:open:options:) 메서드에서 URL을 처리:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    if url.scheme == "myapp" {
        print("Deep Link URL: \(url)")
        // URL Path로 화면 이동 처리
        return true
    }
    return false
}
```

<br>

#### 3.	특정 화면으로 이동:
- URL의 Path와 Query Parameters를 파싱하여 적절한 화면으로 이동:

```swift
if url.host == "product" {
    let productID = url.queryParameters?["id"]
    navigateToProductPage(with: productID)
}
```

<br>

### 주의 사항

#### 보안 문제:
- URL Scheme은 고유하지 않으므로 다른 앱이 동일한 Scheme을 사용할 수 있음.
- URL Scheme 충돌 방지를 위해 고유한 이름을 사용해야 함.

#### 앱 설치 여부:
- 앱이 설치되지 않으면 URL Scheme을 처리할 수 없음.




<br>
<br>

## 20.2 Universal Link의 동작 원리와 설정 방법은 무엇인가요?
### 동작 원리
- HTTPS 기반의 URL을 통해 앱과 웹 간의 연속성을 제공.
- 사용자가 앱이 설치되어 있으면 앱에서 열리고, 설치되지 않았으면 웹 브라우저에서 열림.

### 설정 방법
#### 1. Associated Domains 설정:
- Signing & Capabilities에서 Associated Domains 추가:

```
applinks:www.example.com
```

<br>

#### 2.	apple-app-site-association 파일 생성:
- 서버의 루트 디렉토리에 apple-app-site-association 파일 추가:

```swift
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appID": "TEAMID.com.example.myapp",
        "paths": [ "/example/*" ]
      }
    ]
  }
}
```

<br>

#### 3.	AppDelegate에서 URL 처리:

```swift
func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([UIUserActivityRestoring]?) -> Void) -> Bool {
    if userActivity.activityType == NSUserActivityTypeBrowsingWeb, let url = userActivity.webpageURL {
        print("Universal Link URL: \(url)")
        // URL Path로 화면 이동 처리
        return true
    }
    return false
}
```

<br>

### 주의 사항
- HTTPS 요구: Universal Link는 반드시 HTTPS 프로토콜을 사용해야 함.
- 도메인 소유: 설정된 도메인에 대한 소유권이 필요.

<br>
<br>

## 20.3 Deep Link와 Universal Link를 함께 사용하는 경우의 장점은 무엇인가요?
### 장점

#### 1.	최대 호환성:
- Universal Link는 iOS 9 이상에서 동작하며, Deep Link는 그 이하 버전에서 동작.
#### 2.	웹과 앱의 연속성 제공:
- Universal Link를 통해 앱이 설치되지 않은 경우에도 웹으로 연결 가능.
#### 3.	백업 및 보안성:
- Universal Link가 실패하거나 작동하지 않는 경우 Deep Link로 대체 가능.
#### 4.	다양한 사용자 경험 제공:
- Deep Link를 통해 기존 URL Scheme을 활용한 앱 간 이동.
- Universal Link로 안전하고 매끄러운 웹-앱 전환.

<br>
<br>

## 21. Swift의 Result 타입과 에러 처리 방식에 대해 설명해주세요.
### Result 타입:
- Swift의 Result 타입은 작업의 성공 또는 실패 결과를 표현하는 열거형.
- 두 가지 케이스를 제공: .success와 .failure.

```swift
enum Result<Success, Failure: Error> {
    case success(Success)
    case failure(Failure)
}
```

<br>

### 주요 특징:
#### 1. 에러 처리 단순화:
- 성공과 실패를 명확하게 구분.
- 비동기 작업에서 콜백 클로저를 단순화.
#### 2.	가독성 향상:
- 성공과 실패 시의 코드 처리를 분리하여 가독성을 높임.

<br>

### 에러 처리 방식
#### 1. do-catch 블록을 사용한 에러 처리
- do-catch는 에러를 발생시키는 코드를 실행하고, 발생한 에러를 명확히 처리하기 위한 구문입니다.
#### 예제:

```swift
enum NetworkError: Error {
    case invalidURL
    case noData
}

func fetchData(from url: String) throws -> String {
    guard !url.isEmpty else { throw NetworkError.invalidURL }
    return "Fetched Data"
}

do {
    let data = try fetchData(from: "https://example.com")
    print(data) // 성공 시 데이터 출력
} catch NetworkError.invalidURL {
    print("Invalid URL")
} catch {
    print("Unexpected error: \(error)")
}
```

#### 특징:
- do 블록 안에서 에러를 던지는(throw) 코드를 실행.
- catch 블록에서 에러를 분기 처리.

<br>

#### 2.	옵셔널 반환을 사용한 에러 처리
- 에러를 던지지 않고, nil로 반환하여 에러를 암시적으로 처리하는 방식입니다.
#### 예제:

```swift
func fetchData(from url: String) -> String? {
    guard !url.isEmpty else { return nil }
    return "Fetched Data"
}

if let data = fetchData(from: "https://example.com") {
    print(data)
} else {
    print("Failed to fetch data")
}
```

<br>

#### 3.	Result 타입을 사용한 에러 처리
- 성공과 실패를 Result 타입으로 명확하게 표현.
#### 예제:

```swift
func fetchData(from url: String) -> Result<String, Error> {
    guard !url.isEmpty else { return .failure(NetworkError.invalidURL) }
    return .success("Fetched Data")
}

let result = fetchData(from: "https://example.com")
switch result {
case .success(let data):
    print("Data: \(data)")
case .failure(let error):
    print("Error: \(error)")
}
```

#### 특징:
- 명시적으로 성공(.success)과 실패(.failure)를 처리.
- 비동기 작업이나 복잡한 로직에 적합.

<br>

#### 4.	try?를 사용한 에러 처리
- 에러 발생 시 nil을 반환하는 방식.
#### 예제:

```swift
func fetchData(from url: String) throws -> String {
    guard !url.isEmpty else { throw NetworkError.invalidURL }
    return "Fetched Data"
}

let data = try? fetchData(from: "")
print(data ?? "No data fetched")
```

#### 특징:
- 에러를 무시하고, 결과만 필요할 때 사용.
- 단순화된 에러 처리.

<br>

#### 5.	try!를 사용한 에러 처리
- 에러가 절대 발생하지 않는다고 보장하는 경우에 사용.
#### 예제:

```swift
func fetchData(from url: String) throws -> String {
    guard !url.isEmpty else { throw NetworkError.invalidURL }
    return "Fetched Data"
}

let data = try! fetchData(from: "https://example.com") // 에러 발생 시 크래시
print(data)
```

#### 특징:
- 에러가 발생하면 앱이 크래시하므로, 안전하지 않은 상황에서는 사용하지 말아야 함.


<br>
<br>

## 21.1 Result 타입을 사용하는 이유와 장점은 무엇인가요?
### 이유 
#### 가독성 증가:
- do-catch 블록 없이 성공과 실패를 명확히 표현.
#### 비동기 코드 간결화:
- 클로저로 처리 시 코드 구조를 단순화.
#### 에러 처리 일관성:
- 함수와 클로저 모두 동일한 방식으로 성공/실패를 관리.

<br>

### 장점:
#### 1. 타입 안정성:
- 성공 값(Success)과 실패 값(Failure)에 대해 타입 안정성을 보장.
#### 2.	명확한 제어 흐름:
- 결과를 switch문으로 처리 가능.
#### 3.	중첩된 클로저 최소화:
- 비동기 작업에서 결과를 처리할 때 콜백 지옥 방지.

```swift
func fetchData(completion: (Result<String, Error>) -> Void) {
    let success = true
    if success {
        completion(.success("Data fetched successfully"))
    } else {
        completion(.failure(NSError(domain: "NetworkError", code: 1, userInfo: nil)))
    }
}

fetchData { result in
    switch result {
    case .success(let data):
        print(data) // 성공 처리
    case .failure(let error):
        print("Error: \(error.localizedDescription)") // 실패 처리
    }
}
```



<br>
<br>

## 21.2 에러 처리 시 do-catch 문과 Result 타입을 함께 사용하는 방법을 설명해주세요.

### do-catch와 함께 사용하는 이유:
- do-catch는 동기 작업에서 예외를 처리.
- Result는 비동기 작업의 성공/실패를 포착.
- 두 방식을 함께 사용하면 동기와 비동기 에러 처리를 모두 수용.

#### 구현 예시:
```swift
// 동기 작업에서 Result를 생성
func processData() -> Result<String, Error> {
    do {
        let data = try fetchDataFromDisk() // 성공 시 데이터 반환
        return .success(data)
    } catch {
        return .failure(error) // 에러 반환
    }
}

func fetchDataFromDisk() throws -> String {
    // 예외를 발생시키거나 데이터를 반환
    throw NSError(domain: "DiskError", code: 1, userInfo: nil)
}

// 처리 예시
let result = processData()

switch result {
case .success(let data):
    print("Data: \(data)")
case .failure(let error):
    print("Error: \(error.localizedDescription)")
}
```

#### 비동기 작업에서 Result와 do-catch 활용:

```swift
func fetchRemoteData(completion: (Result<String, Error>) -> Void) {
    DispatchQueue.global().async {
        do {
            let data = try fetchDataFromServer() // 성공 시 데이터 반환
            completion(.success(data))
        } catch {
            completion(.failure(error)) // 에러 반환
        }
    }
}

func fetchDataFromServer() throws -> String {
    // 서버 작업 중 에러를 발생시키거나 데이터 반환
    throw NSError(domain: "ServerError", code: 2, userInfo: nil)
}

// 비동기 호출
fetchRemoteData { result in
    switch result {
    case .success(let data):
        print("Fetched Data: \(data)")
    case .failure(let error):
        print("Failed with error: \(error.localizedDescription)")
    }
}
```

<br>

### 요약:
- do-catch는 동기 작업에서 직접 에러를 처리.
- Result는 성공/실패를 캡슐화하여 비동기 작업에 적합.
- 두 가지를 함께 사용하면 모든 상황에서 에러 처리가 명확하고 일관성 있게 동작.


<br>
<br>

## 22. iOS 앱에서 Thread Sanitizer를 사용하여 동시성 문제를 탐지하고 해결하는 방법을 설명해주세요.
### Thread Sanitizer란?
- **Thread Sanitizer (TSan)** 는 Xcode에서 제공하는 동시성(Concurrency) 문제를 탐지하기 위한 디버깅 도구.
- 경쟁 상태(Race Condition), 데드락(Deadlock), 잘못된 메모리 접근 등 멀티스레드와 관련된 문제를 감지.
- 특징:
	- 런타임에 동시성 문제를 감지.
	- 경고 메시지와 스택 트레이스를 제공하여 문제 위치를 추적 가능.

<br>

### Thread Sanitizer 활성화 방법
#### 1. Xcode에서 활성화:
- Product > Scheme > Edit Scheme로 이동.
- Run 탭 선택.
- Diagnostics 섹션에서 Thread Sanitizer 체크.
#### 2.	명령어 활성화:
- swiftc 또는 clang 컴파일러 사용 시 -fsanitize=thread 플래그 추가.

<br>

### 동시성 문제의 종류
#### 1. Race Condition (경쟁 상태):
- 두 스레드가 동시에 동일한 변수에 접근하고, 최소 하나의 스레드가 이를 수정할 때 발생.
#### 2.	Deadlock (교착 상태):
- 두 개 이상의 스레드가 서로가 필요로 하는 리소스를 기다리며 멈추는 상태.
#### 3.	메모리 문제:
- 잘못된 메모리 접근으로 인해 크래시가 발생할 수 있음.

#### 예제: Race Condition
#### 코드 예시:

```swift
var sharedCounter = 0

func incrementCounter() {
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            sharedCounter += 1
        }
    }
    
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            sharedCounter += 1
        }
    }
}

incrementCounter()
print("Final Counter: \(sharedCounter)") // 예상치 못한 값 출력
```

#### 문제:
- sharedCounter에 동시에 여러 스레드가 접근하여 예기치 않은 값이 출력.

#### Thread Sanitizer 메시지:
- “Data race detected on variable sharedCounter.”

<br>

### 해결 방법
#### 1. 동기화(Synchronization):
- 동기화를 통해 변수에 한 번에 하나의 스레드만 접근하도록 제한.

#### 코드 수정:

```swift
var sharedCounter = 0 // 공유 자원으로, 여러 스레드가 동시에 접근 가능

func incrementCounter() {
    // 첫 번째 비동기 작업을 글로벌 큐에 추가
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            sharedCounter += 1 // sharedCounter에 접근하여 값을 증가
            // 동시에 다른 스레드에서도 sharedCounter를 변경하고 있어 Race Condition 발생 가능
        }
    }
    
    // 두 번째 비동기 작업을 글로벌 큐에 추가
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            sharedCounter += 1 // sharedCounter에 접근하여 값을 증가
            // 두 스레드가 동시에 sharedCounter를 읽고 쓰기 때문에 데이터 충돌 가능
        }
    }
}

incrementCounter()

// Final Counter 값은 예상치 못한 결과를 출력
// 이 코드는 Race Condition(경쟁 상태) 문제를 일으킴.
// 두 스레드가 동시에 sharedCounter에 접근하여 읽고 쓰기 작업을 수행하면서 데이터 불일치 발생
print("Final Counter: \(sharedCounter)") 
```


<br>

#### 2.	Serial Queue 사용:
- DispatchQueue의 직렬 큐를 사용하여 순차적으로 실행.

#### 코드 수정:

```swift
var sharedCounter = 0 // 공유 자원으로, 여러 스레드가 접근 가능
let serialQueue = DispatchQueue(label: "com.example.serialQueue") // Serial Queue를 생성하여 동기화

func incrementCounter() {
    // 첫 번째 비동기 작업을 글로벌 큐에 추가
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            serialQueue.sync { // Serial Queue를 사용하여 작업을 순차적으로 처리
                sharedCounter += 1 // 순서대로 sharedCounter에 접근 및 값 증가
            }
        }
    }
    
    // 두 번째 비동기 작업을 글로벌 큐에 추가
    DispatchQueue.global().async {
        for _ in 0..<1000 {
            serialQueue.sync { // Serial Queue를 사용하여 작업을 순차적으로 처리
                sharedCounter += 1 // 순서대로 sharedCounter에 접근 및 값 증가
            }
        }
    }
}

incrementCounter()

// Serial Queue를 사용하여 경쟁 상태(Race Condition)를 방지
// 여러 스레드가 동시에 sharedCounter에 접근하지 못하고, 순차적으로 작업을 처리
// 결과적으로 Final Counter 값은 2000으로 예상한 값 출력
print("Final Counter: \(sharedCounter)") 
```

- Serial Queue는 동기적(sync) 작업을 순차적으로 실행합니다.
- 즉, 하나의 작업이 끝나야만 다음 작업이 실행됩니다.
- serialQueue.sync 블록 안의 코드가 실행되는 동안, 다른 작업은 대기합니다.

#### 예제: Deadlock
```swift
let lockA = NSLock() // 첫 번째 잠금을 위한 객체
let lockB = NSLock() // 두 번째 잠금을 위한 객체

func taskA() {
    lockA.lock() // lockA를 잠금
    sleep(1) // 1초 동안 대기 -> taskB가 lockB를 잠글 시간을 줌
    lockB.lock() // lockB를 잠그려고 시도하지만 taskB가 이미 잠금 상태라 대기
    lockB.unlock() // lockB 잠금 해제
    lockA.unlock() // lockA 잠금 해제
}

func taskB() {
    lockB.lock() // lockB를 잠금
    sleep(1) // 1초 동안 대기 -> taskA가 lockA를 잠글 시간을 줌
    lockA.lock() // lockA를 잠그려고 시도하지만 taskA가 이미 잠금 상태라 대기
    lockA.unlock() // lockA 잠금 해제
    lockB.unlock() // lockB 잠금 해제
}

// Deadlock 발생 시나리오
DispatchQueue.global().async { taskA() } // taskA 실행
DispatchQueue.global().async { taskB() } // taskB 실행
```


#### 문제:
- lockA와 lockB가 서로 다른 스레드에서 잠금 상태로 대기하며 교착 상태 발생.

#### Thread Sanitizer 메시지:
- “Deadlock detected involving locks: lockA and lockB.”

<br>

#### 해결 방법
1. 잠금 순서 일관성 유지:
- 항상 같은 순서로 잠금을 획득.

#### 코드 수정:

```swift
func taskA() {
    lockA.lock()
    lockB.lock()
    // 작업 수행
    lockB.unlock()
    lockA.unlock()
}

func taskB() {
    lockA.lock()
    lockB.lock()
    // 작업 수행
    lockB.unlock()
    lockA.unlock()
}
```

<br>

### 결론
- Thread Sanitizer는 멀티스레딩 문제를 조기에 발견하고 디버깅하는 데 유용.
- 문제를 해결하기 위해 동기화, 직렬 큐 사용, 또는 잠금 순서 일관성 유지와 같은 방법을 적용.
- Thread Sanitizer를 적극 활용하면 앱의 동시성 문제를 효과적으로 관리하고 안정성을 높일 수 있음.


<br>
<br>

## 23. Swift의 Sequence와 Collection 프로토콜에 대해 설명해주세요.
Sequence와 Collection은 Swift의 핵심 프로토콜로, 데이터를 반복(iterate)하거나 구조화된 데이터에 접근하는 방법을 제공합니다.

### Sequence
- Sequence는 Swift 표준 라이브러리에서 제공하는 프로토콜로, 데이터의 반복(순회, iteration)을 정의하는 원리를 제공합니다.
- 기본적으로 Sequence는 **반복(iteration)** 을 지원하기 위해 필요한 최소한의 요구사항을 정의하며, Swift의 다양한 데이터 구조(배열, 집합, 사전 등)가 이 프로토콜을 준수합니다.

### Sequence의 원리와 동작

Sequence 프로토콜은 반복 가능한 데이터의 집합을 나타내며, 반복을 실행하기 위한 메커니즘을 제공합니다.

#### 1.	makeIterator 메서드:
- Sequence를 구현하는 타입은 makeIterator 메서드를 제공해야 합니다.
- 이 메서드는 Iterator 객체를 반환하며, 반복 과정에서 데이터를 순차적으로 가져오는 데 사용됩니다.
- Iterator는 IteratorProtocol을 따르며, next() 메서드를 통해 다음 데이터를 반환합니다.

#### 2.	for-in 구문과 연계:
- Swift의 for-in 구문은 내부적으로 Sequence의 makeIterator를 호출하여 반복을 실행합니다.
- 반복은 next() 메서드를 계속 호출하면서 nil이 반환될 때까지 진행됩니다.

#### 3.	원리:
- Sequence는 데이터를 한 번에 하나씩 순차적으로 접근할 수 있도록 하는 추상화된 프로토콜입니다.
- 이를 통해 Swift는 다양한 데이터 구조에 대해 동일한 방식으로 순회할 수 있습니다.

<br>

### Sequence의 요구사항
```swift
protocol Sequence {
    associatedtype Element
    func makeIterator() -> Iterator
}
```
1. Element:
- Sequence를 반복하면서 반환할 데이터의 타입.

2. makeIterator():
- Iterator 객체를 반환하는 메서드.
- 이 Iterator는 IteratorProtocol을 준수하며, next() 메서드를 통해 데이터를 반환.

<br>

### Swift의 기본 데이터 구조와 Sequence
- Swift의 배열(Array), 집합(Set), 사전(Dictionary), 범위(Range) 등은 모두 Sequence 프로토콜을 준수합니다.
- 예를 들어:

```swift
let array = [1, 2, 3, 4]
for element in array {
    print(element)
}
```

위 코드에서 배열은 Sequence 프로토콜을 준수하므로, for-in 구문을 사용해 요소를 순회할 수 있습니다.

<br>

### Collection 프로토콜

Collection은 Swift 표준 라이브러리에서 제공하는 프로토콜로, 순차적 데이터를 저장하고 관리하는 데이터 구조에 대해 더 많은 기능과 요구사항을 정의한 프로토콜입니다.
Collection은 Sequence를 확장한 프로토콜로, 인덱스 기반 접근과 범위 연산을 가능하게 합니다.

<br>

### Collection 프로토콜의 요구사항

Collection을 채택하면 다음 요구사항을 반드시 구현해야 합니다:

#### 1.	startIndex와 endIndex:
- 컬렉션의 시작과 끝을 나타내는 인덱스.
- startIndex: 첫 번째 요소를 가리키는 인덱스.
- endIndex: 마지막 요소의 다음 위치를 가리키는 인덱스(요소가 없음).
#### 2.	index(after:):
- 특정 인덱스의 다음 인덱스를 반환하는 메서드.
- 이를 통해 인덱스를 순차적으로 이동할 수 있음.
#### 3.	subscript:
- 특정 인덱스의 요소에 접근하거나 수정할 수 있음.

#### Collection 프로토콜 요구사항 구현 예시

#### 간단한 Custom Collection
```swift
struct SimpleCollection: Collection {
    private let data: [Int] // 데이터를 저장할 배열

    // 시작 인덱스
    var startIndex: Int { data.startIndex }

    // 끝 인덱스
    var endIndex: Int { data.endIndex }

    // 인덱스 이동
    func index(after i: Int) -> Int {
        return data.index(after: i) // 주어진 인덱스의 다음 인덱스 반환
    }

    // 요소에 접근
    subscript(position: Int) -> Int {
        return data[position]
    }
}

// 사용 예시
let collection = SimpleCollection(data: [10, 20, 30, 40, 50])

// for-in 구문으로 순회
for element in collection {
    print(element) // 출력: 10, 20, 30, 40, 50
}

// 특정 인덱스에 접근
print(collection[2]) // 출력: 30
```

<br>

### Collection의 주요 장점

#### 1.	인덱스 기반 접근:
- 원하는 요소를 인덱스로 직접 접근 가능.
- 이를 통해 특정 위치의 요소를 쉽게 가져오거나 수정 가능.
#### 2.	범위 연산 지원:
- Collection은 범위 연산(range)을 기본적으로 지원하므로 특정 범위의 데이터를 손쉽게 처리할 수 있음.
- 예시:

```swift
let array = [1, 2, 3, 4, 5]
let subArray = array[1...3] // 출력: [2, 3, 4]
```

<br>
<br>

## 23.1 Sequence와 Collection 프로토콜의 차이점과 요구 사항을 설명해주세요.

<img src="https://github.com/user-attachments/assets/0ca465af-fe86-41ef-a8fe-f65cf90b4989">

### 알고리즘 문제에서 Sequence와 Collection의 활용

#### 1.	Sequence:
- 순차적 접근만 필요하고, 중간 결과를 저장할 필요가 없는 경우 사용.
- 예를 들어, **게으른 계산(Lazy Evaluation)** 을 활용하여 데이터 처리량을 줄이거나 성능을 최적화할 수 있음.
- 예시:
	- 무한 수열 생성.
	- 게으른 계산으로 큰 데이터 스트림 처리.

<br>

#### 2.	Collection:
- 랜덤 접근(random access), 인덱스 기반 작업, 또는 크기를 알고 있어야 하는 경우 적합.
- 예시:
	- 특정 인덱스에 빠르게 접근해야 하는 문제.
	- 정렬, 슬라이싱, 특정 패턴에 맞는 데이터 조작.
	- 알고리즘 문제에서 자주 사용하는 배열, 스택, 큐, 링크드 리스트 같은 자료구조를 구현 가능.


<br>

### Sequence 프로토콜의 요구 사항
- makeIterator() -> Iterator
	- Iterator는 next() 메서드를 통해 각 요소를 반환.
	- 마지막 요소 이후에는 nil 반환.

```swift
// Sequence 프로토콜의 요구 사항을 구현한 간단한 예시
struct SimpleSequence: Sequence {
    private let data: [Int] // 데이터를 저장할 배열

    // Sequence 프로토콜의 요구 사항: makeIterator()
    func makeIterator() -> Array<Int>.Iterator {
	// MARK: 여기서 비즈니스 로직 처리 가능 예) 짝수만 반환 (필터 처리)
        return data.makeIterator() // 내부 배열의 Iterator를 반환
    }
}

// 사용 예시
let sequence = SimpleSequence(data: [1, 2, 3, 4, 5])
for element in sequence {
    print(element) // 1, 2, 3, 4, 5
}
/*
1. `SimpleSequence`는 Sequence 프로토콜을 채택.
2. `makeIterator()`에서 내부 배열의 Iterator를 반환.
3. for-in 구문에서 Iterator의 next() 메서드를 반복적으로 호출하여 요소를 순회.
*/
```

<br>

### Collection 프로토콜의 요구 사항
- startIndex와 endIndex:
	- startIndex는 컬렉션의 첫 번째 요소 인덱스.
	- endIndex는 마지막 요소 다음을 가리킴.
- index(after:):
	- 특정 인덱스의 다음 요소 인덱스를 반환.
- subscript:
	- 인덱스를 통해 요소를 직접 접근 가능.

```swift
// Collection 프로토콜의 요구 사항을 구현한 간단한 예시
struct SimpleCollection: Collection {
    private let data: [Int] // 데이터를 저장할 배열

    // Collection 프로토콜의 요구 사항: startIndex와 endIndex
    var startIndex: Int { data.startIndex } // 시작 인덱스 반환
    var endIndex: Int { data.endIndex }     // 끝 인덱스 반환 (마지막 요소 다음 인덱스)

    // Collection 프로토콜의 요구 사항: index(after:)
    func index(after i: Int) -> Int {
        return data.index(after: i) // 주어진 인덱스의 다음 인덱스 반환
    }

    // Collection 프로토콜의 요구 사항: subscript
    subscript(position: Int) -> Int {
        return data[position] // 인덱스를 통해 데이터에 접근
    }
}

// 사용 예시
let collection = SimpleCollection(data: [10, 20, 30, 40, 50])

// for-in 구문으로 순회
for element in collection {
    print(element) // 10, 20, 30, 40, 50
}

// 특정 인덱스에 직접 접근
print(collection[2]) // 30

/*
1. `SimpleCollection`은 Collection 프로토콜을 채택.
2. startIndex와 endIndex를 통해 컬렉션의 범위를 정의.
3. index(after:)는 현재 인덱스의 다음 인덱스를 반환.
4. subscript를 통해 배열처럼 특정 요소에 접근 가능.
5. for-in 구문에서는 Collection이 자동으로 Sequence를 준수하므로 요소를 순회할 수 있음.
*/
```

<br>
<br>

## 23.2 사용자 정의 Sequence와 Collection을 구현하는 방법과 사용 예시를 들어주세요.
### 사용자 정의 Sequence 구현
```swift
// 사용자 정의 Sequence 타입
struct FibonacciSequence: Sequence {
    let maxCount: Int // 생성할 피보나치 수열의 최대 개수

    // Sequence 프로토콜 요구 사항인 makeIterator() 구현
    func makeIterator() -> FibonacciIterator {
        return FibonacciIterator(maxCount: maxCount) // FibonacciIterator를 반환
    }
}

// Iterator 타입 정의
struct FibonacciIterator: IteratorProtocol {
    let maxCount: Int // 생성할 피보나치 수열의 최대 개수
    private var currentIndex = 0 // 현재 생성된 피보나치 수의 인덱스
    private var previousValue = 0 // 직전 피보나치 수
    private var currentValue = 1 // 현재 피보나치 수

    // IteratorProtocol의 요구 사항인 next() 메서드 구현
    mutating func next() -> Int? {
        // 현재 인덱스가 최대 개수를 초과하면 nil 반환 (반복 종료)
        guard currentIndex < maxCount else { return nil }

        // defer 블록: 현재 피보나치 값을 반환한 후에 상태를 업데이트
        defer {
            let nextValue = previousValue + currentValue // 다음 피보나치 수 계산
            previousValue = currentValue // 현재 값을 이전 값으로 이동
            currentValue = nextValue // 다음 값을 현재 값으로 이동
            currentIndex += 1 // 인덱스 증가
        }

        // 현재 피보나치 수 반환
        return previousValue
    }
}

// 사용 예시
let fibonacci = FibonacciSequence(maxCount: 10) // 최대 10개의 피보나치 수열 생성
for value in fibonacci {
    print(value) // 결과: 0, 1, 1, 2, 3, 5, 8, 13, 21, 34
}

/*
1. `FibonacciSequence`:
   - Sequence 프로토콜을 준수하는 타입.
   - makeIterator()를 통해 Iterator 객체를 반환하여 for-in 구문에서 사용할 수 있도록 함.

2. `FibonacciIterator`:
   - 실제로 피보나치 수를 생성하는 Iterator 타입.
   - IteratorProtocol을 준수하여 next() 메서드를 구현.
   - next() 메서드는 호출될 때마다 다음 피보나치 값을 반환하고 내부 상태를 업데이트.

3. 동작 원리:
   - `for value in fibonacci`를 실행하면 `fibonacci`가 Sequence로 동작하며 Iterator를 생성.
   - Iterator의 next()가 반복 호출되면서 피보나치 수열을 순차적으로 반환.
   - 최대 `maxCount`에 도달하면 next()가 nil을 반환하여 반복 종료.

4. 주요 포인트:
   - Sequence와 Iterator의 분리를 통해 유연성과 재사용성을 높임.
   - next() 호출마다 상태를 업데이트하여 다음 피보나치 값을 계산.
   - defer를 사용해 반환 전에 상태를 미리 계산하고 업데이트.
*/
```

<br>

### 사용자 정의 Collection 구현

```swift
// 사용자 정의 Collection 타입
struct MyCollection: Collection {
    // 데이터를 저장할 배열
    private let data: [Int]

    // 생성자: 데이터 배열을 초기화
    init(_ data: [Int]) {
        self.data = data
    }

    // Collection 프로토콜 필수 요구 사항 구현

    // 컬렉션의 시작 인덱스 반환
    var startIndex: Int { 
        data.startIndex // 내부 배열의 시작 인덱스를 반환
    }

    // 컬렉션의 끝 인덱스 반환
    var endIndex: Int { 
        data.endIndex // 내부 배열의 끝 인덱스를 반환
    }

    // 주어진 인덱스의 다음 인덱스를 반환
    func index(after i: Int) -> Int {
        return data.index(after: i) // 내부 배열의 다음 인덱스를 반환
    }

    // 특정 인덱스에 있는 요소를 반환 (서브스크립트)
    subscript(position: Int) -> Int {
        return data[position] // 내부 배열에서 해당 위치의 요소를 반환
    }
}

// 사용 예시
let customCollection = MyCollection([10, 20, 30, 40, 50]) // 데이터 배열 초기화

// for-in 구문으로 순회
for element in customCollection {
    print(element) // 10, 20, 30, 40, 50
    // Collection 타입을 준수하므로 for-in을 사용해 요소를 순회할 수 있음
}

// 특정 인덱스의 값에 접근
print(customCollection[2]) // 30
// subscript 구현 덕분에 특정 인덱스의 값을 읽을 수 있음
```


<br>
<br>

## 23.3 Sequence와 Collection의 차이점과 사용용도를 설명해주세요.
<img src="https://github.com/user-attachments/assets/0cd866d6-b043-471a-9a58-884096f8fbd2">

<br>
<br>

## 24. UIKit의 AdaptiveLayout과 Size Classes에 대해 설명해주세요.
Adaptive Layout과 Size Classes는 iOS 앱이 다양한 화면 크기와 방향(세로/가로)에 적응하도록 설계된 도구입니다. 이를 통해 여러 디바이스에서 일관된 사용자 경험을 제공하며, Auto Layout 및 Size Classes를 사용하여 구현됩니다.

<br>
<br>

## 24.1 AdaptiveLayout의 개념과 사용 목적을 설명해주세요.
### Adaptive Layout의 개념

Adaptive Layout은 다양한 화면 크기와 방향을 지원하기 위해 UI를 동적으로 변경하거나 조정하는 방법입니다. iPhone, iPad, Mac 등 다양한 기기에서 동일한 레이아웃 코드를 유지하면서 각 기기에 적합한 UI를 제공하는 것이 목표입니다.

<br>

### 사용 목적
1. 다양한 디바이스 지원: 여러 기기에서 동일한 앱을 사용할 수 있도록 지원.
2. 효율적인 유지보수: UI 변경을 최소화하여 코드 재사용성 증가.
3. 일관된 사용자 경험: 화면 크기와 방향에 따른 적응형 UI 제공.
4. Split View 및 Multi-Window 환경 지원: iPad와 Mac에서의 멀티태스킹 기능을 수용.

<br>
<br>

## 24.2 Size Classes를 활용하여 다양한 기기에 적응적인 UI를 구현하는 방법을 예시와 함께 설명해주세요.

### Size Classes의 개념

Size Classes는 화면의 **너비(Width)**와 **높이(Height)**를 두 가지 상태로 구분하여 UI를 조정할 수 있도록 돕습니다:

- Compact: 작은 크기의 화면이나 영역 (예: iPhone 세로 화면).
- Regular: 큰 크기의 화면이나 영역 (예: iPad, Mac 화면).

#### Size Classes 조합
<img src="https://github.com/user-attachments/assets/50bcb5c9-694e-414d-8cb1-a0b6e3030603">

<br>

### Size Classes를 활용한 코드 예제

#### 스토리보드에서 Size Classes 활용

1. 스토리보드에서 뷰 컨트롤러를 선택.
2. Size Class에 따라 제약 조건 또는 뷰를 설정:
- Compact Width + Regular Height → iPhone 세로 모드.
- Regular Width + Regular Height → iPad 가로 모드.

#### 코드에서 Size Classes 활용

```swift
import UIKit

class AdaptiveViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        setupLayout()
    }

    private func setupLayout() {
        // 중앙에 배치할 레이블 생성
        let label = UILabel()
        label.text = "Adaptive Layout"
        label.textAlignment = .center
        label.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(label)

        // 기본 Auto Layout 제약 조건 설정
        NSLayoutConstraint.activate([
            label.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            label.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }

    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        super.traitCollectionDidChange(previousTraitCollection)

        // Size Class 변경 감지 및 대응
        if traitCollection.horizontalSizeClass == .compact {
            view.backgroundColor = .lightGray // iPhone 세로
        } else {
            view.backgroundColor = .blue // iPad 가로
        }
    }
}
```

### 코드 설명
1. Auto Layout 사용: UILabel을 화면 중앙에 배치.
2. Size Class에 따른 배경색 변경:
- traitCollectionDidChange를 활용하여 Size Class 변경 시 동작.
- Compact Width(좁은 너비)에서는 배경색을 회색으로, Regular Width(넓은 너비)에서는 파란색으로 설정.

### Size Classes 활용의 장점
1. 적응형 UI 설계: 다양한 화면 크기와 방향을 고려한 유연한 UI 제공.
2. 코드 재사용성: 하나의 코드로 여러 디바이스를 지원.
3. iPad Split View와 같은 환경 지원: 멀티태스킹을 고려한 설계 가능.

이와 같은 방식으로 Size Classes와 Adaptive Layout을 함께 사용하면 다양한 디바이스에서 최적의 사용자 경험을 제공할 수 있습니다.


<br>
<br>

## 25. Swift의 커스텀 연산자(Custom Operator)에 대해 설명해주세요.


<br>
<br>

## 25.1 커스텀 연산자를 정의하는 방법과 주의 사항은 무엇인가요?


<br>
<br>

## 25.2 커스텀 연산자를 활용한 코드 가독성 향상 방안을 제시해주세요.


<br>
<br>

## 26. Swift의 생성자(Initializer)와 관련된 고급 개념에 대해 설명해주세요.


<br>
<br>

## 26.1 지정 생성자(Designated Initializer)와 편의 생성자(Convenience Initializer)의 차이점은 무엇인가요?


<br>
<br>

## 26.2 필수 생성자(Required Initializer)와 실패 가능한 생성자(Failable Initializer)는 어떤 경우에 사용하나요?


<br>
<br>

## 27. Combine 프레임워크에서 Scheduler의 역할과 종류에 대해 설명해주세요.


<br>
<br>

## 27.1 Scheduler를 사용하여 작업을 특정 큐(DispatchQueue)에서 실행하는 방법을 설명해주세요.


<br>
<br>

## 27.2 백그라운드에서 작업을 수행하고 메인 큐에서 UI를 업데이트하는 패턴을 Combine으로 구현하는 방법을 설명해주세요.



<br>
<br>


## 28. UIKit의 `UIView`는 클래스 기반으로 구현되어 있지만, SwiftUI에서 `View` 프로토콜을 준수하는 타입은 보통 구조체를 사용합니다. 그 이유는 무엇일까요?


<br>
<br>

## 28.1 `View` 프로토콜을 준수하는 구조체의 주요 특징은 무엇이며, 이는 어떻게 SwiftUI의 성능 및 사용성에 영향을 미치나요?


<br>
<br>

## 28.2 SwiftUI의 `View`가 구조체임에도 불구하고, 상태(state)를 어떻게 관리하고 업데이트하나요?


<br>
<br>

## 28.3 SwiftUI의 구조체 기반 `View` 생성과 업데이트 사이클은 어떻게 UIKit의 클래스 기반 `UIView`와 다른가요?


<br>
<br>
