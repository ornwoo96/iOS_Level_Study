# 레벨 4

## 1. Swift의 메모리 안전성(Memory Safety)에 대해 설명해주세요.

Swift는 메모리 안전성을 보장하기 위해 설계된 언어로, 코드 실행 중 메모리 접근과 관련된 오류(예: 덮어쓰기, 해제 후 접근)를 방지합니다. 이를 위해 **소유권(Ownership)** 과 빌림(Borrowing) 개념을 도입하여 메모리에 안전하게 접근하도록 관리합니다.

<br>
<br>

## 1.1 소유권(Ownership)과 빌림(Borrowing)의 개념과 차이점은 무엇인가요?

### 소유권(Ownership)
- 정의: Swift에서 각 변수는 저장된 값의 유일한 소유권을 가집니다.
-	특징:
  - 소유권은 특정 시점에 단 하나의 변수만 가질 수 있습니다.
  - 소유자가 해제되면 해당 값도 메모리에서 해제됩니다.
  - 주로 참조 타입에서 ARC(Automatic Reference Counting)를 통해 관리.

### 빌림(Borrowing)
- 정의: 소유권을 이전하지 않고, 값을 읽거나 수정하기 위해 임시로 접근하는 것을 의미합니다.
- 특징:
  - 읽기 빌림(let): 읽기 전용으로 접근하며, 값은 변경할 수 없습니다.
  - 쓰기 빌림(inout): 일시적으로 값을 수정할 수 있지만, 빌림이 끝난 후에는 원래 변수로 복구됩니다.
  - 소유권이 변경되지 않으므로 원래 소유자는 계속 값을 관리합니다.

<br>
<br>

## 1.2 메모리 안전성을 보장하기 위한 Swift의 메커니즘(대여 검사, 소유권 검사 등)을 설명해주세요.
### 1. 대여 검사(Borrow Checker)
- Swift는 변수에 접근할 때 읽기/쓰기 대여 규칙을 검사합니다:
  -	하나의 값에 대해 여러 읽기는 허용되지만, 읽기와 쓰기가 동시에 발생하면 오류가 발생합니다.
  -	쓰기 접근은 동시에 하나만 허용됩니다.

#### 예시:

```swift
var number = 42
func modifyNumber(_ value: inout Int) {
    value += 1
}

// 오류: 읽기와 쓰기를 동시에 수행
let copy = number // 읽기 접근
modifyNumber(&number) // 쓰기 접근
```

<br>

### 2. 소유권 검사(Ownership Checker)
- Swift는 참조 타입(클래스)의 소유권을 관리하기 위해 ARC를 사용합니다:
  - 강한 참조: 기본적으로 모든 참조는 강한 참조로, 참조 횟수가 증가.
  - 약한 참조/미소유 참조: 소유권을 갖지 않는 참조 방식으로, 참조 횟수 증가를 방지.


<br>
<br>

## 1.3 메모리 안전성 규칙을 위반하는 경우와 해결 방법을 예시와 함께 설명해주세요.

### 규칙 위반 예시 1: 쓰기 접근 충돌
- 설명: 같은 변수에 대해 동시에 읽기와 쓰기 접근이 발생하면 오류가 발생.

#### 문제 코드:

```swift
var balance = 100

func updateBalance(_ amount: inout Int) {
    amount += 50
}

// 오류: 읽기와 쓰기를 동시에 수행
let currentBalance = balance
updateBalance(&balance)
```


#### 해결 방법:
- 읽기와 쓰기를 순차적으로 수행:

```swift
var balance = 100

func updateBalance(_ amount: inout Int) {
    amount += 50
}

updateBalance(&balance) // 쓰기 수행 후
let currentBalance = balance // 읽기 수행
```

<br>

### 규칙 위반 예시 2: 참조 타입에서 순환 참조 발생
- 설명: 두 객체가 서로 강한 참조를 갖는 경우, 메모리에서 해제되지 않는 **순환 참조(Retain Cycle)**가 발생.

#### 문제 코드:

```swift
class A {
    var b: B?
}

class B {
    var a: A?
}

let a = A()
let b = B()

a.b = b
b.a = a // 순환 참조 발생
```

