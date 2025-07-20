# Say hello to the new look of app icons

## Overview

<img width="629" height="349" alt="Image" src="https://github.com/user-attachments/assets/bb73161a-1c0c-45c7-bfb1-2069e2149010" />

Vision OS 의 Layered Icon, 실제 유리의 특성을 앱 아이콘을 위한 Liquid glass material 로 결합시킴

Edge highlight, 흐림 효과, 반투명 효과를 레이어링 해서 입체감을 타나내주고 아이콘 안쪽에서 빛나는 효과를 줌

<video controls src="ScreenRecording_07-05-2025 18-55-52_1.mov" title="Title"></video>

Gyro 입력 (방향 감지 센서)를 바탕으로 아이콘 가장 자리를 따라 빛이 움직이는 것처럼 보임
(영상은 iOS 기기를 회전하면서 사진 아이콘에서 빛이 어떻게 움직이는지를 확인한 영상)

<img width="597" height="420" alt="Image" src="https://github.com/user-attachments/assets/d396f0ba-146e-4dd8-a4a5-b124cb971803" />

* 새로운 mode
    * Dark tint : Foreground 에 색 추가
    * Light tint : Glass 에 색 추가


## Design system

<img width="470" height="197" alt="Image" src="https://github.com/user-attachments/assets/8da99d55-9346-408a-aef2-35daf79d25a3" />

기존에는 플랫폼 별로 아이콘 이미지가 달라 추가 제작을 해야 하는 경우가 있었음

<img width="423" height="277" alt="Image" src="https://github.com/user-attachments/assets/d674fb0a-c9a6-43cc-9d09-000efd7031a3" />

새로운 아이콘 언어의 통합으로 직사각형 / 원형 형태로 통합된 디자인 가능

<img width="257" height="248" alt="Image" src="https://github.com/user-attachments/assets/66196684-9705-4131-86ce-452dd208b9d3" /><img width="252" height="250" alt="Image" src="https://github.com/user-attachments/assets/19181202-25ea-47bb-99c8-40ff5e900f7d" />

* 더 단순하고 간격이 균일하게 디자인 되도록 grid 업데이트
* 모서리가 더 둥글게 바뀜
* 1024 px 캔버스
* 원 모양 아트워크는 그리드 내에 프레임이 지정되어 있으며(아이콘 artwork 가 차지할 수 있는 프레임이 지정되어 있다는 뜻인듯) 여유공간이 많아짐

<img width="493" height="320" alt="Image" src="https://github.com/user-attachments/assets/2985a152-5f44-476c-bb32-11e3274ced86" />

Figma, Sketch, Photoshop, Illustrator 에서 새로운 디자인 시스템을 기반으로 업데이트 된 템플릿 사용 가능

[Apple Design Resource Page](https://developer.apple.com/design/resources/) 에서 템플릿 확인 가능

## Drawing icons

### Layering

새로운 Apple Design 언어의 핵심 요소

아이콘은 Foreground, background Layer 가 존재 (e.g. message app)
메세지 앱은 하나의 foreground layer가 존재해도 말풍선에 반투명 효과, 그림자를 통해 입체감을 부여함

**Background Layer 는 한 개, Foreground Layer 는 여러개 존재 가능**

새로운 레이어링 효과를 통해 도형을 스택으로 쌓아서 차원을 표현함

<img width="552" height="455" alt="Image" src="https://github.com/user-attachments/assets/98ba9944-7e06-45a2-a79a-a24f5c5af157" />

* More flat

<img width="587" height="271" alt="Image" src="https://github.com/user-attachments/assets/ed555b87-d117-4bf1-822e-76a7a09f8024" />

* material 활용

<img width="601" height="327" alt="Image" src="https://github.com/user-attachments/assets/021e181b-a9d8-416a-8e13-f50d6a5ef327" />

### Translucency

반투명 효과는 미묘한 차이, 가벼움, 깊이감을 디자인에 더해줌

<img width="894" height="235" alt="Image" src="https://github.com/user-attachments/assets/2f49c1f3-b227-4a9e-94b6-20d858f6513b" />

Light, Dark, 투명 모드에서 모두 적용 가능

### Simplifying

Less is more 

<img width="286" height="289" alt="Image" src="https://github.com/user-attachments/assets/99ac6f39-d393-4963-8907-bc19832bf341" />

<img width="276" height="280" alt="Image" src="https://github.com/user-attachments/assets/a06dabd4-99c8-47ec-965e-99084bfe3bb2" />

* 기존
    * 이미 투명도를 사용해서 겹치는 부분 강조, 전체 도형에 차원 추가
* 신규
    * 겹치는 부분 줄임
    * 업데이트 된 그리드 사용, 여유 공간 많아짐
    * 깊이감 더함

Material recipe 에서 여러 동적 효과들을 사용할 수 있기 때문에 기존 artwork 에 빌드된 정적 효과도 다시 연결하는 것을 추천

<img width="583" height="273" alt="Image" src="https://github.com/user-attachments/assets/ff9ada82-d409-42e4-bdf4-8d2a08efc273" />

* 이전 디자인을 단순화
* Glass 효과 적용

#### 디테일

<img width="794" height="523" alt="Image" src="https://github.com/user-attachments/assets/28912967-6255-42ea-ae56-a96be394a7f3" />

이상적으로 날카로운 가장자리와 얇은 선은 피하고 둥근 모서리를 사용하면 가장자리를 따라 빛이 자연스럽게 빛날 수 있음

### Backgrounds 

* 실제 조명 효과가 밝은 색 -> 어두운 색을 따 갈 때 어울림

<img width="713" height="281" alt="Image" src="https://github.com/user-attachments/assets/8cbcf218-26ff-4347-965b-9a301970a3b1" />


* 순 흰 / 검 대신 System light / dark Graident 사용

<img width="773" height="364" alt="Image" src="https://github.com/user-attachments/assets/d75debf4-957e-47a5-9a57-67b7384dd3a5" />

* Dark 모드를 대비해 색상 배경을 더 많이 사용하면 모드 전환 시 구별이 더 잘 됨

<img width="759" height="268" alt="Image" src="https://github.com/user-attachments/assets/9b05b1a9-21a6-4c51-97ec-5835bf1f1795" />

* 참고
    * HIG 가이드 : https://developer.apple.com/design/human-interface-guidelines/app-icons
    