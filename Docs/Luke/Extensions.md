## **Description**

**Extension(확장)** 은 기존 타입(클래스, 구조체, 열거형, 프로토콜 등)에 **새로운 기능을 추가**하는 Swift 문법

- 기존 코드(타입 정의)를 **수정하지 않고도 기능을 추가**할 수 있다.
- Swift에서 기본으로 제공하는 타입 (`Int`, `String`, `Array` 등)이나, 외부 라이브러리/프레임워크에서 가져온 타입에도 적용할 수 있다.
- Swift의 거의 모든 타입에 적용 가능하며, 특히 **프로토콜 확장**과 **제네릭 제약 확장**이 매우 강력한 기능이다.


---

## **주요 기능**

### **계산 프로퍼티 추가 (Computed Properties)**

- 기존 타입에 **계산된 값을 반환하는 프로퍼티**를 추가할 수 있다.
- 값을 저장하는 **저장 프로퍼티는 추가할 수 없다.**

### **인스턴스 메서드 및 타입 메서드 정의**

- 기존 타입에 **인스턴스 메서드**와 **타입 메서드(`static`)** 를 추가할 수 있다.
- `struct`나 `enum`과 같은 **값 타입**에서는 내부 값을 수정하려면 `mutating` 키워드가 필요하다.

### **이니셜라이저 제공 (Initializers)**

- 기존 타입에 **새로운 초기화 방법**을 정의할 수 있다.

### **서브스크립트 정의 (Subscripts)**

- 배열처럼 인덱스를 사용하여 값을 읽어오는 **서브스크립트 문법**을 추가할 수 있다.
- 문자열, 컬렉션 등의 인덱싱 기능을 보완할 때 자주 사용된다.

### **중첩 타입 정의 및 사용 (Nested Types)**

- 기존 타입 내부에 **새로운 `enum`, `struct`, `class` 등의 중첩 타입**을 정의할 수 있다.
- 주로 상태 분류나 내부 전용 로직 구성에 사용된다.

### **기존 타입에 프로토콜 채택 추가 (Protocol Conformance)**

- 기존 타입이 특정 **프로토콜을 채택**하고, 요구사항을 구현할 수 있다.
- 직접 만든 타입이 아니더라도, 외부에서 가져온 타입에도 **새로운 책임을 부여**할 수 있다.

### **프로토콜 확장 (Protocol Extension)**

- **프로토콜 자체에 기본 구현을 추가**할 수 있다.

### **제네릭 조건 확장 (Generic Constraint Extension)**

- 제네릭 타입 중에서도 **특정 조건을 만족하는 경우에만 확장**을 적용할 수 있다.

### **오버라이드 제한**

- `extension`에서는 기존 메서드를 **override(재정의)** 할 수 없다.
- 기존 동작을 변경할 수는 없고, **기능을 추가하는 용도로만** 사용된다.

---

## **코드 예시**

### **계산 프로퍼티 추가 (Computed Properties)**

```swift
extension Double {
  var km: Double { self * 1_000.0 }
  var m: Double  { self }
  var cm: Double { self / 100.0 }
  var mm: Double { self / 1_000.0 }
}

let distance = 1.5.km
print(distance)// 출력: 1500.0
```
- `Double` 타입에 단위를 붙여 계산할 수 있는 속성을 추가
- `1.5.km`은 내부적으로 `1.5 * 1000` 계산을 수행하고, `1.5.km`처럼 직관적인 코드로 단위 변환이 가능하다.
- 이처럼 `extension`을 이용하면 **기존 타입에 의미 있는 기능**을 추가 할 수 있다.

### **❌ 저장 프로퍼티 추가 시 에러**

```swift
extension Double {
// ❌ Error: Extensions may not contain stored properties
  var stored: Double = 100
}
```
- `extension` 내부에서는 값을 저장하는 **저장 프로퍼티**를 정의할 수 없다.
- 계산된 값을 반환하는 **계산 프로퍼티만 허용된**다.


Swift는 모든 타입의 **인스턴스가 차지하는 메모리 크기**를 컴파일 시점에 고정합니다.
extension으로 새로운 저장 프로퍼티를 추가하면
- 인스턴스 크기가 변경되어야 하고
- 이미 생성된 객체와의 불일치로 **메모리 오류**가 발생할 수 있기 때문입니다.

```swift
struct Person {
  var name: String  // 8바이트
  var age: Int      // 8바이트
}
// → Person 인스턴스는 총 16바이트로 고정

extension Person {
  var address: String  // ❌ 추가 불가: 인스턴스 크기를 24바이트로 바꿔야 함
}
```

### **인스턴스 및 타입 메서드 정의**

```swift
struct Point {
  var x = 0.0, y = 0.0
}

extension Point {
  mutating func moveBy(x deltaX: Double, y deltaY: Double) {
    x += deltaX
    y += deltaY
  }

  static func origin() -> Point {
    Point(x: 0, y: 0)
  }
}

var position = Point()
position.moveBy(x: 3, y: 4)
print(position)// 출력: Point(x: 3.0, y: 4.0)

let originPoint = Point.origin()
```

