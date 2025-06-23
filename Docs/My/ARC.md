>[!question]
>GQ1. ARC란 무엇일까
>GQ2. strong / weak / unowned 차이는 무엇일까

## Description
## 1. 기본 개념
- Swift는 ARC로 클래스 인스턴스의 메모리를 자동 관리함
- ARC는 인스턴스가 더 이상 필요하지 않으면 해당 메모리 해제함
- 대부분 상황에서는 개발자가 신경 쓰지 않아도 ARC가 알아서 처리함
- 구조체(struct), 열거형(enum)은 ARC 적용 안 됨 → 값 타입이기 때문임
- 클래스 인스턴스만 참조 카운트 관리 대상임
- ARC는 Objective-C에서 쓰이던 방식과 유사함

---

## 2. ARC 동작 방식

- 클래스 인스턴스를 만들면 ARC가 메모리를 할당함
- 메모리에는 타입 정보 + 저장 프로퍼티 값이 들어감
- 인스턴스가 필요 없어지면 ARC가 해당 메모리 해제함
- 아직 참조 중인 인스턴스를 ARC가 해제하면 앱이 크래시 날 수 있음
- ARC는 몇 개의 변수/상수/프로퍼티가 해당 인스턴스를 참조하는지 추적함
- 하나라도 참조가 남아 있으면 ARC는 메모리를 해제하지 않음
- 참조가 전부 없어지면 → deinit 호출 → 메모리 해제

---

## 3. 기본 strong 참조 예제

- 인스턴스를 변수에 할당하면 기본은 strong(강력한) 참조임
- strong은 참조 카운트를 증가시킴
- 인스턴스를 여러 변수에 할당하면 참조 카운트 누적됨

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?

reference1 = Person(name: "John Appleseed")
reference2 = reference1
reference3 = reference1

```

- 각 참조를 nil로 바꾸면 참조 수가 줄어듦
- 마지막 참조까지 해제되면 deinit 호출됨

```swift
reference1 = nil
reference2 = nil
reference3 = nil

```

---

## 4. 강한 참조 순환 (Strong Reference Cycle)

- 두 클래스 인스턴스가 서로를 strong으로 참조하면 참조 카운트가 0이 되지 않음
- 메모리에서 해제되지 않고 누수 발생함

```swift
class Person {
    var apartment: Apartment?
}

class Apartment {
    var tenant: Person?
}

```

- 다음 코드처럼 연결하면 순환 구조 생김

```swift
john!.apartment = unit4A
unit4A!.tenant = john

```

- 외부에서 john = nil, unit4A = nil 해도 인스턴스 둘 다 메모리에서 살아 있음
- deinit 호출되지 않음 → 순환 참조 발생함

---

## 5. weak 참조로 순환 참조 해결

- weak는 참조 카운트를 증가시키지 않음
- 참조 대상이 해제되면 자동으로 nil로 설정됨
- 항상 옵셔널이어야 함 (nil 가능해야 해서)

```swift
class Apartment {
    weak var tenant: Person?
}

```

- 한쪽이 weak가 되면 참조 카운트는 순환 구조가 되지 않음
- john = nil → Person 해제됨 → tenant 자동 nil → Apartment 해제 가능

---

## 6. unowned 참조

- weak와 유사하지만 **nil이 될 수 없음**
- 대상 인스턴스가 먼저 해제되면 런타임 에러 발생함
- 참조 대상이 더 오래 살아 있는 것이 확실할 때만 사용해야 함
- non-optional로 선언함

```swift
class CreditCard {
    unowned let customer: Customer
}

```

- customer는 해제될 수 없다고 전제함
- john = nil 하면 → Customer와 CreditCard 모두 해제됨

---

## 7. unowned optional

- `unowned var nextCourse: Course?` 같이 옵셔널 타입도 가능
- 여전히 참조 카운트 증가시키지 않음
    - 인스턴스 소유 x
- ARC에서는 weak처럼 취급되지만, 자동으로 nil로 되지는 않음
- 다만 값이 해제된 뒤 접근하면 undefined behavior 가능성 있음 → 직접 관리해야 함

```swift
unowned var nextCourse: Course?
```

---

## 8. 암묵적 언래핑 옵셔널과 unowned 같이 쓰는 패턴

- 두 객체가 서로를 참조하고 둘 다 항상 존재해야 하는 경우 사용함
- 한쪽은 `unowned`
- 다른 쪽은 `var x: Type!` (IUO: Implicitly Unwrapped Optional)

```swift
class Country {
    var capitalCity: City!
}

