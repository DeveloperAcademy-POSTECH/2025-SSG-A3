> [!question]  
> **GQ1.** `mutating` 이란 무엇이고, 왜 필요한가?
> **GQ2.** `mutating`의 컴파일러 처리 방식은 어떤가?
## Description
### 1. `mutating`이란 무엇인가?
> [Swift Language Guide – Methods](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods/#Modifying-Value-Types) 
> "You can modify properties of a value type from within its instance methods by marking the method as mutating."

즉, 구조체(`struct`)나 열거형(`enum`) 같은 **값 타입**에서,
**인스턴스 내 속성을 변경하거나 self 자체를 대체하려면 반드시 `mutating` 키워드를 붙여야 한다**는 의미

---
### 2. 왜 필요한가? (값 타입과 참조 타입의 차이)

| 타입                      | 저장 위치            | 기본 의미 | 속성 수정 시                     |
| ----------------------- | ---------------- | ----- | --------------------------- |
| `class` (참조 타입)         | 힙(Heap)          | 참조 복사 | 참조한 객체 내부 변경 가능             |
| `struct`, `enum` (값 타입) | 스택(Stack) 또는 인라인 | 값 복사  | 인스턴스를 통째로 복사하므로 기본적으로 수정 불가 |
Swift는 값 타입의 **불변성(safety)**을 보장하기 위해
기본적으로 인스턴스 내 속성 수정은 허용 x
→ 따라서 명시적으로 "`mutating`"을 붙여야만 내부를 변경 가능

---

## 코드 예시

### 3.  사용예시
```swift
struct Counter {
    var count = 0

    mutating func increment() {
        count += 1
    }
}
```
위 코드는 `count`라는 속성을 변경하므로 `mutating` 키워드를 붙여야 함
만약 생략하면 컴파일 에러
> Cannot assign to property: 'self' is immutable → “속성에 값을 할당할 수 없습니다. `self`가 불변(immutable)이기 때문입니다.”
---

## 주요 기능
### 4. `self`를 통째로 바꿀 수도 있음

`mutating` 메서드는 속성뿐 아니라 `self` 전체를 새로운 값으로 바꾸는 것도 가능
```swift
struct Point {
    var x: Int, y: Int

    mutating func moveToOrigin() {
        self = Point(x: 0, y: 0)
    }
}

```

`mutating`을 붙인 메서드는 **self의 모든 메모리를 새 값으로 교체**할 수 있음
- 이는 참조 타입에서는 불가능한 패턴

---

### 5. 내부 구현과 컴파일러 처리 방식

### 컴파일러 시점에서 일어나는 일:
> mutating 메서드는 Swift 컴파일러가 self: inout 파라미터처럼 처리한  것
> ⇒ 즉, 이 메서드는 **self를 복사하지 않고, 직접 수정할 수 있는 권한**을 가짐

```swift
struct Counter {
    var count = 0

    mutating func increment() {
        count += 1
    }
}
```

이 메서드는 컴파일러가 내부적으로 다음과 유사하게 처리
```swift
func increment(self: inout Counter) {
    self.count += 1
}
```

즉, `mutating`이 붙었기 때문에 **`self`는 복사본이 아니라 원본 주소**를 참조
→ 그 결과 `count += 1`은 호출한 쪽의 변수(원본)에 직접 적용

**inout과 비교
```swift
func update(_ x: inout Int) {
    x += 1
}

var num = 3
update(&num)
```
- `num`의 메모리 주소가 함수에 전달되고
- 함수 내부에서 그 메모리의 값을 직접 수정
- → 함수 종료 후 `num` 값이 바뀜
`mutating func`는 이 `inout` 패턴을 **`self`에 적용**하는 방식

---

### 6. 메모리 관점: 스택 / 힙과 복사

**구조체는 보통 스택에 저장됨
- `struct` 타입 인스턴스는 기본적으로 스택에 저장되며, 함수에 전달되면 **값 복사**
- `mutating` 메서드는 이 값을 **직접 수정**할 수 있게 해줌

**Copy-on-Write (CoW)와의 관계
- `Array`, `String`, `Dictionary` 등은 `struct`이지만 내부 데이터는 힙에 존재
    - 내부 데이터를 모두 복사해버리면 큰 비용
    - 진짜 복사를 미루고, 효율적으로 처리
- `mutating` 메서드가 실행되면 Swift는 참조 카운트를 확인하고,
    - **1개뿐이면 그대로 수정**
    - **2개 이상이면 새 복사본을 생성한 뒤 수정**
-> 이를 Copy-on-Write (CoW)

> "수정이 일어나기 전까지는 진짜 복사하지 않고, **복사된 것처럼 보이게만 한다**"

```swift
var a = [1, 2, 3]
var b = a  // 실제로는 '공유된 힙 메모리'를 참조

b.append(4) // b를 수정하려 하자 → 복사 발생 (CoW)

print(a) // [1, 2, 3]
print(b) // [1, 2, 3, 4]
```

- **내부 처리**
    1. b를 수정해야 하는 시점에, Swift는 힙 메모리의 참조 카운트를 확인
    2. a와 b가 같은 힙 메모리를 공유 중이라면
        - 참조 카운트가 2 이상
    3. 이때 `b`는 새로운 복사본을 생성하고 수정
    4. 그래서 `a`는 영향을 받지 않음

이런 구조는 `mutating` 키워드와 함께 작동하여 **안정성과 성능**을 동시 보장
Swift에서 `b.append(4)` 같은 명령은 `mutating` 메서드를 통해 이뤄짐

```swift
extension Array {
    mutating func append(_ newElement: Element) {
        // 내부적으로 CoW 확인 → 필요 시 복사 → append 수행
    }
}
```

즉, `mutating` 메서드는 다음 두 가지 역할
1. **CoW 여부 판단 시점**이 됨 (정말 바꿔도 되는지 체크)
2. **필요할 때만 진짜 복사**해서 성능 최적화

---

### 7. `mutating` in Protocols

```swift
protocol Toggleable {
    mutating func toggle()
}

struct LightSwitch: Toggleable {
    var isOn = false
    mutating func toggle() {
        isOn.toggle()
    }
}

```

- **프로토콜에 정의된 `mutating` 메서드는 구현체도 반드시 `mutating`이어야 함**
- 클래스에서 구현할 경우 `mutating` 키워드는 필요 없음 (컴파일러가 자동 적용)
    - class는 왜 필요 없나?
        - 클래스는 참조 타입이라 메서드 내부에서도 self의 속성을 언제든 수정 가능
        - ARC로 관리되기 때문에 복사/반영이 필요 없음
        - `mutating`도 허용되지 않음 (문법적으로)

---

### 8. `enum`에서 사용

```swift
enum State {
    case on, off

    mutating func toggle() {
        self = (self == .on) ? .off : .on
    }
}
```

- `enum`에서도 `self`를 재할당하기 위해 `mutating` 필요
- `enum`은 값 타입이므로 내부 상태 전환을 하려면 명시적으로 허용해야 함

---

### 9. 메모리 접근 안전성 & 충돌 방지

Swift는 `mutating` 메서드 사용 시, **중첩된 메모리 접근(Exclusive Access)을 금지

```swift
func bad(_ array: inout [Int]) {
    array = array.sorted()  // ❌ 중첩된 접근: array 읽으면서 동시에 씀
}
```

- Swift 런타임은 이런 충돌을 **런타임 에러 또는 컴파일 에러**로 감지
    → 이를 통해 `mutating` 메서드로 인한 **데이터 경쟁(race condition)을 방지

---

### 10. `mutating` 메서드의 SIL 표현

> **SIL(Swift Intermediate Language)** _: Swift 컴파일러가 Swift 코드를 **최적화하고 분석하기 위해 사용하는 중간 단계 언어(IR)**_

Ex)
```swift
struct Counter {
    var value: Int

    mutating func increment() {
        value += 1
    }
}
```

