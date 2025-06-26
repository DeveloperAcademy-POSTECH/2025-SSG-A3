### Protocol Extension

- 기본적으로 제공하고 싶은 기능(메서드)를 프로토콜에 제공.

```swift
protocol Band {
	var drum: String { get set }
	func play()
}

extension Band {
	func play() {
		print("day6: 한 페이지가 될 수 있게")
	}
}
```

- 프로토콜에 extension을 주고, 기본적으로 제공하고 싶은 기능을 extension 내부에 구현.
- 이럴 경우, 해당 Protocol을 채택한 곳에서 play()라는 요구사항을 구현하지 않아도 된다.

```swift
struct AcademyRunnerBand: Band {
	var drum: String = "Kadan"
}

AcademyRunnerBand().play()        // "day6: 한 페이지가 될 수 있게"
```

- extension에 구현되어 있는 play() 호출.

```swift
struct AcademyRunnerBand: Band {
	var drum: String = "Kadan"
	func play() {
		print("야댜: 이미 슬픈 사랑")
	}
}

AcademyRunnerBand().play()        // "야댜: 이미 슬픈 사랑"
```

- extension에 기능이 구현되어 있지만 프로토콜을 채택한 곳에서 직접 구현을 한다면? → extension 보다 직접 구현한 play()의 우선순위가 높음.

### 제약도 가능하다!

```swift
protocol Academy {}
protocol Band {
	func play()
}

extension Band where Self: Academy {
	func play() {
		print("아모르파티")
	}
}

struct RunnerBand: Band {}
RunnerBand().play() // error!

struct MentorBand: Band, Academy {}
MentorBand().play() // "아모르파티"
```

### 장점

- 가장 큰 장점은 코드 중복을 최소화할 수 있다는 점. → 여러타입에 대해 공통적으로 사용되는 요소들을 확장 한번으로 코드 중복 최소화!
- 기존의 프로토콜에서 새로운 기능을 추가하거나, 채택된 기능에 대해 기본 구현을 제공하여 코드 재사용성을 높일 수 있다.
- where 절을 이용한 조건부 확장으로 세밀한 제어 가능, TypeSafe한 코드 작성 가능

### 단점

- Object-C 와 Protocol의 호환성 문제 → Swift의 Extension 기능을 Object-C의 Protocol 과 함께 사용할 수 없다. → 코코아터치나 코코아프레임워크는 모두 Object-C 코드로 구현되어 있음. → 따라서 DataSource, Delegate 등 프레임워크 프로토콜에는 Extension 기능 불가