# Pure

[Github](https://github.com/devxoul/Pure)

DI Container 없이 DI 를 할 수 있게 해줌

Pure DI 의 핵심 개념 : 전체 객체 의존성 그래프를 Composition Root 에서 구성하는 것

## Background

### Pure DI?

https://blog.ploeh.dk/2014/06/10/pure-di/

Pure DI 는 Poor Man's DI 의 다른 말임.

DI 자체는 원칙 / 패턴 
DI Container 는 다른 helper 라이브러리

DI Container 없는 DI 를 Poor Man's DI 라고 했었음. 이 용어의 문제는 DI Container 없는 DI 가 DI Container 있는 DI 보다 안 좋게끔 느껴지게 함. 

Pure DI 는 DI 원칙과 패턴을 사용하지만 DI Container 를 사용하지 않는 것을 의미함

### Composition Root

전체 객체 그래프가 설정되는 곳. Cocoa application 에서는 `AppDelegate`


#### AppDependency

Root dependency 는 `AppDelegate` 의 dependency, root view Controller 의 dependency.

이런 의존성을 주입하는 방법으로 `AppDependency` 구조체를 만들고 양쪽의 dependency 를 저장하는 것

e.g. 

```swift
// AppDelegate, root view controller 의존성을 모두 담고 있음
struct AppDependency {
  let networking: Networking
  let remoteNotificationService: RemoteNotificationService
}

extension AppDependency {
  static func resolve() -> AppDependency {
    let networking = Networking()
    let remoteNotificationService = RemoteNotificationService()

    return AppDependency(
      networking: networking,
      remoteNotificationService: remoteNotificationService
    )
  }
}
```

테스트 / 실제 환경을 구분할 필요성이 있음. `AppDelegate` 는 시스템에 의해 `init()` 이 호출되며 생성됨.

* 실제 환경 : 시스템에 의해 `AppDependency.init()` 호출
* 테스트 환경 : `AppDelegate.init(dependency)` 생성자 임의 생성

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
  private let dependency: AppDependency

  // 시스템에 의해 자동으로 호출되며 테스트 환경에서 접근 불가
  private override init() {
    self.dependency = AppDependency.resolve()
    super.init()
  }

  // 테스트 환경에서 호출
  init(dependency: AppDependency) {
    self.dependency = dependency
    super.init()
  }
}
```

주입된 dependency 는 아래와 같이 `self.dependency` 로 접근해서 사용

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
  // inject rootViewController's dependency
  if let viewController = self.window?.rootViewController as? RootViewController {
    viewController.networking = self.dependency.networking
  }
}

func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
  // delegates remote notification receive event
  self.dependency.remoteNotificationService.receiveRemoteNotification(userInfo)
}
```

### Lazy Dependency

#### Factory

Cocoa 애플리케이션에서 view controller 들은 lazy 하게 생성됨. e.g. `DetailViewController` 는 사용자가 `ListViewController` 의 item 을 클릭하기 전까지 생성되지 않음.

이 경우 `DetailViewController` 를 생성하는 factory 클로저를 `ListViewController` 에 전달함.

```swift
/// A root view controller
class ListViewController {
  // 외부에서 주입된 DetailViewController 생성 클로저
  var detailViewControllerFactory: ((Item) -> DetailViewController)!

  func presentItemDetail(_ selectedItem: Item) {
    let detailViewController = self.detailViewControllerFactory(selectedItem)
    self.present(detailViewController, animated: true)
  }
}

// Composition Root 에서 실행될 함수
static func resolve() -> AppDependency {
  let storyboard = UIStoryboard(name: "Main", bundle: nil)
  let networking = Networking()

  // DetailViewController 를 생성하는 클로저
  // 이렇게 함수 내에 바로 factory 클로저가 있을 경우 얘만 따로 빼서 테스트하기가 어려움.
  let detailViewControllerFactory: (Item) -> DetailViewController = { selectedItem in
    let detailViewController = storyboard.instantiateViewController(withIdentifier: "DetailViewController") as! DetailViewController
    // 여기에서 주입하는 것을 깜빡한다면?
    detailViewController.networking = networking
    detailViewController.item = selectedItem
    return detailViewController
  }

  return AppDependency(
    networking: networking,
    detailViewControllerFactory: detailViewControllerFactory
  )
}
```

