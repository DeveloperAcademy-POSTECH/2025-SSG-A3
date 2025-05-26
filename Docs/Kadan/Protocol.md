
### Protocol 정의

- 프로토콜이란, 어떤 기능에 적합한 특정 메서드, 프로퍼티 및 기타 요구 사항의 청사진을 의미.
- 클래스, 구조체, 열거형에 의해 채택되며, 정의된 요구사항의 실제 구현을 제공한다.
- 요구사항을 모두 충족하는 모든 유형은 해당 프로토콜에 부합한다고 말한다.

💡 쉽게 말해서 “약속” 하는 것으로 이해하려 한다. 근데 어떤 약속?

→ 해당 기능에 꼭 필요한 요구사항을 선언해두는 약속.

ex) 밴드가 있다.

- 기타, 드럽, 피아노, 보컬 필요 → `프로퍼티`
- 모여서 연주를 함 → `메서드`

→ 실제 기타는 누가치고 드럼은 누가치고, 연주는 어떤 걸 하는지 실제로 지정 해 주는 것이 아니라, 이런 프로퍼티는 꼭 필요해요! (이게 없으면 밴드는 안돌아가요) , 이런 메서드는 꼭 필요해요! (밴드는 모여서 연주가 제일 중요함) 라고 꼭 필요한 요구사항을 선언 해두는 것이 `Protocol` 이다.

---


### Protocol 선언

```swift
protocol Band {
	var drum: String   { get set }
	var vocal: String  { get set }
	var piano: String  { get set }
	var guitar: String { get set }
	
	func play()
}

// 밴드라는 프로토콜을 따르기 위해선 드럼 보컬 피아노 기타 프로퍼티가 반드시 정의 되어야 함.
// play 란 메서드 구현도 꼭 필요함.
```

- 프로퍼티를 선언하여 값을 직접 정의하고 메서드를 직접 구현하는게 아님.!
- 이 프로퍼티를 따르려면 이러 이러한 것들이 꼭 필요하다! 라는 약속임.

---

### Protocol 채택

```swift
class AcademyBand: Band {
	var drum:   String = "A"
	var vocal:  String = "B"
	var piano:  String = "C"
	var guitar: String = "D"
	
	func play() {
		print("day6 한 페이지가 될 수 있게")
	}
}
```

- 이런 식으로 프로토콜에서는 필수적인 요구사항의 껍데기만 제공하고, 실제 구현은 채택한 곳에서!!

---

### 필수 외에 있을수도 없을수도 있는?

- 만약 베이스 처럼 구하기 힘든 포지션이 있을 경우에?

```swift
@objc protocol Band { //@objc 선언
	var drum: String   { get set }
	var vocal: String  { get set }
	var piano: String  { get set }
	var guitar: String { get set }
	@objc optional var bass: String { get set } // @objc optional 선언
	
	func play()
}

```

- 이렇게 선언 해주면 채택해주는 곳에서 꼭 선언해 주지 않아도 에러가 나지 않는다.

![[Pasted image 20250526232040.png]]
- @objc 를 붙이고 struct 에 채택하면 오류가 난다. → @objc 란 Objectiv-C에서도 사용될 수 있단 건데, Objective-C에서 프로토콜은 오직 “클래스” 에서만 채택가능하고
![[Pasted image 20250526232023.png]]


→ 클래스 전용일 때 사용하는 AnyObject가 자동으로 채택되기 때문에 클래스만 가능하다.