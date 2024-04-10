# Scheme

Scheme 이 정의하는 것들은 다음과 같음

* 빌드할 target 들의 collection
* 빌드할 때 사용할 configuration
* 실행할 test 들의 collection

Scheme 의 수에는 제한이 없으나 active 한 것은 오직 하나. Scheme 이 project 에 포함되는지 (해당 project 를 포함한 모든 workspace 에서 사용 가능함) / workspace 에 포함되는지 (오직 해당 workspace 에서만 사용 가능함) 를 명시할 수 있음.

앱을 빌드하고 실행할 때 scheme 은 Xcode 에게 앱에 어떤 인자를 전달해야할지를 알려줌. Xcode 는 대부분의 target 에 default sheme 을 제공하지만 새로 생성하거나 커스텀 가능.

프로젝트의 현재 scheme 을 아래와 같이 확인할 수 있음.

![](https://docs-assets.developer.apple.com/published/2d0e5631004d1eec48b559593fd8ab0d/build-select-scheme@2x.png)

* 공식 문서
  * https://developer.apple.com/library/archive/featuredarticles/XcodeConcepts/Concept-Schemes.html#//apple_ref/doc/uid/TP40009328-CH8-SW1
  * https://developer.apple.com/documentation/xcode/customizing-the-build-schemes-for-a-project/