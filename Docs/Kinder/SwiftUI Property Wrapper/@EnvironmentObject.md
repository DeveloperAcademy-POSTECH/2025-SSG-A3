>[!question]
>GQ1. @EnvironmentObject는 언제 사용해야 할까?
>GQ2. 사용시 주의해야할 점은 무엇이 있을까?

## Description
![[Pasted image 20250624000533.png]]
> @EnvironmentObject는 상위 View에서 제공한 ObservableObject를 **하위 View에서 주입 없이 사용하는 방식**입니다.
> 
> SwiftUI의 전역 상태 관리 도구로 사용되며, **View 간의 의존성을 간단하게 전달**할 수 있습니다.
> 
> 보통 **초기화 시점에 ViewModel을 넘겨주기 어려운 경우**, 예를 들어 여러 화면에서 동일한 데이터를 공유할 때 사용합니다.
> 
> 사용하는 부분에 있어서는 Published로 선언된 부분이 변경 될때 View가 refresh render 됩니다.

## 결론
> 전역적인 변수 공유가 필요한 상황,
> 하위 뷰에 데이터를 주입해줘야할때
> 등등 View들 간의 의존성이 생겼을때 **의존성을 주입 하는 방법**

## 주요 기능
- ObservableObject를 전역처럼 사용 가능
- 여러 뷰에서 동일한 데이터를 공유
- 앱 전체 또는 특정 화면 트리에 상태 주입 가능
- 뷰 간에 **의존성 주입 없이도** 객체 공유 가능
- 객체의 속성 변경 시 자동으로 View 리렌더링

## 코드 예시
```swift
struct CountCalculatorView: View {

    @StateObject private var provider: DataProvider = .init()
    
    var body: some View {
        VStack {

            EnvironmentObjectView()
                .environmentObject(provider)
            
            Button {
                provider.increase()
            } label: {
                Text("+")
            }
        }
    }
}

struct EnvironmentObjectView: View {
    
    @EnvironmentObject var provider: DataProvider
    
    var body: some View {
        Text("ContentView: \(provider.count)")
        SubView()
    }

}

struct SubView: View {
    
    @EnvironmentObject var provider: DataProvider
    
    var body: some View {
        Text("ContentView: \(provider.count)")
    }
}
```
### 코드 설명
- EnvironmentObjectView는 `@EnvironmentObject`가 사용된 DataProvider를 들고 있음
- 때문에 CountCalculatorView에서 EnvironmentObjectView를 초기화 할 때에는 `.environmentObject`라는 별도의 함수를 통해 @StateObject를 넘겨주고 있습니다.
- 하지만 EnvironmentObjectView에서 SubView를 생성하는 경우 SubView내에서 `@EnvironmentObject`를 선언해놓기만 하면, 별도로 초기화 이후 주입해줄 필요는 없습니다.


## @ObservedObject과 차이점

![[Pasted image 20250624001143.png]]
![[Pasted image 20250624001201.png]]

### 코드로 보는 예시
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

            EnvironmentObjectView()
                .environmentObject(provider)
                
            BindingView(provider: provider)
            
            Button {
                provider.increase()
            } label: {
                Text("+")
            }
        }
    }
}

struct EnvironmentObjectView: View {
    @EnvironmentObject var provider: DataProvider
    
    var body: some View {
        Text("ContentView: \(provider.count)")
        SubView()
    }

}

// 위 ObjectView에서 provider를 사용해서 따로 다시 의존할 필요X
struct SubView: View {
    
    @EnvironmentObject var provider: DataProvider
    
    var body: some View {
        Text("ContentView: \(provider.count)")
    }
}

struct BindingView: View {
    @ObservedObject var provider: DataProvider

    var body: some View {
        Text("BindingView: \(provider.count)")
        Button("Increase in BindingView") {
            provider.increase()
        }
    }
 }
```
### 코드 설명
BindingView는 @ObservedObject를 사용해 DataProvider를 전달받고 있음
- 때문에 CountCalculatorView에서 BindingView를 초기화할 때에는 생성자 매개변수를 통해 provider를 **직접 전달**해주고 있음
- @ObservedObject는 상위 뷰에서 생성된 ObservableObject를 **주입받아 감시**하는 방식이기 때문에, 뷰가 리렌더링될 경우에도 해당 인스턴스를 계속 사용 가능함
    
- 반면 SubView처럼 @EnvironmentObject를 사용하는 경우에는 **환경에 등록된 객체를 자동 주입**받지만, @ObservedObject는 반드시 **초기화 시점에 외부에서 전달받아야 함**
- 또한 @ObservedObject는 해당 뷰가 소유하는 객체가 아니기 때문에, **소유 및 생명주기 관리 책임은 상위 뷰에 있음**
    
- @ObservedObject와 @EnvironmentObject 모두 데이터 변경 시 뷰를 자동으로 갱신하지만, **주입 방식과 초기화 방식**에 차이가 있음


## Keywords
+ [[SwiftUI Property Wrapper]]
+ [[@ObservedObject]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/environmentobject)