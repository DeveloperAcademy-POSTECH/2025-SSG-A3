>[!question]
>GQ1. @UIApplicationMain은 뭘까?
>GQ2. @main과 차이점은 뭘까?

## Description
- Swift 5.3(Xcode 12) 이전 앱의 시작점입니다.
- 이후에는 @main으로 사용


## 주요 기능
+ 앱의 시작점 역할
+ **class에만 선언 가능**


## @Main과의 차이점
|               | @Main                    | @UIApplicationMain |
| ------------- | ------------------------ | ------------------ |
| Swift Version | 5.3 +                    | 4.2 +              |
| 적용 대상         | struct, class, Enum 등 가능 | Class만 가능          |
| 명시적 main 함수   | 명시적으로 main() 메서드 구현 가능   | 자동으로 main() 함수 생성  |

그럼 왜 Main으로 넘어가게 되었는가?

@UIApplicationMain은 class만 가능
- **타입 기반의 Swift 코드에서는 미스매칭
	- 이를 해결하기 위해 @main 속성을 사용함으로 해결해줌
- main() 함수 자체가 static 메서드(타입 메서드)
	- 프로토콜에서 확장 메서드 및 기본 클래스로 제공될 수 있습니다.


## 세월의 흐름
1. Object-C 일때 iOS 앱은 **main.m 소스파일**에 main() 함수가 있었습니다.
2. Swift 처음에는 main.swift에서 main 함수 역할
3. UIKit에서 @UIApplicationMain 사용
4. @Main 사용

이 함수로 인해 앱의 진입점을 나타내며 시작하게 됩니다.


## 함수 
```swift
func UIApplicationMain( 
	_ argc: Int32, 
	_ argv: UnsafeMutablePointer<UnsafeMutablePointer<CChar>?>, 
	_ principalClassName: String?, 
	_ delegateClassName: String? 
) -> Int32
```

## UIKit 코드 예시
```swift
import UIKit @UIApplicationMain class AppDelegate: UIResponder, UIApplicationDelegate { ... }
```

## Keywords
+ @main

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)