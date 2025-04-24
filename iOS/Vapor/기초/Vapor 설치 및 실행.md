# Vapor 설치 및 실행

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

### Run

My Mac 을 scheme 으로 선택한 후 실행 버튼 누름

<img width="502" alt="image" src="https://github.com/user-attachments/assets/97669b54-f185-4da4-bcdc-1b7228c3e8d5">

콘솔에 뜨는 로그 확인

## Localhost

아래의 두 local 주소로 실행 여부 확인

* `localhost:8080/hello`
* `http://127.0.0.1:8080`
