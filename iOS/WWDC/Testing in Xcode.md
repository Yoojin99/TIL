# Testing in XCode

WWDC 19

---
* demonstrate : 입증하다
* regression : 회귀
* leniency : 관대함
* consolidate : 합병하다
* mutually : 서로
* incompatible : 호환되지 않는
* populate : 채우다, ~에 거주시키다
* instruct : 지시하다
---

# Introduction to XCTest

**XCTest는 Xcode 에 내장된 테스트 자동화 프레임워크로 테스트를 할 수 있게 지원해줌.** 테스트는 소스 코드 내 버그를 찾는 중요한 단계.

## Test Pyramid

<img width="459" alt="image" src="https://github.com/user-attachments/assets/ce50e101-b438-4fa3-b633-4240d1365a9d">

테스트의 피라미드 모델은 철저함/퀄리티/실행 속도 간의 밸런스를 맞출 수 있게 해줌.

* Unit test
  * 피라미드의 근간이 됨
  * 작은 코드를 검증. e.g. 함수
  * input 을 주입해서 예상한 output 을 리턴하는지 검증
  * 짧고, 간단하고, 빠르게 실행됨
  * 많은 수를 작성해서 모든 함수를 커버
* Integration test
  * 코드의 더 넓은 부분을 검증
  * 구분되는 subsystem / 클래스들 모음을 테스트해서 다른 요소들이 함께 잘 동작하는지 테스트
  * Unit test 를 바탕으로 테스트. 더 넓은 부분을 테스트하기 전에 각각의 함수가 잘 동작하는지를 먼저 검증해야 함
  * Unit test 보다는 적은 수
  * 더 오래 걸리지만 한 번에 앱의 더 많은 부분을 테스트
* UI test
  * 앱에서 사용자가 사용하는 동작을 관찰
  * 앱이 실제로 의도한 바대로 동작하는지 테스트
  * 가장 실행하는데 오래 걸리지만 모든 것이 잘 동작하는지를 검증하기 위해 필수
  * UI 는 자주 바뀌기 때문에 유지보수가 더 어렵

## XCTest 내 test

<img width="520" alt="image" src="https://github.com/user-attachments/assets/de0623a1-662d-4d34-b4e6-f2005addec48">

* Unit test : 소스코드를 타겟팅하는 테스트. 표준 Unit test + Integration test.
* UI test : 앱 UI 에서 실행되며 앱의 end-to-end 를 테스트. 앱 내의 함수/클래스에 대한 지식에 의존하지 않고 테스트하기 때문에 black box 테스트
* Performance test : 주어진 테스트를 여러 번 실행해서 평균 소요 시간, 메모리 사용을 측정함. 

## XCTest 사용법

<img width="424" alt="image" src="https://github.com/user-attachments/assets/9d5ccc30-3023-4d28-a58a-baf9f328527e">

* `XCTest` 프레임워크, 테스트하는 타켃 import
* `XCTestCase` 클래스를 상속, 테스트 메서드는 `test` 단어로 시작해야 함
* 왼쪽의 다이아몬드가 뜨면 Xcode 가 실행할 수 있다는 뜻
  * 테스트 성공 시 초록색 체크 표시
  * 테스트 실패 시 빨간색 x 뜨며 관련 라인이 하이라이트 됨. 에러 메세지 노출됨
* assertion API 를 사용해서 소스코드 검증
  * `XCTAssertEqual` 에 전달한 문자열은 이슈를 디버깅하는데 도움을 줌

<img width="232" alt="image" src="https://github.com/user-attachments/assets/9dcff95b-9cf2-4a6d-915e-12b277207066">

* `setUp()` : 각 테스트 케이스가 실행되기 전에 실행됨 
* `tearDown()` : 각 테스트 케이스가 실행된 후에 실행됨. 데이터/앱 글로벌 상태를 변경한 것을 초기화해서 테스트가 이어지는 테스트에 영향을 주지 않도록 함