- 코드 핵심 요점
    - `Counter`는 값 타입(struct)이기 때문에
    - `increment()`는 내부 속성 `value`를 변경하기 위해 `mutating`으로 선언됨
    - 컴파일러는 `increment()`를 사실상 `inout` 방식으로 처리함
- SIL로 변환시 (요약본)
```
// increment() method
sil hidden [ossa] @$s7CounterV9incrementyyF : $@convention(method) (@inout Counter) -> () {
entry(%0 : $*Counter):
  // %0은 self의 inout 주소

  %1 = struct_element_addr %0 : $*Counter, #Counter.value
  %2 = load %1 : $*Int
  %3 = integer_literal $Builtin.Int64, 1
  %4 = builtin "add_Int64"(%2 : $Builtin.Int64, %3 : $Builtin.Int64)
  %5 = struct $Int (%4 : $Builtin.Int64)
  store %5 to %1 : $*Int
  return
}
```

|코드|의미|
|---|---|
|`@inout Counter`|이 메서드는 `self`를 참조가 아닌 **주소 기반으로 수정**|
|`%0 : $*Counter`|`self`의 메모리 주소 (포인터처럼 보이지만 안전한 inout)|
|`struct_element_addr %0, #Counter.value`|구조체에서 `value` 프로퍼티의 주소 추출|
|`load %1` → `+1` → `store`|값을 읽고, 1 더하고, 다시 저장|

- `self.value += 1`은 SIL에서 **메모리 주소 기반 연산(load → add → store)으로 처리됨
- `self`는 **inout**으로 취급되어, **복사본이 아니라 원본 메모리에서 직접 수정
- `swiftc` 명령어로 직접 SIL을 추출해서 확인 가능
    ```bash
    swiftc -emit-sil -O main.swift
    ```

## Keywords
- `mutating`
- `inout`
- 값 타입(value type)
- 참조 타입(reference type)
- `self` 재할당
- Copy-on-Write(CoW)
- SIL (Swift Intermediate Language)
- Exclusive Memory Access (중첩 접근 방지)

## References
- [Swift Language Guide – Methods (Apple 공식 문서)](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods/#Modifying-Value-Types)
- [Swift Evolution: Proposal SE-0003 – Removing `var` Parameters and Renaming `inout`](https://github.com/apple/swift-evolution/blob/main/proposals/0003-remove-var-parameters.md)
- [WWDC20 - Understand Swift Performance](https://developer.apple.com/videos/play/wwdc2020/10164/)
- [Swift.org - SIL 문서](https://github.com/apple/swift/blob/main/docs/SIL.rst)