class City {
    unowned let country: Country
}

```

- 초기화 순서 제어 가능함 → 순환 참조도 없음
- 위 구조는 `Country → City`는 strong, `City.country → Country`는 unowned
- 따라서 **참조는 양방향이지만 strong은 단방향** → 순환 참조가 아님

---

## 9. 클로저와 strong reference cycle

- HTMLElement 인스턴스가 asHTML이라는 클로저를 프로퍼티로 소유함
- 클로저 내부에서 self를 캡처하면 → self → 클로저 → self 순환 참조 발생함
    - asHTML 클로저는 self를 캡처함
        - [self.name](http://self.name), self.text에 접근 → self가 클로저 내부로 강한 참조됨

```swift
class HTMLElement {
    let name: String
    let text: String?

    lazy var asHTML: () -> String = {
        return "<\\(self.name)>\\(self.text ?? "")</\\(self.name)>"
    }

    deinit {
        print("\\(name) is being deinitialized")
    }
}
```

- paragraph = nil 해도 HTMLElement 인스턴스 해제 안 됨

---

## 10. 클로저 캡처 리스트로 해결

- `[unowned self]`, `[weak self]`를 캡처 리스트에 넣으면 self를 약한 참조로 캡처함
- 클로저가 self를 strong으로 참조하지 않기 때문에 순환 참조 끊김

```swift
class MyClass {
	lazy var asHTML: () -> String = {
	    [unowned self] in
	    return "<\\(self.name)>\\(self.text ?? "")</\\(self.name)>"
	}
}

```

- paragraph = nil 하면 → HTMLElement deinit 호출됨
- self가 항상 존재하는 경우는 `unowned`, nil 될 수 있는 경우는 `weak` 사용

---

# 영상 내용

```swift
class Traveler {
	var name: String
	var destination: String?
}

func test() {
		let traveler1 = Traveler(name: "Lily")
		let traveler2 = traveler1
		traveler2.destination = "Big Sur"
		print("Done Traveling")
}
```

객체의 수명은 runtime에서 swift 컴파일러가 삽입한 ratain과 release에서 결정됨

↔ C++는 객체의 수명이 닫는 중괄호에서 끝나도록 보장됨

그리고 적용되는 ARC 최적화에 따라 관찰된 객체의 수명은

보장된 최소 수명과 다를 수 있으며, 객체를 마지막으로 사용한 시점을 지나서 끝날 수도 있다.

- 관찰된 객체 수명보다는 보장된 최소 수명에 의존을 해야한다.
- 관찰된 객체 수명의 경우 구현 세부 사항이 변경됨에 따라 변경될 가능성이 큼

→ ARC 최적화는 무엇일까

- ARC 최적화에 따라 보통 괄호가 닫힐때까지 유지되는 경우가 있긴함

Observable object lifetimes

- laguage feature
    
    = 객체 수명을 관찰 가능하게 만드는 언어기능
    
    - Weak and unowned references
    - Deinitialiszer side-effects
- Consequences and safe techniques
    

객체의 수명 관찰하는 방법

- Weak and unowned references
    - 참조 계산에 참여하지 않으므로 참조 순환을 끊는데 일반적으로 사용
    - 약한 참조나 소유되지 않은 참조가 사용중인 동안 참조된 객체의 할당이 해제됨
    - Swift 컴파일러에서 Weak 참조에 대한 액세스를 nil로 변경하고, unowned 참조에 대한 액세스를 트랩으로 변경
- Deinitilaizer side - effect

참조 순환이란

```swift
class Traveler {
	var name: String
	var account: Account?
	func printSummary() {
		if let account = account {
				print("\\(name) has \\(account.points) points")
		}
	}
}

