>[!question]
>GQ1. **`static`** 키워드는 언제 사용하는 것일까?
>GQ2. **`static`** 키워드는 왜 만들어졌을까?

## Description

**`static`** 키워드는 **타입 프로퍼티(Type Properties)** 와 **타입 메서드(Type Methods)** 를 정의할 때 사용한다.

**타입 프로퍼티**는, 해당 타입의 인스턴스가 아닌 타입 그 자체에 속하는 프로퍼티로, 해당 타입의 인스턴스의 수와 관계없이 타입 프로퍼티의 복사본은 하나만 있다. 

모든 인스턴스가 사용할 수 있는 보편적이고 정적인 값을 정의하거나, 모든 인스턴스에서 전역적으로 사용할 수 있는 변수를 정의할 때 사용한다.

**타입 프로퍼티**는 (1) 저장 타입 프로퍼티와 (2) 연산 타입 프로퍼티로 구성된다.

저장 타입 프로퍼티의 특징
- `var` / `let`  모두 선언 가능 
- 반드시 초기값을 설정해주어야 한다 ->  초기화 시점에 저장 타입 프로퍼티에 값을 할당하는 생성자(Initializer)를 갖고 있지 않기 때문이다. 
- 속성에 처음 접근할 때 초기화 된다. -> 멀티 쓰레드에서 동시에 접근하더라도 한번만 초기화 된다. 
- `lazy` 속성을 지니지만  `lazy` 키워드를 붙이지 않아도 된다.

연산 타입 프로퍼티의 특징
- 클래스에서 `static`대신 `class` 키워드를 사용하는 경우에만, 상속시 재정의가 가능하다.
- 메모리 공간에 할당되지 않는다 -> 연산 타입 프로퍼티에 접근할 때마다 코드를 실행하기 때문에 연산이 복잡하거나 외부 리소스를 포함하게 되면 퍼포먼스 이슈가 생길 수 있다.



**타입 메서드**
- 해당 타입 자체와 관련된 보편적인 동작인 경우 사용한다.
- 클래스에서 `static`대신 `class` 키워드를 사용하는 경우에만, 상속시 재정의가 가능하다.


## 주요 기능

1. 타입 자체와 관련된 프로퍼티와 메서드를 정의할 때 사용한다.  


## 코드 예시

```swift
class Square {
	static let angle: Int = 90
	static let sidesNumber: Int = 4
	static var instanceNumber: Int = 0
	
	static var allAngleSum: Int {
		return 4 * angle
	}
	
	static func showInfo() {
		print("Angle: \(angle)")
		print("Number of sides: \(sidesNumber)")
		print("Numebr of Instances: \(instanceNumber)")
		print("Sum of All angles: \(allAngleSum)")
	}
	
	init() {
		Square.instanceNumber += 1
	}
}

Square()
Square()
print(Square.instanceNumber) // prints 2


class SuperClass {
	class var computedTypeProperty: Int {
		return 42
	}
	
	static var overrideImpossibleComputedTypeProperty: String {
		return "This variable can't not be overridden"
	}
}


class SubClass: SuperClass {
	override static var computedTypeProperty: Int {
		return 0
	}
	
	// override static var overrideImpossibleComputedTypeProperty: String {
	//	return "Error occurs..."
	// }
}

Int.random(1...100)
Double.random(3.14...9.99)


```

## Keywords
+ 타입 프로퍼티 (Type Properties)
+ 타입 메서드 (Type Methods)
+ 생성자 (Initializer)

## References
- [Swift 공식문서 - Properties](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/properties)
- [Swift 공식문서 - Methods](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/methods)
- [Stored vs Computed Property Swift: A Developer’s Guide](https://www.dhiwise.com/post/stored-vs-computed-property-swift-a-developers-guide)