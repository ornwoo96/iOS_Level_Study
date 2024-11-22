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
- Swift는 변수에 대한 접근 규칙을 컴파일 시점에 검사합니다.
- inout으로 넘기는 경우는 쓰기 접근이 함수가 끝날 때까지 잠겨 있는 상태로 간주됩니다.
- 함수 호출 전에 읽기/쓰기 접근이 겹치면 충돌 가능성이 생깁니다.

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

Core Bluetooth를 사용하여 BLE 통신을 구현하려면 CBCentralManager와 CBPeripheralManager를 활용하여 Central 및 Peripheral 역할을 구현하고, 서비스와 특성으로 데이터를 교환합니다. 이에 대한 세부 구현 방법은 아래 항목에서 설명합니다.


<br>
<br>

## 2.1 Central과 Peripheral의 역할과 상호작용 과정을 설명해주세요.

### 1. Central:
-	BLE 기기에서 데이터를 검색하고 읽기/쓰기 작업을 수행.
-	예: iOS 앱이 BLE 주변 기기(Peripheral)와 연결하여 데이터를 가져오거나 전송.

### 2. Peripheral:
-	BLE 기기로 데이터 제공.
-	예: 스마트 센서(온도계, 스마트워치)가 데이터를 제공하며, 필요 시 Central에서 요청한 데이터를 반환.

### 3. 상호작용 과정:
- Central이 BLE 장치를 스캔하고 발견된 Peripheral에 연결.
- 연결된 Peripheral에서 **서비스(Service)** 를 검색.
- 서비스 안의 **특성(Characteristic)** 을 검색하고 데이터 교환.

### 작업 흐름:
1. Central 시작 → BLE 기기 검색 → Peripheral 연결.
2. Peripheral이 서비스 및 특성 제공.
3. 데이터 교환 (읽기/쓰기/알림).

<br>
<br>

## 2.2 CBCentralManager와 CBPeripheralManager의 주요 메서드와 델리게이트 메서드를 설명해주세요.

### 1. CBCentralManager (Central 역할):
-	BLE 장치 검색, 연결, 특성 읽기/쓰기 담당.

<img src="https://github.com/user-attachments/assets/25a5155a-78ba-4c28-ac01-f05187e282ab">

#### Delegate 메서드

<img src="https://github.com/user-attachments/assets/17025cee-e869-4ba8-a3d2-9ccf6dece3a7">

<br>

### 2. CBPeripheralManager (Peripheral 역할):
- BLE Peripheral 데이터를 Central에게 제공.

<img src="https://github.com/user-attachments/assets/0593cb36-0bf4-45ef-bf55-de10f2610185">

#### Delegate 메서드:

<img src="https://github.com/user-attachments/assets/2498fff6-5a2a-4398-af4d-f5b9e6309741">


<br>
<br>

## 2.3 BLE 통신에서 사용되는 서비스(Service)와 특성(Characteristic)의 개념과 구현 방법을 설명해주세요.

### 1. 서비스(Service):
- BLE 장치에서 제공하는 기능의 논리적 그룹.
- 예: “심박수 측정” 서비스에는 심박수 데이터 특성과 배터리 상태 특성이 포함.

### 2. 특성(Characteristic):
- 데이터의 최소 단위. 각 특성은 UUID로 식별.
- 읽기/쓰기 권한, 알림(Notify) 기능을 지원.

<br>

### 구현 방법:

#### 1.	Central에서 서비스 및 특성 검색:

```swift
import CoreBluetooth

// BluetoothManager 클래스 정의
class BluetoothManager: NSObject {
    var centralManager: CBCentralManager!
    var discoveredPeripheral: CBPeripheral?

    override init() {
        super.init()
        // CBCentralManager 초기화 및 델리게이트 설정
        centralManager = CBCentralManager(delegate: self, queue: nil)
    }
}

// MARK: - CBCentralManagerDelegate
extension BluetoothManager: CBCentralManagerDelegate {
    // CentralManager 상태 업데이트
    func centralManagerDidUpdateState(_ central: CBCentralManager) {
        if central.state == .poweredOn {
            // BLE 장치 탐색 시작
            centralManager.scanForPeripherals(withServices: nil, options: nil)
        }
    }

    // Peripheral 발견 시 호출
    func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String: Any], rssi RSSI: NSNumber) {
        print("Discovered Peripheral: \(peripheral.name ?? "Unknown")")
        discoveredPeripheral = peripheral
        centralManager.connect(peripheral, options: nil) // Peripheral 연결
    }
}

// MARK: - CBPeripheralDelegate
extension BluetoothManager: CBPeripheralDelegate {
    // Peripheral에서 서비스 발견 시 호출
    func peripheral(_ peripheral: CBPeripheral, didDiscoverServices error: Error?) {
        if let services = peripheral.services {
            for service in services {
                print("Service found: \(service.uuid)")
                peripheral.discoverCharacteristics(nil, for: service) // 특성 검색
            }
        }
    }

    // 서비스의 특성 발견 시 호출
    func peripheral(_ peripheral: CBPeripheral, didDiscoverCharacteristicsFor service: CBService, error: Error?) {
        if let characteristics = service.characteristics {
            for characteristic in characteristics {
                print("Characteristic found: \(characteristic.uuid)")
                // 특성에서 데이터 읽기
                peripheral.readValue(for: characteristic)
            }
        }
    }
}
```

#### 2.	Peripheral에서 서비스 및 특성 제공:

