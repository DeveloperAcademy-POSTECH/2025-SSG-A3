
Swift는 구조화된 방식으로 비동기(asynchronous)와 병렬(parallel)코드 작성을 지원한다. 이를 통해 앱은 동시에 여러 작업을 효율적으로 수행할 수 있다.

### 비동기 코드 (Asynchronous code)

- 일시적으로 중단되었다가 다시 실행할 수 있는 코드
- 실행 중인 코드가 중단되면, 그 사이에 다른 작업이 진행 될 수 있다.
- 예를 들어, UI 업데이트와 같은 짧은 작업과 네트워크에서 데이터를 가져오거나 파일을 파싱하는 오래 걸리는 작업을 동시에 진행할 수 있다.

### 병렬 코드 (Parallel code)

- 여러 작업이 동시에 실행 되는 코드
- 예를 들어 4코어 프로세서가 장착된 컴퓨터는 각 코어가 하나의 작업을 수행하면서 동시에 4개의 코드를 실행할 수 있다.

<aside> 💡

**Swift의 Concurrency(동시성)은 비동기 + 병렬의 조합**

</aside>

### Concurrency의 주의점

- 어떤 코드가 어떤 순서로 실행될지 예측할 수 없다.
- 여러 코드가 동시에 같은 변수에 접근하면 데이터 경쟁(data race)이 발생할 수 있다.

### Swift의 대응

- 대부분 데이터 경쟁은 컴파일 타입에 감지되어 에러로 처리된다.
- 런타임에서 감지되는 경우, 프로그램이 중단될 수 있다.
- `actor`와 격리(isolation)를 통해 안전한 상태를 다룰 수 있다.

### 기존 클로저 기반의 비동기 호출 방식

이전에는 동시성 코드를 작성하기 위해서 클로저 기반의 비동기 호출을 사용했다.

```swift
listPhotos(inGallery: "Summer Vacation") { photoNames in
  let sortedNames = photoNames.sorted()
  let name = sortedNames[0]
  downloadPhoto(named: name) { photo in
    show(photo)
  }
}
```

위 코드는 사진 리스트들 중 첫 번째 사진을 다운로드하고 보여준다.

- 클로저가 중첩되면서 가독성이 떨어지고 코드 흐름을 이해하기 어려워짐
- 복잡한 작업이 많아질수록 빠르게 코드를 파악하기 여러운 콜백 지옥 문제가 발생할 수 있다.

## 비동기 함수 정의와 호출 (Defining and Calling Asynchronous Functions)

비동기 함수(asynchronous function) 또는 비동기 메서드 (asynchronous method)는 실행 도중에 일시 중단될 수 있는 특별한 종류의 함수 또는 메서드이다.

```swift
func listPhotos(inGallery name: String) async -> [String] {
  let result = // ... some asynchronous networking code ...
  return result
}
```

- `async` 키워드를 사용해 해당 함수가 비동기 함수라는 것을 나타낼 수 있다.

비동기 함수를 호출시 해당 작업이 완료될 때까지 일시 중단되는데, 이때 가능한 일시 중단 지점을 표시하기 위해 호출할때 `await` 키워드를 사용한다. 이는 가능한 모든 중단 지점이 `await`로 표시된다는 것을 의미한다.

```swift
let photoNames = await listPhotos(inGallery: "Summer Vacation")
let sortedNames = photoNames.sorted()
let name = sortedNames[0]
let photo = await downloadPhoto(named: name)
show(photo)
```

1. `listPhotos(inGallery:)` 함수를 호출 → `await`로 인해 실행 중단
2. 그 사이 다른 동시 코드가 실행될 수 있음
3. 해당 함수가 완료되면, `photoNames`에 반환값을 할당
4. `sortedNames`와 `name`은 일반 동기 코드로 즉시 실행
5. `downloadPhoto(named:)` 호출 → `await`로 다시 중단
6. 완료되면 결과를 `photo`에 결과 할당→ `show(photo)` 호출

### `await` 사용 가능한 위치

`await`키워드는 코드의 실행을 일시 중단할 수 있어야 하므로 프로그램의 특정 위치에서만 호출할 수 있다.

- 비동기 함수, 메서드 또는 프로퍼티(`async` 키워드가 붙은)의 body(클로저 내부)
- `@main`으로 표시된 구조체, 클래스, 열거형의 `static main()` 메서드
- 비구조화된 Task 내부

## 비동기 시퀀스 (Asynchronous Sequences)

