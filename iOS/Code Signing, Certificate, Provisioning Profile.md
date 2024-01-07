# Code Signing

**Code signing 을 통해 알려지지 않았거나 마지막으로 sign된 후 수정되지 않은 source를 사용자가 사용하는 것을 방지**

앱에 app service 를 통합하거나, 앱을 기기에 설치하거나, 앱스토어에 제출하기 위해서는 Apple 이 발행한 certificate 로 sign 되어야 함.

## Signing

**Signing은 앱을 App Store Connect 에 업로드하고 TestFlight/AppStore 에 배포하기 위해 필요함**

### 장점

* 앱이 마지막으로 sign 된 후에 코드가 변경되지 않았음을 알 수 있음 : OS 는 AppStore 에서 다운받은 앱의 signature 를 검증해서 유효하지 않으면 실행하지 않음. 앱 bundle 내에 실행가능한 코드가 변경될 경우 signature 가 유효하지 않게 되기 때문에 앱의 실행가능한 코드는 signature 를 통해 보호된다고 할 수 있다.
* 코드의 source (개발자/signer) 를 검증할 수 있음

즉 signing 을 통해 앱이 변경됐음을 알 수 있고, 앱의 signature 가 유효할 경우 사용자는 앱이 마지막으로 sign 된 후로 수정되지 않았고 안전한 앱임을 확인할 수 있음.

### 한계

* 코드가 보안상 문제가 없음을 검증할 수 없음
* 앱이 실행 도중 안전하지 않은 코드를 load 하지 않음을 보장할 수 없음
* DRM (Digital Rights Management) / 복제 방지 기술 을 제공하지 않음

## Signing Certificate

Xcode 는 build process 에서 *signing certificate* 를 사용해서 sign 함.

Signing Certificate : Private key + Certificate(Public Key 포함, Private Key 와 쌍을 이룸)

<img width="500" alt="image" src="https://github.com/Yoojin99/TIL/assets/41438361/e0e83dcd-b32d-46d6-a31d-c594bf49a906">

* Private key : Signature 를 생성하기 위한 암호 함수에 사용됨
* Certificate(Public Key 포함) : Apple이 발행하며 developer 계정에 추가됨. 사용자가 key 쌍의 owner 인지 확인. 

Public-private key 쌍은 key chain 에 저장됨. 

<img width="1144" alt="스크린샷 2024-01-06 오후 11 56 38" src="https://github.com/Yoojin99/TIL/assets/41438361/591932fd-6d8d-4171-848f-161708784b08">

### Public-Private key

* Public key : Lock only
* Private key : Unlock only

Keychain 앱에서 **CSR(Certificate Signing Request)** 을 생성할 때 사용. 

키체인 앱에서 private key, certSigningRequest 파일을 생성해서 Apple 에 업로드. Apple 은 request 를 검증한 후 인증서를 발행. 이 인증서에서는 다운로드를 받을 수 있게 public key 만 포함. 인증서를 다운로드하면 더블클릭해서 키체인 앱에 넣을 수 있음.

# Certificate

Signing 에 사용되는 인증서로, Apple이 발행함. 

때에 따라 다른 타입의 인증서 사용.

## 종류

* Apple Development certificate : 앱을 기기에서 실행하고 app service 사용. 개인에게 할당되며 컴퓨터 이름이 인증서 이름 뒤에 붙음.
* Apple Distribution certificate : 테스트를 위해 앱을 배포하고 App Store Connect 에 업로드할 때 사용

아래와 같이 Xcode 에서 Certificate 를 확인 가능.

<img width="942" alt="스크린샷 2024-01-06 오후 11 52 59" src="https://github.com/Yoojin99/TIL/assets/41438361/a898c981-755c-4613-9371-b440d54b8299">

참고로 Apple이 발행한 인증서를 사용하려면 Apple Developer Program 멤버십에 가입해야 함.

# Provisioning Profile

**Apple 플랫폼 (macOS 제외) 에서 임의의 third-party 코드가 실행되기 위해 필요한 Apple 인증.** 기기-개발자 계정 사이의 링크라고 이해하면 편함.

## 기준

Provisioning profile 은 5개의 기준을 포함.

* *누가* code를 sign 할 수 있는가
* *어떤* 앱이 sign이 허용되는가
* *어디서* 앱이 실행될 수 있는가
* *언제* 앱이 실행될 수 있는가
* *어떻게* 앱이 자격을 갖추는가

