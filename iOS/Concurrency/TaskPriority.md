# TaskPriority

시스템이 어떤 Task 가 다음에 실행될 지 결정하는데 사용될 수 있으나 보장되지는 않음

* Priority 할당
    * 특정 priority (`.high`/`.background`) 를 할당받을 수 있음
    * 할당받지 않았다면 `nil`

Task 가 생성됐을 때 priority 를 직접 할당하지 않는다면 Swift 는 아래의 rule 에 따라 자동으로 priority 를 결정함

## Rule of Swift

* Task 가 다른 Task 에서 생성된 경우 : 자식 Task 는 부모 Task 의 priority 를 상속받음
* Task 가 다른 Task 에서 생성되지 않고 main thread 에서 생성된 경우 : 자동으로 `.high` 할당
* Task 가 다른 Task / main thread 에서 만들어지지 않은 경우 : thread 의 priority / `nil` 부여


➡️ **Swift 가 잘 처리할 것이기 때문에 항상 특정 priority 를 지정하는 것이 항상 좋은 것은 아님**

## Priority 종류

* `.high` : 사용자가 명시적으로 시작하거나 active 하게 기다리고 있는 작업. GCD 의 `.userInitiated` 와 동일
* `.medium` : 대부분의 task 에 적합. 사용자가 active 하게 기다리고 있지 않은 작업
* `.low` : Progress bar 가 필요할 정도의 긴 작업에 적합. GCD 의 `.utility` 와 동일. e.g. 파일 copy, import
* `.background` : 사용자가 볼 수 없는 작업. e.g. search index build, 실행되는데 오래 걸리는 작업

## Task Priority Raw Value

* `high` = 25
* `medium` = 21
* `low` = 17
* `background` = 9

## 예시

SwiftUI 에서 버튼을 눌렀을 때 특정 task 를 실행시키고 priority 를 할당하지 않는다면?

```swift
struct ContentView: View {
    @State private var jokeText = ""

    var body: some View {
        VStack {
            Text(jokeText)
            Button("Fetch new joke", action: fetchJoke)
        }
    }

    func fetchJoke() {
        Task {
            let url = URL(string: "https://icanhazdadjoke.com")!
            var request = URLRequest(url: url)
            request.setValue("Swift Concurrency by Example", forHTTPHeaderField: "User-Agent")
            request.setValue("text/plain", forHTTPHeaderField: "Accept")

            let (data, _) = try await URLSession.shared.data(for: request)

            if let jokeString = String(data: data, encoding: .utf8) {
                jokeText = jokeString
            } else {
                jokeText = "Load failed."
            }
        }
    }
}
```

UI 에서 시작되었기 때문에 자동으로 `high` priority 지정됨

TCA 를 사용하면서 reducer 에서 `.run` effect 를 리턴할 때 priority 를 지정하지 않는다면? (Button 클릭시 `.viewAction(.load)` 를 send 하도록 구현함)

```swift
// Inside reducer
case .viewAction(.load):
    // 예상 : .high
    // 실제 : TaskPriority(rawValue:33)
    print("\(Task.currentPriority)")
    
    return .run { send in
        // 예상, 실제 : .high
        print("Inside : \(Task.currentPriority)")
    }
```

➡️ TaskPriority 가 명시적으로 각 case 로 딱 나뉘는 게 아니라 rawValue 로 값이 지정됨

그 이유는 첫 번째 print 문은 Swift `Task` 에서 실행되지 않고 reducer 자체, main thread 에서 실행되기 때문에 시스템이 추론한 값이어서 이상한 값들이 나올 수 있음

## Network Task

* 명시적으로 Task priority 를 설정하는 것이 좋을 때
    * 작업이 시간적으로 민감하지 않음 : e.g. UI 에서 바로 필요하지 않은 서버에 로그 올리는 작업 등
    * 시스템이 우선순위를 낮게 가져가고 다른 우선순위 높은 작업을 먼저 실행하는 것을 원할 때
* Swift 가 정하도록 냅두는 것이 좋을 때
    * 작업이 UI / 사용자 경험에 영향을 줄 때 : API 응답 값이 보여지는 데이터 / 내비게이션 / 피드백 등에 영향을 줄 때
    * 어떤 우선순위를 줘야 할지 모를 때

* Link
    * https://www.hackingwithswift.com/quick-start/concurrency/how-to-control-the-priority-of-a-task
    * https://engineering.linecorp.com/ko/blog/about-swift-concurrency-performance