이전 메서드의 경우 전체 배열이 모두 준비된 뒤에 결과를 한번에 반환했지만, 비동기 시퀀스를 사용하면 데이터를 하나씩 순차적으로 받아올 수 있다.

```swift
import Foundation

let handle = FileHandle.standardInput
for try await line in handle.bytes.lines {
  print(line)
}
```

- for 루프에 `await` 키워드를 추가하면 된다.
- 한줄(`line`)의 데이터가 도착할 때마다 루프가 한 번 실행되고,
- 다음 `line`이 준비될 때 까지 실행은 일시 중단된다.

<aside> 💡

`for-in` 루프는 `Sequence`프로토콜을 준수해야 하는 것 처럼, `for-await-in` 루프는 `AsyncSequence`프로콜을 준수해야 한다.

</aside>

## 비동기 함수 병렬 호출 (Calling Asynchronous Functions in Parallel)

`await`로 비동기 함수를 호출하면 한 번에 하나의 코드만 실행된다. 그래서 비동기 코드가 실행되는 동안 해당 코드의 실행이 끝나고 나서 다음 비동기 코드가 실행된다.

### 순차적 실행

```swift
let firstPhoto = await downloadPhoto(named: photoNames[0])
let secondPhoto = await downloadPhoto(named: photoNames[1])
let thirdPhoto = await downloadPhoto(named: photoNames[2])

let photos = [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

- 예를 들어 `downloadPhoto` 메서드르 이용해서 3개의 이미지를 받고 싶다면 위와 같이 작성할 수 있다.
- 이러면 다운로드가 순차적으로 진행되어 많은 시간이 걸릴 수 있다.

### 병렬 실행

이때 `async let` 키워드를 사용하면 이전 호출이 완료될 때까지 기다리지 않고 한번에 함수들을 병렬적으로 동시에 실행할 수있다.

```swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])