여기서 문제는 factory 클로저를 테스트할 수 없음. Factory 클로저는 Composition Root 에서 생성되는데 테스트 환경에서는 Composition Root 에 접근하면 안되기 때문.

만약 `DetailViewController.networking` 프로퍼티를 주입하는 것을 깜빡한다면?

해결 방법 중 하나는 factory 클로저를 Composition Root 밖에서 생성하는 것.

위 코드에서 `Storyboard`, `Networking` 는 Composition Root 에서 오지만 `Item` 은 이전 view controller 에서 오기 때문에 scope 를 분리할 필요가 있음.

```swift
extension DetailViewController {
  // resolve 내에 위치하던 factory 함수를 외부로 뺌.
  // scope 분리
  // 사용자가 ListViewController 의 item 을 선택할 때 실행됨
  static let factory: (UIStoryboard, Networking) -> (Item) -> DetailViewController = { storyboard, networking in
    return { selectedItem in
      let detailViewController = storyboard.instantiateViewController(withIdentifier: "DetailViewController") as! DetailViewController
      detailViewController.networking = networking
      detailViewController.item = selectedItem
      return detailViewController
    }
  }
}

static func resolve() -> AppDependency {
  let storyboard = ...
  let networking = ...
  return .init(
    detailViewControllerFactory: DetailViewController.factory(storyboard, networking)
  )
}
```

이제 `DetailViewController.factory` 클로저를 따로 테스트 할 수 있음. storyboard / networking 을 따로 주입해서 테스트가 가능해졌기 때문.

모든 dependency 는 Composition Root 에서 설정되며 runtime 에 사용자가 선택된 item 이 `ListViewController` 에서 `DetailViewController` 로 전달될 수 있음.

#### Configurator

Cell 또한 lazy 하게 생성되는데 cell 은 framework 에 의해 생성되기 때문에 factory 클로저를 사용할 수 없음. 개발자가 할 수 있는 건 cell 을 구성하는 것

이미지를 보여주는 `UICollectionViewCell` / `UITableViewCell` 이 있다 가정. 실제 환경에서는 실제 이미지를 다운로드해서 보여주고 테스트 환경에서는 가짜 이미지를 보여주는 `imageDownloader` 가 있음.

```swift
class ItemCell {
  var imageDownloader: ImageDownloaderType?
  var imageURL: URL? {
    didSet {
      guard let imageDownloader = self.imageDownloader else { return }
      self.imageView.setImage(with: self.imageURL, using: imageDownloader)
    }
  }
}
```

Cell 은 `DetailViewController` 에서 보여지며 `DetailViewController` 는 cell 에 `imageDownloader` 를 주입해서 `image` 프로퍼티를 설정해야 함. Factory 클로저처럼 cell 을 구성하는 클로저를 만들 수 있지만 이 클로저는 존재하는 인스턴스를 받는데 리턴 값이 없음

```swift
class ItemCell {
  // 이미 존재하는 cell 을 받아서 내부적으로 상태변경만 하고 리턴 값은 없음 
  static let configurator: (ImageDownloaderType) -> (ItemCell, UIImage) -> Void = { imageDownloader in
    return { cell, image in
      cell.imageDownloader = imageDownloader
      cell.imageView.image = image
    }
  }
}
```

`DetailViewController` 가 이 configurate 클로저를 갖고 cell 을 구성할 때 사용할 수도 있음.

```swift
class DetailViewController {
  var itemCellConfigurator: ((ItemCell, UIImage) -> Void)?

  func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
    ...
    self.itemCellConfigurator?(cell, image)
    return cell
  }
}
```

그리고 `DetailViewController` 의 이 클로저는 factory 에서 주입됨.

