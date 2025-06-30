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

### 코드로 보는 예시(EnvironmentObject)

```swift
class UserSettings: ObservableObject {
    @Published var name: String = "Kim"
}

struct RootView: View {
    @StateObject private var settings = UserSettings()
    
    var body: some View {
        NavigationStack {
            VStack {
                Text("RootView: \(settings.name)")
                
                NavigationLink("Go to ChildView") {
                    ChildView()
                }
            }
        }
        .environmentObject(settings)
    }
}

struct ChildView: View {
    var body: some View {
        VStack {
            Text("ChildView")
            SubChildView()
        }
    }
}

struct SubChildView: View {
    @EnvironmentObject var settings: UserSettings
    
    var body: some View {
        VStack {
            Text("SubChildView: \(settings.name)")
            Button("Change name") {
                settings.name = "Lee"
            }
        }
    }
}
```
### 코드 설명
- 제일 상위 View인 RootView위인 NavigationStack에서 setting를 주입
- 해당 주입을 하면 하위 View 생성 시 Child, SubChild를 만들때 init으로 주입 X
- 해당 데이터들이 View들 간에 의존성이 주입되서 데이터를 동기화 가능


### 코드로 보는 예시(ObjectBinding)
```swift
struct ObservedRootView: View {
    @StateObject private var settings = UserSettings()

    var body: some View {
        NavigationStack {
            VStack {
                Text("RootView: \(settings.name)")
                NavigationLink("Go to ChildView") {
                    ObserveChildView(settings: settings) // 직접 주입해야 함
                }
            }
        }
    }
}

struct ObserveChildView: View {
    @ObservedObject var settings: UserSettings
    
    var body: some View {
        ObserveSubChildView(settings: settings) // 또 주입해야 함
    }
}

struct ObserveSubChildView: View {
    @ObservedObject var settings: UserSettings
    
    var body: some View {
        VStack {
            Text("SubChildView: \(settings.name)")
            Button("Change name") {
                settings.name = "Lee"
            }
        }
    }
}
```
### 코드 설명
- EnvironmentObject와 다르게 Observed는 init시 주입해줘야함
- SubChild로 주입해줄때도 한번더 주입 해줘야함


### 요약
- ObservedObject: 생성자 주입(init)으로 의존성을 정의
	- 하위 View들에 다시 생성자 주입을 통해서 주입해줘야함
- EnvironmentObject: 제일 상위에서 의존성을 주입해서 사용
	- 하위 View들도 Environment는 자동으로 데이터가 받아짐


## Keywords
+ [[SwiftUI Property Wrapper]]
+ [[@ObservedObject]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/environmentobject)