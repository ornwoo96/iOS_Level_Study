
# 레벨 3

## 1. iOS 앱에서 Core Data를 사용한 데이터 마이그레이션(Migration)에 대해 설명해주세요.
Core Data 마이그레이션은 앱의 데이터 모델(Schema)이 변경될 때, 기존 저장된 데이터를 새로운 데이터 모델 구조에 맞게 변환하는 과정입니다.



<br>
<br>

## 1.1 경량 마이그레이션(Lightweight Migration)과 무거운 마이그레이션(Heavyweight Migration)의 차이점은 무엇인가요?

### 1. 경량 마이그레이션(Lightweight Migration)의 변환 과정
경량 마이그레이션은 Core Data가 자동으로 처리하는 방식으로, 매핑 규칙을 개발자가 작성하지 않아도 됩니다.

#### 변환 과정:

#### 1.	Core Data가 변경된 데이터 모델을 확인:
- 새로운 .xcdatamodeld 파일과 기존 .sqlite 파일을 비교.
- 변경된 엔티티, 속성, 관계 등을 자동으로 파악.
#### 2.	자동 매핑 수행:
- 속성이 추가되었거나 삭제된 경우:
  - 새 속성을 기본값으로 초기화.
  - 삭제된 속성은 데이터베이스에서 제거.
- 속성 이름이 변경된 경우:
  - renamingIdentifier 속성을 설정하여 변경된 속성 이름을 매핑.
#### 3.	데이터 복사:
- 기존 데이터를 새로운 데이터 모델에 따라 재구조화.
- 데이터는 새로운 .sqlite 파일에 복사되거나 업데이트.

#### 예시 코드 (renamingIdentifier 설정):

```swift
class User: NSManagedObject {
    @NSManaged var oldName: String // 기존 속성
    @NSManaged var newName: String // 새 속성
}

extension User {
    @objc func awakeFromInsert() {
        super.awakeFromInsert()
        // renamingIdentifier로 자동 매핑
        renamingIdentifier = "oldName"
    }
}
```

<br>

### 2. 무거운 마이그레이션(Heavyweight Migration)의 변환 과정

무거운 마이그레이션은 매핑 모델과 사용자 정의 변환 로직을 통해 데이터 모델을 변경합니다.

#### 변환 과정:

#### 1.	매핑 모델 생성:
- 소스 모델과 대상 모델의 구조를 비교하여 매핑 모델 생성.
- Xcode의 매핑 모델 에디터를 사용.
#### 2.	엔티티 매핑:
- 소스 엔티티를 대상 엔티티로 매핑.
- 엔티티 이름이 변경되었거나, 엔티티가 분할 또는 병합된 경우 이를 정의.
#### 3.	속성 매핑:
- 소스 속성을 대상 속성으로 매핑.
- 데이터 변환이 필요한 경우 Custom Policy를 추가하여 처리.
#### 4.	데이터 변환:
- NSMigrationPolicy를 사용하여 특정 변환 로직을 추가.
- 소스 데이터를 가공하여 대상 모델에 삽입.
#### 5.	매핑 적용 및 데이터 복사:
- NSMigrationManager가 매핑 모델을 사용하여 데이터를 변환하고 새로운 데이터베이스에 복사.

<br>

### 3. 사용자 정의 데이터 변환 로직

NSMigrationPolicy를 사용하여 데이터를 직접 변환합니다.

#### 예시: 성별 데이터를 String에서 Bool로 변환

기존 데이터 모델에서 gender가 String(“Male”, “Female”)로 저장되었다가, 새로운 모델에서는 Bool(true: Male, false: Female)로 저장.

#### 1.	매핑 모델에서 Custom Policy 설정:
- Xcode에서 매핑 모델 생성.
- Custom Policy에 사용자 정의 클래스 추가.
#### 2.	Custom Policy 구현:
```swift
class GenderMigrationPolicy: NSEntityMigrationPolicy {
    // 이 메서드는 기존 데이터(sourceInstance)를 새로운 데이터(destinationInstance)로 변환하는 로직을 정의
    override func createDestinationInstances(forSource sourceInstance: NSManagedObject,
                                             in mapping: NSEntityMapping,
                                             manager: NSMigrationManager) throws {
        // 1. 새 데이터베이스의 새로운 객체 생성
        // `mapping.destinationEntityName`은 마이그레이션 매핑에서 지정된 대상 엔티티의 이름.
        // `manager.destinationContext`는 새로운 데이터베이스의 컨텍스트.
        let destinationInstance = NSEntityDescription.insertNewObject(forEntityName: mapping.destinationEntityName!,
                                                                      into: manager.destinationContext)
        
        // 2. 기존 데이터의 "gender" 속성 값을 읽고 변환 로직 적용
        // - 기존 데이터에서 `gender` 값을 가져옵니다.
        // - `gender`는 문자열로 되어 있으며, "Male"이면 true, 아니면 false로 변환.
        if let genderString = sourceInstance.value(forKey: "gender") as? String {
            destinationInstance.setValue(genderString == "Male", forKey: "gender") // 변환 후 저장
        }

        // 3. 기존 데이터의 "name" 속성을 그대로 복사
        // - "name" 속성은 변환 없이 대상 객체에 복사.
        destinationInstance.setValue(sourceInstance.value(forKey: "name"), forKey: "name")
    }
}
```

