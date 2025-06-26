
> [!question]  
> GQ1. Codable은 왜 만들어졌고, 어떤 상황에서 사용되는가?  
> GQ2. Codable은 Encodable과 Decodable을 어떻게 통합하여 동작하는가?  
> GQ3. Codable 사용 시 자동 처리와 커스텀 구현의 차이는 무엇인가?  
> GQ4. 직접 encode(to:)와 init(from:)을 구현할 때 어떤 유연함이 생기는가?

## Description
**Codable**
- Swift의 `Codable`은 Swift 타입을 JSON, plist, XML 등 **외부 형식으로 인코딩하거나 디코딩할 수 있게 해주는 프로토콜**
- 내부적으로는 `Encodable`과 `Decodable` 프로토콜을 합쳐 만든 **타입 별명(typealias)**
	- `typealias`: 기존 타입에 새로운 이름(별명)을 붙이는 문법
```swift
typealias Codable = Decodable & Encodable
```

- 서버 통신, 파일 저장, 설정 정보 처리 등 **데이터 직렬화 및 역직렬화** 작업에 사용되며, Swift 컴파일러가 대부분의 경우 자동으로 구현을 생성해 줌
	- Swift의 **자동 Codable 생성 기능**을 활용, 별도로 `encode(to:)`, `init(from:)`을 구현하지 않아도 잘 작동

## 주요 기능
- **양방향 직렬화 지원**  
    Swift 객체를 외부 데이터(JSON 등)로 바꾸고, 외부 데이터를 다시 Swift 객체로 복원 가능
    
- **자동 인코딩 및 디코딩 지원**  
    모든 저장 프로퍼티가 `Codable`을 따를 경우 컴파일러가 자동으로 처리
    
- **커스텀 구현 가능**  
    복잡한 데이터 매핑이나 변형이 필요한 경우, 직접 `encode(to:)`와 `init(from:)`을 정의 가능
    
- **다양한 인코더/디코더와 연동**  
    `JSONEncoder`, `PropertyListEncoder`, `JSONDecoder` 등과 함께 사용 가능
    
- **타입 안정성(type safety)**  
    정해진 구조대로만 인코딩/디코딩되므로 런타임 오류 방지에 유리

## 코드 예시
### 기본 사용 예제 (자동 인코딩 & 디코딩)

```swift
import Foundation

struct User: Codable {
    var name: String
    var age: Int
}

let user = User(name: "my", age: 23)

// 인코딩
let jsonData = try JSONEncoder().encode(user)
let jsonString = String(data: jsonData, encoding: .utf8)
print(jsonString!)  // {"name":"my","age":23}

// 디코딩
if let data = jsonString?.data(using: .utf8) {
    let decodedUser = try JSONDecoder().decode(User.self, from: data)
    print(decodedUser.name)  // "my"
}
```

**코드 설명**
1. `Foundation` 프레임워크를 가져와 `Codable`, `JSONEncoder`, `JSONDecoder` 같은 기능을 사용할 수 있게 함
2. `User`라는 구조체를 정의하고 `Codable` 채택
    → `Codable`은 `Encodable` + `Decodable`의 통합 프로토콜로, Swift가 자동으로 인코딩/디코딩 코드를 생성
3. `User(name: "my", age: 23)` 인스턴스를 생성
4. `JSONEncoder().encode(user)`를 통해 `User` 인스턴스를 JSON 형식의 `Data`로 인코딩
5. `String(data: jsonData, encoding: .utf8)`로 JSON 데이터를 사람이 읽을 수 있는 문자열로 변환
6. 출력 결과 -> JSON 문자열
    ```Json
    {"name":"my","age":23}
    ```
7. 디코딩을 위해 JSON 문자열을 다시 `Data` 형식으로 변환
8. `JSONDecoder().decode(User.self, from: data)`를 사용해 JSON 데이터를 다시 `User` 인스턴스로 복원
9. 복원된 `decodedUser.name`을 출력하면 `"my"` 출력

---

### 커스텀 인코딩 & 디코딩 예제

```swift
struct Person: Codable {
    let name: String
    let age: Int

    enum CodingKeys: String, CodingKey {
        case name = "person_name"
        case age = "person_age"
    }

    // 커스텀 인코딩
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(name.uppercased(), forKey: .name) // 대문자로 인코딩
        try container.encode(age + 1, forKey: .age)             // 나이를 1살 더해서 저장
    }

    // 커스텀 디코딩
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let rawName = try container.decode(String.self, forKey: .name)
        self.name = rawName.lowercased()      // 디코딩 시 다시 소문자로
        let rawAge = try container.decode(Int.self, forKey: .age)
        self.age = rawAge - 1                 // 저장된 값에서 다시 원래대로
    }
}
```

**코드 설명**
1. `struct Person: Codable`  
    → `Codable`을 채택해 JSON으로 인코딩/디코딩이 가능하도록 만듬
2. `let name`, `let age`  
    → 두 개의 저장 프로퍼티는 각각 문자열 이름과 정수 나이를 저장
3. `enum CodingKeys: String, CodingKey`  
    → JSON의 키 이름을 Swift 프로퍼티 이름과 다르게 지정하기 위해 사용
    → `name`은 `"person_name"`으로, `age`는 `"person_age"`로 매핑

**인코딩 부분**
4. `func encode(to encoder: Encoder) throws`  
    → Swift가 자동 생성하지 않고 직접 인코딩 동작을 정의
5. `let container = encoder.container(keyedBy: CodingKeys.self)`  
    → JSON 형태로 만들기 위해 키-값 컨테이너를 준비
6. `try container.encode(name.uppercased(), forKey: .name)`  
    → `name` 값을 대문자로 변환하여 `"person_name"` 키에 저장
    예: `"my"` → `"MY"`
7. `try container.encode(age + 1, forKey: .age)`  
    → 실제 나이보다 1살 더 많은 값을 `"person_age"` 키에 저장합니다.  
    예: `23` → 저장은 `254

**디코딩 부분**
8. `init(from decoder: Decoder) throws`  
    → Swift가 자동 생성하지 않고 직접 디코딩 동작을 정의
9. `let container = try decoder.container(keyedBy: CodingKeys.self)`  
    → JSON 데이터를 키-값으로 접근하기 위해 컨테이너를 생성
10. `let rawName = try container.decode(String.self, forKey: .name)`  
    → `"person_name"` 키로부터 문자열을 읽어옴
11. `self.name = rawName.lowercased()`  
    → 대문자로 저장되어 있는 값을 다시 소문자로 변환하여 저장
12. `let rawAge = try container.decode(Int.self, forKey: .age)`  
    → `"person_age"` 키로부터 정수를 읽어옴
13. `self.age = rawAge - 1`  
    → 저장된 값에서 1을 빼서 원래 나이로 복원

## Keywords
- [[Encodable]]
- [[Decodable]]
- JSONEncoder 
- JSONDecoder
- 타입 안전성
- 데이터 모델링

## References
- [Apple 공식 Codable 문서](https://developer.apple.com/documentation/swift/codable)