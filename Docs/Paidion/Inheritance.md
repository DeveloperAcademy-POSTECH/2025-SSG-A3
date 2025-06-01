**Subclass to add or override functionality.**
## 하위 클래스 (Subclassing)
> 하위 클래스`Subclass` 가 상위 클래스 `Superclass` 의 특성을 상속한다는 것을 나타내기 위해 
> 하위 클래스 이름은 상위 클래스 이름 전에 콜론 `:` 으로 구분하여 작성한다. 

```swift
class Vehicle {
	var currentSpeed: Double = 0.0
	var description: String {
		return "traveling at \(currentSpeed) kilometers per hour"
	}
	func makeNoise() {
		// do nothing - an aribitary vehicle doesn't necessarily make a noise
	}
}

class Cycle: Vehicle {
	var hasWheel: Bool = true 
	subscript(wheelCount: Int) -> String {
		switch wheelCount {
		case 1: return "Unicycle"
		case 2: return "Bicycle"
		case 3: return "Tricycle"
		case 4...: return "Unusual Cycle"
		default: return String(describing: type(of: self)) // Prints "Vehicle"
		}
	}
}

class Bicycle: Vehicle {
	/*  Inheritance from Vehicle */
	// 	var currentSpeed = 0
	//	var description: String {
	//		return "traveling at \(currentSpeed) kilometers per hour"
	//	}
	//  func makeNoise() { ... }
	//	var hasWheel: Bool = true 
	//  subscript { ... }
	var hasBucket: Bool = true
}

class Tandem: Bicycle {
	/*  Inheritance from Bicycle */
	// 	var currentSpeed = 0
	//	var description: String {
	//		return "traveling at \(currentSpeed) kilometers per hour"
	//	}
	//  func makeNoise() { ... }
	//	var hasWheel: Bool = true 
	//  subscript { ... }
	//  var hasBucket: Bool = true
	var numberOfSeats: Int = 2
}
```

## 재정의 (Overriding)
> 하위 클래스에서 재정의 하고 싶은 프로퍼티, 메서드 등을 `override` 키워드를 사용해서 재정의 할 수 있다.
 
### 프로퍼티 재정의 
- 접근자 `Getter` , 설정자 `Setter` , 옵저버 `Observer` 를 재정의 한다는 의미 -> 프로퍼티 자체 재정의 X
- `super` 키워드를 사용하여 상위 클래스의 프로퍼티를 사용할 수 있다.
- 저장 프로퍼티는로 재정의 할 수 없다. 
- 하위 클래스에서는 상속 받은 프로퍼티의 이름과 타입만 알고 있으므로, 재정의 하는 프로퍼티의 이름과 타입이 상위 클래스의 프로퍼티의 것과 일치해야 한다. 
```swift
class Car: Vehicle {
	var gear: Int = 1
	/* Stored property can not be overriden. */
	// override var currentSpeed: 10.0
	override var currentSpeed: Double {
		get {
			return super.currentSpeed
		}
		set {
			super.currentSpeed = newValue
			gear = Int(currentSpeed / 10.0) + 1 
		}
	}

	override var description: String {
		return super.description + " in gear \(gear)"
	}
}
let myCar = Car()
print(myCar.description)
// Prints "traveling at 10.0 kilometers per hour in gear 1"
```


### 메서드 재정의
```swift
class Ktx: Vehicle {
	override func makeNoise() {
		print("Ktx train horn sounds great")
	}
}
let pohangKtx = Ktx()
pohangKtx.makeNoise() // Prints "Ktx train horn sounds great"
```


### 서브스크립트 재정의
```swift
class NonCycle: Cycle {
	override subscript(wheelCount: Int) -> String {
		switch wheelCount {
		case ...0: return String(describing: type(of: self))
		default: return super[wheelCount]
		}
	}
}
```


