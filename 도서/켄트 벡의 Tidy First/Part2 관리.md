# 2. 관리

코드 정리를 개인 개발 흐름에 맞추는 방법

* 코드 정리는 언제 시작하는지
* 코드 정리는 언제 멈추는지
* 구조 변경 / 로직 변경을 어떻게 결합할 수 있는지

## 16. 코드 정리 구분

PR 을 사용하는 팀이라고 가정.

* 발생할 수 있는 상황
  * 로직 변경 코드 + 코드 정리 합쳐진 PR : 너무 길다!
  * 코드 정리만 담긴 PR : 무슨 의미로 만들었는지 모르겠다!

**코드 정리는 별도의 PR 로 만들고 PR 당 몇 개의 코드 정리만 넣기**

### 코드 정리 단계

1. 코드를 전반적으로 살펴보며 '변경을 구분하지 않은 상태에서 다수의 변경'

  * <img width="158" alt="image" src="https://github.com/user-attachments/assets/8a078b3c-eaca-42b0-a1f7-565e5b2df3bd">
  * 어떤 지점을 어떻게 변경해야겠다라는 목적 없이 눈에 거슬리는 여러 지점에서의 변경
  * e.g. if 조건문 을 변경하는 도중 변수명이 잘못됐음을 알고 이름 변경하고 다시 if 문으로 돌아가기
2. 코드를 자세히 보면서 초점 맞추기
  * <img width="141" alt="image" src="https://github.com/user-attachments/assets/4b93febe-bdf1-4571-8979-85a7409ba46a">
  * 변경 대상들이 로직/구조 중 어떤 변경에 해당하는지 파악. 
  * 어떻게 바꿔야겠다는 계획 없이 동작/구조 다른 두 종류의 변경이 함께 존재함을 인식하고 있는 단계
3. 각 변경의 순서 파악
  * <img width="149" alt="image" src="https://github.com/user-attachments/assets/14727fe6-a581-42d8-a789-8e3fb78fa26e">
  * 코드 정리 / helper 생성으로 코드 변경을 더 쉽게 만들면서 이후에 진행할 코드 변경 순서 보임
  * 한 개의 PR 에 모두 포함된 상태
4. 변경 사항 나누기
  * <img width="293" alt="image" src="https://github.com/user-attachments/assets/bdede645-3064-4672-b3c0-fc6c9ebf90af">
  * 변경사항들을 나눠 별도의 PR 로 생성
  * 코드 정리 / 로직 변경을 전환할때마다 새 PR 생성

PR 크기에 따른 장단점
* 큰 PR
  * 장점 : 전체 그림 파악 쉬움  
  * 단점 : 검토 입장에서는 검토해야 하는 양이 많기 때문에 유용한 피드백 주기 어려울 수 있음
* 작은 PR
  * 장점 : 검토 시간 단축, 소소한 피드백 가능
  * 단점 : 무시될 염려 있음. 검토를 위한 시간을 내기 / 검토를 하는데 시간이 오래 걸림. 검토자가 검토를 포기할 수 있음. 여러 검토자들이 검토할 부분을 분배하기 어려움. 첫 번째 검토자가 리뷰를 해서 수정을 요청한 경우 다른 리뷰어들이 리뷰를 안하려고 할 수 있음.

