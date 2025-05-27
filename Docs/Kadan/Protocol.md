
### Protocol 정의

- 프로토콜이란, 어떤 기능에 적합한 특정 메서드, 프로퍼티 및 기타 요구 사항의 청사진을 의미.
- 클래스, 구조체, 열거형에 의해 채택되며, 정의된 요구사항의 실제 구현을 제공한다.
- 요구사항을 모두 충족하는 모든 유형은 해당 프로토콜에 부합한다고 말한다.

💡 쉽게 말해서 “약속” 하는 것으로 이해하려 한다. 근데 어떤 약속?

→ 해당 기능에 꼭 필요한 요구사항을 선언해두는 약속.

ex) 밴드가 있다.

- 기타, 드럽, 피아노, 보컬 필요 → `프로퍼티`
- 모여서 연주를 함 → `메서드`

→ 실제 기타는 누가치고 드럼은 누가치고, 연주는 어떤 걸 하는지 실제로 지정 해 주는 것이 아니라, 이런 프로퍼티는 꼭 필요해요! (이게 없으면 밴드는 안돌아가요) , 이런 메서드는 꼭 필요해요! (밴드는 모여서 연주가 제일 중요함) 라고 꼭 필요한 요구사항을 선언 해두는 것이 `Protocol` 이다.

---


### Protocol 선언

```swift
protocol Band {
	var drum: String   { get set }
	var vocal: String  { get set }
	var piano: String  { get set }
	var guitar: String { get set }
	
	func play()
}

// 밴드라는 프로토콜을 따르기 위해선 드럼 보컬 피아노 기타 프로퍼티가 반드시 정의 되어야 함.
// play 란 메서드 구현도 꼭 필요함.
```

- 프로퍼티를 선언하여 값을 직접 정의하고 메서드를 직접 구현하는게 아님.!
- 이 프로퍼티를 따르려면 이러 이러한 것들이 꼭 필요하다! 라는 약속임.

---

### Protocol 채택

```swift
class AcademyBand: Band {
	var drum:   String = "A"
	var vocal:  String = "B"
	var piano:  String = "C"
	var guitar: String = "D"
	
	func play() {
		print("day6 한 페이지가 될 수 있게")
	}
}
```

- 이런 식으로 프로토콜에서는 필수적인 요구사항의 껍데기만 제공하고, 실제 구현은 채택한 곳에서!!

---

### 필수 외에 있을수도 없을수도 있는?

- 만약 베이스 처럼 구하기 힘든 포지션이 있을 경우에?

```swift
@objc protocol Band { //@objc 선언
	var drum: String   { get set }
	var vocal: String  { get set }
	var piano: String  { get set }
	var guitar: String { get set }
	@objc optional var bass: String { get set } // @objc optional 선언
	
	func play()
}

```

- 이렇게 선언 해주면 채택해주는 곳에서 꼭 선언해 주지 않아도 에러가 나지 않는다.
```swift
struct AcademyBand: Band {   // error!
	var drum: String = "A"
	var vocal: String = "B"
	var piano: String = "C"
	var guitar: String = "D"
}
```

- @objc 를 붙이고 struct 에 채택하면 오류가 난다. → @objc 란 Objectiv-C에서도 사용될 수 있단 건데, Objective-C에서 프로토콜은 오직 “클래스” 에서만 채택가능하고

```swift
@objc protocol Band: AnyObject {
	var drum: String   { get set }
	var vocal: String  { get set }
	var piano: String  { get set }
	var guitar: String { get set }
	@objc optional var bass: String { get set }
}

```


→ 클래스 전용일 때 사용하는 AnyObject가 자동으로 채택되기 때문에 클래스만 가능하다.

---

### 프로토콜 안에 선언된 프로퍼티를 구현할 때, 저장 연산 둘다 된다.

```swift
protocol Band {
	var drum: String   { get set }
}

class RunnerBand: Band {
	var drum: String = "Kadan" // 저장 프로퍼티 구현
}

class MentorBand: Band {
	var drum: String = "Sup" // 연산 프로퍼티 구현
}
```

- 그리고 프로토콜에 선언되는 프로퍼티는 항상 `var` 로 선언 되어야 한다. (let 은 에러) → 저장/연산 상관없이 구현 가능한데, 연산 프로퍼티는 반드시 `var` 로 선언해야 하기 때문 → 대신 채택해서 구현하는 곳에서는 상황에 따라 다르다.

---

### { get }

```swift
protocol Band {
	var drum: String { get }
}

class RunnerBand: Band {
	let drum: String = "Kadan" 
}

class MentorBand: Band {
	var drum: String = "Sup" 
}
```

- 저장 프로퍼티로 구현할 경우 let, var 둘다 상관 없음.

```swift
class RunnerBand: Band {
	var drum: String {
		get {
			return "Kadan"
		}
	}
}

class MentorBand: Band {
	var random: String: ""
	var drum: String {
		get {
			return "Sup"
		}
		set {
			self.random = newValue
		}
	}
}
```

- 연산 프로퍼티로 구현할 경우 get-only 도 가능하고 getter setter 모두 만들어서 둘 다 사용 가능

---

### { get set }

```swift
protocol Band {
	var drum: String { get set }
}

class RunnerBand: Band {
	let drum: String = "Kadan"  // error! Type 'RunnerBand' does not conform to protocol 'Band'
}

class MentorBand: Band {
	var drum: String = "Sup" 
}
```

- { get set } 으로 선언 될 경우, 저장 프로퍼티는 무조건 var 로 선언
- { get } 때와 다르게, 연산 프로퍼티는 getter setter 모두 제공하는 것이 필수

---

### 프로토콜은 1급 객체이다

```swift
protocol Band {
	var piano: String { get }
}

struct RunnerBand: Band {
	var piano: String = "Kadan" // 프로토콜에 선언된 필수 프로퍼티
	var bandName: String = "One" // 내부에서 사용하고 싶은 별도 프로퍼티
}

let one: Band = RunnerBand.init()
```

- 1급 객체 특징에 의해 Band라는 타입으로 프로토콜을 선언하고, `RunnerBand`의 인스턴스를 프로토콜 타입을 가진 변수에 대입 가능. → `RunnerBand`라는 구조체 인스턴스를 프로토콜 타입으로 “타입 캐스팅” 하기 때문에 가능하다.
- 대신 타입 자체가 Band라는 프로토콜을 따르기 떄문에 `bandName`은 접근 불가능