>[!question]
>GQ1. State는 언제 쓰일까?
>GQ2. State를 쓸때 어떤 부분을 조심해야할까?

## Description
![[Pasted image 20250623234647.png]]

> SwiftUI 단일 View에서 사용할 데이터
> 선언 후 데이터가 변경시 View가 refresh render됨


## 주요 기능과 특징
- @State는 View 내부에 속해야 할 때만 사용
-  @State는 View 내부에서 초기화 해야하고, 다른 객체로 부터 State를 받는 것은 권장하지 않음
    - 받을 수는 있지만 유지되지 않기 때문에 받을 이유가 없음
    - 그러한 이유로 한 **Struct에서만 유지되기 때문에 `private`으로 선언을 권장**
- SwiftUI에서는 @State Property Wrapper를 내부적으로 저장하고, 
  @State의 변경사항에 대하여 **view는 fresh render** 됨(저장된 value는 fresh render 하는 동안에도 유지 됨)
- 데이터를 전달할때는 `Binding<T>`로 @State property를 자식 view에게 전달 가능(이 경우 자식 뷰에서도 수정이 가능)

## 코드 예시
```swift
struct PlayButton: View {
     @State private var isPlaying: Bool = false

     var body: some View {
         Button(isPlaying ? "Pause" : "Play") {
             isPlaying.toggle()
         }
     }
 }
```

## Keywords
- [[SwiftUI Property Wrapper]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/state)