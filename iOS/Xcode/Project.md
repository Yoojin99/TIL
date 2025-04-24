# Project

**하나 이상의 소프트웨어 제품을 빌드하기 위해 요구되는 모든 파일, 리소스, 정보들의 repository. 제품을 빌드하는데 사용되는 모든 요소들을 포함하며 요소들간의 관계를 유지함.**  

* 하나 이상의 target(어떻게 빌드할지를 명시한 것) 을 포함하고 있음.
* 프로젝트 내 모든 target 들을 위한 default build setting 을 정의하고 있음 (각 target 은 project build setting 을 override 하는 자신만의 build setting 을 가질 수 있음)
* 홀로 존재하거나 workspace 내에 포함될 수 있음

Xcode project 가 포함하는 정보는 다음과 같음

* Source file 에 대한 참조들
* 소스 파일들을 포함하는 Group 들
* Project-level build configuration 들
* Target 들
* 프로그램을 디버깅 / 테스트하는데 사용될 수 있는 실행가능한 환경들

## References to source files

* 소스 코드 (헤더 파일, 구현 파일)
* 라이브러리, 프레임워크
* 리소스 파일
* 이미지 파일
* Interface Builder 파일 (nib)

### Nib 파일

Xib, Nib 파일은 모두 사용자 인터페이스를 표현하며 Interface Builder 를 사용해서 빌드됨. (Xib, Nib != Storyboard)

* xib (Xcode interface builder) 
  * 개발할 때 사용되는 것
  * Xcode 에서 제공하는 시각적인 에디터. `View` 파일을 만들면 `.xib` 확장자 파일이 생성됨. (xml 형태)
* nib (NeXTSTEP interface builder)
  * 빌드할 때 생성되는 것
  * xib 가 컴파일된 후 메모리에 로드할 때 사용되는 파일
* xib 빌드 ➡️ nib 파일, Nib 파일은 App Bundle 디렉토리에 저장되어 코드베이스에서 파일을 사용할 때 bundle 에서 로드해서 사용함

## Project-level buid configuration

프로젝트에 하나 이상의 build configuration 을 명시할 수 있음.

e.g. Debug / release build setting

Build Setting 는 build process 의 모든 것을 관리함.

* Build process
  * 소스 파일들을 어떻게 컴파일 할지
  * Executable 을 어떻게 link 할지
  * Debug information 을 생성할지
  * 어떻게 패키징하고 배포할지

Xcode 의 Build Settings 탭 / build configuration 파일로 project / target 설정 바꿀 수 있음.

![](https://docs-assets.developer.apple.com/published/af7a7a11967dfd4ab70af3f67339364e/build-settings-editor@2x.png)

기본적으로 build configuration 은 debug, release 가 존재.

<img width="522" alt="image" src="https://github.com/Yoojin99/RxSwift-Practice-App/assets/41438361/8501dc03-bef7-4d93-aa5b-a76e6acad9da">

여기에서 inhouse build configuration 을 추가한다고 했을 때 build settings 탭에 debug / release 만 나오던게 inhouse 도 같이 나오면서 inhouse 에 대한 설정도 커스텀 할 수 있는 것.

<img width="494" alt="image" src="https://github.com/Yoojin99/RxSwift-Practice-App/assets/41438361/43f5e2ec-bbe9-49fe-8651-26cd3300b09b">

## Target

각 Target 이 명시하는 것은 다음과 같음

* 프로젝트를 통해 빌드된 제품에 대한 참조
* 제품을 빌드하기 위해 필요한 소스 파일들에 대한 참조
* 해당 제품을 빌드하기 위해 사용되는 build configuration. 다른 target, setting 에 대한 의존성을 포함함. 만약 target 의 build configuration 이 project-level build setting 을 override 하지 않는 경우 project-level build setting 을 사용함

## Executable environments

각 executable environments 가 명시하는 것은 다음과 같음

* Xcode 에서 run/debug 할 때 실행할 executable
* (존재할 경우) executable 에 전달할 커맨드 인자
* (존재할 경우) 프로그램이 실행될 때 설정되어야 할 환경 변수들

* 공식 문서
  * https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Targets.html#//apple_ref/doc/uid/TP40009328-CH4-SW1
  * [Nib 파일](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/LoadingResources/CocoaNibs/CocoaNibs.html)
  * https://developer.apple.com/documentation/xcode/configuring-the-build-settings-of-a-target/
* 기타
  * https://ios-development.tistory.com/1293
  * https://www.hackingwithswift.com/example-code/language/what-is-a-nib