
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