```swift
final class TestAppTests: XCTestCase {
    
    var calculator: DistanceCalculator!

    override func setUpWithError() throws {
        calculator = DistanceCalculator()
    }

    override func tearDownWithError() throws {
        calculator = nil
    }

    func testCoordinatesOfSeattle() throws {
        // given
        // when
        let city = try XCTUnwrap(calculator.city(forName: "San Francisco"))
        
        // then
        XCTAssertEqual(city.coordinate.latitude, 37.78)
        XCTAssertEqual(city.coordinate.longitude, -122.42)
    }
    
    func testSanFranciscoToNewYork() throws {
        // when
        let distanceInMiles = try calculator.distanceInMiles(from: "San Francisco", to: "New York")
        // 숫자가 정확히 일치하지 않아도 통과하게
        XCTAssertEqual(distanceInMiles, 2571, accuracy: 1)
    }
    
    func testCupertinoNotRecognized() throws {
        XCTAssertThrowsError(try calculator.distanceInMiles(from: "Cupertino", to: "New York")) { error in
            XCTAssertEqual(error as? Error, .unknownCity("Cupertino"))
        }
    }
}
```

```swift
final class TestAppUITests: XCTestCase {
    let app = XCUIApplication()

    override func setUpWithError() throws {
        continueAfterFailure = false
        app.launch()
    }

    override func tearDownWithError() throws {
    }

    @MainActor
    func testExample() throws {
        // 화면에 존재하면서 상호작용할 수 있는지 테스트
        // 메인 화면에 있는지
        XCTAssert(app.staticTexts["Cities"].isHittable)
        
        // 화면에 샌프란시스코 버튼이 있는지
        let sanFranciscoButton = app.buttons["San Francisco"]
        XCTAssert(sanFranciscoButton.isHittable)
        sanFranciscoButton.tap()
        
        
        
        // 새로운 화면으로 랜딩됐는지 확인
        // 전체 텍스트가 아예 잘리지 않고 잘 노출되는지도 나오네
        // 거리가 잘 노출되고 있는지
        XCTAssert(app.staticTexts["San Francisco"].exists)
        XCTAssert(app.staticTexts["2571.6893937835052"].isHittable)
    }
}
```

## Test organization

<img width="381" alt="image" src="https://github.com/user-attachments/assets/09b6ff44-3b95-40e8-9b22-c4228ffb68e9">

두 개의 test target

* Unit Test Target
* UI Test Target

각 test target 은 test 클래스들을 포함. Test 클래스는 test case 를 포함.

<img width="397" alt="image" src="https://github.com/user-attachments/assets/5a5f268b-1258-4794-b1af-a367ca8f4cde">

프로젝트가 커지면서 새로운 프레임워크를 추가해야 할 수도 있음.

* Framework : 자신만의 unit test target을 가져야 함
* Swift Pagcakge : 이미 test targget 을 정의하고 있음

## Code Coverage

XCTest 의 기능으로 테스트 도중 소스 코드의 어떤 줄이 얼마나 실행됐는지를 측정하고 시각화함

<img width="432" alt="image" src="https://github.com/user-attachments/assets/02e7b3e5-a425-44b5-8d97-cb0ec1e5097c">

Test plan 의 code coverage 를 활성화 시킨 후 테스트 실행하면

<img width="949" alt="image" src="https://github.com/user-attachments/assets/94fc5ca2-59c1-41d3-a506-4466a536b14e">

report navigator 에서 coverage 데이터를 확인할 수 있음.

실제로 파일 내에서 각 줄이 얼마나 실행됐는지, 어떤 줄이 실행되지 않았는지 확인할 수 있음

Code coverage 도구는 테스트에 대한 정보를 더 많이 제공하고 테스트를 더 작성할 영역을 정의할 수 있게 해줌.

## Test Early, Test Often

<img width="270" alt="image" src="https://github.com/user-attachments/assets/0516ade8-f6fa-462b-93bc-00a44ebe4334">

