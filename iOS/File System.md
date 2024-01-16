# File System

**데이터 파일, 앱, OS 자체와 관련 있는 파일들을 저장하는 persistent storage(기기의 전원이 꺼져도 유지되는 저장소) 를 관리함.**

APFS : macOS, iOS, watchOS, tvOS 의 기본 파일 시스템

포맷과는 상관 없이 기기에 부착된 모든 디스크 (물리적으로 플러그인 되어있거나 네트워크를 통해 간접적으로 연결된 경우) 는 파일들의 signle collection 을 생성하기 위한 공간을 마련함. 파일 자체는 굉장히 많아질 수 있으므로 파일 시스템은 디렉토리를 사용해서 계층적인 구조를 만듦. iOS, macOS 의 기본 디렉토리 구조는 비슷하지만 앱과 사용자 데이터를 구성하는 방법에는 차이가 있음.

## iOS File System

iOS 파일 시스템은 iOS 에서 실행하는 앱에 맞춰져 있음. 시스템을 간단하게 유지하기 위해 iOS 기기의 사용자들은 파일 시스템에 직접 접근할 수 없으며 앱은 이 convention 을 따름.

### iOS 표준 디렉토리 : 파일들이 위치하는 곳

보안상 이유로 iOS 앱-파일 시스템 간의 상호작용은 앱의 sandbox 디렉토리 내의 디렉토리로 제한되어 있음. 새로운 앱을 설치하게 되면 installer 는 sandbox 디렉토리 내에 앱을 위한 container 디렉토리를 여러개 만듦. 각 container 디렉토리는 특정 역할이 있음. 

* Bundle container 디렉토리 : 앱의 bundle 을 담고 있음
* Data container 디렉토리 : 앱, 사용자 모두를 위한 데이터를 담고 있음
  * 앱이 데이터를 정렬하고 구성하는데 사용할 수 있는 여러 개의 sub 디렉토리로 나눠질 수 있음
* (추가적인) container 디렉토리 : e.g. 런타임에 iCloud 컨테이너 사용 가능

Container 디렉토리들은 파일 시스템에 대한 앱의 primary view 를 구성함. 

![](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/art/ios_app_layout_2x.png)

앱의 sandbox 디렉토리를 표현한 것.

앱은 자신의 container 디렉토리 외부에 접근하거나 외부에 파일을 생성하는 것이 금지되어 있음. 단 사용자 연랓거 / 음악과 같은 것에 접근하기 위해 public 시스템 interface 를 사용하는 경우는 예외. 이 경우 시스템 프레임워크는 helper app 을 사용해서 필요한 파일 관련 연산을 처리하거나 적절한 데이터를 읽고 수정하는 것을 처리함.

### Sandbox 내 Important sub 디렉토리

1. *`앱이름`*`.app`

앱의 bundle. 앱과 모든 리소스틀 포함함. 

이 디렉토리에 쓰기 불가능. 마음대로 사용되는 것을 방지하기 위해 bundle 디렉토리는 설치할 때 signed 됨. 이 디렉토리에 쓰기를 하면 signature 가 변하고 앱이 실행되지 않음. 하지만 앱 번들에 저장된 모든 리소스에 대한 읽기 전용 권한을 얻을 수는 있음. 

이 디렉토리의 컨텐츠는 iTunes / iCloud 에 의해 백업되지 않음. 

2. `Documents/`

이 디렉토리를 사용해서 사용자가 만든 컨텐츠를 저장함. File sharing 을 통해 사용자가 디렉토리 내 컨텐츠를 사용하게 할 수 있음. 즉 이 디렉토리에는 사용자에게 노출하고 싶은 파일들만을 포함해야 함. 

이 디렉토리의 컨텐츠는 iTunes / iCloud 에 의해 백업됨

3. `Documents/Inbox`

외부 엔티티에 의해 앱이 열도록 요청된 파일들에 접근하기 위해 사용함. e.g. Mail 프로그램은 이 디렉토리 내에 앱과 관련된 이메일 attachment 들을 저장함. Document interaction controller 들도 이 디렉토리 내 파일들을 저장할 수 있음.

앱은 이 디렉토리내 파일들을 읽기/삭제 가 가능하지만 새로운 파일을 생성하거나 이미 존재하는 파일에 쓰기를 하지 못함. 만약 사용자가 이 디렉토리에 있는 파일을 수정하려고 한다면 앱은 수정사항을 만들기 전에 디렉토리에서 무조건 나와야 함(?) <- 무슨 말일까?

4. `Library/`

사용자 데이터 파일이 아닌 파일들의 최상위 디렉토리. 주로 여러 표준 sub 디렉토리 중 하나에 파일을 저장함. iOS 앱은 주로 `Application Support`, `Caches` sub 디렉토리를 사용함. 직접 커스텀 sub 디렉토리를 생성하는 것도 가능함.

사용자에게 노출하고 싶지 않은 파일들을 저장할 때 사용. 앱은 사용자 데이터 파일들을 위해 이 디렉토리들을 사용하지 않음.

iTunes, iCloud 에 의해 백업됨. (`Caches` sub 디렉토리는 예외)

5. `tmp/`

앱을 실행할때마다 지속할 필요가 없는 (앱이 꺼져도 날아가도 되는) 임시 파일들을 만들 때 이 디렉토리를 사용. 앱은 더 이상 필요 없을 때 이 디렉토리 내 파일들을 지워야 함. 근데 시스템에서 앱이 실행되고 있지 않을 때 이 디렉토리를 숙청할 수 있음.

iTunes / iCloud 에 의해 백업되지 않는다.

iOS 앱은 파일들을 더 잘 정리하기 위해 `Documents`, `Library`, `tmp` 디렉토리에 추가적인 디렉토리를 생성할 수 있음. 


### 앱의 파일을 어디에 저장할까?

iOS 기기의 동기화 / 백업 이 오래 걸리는 것을 방지하기 위해 파일들을 어디에 위치시킬지 결정하는 것은 중요함. 큰 용량의 파일을 저장하는 앱은 iTunes / iCLoud 에 백업하는 게 느릴 수 있음. 또한 이런 앱들은 사용자의 가용 공간을 많이 잡아먹기 때문에 사용자가 앱을 지우거나 iCloud 에 백업하지 않게 해버릴 수 있음. 

* `Documents/` : 사용자 데이터. 주로 사용자에게 노출하고 싶은 파일들을 포함함. 사용자가 생성, import, 삭제, 수정할 파일. 
* `Library/Application support/` : 앱이 생성한 support 파일. 
* `Documents/`, `Application support/` 파일들은 기본적으로 백업됨.
* `tmp`: 임시 데이터
* `Library/Caches/` : 캐시 파일.


* 참고
* https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW2