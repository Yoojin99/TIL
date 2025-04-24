# Associated domains & Universal links

**Universal Links vs URL Schemes**

||Universal Links|URL Schemes|
|-|-|-|
|Format|`https://yourdomain.com/some/path`|`yourapp://some/path`|
|App not installed|Opens in safari|Do nothing|
|Secure association|via AASA file|❌|
|User prompt|❌(more seamless)|Sometimes|
|Supported in WebView|Yes|❌(unless manually handled)|

**Universal link 를 통해 앱 내 컨텐츠를 열 수 있음**

* 사용자가 universal link 를 탭/클릭 하면 시스템은 기본 웹 브라우저 / 사이트 를 통하지 않고 **바로 앱으로 redirect 시킴**
* 표준 HTTP / HTTPS 링크이기 때문에 하나의 URL이 웹/앱에서 모두 동작함. 앱이 설치되어 있지 않은 경우 기본 웹 브라우저에서 URL 을 연다.

**Universal link 지원하기**
    
1. 앱 - 웹사이트 양방향 association 을 생성, 앱이 처리하는 URL 을 명시
2. 시스템에서 Universal link 를 전달했을 때 user activity 객체 (`NSUserActivity` 객체)를 어떻게 처리할 지 app delegate 에 명시

**사용자가 Universal link 를 통해 앱을 여는 메서드/프로퍼티**

* iOS(UIApplication) : `open(_:options:completionHandler:)`
* SwiftUI : `openURL`

사용자가 safari 에서 우리 웹 사이트를 열고 같은 도메인에서 universal link 를 탭할 경우 사용자가 계속 브라우저에서 동작할 것을 기대할 것으로 간주하여 Safari 에서 엶. 다른 도메인에서 universal link 를 탭할 경우 앱에서 링크를 연다.

## 다른 앱과 소통하기

Universal link 를 통해서 **third-party 서버를 사용하지 않고 앱끼리 소통(작은 데이터를 전달)할 수 있음**

URL query 문자열에서 앱이 처리할 수 있는 파라미터를 정의해서 데이터를 전송할 수 있음.

e.g. 앨범 이름과 사진 인덱스를 정의하는 파라미터

```
https://myphotoapp.example.com/albums?albumname=vacation&index=1
https://myphotoapp.example.com/albums?albumname=wedding&index=17
```

**A ➡️ B : 열고 싶어요**

* A : B 앱의 URL 생성 후 아래 부분 호출해서 B 앱 열기
    * iOS(UIApplication) : `open(_:options:completionHander)`
    * SwiftUI : `openURL`

```swift
if let appURL = URL(string: "https://myphotoapp.example.com/albums?albumname=vacation&index=1") {
    // open another app with URL
    UIApplication.shared.open(appURL) { success in
        if success {
            print("The URL was delivered successfully.")
        } else {
            print("The URL failed to open.")
        }
    }
} else {
    print("Invalid URL specified.")
}
```

# 1. Associated Domains 지원

**Associated domain 은 도메인-앱 간 secure association 을 설정해서 앱이 특정 사이트와 보안적으로 안전하게 상호작용할 수 있게 해주는 것**

* 역할
    * credential 을 공유 
    * 웹사이트에서 앱의 기능을 사용할 수 있게 함
* 앱이 웹사이트의 전체 / 일부 컨텐츠를 대신 보여줄 수 있는 universal link 기능의 기반이 됨
* 앱이 설치되지 않은 사용자는 웹에서 같은 정보를 보게 됨
* 사용하는 feature
    * Shared Web credentials, universal links, Handoff, App Clips

### Associated Domains 지원하기 (앱-웹 연결하기)

* 웹사이트에 associated domain file 을 갖고 있어야 함
    * 웹 사이트
    의 `apple-app-site-association` 파일 (AASA 파일) 내 앱들은 일치하는 'Associated Domains Entitlement' 를 가져야 함
* app 에 적절한 entitlement 가 있어야 함

## 웹사이트에 associated domain file 추가

* 동작 : 사용자가 앱을 설치하면 시스템은 associated domain file 을 다운받고 entitlement 내 도메인을 검증
* `apple-app-site-association` 파일
    * 도메인에서 지원하는 서비스에 대한 JSON 코드 파일
    * 위치
        * `.well-known` 디렉토리 하위
        * 최종 URL : `https://<fully qualified domain>/.well-known/apple-app-site-association`

### AASA 파일

```json
{
  "applinks": {
      "details": [
           {
             "appIDs": [ "ABCDE12345.com.example.app", "ABCDE12345.com.example.app2" ],
             "components": [
               {
                  "#": "no_universal_links",
                  "exclude": true,
                  "comment": "Matches any URL with a fragment that equals no_universal_links and instructs the system not to open it as a universal link."
               },
               {
                  "/": "/buy/*",
                  "comment": "Matches any URL with a path that starts with /buy/."
               },
               {
                  "/": "/help/website/*",
                  "exclude": true,
                  "comment": "Matches any URL with a path that starts with /help/website/ and instructs the system not to open it as a universal link."
               },
               {
                  "/": "/help/*",
                  "?": { "articleNumber": "????" },
                  "comment": "Matches any URL with a path that starts with /help/ and that has a query item with name 'articleNumber' and a value of exactly four characters."
               }
             ]
           }
       ]
   },
   "webcredentials": {
      "apps": [ "ABCDE12345.com.example.app" ]
   },


    "appclips": {
        "apps": ["ABCDE12345.com.example.MyApp.Clip"]
    }
}
```

