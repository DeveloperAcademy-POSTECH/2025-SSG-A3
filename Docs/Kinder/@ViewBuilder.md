>[!question]
>GQ1. @ViewBuilder는 언제 사용해야 할까?
>GQ2. 왜 아카데미에서는 ViewBuilder를 지향하라고 하는걸까? 

## Description
- @ViewBuilder는 **여러 View를 하나의 클로저에서 조건문이나 반복문으로 반환할 수 있게 해주는 속성 래퍼**입니다.
- SwiftUI는 기본적으로 ViewBuilder를 사용해 body나 클로저에서 여러 View를 조합해 하나의 View로 인식하게 합니다.
- 예를 들어 VStack, HStack 등의 컨테이너 안에 여러 View를 넣을 수 있는 것도 내부에서 @ViewBuilder 덕분입니다.

## **결론**
> SwiftUI의 View 조합에서 **조건문이나 반복문** 등을 통해 **여러 개의 View를 반환하려면 @ViewBuilder가 필요**합니다.

> Swift의 일반 함수에서는 return 하나만 가능하지만, @ViewBuilder로 여러 View를 나열할 수 있습니다.

## 주요 기능

## 여러 개의 SubView를 만들어서 조합 가능
- 컴포넌트별로 여러개의 SubView를 나눌때 사용 가능
- struct로 만들지 않고 간단하게 변수로 만들 수 있음

### View에 init으로 ViewBuilder를 받을 수 있음
아래와 같이 View를 init으로 받아서 사용할 수 있음
`init(title: String, @ViewBuilder content: () -> Content) {}`


## 왜 [아카데미](https://github.com/DeveloperAcademy-POSTECH/swift-style-guide#view-%EC%84%A0%EC%96%B8-%EB%B0%A9%EB%B2%95)에서는 ViewBuilder를 지향할까?

![[Pasted image 20250710112222.png]]

## Struct 장단점
### **✅ 장점**

- **역할 명확성**: struct 이름만으로도 그 View의 역할을 명확히 알 수 있어 유지보수와 협업에 유리
- **상태 관리가 자연스러움**: @State, @Binding, @ObservedObject 등의 속성 주입이 구조적으로 명확하게 처리
- **재사용성 높음**: 다른 View에서도 독립적으로 가져다 쓰기 좋음
- **프리뷰 지원이 더 용이함**: Item.FavoriteButton_Previews 같은 Preview 구조도 자연스럽게 작성 가능
- **View 계층 구조 파악이 쉬움**: 중첩된 View가 많을 때 struct로 분리하는 것이 가독성을 높임

### **❌ 단점 혹은 고려할 점**

- **간단한 UI 표현도 struct로 분리해야 해서 번거로움**
    예: 단순한 Divider, Spacer, Text("...") 같은 것에 대해 굳이 struct로 분리하면 과도한 추상화가 될 수 있음
- **파일 수 증가**
    프로젝트 규모가 커지면 모든 뷰를 struct로 나누는 것이 파일 관리 측면에서 오히려 불편할 수도 있음
### 예시

```swift
struct MyView: View {
    var condition: Bool

    var body: some View {
        VStack {
            Text("Title")
            ContentView(condition: condition)
        }
    }
}

struct ContentView: View {
    let condition: Bool

    var body: some View {
        if condition {
            Text("A")
        } else {
            Text("B")
        }
    }
}
```

## ## **@ViewBuilder 장단점**
### **✅ 장점**
- **간결성**: 반복적인 코드나 View 조합을 간단히 하나의 함수나 프로퍼티로 묶을 수 있음.
- **로직 기반 조건 분기 가능**: if, switch, ForEach 등을 간단하게 뷰 내부에서 분기 처리 가능.    
- **중첩 View에 대한 내부 전용 View 정의**에 유리함: 외부로 재사용되지 않는, 작은 단위의 View 정의에 적합.

### **❌ 단점 혹은 고려할 점**
- **재사용 어려움**: 다른 View에서 사용하려면 별도로 함수나 프로퍼티를 옮기거나 View로 래핑해야 함.
- **속성 주입 어려움**: @Binding, @State, @EnvironmentObject 같은 속성 사용이 제한적이고, 종종 컴파일 에러 유발.
- **디버깅 불편**: 에러 발생 시 해당 function/computed property 내부에서 원인 추적이 어렵고, 오류 메시지가 명확하지 않을 수 있음.
- **프리뷰 불가**: @ViewBuilder 함수 자체는 프리뷰로 볼 수 없음.

### 예시
```swift
var body: some View {
    VStack {
        Text("Title")
        contentView 
    }
}

@ViewBuilder
var contentView: some View {
    if condition {
        Text("A")
    } else {
        Text("B")
    }
}
```


## 결론
| **항목**  | @ViewBuilder var            | struct                                     |
| ------- | --------------------------- | ------------------------------------------ |
| 정의 위치   | View 내부의 프로퍼티               | 별도 struct로 분리                              |
| 상태 주입   | 외부 상태 접근 직접 가능 <br>(부작용 우려) | **명시적**으로 **파라미터 전달** <br>(Binding, State) |
| 재사용     | 어려움 (MyView 내부 전용)          | **쉬움** (다른 View에서도 사용 가능)                  |
| Preview | 불가능                         | **가능** (ContentView_Previews) 생성 가능        |
| 유지보수    | 조건 많아지면 가독성 저하              | 역할별로 분리되어 **명확함**                          |



## 코드 예시
### 기본 예시
```swift
struct MyView: View {
    var isLoggedIn: Bool

    var body: some View {
        content
    }

    @ViewBuilder
    var content: some View {
        if isLoggedIn {
            Text("Welcome back!")
        } else {
            Text("Please log in")
            Button("Log In") {
                // 로그인 로직
            }
        }
    }
}
```

### 커스텀 빌더 예시
```swift
struct CustomCard<Content: View>: View {
    let title: String
    let content: Content

    init(title: String, @ViewBuilder content: () -> Content) {
        self.title = title
        self.content = content()
    }

    var body: some View {
        VStack(alignment: .leading) {
            Text(title)
                .font(.headline)
            content
        }
        .padding()
        .background(Color.gray.opacity(0.2))
        .cornerRadius(8)
    }
}

struct UsageExample: View {
    var body: some View {
        CustomCard(title: "Info") {
            Text("This is a line of info.")
            Text("Here's another line.")
        }
    }
}
```



## Keywords
+ ViewBuilder
+ struct
+ 뷰 분리

## References
- https://developer.apple.com/documentation/swiftui/viewbuilder
- https://zeddios.tistory.com/1324