```swift
extension DetailViewController {
  static let factory: (UIStoryboard, Networking, @escaping (ItemCell, UIImage) -> Void) -> (Item) -> DetailViewController = { storyboard, networking, imageCellConfigurator in
    return { selectedItem in
      let detailViewController = storyboard.instantiateViewController(withIdentifier: "DetailViewController") as! DetailViewController
      detailViewController.networking = networking
      detailViewController.item = selectedItem
      // cell 의 configurator 도 여기에서 함께 주입됨
      detailViewController.imageCellConfigurator = imageCellConfigurator
      return detailViewController
    }
  }
}
```

최종적으로 Composition Root 는 아래와 같이 보여짐. Composition Root 에서 모든 dependency 가 주입이 이루어짐

```swift
static func resolve() -> AppDependency {
  let storybard = ...
  let networking = ...
  let imageDownloader = ...
  let listViewController = ...
  // DetailViewController 의 factory 메서드를 사용해서 주입되는 모습
  listViewController.detailViewControllerFactory = DetailViewController.factory(
    storyboard,
    networking,
    // cell 의 configurator 메서드
    ImageCell.configurator(imageDownloader)
  )
  ...
}
```

### Problem

이론적으로는 동작하지만 `DetailViewController.factory` 팩토리 클로저에서처럼 주입받아야 하는 의존성이 많은 경우 매우 복잡해짐. Pure 는 팩토리와 configurator 를 깔끔하게 만들어줌.

## Getting Started

### Dependency and Payload

위에서 본 코드에서 factory, configurator 는 아래와 같음

```swift
static let factory: (UIStoryboard, Networking, (ItemCell, UIImage) -> Void) -> (Item) -> DetailViewController
static let configurator: (ImageDownloaderType) -> (ItemCell, UIImage) -> Void
```

위 함수들은 함수를 리턴하는 함수. 

* Outer 함수 : Composition Root 에서 실행됨. `Networking` 과 같은 정적인 의존성을 주입함
* Inner 함수 : View controller 에서 실행됨. `selectedItem` 같은 런타임 정보를 전달함

* Dependency : Outer 함수의 파라미터
* Payload : Inner 함수의 파라미터

```swift
// (UIStoryboard..Void) 까지 Dependency (Item) 은 payload
static let factory: (UIStoryboard, Networking, (ItemCell, UIImage) -> Void) -> (Item) -> DetailViewController
                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^      ^^^^
                                        Dependency                             Payload

static let configurator: (ImageDownloaderType) -> (ItemCell, UIImage) -> Void
                          ^^^^^^^^^^^^^^^^^^^      ^^^^^^^^^^^^^^^^^
                              Dependency                Payload
```

위와 같은 factory / configurator 패턴을 Pure 가 Dependency, Payload 를 사용해서 일반화함

### Module

Dependency 와 Payload 를 요구하는 모든 클래스.

`Module` 프로토콜은 `Dependency`, `Payload` 를 갖고 있어야 함

```swift
protocol Module {
  /// Composition Root 에서 resolve 되는 의존성
  associatedtype Dependency

  /// 모듈을 구성하기 위한 런타임 정보
  associatedtype Payload
}
```

모듈 종류

1. `FactoryModule`
2. `ConfiguratorModule`

#### Factory Module

팩토리 클로저의 일반화된 버전. `dependency`, `payload` 를 받는 생성자를 요구함.

```swift
protocol FactoryModule: Module {
  init(dependency: Dependency, payload: Payload)
}

class DetailViewController: FactoryModule {
  struct Dependency {
    let storyboard: UIStoryboard
    let networking: Networking
  }

  struct Payload {
    let selectedItem: Item
  }

  init(dependency: Dependency, payload: Payload) {
  }
}
```

내부 중첩된 타입으로 `Factory` 존재.

* `Factory` 타입
  * `Factory.init(dependency:)` 로 모듈의 의존성 주입
  * `create(payload:)` 메서드 : 새로운 모듈 인스턴스 생성

```swift
// 모듈을 생성하는 팩토리
class Factory<Module> {
  let dependency: Module.Dependency
  func create(payload: Module.Payload) -> Module
}

// In AppDependency
// DetailViewController 를 생성하기 위한 팩토리. dependency 를 주입
let factory = DetailViewController.Factory(dependency: .init(
  storyboard: storyboard
  networking: networking
))

// In ListViewController
// 팩토리로 Module 을 생성
// factory 의 create 메서드는 어디에서 정의되는 것인가..?
let viewController = factory.create(payload: .init(
  selectedItem: selectedItem
))
```

