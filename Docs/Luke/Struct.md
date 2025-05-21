
## 나의 Challenge
- [[Struct]] 에 대해서 알아보자

## GQ
- Struct 가 무엇인가?
- Struct 를 왜 사용하나?
- Struct의 기능들은 무엇이 있을까?

## GA
- Swift 공식 문서
- 블로그 참고
- AI에게 물어보기


## Description

Swift에서 `struct`(구조체)는 여러 개의 값을 하나로 묶어 하나의 단위로 사용할 수 있게 해주는 사용자 정의 타입이다.

예를 들어, 사람을 표현할 때 이름, 나이, 주소처럼 여러 데이터를 따로 관리하기보다는, 구조체로 하나로 묶으면 더 간단하고 명확하게 표현할 수 있다.

단지 데이터를 담는 것에 그치지 않고, 그 데이터와 관련된 동작([[메서드]])도 함께 정의할 수 있다.

---

## 주요 기능

### 데이터 묶음 (캡슐화)

- 여러 개의 속성([[프로퍼티]])을 하나의 단위로 그룹화 할 수 있다.
- 이렇게 하면 관련된 데이터를 하나의 단위로 쉽게 관리할 수 있다

### 저장/계산 [[프로퍼티]]

- 구조체 내부에 선언된 변수(`var`)나 상수(`let`)인 [[프로퍼티]]라고 하며, 저장된 값을 담거나 계산된 값을 반환할 수 있다
    - 저장 프로퍼티: 실제 값을 저장하는 프로퍼티
    - 계산 프로퍼티: 값을 저장하지 않고, 특정 계산을 통해 값을 반환하는 프로퍼티

### [[메서드]] (동작)

- 구조체가 수행할 수 있는 동작을 정의하는 함수이다.
- 메서드는 구조체의 데이터를 읽거나, 변경할 수 있다.
- 구조체 내부에서 자신의 속성을 변경하려면 [[mutating]] 키워드를 붙여야 한다.

1. [[mutating]] 키워드가 필요한 이유
2. 구조체는 [[값 타입]]이다.
    1. 구조체는 [[값 타입]]이기 때문에, 기본적으로 모든 [[프로퍼티]]가 “불변"으로 취급된다.
    2. 그래서 기본적으로 구조체 인스턴스 [[메서드]]에서는 내부 값([[프로퍼티]])를 수정할 수 없다.
    3. 다만 [[mutating]]키워드를 활용할시 내부 값([[프로퍼티]])를 수정할 수 있다. (단 이때는 var로 선언된 인스턴스만 변경 가능)

### 값 복사(Value Copy)

- 구조체는 [[값 타입]]이다.
- [[값 타입]]은 변수나 상수에 할당되거나 함수에 전달될 때 값이 복사되는 것을 의미한다.
- 즉, 구조체 인스턴스를 다른 변수에 할당하거나 함수에 전달하면 복사된 새로운 복사본이 생성된다.
- 복사된 인스턴스는 원본과는 독립적이기 때문에, 한쪽을 변경해도 다른 쪽에는 영향을 주지 않는다.

### [[이니셜라이저]](초기화 구문)

- Swift는 구조체에 기본적으로 저장 프로퍼티가 있다면, 자동으로 이니셜라이저를 제공하기 때문에 생성자를 직접 만들지 않아도 됩니다.
- 필요하면 이니셜라이저를 직접 정의할 수도 있다

### [[프로토콜]] 채택

- 프로토콜은 특정 속성([[프로퍼티]])이나 [[메서드]]를 반드시 구현하도록 약속하는 일종의 규칙이다.
- 구조체는 프로토콜을 채택하여 특정 기능을 구현할 수 있다.

### [[타입 프로퍼티]]/메서드 (static)

- 구조체 내부에서 static 키워드를 사용해 타입 자체에 속하는 속성이나 메서드를 정의할 수 있습니다.
- 이러한 속성이나 메서드는 구조체의 모든 인스턴스가 공유할 수 있다.

---

## 코드 예시

### 테이터 묶음 (캡슐화)

```swift
struct Person {
  var name: String
  let age: Int
}

let user = Person(name: "루크", age: 28)
print(user.name) // 루크
```

