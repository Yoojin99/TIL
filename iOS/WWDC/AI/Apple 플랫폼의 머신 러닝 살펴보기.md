# Explore machine learning on Apple platforms

부제 : Apple 플랫폼의 머신 러닝 살펴보기

WWDC 24

1. Apple Intelligence
2. ML-powered APIs
3. Running models on device
4. Research

# Apple Intellignece

Apple Intelligence 는 OS 에 이미 내장되어 있음. 

* 지원하는 기능
  1. Writing Tools : 텍스트 검사를 통해 재작성 / 실수 교정 / 요약 기능 제공
  2. Image Playground : 이미지 제작. 모델이 로컬에서 실행되기 때문에 별도 비용이 들지 않음
  3. Siri 개선 : Siri 가 더욱 자연스럽게 대화하고 관련성 있는 정보를 제공할 수 있도록 개선됨. App Intent 프레임워크를 사용해서 앱 고도화 가능

# ML-powered APIs

개발자가 자신만의 intelligent 기능을 제공하려면 모델을 정적으로 처리할 필요없이 지원되는 API, 프레임워크를 사용할 수 있음

1. Vision 프레임워크 : 시각적 인텔리전스를 위한 기능 제공. e.g. 사진에서 텍스트 추출, 얼굴인식, 자세 인식 등 
   * Swift 6와 호환되는 API
   * 신체 자세 + 손 포즈 감지
   * 미적 점수
2. 자연어 처리
3. Speech
4. Sound Analysis
5. 번역
    * 간단한 UI : 간단한 번역 presentation UI 를 사용해서 번역 가능
    * 유연한 API : 텍스트를 번역해서 UI 에 표시 가능 
    * 효율적인 batching : 요청을 일괄처리하고 많은 텍스트를 효율적으로 번역 가능

이 외에도 많은 Apple ML 기반 기능이 제공됨

# Create ML

특정 usecase 에 맞게 모델을 custom 해야 할 경우 Create ML 사용.

<img width="581" alt="image" src="https://github.com/user-attachments/assets/830d3fff-5a02-4002-ae9c-12e510b2a73e">

* 자체 데이터로 프레임워크를 지원하는 모델 커스텀할 수 있음
* 작업에 맞는 템플릿을 선택한 후 일련의 작업을 통해 데이터로 모델 훈련, 평가, 반복 실행 가능
* Create ML 앱 뿐만 아니라 Create ML, Create ML components 프레임워크를 사용해서 모든 플랫폼(ios, macOS, tvOS, watchOS 등)에서 모델을 훈련시킬 수 있음

* 신규 기능
  * 객체 추적 템플릿 : vision OS 에서 공간에 고정시키기 위한 참조 객체 학습 가능
  * 데이터 탐색 : 학습 전에 데이터 주석 탐색 / 검토 쉬워짐
  * time series model : 시계열 분류 / 예측 구성 요소를 앱 내에서 통합 가능

# 기기에서 모델 실행하기

더 복잡한 use case 에 해당

Fine-tuning, 최적화된 모델 / 오픈 소스 커뮤니티에서 다운받은 대규모 언어 모델(e.g. hugging face) 를 실행하려고 하는 경우. 

다양한 모델을 여러 기기에서 일련의 과정을 거쳐 사용할 수 있음

## 개발자 작업

<img width="440" alt="image" src="https://github.com/user-attachments/assets/ad961b03-bf9a-4077-88d3-d9fb9f1339f1">

1. Train : 모델 아키텍처 정의, 학습 데이터 제공, 모델 학습
2. Prepare : 배포를 위해 모델을 Core ML 형식으로 변환. 모델 매개변수 최적화 등을 통해 목표 성능 달성
3. Integrate : 모델을 load, 실행하기 위해 Apple 프레임워크와 통합하는 코드 작성

### Train

<img width="539" alt="image" src="https://github.com/user-attachments/assets/e3005af5-9d48-4fbd-88e9-6f9811f5ec38">