[PR 크기에 대한 글-레퍼런스에 관련 자료 많음](https://bssw.io/blog_posts/pull-request-size-matters)

**작은 PR 은 작은 곳에 초점을 맞춰서 더 빠른 검토를 장려하고 검토 시간을 단축함.** 검토 시간이 단축되며 더 작은 PR 만들도록 동기부여가 됨. 

*책에서는 작은 코드 정리에 익숙해져서 절대적으로 안전하게 작업하는데 익숙해지면 코드 정리 PR을 따로 검토하지 않는 시도를 해보라 함. 대기 시간이 줄어들어 더 작게 PR 을 만들도록 장려하는 효과가 생김.*

* 대기 시간은 코드 정리 PR이 승인 받기까지를 기다리는 시간인가?
* 코드 정리만 따로 pr 로 올리는 것?
* 코드 정리 PR 을 검토하는 것? 

## 17. 연쇄적인 정리

코드를 계속 정리하고 싶은 충동을 관리하는 것 또한 기술. 작은 단계로 나눠 코드를 정리하면서 각 단계를 최적화하기. 

⬇️ 코드 정리 - 코드 정리 이후 따라 올 수 있는 코드 정리

* 보호 구문
  * 보호 구문을 빼면 보호 조건을 helper / 설명하는 변수로 추출할 수 있음
  ```swift
  // 1. 보호 구문 빼기
  if status?.isUserLoggedIn {
    if store.user?.leftSubscriptionDay > 0 {
        print("사용자가 로그인하고 구독이 활성화되어 있습니다.")
    } else {
        if store.contents.count > 0 {
            print("사용자가 로그인했지만 구독이 없습니다. 콘텐츠를 사용할 수 있습니다.")
        } else {
            print("사용자가 로그인했지만 구독이 없으며 콘텐츠를 사용할 수 없습니다.")
        }
    }
  } else {
    print("사용자가 로그인하지 않았습니다.")
  }

  // 2. 설명하는 변수로 추출
  let isUserLoggedIn = status?.isUserLoggedIn ?? false
  let hasSubscription = (store.user?.leftSubscriptionDay ?? 0) > 0
  let isContentAvailable = store.contents.count > 0

  guard isUserLoggedIn else {
    print("사용자가 로그인하지 않았습니다.")
    return
  }

  if hasSubscription {
    print("사용자가 로그인하고 구독이 활성화되어 있습니다.")
    return
  }

  if isContentAvailable {
    print("사용자가 로그인했지만 구독이 없습니다. 콘텐츠를 사용할 수 있습니다.")
    return
  }

  print("사용자가 로그인했지만 구독이 없으며 콘텐츠를 사용할 수 없습니다.")
  ```
* 안 쓰는 코드
  * 안 쓰는 코드를 제거하면 코드를 읽는 순서에 맞춰 정렬하고 응집도를 높일 수 있는 배치 보임
* 대칭으로 맞추기
  * 여러 곳에서 사용되는 코드를 대칭으로 맞추면 유사한 코드들이 드러남. 책에서는 유사한 코드들을 묶여진 순서대로 읽을 수 있다고 함. 
  ```swift
  func presentMainView() {
      if !isValid(url) {
          return
      }
      
      navigationViewController.pushViewController(MainView(url))
  }

  func presentSubView(_ crossSelling: CrossSelling) {
      guard isValid(url) else {
          return
      }
      
      let subView = SubView(url)
      navigationViewController.pushViewController(subView)
  }

  // 1. 대칭으로 맞추기
  func presentMainView() {
      guard isValid(url) else {
          return
      }
      
      let view = MainView(url)
      navigationViewController.pushViewController(view)
  }

  func presentSubView(_ crossSelling: CrossSelling) {
      guard isValid(url) else {
          return
      }
      
      let view = SubView(url)
      navigationViewController.pushViewController(view)
  }

  // 2. 유사한 코드를 묶기
  func showView(action: Action) {
      let url = action.url
      guard isValid(url) else { return }
      
      // 목차?
      let viewToShow = {
          switch action {
          case .main: return MainView(url)
          case .subView: return SubView(url)
          }
      }()
      
      navigationViewController.pushViewController(viewToShow)
  }
  ```
* 새로운 인터페이스로 기존 루틴 부르기
  * 새 인터페이스를 만들어서 기존 루틴을 부를 때 별도의 개발자 도구가 없으면 기존 루틴 호출되는 부분을 일일이 고쳐야 함. 한 곳을 고치면 뒤따라 정리되어야 하는 곳 발생 
  * Xcode 는 개발자 도구를 지원
* 읽는 순서
  * 읽는 순서를 정리하면 멀리 떨어져 있는 코드들을 대칭으로 맞출 기회 생길 수 있음
  ```swift
  func a() {
      // 데이터 준비, 전처리, 세부사항
      let address = user.address
      let profileImage = user.image
      let name = user.name
      
      if name.count <= 0 {
          return
      }
      
      doSth1()
  }

  func b() {
      guard user.name.count > 0 else { return }
      
      // 데이터 준비, 전처리, 세부사항
      let address = user.address
      let profileImage = user.image
      let name = user.name
      
      doSth2()
  }

  // 1. 읽는 순서 변경
  func a() {
    if user.name.count <= 0 {
        return
    }
    ...
  }

  func b() {
      guard user.name.count > 0 else { return }
      ...
  }

  // 2. 대칭으로 맞추기
  func a() {
    guard user.name.count > 0 else { return }

    ...
  }

  func b() {
      guard user.name.count > 0 else { return }
      
      ...
  }
  ```
* 응집도를 높이는 배치
  * 함께 묶인 요소를 하위 요소로 추출할 수 있음
  ```swift
  // file a.swift
  func trimString() {}


  // file b.swift
  func replaceString() {}

  // 1. 응집도 높이는 배치
  // file a.swift
  func trimString() {}
  func replaceString() {}

  // 2. 하위 요소 추출 등
  // Util+String.swift / 다른 기능들은 Manager 를 생성할 수 있음
  extension String {
    func trimString() {}
    func replaceString() {}
  }
  ```
* 설명하는 변수
  * 설명하는 변수로 불필요한 주석을 삭제할 수 있음
  ```swift
  func getUserName() -> String? {
    let userName = user.name
    // 사용자 이름이 빈 문자열인 경우 이름 없음으로 간주
    if userName.count == 0 {
        return nil
    }
    
    return user.name
  }

  // 설명하는 변수, 주석 삭제
  func getUserName() -> String? {
      let userName = user.name
      let isUserNameValid = userName.count > 0
      
      guard isUserNameValid else { return }
      
      return userName
  }
  ```
* 설명하는 상수
  * 응집도를 높이는 배치를 이끔. 한 번에 바뀌는 상수를 모아서 묶어놓으면 추후 수정이 편리함
  ```swift
  func getErrorMessage(response) -> String {
    if response.code == 404 {
        return "서버가 요청한 페이지를 찾을 수 없음"
    }
  }

  func completion(response) {
      if response.code == 404 {}
      else if response.code == 403 {}
      else if response.code == 500 {}
  }

  // 1. 설명하는 상수
  func getErrorMessage(response) -> String {
    let NOT_FOUND = 404

    if response.code == NOT_FOUND {
        return "서버가 요청한 페이지를 찾을 수 없음"
    }
  }

  func completion(response) {
    let FORBIDDEN               = 403
    let NOT_FOUND               = 404
    let INTERNAL_SERVER_ERROR   = 500

    switch response.code {
      case FORBIDDEN:
      case NOT_FOUND:
      case INTERNAL_SERVER_ERROR:
      default:
    }
  }

  // 2. 응집도 높이는 배치
  enum HTTPResponseStautsCode: Int {
    case FORBIDDEN               = 403
    case NOT_FOUND               = 404
    case INTERNAL_SERVER_ERROR   = 500
  } 
  ```
* 명시적인 매개변수
  * 매개변수 집합을 묶어 객체로 만들 수 있음. 코드 정리를 통해 새로운 추상화 도출될 수 있음.
  ```swift
  // status 는 하나의 커다란 객체
  // 같은 범주 내에 포함되는 것들을 객체로 묶을 수 있음. status.user.~~
  let userName = status.userName
  let userAge = status.userAge
  ...
  ```
* 비슷한 코드끼리
  * 코드 앞에 설명하는 주석을 붙이거나 코드 덩어리를 설명하는 도우미로 바꿀 수 있음
  ```swift
  // 2. 설명하는 주석 : 데이터 추출
  guard let statusString = self.parameters["status"],
        let passwordType = PasswordType(rawValue: statusString) else { return }
        let familyID = self.parameters["family_id"]
  // 1. 공백 들어감. 범주가 비슷한 로직으로 묶음 효과

  // 2. 설명하는 주석 : View 띄울 presenter 준비 
  guard let presenter = self.payload.presenter else { return }
  let paymentManagerFactory = self.dependency.paymentManagerFactory
  let paymentManager = paymentManagerFactory.create(payload: .init())
  ```
* 도우미 추출
  * 보호 구문 도입, 설명하는 상수/변수 추출, 불필요한 주석 삭제
* 하나의 더미
  * 코드가 모이면 비슷한 코드끼리 정리, 설명하는 주석 생성, 도우미 추출 가능
* 설명하는 주석
  * 주석에 있는 정보를 설명하는 변수/상수/도우미 등으로 도입 가능
* 불필요한 주석 지우기
  * 읽는 순서 개선, 명시적인 매개변수 사용 가능.

**코드 정리는 작고 느리게 변경**

## 18. 코드 정리의 일괄 처리량

코드 정리의 적절한 크기

* 통합/배포 전 얼마나 많은 코드 정리를 해야 하는가?
* 쉽게 통합/배포가 가능한 크기는?

### 골디락스 딜레마 

한 번에 처리하는 코드 정리의 크기에 따른 현상 (한 PR 에 포함되는 코드 정리의 양으로 이해)

<img width="253" alt="image" src="https://github.com/user-attachments/assets/d57e8852-7579-4ee2-9a47-e9ff1707a188">

* 많을 때
  * 충돌 : merge conflict 가능성 증가, 병합 과정에서의 비용 증가
  * 상호작용 : 코드 정리에 의도치 않은 로직 변경이 들어갈 수 있음. 코드 정리 사이 상호작용 있으면 병합 비용 증가
  * 추측 : 다음 변경에 도움이 될 만큼 코드 정리를 하는게 좋은데 코드 정리를 많이 할 경우 다음 코드 정리에 영향받는 범위가 커지기 때문에 더 많은 코드를 다음에 정리하게 됨. 
* 적을 때
  * 검토 비용 (변경 사항 검토 / 배포 고정 비용) 증가

**코드 정리 개수를 늘려 동작 변경 비용 줄이기. 이러면 검토 비용을 줄일 수 있음**

궁극적 목표 : 검토 비용 줄이기
전제 조건 : 팀에 강력한 신뢰 문화를 구축

## 19. 리듬

앞서서 코드 정리할 때 한 번에 처리하는 규모를 작게 할 것이라는 내용을 봄.

구조 변경 (로직 변경 X 코드 정리 O) 에 어느 정도의 시간을 투자하는 게 적당한가? : 저자는 분 ~ 한 시간을 넘지 않도록을 권장.

근데 코드가 너무 엉망이라 동작 변경 이전에 코드 정리를 몇 시간이라도 하는게 유리할 수 있잖아요? : 소프트웨어 설계는 '길을 닦는' 성격을 띄기 때문에 오래 지속할 수 없다.

길을 닦는다? : 건물들을 다 짓고 통행로 안 정하고 잔디를 다 심어버렸는데 학생들이 알아서 밟고 다니면서 길이 생김. 이 길을 포장해버림. 

**동작 변경(로직 변경)은 특정 지점에 뭉쳐져서 나타남.** 파레토 법칙에 의하면 80%의 변경 사항이 20%의 파일에서 발생. 

즉 코드 정리를 선행할 경우 이 정리가 나타나는 지점이 특정 파일들에 뭉쳐진다는 것임. 따라서 생각보다 코드 정리를 해야하는 지점은 국한됨.

## 20. 얽힘 풀기

일어날 수 있는 케이스

```
코드 정리 -> tc 작성 -> (동작 변경해야 하는데 코드 정리할 곳 추가 발견) -> 코드 정리 -> ... 
```

그 결과 

* 변경해야 하는 동작 모두 알게 됨
* 동작들을 쉽게 변경하기 위해서 어떤 코드를 정리해야 할지 알게 됨
* 정리한 코드, 변경할 동작이 얽힘

이때 선택할 수 있는 케이스

1. 그대로 배포. 검토자가 검토하기 어렵다 느낄 수 있음. 당장 처리 가능
2. 코드 정리 / 변경 사항을 하나 이상의 PR/커밋 으로 나눔. 검토하기는 더 편해졌지만 작업 횟수 늘어남
3. 작업 리버트 하고 코드 정리부터 선행. 작업 많아지지만 커밋의 일관성은 분명함.

저자는 3번을 추천. 코드는 단순히 동작하는 것 뿐만 아니라 사람들에게도 의도를 설명해야 함. 

저자가 말하는 이때의 장점.. 솔직히 말이 모호하고 직관적이지 않아서 와닿지는 않는다. 

* 다시 구현하면서 새로운 것을 발견할 가능성이 높아짐 (뭘 다시 구현? 새로운 점이라면 새로 변경해야 하는지점을 의미하는가? 더 나은 코드가 되기 위한 변경 지점?)
* 동일한 동작 변경을 하면서도 더 많은 가치를 끌어낼 수 있음 (어떤 가치를 말하는 건지..?) 
* 코드 정리들을 선행하면 그렇지 않은 경우에서 코드 정리-동작 변경 사이에서 전환점을 놓치는 일 발생 X (이건 당연하지 않나.. 그런데 코드 정리들을 선행하기 위해서는 전반적으로 계속 코드를 검토해야 하는 시간이 늘어날텐데 이게 과연 옳은 선택인가? 왜 코드 정리/동작 변경을 왔다갔다 하면 안됨? 코드 정리가 보이는 순간은 동작 변경 도중에 보이는 때가 많은데 단순히 코드 정리들을 앞쪽에 몰아넣기 위해서 계속 검토만 하고 있는게 맞는 판단인가?)

## 21. 코드 정리 시점 

코드 정리를 먼저 할지? 동작 변경을 먼저 할지? (그런데 앞 장에서 코드 정리를 선행하라고 한 것이 아닌가?)

**코드 정리를 선행할 때는 상황에 따라 다름.**

* 코드를 정리한다고 해서 일이 쉬워지지 않는다면 정리하지 말기
* 코드 정리를 했을 때 동작 변경이 더 쉬워진다고 판단이 될 경우 먼저 코드 정리
* 코드 변경이 자주 일어나지 않을 경우 코드 정리는 하지 않는 것이 좋음


* 코드 정리를 하지 않는 경우
  * 추후 변경되지 않는 것이 확실한 경우
  * 개선해도 배울 것이 없을 때
* 나중에 정리하는 경우
  * 정리해야 하는 게 많은 데 보상이 바로 보이지 않을 때
  * 정리 보상이 잠재적일 때 (대체 어떤 경우인지..)
  * 작은 묶음으로 여러 번에 나눠서 코드 정리를 할 수 있을 때
* 동작 변경 후 정리하는 경우
  * 다음 코드 정리까지 기다릴 수록 비용이 불어날 때 (이거는 코드 정리를 먼저 하라고 책에 나와있는데... 왜 요약에 이렇게 썼을까)
  * 코드 정리를 하지 않으면 일을 끝냈다는 느낌이 들지 않을 때 
* 코드 정리 후 동작 변경하는 경우
  * 코드 정리 했을 때 코드 이해가 쉬워지거나 동작 변경이 쉬워지는 즉각적인 효과 있을 때
  * 어떤 코드를 어떻게 정리해야 하는지 알 때 
