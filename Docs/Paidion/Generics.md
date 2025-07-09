**Write code that works for multiple types and specify requirements for those types.**

# 제너릭이란?

-  **한 번의 구현으로 모든 타입에 적용가능한 코드를 작성하기 위한 문법**
-  코드 중복을 피할 수 있고 명확하고 추상화된 코드를 작성하여 **유지보수가 쉽고 재사용성이 높다**

# 제너릭 함수

```swift
func sumTwoValues<T>(a: T, b: T) {
	return a + b 
}

/* 만약 제너릭이 없었더라면, 타입 별로 같은 내용의 코드를 많이 만들어야 했을 것이다. */
func sumTwoValues(a: Int, b: Int) { return a + b }
func sumTwoValues(a: Double, b: Double) { return a + b }
func sumTwoValues(a: String, b: String) { return a + b }
```
## ☝️ 타입 파라미터 지정

- 함수 이름 마지막에 `< >` 를 사용해서 타입 파라미터 지정한다.
- 대문자로 시작한다. `(예시) <A>, <U>, <Element>, <T, U>`
- 함수 내부에서 파라미터 형식이나 리턴형, 내부 변수 타입으로 사용된다,
- 어떤 타입이 입력되어야 한다는 것을 제시하는 **플레이스 홀더** 역할한다.

## ✌️ 타입 파라미터 사용

- 실제 파라미터 대신 타입 파라미터 사용한다.
- 스위프트에서 이미 많이 사용되고 있다.
- `func swap<T>(_ a: inout T, _ b: inout T)`, `let array: Array<T>, let dictionary: Dictoncary<T>`


# 제너릭 타입

- 함수 이외에도 사용자 지정 클래스, 구조체, 열거형을 제너릭을 이용해 만들 수 있다. 
- 정의할 때 사용했던 타입 파라미터를 **확장자`Extension` 구현**에도 그래도 사용할 수 있다.

```swift
struct IntStack {
	var items: [Int] = []
	mutating func push(_ item: Int) {
		items.append(item)
	}
	
	mutating func pop() -> Int {
		return items.removeLast()
	}
}

struct Stack<Element> {
	var items: [Element] = []
	mutating func push(_ item: Element) { 
		items.append(item)
	}
	
	mutating func pop() -> Element {
		return items.removeLast()
	}
}

extension Stack {
	var topItem: Element? {
		return items.isEmpty ? nil : items[items.count - 1]
	}
	var bottomItem: Element? {
		return items.isEmpty ? nil : items[0]
	}
}
```


## 제너릭 타입 제약

### 기본 문법

- 타입 파라미터의 이름 뒤에 콜론으로 구분한 단일 클래스 또는 프로토콜 제약을 위치하여 타입 제약을 작성한다.

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) { ... }
```

### 사용 예시

- Swift의 모든 타입이 동등 연산자`==` 로 비교할 수 있는 것은 아니기에 `if value == valueToFind`  동등성을 검사를 위해서는 동등 연산자를 사용할 수 있는 타입으로 타입을 제한해야 한다.
- `Equatable` 인 모든 타입은 동등 연산자`==` 를 지원한다. 
- `Equatable` 프로토콜을 준수하는 모든 타입이라는 의미에서 `<T: Equatable>` 로 작성하고 타입을 제한한다.  

```swift
func findIndex(ofString valueToFind: String, in array: [String]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}

func findIndex<T: Equatble>(of valueToFind: T, in array: [T]) -> Int? {
	for (value, index) in array.enumerated() {
		if value == valueToFind {
			return index
		}
	}
	return nil
}
```


# 연관된 타입 (Associated Type)

- 프로토콜을 정의할 때는 함수와 제너릭 타입을 정의할 때와는 달리 `associatedtype` 키워드를 사용해서 제너릭과 같은 기능 사용할 수 있다.
- 선언부가 아닌 구현부에서 `associatedtype` 을 정의한다.
- 해당 프로토콜을 채택하는 곳에서 `associatedtype` 의 실제 타입이 정해진다.

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}

protocol Container<T> { ... } // 에러 발생

struct IntStack: Container {
    var items: [Int] = []
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    
    // Container 프로토콜 준수하는 구현부
    typealias Item = Int // 타입 추론 가능하다면 반드시 적을 필요는 없다.
    
    // 프로토콜에는 Item 이라고 적었지만 Int 를 명시했기 때문에 타입 추론이 가능하다.
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

- 프로토콜을 구현하는 과정에서, 연관된 타입 `associatedtype`에 대한 타입 추론이 가능하다.
- 타입 추론이 가능하다면 `typealias` 에 대한 코드를 반드시 작성할 필요는 없다.

```swift
struct Stack<Element>: Container {
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```