- `moveBy`는 인스턴스의 `x`, `y` 값을 직접 수정하는 메서드이며, `Point`는 값 타입인 구조체이므로 `mutating` 키워드가 필요하다.
- `origin()`은 타입 메서드로, `Point.origin()`처럼 사용된다.

### **이니셜라이저 추가**

```swift
struct Size {
  var width = 0.0, height = 0.0
}

struct Point {
  var x = 0.0, y = 0.0
}

struct Rect {
  var origin = Point()
  var size = Size()
}

extension Rect {
  init(center: Point, size: Size) {
    let originX = center.x - (size.width / 2)
    let originY = center.y - (size.height / 2)
    self.init(origin: Point(x: originX, y: originY), size: size)
  }
}

let centerPoint = Point(x: 4.0, y: 4.0)
let size = Size(width: 3.0, height: 3.0)
let centeredRect = Rect(center: centerPoint, size: size)
```

- 원래는 `Rect`를 만들 때 `origin`과 `size`를 통한 초기화 방식만 사용했어야 했다.
- 이제는 `extension`을 사용해, 중심 좌표(`center`) 와 크기(`size`) 를 기반으로도 `Rect`를 초기화할 수 있다.

### **서브스크립트 정의**

```swift
extension String {
  subscript(i: Int) -> Character {
    self[self.index(self.startIndex, offsetBy: i)]
  }
}

let text = "Swift"
print(text[2])// 출력: "i"
```
- 원래 Swift의 `String`은 정수 인덱스로 접근이 불가능하다.
- 위 확장을 통해 `"Swift"[2]`처럼 배열처럼 접근할 수 있게 된다.
- 단, 인덱스 범위를 벗어나면 앱이 크래시되므로, 안전한 범위 체크가 필요하다.

### **중첩 타입 정의**

```swift
extension Int {
  enum Kind {
    case negative, zero, positive
  }

  var kind: Kind {
    switch self {
    case 0: return .zero
    case let x where x > 0: return .positive
    default: return .negative
    }
  }
}

[3, 0, -2].forEach {
  print("\($0)의 부호는 \($0.kind)")
}
// 3의 부호는 positive
// 0의 부호는 zero
// -2의 부호는 negative
```

- `Int` 타입 내부에 `Kind`라는 `enum`을 중첩해서 정의하고, 정수의 부호를 판단하는 기능을 추가
- 중첩 타입은 **타입 내부에서만 사용되는 로직을 깔끔하게 분리**하는 데 유용하다.

### **기존 타입에 프로토콜 채택 추가**

```swift
protocol Describable {
  var description: String { get }
}

extension Int: Describable {
  var description: String {
    "숫자: \\(self)"
  }
}

print(42.description)// 출력: 숫자: 42
```

- `Int` 타입에 `Describable` 프로토콜을 채택시키고, 그 요구사항인 `description` 속성을 구현
- `extension`을 통해 기존 타입인 `Int`에 프로토콜을 채택시킬 수 있다.
- 직접 만든 타입이 아니더라도 기능을 추가할 수 있다.

### **프로토콜 확장 (Protocol Extension)**

```swift
protocol Greetable {
  func greet()
}

extension Greetable {
  func greet() {
    print("안녕하세요!")
  }
}

struct Person: Greetable { }
Person().greet()// 출력: 안녕하세요!
```

- 프로토콜 자체에 기본 구현을 추가해 프로토콜을 원하는 방식으로 확장할 수 있다.

### **제네릭 조건 확장 (Generic Constraint Extension)**

```swift
extension Array where Element: Equatable {
  func isFirstEqual(to value: Element) -> Bool {
    self.first == value
  }
}

let numbers = [1, 2, 3]
print(numbers.isFirstEqual(to: 1))// 출력: true
```

- `Array`의 요소가 `Equatable`을 따를 때에만 사용할 수 있도록 만들 수 있다.
- 이렇게 하면 **불필요한 기능을 모든 타입에 노출시키지 않고**, 조건을 만족하는 타입에서만 사용할 수 있다.

### **오버라이드 제한**

```swift
class Animal {
  func sound() {
    print("동물 소리")
  }
}

extension Animal {
// override func sound() { ... } ❌ 컴파일 에러
}

// Extension으로 새로운 기능 추가
extension Animal {
  func walk() {
    print("동물이 걷는다")
  }
}

let dog = Animal()
dog.sound()// 출력: 동물 소리
dog.walk()// 출력: 동물이 걷는다
```

- `extension`에서는 기존 메서드를 **override(재정의)** 할 수 없다.
- 기존 동작을 변경하는 것이 아니라, **기능을 추가하는 용도**로만 사용된다.


extension은 **기능을 덧붙이는** 용도로 설계되었습니다.
기존 메서드를 재정의하면, 어떤 구현이 실행될지 **예측이 어려워지고**
모듈 간 의존성이 복잡해지기 때문입니다.
override가 필요하다면 **서브클래스를 상속**하여 구현해야 합니다.

```swift
class Animal {
  func sound() {
    print("동물 소리")
    }
}

extension Animal {
    // ❌ override func sound() { print("다른 소리") }
    //    허용되지 않음: 어떤 소리가 나올지 예측 불가능
}
```


---

## References

- [Swift 공식 문서 - Extensions](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/extensions/)