- `name과` `age라는` 두 개의 값을 `Person`이라는 타입으로 하나로 묶어서 관리한다.
- 이제 이름과 나이를 하나의 단위로 다룰 수 있다.

### 저장 프로퍼티 / 계산 프로퍼티

```swift
struct Rectangle {
  var width: Double   // 저장 프로퍼티
  var height: Double  // 저장 프로퍼티
  
  var area: Double {  // 계산 프로퍼티
    return width * height
  }
}

let r = Rectangle(width: 4, height: 5)
print(r.area) // 20.0
```

- `area`는 값을 저장하지 않고, `width*height`를 계산해서 반환한다.
- 변할 수 있는 값을 실시간으로 계산하는데 유용하다.

### 메서드 / mutating 키워드

```swift
struct Counter {
  var value: Int = 0
  
  func show() {
    print("현재 값은 \(value)입니다.")
  }
  
  mutating func increase() {
    value += 1
  }
}

var counter = Counter()
counter.increase()
counter.show() // 현재 값은 1입니다.
```

- 메서드는 구조체가 할 수 있는 동작을 정의한다.
- `increase()`는 내부 값을 바꾸는 메서드라서 `mutating` 키워드가 필요하다.

### 값 복사 (Value Type)

```swift
struct User {
  var nickname: String
}

var a = User(nickname: "루크")
var b = a          // 복사됨
b.nickname = "승찬"

print(a.nickname) // 루크
print(b.nickname) // 승찬
```

- 구조체는 복사되어 전달되기 때문에, `a`와 `b`는 서로 다른 인스턴스이다.
- 값을 바꿔도 서로 영향을 주지 않는다. (값 타입이기 때문)

### 이니셜라이저

1. 자동 생성

```swift
struct Rectangle {
  var width: Int
  var height: Int
}

let square = Rectangle(width: 10, height: 10)
```

- Swift는 저장 프로퍼티가 모두 초기화될 수 있도록 자동으로 이니셜라이저를 만들어 준다.
- 별도로 `init`을 작성하지 않아도 인스턴스를 생성할 수 있다.

2. 직접 정의

```swift
struct Rectangle {
  var width: Int
  var height: Int
  var area: Int
  
  init(width: Int, height: Int) {
    self.width = width
    self.height = height
    self.area = width * height
  }
}

let rectangle = Rectangle(width: 5, height: 10)
```

- 특정 프로퍼티(`area`)를 계산된 값으로 초기화하기 위해 커스텀 이니셜라이저를 정의한다.

### 프로토콜 채택

```swift
protocol Greetable {
  func greet()
}

struct Student: Greetable {
  func greet() {
    print("안녕하세요!")
  }
}

let s = Student()
s.greet() // 안녕하세요!
```

- `Greetable` 프로토콜을 채택한 구조체는 `greet()` 메서드를 반드시 구현해야 한다.

### static 프로퍼티 / 메서드

```swift
struct App {
  static let version = "1.0.0"
  
  static func showVersion() {
    print("앱 버전: \(version)")
  }
}

print(App.version)      // 1.0.0
App.showVersion()       // 앱 버전: 1.0.0
```

- `static` 키워드로 선언된 속성/메서드는 인스턴스를 만들지 않고도 사용할 수 있다.
- 모든 인스턴스가 공유하는 정보에 적합하다.

---

## Keywords

- [[프로퍼티]]
- [[메서드]]
- [[mutating]]
- [[값 타입]]
- [[이니셜라이저]]
- [[프로토콜]]
- [[타입 프로퍼티]]

---

## References

[Swift.org - Struct and Classes](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/classesandstructures/)
[Swift.org - Properties](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/)
[Swift.org - Methods (Mutating)](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods/#Modifying-Value-Types-from-Within-Instance-Methods)
[Swift.org - Initialization](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization/)
[Swift.org - Protocols](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/protocols/)
[Swift.org - Properties (Type Properties)](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties/#Type-Properties) 
[Swift.org - Value and Reference Types (개념 정리 아티클)](https://www.swift.org/documentation/articles/value-and-reference-types.html)
[Apple Developer - Swift 시작하기 가이드](https://developer.apple.com/swift/get-started/)
