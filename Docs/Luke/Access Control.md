
접근 제어는 다른 소스 파일이나 모듈에서 코드 접근에 대해 제한하는 기능이다. 이 기능은 코드의 구현 세부사항을 숨기고 해당 코드에 접근하고 사용될 수 있는 기능들을 지정할 수 있다.

클래스, 구조체, 그리고 열거형뿐만 아니라 해당 타입에 속하는 프로퍼티, 메서드, 초기화 구문과 서브스크립트에 할당할 수 있다.

---

## 접근 제어 단위

Swift에서 접근 제어는 다음과 같은 세 가지 단위를 기준으로 작동한다.

### 모듈 (Module)

- 코드 배포 단위이며, 하나의 프레임워크 또는 애플리케이션이 하나의 모듈이 된다.
- `import` 키워드를 통해 다른 모듈을 가져올 수 있다. 예: `SwiftUI`, `UIKit`, `Foundation` 등

### 소스 파일 (Source File)

- 모듈 내에 포함된 `.swift` 파일 하나를 의미한다.

### 패키지 (Package)

- 여러 모듈의 묶음 단위이다.
- 하나의 패키지 안에는 여러 개의 모듈이 포함될 수 있다.

---

## 접근 수준 종류

### open

- 모듈 외부에서 접근, 상속, 오버라이드가 가능하다.
- **단, 클래스와 클래스 멤버에서만 사용**할 수 있다.

### public

- 모듈 외부에서 접근 가능
- 상속 및 오버라이드는 같은 모듈 내에서만 가능

### internal (기본값)

- **기본 접근제어자**로 같은 모듈 안에서만 접근 가능
- 별도로 접근 수준을 지정하지 않으면 기본으로 적용된다.

### fileprivate

- **같은 소스 파일 안에서만** 접근 가능
- 같은 모듈이어도 다른 소스 파일에서는 접근이 불가능하다.

### private

- **선언된 타입 내부와 같은 소스파일 내의 해당 타입에 대한 확장 안에서만** 접근 가능

<aside> 💡

private(Type) > fileprivate(.swift) > internal(모듈 내부) > public(모듈 외부에서 상속 및 오버라이딩 불가능) > open(모듈 외부에서 상속 및 오버라이딩 가능)

</aside>

### 사용 예시

```swift
open class SomeOpenClass {}
public class SomePublicClass {}
internal class SomeInternalClass {}
fileprivate class SomeFilePrivateClass {}
private class SomePrivateClass {}

open var someOpenVariable = 0
public var somePublicVariable = 0
internal let someInternalConstant = 0
fileprivate func someFilePrivateFunction() {}
private func somePrivateFunction() {}

class SomeInternalClass {}              // implicitly internal
let someInternalConstant = 0            // implicitly internal
```

---

## 기본 원칙 (Guiding Principle)

더 높은 접근 수준을 가진 엔티티는 더 낮은(제한적인) 접근 수준을 가진 타입이나 멤버를 사용할 수 없다.

- 여기서 말하는 엔티티는 프로퍼티, 타입, 함수 등에 접근 제어를 적용할 수 있는 코드라고 생각

### 변수 예시

```swift
private struct SecretType {
  var data: String
}

public var item = SecretType(data: "비밀")
// ❌ Variable cannot be declared public
// because its type 'SecretType' uses a private type
```

### 함수 예시

```swift
struct InternalType {} // internal

public func makeSomething() -> InternalType {
  return .init() // ❌ 오류 발생
}
// ❌ Function cannot be declared public
// because its result uses an internal type
```

- 함수는 그 함수의 파라미터 타입이나 반환 값 타입보다 더 높은 접근 수준을 가질 수 없다.

---

## 기본 접근 수준

- 접근 제어자를 명시하지 않으면 기본적으로 `internal` 수준으로 간주된다.

```swift
class InternalClass {}
internal class InternalClass {}
```

---

## 단일 타겟 앱

- 단일 타겟 앱에서는 특별히 접근 수준을 지정할 필요가 없지만, 필요에 따라 `fileprivate` 또는 `private`로 작성할 수도 있다.

## 프레임워크에서의 접근 제어

- 외부에 노출되는 API는 `public` 또는 `open`으로 선언해야 한다
- 내부에서만 사용하는 구현 세부사항은 `internal`, `fileprivate`, `private`로 숨길 수 있다.

```swift
// NetworkModule
public class NetworkManager {
  public init() {}
  
  public func fetchData() {
    print("데이터를 가져옵니다.")
  }
}

class Logger {
  func log(_ message: String) {
    print("LOG:", message)
  }
}

// 다른 모듈에서 import 함
import NetworkModule

let manager = NetworkManager()   // ✅ public이므로 사용 가능
manager.fetchData()

let logger = Logger()            // ❌ internal이라서 외부에서 사용 불가
```