class Account {
	var traveler: Traveler?
	var points: Int
}

func test() {
		let traveler = Traveler(name: "Lily")
		let account = Account(traveler: traveler, points: 1000)
		traveler.account = account
		traveler.printSummary()
}
```

→ 모든 참조가 사라진 후에도 참조 카운트가 둘 다 1로 존재하게 됨

- 객체가 절대 해제되지 않아, 메모리 누수 발생

해결 코드

```swift
class Traveler {
	var name: String
	var account: Account?
	func printSummary() {
		if let account = account {
				print("\\(name) has \\(account.points) points")
		}
	}
}

class Account {
	weak var traveler: Traveler?
	var points: Int
}

func test() {
		let traveler = Traveler(name: "Lily")
		let account = Account(traveler: traveler, points: 1000)
		traveler.account = account
		traveler.printSummary()
}
```

관찰된 객체 수명에 의존해서 문제가 생기는 경우

- traveler.printSummary() 호출시 정상적으로 출력이 이루어 질 수 도 있지만, 그것은 단순히 우연
- 가장 마지막으로 Traveler객체를 사용한 곳이 traveler.printSummary()호출전인 traveler.account = account이기때문에 컴파일러가 마지막 사용 직후 릴리스를 삽입한 경우 Traveler객체의 참조 횟수가 0이 될 수 있음
- 참조 카운트가 0으로 떨어지면, 약한 참조를 통해 Traveler객체에 액세스 할 수 있는 권한이 nil되고, Traveler 객체의 할당이 해제될 수 있음
- 할당 해제후 printSummary() 호출시 Crash 발생
- 충돌이 발생한 이유
    - 강제 풀림 때문
        
    - 선택적 바인딩이 충돌을 방지했을지 궁금
        
        → 실제로 문제를 더 악화시킴
        
        명확한 충돌이 발생하지 않으면 관련 없는 이유로 관찰된 객체의 수명이 변경될 때 눈에 띄지 않는 조용한 버그가 생성됨(Silent Bug)
        

```swift
class Traveler {
	var name: String
	var account: Account?
}

class Account {
	weak var traveler: Traveler?
	var points: Int
	func printSummary() {
			print("\\(traveler!.name) has \\(points) points")
	}
}

func test() {
		let traveler = Traveler(name: "Lily")
		let account = Account(traveler: traveler, points: 1000)
		traveler.account = account
		traveler.printSummary()
}
```

Weak 참조와 unowned 참조를 안전하게 처리하는 여러 기술

- withExtendedLifetime()
- Redesign to access via strong reference
- Redesign to avoid weak/unowned reference

```swift

func test() {
		let traveler = Traveler(name: "Lily")
		let account = Account(traveler: traveler, points: 1000)
		traveler.account = account
		withExtendedLifetime(traveler) {
			traveler.printSummary()
		}
}
```

Sift는 객체 수명을 명시적으로 연장할 수 있는 withExtendedLifetime() 유틸리티 제공

- printSummary() 함수가 호출되는 동안 Traveler 객체의 수명을 안전하게 연장하여 잠재적 버그 방지 가능
    
- 끝에 withExtendedLifetime()에 대한 빈 호출을 배출해도 동일한 효과 가능
    
    ```swift
    
    func test() {
    		let traveler = Traveler(name: "Lily")
    		let account = Account(traveler: traveler, points: 1000)
    		traveler.account = account
    		traveler.printSummary()
    		withExtendedLifetime(traveler) {}
    }
    ```
    
- 더 복잡한 경우 defer를 사용하여 컴파일러에게 객체의 수명을 현재 범위의 끝까지 연장하도록 요청 가능
    
    ```swift
    
    func test() {
    		let traveler = Traveler(name: "Lily")
    		let account = Account(traveler: traveler, points: 1000)
    		defer {withExtendedLifetime(traveler) {}}
    		traveler.account = account
    		traveler.printSummary()
    }
    ```
    
- 단점
    
    - 쉬운 방식처럼 보일 수 있으나, 취약한 기술, 그리고 정확성에 대한 책임이 사용자에게 전가됨

**Redesign to access via strong reference**

- 객체에 대한 접근을 강력한 참조로만 제한하면 객체 수명에 따른 예상치 못한 상황 방지 가능

```swift
class Traveler {
	var name: String
	var account: Account?
	func printSummary() {
		if let account = account {
				print("\\(name) has \\(account.points) points")
		}
	}
}

