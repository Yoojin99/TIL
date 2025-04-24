# Testing Tips & Tricks

WWDC 18

---

thoroughness : 철저한 | We discussed the pyramid model as a guide to how to structure a test suite, balancing thoroughness, understandability.
suite : 모음? | 스위트
complement : 보완하다, 보충하다
discrete : 별개의, 분리된
mitigate : 완화시키다

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

### Unit Test

Request 준비, Response 파싱 단계 의 unit test 작성

테스트하기 좋게 만들기 위해 기존의 view controller 메서드에서 분리해서 
별도의 구조체를 생성, 두 개의 분리된 메서드로 만들었음.

<img width="531" alt="image" src="https://github.com/user-attachments/assets/5c47ae55-eb5e-454d-801d-e046b4ecca6d">

* 효과
  * 디커플링
  * 각각이 어떤 값을 input 으로 받고 결과값을 리턴하는 순수함수가 됨. Side effect X

#### 테스트

<img width="549" alt="image" src="https://github.com/user-attachments/assets/e976349a-0142-4d76-bf34-b5125ac8ec17">

<img width="462" alt="image" src="https://github.com/user-attachments/assets/707cb777-8726-4007-aad6-d194abf651eb">

* 예시 input, mock JSON 을 넣어서 테스트

### Integration Test

URLSession Integration Test 작성

<img width="439" alt="image" src="https://github.com/user-attachments/assets/14c5b881-81fd-4baa-b2cc-fb6937281989">

<img width="579" alt="image" src="https://github.com/user-attachments/assets/81b60548-1865-4d8f-8e6b-f5b872a2df3f">

View controller 에서 분리해서 APIRequest 프로토콜 생성 및 그것을 사용하는 APIRequestLoader 클래스 생성.

`loadAPIRequest` 메서드는 `APIRequest` 를 받아서 URLSession 에 주입함.

#### URLProtocol

<img width="495" alt="image" src="https://github.com/user-attachments/assets/9a5d5286-03df-4c49-9932-5865550bcd96">

* High level API : URLSession. 네트워크 요청을 수행
* Lower-level API : `URLProtocol`
  * 네트워크 커넥션 생성, request 하고 response 를 받는 작업들을 수행함.
  * URL 로딩 시스템의 extensibility point 를 제공하기 위해 상속받을 것을 기대하고 디자인됐음.
* Build-in Protocols Subclasses : 일반적인 프로토콜 e.g. HTTPS
  * 테스트할 때 override 해서 request 가 요청되고 있는지 확인, mock response 제공
* `URLProtocolClient` : `URLProtocol` 이 시스템에 진행사항을 업데이트하기 위해 사용하는 프로토콜

#### 테스트

Integration test 를 작성해서 URLSession API 와의 상호작용이 잘 되는지를 테스트

<img width="323" alt="image" src="https://github.com/user-attachments/assets/8318aa62-d6a3-46f6-bc6e-a48497cc0650">

<img width="517" alt="image" src="https://github.com/user-attachments/assets/f81d9c7a-6d69-4c7c-bb9d-416148258811">

`MockURLProtocol` 을 생성해서 `URLProtocol` 을 상속. 

* `canInit` 을 override 해서 시스템이 제공하는 request 에 관심이 있음을 명시. 
* `canonicalRequest` 를 구현
* 대부분의 액션은 `startLoading()`, `stopLoading()` 에서 발생

<img width="576" alt="image" src="https://github.com/user-attachments/assets/2204d60c-aba3-40d1-bbee-a10f4f9505ab">

위 `URLProtocol` 에 hook 을 제공하기 위해 `requestHandler` 클로저 제공

<img width="493" alt="image" src="https://github.com/user-attachments/assets/9d6d3cd3-40cd-4367-9e36-55ac7288b63b">

테스트 `setUp()` 부분을 위와 같이 작성

<img width="589" alt="image" src="https://github.com/user-attachments/assets/2639f9b0-60d1-42d4-8d1d-020fb814e345">

`requestHandler` 에서 request 의 조건들을 확인한 후 가짜 응답 제공

이 integration test 를 통해서 코드가 함께 잘 동작하는지 확인할 수 있음. 가령 data task 작업에 `resume` 을 호출 안했다면 fail 이 남.

### End-to-End Test

end-to-end 테스트는 UI 테스트로 진행 가능

End-to-end 테스트는 실패가 났을 경우 문제의 시작점이 어디인지 찾기가 어려움. 

