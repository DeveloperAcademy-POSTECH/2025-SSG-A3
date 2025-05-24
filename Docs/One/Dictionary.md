Dictionary[딕셔너리] : Key : value로 저장되는 컬렉션 타입
	정렬되지 않은 컬렉션 -> 순서가 존재하지 않음 => Dictionary를 출력하면, 삽입 순서에 상관없이 출력된다.
	값은 중복 가능하지만, 키는 중복이 불가능하다
	Swift는 엄격하기 때문에 모든 Key의 자료형과 로 모든 Value의 자료형은 같아야 한다.
- **메서드**
	- 생성:
		- `var dict1: [String: Int] = ["ABC": 123, "DEF": 456]`
		- `var dict2 = Dictionary<String, Int>()
		- `var dict3 = [String: Int]()`
		- Swift는 Type에 굉장히 민감하여 선언과 동시에 Type을 꼭 명시하거나 추론하게 할 수 있어야 한다. 따라서, 딕셔너리에도 해당 Type만 저장이 가능하다.
		- -> 해당 값의 타입을 런타임 시점에 알 수 있을 때는 `Any` Type을 사용하거나`NSDictionary`를 사용한다.
			- `var dict4: [String: Any] = ["qwer": "asdf", "AAA": 111]`
			- `var dict5: NSDictionary = ["qwer": "asdf", "AAA": 111]`
		- +)Key의 자료형을 `Any`로 명시하는 경우
			- 딕셔너리의 원리가 Key 값을 해시한것을 토대로 데이터를 저장하는데
			- -> 따라서, Key 값은 Hashable 프로토콜을 준수하는 자료형만 올 수 있다.
			- -> `Any`자료형은 Hashable 프로토콜을 준수하지 않기 때문에,
			- => Key의 자료형으로 `Any` type을 사용할 수 없다.
	- 갯수 확인
		- `.count` - 딕셔너리 갯수 확인
		- `.isEmpty` - 딕셔너리가 비었는지 확인
	- 접근
		- 딕셔너리의 요소에 키를 사용해서 접근할 때, 기본 반환값이 OptionalType이다.
			- (해당 키가 존재하지 않을 것을 대비)
			- default값을 직접 명시하면 OptionalType이 아니게 된다.
				- `let exam = dict1["ABC", 1]`
	- 요소 추가
		- Subscript로 추가
			- `dict1["GHI": 789]`
			- -> 해당 키가 없다면, 딕셔너리에 추가
			- -> 해당 키가 있다면, 값 업데이트
		- `.updateValue`
			- `dict1.updateValue(789, forKey: "GHI)`
			- -> 해당 키가 없다면, 추가하고 nil 출력
			- -> 해당 키가 있다면, 값 덮어쓰고 덮어쓰기 전 값 출력
	- 요소 삭제
		- subscript
			- `dict1["ABC"] = nil` 
			- -> 해당 키가 없으면 아무일도 발생하지 않는다.(에러 안남)
			- -> 해당 키가 있으면 해당 요소 삭제
		- `.removeValue(forKey: )` 
			- `dict1.removeValue(forKey: "ABC")`
			- -> 해당 키가 없다면, nil 출력
			- -> 해당 키가 있다면, 해당 요소 삭제하고, 삭제된 Value를 OptionalType으로 반환
		- `.removeAll()`
			- -> 전체 삭제
	- 나열
		- Key 나열
			- `.keys` -> 실행할 때마다 순서가 바뀐다
			- `.keys.sorted()` -> 정렬 가능
		- Value 나열
			- `.values` -> 실행할 때마다 순서가 바뀐다
			- `.values.sorted()` -> 정렬 가능
	- 비교
		- 비교 연산자로 비교할 수 있으나, 모든 Key와 Value가 모두 일치해야하고, 대소문자도 같아야한다.
	- 검색
		- `.contains` -> 해당 클로저를 만족하는 요소가 하나라도 있으면 true
		- `.first` -> 해당 클로저를 만족하는 첫 번째 요소 튜플로 출력(순서가 변경될 수 있음)
		- `.filter` -> 해당 클로저를 만족하는 요소만 모아서 새 딕셔너리로 리턴
``` Swift
var dict1 = ["ABC": 123, "DEF": 456, "GHI": 789]

let condition: ((String, Int)) -> Bool = {
	$0.0.contains("h")
}

dict1.contains(where: condition)
dict1.first(where: condition)
dict1.filter(condition)
```
- **Dictionary + 반복문**
	- for문 사용
		- 루프상수가 튜플일 때
		- 루프상수가 변수일 때
	- forEach문 사용
		- `for - in` 과 똑같으나 클로저로 넘겨준다.
	- +)마찬가지로 딕셔너리라 순서가 매번 바뀔 수 있다.
``` Swift
let dict1 = ["ABC": 123, "DEF": 456, "GHI": 789]


for (key, value) in dict1 {
	print("\(key) : \(value)")
} //튜플방식

for element in dict1 {
	print("\(element.key) : \(element.value)")
} //변수방식

dict1.forEach {
	print("\($0.key)")
} //forEach
```
참고자료)
	https://developer.apple.com/documentation/swift/dictionary
	https://babbab2.tistory.com/113
	https://babbab2.tistory.com/95