#### 3.	매핑 적용:
```swift
// 1. 이전 데이터 모델과 새로운 데이터 모델을 로드
let sourceModel = NSManagedObjectModel(contentsOf: sourceModelURL)! // 이전 데이터 모델 (source)
let destinationModel = NSManagedObjectModel(contentsOf: destinationModelURL)! // 새로운 데이터 모델 (destination)

// 2. 매핑 모델 생성
// 매핑 모델은 source 모델과 destination 모델 간의 변환 규칙을 정의
let mappingModel = NSMappingModel(from: nil, forSourceModel: sourceModel, destinationModel: destinationModel)!

// 3. NSMigrationManager 초기화
// source 모델에서 destination 모델로 데이터를 변환하는 데 사용
let migrationManager = NSMigrationManager(sourceModel: sourceModel, destinationModel: destinationModel)

do {
    // 4. 데이터 마이그레이션 수행
    try migrationManager.migrateStore(
        from: sourceStoreURL, // 이전 데이터 저장소 URL
        sourceType: NSSQLiteStoreType, // 이전 데이터 저장소 타입 (SQLite)
        options: nil, // 이전 데이터 저장소의 옵션 (nil로 설정)
        with: mappingModel, // 매핑 모델 (변환 규칙)
        toDestinationURL: destinationStoreURL, // 새로운 데이터 저장소 URL
        destinationType: NSSQLiteStoreType, // 새로운 데이터 저장소 타입 (SQLite)
        destinationOptions: nil // 새로운 데이터 저장소의 옵션 (nil로 설정)
    )
} catch {
    // 5. 마이그레이션 실패 시 오류 처리
    print("Migration failed: \(error)")
}
```

#### 4. 데이터 변환의 결과
- Core Data는 위 과정을 통해 소스 데이터베이스의 데이터를 새로운 데이터베이스 구조에 맞게 재구성.
- 데이터 손실을 방지하고 데이터 무결성을 유지.

<br>

### 요약
-	경량 마이그레이션: 자동으로 매핑 규칙을 생성하여 변환.
- 무거운 마이그레이션: 복잡한 매핑 규칙과 데이터 변환 로직을 개발자가 구현.
- Custom Policy를 활용하면 기존 데이터를 새로운 형식으로 변환 가능.

<br>
<br>

## 1.2 매핑 모델(Mapping Model)을 사용하여 데이터를 마이그레이션하는 방법을 설명해주세요.
### 매핑 모델이란?
- 매핑 모델은 Core Data에서 데이터 마이그레이션을 수행하기 위한 규칙을 정의합니다.
- 이전 데이터 모델(Source Model)과 새로운 데이터 모델(Destination Model) 간의 속성 및 관계를 매핑합니다.
- Core Data는 이 매핑 모델을 기반으로 데이터를 변환합니다.

<br>

### 매핑 모델을 사용한 마이그레이션 단계

#### 1.	매핑 모델 생성:
- Xcode에서 .xcdatamodeld 파일을 선택한 후, Editor > Add Mapping Model을 클릭합니다.
- 매핑 모델은 기본적으로 이전 모델과 새로운 모델 간의 기본적인 변환 규칙을 생성합니다.
#### 2.	매핑 모델 수정:
- Xcode의 매핑 모델 에디터를 사용하여 속성 매핑, 관계 매핑, 커스텀 변환 등을 수정할 수 있습니다.
- 필요시 NSEntityMigrationPolicy를 설정하여 복잡한 변환 로직을 작성할 수 있습니다.
#### 3.	코드로 마이그레이션 수행:

```swift
// 소스 모델을 로드
let sourceModel = NSManagedObjectModel(contentsOf: sourceModelURL)! 
// 기존 데이터베이스의 데이터 구조를 정의한 모델 파일을 로드합니다.

// 대상 모델을 로드
let destinationModel = NSManagedObjectModel(contentsOf: destinationModelURL)! 
// 새로운 데이터베이스의 데이터 구조를 정의한 모델 파일을 로드합니다.

// 소스 모델과 대상 모델 간의 매핑 모델 생성
let mappingModel = NSMappingModel(from: nil, forSourceModel: sourceModel, destinationModel: destinationModel)! 
// Xcode에서 생성된 매핑 모델 파일을 기반으로 매핑 규칙을 불러옵니다. 
// 매핑 모델은 소스와 대상 간 데이터 변환 규칙을 정의합니다.

// 마이그레이션 매니저 생성
let migrationManager = NSMigrationManager(sourceModel: sourceModel, destinationModel: destinationModel)
// NSMigrationManager는 소스 모델과 대상 모델 간 데이터를 실제로 변환하는 역할을 합니다.

do {
    // 데이터 마이그레이션 실행
    try migrationManager.migrateStore(
        from: sourceStoreURL,             // 기존 데이터베이스의 위치
        sourceType: NSSQLiteStoreType,    // 기존 데이터베이스의 타입 (SQLite)
        options: nil,                     // 소스 데이터베이스 옵션 (예: 암호화)
        with: mappingModel,               // 매핑 모델 사용
        toDestinationURL: destinationStoreURL, // 새 데이터베이스의 저장 위치
        destinationType: NSSQLiteStoreType,    // 새 데이터베이스의 타입 (SQLite)
        destinationOptions: nil           // 대상 데이터베이스 옵션
    )
} catch {
    // 마이그레이션 실패 처리
    print("Migration failed: \(error)") 
    // 마이그레이션 도중 오류가 발생하면, 오류 메시지를 출력하여 디버깅합니다.
}
```

<br>

### 매핑 모델 사용 시의 장점
- 유연성: 기본적인 속성 복사는 물론, 복잡한 데이터 변환 로직도 지원.
- 자동화: 기본 매핑 규칙은 Xcode가 자동 생성.
- 확장성: 필요할 경우, 커스텀 매핑 로직을 추가 가능.



<br>
<br>

## 1.3 데이터 마이그레이션 중 발생할 수 있는 문제와 해결 방법은 무엇인가요?
### 문제 1: 매핑 모델의 불일치
#### 원인:
- 매핑 모델이 소스 및 대상 모델과 호환되지 않는 경우.
- 매핑 모델의 속성 이름이나 타입 불일치.
#### 해결:
- 매핑 모델의 속성과 데이터 모델의 속성이 일치하는지 확인.
- 필요한 경우 매핑 모델을 다시 생성.

<br>

### 문제 2: 대량 데이터 마이그레이션 시 성능 저하
#### 원인:
- 데이터 변환이 대량으로 발생할 경우 성능 저하.
#### 해결:
- 데이터를 부분적으로 처리하거나, 배치(batch) 마이그레이션을 고려.
- 관계 데이터의 경우 NSEntityMigrationPolicy로 최적화.

<br>

### 문제 3: 데이터 손실
#### 원인:
- 매핑 모델에서 특정 속성이나 관계를 누락.
- 잘못된 변환 로직.
#### 해결:
- 모든 속성 및 관계를 매핑 모델에 명시.
- 데이터 손실을 방지하기 위해 마이그레이션 전에 백업 수행.

<br>

### 문제 4: 데이터 충돌

#### 원인:
- 동일한 데이터가 여러 버전에 걸쳐 수정된 경우.
#### 해결:
- 데이터 충돌 시 우선순위를 지정하거나, 수동으로 병합 처리.

<br>

### 문제 5: 마이그레이션 실패

#### 원인:
- 잘못된 매핑 모델, 손상된 데이터베이스, 또는 불완전한 매핑 로직.
#### 해결:
- 오류 메시지를 확인하고 문제를 디버깅.
- NSMigrationManager에서 발생한 오류를 로깅하여 원인 파악.

<br>

### 문제 해결 방법 코드 예시
```swift
do {
    try migrationManager.migrateStore(
        from: sourceStoreURL,
        sourceType: NSSQLiteStoreType,
        options: nil,
        with: mappingModel,
        toDestinationURL: destinationStoreURL,
        destinationType: NSSQLiteStoreType,
        destinationOptions: nil
    )
} catch let error as NSError {
    print("Migration failed with error: \(error.localizedDescription)")
    if let detailedErrors = error.userInfo[NSDetailedErrorsKey] as? [NSError] {
        for detailedError in detailedErrors {
            print("Detailed error: \(detailedError.localizedDescription)")
        }
    }
    // 백업된 데이터베이스 복구 로직 실행
}
```