<img width="413" alt="image" src="https://github.com/user-attachments/assets/c02e418b-947d-433a-afc1-8ff5ba9f7052">
<img width="407" alt="image" src="https://github.com/user-attachments/assets/ccac547e-935b-4be7-b576-8b4c0adeb589">

* 문제를 완화시키기 위한 방법
  * Mock Server 사용해서 실제 서버 대신 UI 테스트할 때 사용(view까지 업데이트됨을 확인) : 앱에 전달하는 데이터를 컨트롤 할 수 있어서 UI 테스트의 안정성이 생김
  * 그래도 실제 서버에 요청하는 테스트는 필요함 : Unit test 번들에서 실제 서버에 요청하도록 할 수 있음(response까지 확인). 이를 통해 서버가 앱이 생성하는 요청을 받고 있고, 서버 응답을 실제로 파싱할 수 있는지를 UI 를 동시에 테스트하지 않으면서 검증할 수 있음. 즉 실제 서버에 요청/응답 받는 과정까지만 보고 UI 검증은 하지 않는거

### 네트워크 테스트 정리

* Unit test 를 활성화하기 위해 코드를 더 작고 독립된 단위로 분리
* `URLProtocol` 을 사용해서 가짜 네트워크 요청 생성
* 테스트 피라미드 구조를 따르는 전략 통해 균형잡힌 테스트와 앱 안정성 향상

## Notifications

여기서 얘기하는 notification : foundation-level notification (NSNotification)

notification 은 일대다 소통 매커니즘이라 하나의 notification 이 보내지면 여러 관찰자가 받을 수 있음. 따라서 notification 을 **고립된** 형태로 테스트해서 **side effect** 가 없는 테스트를 작성해야 함.

### 관찰 테스트

<img width="597" alt="image" src="https://github.com/user-attachments/assets/52136847-5a45-40f8-9149-49bde977efe9">

`NotificationCenter.default` 를 사용

<img width="476" alt="image" src="https://github.com/user-attachments/assets/71d10869-4a54-44a7-88da-7d7c4a1ea4c9">

`NotificationCenter.default` 에 post 하면 side effect 발생 가능. 고립시키는 것이 필요함

e.g. `appDidFinishLaunching` notification 은 여러 레이어에서 관찰될 수 있기 때문에 side effect 가 생길 수 있고, 테스트가 느려질 수도 있음

* Notification Observer 테스트
  * `.default` 대신 별도의 `NotificationCenter` 인스턴스 생성
  * 이를 통해 범위를 한정하고 추가 side effect 발생 방지

<img width="542" alt="image" src="https://github.com/user-attachments/assets/282e1367-ec65-40d9-b08a-ccc9cf588476">

* notificationCenter 프로퍼티 생성
* notificationCenter 주입받음
* `.default` 교체

<img width="535" alt="image" src="https://github.com/user-attachments/assets/55a4f9e4-666d-4f08-9523-07ab43eaf016">

NotificationCenter 인스턴스를 생성해서 테스트에 사용

### Post 테스트

<img width="476" alt="image" src="https://github.com/user-attachments/assets/116d085d-a1c8-4142-bd21-a0cf8ecb9574">

마찬가지로 `.default` 사용중

<img width="555" alt="image" src="https://github.com/user-attachments/assets/47c4d75f-4c99-451d-bcae-542229199153">

위 테스트를 `XCTNSNotificationExpectation` API 를 사용해서 NotificationCenter observer 를 생성할 수 있음

<img width="530" alt="image" src="https://github.com/user-attachments/assets/e1d3a0c9-e542-4cf0-82a3-e230cb328eb5">

위 테스트에서는 timeout 이 0인데, 이미 조건이 충족됐음을 기대하기 때문

## Mocking with protocols

앱 내 / SDK 에서 전달받은 클래스와 상호작용하는 클래스를 테스트하기는 어렵다. 그 이유는 그 클래스에 접근할 수 없는 경우가 있기 때문. 이때 프로토콜을 사용해서 상호작용을 mocking 할 수 있음.

### 수정 전

<img width="502" alt="image" src="https://github.com/user-attachments/assets/80858ecf-f78b-4f3e-90fd-572998e33102">

`CLLocationManager` 를 생성하고 delegate 를 self 로 설정.

<img width="461" alt="image" src="https://github.com/user-attachments/assets/a82eb30d-6248-4014-9399-faf1cbd97ec0">

현재 위치를 받아 Point of interest 인지 확인하는 completion block. 
`locationManager.requestLocation()` 을 호출하면 현재 위치를 받아 self 의 delegate 메서드를 호출하게 됨

