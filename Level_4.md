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


Core NFC는 iOS 앱이 NFC(Near Field Communication) 태그를 읽거나 쓰기 위한 프레임워크입니다. 이를 통해 NFC 태그에서 정보를 읽어오거나 데이터를 저장할 수 있습니다.

<br>
<br>

## 4.1 NFCNDEFReaderSession과 NFCTagReaderSession의 차이점과 사용 방법을 설명해주세요.

### 1. NFCNDEFReaderSession
-	주요 용도:
  -	NDEF(NFC Data Exchange Format) 형식의 데이터를 읽을 때 사용.
- 제한: NDEF 형식 태그만 지원.
- 사용 예: URL, 텍스트 데이터 등 간단한 정보를 읽어오는 태그.
- 코드 예시:

```swift
import CoreNFC

class NFCReader: NSObject, NFCNDEFReaderSessionDelegate {
    func beginNFCSession() {
        let session = NFCNDEFReaderSession(delegate: self, queue: nil, invalidateAfterFirstRead: true)
        session.begin()
    }

    func readerSession(_ session: NFCNDEFReaderSession, didDetectNDEFs messages: [NFCNDEFMessage]) {
        for message in messages {
            for record in message.records {
                if let payload = String(data: record.payload, encoding: .utf8) {
                    print("Read payload: \(payload)")
                }
            }
        }
    }

    func readerSession(_ session: NFCNDEFReaderSession, didInvalidateWithError error: Error) {
        print("Session invalidated: \(error.localizedDescription)")
    }
}
```

<br>

### 2. NFCTagReaderSession
- 주요 용도:
  - NFC 태그와 저수준에서 상호작용(비NDEF 형식 포함).
  - ISO15693, ISO7816 등과 같은 비표준 태그 읽기/쓰기.
- 제한: 더 복잡한 작업을 처리하므로 사용이 까다로움.
- 사용 예: NFC 기반 인증, 스마트 카드와의 상호작용.
- 코드 예시:

```swift
import CoreNFC

class NFCTagReader: NSObject, NFCTagReaderSessionDelegate {
    func beginTagSession() {
        let session = NFCTagReaderSession(pollingOption: .iso14443, delegate: self, queue: nil)
        session.begin()
    }

    func tagReaderSession(_ session: NFCTagReaderSession, didDetect tags: [NFCTag]) {
        guard let firstTag = tags.first else { return }
        session.connect(to: firstTag) { error in
            if let error = error {
                print("Error connecting to tag: \(error)")
                return
            }
            print("Tag connected successfully")
            session.invalidate()
        }
    }

    func tagReaderSession(_ session: NFCTagReaderSession, didInvalidateWithError error: Error) {
        print("Session invalidated: \(error.localizedDescription)")
    }
}
```


<br>
<br>

## 4.2 NFC 태그 읽기 및 쓰기 과정과 필요한 권한 설정 방법을 설명해주세요.

### 1. 읽기 과정
- 앱이 NFCNDEFReaderSession이나 NFCTagReaderSession을 통해 태그를 탐색.
- 태그와 연결 후 데이터를 읽어옴.
- 예:

```swift
func readerSession(_ session: NFCNDEFReaderSession, didDetectNDEFs messages: [NFCNDEFMessage]) {
    for message in messages {
        for record in message.records {
            print("Record payload: \(record.payload)")
        }
    }
}
```

<br>

### 2. 쓰기 과정
- NFCNDEFPayload 객체를 생성하여 데이터를 구성.
- write 메서드를 호출해 태그에 데이터 저장.
- 예:

```swift
func writeDataToTag(session: NFCNDEFReaderSession, data: String) {
    let payload = NFCNDEFPayload(
        format: .nfcWellKnown,
        type: "T".data(using: .utf8)!,
        identifier: Data(),
        payload: data.data(using: .utf8)!
    )
    let message = NFCNDEFMessage(records: [payload])
    session.writeNDEF(message) { error in
        if let error = error {
            print("Write failed: \(error)")
        } else {
            print("Write successful")
        }
    }
}
```

<br>

### 3. 필요한 권한 설정
- Info.plist에 아래 키 추가:

```xml
<key>NFCReaderUsageDescription</key>
<string>앱이 NFC 태그와 상호작용하기 위해 필요합니다.</string>
```

<br>
<br>

