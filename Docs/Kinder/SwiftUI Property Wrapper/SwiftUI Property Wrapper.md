>[!question]
>GQ1. SwiftUI에서 propertyWrapper란?
>GQ2. 대표적인 상태 관련 propertyWrapper에는 뭐가 있을까?

## Description
### [Swift Property Wrapper](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#Property-Wrappers)
Swift에서 속성 래퍼란?

@proepryWrapper를 이용해서 get/set 동작을 커스터마이징 가능하게 함
```swift
@propertyWrapper
struct Trimmed {
    private var value: String = ""
    
    var wrappedValue: String {
        get { value = "\(value) hello" }
        set { value = newValue.trimmingCharacters(in: .whitespaces) }
    }
    
    init(wrappedValue: String) {
        self.wrappedValue = wrappedValue
    }
}

@propertyWrapper
struct lowerTen {
    var wrappedValue: String {
        get { value = "\(value) hello" }
        set { value = min(value, 10) }
    }
}

// ----- 결과 -----
@Trimmed var name: String = "  John  "

print(name) // "John hello"
```

위와 같이 get을 할때는 value 자체를 보내줌
하지만 set을 할때는 -> trimmingCharacters를 통해서 공백을 없애서 value를 저장해줌


### 사용 이유
- 프로퍼티의 접근을 특정 로직을 통해 제어할 수 있게 함. 
  —> 중복 코드를 인스턴스로 빼내기 때문에 중복을 줄일 수 있음.
- 특정 행동을 정의하는 타입을 만드는 것. 
  특히, 같은 get-set을 활용하는 반복되는 로직을 재사용 해야할 때 PropertyWrapper로 정의하고 해당 로직 자리에 사용하면 동일한 로직을 수행하기 때문에 중복코드를 제거할 수 있음.


## 주요 기능(SwiftUI)
- [@State](https://developer.apple.com/documentation/swiftui/state): **뷰 내부**에서 SwiftUI가 소유하고 관리하는 **로컬 상태**
- [@Binding](https://developer.apple.com/documentation/swiftui/binding): 부모 뷰의 상태를 **자식 뷰에 양방향으로 공유**할 때 사용
- [@StateObject](https://developer.apple.com/documentation/swiftui/stateobject):  뷰에서 **직접 생성한 상태 객체를 소유**하고 **생명주기 전체를 관리**할 때 사용
- [@ObservedObject](https://developer.apple.com/documentation/swiftui/observedobject): 외부에서 주입된 **객체의 상태 변화를 감지**해 뷰를 갱신
- [@EnvironmentObject](https://developer.apple.com/documentation/swiftui/environmentobject): 앱 전역에서 공유되는 **공통 상태를 참조** (ex. 사용자 설정, 로그인 정보 등)


- ObservableObject: ObservableObject 내부에서 **변경 사항을 자동으로 알리는 속성**에 사용
- @Published: 여러 뷰에서 구독할 수 있는 **상태 객체를 선언**할 때 사용 (뷰는 이 객체를 @ObservedObject 등으로 구독)

## Keywords
+ [[@State]]
+ [[@Binding]]
+ [[@StateObject]]
+ [[@ObservedObject]]
+ [[@EnvironmentObject]]

+ ObservableObject
+ @Published

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)