<img width="603" alt="image" src="https://github.com/user-attachments/assets/3a3614ef-acc0-4616-8903-817dbba56186">

Delegate 메서드

<img width="569" alt="image" src="https://github.com/user-attachments/assets/9c86e9d5-c530-4ed7-b2a9-bc3c687fb160">

* 문제
  * `CLLocationManager` 의 `requestLocation()` 이 언제 호출되는지 모름. 그 이유는 이 메서드는 매니저에 속해있고 우리 코드에서 작성한게 아니기 때문이다. 
  * `CoreLocation` 은 사용자 권한을 요구하는데 만약 이전에 권한을 주지 않았다면 요청 다이얼로그가 뜨게 됨. 이는 기기 상태에 따라 테스트 환경이 좌지우지 된다는 것. 

### Subclass?

위와 같은 상황에서 외부 클래스를 상속하고 호출하는 메서드를 override 하는 방식을 고려할 수 있음. 동작은 하지만 까다롭다.

* 문제
  * SDK 의 어떤 클래스들은 상속이 불가능하게 디자인되어 있을 수 있고 상속을 해도 다르게 동작할 수 있다.
  * 여전히 부모 클래스의 init 을 불러야 하는데 이는 우리가 override 할 수 있는 코드가 아님
  * 가령 `CLLocationManager` 의 다른 메서드를 호출하도록 코드를 수정해야 하면 하위 클래스에서도 똑같이 override 하도록 수정해줘야 함
  * 즉 상속에 의지하면 컴파일러는 내가 다른 메서드를 호출할 때 알려주지 않기 때문에 까먹기 쉽고 테스트는 실패하기 쉽다.

### 프로토콜 사용하기

1. 새로운 프로토콜 정의

<img width="506" alt="image" src="https://github.com/user-attachments/assets/da30f6d0-416a-4044-9519-6fd17311f187">

* `LocationFetcher` 프로토콜 정의, `CLLocationManager` 에서 사용하는 메서드, 프로퍼티와 정확히 일치하는 메서드, 프로퍼티를 정의
* `CLLocationManager` 가 프로토콜 채택하도록 설정
* 기존 `LocationManager` 사용하던 걸 `LocationFetcher` 로 대체
* 이니셜라이저에 기본 인자값을 설정해서 기존 앱 코드가 에러나지 않게 설정

2. Delegate 프로토콜 정의

<img width="581" alt="image" src="https://github.com/user-attachments/assets/751427fb-a765-46b4-ba1a-319b87ffd0c9">

* `LocationFetcherDelegate` 를 생성
* `CLLocationManagerDelegate` 에서 호출하던 메서드와 유사한 메서드 정의
* `CLLocationManager` 내 `LocationFetcherDelegate` 타입 프로퍼티 설정

<img width="584" alt="image" src="https://github.com/user-attachments/assets/3f0d6103-c5f1-4728-a324-cddd91d9bfc3">

* `CLLocationManagerDelegate` 는 새롭게 생성한 `LocationFetcherDelegate` 를 모르니 해당 delegate 의 메서드를 호출하도록 수정

<img width="570" alt="image" src="https://github.com/user-attachments/assets/c31d7911-4a96-4670-86b8-516fecbf2e80">

* Mock 객체 생성

<img width="500" alt="image" src="https://github.com/user-attachments/assets/c7da3bdb-c75a-46df-b091-1aaffc9053ae">

* Mock 객체를 통해 현재 location mock 가능

### 정리

* 프로토콜로 mocking
  1. 외부 interface 를 표현하는 프로토콜 정의
  2. 외부 클래스가 해당 프로토콜을 채택하도록 함
  3. 외부 클래스 사용처를 프로토콜로 모두 대체함
* 프로토콜로 delegate mocking
  1. Delegate 프로토콜 정의, delegate 에서 호출하던 메서드와 비슷한 signature 로 정의
  2. 실제 타입을 delegate 타입으로 교체
  3. 프로토콜 내부에서 delegate 프로퍼티 명명
  4. 원래 타입 extension 에서 mock delegate 프로퍼티를 구현하고 교체함
* 장점
  * 상속에 비해 더 많은 코드를 작성하지만 안정성 있고 코드를 확장해도 깨지기 어려움. 그 이유는 컴파일러가 내가 코드에서 호출하는 코드들이 무조건 프로토콜에 정의되게끔 강제하기 때문

## Test 실행 속도

