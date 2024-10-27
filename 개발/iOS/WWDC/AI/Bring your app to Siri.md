# Bring your app to Siri

WWDC 24

* 목차
  * 새롭게 도입된 Apple Intelligence
  * 새로운 API
  * Personal context

앱을 시리와 통합해야 하는 이유? 

1. 앱 외부에 기능 노출 : 사용자가 기기 어느 화면에서든 앱을 사용해서 작업을 수행할 수 있음 
2. 빠르고 효율적인 작업을 수행할 수 있게 해줌

## SiriKit 과 App Intent

* SiriKit
  * iOS 10 부터 도입
  * 시스템이 제공하는 intent 를 통해 이미 이용하는 siri 기능을 앱에서도 사용할 수 있게 됨 (음악 재생 / 메세지 보내기)
* App Intent
  * iOS 16 부터 도입
  * 앱을 Siri, 단축어, Spotlight 과 통합해줌

# 새로 도입된 Apple Intelligence

Apple Intelligence 의 대규모 언어 모델을 통해 Siri 성능이 향상됨

1. 의사소통 향상 (SiriKit 을 사용하는 앱에서 개선 사항이 자동으로 적용됨)
   1. 더 자연스러운 목소리로 말함
   2. Personal context, 화면 인식 : 맥락에 맞고 사용자에게 맞춤화 됨. Apple Intellignece 로 siri 가 화면을 인식할 수 있어 사용자가 무엇을 보는지 이해하고 그에 맞게 행동할 수 있음
   3. 언어 이해 향상 : Siri 와 훨씬 자연스럽게 대화 가능
2. 수행 능력
   1. Siri 가 사용자를 대신해 더 많은 작업을 할 수 있게 Siri 환경을 재구상해서 사용자가 기기에서 하는 작업을 더 많이 이해할 수 있게 함.
   2. Apple Intelligence 에 앱 세계를 연결 하기 위한 수단으로 앱 인텐트 프레임워크에 투자함 ➡️ App Intent Domain 이라는 새로운 API 탄생

## App Intent 도메인

<img width="484" alt="image" src="https://github.com/user-attachments/assets/d28b6046-991a-4d82-9c0c-fe0dcfd814b4">

* 특정 기능을 위해 설계된 앱 인텐트 기반 API 모음 e.g. Books, 카메라, 스프레드 시트
* iOS 18 에서 도메인 중 12 개 출시 
* 각 도메인에 다양한 액션 포함됨. 액션은 유연한 음성 상호작용을 지원하고 쉽게 적용할 수 있게 학습/테스트 됨
  * 액션 예시 : Darkroom 같은 앱에서 "photos.setFilter" 인텐트를 사용할 수 있으므로 사용자가 대신 "어제 내가 찍은 내 셀카에 시네마틱 프리셋을 적용해 줘" 라고 음성으로 말해서 작업을 수행할 수 있음

# Actions

## Building

### Assistant Schema

Assistant Schema 로 앱 인텐트 도메인에 대한 작업을 빌드하는 방법

*Schema? : 사전상 의미는 클래스의 모든 멤버에게 공통적으로 적용되는 개념. 일반적이거나 필수적인 유형/양식.*

* Apple Intelligence 는 앞선 도메인들에서 Siri 가 새로운 기능을 할 수 있게 하는 foudation model 에서 구동됨
* Foundation model 은 **특정 형태**의 인텐트를 예상하도록 학습됨
* 이 특정 형태를 스키마라 함.
* Assistant Schema 를 API 라 부름
* App Intent 를 올바르게 구축하면 자연어의 복잡성을 걱정할 필요 없이 Apple Intelligence 가 학습한 것의 이점을 얻을 수 있음 : `perform` 메서드만 작성하고 나머지는 플랫폼이 알아서 처리하게 두기

### Shape

<img width="494" alt="image" src="https://github.com/user-attachments/assets/309ad855-c959-42d1-8f79-69e0b64d9dec">

* Intent 는 여러가지가 존재. e.g. 사진 생성 / 이메일 전송 ...
* Schema 는 각 인텐트를 사용하는 모든 사용자에게 공통으로 적용되는 input, output 을 정의함
* Shape : Schema 가 각 인텐트마다 정의한 공통으로 적용되는 input - output 의 형태
* perform 메서드 : input - output 사이에 존재하며 자유롭게 사용해서 앱에 적합한 작업을 하도록 정의 가능

### Apple Intelligence

Apple Intelligence 를 통한 Siri 요청의 lifecycle. Assistant Schema 를 실제로 시연

<img width="497" alt="image" src="https://github.com/user-attachments/assets/5a1425c9-7132-44b3-ab0c-3024803a4c7f">

