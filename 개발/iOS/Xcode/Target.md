# Target

**빌드할 product 를 명시하며 프로젝트/workspace 내의 파일들에서 product 를 빌드하는 방법을 포함함**

*product : 앱 / 프레임워크 / app extension / unit test*

프로젝트는 여러 개의 target 을 포함할 수 있음. 이때 각 target 은 한 product 의 여러 밀접한 부분을 나타내고 있을 것. (e.g. 앱/private framework/app extension/test)

아래와 같이 Xcode 의 target 에서 확인 가능

![](https://docs-assets.developer.apple.com/published/fde42f90bbd1c2fbf1b5c60aa1805f67/build-targets@2x.png)

* target 당 하나의 product 를 정의 : 빌드 시스템에서 product 를 빌드하기 위해 필요한 input 들을 (소스 파일들, 소스 파일들을 처리하는 instruction) 을 조직함
* Product 를 빌드하는데 사용되는 instruction 들은 build setting, build setting 으로 Xcode project 에디터에서 확인할 수 있음. 
* Target 은 project 의 build setting 을 상속받음과 동시에 target level에 따라 다르게 설정해서 project setting 을 override 할 수 있음.
* 한 시점에 active target은 오직 하나. Xcode scheme 이 active target 명시
  
Target, 해당 target 이 생성한 product 는 다른 target 과 연관될 수도 있음.

* Implicit dependency : 같은 workspace 내 두 개의 target 이 있고 한 target 이 다른 target 이 빌드된 결과물을 필요로 할 경우. Xcode 는 의존성을 파악해서 product 를 순서대로 빌드할 수 있음.
* Explicit dependency : Build setting 에서 명시적으로 target dependency 설정 가능.


* 공식문서
  * https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Targets.html#//apple_ref/doc/uid/TP40009328-CH4-SW1
  * https://developer.apple.com/documentation/xcode/configuring-a-new-target-in-your-project/