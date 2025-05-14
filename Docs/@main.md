>[!question]
>GQ1. @main은 뭘까?
>GQ2. SwiftUI에서는 어떻게 쓰일까?
>GQ2. UIKit에서는 어떻게 쓰일까?

## Description
5.3 

그리고 **타입 기반**입니다.

---
## 주요 기능
- App 컨포머 선언 앞에 @main을 추가하면 main()을 실행해서 앱을 실행
+ iOS 앱의 프로그램의 진입점 역할을 해줌
+ Swift 5.3 이전에는 @UIApplicationMain 키워드 사용
+ 한개의 앱에 반드시 **한개의 @main 어노테이션만** 존재
+ main() 함수 커스텀 가능
+ 타입 기반



---

## 코드 예시(SwiftUI)
+ 실제 코드 예시를 작성
```swift
@main
struct ThankItApp: App {
    var body: some Scene {
        WindowGroup {
            if userNickname.isEmpty {
                LoginView(onComplete: {})
            } else {
                MainView()
            }
        }
    }
}
```

## 코드 예시(UIKit)
```swift
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        true
    }
}
```


## Keywords
+ Top-Level
+ @UIApplicationMain

## References
- https://green1229.tistory.com/265
- https://hong-sangcompany.tistory.com/24
- https://developer.apple.com/documentation/swiftui/app/main()
- https://docs.swift.org/swift-book/documentation/the-swift-programming-language/attributes/#main




