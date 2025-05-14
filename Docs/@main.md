>[!question]
>GQ1. @main은 뭘까?
>GQ2. SwiftUI에서는 어떻게 쓰일까?
>GQ2. UIKit에서는 어떻게 쓰일까?

## Description
> iOS앱의 프로그램의 진입점(entry Point)

모든 프로그램에는 진입점(entry Point)이 있어야합니다.
그러한 부분에서 iOS앱의 진입점(시작점)은 
1. main()으로 실행되거나
2. @main 
3. [[@UIApplicationMain]]
으로 정의 됩니다.

그리고 **타입 기반**입니다.

## 주요 기능
- App 컨포머 선언 앞에 @main을 추가하면 main()을 실행해서 앱을 실행
+ iOS 앱의 프로그램의 진입점 역할을 해줌
+ Swift 5.3 이전에는 [[@UIApplicationMain]] 키워드 사용
+ 한개의 앱에 반드시 **한개의 @main 어노테이션만** 존재
+ 타입 기반


## Top-Level
Swift 소스 파일에서 최상위 레벨에 위치한 코드!
최상위에서 직접 실행되는 코드

```swift
// [[Top-Level]] 변수 선언
let globalVariable = "Hello, Swift!"

// Top-Level 함수 선언
func greet() {
    print(globalVariable)
}

// Top-Level 실행 가능한 코드
print("Hello, World!")
```
위와 같이 최상위에서 직접 실행되는 코드!!(함수나  구조체 내부에서 돌아가는게 아님)



### Top-Level은 왜??
Top-Level 코드는 스크립트나 단일 파일로 구성된 프로젝트에 사용(알고리즘 풀때 등)

큰 프로젝트(App 등)은 Top-Level 코드를 최소화하고 `@main`을 통한 명시적 진입점 지정 권장!

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
+ @main
+ UIApplicationMain

## References
- https://green1229.tistory.com/265
- https://hong-sangcompany.tistory.com/24
- https://developer.apple.com/documentation/swiftui/app/main()
- https://docs.swift.org/swift-book/documentation/the-swift-programming-language/attributes/#main