## 유닛 테스트에서의 접근 제어

- `@testable import`를 사용하면 `internal` 멤버에 접근 가능하다.
- 단, `private`과 `fileprivate`은 여전히 접근할 수 없다.

```swift
// UserModule
struct User {
  private var password: String = "1234"
  fileprivate var secretToken: String = "abcd"
  internal var nickname: String = "Tester"
}

// UserModuleTests
@testable import UserModule
import XCTest

final class UserTests: XCTestCase {
  func testNickname() {
    let user = User()
    print(user.nickname) // ✅ internal 접근 가능
  }
  
  func testSecretToken() {
    let user = User()
    // print(user.secretToken) // ❌ fileprivate 접근 불가 (컴파일 에러)
  }
  
  func testPassword() {
    let user = User()
    // print(user.password) // ❌ private 접근 불가 (컴파일 에러)
  }
}
```

## 사용자 정의 타입과 멤버 기본 접근 수준

- 사용자 정의 타입에 접근 수준을 다양하게 지정할 수 있다.
- 타입의 접근 수준은 멤버의 기본 접근 수준에도 영향을 준다.
- 예를 들어 타입의 접근 수준을 `private` 또는 `fileprivate`으로 정의하면 멤버의 기본 접근 수준 또한 `private` 또는 `fileprivate`이 된다.
- 그러나 `internal` 또는 `public`으로 정의하면 멤버의 기본 접근 수준은 `internal`이 된다.
- 이것은 실수로 타입의 내부 작업을 공개 API로 표시되지 않도록 한다.

```swift
public class SomePublicClass {                // explicitly public class
  public var somePublicProperty = 0           // explicitly public class member
  var someInternalProperty = 0              // implicitly internal class member
  fileprivate func someFilePrivateMethod() {}// explicitly file-private class member
  private func somePrivateMethod() {}       // explicitly private class member
}

class SomeInternalClass {                  // implicitly internal class
  var someInternalProperty = 0             // implicitly internal class member
  fileprivate func someFilePrivateMethod() {} // explicitly file-private class member
  private func somePrivateMethod() {}      // explicitly private class member
}

fileprivate class SomeFilePrivateClass {      // explicitly file-private class
  func someFilePrivateMethod() {}       // implicitly file-private class member
  private func somePrivateMethod() {}       // explicitly private class member
}

private class SomePrivateClass {              // explicitly private class
  func somePrivateMethod() {}               // implicitly private class member
}
```

## 튜플 타입

- 튜플의 접근 수준은 구성 타입 중 가장 제한적인 수준을 따른다.

```swift
private struct A {}
internal struct B {}

private let value: (A, B) = (A(), B()) // ✅ 정상
internal let value2: (A, B) = (A(), B()) // ❌ 오류
```

## 함수 타입

- 함수 타입의 접근 수준은 파라미터와 반환 타입 중 가장 제한적인 수준을 따른다.

```swift
class SomeInternalClass {}
private class SomePrivateClass {}

// ❌ 명시적인 접근 레벨을 명시하지 않은 함수는 컴파일 시 에러가 발생
func someFunction() -> (SomeInternalClass, SomePrivateClass) {
  return (SomeInternalClass(), SomePrivateClass())
}

// ✅ private로 접근 레벨을 명시하여 컴파일 에러 제거
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
  return (SomeInternalClass(), SomePrivateClass())
}
```

## 열거형 (enum)

- 열거형의 개별 케이스는 열거형과 같은 접근 수준을 자동으로 받는다.
- 개별 열거형 케이스에 대해 다른 접근 수준을 지정할 수 없다.

```swift
public enum CompassPoint {
  case north
  case south
  case east
  case west
}
```
![[스크린샷 2025-06-24 오후 9.26.38.png]]

## 상수, 변수, 프로퍼티 접근 제어

- 프로퍼티나 변수는 자신이 사용하는 타입보다 더 높은 접근 수준으로 선언할 수 없다.

```swift
private class SomePrivateClass { }

private var privateInstance = SomePrivateClass()

public var publicInstance = SomePrivateClass()
// ❌ Variable cannot be declared public
// because its type 'SomePrivateClass' uses a private type
```

- 필요에 따라 게터보다 더 낮은 접근 수준으로 세터를 정할 수도 있다.
- 이를 위해 `var`또는 `subscript`앞에 `fileprivate(set)`, `private(set)`, 또는 `internal(set)`을 작성하여 더 낮은 접근 수준을 할당할 수 있다.

```swift
struct TrackedString {
  private(set) var edits = 0
  var value: String = "" {
    didSet { edits += 1 }
  }
}
```

## 초기화 구문 (init)

