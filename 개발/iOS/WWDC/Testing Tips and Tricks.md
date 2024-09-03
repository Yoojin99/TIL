# Testing Tips & Tricks

WWDC 18

---

thoroughness : 철저한 | We discussed the pyramid model as a guide to how to structure a test suite, balancing thoroughness, understandability.
suite : 모음? | 스위트
complement : 보완하다, 보충하다
discrete : 별개의, 분리된

---

1. Testing network requests
2. Working with notifications
3. Mocking with protocols
4. Test execution speed

## 테스트 피라미드

<img width="372" alt="image" src="https://github.com/user-attachments/assets/0160fdee-ee69-48d2-91cc-b5d7e0b94752">

이상적인 테스트 suite 은 위와 같다.

* Unit test
  * 대부분을 차지
  * 개별 클래스, 메서드 테스트
  * 간단함
  * 문제가 생겼을 때 분명한 에러 메세지 생성
  * 매우 빠르게 실행돼서 몇 분 안에 몇 백 ~ 천 개를 실행할 수 있음
* Integration test
  * Unit test 를 보완함
  * 분리된 subsystem / cluster / 클래스 테스트
  * 같이 동작했을 때 잘 동작하는 지 확인
  * 몇 초 이상 안 걸림
* End-to-end system test
  * 대부분 UI test
  * 기기에서 end-user 가 하듯이
  * 모든 요소가 잘 연결되어 있는지, os/외부 리소스와도 잘 상호작용하는지 확인

## 네트워크 작업

<img width="582" alt="image" src="https://github.com/user-attachments/assets/b25e63ca-ef13-4434-94f3-1d9d495a7093">

위와 같은 플로우로 네트워크 작업을 수행했을 때, 예시에서는 UIViewController 에서 위 4가지 작업을 모두 한 메서드에서 수행함.

<img width="538" alt="image" src="https://github.com/user-attachments/assets/f3c8610a-7e68-4238-8782-abe1fc7aed4b">

위와 같이 작성하면 모든 작업을 단 15줄의 코드로 수행할 수 있지만,
**한 메서드에 모두 집어넣음으로써 코드의 유지보수성, 테스트성이 저하됨**

목표는 플로우의 **각 단계에 집중하는 unit test 를 작성**하는 것.