### 프로퍼티 옵저버 재정의 
- 프로퍼티 옵저버를 추가하기 위해, 프로퍼티를 재정의할 수 있다. 
- 프로퍼티 옵저버를 재정의 해도, 슈퍼클래스에 정의한 프로퍼티 옵저버 또한 같이 동작한다.
- 변하지 않는 저장 속성 `constant stored property` 과 읽기 전용 연산 프로퍼티 `read-only property` 는 프로퍼티 옵저버를 추가할 수 없다. -> 변하지 않기 때문에 사용할 필요가 없다.
- 설정자와 프로퍼티 옵저버를 함께 재정의 할 수 없다. -> 프로퍼티의 변화를 감지하고 싶다면, 이미 구현해둔 설정자에서 처리하도록 한다.
```swift
class AutomaticCar: Car {
	override var currentSpeed: Double {
		willSet {
			print("`willSet` is called just before the value is stored.")
		}
		didSet { 
			print("`didSet` is called immediately after the new value is stored.")
		}
		/* Property Observer cannot be provided together with a getter/setter */
		// get { ... }
		// set { ... }
	}
}
let myAutomaticCar = AutomaticCar()
myAutomaticCar.currentSpeed = 50.0
print(myAutomaticCar.description)
// Prints "traveling at 50.0 kilometers per hour in gear 6"
```


### 재정의 방지
> 재정의를 방지하고 싶다면,`final` 키워드를 사용할 수 있다.  
> `final var, final func, final class func, final subscript` 모두 가능하다.
> `final class` 를 사용하면, 해당 클래스는 더 이상 상속할 수 없다. 

```swift
final class FinalClass: SwiftClass {
	var newStoredProperty: Int = 12
}

/***
 🚫Error: Inheritance from a final class 'FinalClass'
 class LastClass: FinalClass { ... }
***/
```

## 상속의 장단점
| 👍 장점                                                        | 👎 단점                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------------ |
| 1️⃣ **코드 재사용성** <br>: 중복 코드 없이 상속 받은 프로퍼티, 메서드 그대로 사용 가능     | 1️⃣ **강한 결합**<br>: 상위 클래스의 변경이 하위 클래스에 직접적인 영향                     |
| 2️⃣ **유연한 확장**<br>: 재정의 `overriding` 와 다형성 `Polymorphism` 지원 | 2️⃣ **캡슐화 약화**<br>: 상위 클래스 내부 상태나 동작이 하위 클래스에 노출되고, 상위 클래스에 의존하게 됨 |
| 3️⃣ **계층적 구조**<br>: 명확한 계층 구조로 인해 코드 이해도 증가                  | 3️⃣ **복잡성 증가**<br>: 계층이 깊어질수록 복잡성 증가, 디버깅/유지보수 어려움                 |
|                                                              | 4️⃣ **불필요한 상속**<br>: 메모리 사용이나 성능에 악형향 줄 수 있음                       |
 
## 상속을 잘 사용하는 방법

### 상속이 적합한 상황
- 여러 클래스가 **공통적으로** 사용하는 프로퍼티나 속성이 있을 때
- **계층적 구조**가 명확히 필요할 때

### 상속을 피해야 하는 상황
- 단순히 코드 재사용만 필요할 때 ➡️ **프로토콜**과 **확장**을 생각해보자!
- 여러 타입의 서로 다른 기능을 조합해야 할 때 (다중 상속이 필요할 때) ➡️ **프로토콜** 채택 권장!
- 상속 계층이 깊어져서 복잡성이 높아질 때 ➡️ **합성** 혹은 **프로토콜**을 생각해보자! 

### 상속을 잘 사용하는 방법
- **최소한의 계층 구조** : 상속 계층 구조를 얕게 하여 복잡성을 줄인다. 
- **`final` 키워드 사용** : 불필요한 상속 및 재정의를 방지한다.
- **`super` 호출 명확화** : 부모 클래스에서 수행되어야 하는 중요한 기능(예: 뷰 초기화, 데이터 로딩)이 누락되지 않도록 한다.
- **접근 제어자 사용** : 필요에 따라 접근 제어자를 적절히 설정해 캡슐화를 유지하도록 한다.
- **프로토콜과의 조화** : 상속과 프로토콜을 적절히 조합하여 유연하고 확장성 있는 구조를 설계하도록 한다.


## Reference
- [Swift Documentation - Inheritance](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/inheritance/)
- [Swift : 기초문법 상속#2 재정의 override](https://seons-dev.tistory.com/entry/Swift-기초문법-상속2-재정의-override)