<br>
<br>

## 2. iOS 앱의 낮은 메모리 상황 대응 방안과 관련 API에 대해 설명해주세요.


<br>
<br>

## 2.1 낮은 메모리 경고(Low Memory Warning)의 개념과 iOS에서의 동작 방식에 대해 설명해주세요.

### 개념
- iOS는 메모리가 한정된 환경에서 동작하며, 메모리가 부족해질 경우 운영 체제가 앱에 **메모리 경고(Low Memory Warning)** 를 보냅니다.
- 메모리 경고는 백그라운드 앱을 우선 종료하거나, 전면 앱에도 경고를 보내 리소스를 해제하도록 유도합니다.

### 동작 방식
- iOS는 UIApplicationDelegate의 applicationDidReceiveMemoryWarning 메서드와 UIViewController의 didReceiveMemoryWarning 메서드를 통해 메모리 경고를 앱에 전달합니다.
- 메모리 경고를 처리하지 못하면 앱이 강제로 종료될 수 있습니다.

<br>
<br>

## 2.2 didReceiveMemoryWarning() 메서드의 역할과 구현 방법에 대해 설명해주세요.
### 역할
- didReceiveMemoryWarning()은 **뷰 컨트롤러(ViewController)** 에서 메모리 경고를 처리하는 메서드입니다.
- 이 메서드의 역할은:
  - 불필요한 리소스(캐시, 이미지 등)를 해제하여 메모리 사용량을 줄이는 것.
  - 메모리 부족 상황에서 앱이 강제 종료되는 것을 방지하는 것.
 
#### 구현 방법

```swift
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    
    // 캐시 데이터 해제
    imageCache.removeAllObjects()
    
    // 메모리 소모가 큰 리소스 해제
    largeDataArray = nil
    
    print("Memory warning received. Resources released.")
}
```


<br>
<br>

## 2.3 낮은 메모리 상황에서 앱의 안정성을 유지하기 위한 리소스 관리 전략에 대해 설명해주세요.

### 리소스 관리 전략

#### 	1.	캐시 관리:
- 메모리 캐시(NSCache)를 활용하여 필요한 시점에 데이터를 로드.
- 메모리 부족 시 캐시를 비우는 로직 구현.

```swift
let imageCache = NSCache<NSString, UIImage>()
```

<br>

#### 2.	불필요한 리소스 즉시 해제:
- 메모리를 많이 사용하는 객체(이미지, 데이터 배열 등)를 사용 후 즉시 해제.
- 강한 참조를 제거하여 ARC가 정상적으로 메모리를 해제하도록 유도.

<br>

#### 3.	지연 로딩(Lazy Loading):
- 메모리 사용량을 줄이기 위해 데이터나 이미지를 필요한 시점에 로드.

```swift
lazy var largeImage: UIImage? = {
    return UIImage(named: "largeImage")
}()
```

<br>

#### 4.	메모리 프로파일링:
- Xcode의 Instruments를 사용하여 메모리 사용량을 분석하고 메모리 누수(Leaks) 해결.
- Allocations Instrument로 메모리 할당 확인.

<br>

#### 5.	백그라운드에서 리소스 정리:
- 앱이 백그라운드로 전환될 때 불필요한 리소스를 해제.

```swift
func applicationDidEnterBackground(_ application: UIApplication) {
    imageCache.removeAllObjects()
}
```

<br>

#### 6.	메모리 경고 시 동작:
- applicationDidReceiveMemoryWarning에서 앱 전체에 영향을 미치는 리소스를 해제.

```swift
func applicationDidReceiveMemoryWarning(_ application: UIApplication) {
    // 글로벌 리소스 정리
    globalCache.removeAllObjects()
}
```

<br>

### 추가 전략
- 메모리 집약적 작업 최적화:
  - 이미지 처리, 동영상 처리 등을 수행할 때 메모리를 과도하게 사용하지 않도록 최적화.
- 배경 리소스 관리:
  - 백그라운드 작업 시 메모리 사용량을 줄이고 우선순위가 낮은 작업은 취소.

#### 예시: 메모리 관리 통합

