# Dependency Management

## Dependency

**외부에 존재하는 단독으로 존재할 수 있는 프로그램(라이브러리)** 

필요한 모든 기능을 구현할 수도 있지만 그러기에는 시간과 자원이 부족할 수 있음. 이때 이런 작업들을 수행할 수 있는 다른 곳에서 이미 구현된 프로그램을 이용할 수 있음. 우리의 프로그램에서 사용하는 이런 외부의 프로그램들을 **의존성**이라 할 수 있다.

## Dependency Management 의 필요성

의존성의 장점은 의존성을 통해 특정 기능을 개발할 시간이 줄어든다는 것이지만 **의존성 통합 비용이 증가**한다는 단점이 있음.

의존성은 개발자의 시간을 아끼고 작업을 덜 할 수 있게 하는 것이 목표. 의존성이 프로젝트에 
문제를 일으킨다면 이는 목표와 정반대의 상황. 따라서 의존성을 잘 관리해야 함.

의존성을 다운받고 빌드하는 것에 추가로, **의존성의 의존성 또한 다운받아지고 빌드되어야 하며 전체 dependency 그래프가 충족**되어야 함. 의존성이 특정 버전 요구사항에 맞춰져야 할 수도 있는 등 복잡한 상황이 분명 존재함. 이런 **복잡한 상황을 관리**하는데 dependency manager 를 사용할 수 있음.

* 보안 : 라이브러리에 보안상 이슈가 있을 때 이를 업데이트하지 않을 경우 앱에도 영향을 줄 수 있음.
* 성능 향상 : 라이브러리를 업데이트 해서 새로운 기능을 도입하거나 성능을 향상시킬 수 있음

## Dependency Hell

**의존성들의 그래프가 프로젝트가 요구하는 것과 맞지 않는 상황**

* 버전 이슈 : 적절한 의존성 버전이 명시되어야 함
* Major 버전 요구사항과 일치하지 않음 : 패키지가 해당 패키지가 요구하는 버전에 못 미치는 의존성을 가진 상황
* Namespace 충돌 : 같은 이름을 가진 둘 이상의 의존성이 존재하는 상황
* 동작하지 않는 소프트웨어 : Outdated/보안상, 성능상 이슈가 있는 의존성이 있는 상황
* Global state 충돌 : 같은 global state 에서 충돌이 아는 의존성들이 이쓴 상황
* 패키지가 더이상 사용 불가 : 더이상 사용할 수 없는 의존성이 있는 상황

이런 상황들과 앞선 복잡한 상황들을 관리하고 dependency hell 이 발생할 위험을 최소화하기 위해 package manager 를 사용함.

## Dependency Manager

**외부 라이브러리/패키지를 어플리케이션에 통합하는 소프트웨어 모듈. 프로젝트의 의존성을 다운로드, 빌드하는 프로세스를 자동화하고 통합하는 비용을 최소화 하는 것이 역할.**

즉 우리가 개발한 애플리케이션에서 사용할 의존성들을 우리의 애플리케이션에 통합해주는 소프트웨어라고 생각할 수 있다.

특정 configuration 파일을 사용해서 다음 사항들을 판단함.

1. 어떤 dependency 를 가져올지
2. Dependency 가 어느 버전인지
3. 어떤 repository 에서 가져올지

*repository : 명시된 이름, 버전의 의존성을 가져올 수 있는 소스.*

### iOS Dependency Manager 

* Swift Package Manger (SPM, SwiftPM)
* CocoaPods
* Carthage

## Semantic Versioning

[Semantic Versioning 규칙을 명시한 사이트](https://semver.org)

어떻게 버전 넘버가 할당되고 증가하는지에 대한 규칙이 존재.

`X.Y.Z` (`MAJOR`.`MINOR`.`PATCH`)

* `MAJOR` : 기존 것과 대체되지 않는 API 변경사항 있을 때
* `MINOR` : 기능 추가가 되었을 때
* `PATCH` : 버그를 고쳤을 때

## Binary Dependencies

바이너리 의존성을 포함시키는 것은 단점이 존재. 바이너리 의존성은 특정 플랫폼만 지원할 수 있기 때문에 portable 하지 않음.

소스코드 기반 의존성과 바이너리 의존성이 있으며 둘이 같은 기능을 제공할 경우 소스코드 의존성을 사용하는 것이 좋음.

## Git Submodules

Repository 내의 폴더가 다른 git repository 에서 생성될 수 있게 함. (다른 Git repsoitory 에서 끌어와서 메인 repository 내의 폴더를 구성한다는 얘기인 듯) Submodule 의 특정 커밋/브랜치를 트래킹할 수 있음.

간단한 프로젝트에는 잘 동작하지만 메인으로 사용하기에는 적합하지 않음.

* Git 은 submodule 컨텐츠를 기본적으로 다운로드 하지 않음.
* Submodule 을 업데이트하면 자동으로 협업자들도 submodule 이 업데이트 되지 않고 일일이 git submodule 을 업데이트 시켜야 함.
  
## vs Package Manager

* Package Manager : 시스템에 사용됨
* Dependency Manager : 특정 프로젝트에 사용됨

Package Manager 는 개발 환경을 설정하는데 사용되며 이를 사용해서 많은 프로젝트를 구성할 수 있음.

Dependency Manger 는 특정 프로젝트에 국한됨. 한 프로젝트의 의존성을 모두 관리할 수 있음. 다른 프로젝트를 만든다면 다시 의존성들을 관리해야 함.

* 참고
* https://medium.com/prodsters/what-are-dependency-managers-26d7d907deb8
* https://byby.dev/ios-dependency-managers
* https://stackoverflow.com/questions/27285783/package-manager-vs-dependency-manager
* https://semver.org