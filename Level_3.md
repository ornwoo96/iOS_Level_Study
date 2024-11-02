
# 레벨 3

## 1. iOS 앱에서 Core Data를 사용한 데이터 마이그레이션(Migration)에 대해 설명해주세요.
- 경량 마이그레이션(Lightweight Migration)과 무거운 마이그레이션(Heavyweight Migration)의 차이점은 무엇인가요?
- 매핑 모델(Mapping Model)을 사용하여 데이터를 마이그레이션하는 방법을 설명해주세요.
- 데이터 마이그레이션 중 발생할 수 있는 문제와 해결 방법은 무엇인가요?

<br>
<br>

## 2. iOS 앱의 낮은 메모리 상황 대응 방안과 관련 API에 대해 설명해주세요.
- 낮은 메모리 경고(Low Memory Warning)의 개념과 iOS에서의 동작 방식에 대해 설명해주세요.
- didReceiveMemoryWarning() 메서드의 역할과 구현 방법에 대해 설명해주세요.
- 낮은 메모리 상황에서 앱의 안정성을 유지하기 위한 리소스 관리 전략에 대해 설명해주세요.

<br>
<br>


## 3. Swift의 메타타입(Metatype)과 미러(Mirror)에 대해 설명해주세요.
- 메타타입을 사용하여 타입 정보에 접근하는 방법은 무엇인가요?
- 미러를 사용하여 객체의 속성을 동적으로 탐색하는 방법을 설명해주세요.
- 메타타입과 미러를 활용한 실제 사용 사례를 들어주세요.

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