let photos = await [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

- 이 경우 이전 비동기 작업이 완료될때 까지 기다리지 않고, `downloadPhoto(named:)` 에 대한 세 가지 호출이 같은 시점에 시작된다.
- `downloadPhoto(named:)` 메서드에 대해서 기다리지 않기 때문에, `await` 키워드를 작성할 필요가 없다.
- 다만, 해당 결과를 통해 구현되어야 할 `photos`의 경우 세장의 사진 모두 다운로드가 완료될때 까지 실행을 멈춰야 하기 때문에 `await` 키워드를 작성해야 한다.

await

- 다음 코드가 결과를 필요로 할 때 사용
- 순차적으로 작업 수행

async let

- 결과를 나중에 사용할 때
- 병렬로 작업 수행 가능

## Task and Task Groups (작업과 작업 그룹)

Task

- 비동기 코드를 실행할 수 있는 최소 단위
- 모든 비동기 코드는 Task 내부에서 실행된다.
- 한 Task는 한 번에 하나의 작업만 수행하지만, 여러 Task가 시스템 상황에 따라 동시에 실행할 수 있다.

TaskGroup

- 여러 Task를 그룹으로 묶어 동시에 실행할 수 있는 구조
- Task 간 부모-자식 관계를 명확히 정의하여 실행 흐름 제어 가능
- Swift의 구조화된 동시성(Structured Concurrency)을 구현하는 핵심 기능

### 구조화된 동시성

구조화된 동시성은 비동기 작업 간의 명확한 부모-자식 관계를 정의하여 작업 흐름을 체계적으로 관리하는 방식이다.

Swift에서는 각 비동기 작업이 특정 부모 작업에 소속되도록 구조화 함으로써 일관되게 처리할 수 있다.

### 구조화된 동시성의 장점

- 부모 작업이 자식 작업의 완료를 반드시 기다림 → 누락된 비동기 호출 방지
- 자식 작업의 우선순위가 상승하면 부모 작업의 우선순위로 자동 상승 → 스케줄링 최적화
- 부모 작업이 취소되면 자식 작업도 함께 자동 취소 → 자원 누수 방지
- 작업 로컬 값이 자식 작업에 자동 전파됨 → 안전한 상태 공유

```swift
await withTaskGroup(of: Data.self) { group in
  // 1. 비동기로 사진 목록을 가져옴
  let photoNames = await listPhotos(inGallery: "Summer Vacation")
  
  // 2. 각 사진 이름에 대해 다운로드 작업을 그룹에 추가
  for name in photoNames {
    group.addTask {
      // 3. 비동기 다운로드 실행
      return await downloadPhoto(named: name)
    }
  }
  
  // 4. 완료된 작업부터 순차적으로 결과 처리
  for await photo in group {
    show(photo)
  }
}
```

- `withTaskGroup`으로 작업 그룹 생성
- `addTask`를 통해 각 다운로드 작업을 자식 Task로 등록
- 오류가 발생할 수 있는 경우에는 `withThrowingTaskGroup`메서드를 사용할 수 있다.

## Task Cancellation (작업 취소)

Swift 동시성은 협동 취소 모델(cooperative cancellation model)을 사용한다. 이는 작업이 실행 도중 스스로 적절한 시점에 취소되었는지 확인하고, 그에 맞게 처리를 한다.

### 작업이 취소되었을 때 처리 방식

- `CancellationError`를 던져 작업 중단
- `nil` 또는 빈 컬렉션 반환
- 부분적으로 완료된 결과만 반환 후 중단

### 취소 여부를 확인하는 방법

사용자가 모든 작업이 완료될 때까지 기다리지 않고 작업을 중지할 수 있도록 하려면 작업이 취소되었는지 확인하고 취소된 경우 실행을 중지해야 한다.

- `Task.checkCancellation()`: 작업이 취소된 경우 에러를 던짐
- `Task.isCanelled`: 프로퍼티를 확인해 `Bool`값으로 취소 여부 판단

`checkCancellation()`은 간단하게 구현할 수 있는 장점이 있으며, 작업이 에러를 던져 중단되도록 한다. 반면 `isCancelled`는 좀 더 유연하게 정리작업을 함께 수행할 수 있는 장점이 있다. 예를 들어 네크워크 연결을 닫거나 임시 파일을 삭제할 수 있다.

```swift
let photos = await withTaskGroup { group in
  let photoNames = await listPhotos(inGallery: "Summer Vacation")
  
  for name in photoNames {
    // 그룹이 이미 취소되었으면 작업을 추가하지 않음
    let added = group.addTaskUnlessCancelled {
      // 내부 작업에서도 취소 여부 확인 후 실행
      Task.isCancelled ? nil : await downloadPhoto(named: name)
    }
    guard added else { break } // 추가에 실패하면 루프 종료
  }
  
  var results: [Data] = []
  for await photo in group {
    if let photo {
      results.append(photo) // nil은 건너뛰고 결과만 수집
    }
  }
  
  return results
}
```

- 각 작업은 `addTaskUnlessCancelled`메서드를 통해 등록된다.
- 이 메서드는 그룹이 이미 취소된 상태라면 작업을 추가하지 않는다.
- 작업 추가 후 `added` 값이 `false`이면 작업이 추가되지 않은 것으로 판단하고 루프를 종료한다.
- 각 작업은 실행 전 `Task.isCancelled`을 확인해 취소된 경우 `nil`을 반환하고 실행을 생략한다.
- 결과 수집시 `nil` 값을 건너뛰고 완료된 작업들만 결과로 남긴다.
- 이를 통해 취소되기 전까지 완료된 일부 결과만 반환할 수 있다.

또한 외부에서 특정 Task 인스턴스의 취소 여부를 확인하려면 `Task.isCancelled`의 인스턴스 속성을 사용해야한다.

```swift
let task = Task {
  return await someAsyncOperation()
}

task.cancel() // 외부에서 작업 취소

if task.isCancelled {
  print("작업이 외부에서 취소되었습니다.")
}
```

즉시 취소 이벤트에 대응하기 위해선 `Task.withTaskCancellationHandler` 메서드를 사용할 수도 있다. 이 메서드는 작업이 취소되는 즉시 `onCancel`을 실행할 수 있게 해준다.

```swift
let task = await Task.withTaskCancellationHandler {
  // 취소되지 않았을 때 실행되는 비동기 작업
  try await someAsyncOperation()
} onCancel: {
  // 작업이 취소된 경우 즉시 실행되는 로직
  print("사용자가 작업을 취소했습니다.")
}
```

## Unstructured Concurrency (비구조화 동시성)

Swift는 구조화된 동시성 외에도 비구조화된 동시성(unstructured concurrency)을 지원한다. 비구조화된 작업은 TaskGroup과 같은 상위 작업에 속하지 않으며, 독립적으로 생성되고 실행된다. 따라서 구조화된 작업과 달리 부모 작업이 존재하지 않아 개발자가 직접 책임져야한다.

### 비구조화된 작업을 만드는 방법

이러한 구조화되지 않은 작업을 만들기 위해서는 `Task.init` 혹은 `Task.detached`를 사용하여 구현할 수 있다. 비구조화된 작업은 개발자가 직접 작업을 생성하고, 추적하고, 결과를 수집하거나 취소할 수 있는 핸들을 관리해야 한다.

```swift
let newPhoto = // ... 새로운 사진 데이터
let handle = Task {
  // 비구조화된 작업에서 갤러리에 사진 추가
  return await add(newPhoto, toGalleryNamed: "Spring Adventures")
}

let result = await handle.value // 작업 결과를 기다림
```

위 코드는 `Task`를 통해 비동기 작업을 시작하고, `handle.value`를 통해 결과를 기다린다. 작업이 완료되기 전까지 `await`는 일시 중단되며, 결과가 준비되면 다시 이어서 실행된다.

## **격리(Isolation)**

동시성 환경에서 여러 작업이 동시에 같은 데이터를 수정하면 데이터 경쟁(data race)이 발생할 수 있다. Swift는 이러한 문제를 방지하기 위해 데이터 격리(data isolation)를 제공해 데이터를 읽거나 수정할 때 다른 작업이 동시에 해당 데이터에 접근하지 못하도록 자동으로 보호해준다.

데이터 경쟁 (Data race)

- 여러 작업이 동기화 없이 같은 변수나 메모리에 접근하고, 그중 하나 이상이 값을 변경하려고 하는 상황
- 실행 순서에 따라 결과가 달라지며 예상치 못한 값이 저장될 수 있다.

데이터 격리 (Data isolation)

- 동시에 하나의 코드만 특정 데이터에 접근하도록 제한해주는 보장
- 데이터를 읽거나 수정할때 다른 작업이 해당 데이터에 접근하지 못하게 자동으로 보호해준다.

### 데이터 격리의 세가지 방법

**1. 불변 데이터(Immutable data)**

- `let`으로 선언된 상수는 **수정이 불가능하므로**, 동시 수정의 위험이 전혀 없다.
- 값이 고정되어 있기 때문에 동시에 접근해도 충돌이 발생하지 않음

**2. 현재 Task만 참조하는 데이터(Local task-exclusive data)**

- 지역 변수처럼 현재 작업에서만 참조하는 값은 외부에서 접근할 수 없어 안전하다.
- 만약 해당 데이터를 클로저에서 캡처하더라도, 해당 클로저가 동시에 여러 번 사용되지 않도록 Swift 자체에서 보장한다.

**3. Actor로 보호되는 데이터**

- `actor` 내부 데이터는 해당 `actor`에 속한 코드에서만 접근 가능
- 다른 작업이 접근하려면 순서를 기다려야 하므로 동시 접근이 불가능하다.

## **MainActor**

actor는 하나의 작업만이 내부 데이터를 읽고 쓸수 있도록 보장함으로써, 변경 가능한 데이터를 안전하게 보호하는 타입이다. 앱에서 가장 중요한 actor는 바로 MainActor이며, 이는 UI를 구성하고 표시하는데 사용되는 모든 데이터를 보호한다.

MainActor는 UI를 렌더링하고, UI 이벤트를 처리하며, UI 상태를 읽거나 갱신해야 하는 코드들을 실행한다.

동시성을 사용하기 전에는 모든 코드가 기본적으로 MainActor에서 실행된다. 장시간 실행되거나 리소스를 많이 사용하는 작업을 식별하여, 해당 작업들을 안전하게 MainActor 외부로 분리할 수 있다.

예를들어 사용자가 이미지를 다운로드하고 화면에 표시한다고 하자. 이미지를 다운로드하는 작업은 시간이 오래 걸리므로, MainActor외부에서 실행하는 것이 좋고, 다운로드가 완료된 후에 이미지를 화면에 표시하는 작업은 UI 업데이트이므로 MainActor에서 실행해야 한다.

```swift
func downloadAndShowPhoto(named name: String) async {
  // 이미지 다운로드: 시간이 오래 걸리는 작업 → 백그라운드에서 실행
  let photo = await downloadPhoto(named: name)
  
  // UI 업데이트: 다운로드한 이미지 화면에 표시 → MainActor에서 실행
  await show(photo)
}
```

### MainActor VS MainThread

MainActor는 MainThread와 밀접한 관련이 있지만 같은 것은 아니다. MainActor는 가변 상태를 가지고 있고, MainThread는 해당 상태에 대한 접근을 직렬화한다. MainActor에서 코드를 실행하면 Swift는 해당 코드를 MainThread에서 실행한다. 코드는 MainActor와 상호작용하며, MainThread는 하위 수준 구현 세부사항입니다.

- MainActor: UI 관련 작업을 관리하고 순서를 정하는 "관리자" (상위 수준 개념)
- MainThread: 실제 작업을 수행하는 "작업자" (하위 수준 구현)

## MainActor 사용 방법

### 함수에 @MainActor 적용

```swift
@MainActor
func show(_: Data) {
  // ... UI code to display the photo ...
}
```

- `@MainActor`를 함수 선언 앞에 작성한다.
- 위 함수는 MainActor에서만 실행된다.
- 만약 이 함수를 MainActor외부에서 호출하려면 `await` 키워드를 붙여야 한다.

```swift
func downloadAndShowPhoto(named name: String) async {
  let photo = await downloadPhoto(named: name)
  await show(photo)
}
```

- `downloadPhoto(named:)`는 일반 비동기 함수로 MainActor외부에서 실행된다.
- `show(photo)`는 `@MainActor`로 선언되어 있기 때문에, MainActor로 전환해 UI 코드를 안전하게 실행해준다.

### **클로저에서 @MainActor 사용**

```swift
let photo = await downloadPhoto(named: "Trees at Sunrise")
Task { @MainActor in
    show(photo)
}
```

- `@MainActor` 를 캡처 목록 앞, `in` 앞에 작성한다.
- `Task { @MainActor in ... }` 형태로 클로저 전체를 MainActor에서 실행 가능.

### **타입에 @MainActor 적용하기**

```swift
@MainActor
struct PhotoGallery {
    var photoNames: [String]
    func drawUI() { /* UI 그리기 */ }
}
```

- 타입 선언에 `@MainActor`를 작성한다.
- 해당 타입의 모든 프로퍼티와 메서드는 MainActor에서 실행된다.

### 프로토콜을 통한 암시적 적용

```swift
@MainActor
protocol View { /* ... */ }

// Implicitly @MainActor
struct PhotoGalleryView: View { /* ... */ }
```

- `@MainActor`가 선언된 프로토콜을 채택하면, 채택한 타입들도 암시적으로 `@MainActor`로 표시된다.

![스크린샷 2025-07-06 21.43.59.png](attachment:5176366f-1f0c-4dc5-8b3f-631f6865c39c:%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-07-06_21.43.59.png)

### **일부 멤버만 @MainActor 적용**

```swift
struct PhotoGallery {
    @MainActor var photoNames: [String]
    var hasCachedPhotos = false

    @MainActor func drawUI() { /* UI 그리기 */ }
    func cachePhotos() { /* 백그라운드 캐시 */ }
}
```

- 타입 전체가 아닌, 일부 메서드나 프로퍼티만 MainActor에서 실행되도록 지정할 수도 있다.
- `photoNames`는 UI에 영향을 주는 상태이므로 MainActor 격리
- `drawUI()`는 직접 UI를 렌더링하므로 MainActor에서 실행해야 함
- `hasCachedPhotos`, `cachePhotos()`는 UI와 무관하므로 일반 context에서 실행 가능

---

## **propertyWrapper와 MainActor**

- SwiftUI의 `@State`, `@Published` 같은 property wrapper는 내부적으로 `@MainActor`로 격리된 경우가 많다.

![스크린샷 2025-07-06 21.46.28.png](attachment:6afb554b-327f-44ab-b25d-fe49ce14ed9a:%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2025-07-06_21.46.28.png)

---

## **Actor**

Swift는 MainActor처럼 기본 제공하는 actor 외에도, 직접 actor를 정의할 수 있다.

actor를 사용하면 여러 개의 concurrent 코드에서 안전하게 정보를 공유할 수 있다.

### **actor의 특징**

- actor는 참조 타입(reference type)이다.
- 하지만 클래스와 달리, actor는 동시에 하나의 작업만 내부의 변경 가능한 상태에 접근할 수 있도록 제한한다.
- 이런 제한 덕분에, 여러 task가 같은 actor 인스턴스를 사용해도 데이터 경쟁 없이 안전하게 처리할 수 있다.

```swift
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int

    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}
```

- `actor` 키워드로 선언하며, 클래스나 구조체처럼 중괄호 안에 정의함
- 외부에서 읽을 수 있지만, 내부에서만 수정 가능한 `max` 프로퍼티처럼 접근 제어도 가능함

### **actor 인스턴스 생성 및 외부 접근**

```swift
let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
print(await logger.max) // "25"
```

- `actor`는 의 인스턴스를 생성할 때는 클래스나 구조체처럼 초기화 구문을 사용 할 수 있다.
- 외부에서 `actor`의 프로퍼티나 메서드에 접근할 때는 `await`를 붙여야 함
- `actor`는 내부 상태를 하나의 작업만 접근 가능하게 하므로, 다른 작업이 이미 실행 중이라면 대기해야 하기 때문이다.

### **actor 내부에서는 await 없이 접근**

```swift
extension TemperatureLogger {
    func update(with measurement: Int) {
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}
```

- actor 내부에서 정의된 메서드는 이미 해당 actor에 격리되어 있으므로 `await` 없이 상태에 접근 가능
- `update()`는 내부에서 `measurements`, `max`에 직접 접근하고 있음

---

## **Sendable Types**

Swift에서 `Task`와 `actor`는 프로그램을 여러 개의 **동시성 도메인(concurrency domain)**으로 나눌 수 있게 해준다.

이러한 동시성 도메인은 각각 **변경 가능한 상태(mutable state)**를 포함할 수 있으며, 외부 도메인에서 동시에 접근할 경우 충돌이나 예기치 않은 동작이 발생할 수 있다.

이처럼 동시성 도메인 간에 데이터를 안전하게 주고받기 위해서는, 해당 데이터가 **전달 가능한(Sendable)** 타입이어야 한다.

### **Concurrency Domain**

- `Task`나 `actor` 인스턴스처럼 **서로 격리된 실행 단위**를 의미한다.
    
- 각 도메인은 **고유의 변경 가능한 상태(예: 변수, 프로퍼티 등)**를 가지며,
    
    다른 도메인에서는 해당 상태에 직접 접근할 수 없다.
    

---

### **Sendable**

- 하나의 동시성 도메인에서 **다른 도메인으로 안전하게 전달할 수 있는 타입**을 의미한다.
- 예를 들어, actor의 메서드에 인자로 전달되거나, task의 결과로 반환되는 값이 이에 해당된다.

```swift
struct TemperatureReading: Sendable {
    var measurement: Int
}

```

- `Sendable` 프로토콜은 구현할 코드 요구사항은 없지만,
    
    Swift는 컴파일 타임에 해당 타입이 안전한지 검사한다 (모든 프로퍼티가 Sendable인지 등).
    

---

### **Sendable 타입 조건**

1. **값 타입**이며, 내부의 **모든 저장 프로퍼티가 Sendable**인 경우
    - 예: `struct`나 `enum`이 모든 속성을 Sendable 타입으로 가질 때
2. **읽기 전용 상태만 가지며**, 그 상태가 모두 Sendable인 경우
    - 예: 불변 프로퍼티만 가지는 `struct` 또는 `class`
3. 내부적으로 **접근을 직렬화하는 구조를 가진 경우**
    - 예: `@MainActor`가 적용된 클래스, 특정 큐에서만 접근하도록 설계된 타입 등

---

### **암시적 Sendable**

아래와 같은 경우, `Sendable`을 명시적으로 선언하지 않아도 **자동으로 적용된다**:

```swift
struct TemperatureReading {
    var measurement: Int
}

```

- 해당 구조체가 `public`이나 `@usableFromInline`이 아니고,
    
- 내부 프로퍼티가 모두 `Sendable`인 경우에는
    
    Swift가 해당 타입을 **암시적으로 Sendable로 간주**한다.
    

---

### **명시적으로 Sendable을 막는 방법**

```swift
struct FileDescriptor: ~Sendable {
    let rawValue: Int
}
```

- `~Sendable`을 사용하면 해당 타입이 **명시적으로 Sendable이 아님**을 선언할 수 있다.
- 이 방법은 기본적으로 적용될 수 있는 `Sendable` 자동 채택을 억제하고자 할 때 사용한다.

---

### **예제 전체 흐름 요약**

```swift
struct TemperatureReading: Sendable {
    var measurement: Int
}

extension TemperatureLogger {
    func addReading(from reading: TemperatureReading) {
        measurements.append(reading.measurement)
    }
}

let logger = TemperatureLogger(label: "Tea kettle", measurement: 85)
let reading = TemperatureReading(measurement: 45)
await logger.addReading(from: reading)

```

- `TemperatureReading`은 Sendable 타입이기 때문에,
- `TemperatureLogger` actor의 메서드에 안전하게 전달될 수 있다.