테스트가 코드를 충분히 커버하지 못한다고 느껴질 때가 테스트를 더 많이 작성해야 하는 때.

테스트는 이른 시점에 작성하는 것이 좋음. 소스 코드를 검증하는 테스트 모음이 있으면 새로 작성하는 함수가 믿을만하며 예상가능하다는 것을 보장할 수 있음.

# Test Plans

Test Plan 은 Xcode 11 의 새로운 기능으로 테스트를 돕는 기능.

애플은 테스트를 여러 방법으로 한 번 이상 실행하는 것을 권장함.

e.g. 앱이 다국어를 지원함. UI 테스트를 작성했다면 개발 언어(영어)에서만 통과할 가능성 높음. 독일어에서만 UI 깨지는 현상 발생할 수 있음.

이 경우 Xcode 설정을 조정해서 UI 테스트를 독일어로 실행할 수 있음. 만약 UI test 실패가 뜨지 않는다면 그 꺠지는 현상을 재현하는 UI 테스트를 작성해서 버그를 고쳐야 함.

## Running multiple ways

아래의 조건들로 여러 방법으로 테스트를 실행했을 때 버그를 발견하기 쉽다.

* 다국어
* 임의 순서
* Sanitizer 사용
* 변수 / 환경 변수

Xcode 의 scheme editor 를 통해 어떻게 앱을 실행할지 옵션을 구성할 수 있음. 하지만 이는 선택한 설정으로 오직 한 번만 앱을 실행함. 

우리가 원하는 것은 테스트를 여러 번 실행하는 것. 여기에 test plan 을 사용할 수 있음.

## Test plan

**Test plan 은 다른 설정으로 테스트를 한 번 이상 실행할 수 있게 해줌.**

* 한 곳에서 test 변수들 관리
* 여러 scheme 에서 공유
* 이전에는 한 번 이상 테스트를 실행하기 위해 scheme 을 복제했다면 이제는 하나의 test plan 을 사용하도록 scheme 을 병합할 수 있음
* Xcode, xcodebuild (CI), Xcode Server 에서 지원됨
* 이미 존재하는 프로젝트에 적용하기 쉬움

<img width="1163" alt="image" src="https://github.com/user-attachments/assets/a6c67918-16e4-4a55-8867-7d46ae97279a">

Product - Test Plan 메뉴에서 확인 가능

* Test target - test class - test method 를 확인할 수 있음
* filter 필드에서 특정 테스트 검색 가능
* 토글 버튼을 클릭해서 테스트를 disable 가능
* Options 버튼으로 test target 의 설정을 수정할 수 있음
  
<img width="1166" alt="image" src="https://github.com/user-attachments/assets/ed3fcb83-ba4a-4940-9533-706bbdccf7f6">

Configuration Tab 에서는 test 를 어떻게 실행할지, 얼마나 많이 실행할지를 관리할 수 있음

* 왼쪽 - test configuration list
  * Shared Setting : 테스트를 실행할 때 공통적으로 적용되는 옵션
* 볼드체 : 개발자가 커스텀으로 값을 준 것.

e.g. 위에서 봤던 예제처럼 다른 언어를 적용해서 두 번 테스트를 실행하도록 하려면? Configuartion 을 두 개 생성. Configuration 하나는 영어를 사용하도록, 다른 하나는 한국어를 사용하도록 지정. Region 도 한국어 configuartion 에서는 한국으로 지정.

이렇게 테스트를 실행하면 test plan 을 두 개의 configuration 으로 구성했기 때문에 테스트를 두 번 실행함.

위 전략이 CI 서버에서 모든 테스트를 실행하고 싶을 때 좋은 전략이기는 하지만 개발 단계에서는 모든 테스트를 수행할 필요는 없을 수 있음. 

<img width="289" alt="image" src="https://github.com/user-attachments/assets/f38b87dd-3962-4236-8449-4fa5a9515f7a">

