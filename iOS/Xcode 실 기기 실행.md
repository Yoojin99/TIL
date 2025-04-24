# Xcode 실 기기 실행

## Apple Developer Membership

앱 개발 후 배포하기 위해 필요한 기능들을 지원함.

* Apple Developer Membership 에 가입하지 않고서도 Apple ID 만으로도 실제 기기에서 앱을 테스트해 볼 수 있음.
* 앱을 배포하려면 Apple Developer program 에 등록해야 함.

<img width="602" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/01a8a6c9-fb4e-42e6-98b5-5fd11f1f0bf6">

## Simulator vs Real device

* Simulator
	* 다양한 하드웨어로 앱 디버깅 가능
	* Mac 에서만 실행
	* 실 기기의 성능 / 기능을 충분히 사용 못함
* Real device
	* 정확한 의도대로 앱이 동작하는지 검증 가능

## Build scheme 선택

빌드 전 앱 타겟을 포함한 build scheme 선택

<img width="192" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/52277a54-bce5-4d8a-89bb-ac5494ed36f2">

* Scheme : 프로젝트를 어떻게 빌드하고 실행할지에 대한 프로젝트 세부설정

## 실 기기 연결하기

1. Xcode 의 Account preferences 에서 Apple ID 를 설정

<img width="804" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/b54e35af-598a-4046-8a9b-374b8beb669b">

2. 프로젝트의 Signing & Capabilites 패널에서 유효한 team 설정

<img width="522" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/f7fee242-c9c7-470d-b4d9-8fefd3945bd8">

3. code signing 이 필요한 capability 를 추가할 경우 code sign.

<img width="793" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/95d6ea00-f8c2-4399-af89-8ee63e3ebc5e">

4. Apple Developer Program 에 등록한 경우에는 팀에 기기를 등록함

5. iOS 에서 Developer Mode 활성화함

6. Xcode 의 destination 을 실기기로 설정 후 실행

7. 설정 후 실행했을 때 '신뢰하지 않는 개발자' 알림 뜨면 개발자 신뢰 설정 (설정 > 일반 > VPN 및 기기 관리 > 개발자 앱 신뢰)

<img width="282" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/d8c236ab-6875-4e91-a56d-b75195745a22">

![IMG_1305](https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/8260551d-6322-4eb6-9599-6fd6ed4c9e5c)

![image](https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/a43e5d67-aca6-482b-a99e-577a3c44c0e3)

8. 완료

![image](https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/afcd00c1-019d-41b7-9bb3-7aebc40f7ca8)

## Developer Mode

**위험한 소프트웨어 설치 방지 및 developer-only 모드로 노출되는 attack vector 줄임.**

* iOS 16 에서 등장
* 앱 설치 기술(e,g, App Store 에서 앱 구입 / TestFlight 팀에 참여)가 아닌 Xcode 의 빌드/실행에 초점을 맞춤.(e.g. Apple Configurator 를 사용해서 `.ipa` 파일 설치)

### Developer Mode 활성화 하기

1. Mac 에 처음 기기 연결하면 기기가 Developer Mode 가 비활성화 되어 있기 때문에 destination 이 "Unavailable Device" 로 나옴.

<img width="308" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/88f27ec6-901a-4232-b9be-15800c0613b2">

2. 기기의 설정 > 개인정보 보호 및 보안 > 개발자 모드 키기

![image](https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/fb95013f-d399-40d4-b6ee-0973de566c16)

3. 완료

<img width="192" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/fac98b14-13f6-4570-a2ba-7331548e7adb">



* 참고
* https://developer.apple.com/support/compare-memberships/
* https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device
* https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device