## 4.3 Core NFC를 사용할 때 주의해야 할 점과 제한 사항은 무엇인가요?
### 1. 주의할 점
-	단말기 지원: NFC는 iPhone 7 이상에서만 사용 가능.
-	작업 시간 제한: 세션당 최대 60초.
-	동시성 제한: 한 번에 하나의 NFC 세션만 활성화 가능.
-	오류 처리: 세션이 예상치 못하게 종료될 경우 사용자 경험을 고려한 에러 처리 필요.

### 2. 제한 사항
- NFCNDEFReaderSession은 NDEF 형식 데이터만 처리 가능.
- 백그라운드에서 NFC 태그 탐지가 불가능.

<br>
<br>

## 4.4 Core NFC를 사용할 때 고려해야 할 보안 사항과 모범 사례를 설명해주세요.
### 1. 보안 사항
- 암호화된 데이터 처리: NFC 데이터를 읽거나 쓸 때 암호화된 프로토콜 사용.
- 태그 인증: NFC 태그가 신뢰할 수 있는 출처인지 확인.
- 민감 데이터 제한: 개인정보 및 민감 데이터를 태그에 저장하지 않음.

### 2. 모범 사례
- 사용자 경험 개선: NFC 태그를 잘못 스캔한 경우 사용자에게 명확한 에러 메시지 제공.
- 최적화된 태그 탐지: 필요 없는 태그를 무작위로 스캔하지 않도록 서비스 UUID를 명확히 설정.
- 권한 관리: 사용자에게 NFC 사용 권한을 투명하게 요청하고 사용 이유 설명.

<br>

### 요약

Core NFC는 간단한 NDEF 데이터 읽기부터 비NDEF 태그와의 복잡한 상호작용까지 다양한 기능을 제공합니다. 앱의 요구 사항에 따라 적절한 세션 타입과 보안 전략을 선택하는 것이 중요합니다.

<br>
<br>

## 5. Swift의 actor와 structured concurrency에 대해 설명해주세요.

<br>
<br>

## 5.1 actor의 개념과 동시성 문제 해결 방법을 설명해주세요.

### actor란?

- actor는 Swift 5.5에서 도입된 동시성 기능으로, **데이터 레이스(data race)** 와 같은 문제를 방지하기 위해 설계된 참조 타입입니다.
- actor는 데이터 접근을 안전하게 관리하며, 이를 통해 여러 비동기 작업에서 동일한 데이터에 접근할 때 발생할 수 있는 충돌을 방지합니다.

> 데이터 레이스 : 프로그램에서 두 개 이상의 스레드가 동일한 메모리 위치에 동시에 접근하며, 하나 이상의 접근이 쓰기 작업일 때 발생하는 문제입니다. 이로 인해 프로그램이 예측할 수 없는 동작을 하거나, 버그가 발생할 수 있습니다.


### 특징
- 독점적인 데이터 접근: actor 내부의 속성은 암시적으로 안전하며, 외부에서 접근할 때 반드시 비동기적으로 접근해야 합니다.
- 순차적 실행 보장: actor 내부의 코드 실행은 한 번에 하나의 작업만 실행됩니다.
- thread-safe: actor는 내부 데이터를 스레드 간 안전하게 보호합니다.

<br>

### 동시성 문제 해결 방법
- 외부에서 actor 내부의 데이터를 접근하려면 await 키워드를 사용해야 합니다.
- actor는 내부적으로 직렬화된 실행을 보장하므로 데이터 레이스를 방지합니다.

```swift
actor Counter {
    private var value = 0

    func increment() {
        value += 1
    }

    func getValue() -> Int {
        return value
    }
}

let counter = Counter()

Task {
    await counter.increment()
    print(await counter.getValue()) // 1
}
```

#### 데이터 레이스 발생 예시 (actor 미사용)

```swift
var value = 0

DispatchQueue.global().async {
    value += 1 // Thread 1
}

DispatchQueue.global().async {
    value += 1 // Thread 2
}

// value 값이 1 또는 2가 될 수 있음
```

#### actor를 사용한 데이터 레이스 방지

```swift
actor Counter {
    private var value = 0

    func increment() {
        value += 1
    }

    func getValue() -> Int {
        return value
    }
}

let counter = Counter()

Task {
    await counter.increment() // 첫 번째 작업
    print(await counter.getValue()) // 두 번째 작업
}
```

