# App Size

앱 크기가 중요한 이유

1. 앱스토어 다운로드
   1. 시간 이슈
   2. 데이터 이슈
2. 기기 용량 이슈

## App store

앱 다운로드가 오래 걸리지 않게 하고 사용자에게 추가적인 데이터 요금을 물지 않게 하기 위해서 App Store 는 데이터를 사용해서 다운받을 수 있는 앱 크기를 제한함.

![File](https://github.com/Yoojin99/TIL/assets/41438361/76a954ae-73c7-4646-af04-31de4f2125c9)

설정 앱 > App Store > App Downloads 에는 앱 다운로드에 관한 메뉴가 있음.

기본 설정은 200MB 가 넘을 경우 셀룰러 데이터를 사용해서 다운로드할 것인지를 alert 으로 물어봄. 

200MB 가 넘을 경우 와이파이를 사용해서 다운받아야 함. 

## 기기 용량 

기기의 용량은 한정되어 있으므로 개발, 테스트 과정에서 앱 사이즈를 지속적으로 측정하고 최적화해야 함

## 측정

### 이용할 수 없는 바이너리

* APP 파일 (app bundle)
* 앱을 아카이빙할 때 생성하는 XCARCHIVE 번들
* App Store Connect 에 업로드하는 IPA 파일

**XCode 를 통해 App Store 에 업로드하는 디버깅용 바이너리들은 앱 크기를 잴 때 사용하기로 적절하지 않음.**

위 바이너리들은 App Store 에서 사용자들이 다운로드할 때 bundle 에 포함되지 않는 리소스, 파일들을 포함하고 있음. e.g. DSYM 파일 (crash 리포트에 사용됨)

### 정확한 size 측정 방법

* Mac 에 app size report 를 생성
* 앱이 이미 App Store / TestFlight 앱에서 지원중이라면 App Store Connect 가 가장 정확

App Store Connect 는 앱의 여러 버전에 대한 사이즈를 표시하고 모바일 데이터를 사용해서 다운받을 수 있는 용량을 초과할 경우 경고를 날림

### App Store 최종 앱 크기

TestFlight 앱 크기 == App Store 빌드 앱 크기 + 추가 데이터

하지만 업로드한 바이너리와 비교했을 때 App Store 에서 승인된 최종 앱 크기는 조금 더 커질 수 있음 : App Store 가 앱 바이너리에 추가적인 작업 (app piracy 를 예방하기 위해 DRM 을 추가하고 바이너리를 다시 압축할 때) 을 할 수 있음.

## Workflow

![](https://docs-assets.developer.apple.com/published/38f7a1fa14b3caf60822d8a97a802dc0/reducing-your-app-s-size-1@2x.png)





* 참고
* https://developer.apple.com/documentation/xcode/reducing-your-app-s-size