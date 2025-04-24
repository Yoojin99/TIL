# Xcode 설치 오류 - Loading a plug-in failed

1. Applications 폴더 내에 Xcode.app 위치시킴
2. `xcode-select -s 'Xcode.app 파일 위치'` 를 통해 xcode 버전을 지정 
   * Xcode: /Applications/Xcode.app/Contents/Developer
   * Xcode-beta: /Applications/Xcode-beta.app/Contents/Developer
   * `xcode-select -p` 명령어를 통해 현재 xcode 버전 확인
3. `xcodebuild -runFirstLaunch` 실행
   * 약관 동의 문제가 있을 경우 먼저 `sudo xcodebuild -license` 로 약관 동의

* https://stackoverflow.com/questions/17980759/xcode-select-active-developer-directory-error
* https://forums.developer.apple.com/forums/thread/759396