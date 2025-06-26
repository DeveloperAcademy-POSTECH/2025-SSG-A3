>[!question]
>GQ1. ObservableObject는 언제 채택할까?
>GQ2. 채택하면서 주의해야할 점은 무엇이 있을까?

## Description
![[Pasted image 20250623235446.png]]
>ObservableObject는 SwiftUI에서 데이터 변경을 View에 **자동으로 반영**하기 위해 사용하는 프로토콜입니다.

> 이 프로토콜을 채택한 클래스는 @Published 속성을 통해 프로퍼티 변경을 감지하고, 이를 구독하는 뷰(@StateObject, @ObservedObject, @EnvironmentObject)는 자동으로 리렌더링됩니다.

> 만약 ObservableObject를 채택하지 않고 @StateObject나 @ObservedObject로 뷰 모델을 선언하면 **컴파일 에러**가 발생합니다.

## 주요 기능
- @Published 속성의 변경을 감지하고 뷰 업데이트
- Combine의 objectWillChange 퍼블리셔 자동 제공
- @StateObject, @ObservedObject, @EnvironmentObject에서 사용 가능
- 뷰 외부에서 생성한 객체를 상태로 관리 가능 (@ObservedObject)
- 뷰 내부에서 객체를 소유하고 유지 가능 (@StateObject)

## 코드 예시
```swift
import SwiftUI

class CounterViewModel: ObservableObject {
    @Published var count = 0
    
    func increase() {
        count += 1
    }
}

struct CounterView: View {
    @StateObject private var viewModel = CounterViewModel()
    
    var body: some View {
        VStack {
            Text("Count: \(viewModel.count)")
            Button("증가") {
                viewModel.increase()
            }
        }
    }
}
```

```swift
class Contact: ObservableObject {
    @Published var name: String
    @Published var age: Int


    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }


    func haveBirthday() -> Int {
        age += 1
        return age
    }
}


let john = Contact(name: "John Appleseed", age: 24)
cancellable = john.objectWillChange
    .sink { _ in
        print("\(john.age) will change")
}
print(john.haveBirthday())
// Prints "24 will change"
// Prints "25"
```

## Keywords
+ [[@StateObject]]
+ [[@ObservedObject]]
+ [[@EnvironmentObject]]
+ [[@Published]]

## References
- [공식 문서](https://developer.apple.com/documentation/combine/observableobject)