class Account {
	private weak var traveler: Traveler?
	var points: Int
}

func test() {
		let traveler = Traveler(name: "Lily")
		let account = Account(traveler: traveler, points: 1000)
		traveler.account = account
		traveler.printSummary()
}
```

- Account 클래스의 약한 참조가 숨겨짐
- 테스트에서는 강력한 참조를 통해 printSummary()를 호출해야하므로 잠재적인 버그가 제거됨

Redesign to avoid weak/unowned reference

- 약한 참조와 소유되지 않은 참조는 성능 저하를 초래할 뿐만 아니라 클래스 설계에 주의하지 않으면 버그를 노출시킬 수도 있음
    
    → 처음 부터 참조 순환이 일어나지 않도록 알고리즘 설계
    

```swift
class PersonalInto {
	var name: String
}

class Traveler {
	var info: PersonalInfo
	var account: Account?
}

class Account {
	var info: PersonalInfo
	var points: Int
}
```

- 순환 클래스 관계를 트리 구조로 변경하면 종종 순환 참조를 피할 수 있음
- 구현 비용이 더 들수도 있지만
- 모든 잠재적인 객체 수명 버그를 제거하는 확실한 방법

**Deinitialiszer side-effects(초기화 해제의 부작용)**

- 해제 프로그램은 할당 해제 전에 실행되며, 그 부작용은 외부 프로그램 효과에 의해 관찰될 수 있음
    
- 외부 프로그램 효과와 함께 초기화 해제 부작용을 시퀀싱하는 코드를 작성하면, 숨겨진 버그가 발생할 수 있으며, 이러한 버그는 관련 없는 이유로 관찰된 객체 수명이 변경될때만 발견됨
    
    ```swift
    class Traveler {
    	var name: String
    	var destination: String?
    	deinit {
    		print("\\(name) is deinitializing")
    	}
    }
    
    func test() {
    		let traveler1 = Traveler(name: "Lily")
    		let traveler2 = traveler1
    		traveler.destination = "Big Sur"
    		print("Done traveling")
    }
    ```
    
- 해제: 콘솔에 메시지를 출력하는 전역적인 부작용 존재
    
- “여행 완료”가 출력된 후에 초기화 해제 실행될 수 있음
    
    - but, Traveler 객체의 마지막 용도가 목적지 업데이트이므로, ARC 최적화가 적용되면 “Done traveling”이 출력되기 전에 초기화 해제가 실행될 수 있음
- 초기화 해제의 부작용은 관찰가능했지만 의존하지는 않음
    

외부 프로그램 효과에 의해 d**einitialiszer 부작용이 발생하는 좀 더 복잡한 예**

```swift
class Traveler {
	var name: String
	var id: UInt
	var destination: String?
	var travelMetrics: TravelMetrics
	func updateDestination(_ destination: String) {
		self.destination = destination
		travelMetrics.destination.append(self.destination)
	}
	deinit {
		travelMetrics.pulish()
	}
}

class TravelMetrics {
	var id: UInt
	var destination = [String]()
	var category: String?
	func computeTravelInterest()
	func publish()
}

