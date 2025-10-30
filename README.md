# Facebook DeiT Tiny patch16-224 CIFAR-10 Fine-tuning, to `.tflite`
- 기존 output 1,000 개를 CIFAR-10 에 맞춰서 10개로 줄여 파인튜닝
- QLoRA 사용
- `.tflite` 결과물 도출
- 8비트 동적 양자화

## 실습환경
- Colab 에서 작동 안함
  - ai-edge-torch 패키지가 불안정한 관계로, Colab 의 현재 dependency 와 일치하지 않음
  - GPU 가 없다면, 1장까지 Colab GPU 로 진행해서 가중치를 도출하고, 2장부터 로컬에서 CPU torch 를 사용해 구현할것을 권장함
- torch, torchvision 은 Colab 의 경우 미설치, 로컬의 경우 홈페이지 접속해서, 운영체제와 CUDA 버전 맞춰서 설치
- torch 는 2.8 을 설치해야 함. 현재 2.9 가 stable 인데, ai-edge-torch 에서 지원 안함
- https://pytorch.org/get-started/previous-versions/

- hf-transfer 환경변수 지정 시, 훨씬 빠르게 모델 다운로드 가능
  - `HF_HUB_ENABLE_HF_TRANSFER=1`

## 간단하게 작동만 시켜보고 싶다면?

- 테스트 환경
  - Ubuntu 22.04 Server
  - CPU 1 Core
  - RAM 1GiB (더 줄이면 커널패닉 발생)


```bash
python ./run-tflite.py
```

```
testset 이미지 갯수: 10000
실제 테스트할 이미지 갯수: 5
INFO: Created TensorFlow Lite XNNPACK delegate for CPU.
================================
테스트 실행
================================
img_idx: 0
time: 0.015086650848388672
================================
img_idx: 1
time: 0.011963844299316406
================================
img_idx: 2
time: 0.011947870254516602
================================
img_idx: 3
time: 0.011883974075317383
================================
img_idx: 4
time: 0.011890411376953125
================================
테스트 종료
================================

--- TFLite 모델 평가 결과 ---
TFLite 모델 테스트 정확도: 100.00%
평균 단일 이미지 추론 시간: 0.0126초
```

- 전체 10,000 개 테스트 이미지를 적용할 시, 약 92.31% 의 정확도 나옴

## 완전정수양자화 (integer-only quantization)
- 실제로 ESP32, STM32 에 올리고 싶다면, 완전정수양자화 도전해볼 것
  - ai-edge-torch 에서 공식적으로 제공 안함
  - PyTorch 가 아닌 TensorFlow 에서 모델을 직접 만든 후, integer-only quantization 한 `.tflite` 모델로 변환할 것
  - [튜토리얼 링크](https://ai.google.dev/edge/litert/models/post_training_integer_quant)

## 발전방향
- ESP32 Arduino IDE 에서 LiteRT C++ 라이브러리를 사용해 각종 프로젝트 진행 가능
- LiteRT C++ 라이브러리는 아두이노 기반으로 개발되었음
  - [참고링크](https://ai.google.dev/edge/litert/microcontrollers/overview)