테스트가 비동기이거나 타이머를 사용하는 경우 시간이 너무 오래 걸림.

테스트를 할 때는 딜레이를 무시하도록 할 수 있음.

<img width="539" alt="image" src="https://github.com/user-attachments/assets/725b7387-d968-4d43-83db-cf59d48cc1e8">

위 코드는 10초마다 새로운 장소를 보여주는 코드. Timer 사용

<img width="431" alt="image" src="https://github.com/user-attachments/assets/3cca8d88-6a2b-4a08-a634-4e1449b15205">

Unit test 를 작성하는 경우 넉넉하게 11초를 줘서 테스트를 하는데 너무 오래 걸림

코드상으로 delay 를 외부에서 변경가능하게 설정하는 방식으로 테스트에서는 1초를 주고 설정할 수도 있으나 이는 근본적인 해결책이 아님. 

* 문제
  * 딜레이가 짧아졌을 뿐 여전히 딜레이 존재
  * 테스트하는 코드는 여전히 시간에 의존하기 떄문에 딜레이가 짧아질수록 테스트는 점점 믿을 수 없어짐. 그 이유는 CPU 가 스케줄링하는 정확도에 더욱더 의존하게 되기 때문임.

* 해결 방법
  * 딜레이 매커니즘 정의 (`Timer`, `DispatchQueue.asyncAfter`)
  * 위 매커니즘을 테스트할때 mock. Delay 된 액션을 즉각 호출하고 딜레이 무시

기존 코드

`Timer.scheduledTimer()` 는 두 가지 일을 함

1. Timer 생성
2. Timer 를 현재 run loop 에 추가

<img width="497" alt="image" src="https://github.com/user-attachments/assets/7feaf038-de98-4d19-9863-c59b84877d4c">

테스트하기 좋게 하기 위해서는 두 단계를 분리함. 위에서 봤던 protocol 로 mocking 하는 전략을 runLoop 쪽에도 적용할 수 있다.

<img width="366" alt="image" src="https://github.com/user-attachments/assets/49002af9-595c-4fe7-af21-222eb7763349">

`TimerScheduler` 라는 프로토콜 생성, 기존에 코드에서 사용하던 API 와 같은 명세의 메서드 정의.

<img width="496" alt="image" src="https://github.com/user-attachments/assets/a72c88cf-1809-4591-890e-73272d6c1697">

기존에 `RunLoop` 를 사용하던 곳을 프로토콜로 대체

<img width="377" alt="image" src="https://github.com/user-attachments/assets/59349142-830f-4ebc-be53-136bdd197fdc">

프로토콜을 채택한 mock 객체 생성

<img width="474" alt="image" src="https://github.com/user-attachments/assets/c590fd53-96b3-43d2-80e4-93ec3f964fe5">

Unit test 에서 mock 객체에 전달하는 핸들 클로저에 딜레이를 무시하도록 설정.

장점은 timer 의 delay 를 검증할 수도 있다는 점.

## NSPredicateExpectation

얘네들은 direct callback 매커니즘보다는 polar 에 의존하고 있기 때문에 다른 expectation 클래스만큼 성능이 좋지 않음. 주로 UI 테스트에서 사용해야 함.

Unit test 에서는 XCTestExpectations, NSNotification, KVOExpectations 를 사용

## 테스트 할 때 앱 실행 최적화

테스트할 때 앱이 빠르게 실행되는 것을 보장하는 것이 좋음

테스트하는 경우에는 실제 환경에서 수행하는 setup 작업들이 불필요한 경우가 있음. e.g. view controller 로딩, network request 수행, 분석 패키지 구성

XCTest 는 AppDelegate 의 did finish launching 메서드가 리턴되기까지 기다렸다가 테스트를 실행하기 때문에 앱 실행시 오래 걸린다면 테스트 할 때 이런 작업들을 무시하게 할 수 있음.

<img width="915" alt="image" src="https://github.com/user-attachments/assets/c59951ef-1d7b-444f-942e-110de58a8aca">

scheme edit > test > test plans 에서

<img width="774" alt="image" src="https://github.com/user-attachments/assets/c4fe1dcd-b206-4407-b3db-4709856e2df9">

test scheme action 쪽에 environment variables 를 설정하고 AppDelegate 코드에서 

<img width="865" alt="image" src="https://github.com/user-attachments/assets/6282db3b-fe59-45d9-9772-ecd3d893f6aa">

분기 처리를 통해 unit test 중인지 아닌지를 분기할 수 있음