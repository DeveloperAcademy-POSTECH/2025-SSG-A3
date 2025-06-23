>[!question]
>GQ1. StateObject는 언제 쓰일까?
>GQ2. 주의해야할 점은 무엇이 있을까?

## Description
![[Pasted image 20250623234718.png]]
> ObservableObject, @Observable을 채택한 객체에 사용
> State의 객체(Object) 버전

## 주요 기능 및 특징
@StateObject는 언제나 **`참조 타입!!!`**
`ObservableObject`를 채택한 Object 내부의 `@Published는 속성이 바뀔때` 마다 **SwiftUI에게 알려줌!**

SwiftUI는 fresh render를 할때 처음 생성된 `@StateObject property`를 유지!

- @StateObject를 사용하는 view는 내부적으로 ObservableObject를 만듭니다.
- SwiftUI는 @StateObject와 관련된 instance를 따로 설정
- View가 초기화 될 때 다시 사용
- @StateObject는 `한번만 초기화 되고 reuse`되기 때문에 @StateObject로 마크된 `instance`나 `property`는 `새로 얻을 수 없음`
- 또한 `refresh render` 되더라도 **처음 할당된 instance로 유지!**

### 결론
> View가 재생성되도 @StateObject는 한번만 생성되서 계속 재사용 됨

## 코드 예시
```swift
class DataProvider: ObservableObject {
    @Published var count: Int = 0
    
    func increase() {
        count += 1
    }
}

struct CountCalculatorView: View {

    @StateObject private var provider DataProvider = .init()
    
    var body: some View {
        VStack {
            Text("CountCalculatorView: \\(provider.count)")

            Button {
                provider.increase()
            } label: {
                Text("+")
            }
        }
    }
}
```

## Keywords
+ [[SwiftUI Property Wrapper]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/StateOBject)