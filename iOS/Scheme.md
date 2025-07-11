# 앱의 custom URL scheme 정의

**Universal Links vs URL Schemes**

||Universal Links|URL Schemes|
|-|-|-|
|Format|`https://yourdomain.com/some/path`|`yourapp://some/path`|
|App not installed|Opens in safari|Do nothing|
|Secure association|via AASA file|❌|
|User prompt|❌(more seamless)|Sometimes|
|Supported in WebView|Yes|❌(unless manually handled)|

# Custom URL

Custom URL 을 통해 **앱 내 리소스에 접근** 

* Deep linking 의 일종. 공식 문서에서는 universal link 를 추천함. 
* 시스템 앱에도 scheme 존재. e.g. `mailto`, `tel`, `sms`, `facetime`

*Deep Linking : 앱 내 특정 위치 / 화면을 띄우는 것*

Universal link 와 동일하게 URL Scheme 은 app 을 공격할 수 있는 가능성을 제공하기 때문에 모든 URL 파라미터를 검증하고 이상한 URL 은 버려야 함. 또한 사용자의 데이터를 이상하게 변경할만한 액션은 막아야 함.

* Custom URL Scheme 지원하기
    1. 앱 URL 의 형식을 정의
    2. 앱에 scheme 을 등록해서 시스템이 적절한 URL 을 앱으로 랜딩할 수 있게 함
    3. 앱에서 URL 처리

URL scheme 형식은 아래와 같음

```
<Custom scheme name>:<파라미터들>

myphotoapp:albumname?name="albumname"
myphotoapp:albumname?index=1
```

URL 을 열 때는 아래 메서드를 사용

```swift
let url = URL(string: "myphotoapp:Vacation?index=1")


UIApplication.shared.open(url!) { (result) in
    if result {
       // The URL was delivered successfully!
    }
}
```

## URL scheme 등록하기

Info 탭 > URL Types 에 scheme 등록

<img width="1585" alt="Image" src="https://github.com/user-attachments/assets/eee51857-62b9-46cc-a51e-1822ee2c2d9f" />

* URL Schemes : URL 에 사용할 prefix

Scheme 에 제공한 identifier 는 같은 scheme 을 지정한 다른 앱과 구분해줌. 고유함을 보장하기 위해서 회사 도메인, 앱 이름을 나타내는 역-DNS 문자열을 설정. 그래도 다른 앱이 같은 스킴을 등록하는 것을 막을 수 없기 때문에 웹 사이트에 고유하게 연결된 universal link 를 사용하는 것을 추천

참고로 `URL Schemes` 에는 대문자로 기입해도 자동으로 소문자로 인식. 

* 예시
    * `URL Schemes` : `customApp`
    * 사파리에 입력한 URL : `customAPP://test`
    * 앱 delegate 메서드 등에서 확인한 URL : `customapp://test`

## Incoming URL 처리

다른 앱에서 우리 앱의 custom scheme 을 포함하는 URL 실행할 경우 시스템은 우리 앱을 실행하고 필요한 경우 foreground 로 불러옴. 

### SceneDelegate

SceneDelegate 를 사용할 경우 AppDelegate method 는 호출되지 않음

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
    if let url = URLContexts.first?.url {
        // Handle URL
        print(url)
    }
}
```

### AppDelegate

시스템은 URL 을 app delegate 의 `application(_:open:options:)` 을 호출해서 앱에 URL 을 전달

```swift
func application(_ application: UIApplication,
                 open url: URL,
                 options: [UIApplicationOpenURLOptionsKey : Any] = [:] ) -> Bool {


    // Determine who sent the URL.
    let sendingAppID = options[.sourceApplication]
    print("source application = \(sendingAppID ?? "Unknown")")


    // Process the URL.
    guard let components = NSURLComponents(url: url, resolvingAgainstBaseURL: true),
        let albumPath = components.path,
        let params = components.queryItems else {
            print("Invalid URL or album path missing")
            return false
    }


    if let photoIndex = params.first(where: { $0.name == "index" })?.value {
        print("albumPath = \(albumPath)")
        print("photoIndex = \(photoIndex)")
        return true
    } else {
        print("Photo index missing")
        return false
    }
}
```

### SwiftUI

```swift
ContentView()
    .onOpenURL { url in
        print("url : \(url)")
    }
```

SwiftUI 앱에 AppDelegate 를 추가하고 AppDelegate 메서드를 추가했을 때는 AppDelegate 내 메서드 호출이 안됨

* 링크
    * [Defining a custom URL scheme for your app](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)