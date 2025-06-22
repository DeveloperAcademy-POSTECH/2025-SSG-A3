

> [!question]  
> GQ1. Swift에서 `init` 키워드는 왜 필요한가요?  
> GQ2. Swift의 초기화 과정에서 메모리와 관련된 이슈는 어떤 게 있을까요?

## Description

`init`
- 클래스, 구조체, 열거형 인스턴스를 **초기화**하는 특수 메서드입
- `init`을 통해 모든 저장 프로퍼티가 사용되기 전 반드시 초기화되도록 보장하며, Swift의 **안전성(safety)** 원칙을 지키는 핵심 요소
- `init`이 없으면 저장 프로퍼티가 초기화되지 않은 채로 사용될 수 있으므로 **런타임 크래시 유발 가능
### 종류

- Designated Initializer: 기본 초기화자
- Convenience Initializer: 보조 초기화자
- Failable Initializer (`init?`, `init!`): 실패 가능 초기화자
- Required Initializer: 서브클래스에서 필수 구현해야 하는 초기화자

---

## 주요 기능

- 모든 저장 프로퍼티의 초기화 보장
- 사용자 정의 초기값 설정
- 인스턴스 생성 시점에 실행되는 로직 처리
- 상속받은 클래스에서 초기화 로직 공유
- 초기화 실패 가능성 처리 (`init?`)
- 암묵적 호출 (`super.init()` 등)

---

## 코드 예시
### 1. Designated Initializer (지정 초기화자)

**특징**
- 클래스나 구조체의 **기본 초기화자**
- 모든 저장 프로퍼티를 직접 초기화
- 상속받는 클래스에서는 `super.init()`을 통해 상위 클래스의 designated initializer를 호출해야 함

```swift
class Person {
    let name: String
    let age: Int

    // Designated Initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
}
```

### 2. Convenience Initializer (편의 초기화자)

**특징**
- 반드시 같은 클래스 내의 **다른 initializer를 호출**해야 함 (`self.init(...)`)
- 프로퍼티를 직접 초기화하지 않음
- 주로 **초기화 과정을 단순화**하거나, **디폴트 값 제공** 등의 목적으로 사용

```swift
class Person {
    let name: String
    let age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    // Convenience Initializer
    convenience init(name: String) {
        self.init(name: name, age: 0)
    }
}
```
>  주의: `convenience`는 항상 `self.init`을 사용해야 하고, `super.init`을 호출할 수 없음


### 3. Failable Initializer (`init?`, `init!`)

**특징**
- 초기화 **실패 가능성**을 표현할 수 있음
- 조건이 만족되지 않으면 `nil` 반환
- 주로 **유효성 검사**가 필요한 경우 사용


```swift
struct File {
    let filename: String

    // Failable Initializer
    init?(filename: String) {
        guard filename.hasSuffix(".txt") else {
            return nil // 초기화 실패
        }
        self.filename = filename
    }
}

let file1 = File(filename: "document.txt") // 성공
let file2 = File(filename: "image.png")    // 실패 → nil
```
> `init!`도 가능하지만, 암묵적으로 강제 언래핑되므로 크래시 위험이 있음

### 4.  Required Initializer

**특징**
- **상속받는 모든 서브클래스에서 반드시 구현해야 하는 초기화자**
- 보통 **프로토콜 기반 초기화 강제** 등에 사용
- `class`에서만 사용 가능

```swift
class Animal {
    let type: String

    // Required Initializer
    required init(type: String) {
        self.type = type
    }
}

class Dog: Animal {
    let breed: String

    // 반드시 구현해야 함
    required init(type: String) {
        self.breed = "Maltese"
        super.init(type: type)
    }
}
```
> `required`와 `override`는 **동시에** 붙일 수도 있음

### 정리

|구분|목적|필수 호출 대상|실패 가능성|주로 쓰이는 상황|
|---|---|---|---|---|
|Designated|주 초기화자|`super.init()`|❌|저장 프로퍼티 초기화 전반|
|Convenience|보조 초기화자|`self.init()`|❌|기본값 설정, 초기화 간편화|
|Failable (`init?`)|실패 가능한 초기화자|없음|✅|유효성 검사, nil 반환 가능 상황|
|Required|상속 시 필수 초기화자 구현 강제|`super.init()` or `self.init()`|❌ / ✅|프로토콜 또는 상속 구조 강제 초기화 필요|

## 작동 원리 & 메모리 측면

### 작동 순서
1. `init`가 호출되면 인스턴스의 **메모리 공간이 먼저 할당**됨
2. 모든 저장 프로퍼티가 **초기값을 가질 때까지** 초기화 과정이 진행됨
3. `self`는 모든 저장 프로퍼티가 초기화되기 전까지는 접근 불가 (`self` 접근 제한)
4. 상속 구조라면 `super.init()`을 통해 상위 클래스의 `init` 호출

### 메모리 장점

- 프로퍼티가 **초기화 전까지는 메모리에 완전하게 진입하지 않음**
- Swift는 `ARC` 기반이므로, 초기화 도중 strong reference가 발생하지 않으면 **retain cycle 위험도 없음**
- Failable initializer는 실패 시 메모리 **즉시 해제**

### 단점 또는 주의사항

- 초기화 순서가 복잡하면 버그 발생 여지 있음
- 클래스에서 convenience init의 남용은 **가독성 저하** 유발 가능
- 초기화 중 클로저 캡처 시 **self 캡처 주의** 필요 (retain cycle 가능)

---

## Keywords

- Designated Initializer
- Convenience Initializer
- Failable Initializer
- Required Initializer
- 초기화 순서
- ARC & Memory Initialization
- Self 접근 시점
- Initializer Delegation

---

## References
- [Apple 공식 문서 - Initialization](https://developer.apple.com/documentation/swift/initialization)