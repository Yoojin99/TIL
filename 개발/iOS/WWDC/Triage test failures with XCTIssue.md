# Triage test failures with XCTIssue

WWDC20

---
* idomatic : 관용적인
---

Test failure 를 탐색하는 것은 test 모음을 유지보수하는데 핵심적인 내용 중 하나임. 진단하기가 어려우면 시간을 너무 많이 잡아먹음.

테스트가 실패할 때 아래의 질문들을 할 수 있음

* 무엇이 실패했는지
* 어떻게 실패했는지
* 왜 실패했는지
* 어디에서 실패했는지

* Swift errors in test
* Rich failure objects
* Failure call stacks
* Advanced workflows

<img width="484" alt="image" src="https://github.com/user-attachments/assets/a3c5087e-426e-4101-9ac3-ff8ce98d47f6">

* 테스트가 실패한 내용을 더 자세히 보면 위와 같이 테스트 코드 내 call stack 이 나옴
* 각 행에 마우스를 hover 했을 때 우측에 나오는 화살표를 눌러서 해당 코드로 바로 이동할 수 있음

# Swift error in test

XCTest 는 테스트 함수가 throw 하도록 하는 일반적인 코딩 패턴을 채택함.

```swift
// 이전
func testExample {
    try {
        try codeThatThrows()
    } catch {
        XCTFail("\(error)")
    }
}

// 개선
func testExample() throws {
    try codeThatThrows()
}
```

* test 가 throw 하면서 에러는 failure 메세지를 채우는데 사용됨
* test 가 throw 하면서 테스트 코드를 더 간단하게 작성할 수 있게 됨
* Swift runtime 이 개선되면서 원래 이전에는 failure 가 에러가 난 소스 코드 위치를 제공하지 않았는데 이제는 제공함.

<img width="343" alt="image" src="https://github.com/user-attachments/assets/957b9a4e-cde9-410d-96c0-4a6369dbd6eb">

새로운 API `setUpWithError()` 과 `tearDownWithError()` 는 기존의 `setUp()`, `tearDown()` 의 다른 버전.

* 호출 순서
  * `setUpWithError()`
  * `setUp()`
  * `tearDown()`
  * `tearDownWithError()`

# Rich Failure Object

XCTest 는 filaure 를 네 개의 구분된 값으로 기록했음.

<img width="340" alt="image" src="https://github.com/user-attachments/assets/4c98722d-8c19-4238-99c8-f0e0e583573e">

* failure messaage
* file path
* line number
* "Expected" flag (expected failure 는 XCTAssert 가 기록함. Unexpected failure 는 XCTest 가 테스트 코드가 던진 다뤄지지 않은 exception 을 catch 했다는 뜻)

위 값들은 XCTAssert 에서 전달돼서 `recordFailure` api 에 전달되고 이 api 는 Xcode 에서 failure 가 로깅되고 노출될 수 있게 함

## XCTIssue

위에 언급한 값들은 XCTIssue 라는 새로운 객체로 캡슐화됨

* 추가된 failure data
  * Distinct type
  * Detailed description
  * Underlying error
  * Attachments

### XCTAttachment

* 테스트가 실행될 때 임의 데이터를 캡처하는 API
* 테스트 자체 / `XCTContext` 에 의해 생성된 activity / `XCTIssue` 에 추가될 수 있음
* 테스트 failure 와 커스텀 진단을 연결할 수 있게 함


<img width="613" alt="image" src="https://github.com/user-attachments/assets/436a48c8-c5a2-4937-aa63-dc5ea4bafa25">

* `record(_issue:)` : 새로운 API

<img width="576" alt="image" src="https://github.com/user-attachments/assets/2fbc2d3e-cd03-4b4f-9b1a-19bcca343acd">

`XCTIssue` 객체를 상수 / 변수로 생성할 수 있음

## Failure Call Stacks

Call stack 은 테스트 failure 가 어디에서 발생했는지에 대한 해답을 줌

핵심 test failure data 가 포함하는 내용

* 파일 경로
* 줄 number

<img width="1221" alt="image" src="https://github.com/user-attachments/assets/864806ce-fabc-4265-b95b-577c4a918b80">

테스트 코드를 위한 어떤 함수가 여러 테스트에서 사용될 때 상황이 번거로워짐. 하지만 XCTIssue 는 call stack 을 캡처하기 때문에 복잡한 테스트 코드에 문백을 부여할 수 있음.

# Advanced workflows

1. `XCTIssue` 인스턴스를 직접 만들어서 custom assertion 을 구현할 수 있음

<img width="567" alt="image" src="https://github.com/user-attachments/assets/cf2040df-ec86-4d2c-8ea3-53551b8e5822">

* `XCTIssue` 인스턴스 생성
* 이슈에 attachment 첨부. 이 데이터는 테스트가 fail 일 때 Xcode test report 에  기록될 것임
* 커스텀 assertion 이 호출된 위치를 캡처함. 
* `record()` 를 사용해서 이슈를 로깅하고 Xcode 에 전달

2. `record(_issue:)` 오버라이딩

<img width="407" alt="image" src="https://github.com/user-attachments/assets/8c3b37b2-ff12-488e-8022-48716553eecd">

* test class 에서 기록된 failure 를 관찰하거나 막거나 수정할 수 있음
* `super.record()` 를 꼭 호출해야 함. 이래야 이슈가 기록 chain 을 따라가는 것을 보장할 수 있음
* `super.record()` 를 호출하지 않으면 이슈를 막는 것이기 때문에 로깅되거나 Xcode 에 리포트 되는 것이 없음
* 주로 위 패턴은 attachment 를 추가해서 진단할 때 도움이 되도록 하는 것을 목적으로 함