1. await counter.increment() 실행 시 actor 내부에서 value에 독점적으로 접근.
2. await counter.getValue() 실행 시에도 마찬가지로 독점적으로 접근.
3. actor는 직렬화된 방식으로 작업을 처리하므로, 동시에 여러 스레드가 value에 접근할 수 없게 됩니다.

<br>
<br>

## 5.2 async let과 TaskGroup을 사용한 구조적 동시성 프로그래밍 방법을 예시와 함께 설명해주세요.

### async let

async let은 비동기 코드에서 병렬 작업을 간단히 수행할 수 있도록 도와주는 키워드입니다. 여러 비동기 작업을 시작한 뒤 결과를 동시에 기다릴 수 있습니다.

```swift
func fetchImage() async -> String {
    // 비동기 작업 시뮬레이션
    return "Image Loaded"
}

func fetchData() async -> String {
    // 비동기 작업 시뮬레이션
    return "Data Loaded"
}

func loadResources() async {
    async let image = fetchImage()
    async let data = fetchData()

    let loadedImage = await image
    let loadedData = await data

    print("Loaded: \(loadedImage), \(loadedData)")
}

Task {
    await loadResources()
}
```

<br>

### TaskGroup

TaskGroup은 여러 비동기 작업을 동적으로 생성하고 결과를 집계하는 데 유용합니다. 주로 작업의 수를 미리 알 수 없거나 작업 결과를 순서에 상관없이 처리할 때 사용됩니다.

```swift
func fetchDetails(for ids: [Int]) async -> [String] {
    await withTaskGroup(of: String.self) { group in
        var results: [String] = []

        for id in ids {
            group.addTask {
                return "Detail for \(id)"
            }
        }

        for await result in group {
            results.append(result)
        }

        return results
    }
}

Task {
    let details = await fetchDetails(for: [1, 2, 3])
    print(details) // ["Detail for 1", "Detail for 2", "Detail for 3"]
}
```

<br>
<br>

## 5.3 actor와 structured concurrency를 활용한 효과적인 비동기 프로그래밍 패턴을 소개해주세요.

### 패턴 1: actor와 async let의 조합

actor를 사용해 공유 상태를 안전하게 관리하면서, async let을 통해 비동기 작업을 병렬로 실행합니다.

```swift
actor SharedState {
    private var data: [String] = []

    func addData(_ item: String) {
        data.append(item)
    }

    func getData() -> [String] {
        return data
    }
}

let sharedState = SharedState()

func processItems(_ items: [String]) async {
    async let task1 = sharedState.addData(items[0])
    async let task2 = sharedState.addData(items[1])

    await task1
    await task2

    print(await sharedState.getData())
}

Task {
    await processItems(["Item1", "Item2"])
}
```

<br>

### 패턴 2: actor와 TaskGroup의 조합

다수의 비동기 작업을 TaskGroup으로 처리하면서 actor를 사용해 결과를 안전하게 집계합니다.

```swift
actor ResultCollector {
    private var results: [String] = []

    func addResult(_ result: String) {
        results.append(result)
    }

    func getResults() -> [String] {
        return results
    }
}

func performConcurrentTasks() async {
    let collector = ResultCollector()

    await withTaskGroup(of: String.self) { group in
        for i in 1...5 {
            group.addTask {
                return "Task \(i) completed"
            }
        }

        for await result in group {
            await collector.addResult(result)
        }
    }

    print(await collector.getResults())
}

Task {
    await performConcurrentTasks()
}
```

<br>

### 결론

actor는 안전한 상태 관리를 제공하고, Structured Concurrency(async let, TaskGroup)는 비동기 작업의 체계적인 병렬 실행을 지원합니다. 이 두 기능을 조합하여 비동기 프로그래밍의 가독성과 안정성을 높일 수 있습니다.

<br>
<br>

## 6. iOS 앱에서 Vision 프레임워크를 사용하여 이미지 분석 및 처리를 수행하는 방법은 무엇인가요?

## 6.1 얼굴 감지 및 인식, 바코드 인식, 텍스트 인식 등의 기능 구현 방법을 설명해주세요.

### 1. 얼굴 감지 및 인식

Vision 프레임워크를 통해 이미지에서 얼굴을 감지하거나, Core ML 모델과 결합하여 얼굴 인식을 구현할 수 있습니다.

