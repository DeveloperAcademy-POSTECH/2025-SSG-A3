Optional[옵셔널] : 사용자 타입이 초기화 동안 값을 설정할 수 없거나 추후에 "no value"를 가질 수 있기 때문에 논리적으로 "no value"를 가질 수 있는 하나의 저장된 프로퍼티를 가지고 있다면 optional 타입으로 프로퍼티를 선언한다.  optional 타입의 프로퍼티는 자동적으로 초기화 동안 "no value yet"을 가진다는 의도를 위해 `nil` 의 값으로 초기화 된다.
- 공식문서 링크 : https://docs.swift.org/swift-book/documentation/the-swift-programming-language/initialization/#Optional-Property-Types
- `nil` : 변수에 **값이 없음**을 나타냄
	- 값이 없다는 것은 단순히, `""`이나 `0`과 다른 순수하게 아무것도 없다는 의미이다. 따라서, String, Int 등의 타입에 할당할 수 없다.
	-  => 옵셔널 : 값이 있을 수도 있고, 없을 수도 있는 변수를 정의할 때는 타입 뒤에 `?`를 붙인다.
```
var a: String?
print(a) //nil

a = "qwer"
print(a) //Optional("qwer")
```

- Optional Wrapping[옵셔널 래핑]
	- ex)
		- 값은 내용물, 옵셔널은 택배상자
		- 변수가 옵셔널 타입으로 정의되는 순간, 택배상자에 내용물이 있든 없든, 상자하나를 포장 -> wrapped
```
var test: Int? = 10
var answer = test
```
	=> test가 옵셔널 타입이어서 자동으로 answer가 옵셔널 타입으로 지정된다.
```
var test: Int? = 10
var answer: Int = test // Error
```
	=> answer은 옵셔널이 아니기 때문에 항상 값을 가지고 있어야 하지만, test는 옵셔널로 선언된 변수여서 실제 코드가 실행되기 전까지는 변수에 값의 존재 여부를 확인할 수 없다. 따라서, Swift 컴파일러는 안전을 위해 answer에 test를 대입하지 못하도록 한다!!!




[[Optional Unwrapping]]
