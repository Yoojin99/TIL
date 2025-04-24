# Write Tests to Fail

WWDC20

---
* triage : 회진하다
* robust : 건장함
---

실패하는 테스트 작성하기 : 좋은 테스트는 버그를 잡아내고, 실패난 것을 바탕으로 우리가 후속 작업을 할 수 있음. 

WWDC 영상에서는 테스트는 모두 CI 시스템에서 실행되기 떄문에 실패난 것을 테스트 Result bundle 에서 빠르게 확인할 수 있어야 함.

테스트는 set up, test, tear down 테스트 패턴을 따름.

Test 부분에서는 action / assertions 로 나눌 수 있음

# Set up

요구사항에 대한 가정을 명시적으로 정의하고 테스트를 실행하기 전의 앱 / 환경의 상태를 설정하는 곳

* `setUpWithError() throws` 메서드 사용
* 클래스 공통 setup 작업 수행
* `launchArguments` / `launchEnvironment` 을 사용해서 상태 설정

Xcode 11.4 에서 `setUpWithError` 라는 새로운 setup 함수 등장. 

* `setUpWithError`
  * setup 도중에 전달되는 에러를 catch 하거나 전달할 수 있음
  * 테스트 실행 전에 요구되는 최초 상태를 설정. 이전 테스트가 앱의 상태나 테스트가 사용하는 데이터를 변경했을 수 있음

<img width="572" alt="image" src="https://github.com/user-attachments/assets/92e206df-fdd7-47cd-b15d-b4f2f35544fa">

* `continueAfterFailure = false`
  * 테스트가 하나라도 실패하면 즉시 테스트 메서드 실행을 중지
  * 여러 에러를 분석하는 대신 빠르게 첫 번째 에러를 찾을 수 있게 해줌. 
* `launchArguments` 
  * 환경 변수를 설정해서 앱 상태를 빠르게 설정함
  * 위 케이스에서는 메인 화면에 탭 바가 있고, 메뉴 탭 / 레시피 탭이 있는데 앱을 실행하자마자 메뉴 탭을 건너뛰고 레시피 탭에서 시작할 수 있게 한 것
  * 레시피 탭을 테스트하는 동안 메뉴 탭에서 발생할 수 있는 failure 를 살펴보지 않아도 되게 해줌

# Test

테스트는 액션을 수행하고 액션이 완료된 것을 assert 해야 함

## Action

* 특정 목표를 위해 테스트를 디자인
* Enum 을 사용
* 공통 코드를 helper 함수로 리팩토링
* 앱의 UI 계층을 반영하기 위해 테스트 내에 모델링한 객체 생성
* 프로젝트간 공유되는 테스트 코드를 위해 framework / Swift package 사용

<img width="571" alt="image" src="https://github.com/user-attachments/assets/7b2903ab-02c6-4e76-8134-17f99acf8cb6">

* 테스트는 특정 목표가 있어야 하고 이름에 반영되어야 함.
* 위 예시에서는 액션을 블루베리 항목을 터치하는 것으로 최소화함. 액션을 최소화하면 나중에 failure를 쉽게 살펴볼 수 있음
* Result Bundle 에서는 테스트 이름이 보이기 때문에 테스트가 어떤 것을 검증하는지 확인할 수 있음
  
<img width="573" alt="image" src="https://github.com/user-attachments/assets/545333dc-4b5c-4a0e-903d-4062c1221d35">

* Label 내의 내용은 자주 바뀜. 모든 문자열에 enum 을 사용하면 UI 가 변경되도 해당 변경사항을 테스트에도 쉽게 반영해서 테스트 할 수 있음

<img width="467" alt="image" src="https://github.com/user-attachments/assets/cb37c436-2c14-44c2-83a7-cddff1a2cea7">

* 공통 코드를 helper function 으로 리팩토링해서 여러 테스트가 같은 code path 를 사용할 수 있게 함

<img width="479" alt="image" src="https://github.com/user-attachments/assets/fb36144a-077e-452c-8649-faf7129db979">
<img width="269" alt="image" src="https://github.com/user-attachments/assets/84f903e8-29f1-442e-8220-aab0c929ba82">

* 앱의 도메인을 모델하고 그 도메인에 대한 테스트 언어를 디자인 함. 테스트는 앱의 언어를 반영할 수 있음
* 위 예제에서 Fruta App 에 SmoothieList 를 요청하고, SmoothieList 에서는 Recipe (UI 요소)를 선택할 수 있음. 얘네들은 FrutaUIElement 클래스를 기반으로 하고 있어서 더 낮은 레벨에서 앱과 요소들을 추적할 수 있음
* 위 방법으로 공유되는 코드를 객체지향적으로 만들 수 있음
* 테스트는 요소와 쿼리에 기반한 것으로 취급되는데, 가독성을 위해 객체 지향적인 환경을 시뮬레이션 할 수 있음
* 이렇게 테스트에서 호출하면 앱을 subview 의 시리즈로 생각할 수 있음. 
* 이런 모델링의 결과는 계층이 줄어드는 효과가 있고 해당 요소의 subelement 에만 집중할 수 있다는 것

또한 공유되는 테스트 코드는 시간이 갈수록 커짐.

이를 위해 공유되는 테스트 코드를 product code 처럼 취급해서 테스트를 위한 공유되는 프레임워크 / Swift package 를 생성할 수 있음 (특히 여러 애플리케이션 사이에 공유할 때)