* `appLinks`, `webcredentials`, `appclips` : service types
* `applinks` : Universal link 지원, 서비스에 app identifier 명시
* `appclips` : App Clip 의 app identifier 명시
* `appIDs`, `app` key : 이 웹 사이트 내 서비스 타입에서 사용 가능한 앱들의 application identifier 명시. 값은 아래의 형식을 따름
    * `<Application Identifier Prefix>.<Bundle Identifier>`
* `details` : `applinks` 서비스 타입에서만 사용됨
* `components` : URL component 에 pattern matching 을 제공

## 앱에 associated domains entitlement 추가

Xcode > target 의 Signing & Capabilities 에서 Associated Domains capability 추가

<img width="1515" alt="Image" src="https://github.com/user-attachments/assets/38eb1533-d67b-4027-b1cf-574cc56c0e07" />

각 도메인은 아래의 형태를 따름

```
<service>:<fully qualified domain>
```

e.g. `applinks:example.com` / `webcredentials:example.com` / appclip 의 경우 domain 을 `*` 로 prefix 로 설정해서 모든 subdomain 을 매칭하게 할 수 있음.

### 동작

iOS 14 부터 앱은 AASA 파일에 대한 request 를 관련 도메인에 할당된 Apple CDN (Content delivery network) 에 보냄

Apple 의 CDN 은 24시간 내로 도메인의 `apple-app-site-association` 을 요청함. 기기는 앱 설치 이후 대략 주에 한 번씩 업데이트 여부를 확인함

* CDN 이 하는 일 : Middleman. 24 시간 내로 우리 도메인을 확인함.
    * ASSA 파일을 다운로드 하는 것을 시도
    * 새로운 앱 버전을 publish 하거나 기기에 앱을 설치할 때 자동으로 수행됨
    * 성능, 확장성을 위해 CDN 이 그 파일을 캐싱함 (매번 우리 서버를 찌를 필요 없음)
    * 필요한 경우 애플 기기에 데이터를 전달/
    * 앱이 직접 CDN 에 request 를 보내는 것이 아니라 system 에서 이 역할을 수행함

* 사용자가 Universal Link 를 탭할 경우
    1. 시스템이 앱이 설치되어 있는지 확인함 : iOS 는 CDN 에서 캐시된 AASA 파일을 통해 도메인이 우리 앱과 관련이 있는지를 이미 알고 있음. AASA 파일 내 일치하는 path 가 있는지 확인
    2. 앱이 설치가 되어 있다 ➡️ 앱 열고 app delegate 메서드 실행
    3. 앱이 설치가 안 되어 있다 ➡️ 일반 웹 URL 처럼 사파리를 연다

![Image](https://github.com/user-attachments/assets/3f4a37bb-3730-43fa-abee-311c20b574c4)

# 2. App 에서 Universal link 가 들어왔을 때 처리할 수 있게 지원하기

사용자가 universal link 를 활성화하면 시스템은 앱을 실행하고 `NSUserActivity` 객체를 전송함.

* 주의
    * Universal link 를 지원하면 앱을 공격할 수 있는 가능성이 생김
    * 모든 URL 파라미터를 검증하고 이상한 URL 은 버려야 함
    * 사용자 데이터를 조작하지 않도록 액션 제한

## App Delegate 가 Universal link 를 처리하도록 업데이트

시스템이 Universal link 를 통해서 앱을 열면 `NSUserActivity` 객체를 받음.

* `activityType` (String property) : 값은 `NSUserActivityTypeBrowsingWeb` 으로 설정되어 있음.
* `webpageURL` (URL property) : 사용자가 접근한 HTTP / HTTPS URL 을 포함

```swift
func application(_ application: UIApplication,
                 continue userActivity: NSUserActivity,
                 restorationHandler: @escaping ([UIUserActivityRestoring]?) -> Void) -> Bool
{
    // Get URL components from the incoming user activity.
    guard userActivity.activityType == NSUserActivityTypeBrowsingWeb,
        let incomingURL = userActivity.webpageURL,
        let components = NSURLComponents(url: incomingURL, resolvingAgainstBaseURL: true) else {
        return false
    }


    // Check for specific URL components that you need.
    guard let path = components.path,
    let params = components.queryItems else {
        return false
    }    
    print("path = \(path)")


    if let albumName = params.first(where: { $0.name == "albumname" } )?.value,
        let photoIndex = params.first(where: { $0.name == "index" })?.value {


        print("album = \(albumName)")
        print("photoIndex = \(photoIndex)")
        return true


    } else {
        print("Either album name or photo index missing")
        return false
    }
}
```

앱이 Scenes 에 맞춰져 있다면
* 앱이 실행되고 있지 않음 : 시스템은 앱 실행 후에 `scene(_:willConnectTo:options:)` 실행
* 앱이 실행중 / 메모리에 suspend 된 상태 : `scene(_:continue:)` 실행


* 링크
    * [Allowing apps and websites to link to your content](https://developer.apple.com/documentation/xcode/allowing-apps-and-websites-to-link-to-your-content)
    * [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains)
    * [Supporting universal links in your app](https://developer.apple.com/documentation/xcode/supporting-universal-links-in-your-app)