#### 해결 방법:
- 약한 참조(weak)를 사용하여 순환 참조 방지:

```swift
class A {
    var b: B?
}

class B {
    weak var a: A?
}

let a = A()
let b = B()

a.b = b
b.a = a // 메모리 안전성 보장
```

#### 해결 방법:
- 약한 참조(weak)를 사용하여 순환 참조 방지:

```swift
class A {
    var b: B?
}

class B {
    weak var a: A?
}

let a = A()
let b = B()

a.b = b
b.a = a // 메모리 안전성 보장
```

<br>

### Swift 메모리 안전성의 주요 특징
1. 동시 접근 방지:
- 읽기/쓰기 접근 규칙을 통해 메모리 충돌 방지.
2. 소유권 및 빌림 관리:
- 소유권 이전과 빌림을 명확히 구분하여 메모리 접근을 안전하게 관리.
3. ARC로 참조 타입 관리:
- 자동으로 메모리를 관리하며, 순환 참조와 같은 문제를 예방하도록 설계.

<br>

### 요약

Swift는 소유권과 빌림 개념을 통해 메모리 안전성을 보장하며, ARC와 대여 검사를 통해 동시 접근 충돌, 순환 참조 등의 문제를 방지합니다. 이러한 메커니즘은 코드의 안정성을 높이고 런타임 오류를 줄이는 데 중요한 역할을 합니다.


<br>
<br>

## 2. iOS 앱에서 Core Bluetooth를 사용하여 BLE(Bluetooth Low Energy) 통신을 구현하는 방법은 무엇인가요?

<br>
<br>

## 2.1 Central과 Peripheral의 역할과 상호작용 과정을 설명해주세요.

<br>
<br>

## 2.2 CBCentralManager와 CBPeripheralManager의 주요 메서드와 델리게이트 메서드를 설명해주세요.

<br>
<br>

## 2.3 BLE 통신에서 사용되는 서비스(Service)와 특성(Characteristic)의 개념과 구현 방법을 설명해주세요.


<br>
<br>

## 3. Swift의 Copy-on-Write 메커니즘에 대해 설명해주세요.

<br>
<br>

## 3.1 Copy-on-Write의 동작 원리와 장점은 무엇인가요?

<br>
<br>

## 3.2 Copy-on-Write를 사용하는 Swift의 타입에는 어떤 것들이 있나요?

<br>
<br>

## 3.3 Copy-on-Write를 고려하여 성능 최적화를 하는 방법을 예시와 함께 설명해주세요.


<br>
<br>


## 4. iOS 앱에서 Core NFC를 사용하여 NFC 태그와 상호작용하는 방법은 무엇인가요?

<br>
<br>

## 4.1 NFCNDEFReaderSession과 NFCTagReaderSession의 차이점과 사용 방법을 설명해주세요.

<br>
<br>

## 4.2 NFC 태그 읽기 및 쓰기 과정과 필요한 권한 설정 방법을 설명해주세요.

<br>
<br>

## 4.3 Core NFC를 사용할 때 주의해야 할 점과 제한 사항은 무엇인가요?

<br>
<br>

## 4.4 Core NFC를 사용할 때 고려해야 할 보안 사항과 모범 사례를 설명해주세요.


<br>
<br>

## 5. Swift의 actor와 structured concurrency에 대해 설명해주세요.

<br>
<br>

## 5.1 actor의 개념과 동시성 문제 해결 방법을 설명해주세요.

<br>
<br>

## 5.2 async let과 TaskGroup을 사용한 구조적 동시성 프로그래밍 방법을 예시와 함께 설명해주세요.

<br>
<br>

## 5.3 actor와 structured concurrency를 활용한 효과적인 비동기 프로그래밍 패턴을 소개해주세요.


<br>
<br>

## 6. iOS 앱에서 Vision 프레임워크를 사용하여 이미지 분석 및 처리를 수행하는 방법은 무엇인가요?

<br>
<br>

## 6.1 얼굴 감지 및 인식, 바코드 인식, 텍스트 인식 등의 기능 구현 방법을 설명해주세요.

<br>
<br>

## 6.2 Vision 요청(VNRequest)의 종류와 사용 방법, 결과 처리 과정을 설명해주세요.

<br>
<br>

## 6.3 Vision 프레임워크와 Core ML, ARKit 등 다른 프레임워크와의 연동 방법을 소개해주세요.


<br>
<br>

## 7. Swift의 property wrappers에 대해 설명해주세요.

<br>
<br>

## 7.1 property wrappers의 동작 원리와 사용 목적, 구현 방법을 설명해주세요.


<br>
<br>

## 8. iOS 앱의 보안을 강화하기 위한 방법과 모범 사례에 대해 설명해주세요.

<br>
<br>

## 8.1 안전한 데이터 저장 및 전송을 위한 암호화 기술(AES, RSA 등)과 구현 방법을 설명해주세요.

<br>
<br>

## 8.2 앱 바이너리 보호, 탈옥 감지, 동적 라이브러리 감지 등의 보안 대책을 소개해주세요.

<br>
<br>

## 8.3 코드 난독화, 런타임 무결성 검사 등 추가적인 보안 강화 방안을 제안해주세요.


<br>
<br>

## 9. Swift의 custom string interpolation에 대해 설명해주세요.

<br>
<br>

## 9.1 custom string interpolation을 사용하여 문자열 보간법을 확장하는 방법을 예시와 함께 설명해주세요.


<br>
<br>

## 10. Swift의 Distributed Actor에 대해 설명해주세요.

<br>
<br>

## 10.1 Distributed Actor의 개념과 사용 목적을 설명해주세요.

<br>
<br>

## 10.2 분산 시스템에서 Distributed Actor를 활용한 통신 및 상태 동기화 방법을 예시와 함께 설명해주세요.


<br>
<br>

## 11. Swift의 DSL(Domain-Specific Language) 설계 및 구현 방법에 대해 설명해주세요.

<br>
<br>

## 11.1 DSL의 개념과 장점, Swift에서의 구현 방식을 설명해주세요.

<br>
<br>

## 11.2 result builder를 활용한 DSL 설계 사례를 소개해주세요.


<br>
<br>

## 12. Swift의 유연한 문법 기능(e.g., 오퍼레이터 오버로딩, 첨자 표기법)을 활용한 코드 설계 방법에 대해 설명해주세요.

<br>
<br>

## 12.1 오퍼레이터 오버로딩을 사용하여 사용자 정의 타입에 대한 연산을 직관적으로 표현하는 방법을 예시와 함께 설명해주세요.

<br>
<br>

## 12.2 첨자 표기법을 사용하여 사용자 정의 컬렉션 타입을 구현하는 방법과 주의 사항을 설명해주세요.


<br>
<br>

## 13. Swift의 리플렉션(Reflection)과 런타임 프로그래밍에 대해 자세히 설명해주세요.

<br>
<br>

## 13.1 리플렉션을 사용하여 런타임에 타입 정보를 검사하고 메서드를 호출하는 방법을 예시와 함께 설명해주세요.

<br>
<br>

## 13.2 리플렉션을 활용한 의존성 주입(Dependency Injection) 프레임워크 구현 방법을 설명해주세요.


<br>
<br>

## 14. iOS 앱에서 Core ML을 사용하여 머신러닝 모델을 통합하는 방법은 무엇인가요?

<br>
<br>

## 14.1 Core ML 모델을 생성하고 앱에 추가하는 과정을 설명해주세요.

<br>
<br>

## 14.2 Vision 프레임워크와 Core ML을 함께 사용하여 이미지 인식을 수행하는 방법은 무엇인가요?

<br>
<br>

## 14.3 Core ML 모델의 성능을 최적화하는 방법과 주의 사항을 설명해주세요.

<br>
<br>

## 14.4 Core ML 이외에 사용할 수 있는 머신러닝 프레임워크와 장단점을 비교해주세요.

<br>
<br>

## 14.5 머신러닝 모델의 경량화 및 최적화 기법을 소개하고, 모바일 환경에 적합한 모델 설계 방안을 제시해주세요.


<br>
<br>

