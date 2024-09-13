# XCTSkip your tests

WWDC 20

몇몇 테스트 (특히 integration test) 는 쉽게 mock 되지 못하는 요구사항 / 의존성이 있음

* IPhone 에서 수행할 수 없는 iPad 전용 기능
* 오래된 OS 에서는 실행할 수 없는 API
* 서버가 임시점검 중

테스트가 빠르게 성공 / 실패하게 할 수 있지만 둘 다 좋은 방법이 아님

* 성공 시킬 경우 : 실제로 검증되지 않았는데 성공 처리해버림
* 실패 시킬 경우 : 실제로 문제가 아닌데 실패가 되고 에러를 살피는 리소스가 사용됨

<img width="452" alt="image" src="https://github.com/user-attachments/assets/9407a7a5-1ee6-47b4-89d1-0885d0f7c402">

테스트는 성공 / 실패 / 스킵 될 수 있음

<img width="518" alt="image" src="https://github.com/user-attachments/assets/c44bc19c-2a72-41da-8fc3-1680006f10f5">

* 위 예제에서는 특정 플랫폼의 특정 버전이상이 아니면 테스트를 스킵하도록 함
* 플랫폼 / 버전을 만족하지 못한 경우 스킵이 됨
* Test navigator / test report 모두에서 skip 됐음을 확인할 수 있음

*유용한 단축키 : Control-Option-Command-G : 이전 test action 반복*

<img width="444" alt="image" src="https://github.com/user-attachments/assets/b83b6950-aa6f-44bf-9003-12d1248140ec">


* `XCTSkipIf` : 표현이 true 인 경우 skip
* `XCTSkipUnless` : 표현이 false 인 경우 skip
* `throw XCTSkip` 으로 구조체를 바로 throw 할 수도 있음