이때 test diamond 에 option-click 을 해서 하나의 configuration 을 선택할 수 있음.

<img width="779" alt="image" src="https://github.com/user-attachments/assets/b826cfaa-a103-416e-b3a1-f251282d998d">

test naivgator 에서 특정 항목을 control-click 한 다음 configuration 을 설정할 수도 있음

<img width="414" alt="image" src="https://github.com/user-attachments/assets/4caa3516-0b36-4828-8848-12ca1c265dd8">

Test report 에서 여러 필터링 옵션을 통해 특정 configuration 만 볼 수도 있음.

## Test Plan File

JSON 파일로, `.xctestplan` 확장자를 갖고 있음

* 내용
  * 실행할 테스트
  * 테스트 configuration : 어떻게 테스트를 실행할지 설명하는 것 

Test plan 파일은 프로젝트 structure 내부에 포함되어 있고 하나 이상의 scheme 에서 참조될 수 있음.

## Test Configuration

* Plan 의 테스트들을 한 번 실행하는 것에 대한 내용
* 각 configuration 은 커스텀 가능한 유일한 이름을 갖고 있음 (Xcode 의 여러 메뉴에서 이름이 노출되므로 의미 있는 이름을 짓는 것이 좋음)
* 테스트를 어떻게 빌드하고 실행할지 옵션을 포함함
* Plan 의 shared settings 을 상속함

## Scheme 이 Test Plan 을 사용하도록 변경

<img width="722" alt="image" src="https://github.com/user-attachments/assets/f8febd69-7646-4f1f-b62f-2f21bd6848e9">

Scheme edit 에서 test 탭에서 test plan 을 설정할 수 있음. 새로운 plan 을 만들거나, 이미 존재하는 plan 을 scheme 이 사용하도록 설정할 수 있음

## 가능한 Use Case

<img width="592" alt="image" src="https://github.com/user-attachments/assets/985baecf-f5b7-441f-a143-8fc3a63b8778">

각 행은 plan 의 하나의 configuration 을 나타냄. 

* 하나는 address Sanitizer, 하나는 thread sanitizer 사용
* 만약 프로젝트가 C / Obj-C 코드를 포함하고 있으면 plan 이 undefined behavior sanitizer 를 사용하도록 configuration 에 추가할 수 있음
  * 이 내용은 두 configuration 에서 공통되는 내용이기 떄문에 shared settings level 에서 설정함
* 위와 같이 test plan 을 서로 호환되지 않는 sanitizer 를 사용하도록 구성하면 Xcode 는 총 두 번 프로젝트를 빌드해야 함

*sanitizer : Xcode 에 내장된 도구로서 재현이 어려운 버그를 찾아내는데 도움을 주는 도구*

<img width="407" alt="image" src="https://github.com/user-attachments/assets/6e67cb1b-d0f6-48d3-95c4-2da86ecf9ec0">

Configuration 을 사용해서 다른 언어/지역을 설정할 수 있음.

위와 같이 plan 을 설정해서 UI test 를 수행하고, Shared settings 내 localization screenshots 를 활성화해서 스크린샷을 모을 수 있음.

**Localization screenshots** 는 Xcode 11 에 등장한 기능으로 성공한 UI test 들이 스크린 샷을 갖고 있도록 하는 기능. 그리고 앱이 사용한 다국어 문자열에 대한 데이터를 모음.

<img width="591" alt="image" src="https://github.com/user-attachments/assets/c17a0ab0-7fd1-4060-9bfc-00409d1de2c8">

위와 같이 test plan 을 세 개의 다른 configuration 으로 구성 할 수 있음.

1. 메모리 안정성 확인 : address sanitizer, zombie object 설정
2. Concurrency : thread sanitizer, undefined behavior sanitizer, random order
3. 추가 진단 : Environment variable 을 통해 테스트가 로그를 수집해야 하는지 판단하게 할 수 있음. 성공한 테스트에 파일 첨부를 유지하게 할 수 있음.

