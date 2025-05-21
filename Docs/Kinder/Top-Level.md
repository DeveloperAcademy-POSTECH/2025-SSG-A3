>[!question]
>GQ1. Top-Level이란 뭐지?
>GQ2. 어떻게 쓰이고 뭐할때 쓰는거지?

## Description
> **Top-Level**이란 
> Swift 소스 파일에서 최상위 레벨에 위치한 코드로, 함수나 클래스, 구조체 내부가 아닌 파일의 **최상위**에서 직접 **실행되는 코드**

## 종류
### 1. **Top-Level 선언**
- 변수, 상수, 함수, 클래스, 구조체, 열거형 등의 선언을 포함합니다.
- 이러한 선언은 해당 파일뿐만 아니라 동일 모듈의 다른 파일에서도 접근할 수 있습니다.
```swift
// Top-Level 변수 선언
let globalVariable = "Hello, Swift!"
  

// Top-Level 함수 선언
func greet() {
    print(globalVariable)

}
```

### 2. **실행 가능한 Top-Level Code**
- 선언 외에도 실제로 실행 가능한 명령문과 표현식이 포함된 코드입니다.
- 예를 들어, 조건문, 반복문, 함수 호출 등이 여기에 해당합니다.
- 이 코드는 Swift 프로그램의 진입점으로 사용됩니다.
```swift
// Top-Level 실행 가능한 코드
print("Hello, World!")
```

## 동작 원리
- Swift는 기본적으로 **Top-Level Code**를 포함하는 파일에서 진입점을 자동으로 설정하지 않습니다.
- 하지만 @main 또는 @UIApplicationMain 속성을 사용하여 특정 타입을 Top-Level 진입점으로 지정할 수 있습니다.


## 그럼 언제 쓰이지?
|           | 사용 방법                                                        |
| --------- | ------------------------------------------------------------ |
| Top-Level | 스크립트나 단일 파일로 구성된 프로젝트에 사용(알고리즘 풀때 등)                         |
| @main     | 큰 프로젝트(App 등)는 Top-Level 코드 최소화<br>@main을 통해 명시적으로 진입점 지정하기! |


## 결론
> **Top-Level Code**는 Swift 파일의 최상위에서 실행되는 모든 코드!!
> 컴파일러가 Top-Level 코드는 main() 함수로 감싸줌



> Entry Point (진입점)는 프로그램이 시작하는 지점을 지정하며,
>  기본적으로 @main이나 @UIApplicationMain으로 지정할 수 있습니다.
>  
> Top-Level Code는 Entry Point로 사용될 수 있지만, 
> Entry Point가 반드시 Top-Level Code로 구성될 필요는 없습니다.

## References
- 참고한 레퍼런스를 작성 (예 : Apple의 공식 문서)