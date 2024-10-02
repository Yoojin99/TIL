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
   2. Apple Intelligence 에 앱 세계를 연결 하기 위한 수단으로 앱 인텐트 프레임워크에 투자함

## App Intent 도메인

<img width="484" alt="image" src="https://github.com/user-attachments/assets/d28b6046-991a-4d82-9c0c-fe0dcfd814b4">

* 특정 기능을 위해 설계된 앱 인텐트 기반 API 모음 e.g. Books, 카메라, 스프레드 시트
* iOS 18 에서 도메인 중 12 개 출시 
* 각 도메인에 다양한 액션 포함됨. 액션은 유연한 음성 상호작용을 지원하고 쉽게 적용할 수 있게 학습/테스트 됨
  * 액션 예시 : Darkroom 같은 앱에서 "photos.setFilter" 인텐트를 사용할 수 있으므로 사용자가 대신 "어제 내가 찍은 내 셀카에 시네마틱 프리셋을 적용해 줘" 라고 음성으로 말해서 작업을 수행할 수 있음

# Actions

## Build

## Assistant Schema

Assistant Schema 로 앱 인텐트 도메인에 대한 작업을 빌드하는 방법

*Schema? : 사전상 의미는 클래스의 모든 멤버에게 공통적으로 적용되는 개념. 일반적이거나 필수적인 유형/양식.*

* Apple Intelligence 는 foudation model 에서 구동됨
* Apple Intelligence 는 앞선 도메인 예시들에서 Siri 가 새로운 작업을 할 수 있게 해줌 
* Foundation model 은 **특정 형태**의 인텐트를 예상하도록 학습됨
* 이 특정 형태를 스키마라 함.
* Assistant Schema 를 API 라 부름
* App Intent 를 올바르게 구축하면 자연어의 복잡성을 걱정할 필요 없이 Apple Intelligence 가 학습한 것의 이점을 얻을 수 있음 : `perform` 메서드만 작성하고 나머지는 플랫폼이 알아서 처리하게 두기

### 