<img width="954" alt="image" src="https://github.com/user-attachments/assets/5b527d02-1e5e-4727-a4ae-0071400eec99">

위와 같이 스크린샷을 유지하게 하면 갤러리에서 확인할 수 있다!

# CI Workflow

테스트를 여러 다른 configuration 을 설정하고 실행하기 좋은 곳은 테스트를 빌드하고 실행하는 것을 자동화하는 CI 단계임.

실제 로컬에서는 각 테스트가 통과하는지에 집중하고, CI 에서는 coverage 를 넓히기 위해 여러 디바이스들에서 모든 테스트를 실행함.

* Xcode 로 CI
  1.  Xcode Server (Xcode 에 내장되어 있음) : 최소한의 configuration 으로 앱을 빌드하고 테스트할 수 있는 환경 조성 가능.
  2.  Custom : 커스텀 설정. 커스텀 요구사항이 필요하고 이미 있는 인프라에 통합할 필요가 있는 경우 사용.

## Custom CI

<img width="351" alt="image" src="https://github.com/user-attachments/assets/11d95809-bbd4-4315-a024-b30da780bfcc">


1. Step 1 : Build tests. 테스트를 전용 빌드 머신에서 빌드함
2. Step 2 : Run tests. 빌드된 테스트를 디바이스 모음에서 테스트. 이 디바이스들은 테스트 실행을 위한 두 번째 머신에 연결되어 있음. (여기까지 테스트는 빌드와 테스트 결과를 생성함.)
3. Step 3 : Populate issue tracker. 실패한 빌드/테스트 결과로 issue tracker 를 채움
4. Step 4 : Track code coverage. Code coverage 트래킹

## 테스트 빌드, 실행

xcodebuild 를 사용. `xcodebuild` 는 Xcode 의 CLI.

테스트를 실행하는 두 가지 방법

1. 한 번에 빌드하고 테스트

```
$ xcodebuild test
-project MyProject.xcodeproj # 프로젝트 이름
-scheme MyScheme # 스킴
-destination 'platform=iOS,name=My iPhone' # 테스트가 실행될 기기
```

2. 빌드 후 테스트

빌드와 테스트를 두 단계로 분리함. 하나의 머신이 빌드, 다른 하나의 머신이 테스트를 전담할 때 사용

```
$ xcodebuild build-for-testing
-project MyProject.xcodeproj 
-scheme MyScheme 
-destination 'platform=iOS,name=My iPhone'

# 위 명령어를 통해 테스트에 필요한 build product, .xctestrun 파일이 생성됨.
# xctestrun 파일은 build product 를 설명하고 테스트 할 때 xcodebuild 에 지시하는 manifest


$ xcodebuilde test-without-building
-xctestrun MyProject.xctestrun
-destination 'platform=iOS,name=My iPhone'
```

---

xctestrun 파일을 직접 만들어서 빌드 말고 테스트 도중에 어떻게 할 지를 통제할 수도 있음.

<img width="623" alt="image" src="https://github.com/user-attachments/assets/f5531fc3-2e44-4602-830c-b7dcb3000d42">

파일 형식에 대해 더 보려면 man 페이지 확인. 형식은 Xcode 릴리즈 버전에 따라 바뀔 수 있기 때문에 빌드, 테스트를 위한 Xcode 에 같은 버전 사용.

<img width="571" alt="image" src="https://github.com/user-attachments/assets/320af30f-0301-46bf-bd0e-05ad90ab79bb">

여러 destination 에서 동시에 테스트도 가능

<img width="288" alt="image" src="https://github.com/user-attachments/assets/cc35fcd3-3dd7-478d-bd43-6c39755a6069">

* `-showTestPlans` : 한 스킴에 여러 개의 test plan 이 있을 경우 모든 test plan 목록을 보여줌
* `-testPlan` 옵션을 통해 실행할 test plan 을 지정. e.g. 모든 테스트를 포함하는 테스트 plan 하나, smoke test 를 수행하는 테스트 plan 하나가 있을 때 지정해서 사용