## Assertions

* assertion 메세지 추가
* 관련 있는 `XCTAssert*` 함수 사용
* 옵셔널 언래핑
* 비동기 이벤트에 `waitForExistence()` 사용
* 공유되는 코드에서 에러 throw
* `XCTContext.runActivity()` 와 첨부물 사용
* `XCTSkip()` 사용

<img width="581" alt="image" src="https://github.com/user-attachments/assets/64b92ae6-83b6-4295-947b-6f3f4eacd26c">

* XCTAssert 함수 내 옵셔널 메세지를 잘 활용하기
* 로컬 환경에서는 테스트할 때 메세지를 비워둬도 괜찮지만 result bundle 에서 보게 되면 문맥이 빠지게 됨
* 메세지는 구체적이면 좋지만 너무 구체적이어서는 안됨. 따라서 date/time stamp 나 파일 경로는 비워둔다. 그 이유는 같은 이유로 여러 테스트가 실패할 수 있기 때문

<img width="359" alt="image" src="https://github.com/user-attachments/assets/2c086cf8-972f-42d8-96b5-bf28b9a8162a">

* 상황에 맞는 올바른 assertion 을 쓰면 fail 이 떴을 때 자동으로 생성되는 메세지의 내용이 더 관련있게 됨
* XCTIssue 는 failure 를 report 하는 새로운 low-level 방법임

<img width="564" alt="image" src="https://github.com/user-attachments/assets/9cf5ef46-f768-4e4f-b1da-e20a09b76055">

* 비동기 이벤트를 테스트할 때 기존에는 sleep 을 사용해서 UI 가 준비될 시간을 마련했음
* 이 방법은 테스트 시간을 길게 만들기 떄문에 좋은 방법이 아님.
* `waitForExistence(timeout:)` 을 사용
  * Polling 을 통해 expectation 이 timeout 보다 이른 시간 내에 true 가 되면 바로 참으로 만들어버리기 때문에 기다리는 시간을 절약할 수 있음
  *  또한 제한 시간을 둬서 테스트가 성공/실패 하게 할 수 있음

테스트 내에서 nil인 optional 에 잘못 접근하면 code 를 로컬에서 실행할 때는 크래시가 나고 크래시가 나는 정확한 위치를 알 수 있지만 CI 환경에서는 "Test crashed with signal ill" 이라는 test failure 가 적힌 result bundle 을 보게 됨.

* if let / guard let else 문 활용
* nil-coalescing operator 사용 (??)
* XCTUnwrap 사용 (guard let 문의 간단화된 버전)

테스트에서 공유되는 코드에서는 assert 대신에 throw 함. 공유되는 코드는 테스트 여러 곳에서 실행되는데, negative test case 도 존재할 수 있음. 

<img width="606" alt="image" src="https://github.com/user-attachments/assets/4b25221e-f495-4900-a02a-920021a80d8b">

<img width="580" alt="image" src="https://github.com/user-attachments/assets/fc9b4795-47fb-4c7f-8874-a2ffa2b271d6">

* 코드에서 에러를 던짐. 
* `CustomStringConvertible` 프로토콜을 채택한 다음 에러 description 에 나타났으면 하는 값을 전달할 수 있음. 
* 위 전략은 로컬 환경에서 결과를 살펴보지 않는 경우 문맥과 관련된 에러를 result bundle 에서 볼 수 있게 해줌
* 로컬 환경에서 에러를 살펴볼 경우 backtrace 를 제공하기 때문에 에러가 던져진 위치를 확인할 수 있음

<img width="585" alt="image" src="https://github.com/user-attachments/assets/09221cab-9712-4bf4-978b-d2f6a3b74d05">
<img width="596" alt="image" src="https://github.com/user-attachments/assets/f3bf7ffb-3015-44f7-8d58-65ff1ee78eb5">

* 테스트할 때 상위에 discolure group 을 제공할 수 있음
* `XCTContext.runActivity` 를 통해 result bundle 에서 어떤 액션들이 해당 block 내에서 수행됐는지 알 수 있음

<img width="597" alt="image" src="https://github.com/user-attachments/assets/42606ea8-2a12-492b-a267-eba0e1dba790">

* `XCTAttachment` 를 사용해서 `runActivity` 에 첨부물을 추가할 수 있음
* CI 시스템에서 테스트할 때 추가로 로깅하기 좋음
* assert 메세지에 file path 를 추가하지 않아도 되는 이유는 attachment 에 파일 경로와 파일을 추가할 수 있기 떄문

### XCTSkip

실행되지 말아야 하는 테스트는 `XCTSkip`, `XCTSkipUnless`, `XCTSkipIf` 를 추가할 수 있음

<img width="430" alt="image" src="https://github.com/user-attachments/assets/6cb2d751-eb68-47eb-8636-a44a58cae332">

* 특정 플랫폼에서 실행할 수 없는 테스트인 경우
* 아직 구현되지 않은 기능인 경우
* 아직 고칠 수 없는 테스트이지만 failure 를 살펴보고 싶지 않을 때

result bundle 에서 스킵된 테스트를 볼 수 있음

## Tear down

1. `tearDownWithError() throws` 메서드 사용
2. 추가 로깅(failure 에 대한 분석)
3. setup 에서 변경했던 내용들 reset