# 온디바이스 기반 모델에 대한 프롬프트 디자인 및 안정성 살펴보기

* prompt
    * Response 를 guide 하기 위해 AI model 에 입력하는 text input
    * 자연어로 작성
    * Apple Intelligence 에서 지원하는 모든 언어로 사용 가능

![alt text](image-27.png)

Prompt -> Foundation Model Framework (LLM) -> response

Writing Tools 에서 사용하는 것과 동일한 모델임


## Design for on-device LLM

다양한 작업을 수행할 수 있지만 소형 device 에 맞게 최적화되어 있기 때문에 서버 기반 LLM 보다 성능이 떨어질수밖에 없음

* 수행 가능 작업
    * Summarization
    * Classification
    * Multi-turn
    * Composition
    * Revision
    * Tagging

Tip : 복잡한 작업인 경우 여러 간단한 단계로 나눠 수행



## Prompting best practices

## Design for safety

## Evaluate and test