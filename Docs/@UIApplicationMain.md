>[!question]
>GQ1. @UIApplicationMain은 뭘까?
>GQ2. [[@main]]과 차이점은 뭘까?

## Description
- Swift 5.3(Xcode 12) 이전 앱의 시작점입니다.
- 이후에는 [[@main]]으로 사용


## 주요 기능
+ 앱의 시작점 역할
+ **class에만 선언 가능**

## 코드 예시
```swift
import UIKit @UIApplicationMain class AppDelegate: UIResponder, UIApplicationDelegate { ... }
```

## Keywords
+ [[@main]]

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)