## Result Bundle

테스트 빌드, 실행의 결과를 설명하는 구조화된 데이터 파일로, Xcode 가 생성하는 파일임.

* 내용
  * 빌드 로그 : 어떤 target, source 파일이 컴파일 됐는지
  * Test report
    * Code coverage report : 테스트에서 어떤 코드가 커버됐는지
    * Test attachment : XC test attachment API 를 사용해서 테스트에 의해 생성된 첨부물

Result bundle 을 생성하려면 `xcodebuild` 에 result bundle path 옵션을 전달하면 됨.

```
$ xcodebuild test
-project MyProject.xcodeproj
-scheme MyScheme
-resultBundlePath /path/to/ResultBundle.xcresult # 여기에 result bundle path 를 지정
```

Result bundle 은 Xcode 11 에서 파일 형식이 다시 디자인 됐음

* 디자인 변경된 후의 장점
  1. 새로운 형식은 디스크에서 효율적이도록 최적화됨. 애플이 테스트할 때는 이전 형식보다 4배 작아졌다고 함. CI에서 result bundle 이 빈번하게 생성되고 저장되는 경우 유용함
  2. Result bundle 을 Xcode 에서 바로 열 수 있음
  3. CI 셋업에서 result bundle 의 컨텐츠에 코드상으로 접근할 수 있는 방법이 생김

<img width="718" alt="image" src="https://github.com/user-attachments/assets/e5876c65-bb6e-4b96-81d5-0fb22b060234">

Result bundle 을 열어서 report, build log, code coverage 확인 가능

### xcresulttool

코드상으로 result bundle 컨텐츠에 접근하려면 `xcresulttool` 을 사용

`xcresulttool` 은 result bundle 에 포함된 데이터에 접근할 수 있게 해줌. 

데이터를 JSON 으로 내보내고 JSON 의 형식은 공식적으로 문서화되고 버전화된 버전임. `xcresulttool` 을 사용해서 이슈 트래커에 빌드/테스트 중에 실패한 것들을 전달할 것임.

```
$ xcrun xcresulttool get test-results summary --path ResultBundle.xcresult --compact
```

<img width="565" alt="image" src="https://github.com/user-attachments/assets/ac8a0309-4a67-47f4-9824-b3cf850ee239">

여러 테스트 결과를 추출하려면 path 에 resultBundle 경로를 넣고 다양한 커맨드를 사용. 
xcresulttool의 큰 장점은 생성하는 JSON 이 공식 형식이라는 것임.

xcresulttool 에 대한 자세한 내용을 확인하려면 man page 확인.

### xccov 와 code coverage report

Xccov 는 code coverage report 에 코드상으로 접근해서 사람이 읽을 수 있는 텍스트나 JSOn 형태로 제공함.

```
$ xcrun xccov view --report ResultBundle.xcresult
```

<img width="565" alt="image" src="https://github.com/user-attachments/assets/4064b315-ca4d-4a3d-acd4-54c3010b2655">

결과물에서 모든 target, 소스 파일, 함수/메서드에 대한 coverage 를 확인할 수 있음.

<img width="507" alt="image" src="https://github.com/user-attachments/assets/5863981c-6eeb-4520-ac88-0073cbf9c34a">

`diff` : 두 개의 report 를 비교해서 coverage 가 더 좋아졌는지 나빠졌는지 확인함

## 정리

<img width="353" alt="image" src="https://github.com/user-attachments/assets/adaa67a3-4267-430e-88d0-5aca1d84828d">

1. Build tests : `xcodebuild build-for-testing`
2. Run tests : `xcodebuild test-without-building` ... `resultBundlePath ResultBundle.xcresult`(결과물)
3. Populate issue tracker : `xcresulttool` 을 사용해서 result bundle 에서 빌드와 테스트 실패한 내용들을 추출해서 issue tracker 에 넣었음
4. Track code coverage : `xccov view`
