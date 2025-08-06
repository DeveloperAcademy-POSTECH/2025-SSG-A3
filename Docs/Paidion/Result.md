**A value that represents either a success or a failure, including an associated value in each case.**

# Result란?

> **성공과 실패**, 두 가지 결과를 명확하게 표현하는 **열겨형(enum) 타입**으로
> **각 경우에 따른 연관값**을 포함하여 성공과 실패를 나타내는 값

```swift
@frozen public enum Result<Success, Failure> where Failure : Error, Success : ~Copyable {
    /// 성공시에 제너릭으로 설정한 Success 타입의 연관값을 갖는다.
    case success(Success)
    /// 실패시에 제너릭으로 설정한 Failure 타입의 연관값을 갖는다.
    case failure(Failure)

```


# 도입 배경 

> Swift는 기본적으로 `throws`, `try`, `catch` 문법을 통해 오류를 처리한다.
> 콜백(Completion Handler)에서 성공과 실패 값을 별도의 optional 파라미터로 나누거나, 여러 개의 클로저를 사용해야 했고, 이로 인해 코드가 복잡해지고 버그가 생길 여지가 많았다. 

### 기존 방식: Completion Handler + Optional + Error

Swift 5 이전에는 아래와 같이 비동기 작업의 결과를 **옵셔널 값과 Error로 처리** 하곤 했다.

```swift
func fetchUserData(completion: @escaping (User?, Error?) -> Void)
```

- `User`와 `Error` 둘 다 `nil`인 경우도 존재할 수 있어서 애매한 상태를 만들 수 있음
- 개발자가 항상 상태를 수동으로 체크해야 함
- 코드의 가독성과 안정성이 떨어짐


# Result 사용

## 장점

- 성공과 실패가 명확하게 구분됨
- 한 번에 하나의 상태만 존재함 (`success` 또는 `failure`)
- switch 문이나 `.map()`, `.flatMap()` 등의 함수형 처리 가능
- 테스트나 Mocking에도 유리함

## Preserving the Results of a Throwing Expression

> 성공과 실패 여부에 상관없이 함수나 표현식의 전체 결과를 보존해서 활용해야할 때가 있다.
> 이렇게 잠재적으로 실패할 수 있는 동작이 필요한 상황에서 Result 타입을 활용하면 모든 결과를 보존하여 활용할 수 있다.

```swift
enum EntropyError: Error {
	case entropyDepleted
}

struct UnreliableRandomGenerator {
	func random() throws -> Int {
		if Bool.random() {
			return Int.random(in: 1...100) } 
		else {
			throw EntropyError.entropyDepleted
		}
	}
}

var results: [Result<Int, Error>] = []
let randomnessSource = UnreliableRandomGenerator()
  
let sample = Result { try randomnessSource.random() }

/// random() 성공 및 실패 여부와 관계 없이 모든 결과가 result 배열에 저장된다.
results.append(sample)

```

## Reference
- [Writing Failable Asynchronous APIs](https://developer.apple.com/documentation/swift/writing-failable-asynchronous-apis)
- [Preserving the Results of a Throwing Expression](https://developer.apple.com/documentation/swift/preserving-the-results-of-a-throwing-expression)
- [Result 이란 + 사용법](https://luke-kong.oopy.io/swift/result-type)
- [Swift - Result 사용법](https://luke-kong.oopy.io/swift/result-type)
- [Swift - Result 타입](https://swifty-cody.tistory.com/17)