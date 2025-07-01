### Protocol Generic

- 제네릭이란 타입에 의존하지 않는 범용 코드를 작성할 때 사용함.
- 프로토콜에서도 사용 가능함.

```swift
protocol Stack<T: Equatable> {
  func push(value: T)        // 기존에 했던 제네릭 방식을 쓰면 프로토콜에선 오류가 남!
  func pop() -> T
}

protocol Stack {                      // associatedtype을 이용해야함.
 associatedtype Element
	
	func push(value: Element)
	func pop() -> Element
}

protocol Stack {                      
 associatedtype Element: Equatable    // 제약 주는 것도 가능함.
	
	func push(value: Element)
	func pop() -> Element
}
```

- 프로토콜 내부에 associatedtype을 쓰고 protocol 내부에서 사용할 범용 타입의 이름을 선언.
- 그럼 기존 제네릭처럼 Element라는 이름으로 범용 타입으로 사용 가능해짐.

### 프로토콜을 채택하는 곳 에서는??

```swift
struct VStack: Stack {
	typealias Element = Int
	
	func push(value: Element) {}
	func pop() -> Element { ... }
}
```

- typealias 를 통해서 associatedtyep을 어떤 타입으로 사용할건지 명시해주는게 일반적인 방법.
    
- typealias 없이도 어떤 타입인지 충분히 추론이 가능한 경우 typealias를 생략해도 된다.
    
    ### 장점
    
    - 타입 안전성 유지, 범용성 있는 코드 작성 가능
    - Associated Type을 사용하면 프로토콜에 쓰이는 타입에 placeholder name을 줄 수 있어, 다양한 타입에서 동일한 프로토콜 사용 가능.
    - 컴파일 시점에 타입이 결정되므로 성능적 이점이 있다.
    
    ### 단점
    
    - 의존성 주입에 제약이 있다. → 일반적인 프로토콜처럼 변수의 타입으로 선언할 수 없고 generic constraint로만 사용할 수 있다.