#### 코드 예제: 얼굴 감지

```swift
import Vision
import UIKit

func detectFaces(in image: UIImage) {
    guard let cgImage = image.cgImage else { return }

    let request = VNDetectFaceRectanglesRequest { (request, error) in
        guard let results = request.results as? [VNFaceObservation] else { return }

        for face in results {
            print("Detected face at: \(face.boundingBox)")
        }
    }

    let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])

    DispatchQueue.global(qos: .userInitiated).async {
        do {
            try handler.perform([request])
        } catch {
            print("Error detecting faces: \(error)")
        }
    }
}
```

<br>

### 2. 바코드 인식

Vision 프레임워크의 VNDetectBarcodesRequest를 사용하여 다양한 형식의 바코드를 인식할 수 있습니다.

#### 코드 예제: 바코드 인식

```swift
func detectBarcodes(in image: UIImage) {
    guard let cgImage = image.cgImage else { return }

    let request = VNDetectBarcodesRequest { (request, error) in
        guard let results = request.results as? [VNBarcodeObservation] else { return }

        for barcode in results {
            print("Barcode payload: \(barcode.payloadStringValue ?? "N/A")")
        }
    }

    let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])

    DispatchQueue.global(qos: .userInitiated).async {
        do {
            try handler.perform([request])
        } catch {
            print("Error detecting barcodes: \(error)")
        }
    }
}
```

<br>

### 3. 텍스트 인식

Vision 프레임워크의 VNRecognizeTextRequest를 사용하여 이미지에서 텍스트를 추출할 수 있습니다.

#### 코드 예제: 텍스트 인식

```swift
func recognizeText(in image: UIImage) {
    guard let cgImage = image.cgImage else { return }

    let request = VNRecognizeTextRequest { (request, error) in
        guard let results = request.results as? [VNRecognizedTextObservation] else { return }

        for observation in results {
            if let topCandidate = observation.topCandidates(1).first {
                print("Recognized text: \(topCandidate.string)")
            }
        }
    }

    request.recognitionLevel = .accurate

    let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])

    DispatchQueue.global(qos: .userInitiated).async {
        do {
            try handler.perform([request])
        } catch {
            print("Error recognizing text: \(error)")
        }
    }
}
```


<br>
<br>

<br>
<br>

## 6.2 Vision 요청(VNRequest)의 종류와 사용 방법, 결과 처리 과정을 설명해주세요.

### 1. Vision 요청의 종류
- VNDetectFaceRectanglesRequest: 얼굴 감지.
- VNDetectBarcodesRequest: 바코드 및 QR 코드 감지.
- VNRecognizeTextRequest: 텍스트 인식.
- VNDetectHumanRectanglesRequest: 사람 감지.
- VNGenerateImageFeaturePrintRequest: 이미지 특징 벡터 생성.
- VNCoreMLRequest: Core ML 모델을 사용한 사용자 정의 분석.

<br>

### 2. Vision 요청 사용 방법
1. 요청 생성: VNRequest를 서브클래스로 생성.
2. 핸들러 생성: 이미지 데이터를 처리하는 VNImageRequestHandler 생성.
3. 요청 수행: perform() 메서드를 호출하여 요청 실행.

```swift
let request = VNDetectFaceRectanglesRequest { (request, error) in
    if let results = request.results as? [VNFaceObservation] {
        for face in results {
            print("Face bounding box: \(face.boundingBox)")
        }
    }
}

let handler = VNImageRequestHandler(cgImage: image.cgImage!, options: [:])
try handler.perform([request])
```

<br>

### 3. 결과 처리 과정
- 요청의 completionHandler에서 결과를 받아옵니다.
- 결과는 주로 VNObservation의 서브클래스(VNFaceObservation, VNBarcodeObservation, VNRecognizedTextObservation) 형태로 반환됩니다.

<br>
<br>

## 6.3 Vision 프레임워크와 Core ML, ARKit 등 다른 프레임워크와의 연동 방법을 소개해주세요.
### 1. Vision + Core ML

Vision 프레임워크는 VNCoreMLRequest를 통해 Core ML 모델을 쉽게 활용할 수 있습니다.

#### 코드 예제: MobileNetV2를 활용한 이미지 분류
- Apple’s Core ML Model Gallery에서 MobileNetV2 모델을 다운로드하여 프로젝트에 추가합니다.
- 모델 파일(MobileNetV2.mlmodel)을 프로젝트에 드래그하여 포함합니다.

