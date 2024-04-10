# .gitignore for iOS

## .DS_Store

Desktop Services Store, macOS 운영체제에서 Finder 로 폴더를 볼 때마다 자동으로 생성됨.

* 폴더의 사용자 정의 속성 / 메타데이터 정보 저장
* 파일을 통해 보안 침해 가능

## .swp

리눅스에서 vi 편집기에서 작업을 하다가 정상적인 종료를 하지 않는 경우 생기는 파일.

* 스왑 파일
* 작업중 사용자의 의도와 무관하게 예기치 못한 종료를 통해 파일이 손상, 유실되는 경우를 대비한 백업의 성격을 띔
* 1개에 하나만 생성되는 것이 아님
* 특정 파일에 대한 swp 파일이 생성된 경우 swp 파일이 덮여쓰여지는 것이 아니라 swk, swn 등의 변경된 확장자로 추가 파일 생성 가능

## .gitignore 에 추가했는데도 제외가 안되는 경우?

`git rm -f --cached [파일 경로]` 로 지워주면 됨

* 참고
* https://stackoverflow.com/questions/49478/git-ignore-file-for-xcode-projects
* https://dlee0129.tistory.com/250
* https://tree.nathanfriend.io/?s=(%27options!(%27fancy8~fullPath!false~trailingSlash8~rootDot8)~B(%27B%2706I6I.Fode7%20%2F%2F%20디렉토리G.pbx7G5Ccontents59C43-42sCI.2-3CHName.3dC*2sC**2management.plist6%27)~version!%271%27)*%20%20-6*IExampleApp2Fscheme3FH94Fshared9C5.Fworkspace6%5Cn7proj8!true9dataBsource!C-*FxcG-7ectHuserI*0%01IHGFCB987654320-*