```swift
class MyViewController: UIViewController {
    var largeImage: UIImage?
    let imageCache = NSCache<NSString, UIImage>()

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // 불필요한 데이터 해제
        largeImage = nil
        imageCache.removeAllObjects()
        print("Memory warning received. Resources released.")
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        // 대용량 리소스 로드 (필요한 시점에 로드)
        loadLargeImage()
    }

    private func loadLargeImage() {
        DispatchQueue.global().async {
            if let image = UIImage(named: "largeImage") {
                DispatchQueue.main.async {
                    self.largeImage = image
                }
            }
        }
    }
}
```




<br>
<br>


## 3. Swift의 메타타입(Metatype)과 미러(Mirror)에 대해 설명해주세요.

Swift에서 **메타타입(Metatype)** 은 타입 그 자체를 표현하며, 타입의 정보를 다룰 수 있습니다.
**미러(Mirror)**는 런타임에 객체의 속성과 메타데이터를 동적으로 탐색할 수 있는 도구입니다.

<br>
<br>

## 3.1 메타타입을 사용하여 타입 정보에 접근하는 방법은 무엇인가요?

### 메타타입의 개념
- 메타타입은 타입의 타입을 의미합니다.
- Type 키워드를 사용하여 선언됩니다.
- 예: String.Type은 String의 메타타입을 나타냅니다.

#### 코드 예시
```swift
// 메타타입 사용 예시
struct MyStruct {}

let typeOfMyStruct: MyStruct.Type = MyStruct.self // MyStruct의 메타타입
print(typeOfMyStruct) // 출력: MyStruct

// 인스턴스 생성
let instance = typeOfMyStruct.init()
print(instance) // 출력: MyStruct()
```

#### 주요 사용 상황
1. 런타임에 타입 정보 확인:
- 메타타입을 통해 타입을 런타임에 동적으로 확인 가능.
2. 동적 인스턴스 생성:
- .init()을 사용하여 타입의 메타타입을 통해 인스턴스를 생성할 수 있음.

<br>
<br>

## 3.2 미러를 사용하여 객체의 속성을 동적으로 탐색하는 방법을 설명해주세요.

<br>
<br>

## 3.3 메타타입과 미러를 활용한 실제 사용 사례를 들어주세요.

<br>
<br>

## 4. iOS 앱에서 바이너리 프레임워크(Binary Framework)를 생성하고 사용하는 방법은 무엇인가요?
- 바이너리 프레임워크와 소스 코드 프레임워크의 차이점은 무엇인가요?
- 바이너리 프레임워크를 생성할 때 고려해야 할 사항은 무엇인가요?
- 바이너리 프레임워크를 배포하고 버전 관리하는 방법을 설명해주세요.

<br>
<br>

## 5. Combine 프레임워크에서 에러 처리는 어떻게 하나요?
- 에러 이벤트를 처리하기 위한 Operator에는 어떤 것들이 있나요?
- 에러 이벤트 발생 시 Subscription을 자동으로 취소하는 방법은 무엇인가요?
- Combine과 Result 타입을 함께 사용하여 에러 처리를 하는 방법을 설명해주세요.

<br>
<br>

## 6. Swift의 동적 멤버 조회(Dynamic Member Lookup)에 대해 설명해주세요.
- @dynamicMemberLookup 속성의 역할과 사용 방법은 무엇인가요?
- 서브스크립트(Subscript)를 사용하여 동적 멤버 조회를 구현하는 방법을 설명해주세요.
- 동적 멤버 조회를 활용한 실제 사용 사례를 들어주세요.

<br>
<br>

## 7. Swift의 Property Wrapper에 대해 설명해주세요.
- Property Wrapper를 사용하는 이유와 장점은 무엇인가요?
- @State, @Binding, @ObservedObject 등의 Property Wrapper의 차이점과 사용 방법을 설명해주세요.
- Custom Property Wrapper를 만드는 방법과 사용 예시를 들어주세요.

<br>
<br>

## 8. iOS 앱에서 Siri Shortcuts을 구현하는 방법은 무엇인가요?
- Siri Shortcuts의 동작 원리와 사용 사례를 설명해주세요.
- NSUserActivity와 Intents Framework를 사용하여 Siri Shortcuts을 구현하는 방법을 설명해주세요.
- Siri Shortcuts을 사용자 정의하고 파라미터를 전달하는 방법은 무엇인가요?

<br>
<br>

