https://developer.apple.com/wwdc22/10072

# UIHostingController

SwiftUI 뷰 계층 구조를 포함하고 있는 UIViewController.

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/58e07a47-13c0-40b6-9ae5-2078a384efd0)


* UIHostingController (== UIViewController)
	* view : 이 안에 SwiftUI 컨텐츠를 담고 있음

* Presenting

```swift
let heartRateView = HeartRateView() // SwiftUI View
let hostingController = UIHostingController(rootView: heartRateView) // rootView 로 지정

// modal 형태로 controller 띄움
self.present(hostingController, animated: true)
```

* Embedding

```swift
let heartRateView = HeartRateView() // a SwiftUI view
let hostingController = UIHostingController(rootView: heartRateView)

// child view controller 로 추가
self.addChild(hostingController)
self.view.addSubview(hostingController.view)
hostingController.didMove(toParent: self)
```

## Sizing options

내부 SwiftUI 컨텐츠 변경에 따라 view 크기를 재조정해야 할 수 있음.

iOS 16 부터 sizing options property 사용해서 view 고유 크기에 대한 자동 업데이트 기능 사용 가능.

* UIViewController - preferredContentSize 
* view - intrinsicContentSize 

```swift
let heartRateView = HeartRateView() // SwiftUI View
let hostingController = UIHostingController(rootView: heartRateView)

// Hosting controller 에 자동 preferredContentSize update 활성화
hostingController.sizingOptions = .preferredContentSize

hostingController.modalPresentationStyle = .popover
self.present(hostingController, animated: true)
```

## Data Bridging

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/4d77dbf9-9c17-4062-b443-e8feb35479fe)

기본 구조

* App Data : 기존에 사용하던 model 객체
* UIViewController : SwiftUI 를 담고 있는 UIHostingController 

### 데이터 종류

* SwiftUI 뷰 내부에서 저장하는 데이터 : `@State` / `@StateObject` property wrapper 사용
* **SwiftUI 뷰 외부에서 주입되는 데이터**

### 외부 데이터 Bridging

1. View 초기화 시 데이터 주입 (Passed arguments)

```swift
struct HeartRateView: View {
    var beatsPerMinute: Int

    var body: some View {
        Text("\(beatsPerMinute) BPM")
    }
}

class HeartRateViewController: UIViewController {
    let hostingController: UIHostingController<HeartRateView> // HeartRateView 는 값 타입. HeartRateView 를 한 번 만들면 복사본이 생성되며 UI update 불가.
    var beatsPerMinute: Int {
        didSet { update() }
    }

    func update() {
        hostingController.rootView = HeartRateView(beatsPerMinute: beatsPerMinute) // 새로운 View 생성 후 할당. 데이터 변경될 때마다 rootView 를 수동으로 업데이트 해야 한다는 단점
    }
}
```

2. 데이터 타입 사용 (외부 데이터 참조 및 변경 사항 추적)

* `@ObservedObject`
* `@EnvironmentObject`

Property wrapper 로 `ObservableObject` 프로토콜 따르는 객체 참조하면 **변경된 데이터를 추적해서 SwiftUI 가 자동으로 업데이트.**

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/8a6c6dc2-ead0-465f-a054-cb63d17c5266)

* 구조
	* 외부 모델 객체 : `ObservableObject` 프로토콜 채택
	* SwiftUI View : `@ObservedObject` property 저장. property 가 변경될 때 view 자동으로 업데이트 됨.

```swift
// 모델
class HeartData: ObservableObject {
    @Published var beatsPerMinute: Int // SwiftUI View 에 변경 사항 업데이트하게 함

    init(beatsPerMinute: Int) {
       self.beatsPerMinute = beatsPerMinute
    }
}

// SwiftUI View
struct HeartRateView: View {
    @ObservedObject var data: HeartData

    var body: some View {
        Text("\(data.beatsPerMinute) BPM")
    }
}

// UIViewController
class HeartRateViewController: UIViewController {
    let data: HeartData
    let hostingController: UIHostingController<HeartRateView> 

    init(data: HeartData) {
        self.data = data
        let heartRateView = HeartRateView(data: data)
        self.hostingController = UIHostingController(rootView: heartRateView)
    }
}
```
모델 객체의 `beatsPerMinute` 값이 변함 ➡️ `ObservableObject` 객체의 property 가 변경됐기 때문에 SwiftUI View 가 자동으로 업데이트 됨

# UIHostingConfiguration

**SwiftUI 를 사용해 UIKit cell 을 구성할 수 있게 해줌.** 별도의 추가 `UIView` / `UIViewController` 필요 없음.

## 기존의 cell configuration

* cell 의 내용, 스타일, 동작을 현대적으로 정의하는 방법
* `UIView` / `UIViewController` 와 다르게 가벼운 struct : 만들기 쉬움
* cell 의 모습에 대한 설명 : 셀에 적용되어야 rendering 됨
* 직접 구성 가능, `UICollectionViewCell` / `UITableViewCell` 에 모두 적용 가능

