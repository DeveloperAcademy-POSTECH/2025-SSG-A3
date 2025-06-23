>[!question]
>GQ1. @Binding은 언제 쓰일까?
>GQ2. 주의해야할 점은 무엇이 잇을까?

## Description
![[Pasted image 20250623234701.png]]
> 다른 View로 부터 property를 받을 때 사용 됨
> 이 데이터도 변경시 View가 Refresh render


## 주요 기능 및 특징
+ 다른 View로 부터 property들을 받을 때에 사용 됨
- @Binding을 받은 View는 외부 소스(부모 뷰 등)로 부터 생성된 property 변경 사항에 대하여 fresh render 됨
- 이 Property는 수정 역시 가능함
- binding된 값을 수정하면 제공한 뷰(부모 뷰 등)의 property도 같이 업데이트 됨
- @State를 넘길때는 **$를 앞에 붙여줘야함**
- view가 fresh render할 때 @Binding은 유지 되지 않음.
- 외부에서부터 전달되기 때문에 굳이 유지할 필요가 없기 때문

## 코드 예시
```swift
struct PlayerView: View {
     var episode: Episode
     @State private var isPlaying: Bool = false

     var body: some View {
         VStack {
             Text(episode.title)
                 .foregroundStyle(isPlaying ? .primary : .secondary)
             PlayButton(isPlaying: $isPlaying) // Pass a binding.
         }
     }
 }

struct PlayButton: View {
     @Binding var isPlaying: Bool

     var body: some View {
         Button(isPlaying ? "Pause" : "Play") {
             isPlaying.toggle()
         }
     }
 }
```

## Keywords
+ [[SwiftUI Property Wrapper]]

## References
- [공식 문서](https://developer.apple.com/documentation/swiftui/binding)