* Apple Silicon 기반 Mac 의 통합 메모리 아키텍처를 사용해서 PyTorch, TensorFlow, JAX 등 학습 라이브러리를 사용해서 모델 설계 및 학습 가능
* 학습 라이브러리는 Apple GPU 에서 효율적인 학습을 위해 Metal 사용

*자세한 내용은 "Train your machine learning and AI models on Apple GPUs" 영상에서 확인. Scaled dot product attention 에 대한 향상된 학습 효율성, PyTorch에서 사용자 정의 Metal operation 을 통합하는 법, JAX 에 새로 추가된 혼합 정밀도에 대한 내용이 나와 있음*

### Prepare

<img width="405" alt="image" src="https://github.com/user-attachments/assets/05ec346a-424a-4bd6-8c96-4e3c5d707052">

* Core ML Tools 를 사용해서 학습된 모델을 Core ML 형식으로 바꿀 수 있음
* Core ML Tools 의 다양한 압축 기법을 사용해서 Apple 하드웨어에 맞게 모델을 최적화 할 수 있음
* 신규 기능
  * 새로운 모델 압축 기술
  * State, Transformer 관련 작업
  * 하나의 모델에 여러 기능을 포함할 수 있는 방법

*자세한 내용은 "Bring your machine learning and AI models to Apple silicon" 영상 참고*

### Integrate

* 모델을 load, 실행하기 위해 OS 프레임워크와 상호작용하는 코드 작성 

## Core ML

* Apple 기기에 모델을 배포하기 위한 게이트웨이.
* 요구되는 성능은 도달하면서도 Xcode 통합을 통해 개발 작업 플로우를 간소화
* 하드웨어 활용을 극대화하기 위해 CPU, GPU Neural Engine 에 나눠 모델 segment 를 분할함

*"Deploy machine learning and AI models on-device with Core ML" 영상에서 AI 모델을 실행하는데 도움이 되는 Core ML 기능 다룸. 모델을 연결하는 연산 glue 코드를 간소화하기 위해 디자인된 MLTensor 유형 소개, 상태 정보가 있는 대규모 언어 모델을 효율적으로 디코딩하기 위한 키-값 캐시 관리 방법, 런타임 이미지 생성 모델에서 특정 스타일 어댑터를 선택하기 위한 함수 사용법, 업데이트 된 성능 보고서에 대한 내용이 나와 있음*

<img width="470" alt="image" src="https://github.com/user-attachments/assets/cde58f6b-72c3-4b3b-817f-4e322de1d5eb">

* Core ML 은 모델을 on device 에 배포할 때 가장 많이 사용되는 프레임워크
* 머신 러닝 작업을 할 때 Core ML 보다 세밀하게 제어해야 하는 경우 존재
  * Metal 의 MPS Graphs : 방대한 그래픽 처리가 필요한 경우. ML 작업의 sequence 를 설정해서 GPU 활용 최적화
  * Accelerate 의 BNNS Graph : ML 작업에 대해 엄격한 지연 시간 / 메모리 관리 제어 기능 제공
* Domain API 와 함께 앱에서 Apple 플랫폼의 다양한 머신 러닝 도구/API (e.g. vision, 자연어 처리, 소리 분석, 음성, 번역) 에 액세스 가능
  * Apple 모델이 제공하는 기본 API 로 시작하거나 Apple 프레임워크를 사용해서 머신 러닝 / AI 모델을 직접 배포

# Research

Apple 은 ML, AI 구동 기반을 제공하기 위한 노력 (연구) 를 하고 있음

* 관련 연구는 사이트에 공개되어 있음 : https://machinelearning.apple.com
* 샘플 코드, 데이터 set, MLX 같은 연구 도구를 오픈 소스를 통해 제공

## MLX

Apple 머신 러닝 연구진이 설계한 것

* 친숙 / 확장 가능 API 제공
* Apple Silicon 을 위해 디자인 됨 : CPU,  GPU 에서 효율적인 작업이 가능하게 함
* Multi-language 지원 : Python, C++, Swift 
* document : https://ml-explore.github.io/mlx/build/html/index.html
* github : https://github.com/ml-explore/mlx
















