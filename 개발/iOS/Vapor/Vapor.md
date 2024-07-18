# Vapor

Swift 로 작성할 수 있는 web framework. Swift 로 웹 앱 API, HTTP 서버를 구현할 수 있음.

## 설치

자세한 설치 방법은 [Vapor Docs](https://docs.vapor.codes) 참고.

* Swift 5.6 이상 버전 요구
* Vapor Toolbox : 유용한 기능을 포함하는 CLI 도구

## 프로젝트 생성

```
vapor new hello -n
```

* `-n` : 모든 질문에 no 로 답한 템플릿 버전을 생성

<img width="822" alt="image" src="https://github.com/user-attachments/assets/2ed442c4-c5e8-4f40-8670-b9713c3c7628">

<img width="486" alt="image" src="https://github.com/user-attachments/assets/190de5e6-2f29-47b5-aebc-9baf4a93e054">

## Build & Run

Xcode / Linux 로 열 수 있는데 Xcode 로 열기

```
open Package.swift
```

### package 'hello' is using Swift tools version 5.10.0 but the installed version is ~

<img width="247" alt="image" src="https://github.com/user-attachments/assets/fd17a98c-e713-4601-8144-f69e3a849bfa">

위 에러가 뜨면 `// swift-tools-version:` 뒤에 오는 버전을 맞는 버전으로 정정 후 package.swift 파일 껐다 다시 열기

```swift
// swift-tools-version:5.9
```

### Failed to resolve dependencies Dependencies could not be resolved ~

그래도 여전히 위 에러가 뜨면 Xcode 메뉴 File > Packages > Reset Package Caches 를 누르면 에러 사라지며 Xcode 가 자동으로 가능한 scheme 목록을 보여줌.

<img width="170" alt="image" src="https://github.com/user-attachments/assets/d4638f95-6495-4567-91db-1aa408aef000">

### Run

My Mac 을 scheme 으로 선택한 후 실행 버튼 누름







