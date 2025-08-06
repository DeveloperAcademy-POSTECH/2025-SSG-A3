**Write code that works for multiple types and specify requirements for those types.**

# 제너릭이란?

> **한 번의 구현으로 모든 타입에 적용가능한 코드를 작성하기 위한 문법**
> 코드 중복을 피할 수 있고 명확하고 추상화된 코드를 작성하여 **유지보수가 쉽고 재사용성이 높다**

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
## 타입 파라미터 지정

- 함수 이름 마지막에 `< >` 를 사용해서 타입 파라미터 지정한다.
- 대문자로 시작한다. `(예시) <A>, <U>, <Element>, <T, U>`
- 함수 내부에서 파라미터 형식이나 리턴형, 내부 변수 타입으로 사용된다,
- 어떤 타입이 입력되어야 한다는 것을 제시하는 **플레이스 홀더** 역할한다.

## 타입 파라미터 사용

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


## 제너릭 타입과 제약조건

### 기본 문법

- 타입 파라미터의 이름 뒤에 콜론으로 구분한 단일 클래스 또는 프로토콜을 위치하여 타입 제약조건을 작성한다.

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

## 기본 사용법

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

## 연관된 타입과 제약조건

### 연관된 타입에 제약조건 추가하기

- `associatedtype` 에 준수해야하는 프로코톨을 명시하여 연관된 타입에 제약조건을 더할 수 있다.

```swift
protocol Container { 
	associatedtype Item: Equatable
	...
}
```

### 제너릭 Where 절

- `where` 절을 이용해 연관된 타입에 대한 요구조건을 정의할 수 있다.
- 타입 또는 함수의 본문을 여는 중괄호 바로 전에 `where` 키워드를 활용해서 연관된 타입이 특정 프로토콜을 준수하거나 얀관된 타입이 특정 파라미터와 동일해야 한다고 요구할 수 있다.

#### Where 절 사용예시

- `someContainer` 는 `Container` 프로토콜을 준수하는 `C1` 타입이다.
- `anotherContainer` 는 `Container` 프로토콜을 준수하는 `C2` 타입이다.
- `someContainer` 와 `anotherContainer` 는 같은 타입의 `Item`을 지닌다.
- `someContainer` 안에 항목은 서로 다름을 확인하기 위해 비동등 연산자 (`!=`)를 사용할 수 있다.

```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {
		
        if someContainer.count != anotherContainer.count {
            return false
        }
		
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
		
        // All items match, so return true.
        return true
}
```

- 같은 타입의 `Item`을 지닌다면,   `C1` 타입과 `C2` 타입이 다르더라도 `allItemsMatch(_:_:)` 함수를 사용할 수 있다.
- 아래 예시 코드처럼, `Stack` 과 `Array` 는 다른 타입이지만, 타입의 `Item` 의 타입이 모두 `String` 이기 때문에 `allItemsMatch(_:_:)` 함수를  사용할 수 있다.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")

var arrayOfStrings: [String] = ["uno", "dos", "tres"]

if allItemsMatch(stackOfStrings, arrayOfStrings) {
    print("All items match.")
} else {
    print("Not all items match.")
}
// Prints "All items match."
```

#### Where 절을 활용한 제너릭 확장

- 확장 `Extension` 에서도 `where` 절을 활용할 수 있다.
- 아래 코드에서 `isTop(_:)` 구현은 `==` 연산자를 사용하지만 `Stack` 정의에서 `items` 는 동등성 연산을 요구하지 않기 때문에 `==` 연산자를 사용하면 컴파일 에러가 발생한다.
- 아래 확장 은 스택에 `items` 이 `Equatable` 프로토콜을 준수할 때만 `isTop(_:)` 메서드를 추가한다.

``` swift
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}

struct NotEquatable { } 
var notEquatableStack = Stack<NotEquatable>() 
let notEquatableValue = NotEquatable() notEquatableStack.push(notEquatableValue)
// 에러 발생 -> notEquatableStack 의 items는 Equatable 프로토콜 준수하지 않음
notEquatableStack.isTop(notEquatableValue)
```

## 암묵적 제약조건 (Implicit Contraints)

- 많은 경우, 제너릭 코드에서 암묵적으로 `Copyable` 과 같은 매우 일반적으로 사용되는 프로토콜 준수를 요구한다.
- 아래의 1번 코드는 암시적 제약 조건을 가지고 있고, 2번은 명시적으로 준수성을 표시한 것이다. 

```swift
function someFunction<MyType> { ... } // 1번
function someFunction<MyType: Copyable> { ... } // 2번
```

- 스위프트에서 사용하는 많은 타입이 이러한 프로토콜을 준수하기 때문에, 명시적으로 코드를 작성하는 것은 불필요할 수 있다.
- 대신에 예외적으로 암묵적 제약조건을 제한하기 위해, `~(tilde)` 를 활용할 수 있다.
- `~Copyable` 은 복사 가능한 타입과 복사 불가능한 타입 모두 허용한다는 의미이다. (참고 1)
- `~Copyable` 는 복사 불가능한 타입만 요구한다고 오해하지 않아야 한다.

```swift
func f<MyType>(x: inout MyType) {
    let x1 = x  
    let x2 = x 
}

func g<AnotherType: ~Copyable>(y: inout AnotherType) {
    let y1 = y  
    let y2 = y  // Error: Value consumed more than once.
}
```

- 참고 1
<img width="634" alt="Image" src="https://github.com/user-attachments/assets/950a81f6-3cc2-4b8a-8777-d0c92b63fe67" />

# Reference
- [Generics | Swift Docs](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/generics/)
- [Generics | Swift Korean Docs](https://bbiguduk.gitbook.io/swift/language-guide-1/generics)
- [Consume noncopyable types in Swift | WWDC24](https://developer.apple.com/videos/play/wwdc2024/10170)