```swift
import CoreBluetooth

// PeripheralManager 초기화
let peripheralManager = CBPeripheralManager()

// MARK: - CBPeripheralManagerDelegate
extension BluetoothManager: CBPeripheralManagerDelegate {
    // PeripheralManager 상태가 업데이트될 때 호출
    func peripheralManagerDidUpdateState(_ peripheral: CBPeripheralManager) {
        // Bluetooth 상태가 "켜짐"인 경우
        if peripheral.state == .poweredOn {
            // 1. 특성 정의
            let characteristic = CBMutableCharacteristic(
                type: CBUUID(string: "1234"), // 고유 UUID
                properties: [.read, .write], // 읽기/쓰기 설정
                value: nil,                  // 초기 값은 nil
                permissions: [.readable, .writeable] // 권한 설정
            )

            // 2. 서비스 정의
            let service = CBMutableService(type: CBUUID(string: "5678"), primary: true)
            service.characteristics = [characteristic] // 특성 추가

            // 3. PeripheralManager에 서비스 추가
            peripheralManager.add(service)

            // 4. Advertising 시작
            peripheralManager.startAdvertising([
                CBAdvertisementDataServiceUUIDsKey: [service.uuid]
            ])
        }
    }
}
```

<br>

### 요약
- Central: Peripheral을 검색하고 데이터를 읽거나 쓰는 역할.
- Peripheral: 데이터를 제공하며 Central의 요청을 처리.
- Service: 기능의 논리적 그룹화, Characteristic: 데이터의 최소 단위.
- CBCentralManager와 CBPeripheralManager를 활용해 BLE 통신을 구현.


<br>
<br>

## 3. Swift의 Copy-on-Write 메커니즘에 대해 설명해주세요.

Swift의 Copy-on-Write(CoW) 메커니즘은 값 타입의 복사가 발생할 때 불필요한 메모리 복사를 방지하여 성능을 최적화하는 방법입니다. 값 타입이 복사될 때, 실제로 값이 변경되기 전까지는 원본 데이터의 참조만 공유합니다. 값이 수정될 경우에만 복사가 발생합니다.

<br>
<br>

## 3.1 Copy-on-Write의 동작 원리와 장점은 무엇인가요?
### 동작 원리 :
#### 1.	값 타입 복사 :
- 값 타입(예: Array, Dictionary, Set)은 일반적으로 복사될 때 동일한 데이터를 새로운 메모리 공간에 저장합니다.

#### 2.	참조 공유:
-	Swift에서는 복사를 요청받은 값이 변경되지 않을 경우, 원본 데이터를 참조만 공유하여 메모리를 절약합니다.

#### 3.	수정 시 복사:
-	복사된 값이 수정되려는 순간, 원본 데이터를 복사하여 별도의 메모리 공간에 저장합니다.

<br>

### 장점:
- 메모리 효율성: 불필요한 데이터 복사를 방지하여 메모리 사용량을 줄입니다.
- 성능 최적화: 동일한 데이터를 복사할 필요가 없으므로 속도가 빨라집니다.
- 스레드 안전성: 값 타입은 원본 데이터와 복사본이 별도로 존재하므로 스레드 간 데이터 충돌을 방지할 수 있습니다.


#### 코드 예시:

```swift
var array1 = [1, 2, 3]
var array2 = array1 // 복사가 발생하지 않고 참조 공유

array2.append(4) // array2를 수정하려고 하면 원본(array1) 데이터를 복사
print(array1) // [1, 2, 3]
print(array2) // [1, 2, 3, 4]
```


<br>
<br>

## 3.2 Copy-on-Write를 사용하는 Swift의 타입에는 어떤 것들이 있나요?
- 기본 컬렉션 타입:
  - Array, Dictionary, Set 등
-	문자열:
  -	String 타입도 CoW 메커니즘을 따릅니다.

<br>
<br>

## 3.3 Copy-on-Write를 고려하여 성능 최적화를 하는 방법을 예시와 함께 설명해주세요.

### 최적화 방법:
#### 1. isKnownUniquelyReferenced 사용:
- 클래스 기반의 데이터 구조에서 원본 데이터가 복사되었는지 확인할 수 있습니다.
- 값 타입에서는 이와 유사한 메커니즘이 내부적으로 동작합니다.

#### 코드 예시:

```swift
final class MyBuffer {
    var storage: [Int]
    
    init(_ storage: [Int]) {
        self.storage = storage
    }
}

struct MyArray {
    private var buffer: MyBuffer
    
    init(_ elements: [Int]) {
        self.buffer = MyBuffer(elements)
    }
    
    // CoW 적용
    mutating func append(_ element: Int) {
        if !isKnownUniquelyReferenced(&buffer) {
            buffer = MyBuffer(buffer.storage) // 복사 발생
        }
        buffer.storage.append(element)
    }
    
    func getStorage() -> [Int] {
        return buffer.storage
    }
}

// 사용 예시
var array1 = MyArray([1, 2, 3])
var array2 = array1 // 참조 공유
array2.append(4)    // 수정 시 복사
print(array1.getStorage()) // [1, 2, 3]
print(array2.getStorage()) // [1, 2, 3, 4]
```

<br>

### 주요 포인트:
- isKnownUniquelyReferenced를 통해 복사가 필요한 시점을 정확히 판단합니다.
- 복사가 필요한 시점에만 데이터를 복사하여 불필요한 메모리 사용을 방지합니다.

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