*여기서 '앱'은 bundle 구조 내에 패키징 된 main executable 을 의미함. 즉 앱, app extension, App Clip, system extension, XPC Service 를 포괄함.*

## 종류

* Development Provisioning Profile 
* Distribution Provisioning Profile : Device ID 를 명시하지 않음. 만약 제한된 숫자의 등록된 기기에 앱을 릴리즈하고 싶을 경우 Ad-Hoc profile 사용해야 함.

개발 도중 앱을 실행할 기기를 선택하며 앱이 접근할 수 있는 app service 를 선택함. Provisioning profile 이 developer 계정에서 다운르도 되고 app bundle 에 임베딩되며 전체 bundle 이 code-sign 됨. Development Provisioning Profile 은 앱 코드를 실행할 기기에 설치되어 있어야 함. Provisioning profile 내의 정보가 위의 기준을 맞추지 못한다면 앱은 실행되지 않음.

예외 : 앱을 App Store 에 제출하면 App Store 는 distribution process 의 일종으로 앱을 다시 sign 함. Sign 이전에 앱이 올바르게 sign 되며 provisioning 됐는지 확인. 이 확인하는 과정이 각 개별 기기가 추후 보안 체크가 필요없음을 의미하기 때문에 최종 앱은 provisioning profile 이 없음. Distribution 

### Development Provisioning Profile 구조

<img width="819" alt="스크린샷 2024-01-07 오후 3 45 45" src="https://github.com/Yoojin99/TIL/assets/41438361/8d0d564c-9a86-424c-8569-59c29f86c2b8">

* App ID : 하나의 development team 에서 만든 앱을 구별하기 위해 사용되는 string
* Development Certificate : 앱을 기기에서 실행하기 위한 certificate
* Unique Device Identifiers : 앱이 실행될 수 있는 기기 목록

즉 어떤 앱(App ID)을 어떤 기기(Device Identifier)에서 실행하려는데 유효함(Development Certificate) 을 확인하기 위한 정보라고 생각할 수 있음.

# 정리

Provisioning, Code Signing 과 관련된 항목들

* Xcode
* Memeber Center : Provisioning Profile, App ID, Certificate 등을 생성할 수 있는 곳. Apple Developer Program 에 등록했을 때 사용가능.
* Keychain
* Signing Identity : 앞서 본 Signing Certificate
* Private & Public Key 
* Provisioning Profile
* App ID

## Provisioning, Code signing 이 일어나는 과정

1. Xcode 가 설치됨. Intermediate Certificate (Developer/Distribution certificate 가 다른 CA 에 의해 발행됨을 증명하기 위한 인증서. Xcode 가 처음 설치될 때 자동으로 다운받아짐. 키체인 내부에 존재.) 가 키체인에 들어감
2. CSR (Certificate Signing Request) 가 생성됨. certSigningRequest 파일 생성됨.
3. CSR 이 생성될 때 Private Key 도 함께 생성되며 키체인에 저장됨
4. CSR 이 Member Center 에 업로드 됨
5. Apple 이 검증 후에 Certificate(public key 포함) 발행
6. 내 컴퓨터에 Certificate(public key 포함) 받아짐
7. 키체인에 Certificate 가 들어가며 private key 와 짝지어져서 Code Signing Identity 형성
8. Certificate, App ID, Device Identifier 를 사용해서 Provisioning Profile 생성
9. Xcode 를 통해 Provisioning Profile 다운받음
10. Xcode 가 앱(code)을 sign 함. 
11. Provisioning Profile 을 기기에 push
12. iOS 내부에서 검증

* 참고
  * [Code Signing](https://developer.apple.com/support/code-signing/)
  * [Certificates](https://developer.apple.com/support/certificates/)
  * [What is app signing?](https://help.apple.com/xcode/mac/current/#/dev3a05256b8)
  * [About Code Signing](https://developer.apple.com/library/archive/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40005929-CH1-SW1)
  * [TN3125: Inside Code Signing: Provisioning Profiles](https://developer.apple.com/documentation/technotes/tn3125-inside-code-signing-provisioning-profiles)
  * [What is a provisioning profile & code signing in iOS?](https://abhimuralidharan.medium.com/what-is-a-provisioning-profile-in-ios-77987a7c54c2)
  * [iOS Code Signing & Provisioning in a Nutshell](https://medium.com/ios-os-x-development/ios-code-signing-provisioning-in-a-nutshell-d5b247760bef)
  * [[iOS] Certificate 와 Provisioning profile](https://sujinnaljin.medium.com/ios-certificate-와-provisioning-profile-e1b9455e8a51)