func test() {
		let traveler = Traveler(name: "Lily", id: 1)
		let metrics = traveler.travelMetrics
		...
		traveler.updateDestination("Big Sur")
		...
		traveler.updateDestination("Catalina")
		metrics.computeTravelInterest()
}
verifyGlobalTravelMetrics()
```

- 작동
    - 목적지가 업데이트 될때마다 TravelMetrics클래스에 기록됨
    - Traveler 객체의 초기화를 해제하면, 메드릭이 글로벌 레코드에 게시됨
    - 공개되는 지표에는 여행자의 익명 ID, 검색된 목적지 수, 계산된 여행 관심 범주가 포함됨
    - Test 함수
        - 먼저 traveler 객체를 생성한 다음, Traveler 객체에서 travelMetrics에 대한 참조를 복사
        - 여행자의 목적지가 Big Sur으로 업데이트되고, TravelMetrics에 Big Sur가 기록됨
        - 여행자의 목적지가 Catalina로 업데이트되고, TravelMetrics에 Catalina가 기록됨
        - 기록된 목적지를 살펴보아 여행 관심 번주를 계산
- 오늘날에는 여행 관심을 계산한 후 초기화 해제를 실행하여 관심있는 카테고리를 Nature로 게시할 수 있음
- but, Traveler객체의 마지막 용도는 Catalina에 대한 대상 업데이트이며, 그 직후에 초기화 해제 프로그램이 실행될 수 있음
- 해제자는 여행 관심을 계산하기 전에 실행되므로 nil이 게시되어 버그가 발생

초기화 해제 부작용을 안전하게 처리하는 다양한 기술

- withextendedLifetime()
- Redesign to limit visibility of internal class details
- Redesign to avoid deinitializer side-effects

→ 각자의 비용과 지속적인 유지 관리 비용의 정도 다름

**withextendedLifetime()**

```swift
func test() {
		let traveler = Traveler(name: "Lily", id: 1)
		let metrics = traveler.travelMetrics
		...
		traveler.updateDestination("Big Sur")
		...
		traveler.updateDestination("Catalina")
		withextendedLifetime(traveler) {
				metrics.computeTravelInterest()
		}
}
```

- withextendedLifetime()을 사용하면 여행 관심 범주가 계산될때까지 Traveler객체의 수명을 명시적으로 연장하여 잠재적인 버그 방지 가능
- 단점: 정확성에 대한 책임을 사용자에게 전가하는 것
- 초기화 해제 부작용과 외부 프로그램 효과 사이에 잘못된 상호 작용이 발생할 가능성이 있을때마다 withextendedLifetime()을 사용해야 하며, 이는 유지 관리 비용을 증가시킴,

**Redesign to limit visibility of internal class details**

```swift
class Traveler {
	...
	private var travelMetrics: TravelMetrics
	deinit {
		metrics.computeTravelInterest()
		travelMetrics.pulish()
	}
}

func test() {
		let traveler = Traveler(name: "Lily", id: 1)
		...
		traveler.updateDestination("Big Sur")
		...
		traveler.updateDestination("Catalina")
}
```

- 효과가 모두 국소적이라면 초기화 해제의 부작용은 관찰 될 수 없음
- 내부 클래스 세부정보의 가시성을 제한하여 클래스 API를 재설계하면, 객체 수명 버그 방지 가능
- TravelMetrics는 비공개로 표시되어 외부 접근이 불가능
- 초기화 해제는 가장 관심 있는 여행 카테고리를 계산하고 측정 항목을 게시함
- 효과적이지만, 아래 Redesign to avoid deinitializer side-effects가 더 원칙적인 접근 방식

**Redesign to avoid deinitializer side-effects**

```swift
class Traveler {
	...
	private var travelMetrics: TravelMetrics
	
	func publishAllMetrics() {
		travelMetrics.computeTravelInterest()
		travelMetrics.publish()
	}
	deinit {
		assert(travelMetrics.published)
	}
}

class TravelMetrics {
	...
	var published = Bool
	...
}

func test() {
		let traveler = Traveler(name: "Lily", id: 1)
		defer { traveler.publishAllMetrics() }
		...
		traveler.updateDestination("Big Sur")
		...
		traveler.updateDestination("Catalina")
}
```

- deinitializer 대신 defer를 사용하여 메트릭을 게시하고, deinitializer는 검증만 수행
- 초기화 해제의 부작용 제거시, 모든 잠재적인 객체 수명 버그를 없앨 수 있음


## References
- [WWDC21: ARC in Swift: Basics and beyond | Apple](https://www.youtube.com/watch?v=GFq6sV2jD_c)
- [Documentation](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/automaticreferencecounting/?utm_source=chatgpt.com)