#### Configurator Module

Configurator 클로저의 일반화된 버전. `dependency`, `payload` 를 받는 `configure()` 메서드 있음

```swift
protocol ConfiguratorModule: Module {
  func configure(dependency: Dependency, payload: Payload)
}

class ItemCell: ConfiguratorModule {
  struct Dependency {
    let imageDownloader: ImageDownloaderType
  }

  struct Payload {
    let image: UIImage
  }

  func configure(dependency: Dependency, payload: Payload) {
    self.imageDownloader = dependency.imageDownloader
    self.image = payload.image
  }
}
```

내부에 `Configurator` 라는 중첩된 타입을 가짐.

* `Configurator` 타입
  * `Configurator.init(dependency:)` : 모듈의 의존성을 주입받음
  * `configure(_:payload:)` : 존재하는 인스턴스를 구성함

```swift
class Configurator<Module> {
  let dependency: Module.Dependency
  func configure(_ module: Module, payload: Module.Payload)
}

// In AppDependency
// 의존성 주입받음
let configurator = ItemCell.Configurator(dependency: .init(
  imageDownloader: imageDownloader
))

// In DetailViewController
// configurator 의 configure 메서드는 어디에서 정의되는 것인가..?
configurator.configure(cell, payload: .init(image: image))
```

`FactoryModule`, `ConfiguratorModule` 을 사용해서 위의 코드를 아래와 같이 리팩토링 할 수 있음

```swift
// 리팩토링 이전
static func resolve() -> AppDependency {
  let storybard = ...
  let networking = ...
  let imageDownloader = ...
  let listViewController = ...
  // DetailViewController 의 factory 메서드를 사용해서 주입되는 모습
  listViewController.detailViewControllerFactory = DetailViewController.factory(
    storyboard,
    networking,
    // cell 의 configurator 메서드
    ImageCell.configurator(imageDownloader)
  )
  ...
}

// 리팩토링 이후
static func resolve() -> AppDependency {
  let storybard = ...
  let networking = ...
  let imageDownloader = ...

  return .init(
    // 팩토리를 넘겨준다
    detailViewControllerFactory: DetailViewController.Factory(dependency: .init(
      storyboard: storyboard,
      networking: networking,
      itemCellConfigurator: ItemCell.Configurator(dependency: .init(
        imageDownloader: imageDownloader
      ))
    ))
  )
}
```

### Customizing

`Factory`, `Configurator` 는 커스텀이 가능함.

```swift
extension Factory where Module == DetailViewController {
  func create(payload: Module.Payload, extraValue: ExtraValue) -> Payload {
    let module = self.create(payload: payload)
    module.extraValue = extraValue
    return module
  }
}
```

#### Storyboard Support

`FactoryModule` 은 커스텀 기능을 사용해서 Storyboard 를 통해 초기화된 view controller 를 지원 가능함. 

```swift
extension Factory where Module == DetailViewController {
  func create(payload: Module.Payload) -> Payload {
    let module = self.dependency.storyboard.instantiateViewController(withIdentifier: "DetailViewController") as! Module
    module.networking = dependency.networking
    module.itemCellConfigurator = dependency.itemCellConfigurator
    module.selectedItem = payload.selectedItem
    return module
  }
}
```

## 정리

Pure 는 DI Container 없이 Composition Root 에서 의존성을 해결할 수 있게 하는 프레임워크. 

의존성이 필요한 클래스를 생성하기 위한 factory, configurator 클로저가 존재.

정적 의존성이 Dependency 와 런타임 정보인 Payload 를 주입받는 Module 이 있음.

이 모듈을 생성하는 factory, configurator 가 의존성을 주입받는 패턴을 일반화해서 프레임워크 단에서 객체 생성 헬퍼 메서드를 지원해주고 있음. 이런 헬퍼 메서드를 사용해서 Composition Root 에서 의존성을 해결함.