```swift
import UIKit
import CoreML
import Vision

// MobileNetV2를 사용하여 이미지를 분류하는 함수
func classifyImage(image: UIImage) {
    // MobileNetV2 모델 불러오기
    guard let model = try? MobileNetV2(configuration: MLModelConfiguration()).model else {
        print("Failed to load Core ML model")
        return
    }

    // UIImage를 Core ML 모델이 처리할 수 있도록 변환
    guard let cgImage = image.cgImage else {
        print("Failed to convert UIImage to CGImage")
        return
    }

    // Vision의 Core ML 모델 래핑
    let vnModel = try! VNCoreMLModel(for: model)

    // Vision Request 생성
    let request = VNCoreMLRequest(model: vnModel) { (request, error) in
        if let results = request.results as? [VNClassificationObservation] {
            for classification in results.prefix(5) { // 상위 5개 결과 출력
                print("Label: \(classification.identifier), Confidence: \(classification.confidence)")
            }
        } else if let error = error {
            print("Error during classification: \(error.localizedDescription)")
        }
    }

    // Request Handler 생성
    let handler = VNImageRequestHandler(cgImage: cgImage, options: [:])

    // 비동기로 분류 수행
    DispatchQueue.global(qos: .userInitiated).async {
        do {
            try handler.perform([request])
        } catch {
            print("Failed to perform classification: \(error)")
        }
    }
}

// 사용 예제: UIImage를 전달하여 분류 수행
let sampleImage = UIImage(named: "sample")! // "sample"은 앱에 포함된 이미지 파일 이름
classifyImage(image: sampleImage)
```

#### 결과 출력 예시

위 코드를 실행하면 콘솔에 다음과 같은 결과가 출력됩니다.

입력: 고양이 이미지 (sample.jpg)

#### 출력:

```
Label: tabby cat, Confidence: 0.98
Label: Egyptian cat, Confidence: 0.01
Label: tiger cat, Confidence: 0.005
Label: lynx, Confidence: 0.003
Label: Persian cat, Confidence: 0.002
```

<br>

### 2. Vision + ARKit

ARKit에서 가져온 카메라 데이터를 Vision으로 분석하여 증강현실 환경에 반영할 수 있습니다.

#### 코드 예제: ARKit 프레임 버퍼를 Vision으로 전달

```swift
import ARKit
import Vision

func processARFrame(_ frame: ARFrame) {
    let pixelBuffer = frame.capturedImage

    let request = VNDetectFaceRectanglesRequest { (request, error) in
        guard let results = request.results as? [VNFaceObservation] else { return }
        print("Detected \(results.count) faces in AR frame.")
    }

    let handler = VNImageRequestHandler(cvPixelBuffer: pixelBuffer, options: [:])
    try? handler.perform([request])
}
```

<br>

### 3. Vision + Core Image

Vision에서 생성된 결과를 Core Image로 후처리하여 이미지 필터링 및 편집에 활용할 수 있습니다.

#### 코드 예제

```swift
func highlightFaces(in image: UIImage, observations: [VNFaceObservation]) -> UIImage {
    UIGraphicsBeginImageContext(image.size)
    let context = UIGraphicsGetCurrentContext()!
    image.draw(at: .zero)

    context.setStrokeColor(UIColor.red.cgColor)
    context.setLineWidth(2.0)

    for face in observations {
        let boundingBox = face.boundingBox
        let rect = CGRect(x: boundingBox.minX * image.size.width,
                          y: (1 - boundingBox.maxY) * image.size.height,
                          width: boundingBox.width * image.size.width,
                          height: boundingBox.height * image.size.height)
        context.stroke(rect)
    }

    let resultImage = UIGraphicsGetImageFromCurrentImageContext()
    UIGraphicsEndImageContext()

    return resultImage ?? image
}
```

<br>

### 결론

Vision 프레임워크는 얼굴 감지, 바코드 인식, 텍스트 인식 등 다양한 이미지 분석 작업을 수행할 수 있으며, Core ML, ARKit 등과 통합하여 더 강력한 기능을 구현할 수 있습니다. VNRequest와 VNImageRequestHandler를 활용해 Vision의 분석 요청과 결과 처리를 체계적으로 수행할 수 있습니다.

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

