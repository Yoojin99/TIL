
# CopilotForXcode 사용기

[CopilotForXcode](https://github.com/intitni/CopilotForXcode)

CopilotForXcode 는 Xcode 상에서 copilot 을 쓸 수 있게 해주는 extension.

* 기능
  * 코드 추천 (Copilot, Codeium 기반)
  * 채팅 (ChatGPT 기반)
  * 코드 prompt (ChatGPT 기반)

## 사전 준비

1. Github Copilot 구독
2. Copilot LSP 실행 위한 [Node](https://nodejs.org/en) 설치

![image](https://github.com/Yoojin99/TIL/assets/41438361/9b880115-fb4b-421b-bee4-1d664358821b)

참고로 설치 하면 `/usr/local/bin` 이 `$PATH` 에 포함되어 있는지 확인하라는데 이건 터미널에서 `echo $PATH` 를 해주면 목록에 포함되어 있는지 아닌지 확인할 수 있음.

## 권한

1. 폴더 접근 권한
2. Accessibility API

## 설치

Homebrew 를 통한 설치

```
brew install --cask copilot-for-xcode
```

or [github release](https://github.com/intitni/CopilotForXcode/releases) 에서 다운로드

<img width="140" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/a16d10c2-22bb-4a77-9ea7-b0d4d8791a59">

## 실행

1. 앱 실행

<img width="1012" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/709e4eaa-45ad-4bfc-aa57-36cb4f1d0d57">

2. Source editor extension 활성화

"Extensions Settings" 버튼 누름

<img width="827" alt="스크린샷 2024-01-10 오후 9 51 36" src="https://github.com/Yoojin99/TIL/assets/41438361/f92acb48-f2ee-44cc-afdf-8d629b7f5e55">

3. Axtension app 에 Accessibility API 권한 허용

"Accessibility Settings" 버튼 누름

<img width="397" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/6abe393d-a4a5-4ca6-8bd8-c84150cd25ac">

4. Copilot 계정 설정

Service 탭 > Github Language Server 쪽에서 install 클릭, 아래 Sign In 버튼 누름

<img width="1012" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/8e6d6511-1225-4be1-ab4d-d088a88651f9">

그러면 아래와 같이 웹에서 로그인 할 수 있게 뜸

<img width="579" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/39a7f86f-ae98-454c-8e74-0cc14610dcb7">

Authorize 클릭

<img width="603" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/22fc27bb-d33d-43c9-be25-03ec2cfb53df">

5. Chat Model 설정

https://platform.openai.com/api-keys 사이트에서 API Key 생성 가능

Chat Models > 이미 생성되어 있는 항목 클릭 > 생성한 키를 설정 후 Save

<img width="1012" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/d7fe220a-e375-4dab-a779-078cd5810d88">

6. Key binding 설정

Xcode 에서 키 바인딩 설정

<img width="942" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/20254710-fa5d-4913-bbd0-0fe80cfbe0cf">

## 사용

코드를 칠 때 추천하는 내용을 보여준다.

<img width="616" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/7a6202bb-ba02-4062-8f35-e2abe95451ce">

<img width="884" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/e4085573-d550-4fb2-827f-c6a88745db30">


## 끄기

열려 있는 앱을 종료하고 메뉴 바에 있는 Copilot 까지 종료해주면 됨

<img width="164" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/649ff62c-8121-45d2-9447-75bca1ee550c">