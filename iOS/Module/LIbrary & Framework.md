# Library, Framework

개발자들은 프로젝트에 클린 아키텍처를 적용하려고 한다.

* Modular codebase > Monolith app 을 선호 하는 이유?
    * Separation of concerns : 각 모듈이 한 작업만 하면 코드 이해 / 작성이 쉽다
    * Reusability : 다른 앱 / 프로젝트에서 재사용할 수 있다
    * Maintainability : 코드가 함수 / 도메인 단위로 구성되어 있을 때 버그 / 기능을 추적하기 더 쉽다


# 기초

Swift 내 code organization 과 access control 은 **Module** 에 기반함. 

**Module** : Code distribution 의 single unit. 

* 자신의 namespace 와 access control이(모듈 밖에서 어떤 코드에 접근을 허용할 것인지를 설정) 있음
* e.g. Framework, library, swift package, Xcode 내 build target

* **Bundle**
    * 연관된 파일들(e.g. images, nibs, compiled code)을 하나의 package 로 묶은 것
    * 파일 디렉토리. 내부에 subdirectory 존재
    * 시스템은 bundle 을 하나의 파일로 취급해서 개발자는 내부 구조를 몰라도 접근할 수 있음
* **Executable** : Application 의 main binary
* **Binary** : 모든 소스 코드를 포함한 파일
* **Object file**
    * Object code(직접적으로 실행할 수 없는 machine code 형태)를 포함한 파일
* **Object Code** : compiler 의 결과물

# Library

Code, resouce 들의 collection 을 하나 이상의 아키텍처로 compile 한 것

Static vs Dynamic 의 차이 : Linking 방식의 차이

## Static Library (*.a)

Object file 들의 collection / archive.

Static linker 는 컴파일된 source code 와 library code 를 하나의 executable file 로 생성. Excutable file 은 runtime 에 전체가 메모리에 올라간다.

<img width="520" alt="Image" src="https://github.com/user-attachments/assets/2c3dcf07-0d94-4da1-80eb-1744bda34a79" />

Static library 는 일반적으로 machine code 언어로 작성된 statement / instruction 들의 sequence 이므로 생성 / 배포에 제약이 있음

* 제약
    * 클라이언트 코드와 같은 프로세서 아키텍처로 라이브러리를 빌드해야 함 (라이브러리 아키텍처 == 클라이언트 프로그램 아키텍처)
        * 클라 코드가 x86 (32-bit) 용으로 컴파일되었다면 사용하는 static library 도 x86 (32-bit) 이어야 함
    * Resource 파일을 포함할 수 없음. (e.g. image, assets, nibs, string file, ...) : 모든 외부 리소스는 별도의 독립적인 bundle 로 제공해줘야 함

*라이브러리를 클라이언트 코드와 같은 아키텍처로 빌드해야 하는건 당연한거 아닌가? 이게 왜 제약이냐?*

1. 크로스 컴파일 환경 : 개발 환경(Mac)에서 코드를 작성하고 다른 플랫폼용(Raspberry Pi)으로 컴파일(배포) 해야 하는 경우 라이브러리를 ARM 용으로도 빌드해야 함
2. 서드파티 라이브러리 : 어떤 라이브러리를 다운로드해서 쓰고 싶은데 x86 만 있고 x64 는 없다? 내가 원하는 아키텍처나 OS 에 맞는 버전이 없으면 못 씀
3. CI/CD : 개발은 x64 에서 개발했는데 최종 사용자 시스템은 ARM64 인경우 빌드 파이프라인에서 아키텍처별로 라이브러리를 빌드해야 하는데 귀찮고 실수 가능성 있음.

*근데 xcframework 도 ios / ios simulator / .. 등의 아키텍처로 빌드해야 하는 건 똑같잖아요. 그러면 static library 와 동일한 제약을 받고 있는 거 아닌가요?*

* 공통점 : xcframework, static library 모두 결국 아키텍처별로 따로 빌드해야 함. 빌드는 여전히 수고스러움
* xcframework 는 뭘 해결하냐?
    * 기존에 .framework / .a static lib 를 만들때
        * Fat Binary 생성
        * Simulator/Device 혼합 아키텍처를 한 파일로 뭉쳐야 함
        * Xcode 12 이후로 Simulator / Device 용 arm64 가 충돌하는 빌드 오류가 많이 생김
        * 🙅‍♀️ Fat binary: 여러 아키텍처를 한 바이너리에 우겨넣는 방식인데, Apple이 요즘 싫어함.
    * 즉 아키텍처별 라이브러리를 **깨끗하게 분리해서 하나로 묶어주는 포맷, Xcode 가 알아서 플랫폼/아키텍처에 따라 올바른 라이브러리 선택**

## Dynamic Library (*.dylib)

*.dylib 는 macOS 에서는 가능하지만 iOS/iPadOS 등에서는 보안, 앱 샌드박싱이 엄격해서 금지됨. .framework 형태로 감싸서 안전하게 관리*

Static library 처럼 executable file 안으로 복사되지 않고 load / runtime 에 동적으로 linking 됨. Binary 파일과 library 가 모두 메모리 안에 존재.

Dynamic library 는 별도로 저장 / 버저닝 되기 때문에 만약 업데이트 된 binary 가 이전에 사용하던 것과 호환이 된다면 로드된 바이너리가 기존에 참조하던 것과 달라질 수 있음

<img width="511" alt="Image" src="https://github.com/user-attachments/assets/1ab80ed8-30b7-4223-8749-a44f8da202ce" />

System iOS / macOS library 는 dynamic. 즉 우리 앱을 다시 빌드하거나 제출하지 않아도 Apple 에서 업데이트 한 내용을 받을 수 있음. 따라서 새로운 OS 버전이 정식 출시되기 전에 앱을 신규 OS 에서 테스트해봐야 함(새로운 OS 에서 업데이트 된 dynamic library 를 사용했는데 호환성 문제가 발생할 수도 있기 때문에)

*.dylib 는 왜 보안 이슈가 있나?*

* iOS 는 통제된 환경이 핵심. Apple 은 iOS 를 극도로 안전한 플랫폼으로 유지하는 것이 목표
    1. No runtime code injection : 앱은 고정된 코드만 실행해야 한다
    2. Immutable binary : 앱은 앱스토어 제출 당시의 상태에서 벗어나면 안 된다
* dylib 위험성
    1. Dynamic Code Injection 가능 : 외부에서 동적으로 코드 로드가 가능해서 런타임에 악성 코드 삽입, 앱 샌드박스와 Signing 을 무력화시킬 수 있음
    2. 앱 번들에서 벗어난 곳도 참조 가능 : 민감 데이터 / 기능에 접근 가능할 수 있음

# Framework (.framework)

