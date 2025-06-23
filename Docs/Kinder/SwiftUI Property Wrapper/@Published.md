>[!question]
>GQ1. @Published는 언제 쓰일까?
>GQ2. 주의해야할 점은 무엇이 있을까?

## Description
![[Pasted image 20250624000553.png]]
> @Published는 Swift의 Combine 프레임워크에서 제공하는 **속성 래퍼(Property Wrapper)**로, ObservableObject를 채택한 클래스에서 사용됩니다.
> 
> 특정 프로퍼티 값이 바뀔 때마다 **자동으로 변경 알림을 전파**하여, 해당 객체를 구독하고 있는 SwiftUI 뷰들이 뷰를 다시 렌더링하게 만듭니다.
> 
> 단독으로는 사용할 수 없으며, 반드시 ObservableObject 내에서 사용해야 정상 동작합니다.

## 주요 기능
+ 뷰(View)에서 자동으로 **변경 사항을 감지**하여 리렌더링
- Combine의 Publisher로 동작 → .sink를 통해 수동으로 구독도 가능
- @StateObject, @ObservedObject, @EnvironmentObject와 함께 사용할 때 핵심

## 코드 예시
```swift
import SwiftUI

class TimerViewModel: ObservableObject {
    @Published var seconds = 0

    init() {
        Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
            self.seconds += 1
        }
    }
}

struct TimerView: View {
    @StateObject var viewModel = TimerViewModel()

    var body: some View {
        Text("Seconds elapsed: \(viewModel.seconds)")
    }
}
```

```swift
class Weather {
    @Published var temperature: Double
    init(temperature: Double) {
        self.temperature = temperature
    }
}


let weather = Weather(temperature: 20)
cancellable = weather.$temperature
    .sink() {
        print ("Temperature now: \($0)")
}
weather.temperature = 25


// Prints:
// Temperature now: 20.0
// Temperature now: 25.0
```

## Keywords
+ [[ObservableObject]]

## References
- [공식 문서](https://developer.apple.com/documentation/combine/published)