## 9. Swift의 unsafe 포인터(Unsafe Pointer)에 대해 설명해주세요.
- UnsafePointer, UnsafeMutablePointer, UnsafeRawPointer의 차이점과 사용 방법은 무엇인가요?
- unsafe 포인터를 사용할 때 주의해야 할 점은 무엇인가요?
- unsafe 포인터를 사용하여 C 언어 라이브러리와 상호작용하는 방법을 설명해주세요.

<br>
<br>

## 10. Swift의 reflection에 대해 설명해주세요.
- Mirror 타입을 사용하여 객체의 속성을 동적으로 탐색하는 방법은 무엇인가요?
- 런타임에 타입 정보를 검사하고 메서드를 호출하는 방법을 설명해주세요.
- reflection을 사용할 때 주의해야 할 점과 성능 고려 사항은 무엇인가요?

<br>
<br>

## 11. iOS 앱에서 Keychain을 사용하여 민감한 데이터를 안전하게 저장하는 방법은 무엇인가요?
- Keychain Services API를 사용하여 데이터를 저장하고 읽어오는 과정을 설명해주세요.
- Keychain Access Groups를 사용하여 앱 간에 데이터를 공유하는 방법은 무엇인가요?
- Keychain의 접근 제어(Access Control) 옵션과 사용 방법을 설명해주세요.

<br>
<br>

## 12. Swift의 async/await를 사용한 비동기 프로그래밍에 대해 설명해주세요.
- async/await 문법의 동작 원리와 사용 방법은 무엇인가요?
- Task와 TaskGroup을 사용하여 비동기 작업을 관리하는 방법을 설명해주세요.
- 비동기 시퀀스(AsyncSequence)와 비동기 스트림(AsyncStream)의 차이점과 사용 예시를 들어주세요.

<br>
<br>

## 13. iOS 앱에서 WidgetKit을 사용하여 홈 화면 위젯을 구현하는 방법은 무엇인가요?
- 위젯의 생명주기(Life Cycle)와 업데이트 방식을 설명해주세요.
- SwiftUI를 사용하여 위젯의 UI를 구성하는 방법과 주의 사항은 무엇인가요?
- 위젯과 앱 간의 데이터 공유 및 통신 방법을 설명해주세요.

<br>
<br>

## 14. MVVM-C(Coordinator) 아키텍처 패턴에 대해 설명해주세요.
- Coordinator의 역할과 구현 방법을 설명해주세요.
- MVVM-C 패턴의 장단점과 적용 사례를 소개해주세요.

<br>
<br>

## 15. Swift의 @dynamicCallable과 @dynamicMemberLookup에 대해 설명해주세요.
- @dynamicCallable을 사용하여 사용자 정의 호출 가능 타입을 만드는 방법과 사용 예시를 들어주세요.
- @dynamicMemberLookup을 활용하여 동적으로 속성에 접근하는 방법과 실제 사용 사례를 소개해주세요.

<br>
<br>

## 16. Swift의 ABI(Application Binary Interface) 안정성에 대해 설명해주세요.
- ABI 안정성의 개념과 중요성을 설명해주세요.
- ABI 안정성이 프레임워크 개발과 배포에 미치는 영향을 설명해주세요.

<br>
<br>

## 17. iOS 앱에서 Combine 프레임워크를 활용한 반응형 프로그래밍 패턴에 대해 설명해주세요.
- MVVM 아키텍처에서 Combine을 활용한 데이터 바인딩 방법을 예시와 함께 설명해주세요.
- Combine과 SwiftUI를 함께 사용하여 선언적이고 반응형 UI를 구축하는 방법을 소개해주세요.

<br>
<br>

## 18. Swift의 런타임 동작과 성능 최적화 기법에 대해 설명해주세요.
- Swift 런타임의 구조와 동작 방식을 설명해주세요.
- 동적 디스패치, 인라이닝, 스택 프로모션 등 Swift 성능 최적화 기법과 컴파일러 최적화 옵션을 소개해주세요.

<br>
<br>

## 19. iOS 앱의 접근성(Accessibility)을 향상시키기 위한 방법과 고려 사항에 대해 설명해주세요.
- VoiceOver, Switch Control 등 접근성 기술의 동작 원리와 지원 방법을 설명해주세요.
- Dynamic Type, Bold Text 등 시각적 접근성 향상을 위한 기술과 구현 방법을 소개해주세요.
- 접근성 테스트 및 심사 기준, 모범 사례 등을 예시와 함께 설명해주세요.

<br>
<br>

## 20. iOS 앱에서 Objective-C 브리징(Bridging)을 하는 방법과 주의 사항을 설명해주세요.

<br>
<br>
