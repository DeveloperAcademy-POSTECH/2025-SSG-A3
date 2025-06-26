
> [!question]  
> GQ1. Decodable은 어떤 상황에서 사용되는가?  
> GQ2. 외부 데이터(JSON 등)가 어떻게 Swift 타입으로 변환되는가?  
> GQ3. Decodable을 사용할 때 자동 구현과 커스텀 구현은 어떤 차이가 있는가?

## Description
**Decodable**
- Swift 표준 라이브러리에서 제공하는 **역직렬화(deserialization)** 프로토콜
- JSON, plist 등 외부 포맷의 데이터를 Swift 타입(예: `struct`, `class`, `enum`)으로 변환할 수 있게 해줌
- 보통 서버로부터 받은 JSON 데이터를 Swift 모델로 바꿀 때 사용됨
- `Codable`의 구성 요소 중 하나이며, `Encodable`과 함께 양방향 데이터 직렬화 기능 제공

## 주요 기능
- **자동 디코딩 지원**
    - `Decodable`을 채택한 타입이 저장 프로퍼티만으로 구성되어 있다면, Swift가 자동으로 디코딩 구현 생성
        
- **커스텀 디코딩 가능**
    - 외부 데이터가 복잡하거나 변환이 필요할 경우 `init(from decoder: Decoder)`를 구현해 직접 처리 가능
        
- **JSONDecoder, PropertyListDecoder 등과 연동**
    - 다양한 외부 포맷에 대응하여 API 응답 처리, 파일 불러오기 등에 활용 가능
        
- **CodingKeys 사용 가능**
    - JSON 키 이름과 Swift 프로퍼티 이름이 다를 경우 매핑 가능
        

## 코드 예시

### 기본 사용 (자동 디코딩)

```swift
import Foundation

struct User: Decodable {
    let name: String
    let age: Int
}

let jsonString = """
{
    "name": "my",
    "age": 23
}
"""

if let jsonData = jsonString.data(using: .utf8) {
    let decoder = JSONDecoder()
    if let user = try? decoder.decode(User.self, from: jsonData) {
        print(user)
    }
}
```

**코드 설명**
1. `User` 구조체가 `Decodable`을 채택 → Swift가 자동으로 디코딩 구현 생성
2. JSON 문자열을 `Data` 타입으로 변환
3. `JSONDecoder()`를 통해 `jsonData`를 Swift 타입(`User`)으로 변환
4. `decode(_:from:)`에서 `User.self`를 타입으로 명시

**출력 결과
```swift
User(name: "my", age: 23)
```

---

### 커스텀 디코딩 → JSON 키 이름이 다르거나 변환 필요할 때

```swift
struct Product: Decodable {
    let name: String
    let age: Int

    enum CodingKeys: String, CodingKey {
        case name = "product_name"
        case age = "person_age"
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let rawName = try container.decode(String.self, forKey: .name)
        self.name = rawName.capitalized
        self.age = try container.decode(Int.self, forKey: .age)
    }
}
```

**설명**
1. `Decodable`을 채택하되 `init(from:)`을 구현 → 직접 디코딩 로직 작성
2. `CodingKeys`로 외부 JSON 키(`product_name`, `person_age`)를 Swift 프로퍼티와 매핑
3. `capitalized`를 사용하여 name의 첫 글자를 대문자로 변환

**예제 JSON 데이터**
```json
{
  "product_name": "my",
  "person_age": 23
}
```

**결과**
```swift
Product(name: "Minji", age: 23)
```

## Keywords
- [[Codable]]
- [[Decodable]]
- JSONDecoder
- CodingKeys
- 역직렬화 (Deserialization)
- JSON 파싱

## References
- [Apple 공식 Decodable 문서](https://developer.apple.com/documentation/swift/decodable)
