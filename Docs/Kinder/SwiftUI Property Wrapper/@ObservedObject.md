>[!question]
>GQ1. ObservedObject는 언제 쓰일까?
>GQ2. 주의해야할 점은 무엇이 잇을까?

## Description
![[Pasted image 20250623234855.png]]
>Binding과 비슷한 기능을 함
>다른 View에서 StateObject를 받아서 사용할때 사용
>State -> Binding
>StateObject -> ObservedObject

## 주요 기능
+ @State에 @Binding이 있다면 @StateObject에는 @ObservedObject가 있음
    - 그러나 Binding과 다르게 $를 붙힐 필요는 없음
- 사용되는 뷰에 의해 ‘생성 또는 소유’ 되지 않은 ObservableObject instances를 wrap하는 데에 사용
- @StateObject와 동일한 유형의 객체에 적용이 됨
- View가 자체 ObservedObject instance를 만들지 않는다는 점을 제외하고 비슷함
- 때문에 fresh render시에 별도로 유지되지 않음

### 결론
> 부모가 property를 소유 → @StateObject
> 부모가 property를 소유하지 않음 → @ObservedObject

## 코드 예시
```swift
class DataProvider: ObservableObject {
    @Published var count: Int = 0
    
    func increase() {
        count += 1
    }
}

struct CountCalculatorView: View {

    @StateObject private var provider: DataProvider = .init()
    
    var body: some View {
        VStack {
            ObservedObjectView(provider: provider)
        
            Button {
                provider.increase()
            } label: {
                Text("+")
            }
        }
    }
}

struct ObservedObjectView: View {
    
    @ObservedObject var provider: DataProvider
    
    var body: some View {
        Text("ContentView: \\(provider.count)")
    }
}
```

## Keywords
+ [[SwiftUI Property Wrapper]]
+ [[ObservableObject]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/ObservedObject)