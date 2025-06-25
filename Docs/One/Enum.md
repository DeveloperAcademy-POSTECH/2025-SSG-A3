- 열거형[Enum]
	- -> 연관된 값의 그룹을 정의하고, 코드에서 이러한 값들을 구조화된 방식으로 사용하도록 돕는 자료형
		- Swift에서 열거형은 단순한 값의 그룹을 정의하는 것 이상으로, 각 값에 추가 정보를 저장하거나 메서드를 정의할 수도 있다.
	- 열거형 내의 값은 [열거형 케이스] 또는 [멤버]라고 한다.
	- 특징
		- [타입 안정성] -> 정해진 값만 사용하도록 강제하여 타입 안정성을 제공
		- [추가 기능] -> 연관된 값을 저장하거나, 메서드를 추가하여 열거형 확장 가능
- 열거형 선언
		- `enum`키워드를 사용하여 선언
		- 열거형 내부에 `case`로 선언된 멤버들은 하나의 값으로 나열된다.
- 열거형의 원시값
		- 열거형에서는 각 케이스에 원시값을 저장할 수 있다.
		- `rawValue` 속성을 사용하여 원시값에 접근 가능
		- +)값을 유추할 수 있으면 자동으로 값 대입 가능
``` swift
enum Weekday: Int {
	case monday = 1
	case tuesday
	case wednesday
	case thursday
	case friday
	case saturday
	case sunday
}
let today: Weekday = .friday
print(today.rawValue) // 5
```
- 열거형
	- 열거형의 연관값
		- 열거형에서는 각 케이스별로 연관된 값을 저장할 수 있다.
		- 각 케이스는 각각 다른 형태의 데이터를 가질 수 있다.
``` swift
enum Barcode {
	case upc(Int, Int, Int, Int)
	case qrCode(String)
}
```
- 열거형의 메서드 정의
	- 열거형은 케이스로 메서드를 가질 수 있다.
	- +)메서드는 `case` 키워드를 사용하지 않아도된다.
	- +)열거형 내부의 값을 변경할 때는 `mutating` 키워드를 사용한다.
``` swift
enum LightSwitch {
	case on
	case off
	mutating func toggle() {
		self = (self == .on) ? .off : .on
	}
}
```
- [열거형의 Iterator]
	- Swift에서 열거형은 반복가능한 타입이 아니어서, 반복 구문을 사용하여 각 케이스를 순회할 수 없다.
	- -> `CaseIterable`프로토콜을 채택하여, 열거형의 모든 케이스를 반복 가능하게 만들 수 있다.
		- => `CaseIterable`프로토콜로인해, 자동으로 생성된 배열 `allCases`를 사용하여 각케이스를 순회할 수 있습니다.
``` swift
enum Beverage: CaseIterable {
	case coffee
	case tea
	case juice
}

	for beverage in Beverage.allCases {
	print(beverage)
}
///coffee
///tea
///juice
```
- 장점
	- 열거형은 다양한 타입을 저장할 수 있어, 앱의 다양한 상태를 관리하는데 매우 유용하다.
	- 로그인 상태 관리, 네트워크 요청 상태 관리, UI 상태 관리 등 명확하게 정의된 값을 갖도록하여,
	- 코드의 가독성 상승, 오류 발생 방지 가능