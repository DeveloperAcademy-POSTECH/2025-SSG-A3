Optional Unwrapping[옵셔널 언래핑] : 옵셔널의 값을 추출
옵셔널 타입의 값을 출력하면, `Optional("")`와 같이 옵셔널로 래핑(wrapping)되어 있다. 
*어떻게 옵셔널의 값을 추출할까?*

 => Optional Binding[옵셔널 바인딩] : 옵셔널의 값이 존재하는지를 검사한 뒤 값이 존재한다면 그 값을 다른 변수에 대입
- if let
	- 옵셔널 타입인 변수의 값이 존재할 경우를 가정하고, 값이 존재하는 경우에만 블록 내에서 구문을 실행
	- 값이 없을 때는 if let 구문을 지나치고 밑에 코드를 순차적으로 실행
```swift
var optionalName: String? = "One"

if let name = optionalName {
	print(name) //optionalName의 값이 존재한다면 출력
}
//optionalName의 값이 존재하지 않으면 if let 구문 생략
```
- guard let
	- 값이 존재하지 않을 경우 블록 내 구문을 실행
	- 값이 존재하는 경우 guard let 구문을 지나치고 밑에 코드를 순차적으로 실행
```swift
var optionalAge: Int?

guard let age = optionalAge else {
	return //optionalAge의 값이 존재하지 않는 경우, 해당 구문에서 함수 종료
}
print(age) //optionalAge 값이 존재한다면 해당 값 출력
```
- 효용 : 값의 존재 유무를 미리 검사한 후 추출하여, 매우 **안전한** 추출 방법이다.

=> Optional Chaining[옵셔널 체이닝] : 옵셔널 변수, 메서드에 접근할 때 사용
- Optional 변수 값이 nil인 경우 nil을 반환하고, 값이 존재한다면 프로퍼티나 매서드에 접근
- +)`.`(dot)을 통해 내부 프로퍼티나 메서드에 접근할 떄 옵셔널 값이 하나라도 껴 있으면, 옵셔널 체이닝
```swift
var optionalString: String? = "Hello"
if let count = optionalString?.count {
	print("optionalString은 \(count)개의 문자를 가지고 있습니다.")
} else {
	print("optionalString에는 값이 없습니다.")
}
```

=> [nil 병합 연산자] : `??` 연산자를 이용하여 nil일 경우 기본 값을 반환
```swift
var optionalString: String? = nil
let string = optionalString ?? "qwer"
print(string) //qwer
```


=>[강제 언래핑] :  ! 키워드를 사용해서 강제로 옵셔널 값 추출
```swift
var optionalString: String? = "qwer"
var emptyString: String? = nil

print(optionalString) //Optional("qwer")
print(optionalString!) //qwer
print(emptyString!) //Error
```
	=> `nil`값을 가진 변수를 강제 언래핑하게 되면 컴파일 에러가 발생하게된다. 따라서, 강제 언래핑은 무조건 값을 가지고 있는 옵셔널 변수에만 사용하도록 한다.



[[Optional]]