## 사용법

```swift
cell.contentConfiguration = UIHostingConfiguration {
    HeartRateTitleView() // SwiftUI View 구성
}

struct HeartRateTitleView: View {
    var body: some View {
        HStack {
            Label("Heart Rate", systemImage: "heart.fill")
                .foregroundStyle(.pink)
                .font(.system(.subheadline, weight: .bold))
            Spacer()
            Text(Date(), style: .time)
                .foregroundStyle(.secondary)
                .font(.footnote)
        }
    }
}
```

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/77ef9fcf-7ceb-4796-b9b6-b788854a137d)


## Content / Background

* Content
	* `.margins()` modifier 사용. 
	* SwiftUI 컨텐츠는 기본적으로 UIKit cell 의 layout margin 내부에 자리잡고 있음 (마진이 적용됨)
	* cell 크기에 영향 줌
	* ![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/8bde0f1c-0e6f-4dc1-b14b-57216750cef3)
* Background
	* `.background()` modifier 사용
	* 기본적으로 마진 X
	* 컨텐츠, accessory 뒤에 깔림
	* cell 크기에 영향 안 줌

## List Seperators

기본적으로 SwiftUI Text 와 자동 정렬됨. 변경하고 싶으면 `.alignmentGuide` modifier 사용

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/ba855e45-c4c9-42da-a8d6-05fb5376d245)

## Swipe action

cell 을 스와이프 했을 때 동작 설정 가능. IndexPath 는 cell 이 visible 한 와중에 바뀔 수 있으므로 identifier 를 사용해서 동작 처리.

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/88c88bc9-42ef-466d-99ef-e6484404ed08)

## configurationUpdateHandler

**UIKit cell 상태와 상관없이 SwiftUI View 를 따로 변경하고 싶을 때 내부에서 hostingConfiguration 생성해서 cell 에 적용.** Cell 의 상태가 변할때마다 다시 실행됨.

`UIHostingConfiguration` 을 사용할 때도 탭 처리/강조 표시/선택 같은 **cell 상호작용은 여전히 collectionView / tableView 에서 처리되기 때문.** 

```swift
cell.configurationUpdateHandler = { cell, state in
	// 새로운 hostingConfiguration 생성 후 적용 
    cell.contentConfiguration = UIHostingConfiguration {
      HStack {
        HealthCategoryView()
            Spacer()
            if state.isSelected {
                Image(systemName: "checkmark")
            }
        }
    }
}
```

## Data Binding

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/d7856d9c-32d0-4884-a540-92cbe92c87d4)

* 기본 구조
	* Data Collection : 화면에 보여주고자 하는 데이터 collection
	* Diffable Data Source : 데이터 원본(collection) 의 snapshot 관리. Snapshot 에는 각 데이터의 고유한 identifier 포함.
	* Collection View : 각 cell 마다 SwiftUI View 포함

* 데이터가 변경되는 상황
	1. 데이터 collection 자체가 바뀜 (새로운 데이터 삽입 / 순서 바뀜 / 삭제) : 원본 데이터의 새로운 snapshot 생성 후 적용. Cell 자체가 삽입/이동/삭제되며  **Cell 내부에는 영향을 미치지 않음.**
	2.  **개별 모델 객체에 변경사항 생김** 
		* cell 업데이트 필요
		* snapshot 에는 데이터 객체의 identifer 만 포함하고 있기 때문에 identifier 가 업데이트 되지 않는 이상 데이터가 변경됐음을 모름. 
		* 기존에는 snapshot 항목 재구성 / 다시 로딩해서 직접 업데이트
		* 이제는 SwiftUI View 의 `ObservableObject` 을 따르는 모델을 property 로 참조하며 모델 내부의 property 가 바뀌면 자동으로 SwiftUI 가 업데이트 됨

### Self-resizing cells

**iOS 16 부터 UIKit 의 새로운 기능을 사용해서 Cell 의 SwiftUI 가 UIKit 을 거치지 않고 업데이트 됐음에도 불구하고 셀 크기 자동으로 재조정됨**

### SwiftUI ➡️ 모델 객체 변경사항 반영

**Two-way binding 을 사용해서 SwiftUI 내부 변경사항을 모델 객체에도 적용 가능**

![image](https://github.com/Yoojin99/AppleSwiftUITutorials/assets/41438361/cfed4c8f-52ad-4b35-bbac-9e5e4bfcbae6)

```swift
class MedicalCondition: Identifiable, ObservableObject {
    let id: UUID
   
    @Published var text: String
}

struct MedicalConditionView: View {
    @ObservedObject var condition: MedicalCondition

    var body: some View {
        HStack {
			TextField("Condition", text: $condition.text) // binding 을 통해 SwiftUI 가 변경된 내용을 ObservableObject 에 반영함
            Spacer()
        }
    }
}
```
