>[!question]
>GQ1. Encodable은 어떤 목적을 위해 사용되는가?
>GQ2. Swift 타입이 어떻게 외부 표현으로 변환되는가?
>GQ3. Encodable을 사용할 때 자동 구현과 커스텀 구현은 어떻게 다른가?

## Description
**Encodable**
- Swift 표준 라이브러리에서 제공하는 **직렬화(serialization)** 프로토콜
- Swift 타입(예: `struct`, `class`, `enum`)이 **외부 표현(JSON, plist 등)으로 변환될 수 있도록 보장**
    
- Swift에서 `Encodable`을 채택하면 `JSONEncoder`와 같은 인코더를 통해 해당 타입을 쉽게 `Data` 형식으로 바꿔 외부로 전송하거나 저장 가능
    
- `Codable`의 구성 요소 중 하나이며, `Decodable`과 함께 양방향 직렬화를 구성

## 주요 기능
- **자동 인코딩 지원**
	- `Encodable`을 채택한 타입이 저장 프로퍼티만으로 구성된 경우, Swift가 자동으로 인코딩 로직을 생성
	
- **커스텀 인코딩 가능**
	- `encode(to encoder: Encoder)` 메서드를 오버라이드하여 복잡한 변환이 필요한 경우 커스텀 인코딩을 정의 가능
    
- **JSONEncoder, PropertyListEncoder 등과 연동**
	- 다양한 외부 포맷(JSON, plist 등)에 대응 가능하여 서버 통신, 파일 저장 등에 유용
    
- **CodingKeys** 사용 가능
	- 프로퍼티 이름을 실제 출력되는 키와 다르게 매핑 가능

## 코드 예시

### 기본 사용 (자동 인코딩)
```Swift
import Foundation

struct User: Encodable {
	let name: String     
	let age: Int 
}  

let user = User(name: "my", age: 23)
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted

if let jsonData = try? encoder.encode(user),
	let jsonString = String(data: jsonData, encoding: .utf8) {     
	 print(jsonString) 
}
```
**코드 설명**
1. `User` 구조체가 `Encodable`을 채택함 → Swift가 자동으로 인코딩 코드를 만들어줌
2. `JSONEncoder()`는 Swift 객체를 **JSON 데이터(Data 타입)로 바꿔주는 인코더
3. `outputFormatting = .prettyPrinted` → JSON을 보기 좋게 줄바꿈과 들여쓰기 적용
4. `try? encoder.encode(user)` → `user` 객체를 JSON 형식의 `Data`로 변환
5. `String(data: jsonData, encoding: .utf8)` → 변환된 JSON 데이터를 UTF-8 문자열로 변환

**출력 결과**
```Swift
{   
	"name" : "my",
	"age" : 23
}
```
-> 별도 로직 없이, `Encodable`을 채택만 하면 자동으로 키-값 형태로 JSON이 만들어짐

---

###  커스텀 인코딩 -> `name` 값을 대문자로 인코딩, JSON 키 이름도 교체

```Swift
struct Product: Encodable {
	let name: String
	let age: Int  
	
	enum CodingKeys: String, CodingKey {
		case name = "product_name"         
		case age = "person_age"
	}      
	
	func encode(to encoder: Encoder) throws {
	    var container = encoder.container(keyedBy: CodingKeys.self)
	    try container.encode(name.uppercased(), forKey: .name)
	    try container.encode(age, forKey: .age)
	}
}
```

**설명**
1. `Product` 구조체가 `Encodable`을 채택했지만, **자동 인코딩 대신 직접 구현**하고 있음
2. `CodingKeys` 열거형을 정의해, JSON 출력 시 키 이름을 커스터마이징
	- `name` → `"person_name"`
	- `age` → `"person_age"`
3. `encode(to:)` 함수는 `Encodable`에서 제공하는 **직접 인코딩 구현 함수**
4. `container`는 JSON 딕셔너리처럼 키-값을 담는 객체
5. `encode(name.uppercased(), forKey: .name)`
    - 이름을 **대문자로 변환**해서 인코딩
    - ex) `"my"` → `"my".uppercased()` = `"My"`

## Keywords
- [[Codable]]
- [[Encodable]]
- JSONEncoder
- CodingKeys
- serialization

## References
- [Apple 공식 Encodable 문서](https://developer.apple.com/documentation/swift/encodable)