1. User request : 사용자 요청
2. Model 을 통해 적절한 Schema 선택 : Model 은 사용자 요청이 들어왔을 때 Schema 를 추론하도록 학습됨. 사용자 요청은 Apple Intelligence 를 통해 라우팅되어 모델을 통해 처리됨
3. 사용자 요청을 Toolbox 로 routing : Toolbox 에는 기기의 모든 앱의 AppIntent 모음이 Schema 별로 그룹화되어 있음. 
4. App Intent 를 호출해서 작업을 수행 : 결과 표시됨, output 리턴

### Assistant Intent

```swift
import AppIntents

// Intent 가 Schema 를 따르도록 하는 매크로. 모델이 추론할 수 있게 함
// photos 가 도메인, createAlbum 이 schema
@AssistantIntent(schema: .photos.createAlbum)
struct CreateAlbumIntent: OpenIntent {
  // schema 의 shape 은 컴파일 시 알고 있기 때문에 AppIntent 에 대한 아래 추가 메타데이터 제공 필요 없음
  // static var title = LocalizedStringResource("Create Album")
  // static var description = "Creates album with name"
    
  // @Parameter(title: "Album name")
    var name: String

    // 필요한 경우 optional 파라미터를 사용해서 확장 가능
    @Parameter(title: "Album name")
    var albumType: AlbumType?
    
    func perform() async throws -> some ReturnsValue<AlbumEntity> {
        // AlbumEntity 엔티티
        return .result(value: AlbumEntity(name: name))
    }
}
```

### AppEntity

```swift
import AppIntents

// Siri 에 AppEntity 를 노출하기 위한 매크로
// 매크로 덕분에 엔티티 정의가 더 간단해짐
@AssistantEntity(schema: .photos.album)
struct AlbumEntity: AppEntity {
    static let defaultQuery = Query()

    let id: String

    var name: String
    var albumType: AlbumType

    // optional property 를 사용해서 확장
    @Property(title: "Album Color")
    var color: Color?
}
```

### Assistant Enum

```swift
import AppIntents

@AssistantEnum(schema: .photos.albumType)
enum AlbumType: String, AppEnum {
    case custom

    static let caseDisplayRepresentations: [AlbumType: DisplayRepresentation] = [
        .custom: "Custom"
    ]
}
```

enum 은 다른 유형들과 다르게 형태상 제약이 없기 때문에 자유롭게 구성 가능

### Compiler validation

* Assistant Schema 는 빌드할 때 이미 존재하는 App Intent 들에 추가적인 validation 수행 ➡️ 스키마가 유효한지와 모델이 학습한 shape 에 일치하는지 확인
* Xcode snippet 을 사용해서 쉽게 빌드 가능

### Shortcuts

<img width="242" alt="image" src="https://github.com/user-attachments/assets/9e7deb69-5740-4479-8420-4549a2d1520e">

* 스키마에 부합하는 App Intent 는 자동으로 Shortcuts 에 노출됨

# Personal Context

Apple Intellignece 를 통해 Siri 는 사용자의 개인적인 맥락을 충분히 이해할 수 있게 됨.

Siri 는 기기 내 사용 가능한 모든 정보를 검색, 추론 가능

## In-app search

* `ShowInAppSearchResultsIntent` 를 기반으로 구축됨
* 앱의 검색기능을 활용해서 siri 가 검색 결과로 바로 안내해줌. (e.g. "Superhuman 앱에서 자전거를 찾아줘" 하면 앱에서 결과 볼 수 있음)

```swift
import AppIntents

// search 스키마
@AssistantIntent(schema: .system.search)
struct SearchAssetsIntent: AppIntent {
    static let searchScopes: [StringSearchScope] = [.general]

    var criteria: StingSearchCriteria

    @MainActor
    func perform() async throws -> some IntentResult {
      // Navigate to search field.
      return .result()
    }
}
```

## Semantic search

* 사진 / 파일 / 앱 컨텐츠를 찾을 뿐만 아니라 **이해** 할 수 있음 (e.g. 반려동물을 검색하면 반려동물이라는 단어만 찾지 않고 멍냥이, 뱀 등을 찾을 수 있음)
* 검색 결과를 찾으면 원하는 동작을 수행할 수 있음 (e.g. 반려동물 사진을 찾으면 바로 공유 가능)
* `IndexedEntity` 라는 API 를 준수해서 Siri 가 앱 컨텐츠를 검색하게 할 수 있음

# 정리

* Apple Intellignece 의 새로운 대규모 언어 모델 덕분에 Siri 는 유연해지고 똑똑해짐
* SiriKit, AppIntent 는 앱을 Siri 와 통합하기 위한 프레임워크들. 앱이 SiriKit 도메인과 겹치지 않는다면 AppIntent 를 사용하기
* Assistant Schema API 를 사용해서 앱을 Apple Intelligence 와 통합하기