### 사용자 정의 init

- `init`의 접근 수준이 타입보다 더 높은 접근 수준을 가질 수 없다.

```swift
private struct Person {
  public init() {} // ❌ init은 Person보다 더 높은 접근 수준을 가지면 안됌
}

private struct Person {
  private init() {} // ✅ OK
}
```

- `required init`은 클래스와 같은 접근 수준이어야 한다.

```swift
public class Animal {
  required internal init() {} // ❌ 접근 수준 불일치
}

public class Animal {
  public required init() {} // ✅ OK
}
```

- 파라미터 타입은 `init`보다 더 낮을 수 없다.

```swift
private struct Secret {}

public struct User {
  public init(secret: Secret) {} // ❌ 오류
}
```

### 기본 init

- 타입이 `public`이면 기본 `init()`은 `internal` 수준이다.
- 외부에서 사용하려면 `public init()`을 명시해야 한다.

## 프로토콜 (Protocols)

### 프로토콜 정의와 요구사항

- 프로토콜을 정의할 때 접근 수준을 명시할 수 있다.
- 그리고 프로토콜 내부의 모든 요구사항(메서드, 프로퍼티 등)은 **프로토콜 자체의 접근 수준을 따른다**.
- 즉, `public` 프로토콜이라면 내부 메서드도 모두 `public`이 된다.

```swift
public protocol Drawable {
  func draw() // 자동으로 public
}
```

![[스크린샷 2025-06-26 오전 12.10.45.png]]

### 프로토콜 상속

- 더 낮은 접근 수준의 프로토콜을 더 높은 프로토콜이 상속할 수 없다.

```swift
internal protocol InternalProtocol {}

public protocol PublicProtocol: InternalProtocol {}
// ❌ 오류: public 프로토콜은 internal 프로토콜을 상속할 수 없음
```

### 프로토콜 준수

- 타입이 프로토콜을 채택할 때도 접근 제어가 적용된다.
- 프로토콜의 접근 수준과 타입의 접근 수준 중 더 낮은 쪽이 프로토콜 준수 자체의 접근 수준이 된다.

```swift
internal protocol A {
  func doSomething()
}

public struct SomeType: A {
  func doSomething() {} // internal
}
```

## 확장 (Extensions)

### 기본 규칙

- 확장에서 정의한 멤버는 확장 대상 타입의 기본 접근 수준을 따른다.

```swift
public struct SomeStruct {}

extension SomeStruct {
  func hello() {} // internal => 외부 모듈에서 접근 불가
  public func hi() {} // 명시적으로 설정 => 외부 모듈에서 접근 가능
}
```

### 확장에 접근 수준 명시

- 확장에 `private`, `fileprivate` 등 접근 수준을 지정하면,
- 해당 확장 내의 멤버들은 별도 지정이 없을 경우 해당 접근 수준을 기본값으로 갖는다.

```swift
private extension SomeStruct {
  func foo() {}              // 기본 private
  internal func bar() {}     // 명시적으로 internal
}
```

### 프로토콜 준수 확장

- 프로토콜을 채택하는 확장에서는 확장 선언부에 접근 수준을 붙일 수 없다.

```swift
protocol SomeProtocol {
  func doSomething()
}

struct SomeType { }

// ❌ 컴파일 오류 발생
fileprivate extension SomeType: SomeProtocol {
  func doSomething() {}
}
```

### 확장에서 private 접근

```swift
// Person.swift
struct Person {
  private var name = "Luke"
}

extension Person {
  func sayHello() {
      print("Hi, \(name)") // ✅ 접근 가능!
  }
}

// Person+SayHello.swift
extension Person {
  func sayHello() {
      print("Hi, \(name)") // ❌ 오류: 'name'은 private이라 접근 불가
  }
}
```

- `private`은 **선언된 타입의 내부와 같은 소스 파일 내에 있는 해당 타입의 확장에서만** 접근할 수 있다.
- **파일이 다르거나**, **타입이 다르면**, 같은 파일이어도 `private` 속성에는 접근할 수 없다.

## 제네릭 (Generics)

- 제네릭 타입 또는 함수에 대한 접근 수준은 제네릭 타입 또는 함수 자체의 접근 수준과 해당 타입 파라미터에 대한 모든 타입 제약조건의 접근 수준 중 가장 낮은 접근 수준을 따라야 한다.

```swift
private class A {}
public struct MyStruct<T: A> {} // ❌ 오류! public이 private을 참조하지 못함
```

## 타입 별칭 (Type Aliases)

- 타입 별칭은 대상 타입보다 더 높은 접근 수준을 가질 수 없다.

```swift
private struct SomeStruct {}
public typealias PublicAlias = SomeStruct // ❌ 오류
```