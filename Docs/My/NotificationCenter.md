>[!question]
>GQ1. NotificationCenter는 무엇일까요?
>GQ2. NotificationCenter을 사용하는 방법은 무엇일까요?
>GQ3. NotificationCenter는 NotificationCenter와 delegate, closure, Combine 등 다른 통신 방식과는 무슨 차이점이 있을까요?
>

## Description
- 등록된 관찰자에게 정보를 방송할 수 있는 알림 전송 메커니즘
```swift
class NotificationCenter
```
- **Foundation 프레임워크에 속한, 순수 Swift (혹은 Objective-C) 레벨의 API**
- `class`로 정의된 참조 타입
	- **다수의 옵저버/포스터 간 상태 공유**를 해야 하므로 참조 타입이 적절
- 싱글톤 인스턴스인 `.default`를 주로 사용
- 복수의 옵저버가 동일한 NotificationCenter 인스턴스 구독 가능

## 주요 기능
- 앱 내에서 메세지를 던지면 아무데서나 이 메세지를 받을 수 있게 하는 역할
- NotificationCenter 에 등록된 event 가 발생하면 해당 event에 대한 행동을 취함

+ 실제 활용되는 곳
	+ 보통 백그라운드 작업의 결과, 비동기 작업의 결과 등 현재 작업의 흐름과 다른 흐름의 작업으로부터 이벤트를 받을 때 사용

## 특징
- 객체는 알림 센터에 등록하여 알림(NSNotification 객체)을 수신
	= 객체는 `NotificationCenter`에 자신을 등록(register)해서 특정 알림(Notification)을 받을 수 있음
	- 등록할 때 사용하는 메서드
			1.**`addObserver(_:selector:name:object:)`** – 셀렉터 기반 (Objective-C 방식)
			2. **`addObserver(forName:object:queue:using:)`** – 클로저 기반 (Swift에서 자주 사용)

	- 객체가 자신을 옵저버로 등록할 때, **어떤 알림을 받을지 구체적으로 지정**
		(= 알림 이름(`Notification.Name`)을 지정)
    
	- 하나의 객체가 여러 알림을 받을 경우, **`addObserver`를 여러 번 호출**해서 각각 등록해야 함

---------

 - 모든 앱은 자체적으로 **기본(NotificationCenter.default)** 인스턴스를 하나 가지고 있음
	```swift
let center = NotificationCenter.default // 싱글톤 객체 -> 전역에서 사용
```
	-  but, 별도의 `NotificationCenter()` 인스턴스를 만들어서 사용할 수도 있습니다.
		-> **특정 기능이나 모듈만을 위한 독립된 통신 채널**을 만들 수 있음 (e.g. 테스트, 모듈 간 격리)
    ```swift
let MyCenter = NotificationCenter()
```

---------

- `NotificationCenter`는 **단일 앱 내부에서만** 알림 전달 가능
	- 다른 앱(프로세스) 간에 알림을 주고받으려면, `DistributedNotificationCenter`를 사용
		(**iOS에서는 사용 불가**, macOS 앱끼리 통신할 때 사용)    

## 사용 과정( 코드 예시 )
### 1. Notification 이름 정의 - 세 가지 방식
- **extension 방식 (가장 권장)**
		- 장점
			- 재사용성 좋음
			- 오타 방지 (`.userDidLogin`처럼 자동완성 가능)
			- 프로젝트 전체에서 일관성 유지 가능
	```swift
extension Notification.Name {
	public static let questionDidSave = Notification.Name("questionDidSave")
    public static let receivedDiverFromFile = Notification.Name("receivedDiverFromFile")
}
```
- **직접 선언 (inline 방식)**
		- 단점
			- 문자열 하드코딩 → 오타 위험
			- 여러 번 쓰이면 코드 중복
			- 관리 어려움
	```swift
NotificationCenter.default.post(name: Notification.Name("userDidLogin"), object: nil)
```
- **enum 또는 struct로 감싸기 (네임스페이스화)**
		- 장점
			- `Noti.`처럼 **네임스페이스 역할** 가능
			- 알림 이름을 그룹으로 정리할 수 있음
	```swift
struct Noti {
    static let userDidLogin = Notification.Name("userDidLogin")
}

// 사용
NotificationCenter.default.post(name: Noti.userDidLogin, object: nil)
```

---------

### 2. 이벤트 수신자 등록 (관찰자 등록)
-  **`.addObserver(_:selector:name:object:)` – 셀렉터 기반 (Objective-C 방식)**
	- **`@objc` 메서드 필요**
	- `NSObject`를 상속한 클래스에서 사용
	- UIKit에서 가장 많이 사용됨
```swift
NotificationCenter.default.addObserver(
    self,
    selector: #selector(handleLogin),
    name: .userDidLogin,
    object: nil
)

@objc func handleLogin(_ notification: Notification) {
    // 로그인 처리
}
```

- **`.addObserver(forName:object:queue:using:)` – 클로저 기반 (Swift에서 자주 사용)**
	- `NSObject` 상속 필요 없음
	- 반환값인 `NSObjectProtocol` 토큰을 **보관 후 해제해야 함**
```swift
let token = NotificationCenter.default.addObserver(
    forName: .userDidLogin,
    object: nil,
    queue: .main
) { notification in
    print("클로저 방식: 로그인 알림 수신")
}
```

