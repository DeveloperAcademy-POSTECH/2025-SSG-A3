- 네트워크 데이터 전송 작업 그룹을 조정하는 객체 (애플에서 제공하는 네트워크 통신 관련 API)
- URLSession 인스턴스는 브라우저의 탭과 유사

### URLSessionConfiguration
- URLSession 인스턴스 관련 다양한 설정 (타임아웃 / 네크워크 / 쿠키 / 보안)
#### shared
- URLSession 의 싱글톤 객체 `URLSession.shared`
- 간단한 네트워크 작업에 적합하지만, 사용자화 할 수 없음
- 한계점
	- 서버로부터 점진적으로 데이터를 받을 수 없다
	- 인증 관련 작업이 제한된다
	- 백그라운드에서의 다운로드 및 업로드 작업을 수행할 수 없다.

#### default
- 일반적인 네트워크 작업에 적합하다
- 디스크 저장 / 키체인에 인증 정보 저장 / 쿠기 저장
- `let session = URLSession(configuration: .default)`

#### ephemeral
- 브라우저에서 시크릿 탭과 유사
- <s>디스크 저장 / 키체인에 인증 정보 저장 / 쿠기 저장</s> => 파일 다운로드 할 때만 디스크에 저장함
- `let session = URLSession(configuration: .ephemeral)`

#### background 
- 앱이 종료되어도 백그라운드에서 다운로드 및 업로드 작업을 수행할 수 있다.
- 정확히는 타입 메서드(Type Method)이다.
- `let config = URLSessionConfiguration.background(withIdentifier: "com.example.bg")

**let** session = URLSession(configuration: config)`

| 종류           | 설명                         | 캐시/쿠키 | 사용 예시         |
| ------------ | -------------------------- | ----- | ------------- |
| `shared`     | 시스템이 제공하는 전역 세션            | 있음    | 아주 간단한 요청     |
| `default`    | 사용자 설정 가능, 가장 일반적          | 있음    | 대부분의 API 요청   |
| `ephemeral`  | 비휘발성 저장 안 함 (쿠키, 캐시, 인증 등) | 없음    | 개인정보 보호 중요 시  |
| `background` | 백그라운드에서도 작업 가능 (시스템에 위임)   | 있음    | 큰 파일 다운로드/업로드 |

### URLSessionTask
- URL Session 에서 수행되는 네트워크 관련 특정 작업
- 다음과 같은 상태 `state` 를 가질 수 있다.
	- `running` / `suspended` / `canceling` / `completed`
- 다음과 같은 메서드를 갖는다 ➡️ `resume()` / `cancel()` / `suspend()` 

| 종류                        | 설명                                   | 사용 예시                         |
| ------------------------- | ------------------------------------ | ----------------------------- |
| `URLSessionDataTask`      | 서버에서 JSON, 텍스트 등 **작은 데이터 받기**       | API 통신 (RESTful)              |
| `URLSessionDownloadTask`  | 파일을 다운로드해서 **디스크에 저장**               | 이미지, PDF, ZIP 등               |
| `URLSessionUploadTask`    | 서버에 **파일 업로드**                       | 파일 전송, 이미지 업로드                |
| `URLSessionStreamTask`    | TCP/IP 스트리밍 연결 (Input/Output Stream) | 실시간 데이터 송수신                   |
| `URLSessionWebSocketTask` | WebSocket 기반의 양방향 통신 (메시지 단위)        | 채팅, 실시간 알림, 게임 서버 통신, 주식 시세 등 |

##### `URLSessionStreamTask` vs. `URLSessionWebSocketTask`
> `URLSessionStreamTask` 과 `URLSessionWebSocketTask` 의 차이점은 무엇일까?

| 항목       | `URLSessionStreamTask`                     | `URLSessionWebSocketTask`       |
| -------- | ------------------------------------------ | ------------------------------- |
| 프로토콜     | TCP (저수준)                                  | WebSocket (고수준, HTTP 업그레이드 기반)  |
| 용도       | 저수준 스트림 제어 (직접 바이트 송수신)                    | 고수준 메시지 기반 실시간 통신               |
| 데이터 단위   | `InputStream` / `OutputStream`으로 직접 바이트 처리 | `String` 또는 `Data` 메시지 단위로 주고받음 |
| 핸드셰이크/보안 | 직접 제어해야 함 (보통 TLS 커스터마이징 필요)               | 자동 핸드셰이크 (`wss://` 시 TLS 포함)    |
| 복잡도      | 더 복잡함, 직접 프로토콜 구현 필요                       | 단순함 (Apple이 WebSocket 프로토콜 관리)  |
| 실제 사용 예시 | FTP, SMTP, 직접 만든 프로토콜                      | 채팅, 알림, 게임 통신 등 실시간 서비스         |
| 도입 가능 버전 | iOS 9.0+                                   | iOS 13.0+                       |


## Reference
- [URLSession | Apple Developer Documentation](https://developer.apple.com/documentation/foundation/urlsession)
- [iOS URLSession을 알아야할까?](https://d0ngurrrrrrr.tistory.com/159)