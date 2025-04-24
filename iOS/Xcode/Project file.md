# Xcode Project file

```
.
└── ExampleApp/
    ├── ExampleApp
    └── ExampleApp.xcodeproj # 프로젝트 디렉토리
        ├── project.pbxproj # 실제 설정파일
        ├── project.xcworkspace/ 
        │   ├── contents.xcworkspacedata
        │   ├── xcshareddata
        │   └── xcuserdata
        ├── xcshareddata/ # 공유되는 설정
        │   └── xcschemes/
        │       └── ExampleApp.xcscheme
        └── xcuserdata/ # 개인 설정
            └── userName.xcuserdatad/
                └── xcschemes/
                    └── xcschememanagement.plist
```

## project.pbxproj

프로젝트 설정 파일. 파일들을 유형에 따라 reference 를 저장하며 build setting 저장.

Git conflict 의 주범.

<img width="576" alt="image" src="https://github.com/Yoojin99/RxSwift-Practice-App/assets/41438361/544ae557-8513-426b-9907-02ff258f9729">

## project.xcworkspace

여러 개의 project, 파일들을 묶을 수 있는 개념

### contents.xcworkspacedata

프로젝트들의 reference 저장

### xcshareddata

workspace 의 공유된 설정을 담은 디렉토리

### xcuserdata

workspace 의 개인 설정을 담은 디렉토리

## xcuserdata

프로젝트의 개인 설정을 담은 디렉토리. breakpoint, UI layout, 스냅샷 설정을 담은 프로젝트 자체에 큰 영향을 주지 않는 디렉토리.

* 참고
  * https://tree.nathanfriend.io
  * https://hcn1519.github.io/articles/2018-06/xcodeconfigurationx