- **.onReceive(...) 방식**
	- Combine Publisher 생성
	- 즉, NotificationCenter가 이벤트를 발행할 때마다 값을 내보내는 **Publisher<Notification, Never>** 타입이 됨
```swift
.onReceive(NotificationCenter.default.publisher(for: .questionDidSave)) { _in
            viewModel.fetchMyQuestions()
        }
```

| 기준      | addObserver                     | onReceive                    |
| ------- | ------------------------------- | ---------------------------- |
| 주 사용 환경 | UIKit / NSObject 기반 클래스         | SwiftUI View                 |
| 콜백 방식   | `@objc func` (selector)         | 클로저                          |
| 메모리 관리  | ✅ 수동 `removeObserver` 권장        | ❌ 자동 관리됨 (SwiftUI 수명 주기 내에서) |
| 사용      | 주로 `UIViewController`에서 선언해서 사용 | 뷰 수식자에서 주로 사용                |

---------

### 3. 이벤트 발생 (알림 전송)
```swift
if success {
	NotificationCenter.default.post(name: .questionDidSave, object: UUID())
	dismiss()
} else {
	showErrorToast()
}
```

---------

### 4. 옵저버 해제 (선택적)
- **`.addObserver(_:selector:name:object:)` – 셀렉터 기반 (Objective-C 방식)**
	- `name`은 옵셔널 → 생략하면 해당 객체의 **모든 옵저버가 제거**됨
```swift
deinit {
    NotificationCenter.default.removeObserver(self, name: .userDidLogin, object: nil)
}
```

- **`.addObserver(forName:object:queue:using:)` – 클로저 기반 (Swift에서 자주 사용)**
```swift
let token = NotificationCenter.default.addObserver(forName: .userDidLogin, object: nil, queue: .main) { _ in
    print("로그인 알림 받음")
}

NotificationCenter.default.removeObserver(token)
```

- **`.onReceive(...)` 방식**
	- 자동 해제
		- **뷰 생명 주기**와 **상태 관리 시스템(`@StateObject`, `@ObservedObject`)가 알아서 해제
		- @StateObject
			- 해당 뷰에서 직접 객체를 생성하고 소유
		- @ObservedObject
			- **외부에서 전달받은 ViewModel을 관찰만** 함


## NotificationCenter와 delegate, closure, Combine 등 다른 통신 방식과의 차이점

- ### 간단 설명
	- #NotificationCenter 
		- **여러 객체에게 동시에 알림 전파**
		- 느슨한 연결 (관계 없는 객체끼리 통신 가능)
		- 예: 로그인 완료 → 여러 화면에서 감지
    
	- #Delegate
		- **한 객체가 다른 객체에게 책임을 위임**
		- 강하게 연결되어 있음 (두 객체가 명확히 연결됨)
		- 예: TableView → 셀 선택 이벤트 전달
    
	- #Closure
		- **이벤트가 발생했을 때 실행되는 코드 블록**
		- 가장 간단한 통신 방법, 단방향, 1회성 처리에 적합
		- 예: 버튼 탭 시 클로저 실행
    
	- #Combine
		- **데이터 흐름을 스트림처럼 다루는 선언형 방식**
		- `.onReceive`, `@Published`, `sink` 등
		- 비동기/이벤트 흐름 처리에 강력
- ### 상황별 추천
	- #NotificationCenter: 여러 객체에 이벤트 전달
		- **사용예시**: 로그인 알림, 시스템 이벤트
	- #Delegate: 1:1 책임 위임
		- **사용예시**:테이블 셀 클릭, 커스텀 위임
	- #Closure: 짧고 간단한 이벤트 전달
		- **사용예시**: 버튼 클릭 콜백, 애니메이션
	- #Combine: 복잡한 상태 변화 및 비동기 처리
		- **사용예시**: 상태 바인딩, 실시간 처리


## Keywords
+ NSObject
	+ Objective-C와 Swift에서 모든 클래스 계층의 루트 클래스
		+ Swift는 
	+ **어플리케이션의대부분의 객체에 필요한 기본 동작을 정의하는 클래스**
	+ 역할
		+ 객체를 생성, 복사, 비교 및 메모리에서 해제하는 메서드 제공
		+ iOS의 많은기본  기능(런타임, KVO(Key-Value Observing), 셀렉터, 메시지 전달 등)을 제공

- NSNotification 객체
	- 전달되는 알림을 나타내는 객체
	- 알림 이름(name), 전송 객체(object), 부가 정보(userInfo) 등 포함

- DistributedNotificationCenter
	- macOS에서 **다른 프로세스 간에 알림(Notification)을 전달**할 수 있게 해주는 클래스
	- 수신자 (Observer)
	```swift
DistributedNotificationCenter.default().addObserver(forName: .myEvent, object: nil, queue: .main) { _ in
    print("알림 수신됨")
}
```
	- 발신자 (Sender)
	```swift
DistributedNotificationCenter.default().post(name: .myEvent, object: nil)
```

- 클로저(Closure)
	- swift에서 **이름 없는 함수**이자, 코드 블록
	- 변수처럼 전달하거나 저장할 수 있는 "함수 덩어리"
	```swift
{ (매개변수) -> 반환타입 in
    실행할 코드
}
```

- onReceive
	- SwiftUI에서 이벤트(Publisher)를 구독해서 반응하는 수식자(modifier)


## References
- Apple의 공식 문서
	- NotificationCenter
		- https://developer.apple.com/documentation/foundation/notificationcenter
- 참고 tistory: https://silver-g-0114.tistory.com/106