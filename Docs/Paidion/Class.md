### 객체 지향 프로그래밍(OOP)
> 컴퓨터 프로그램을 **"객체"** 에 기반한 컴퓨터 프로그래밍 패러다임

#### 객체 (Object)
>**객체 =  프로퍼티  + 메서드**

**프로터피** 는 객체의 정보를 지니는 변수이다. (a.k.a fields, members or attributes)
**메서드** 는 객체의 행동을 정의하는 함수이다. 


### What is `class`?
> `class` 는 객체의 프로퍼티와 메서드가 정의된 사용자 정의 타입으로, **객체의 설계도** 같은 역할을 한다.

```swift
class SwiftClass {
	var mutableStoredProperty: Int = 42
	let immutableStoredProperty: Int = 42
	static var mutableTypeProperty: Int = 42
	static let immutableTypeProperty: Int = 42
	
	class var classComputedTypeProperty: Int { // overridable type property
		return 42 * 42
	}
	
	func instanceMethod() {
		print("instanceMethod")
	}
	
	static func typeMethod() {
		print("typeMethod")
	}
	
	class func classTypeMethod() { // overridable type method
		print("class keyword makes type function overridable")
	}
}
```


### `class` vs. `struct`

|              | **구조체(struct)**                          | **클래스(class)**                                            |
| ------------ | ---------------------------------------- | --------------------------------------------------------- |
| **타입**       | 값 타입 (Value Type)                        | 참조 타입 (Reference Type)                                    |
| **메모리**      | · Stack 에 저장<br>· 값 복사 전달<br>· 메모리 자동 제거 | · Heap 에 저장<br>· 주소 전달<br>· ARC 로 관리                      |
| let / var 선언 | let 으로 선언하면<br>저장 속성 모두 상수로 선언됨          | let 으로 선언하면<br>가리키는 인스터스만 고정<br>(저장 속성은 let / var 선언에 따름) |
| 생성자          | 멤버와이즈 이니셜라이저 (자동) 제공                     | 편의 생성자 사용 가능                                              |
| 소멸자          | 없음                                       | 있음                                                        |
| 상속           | 상속 불가능                                   | 상속 가능                                                     |

#### 값 타입과 참조 타입의 차이

##### 값 타입 `Value Type`
- 값 타입은 데이터를 전달할 때, 새롭게 인스턴스를 만들고 값을 복사해서 전달한다.
- `Struct`,`Int` , `String`, `Tuple`, `Enum`, `Collection`

```swift
struct ValueType { 
	var property: Int = 0 
} 

var structOne = ValueType()
var structTwo = structOne

structOne.property = 42

print(structOne.property) // prints 42
print(structTwo.property) // prints 0
```


##### 참조 타입 `Reference Type`
- 참조 타입은 데이터를 전달할 때, 메모리 주소를 전달한다.

```swift
class ReferenceType { 
	var property: Int = 0 
} 

var classOne = ReferenceType()
var classTwo = classOne // classOne and classTwo point same memory address

classTwo.property = 42

print(classOne.property) // prints 42
print(classTwo.property) // prints 42
```


####  `class` vs. `struct` 의 사용 시기는 어떻게 구분할까?

##### `struct`: ~~~~~~~ 때, 사용한다.
- 연관된 데이터를 단순히 캡슐화 하고 싶을 때
- 데이터를 참조하는 것보다 복사하는 것이 효율적이거나, 복사하는 것이 합당할 때

##### `class`:  ~~~~~~ 때, 사용한다.
- 인스턴스 참조하는 곳에 인스턴스의 변화를 반영하고 싶을 때 -> 파일 관리, 네트워크 연결 등




### Reference
- [Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing-between-structures-and-classes)
- [Swift - 구조체(Struct)와 클래스(Class) 완전 정복하기: 기본 개념부터 프로퍼티, 인스턴스, 상속까지](https://mini-min-dev.tistory.com/117)