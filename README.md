# 파일 설명

## cropImage.ipynb 
- OCR Prototype End-To-End
- Text Detection -> Super Resolution -> Text Recognition
- Text Detection 결과 
### ![image](https://user-images.githubusercontent.com/54635552/177793143-e32f5b45-6706-40f6-82bc-b71492398c1a.png)
- Text Recognition 결과 
### ![image](https://user-images.githubusercontent.com/54635552/177793476-46d9b2d6-8a11-456e-8d71-edfe79b692e2.png)


## Detection_SR_Recognition_drawtext.ipynb : 이미지에 글씨쓰기
- 원본 Image
### ![image](https://user-images.githubusercontent.com/54635552/177794178-4d481211-aef5-4f8a-a341-00dbe05e7391.png)
- OCR 된 결과 Image에 Text 적기
### ![image](https://user-images.githubusercontent.com/54635552/177794282-a0dc297c-68f7-40d9-b892-997b136b532d.png)


## SuperResolution_Recognition_deomo.ipynb Post-Processing, TTS 추가
- <a href="https://www.notion.so/Post-Processing-f8d9e4022d844ab99c88ea2103eb45b6">각종 맞춤법검사기 추가</a>
### ![image](https://user-images.githubusercontent.com/54635552/177795444-a669f0b1-b309-41ca-a5da-d1bce81284e1.png)

- 구글 TTS 추가
### ![image](https://user-images.githubusercontent.com/54635552/177795345-4ae24ac1-8454-46b4-981d-c3eb5dfca092.png)


# Ko-OCR Model 구조

## Pre Processing(도입여부 고민)
- <a href="https://stump-rosehip-2a5.notion.site/Pre-Processing-5f15da1c3b554b8bb9108671d457e19f">Pre Processing</a>

## Text Detection
- <a href="https://github.com/ultralytics/yolov5">yolov5</a>
- <a href="https://github.com/meituan/YOLOv6">yolov6</a>
- <a href="https://github.com/Megvii-BaseDetection/YOLOX">yoloX</a>
- <a href="https://github.com/jinfagang/yolov7">yolov7</a>
- 실시간 처리를 위한 Yolo 모델 사용

## Super Resolution
- <a href="https://github.com/jingyunliang/swinir">SwinlR</a>
- SR분야에서 SOTA Model인 SwinIR 사용

## Text Recognition
- <a href="https://github.com/clovaai/deep-text-recognition-benchmark">deep-text-recognition-benchmark</a> 의 STAR-Net
- <a href="https://github.com/PaddlePaddle/PaddleOCR?utm_source=catalyzex.com">SVTR-Tiny</a>
- SVTR 논문 정리 
- https://stump-rosehip-2a5.notion.site/SVTR-Scene-Text-Recognition-with-a-Single-Visual-Model-9c9ff5eda4964493ac2e96521456ae96

## Post Processing
- <a href="https://www.notion.so/Post-Processing-f8d9e4022d844ab99c88ea2103eb45b6">각종 맞춤법검사기</a>

## Application
- TTS 
구글에서나온 TTS API사용
- Translation
기존에 학습한 Attention LSTM 한영 번역기를 사용해서 Translation을 하려했지만, Model Train당시 Dataset이 구어체기반이라 Inference가 General하지 못한 상황발생
이를 해결하기위해 네이버 파파고API 연동하여 OCR로 나온 한글 Text -> 영어로 Translation하는 작업진행
- Summarization
KoBart와 TextRank 사용하여 문서 요약

## 모델 최적화(미정)
- <a href="https://stump-rosehip-2a5.notion.site/TensorRT-144ae215503b4392918220334052b719">TensorRT</a>
- <a href="https://stump-rosehip-2a5.notion.site/OpenVINO-0301abbce9144c74bd2b8b99861227d4">OpenVINO</a>

## Web,APP 서비스구현(미정)
- Django를 이용하여 Web상 Service 서빙
- Flutter를 이용한 APP개발

## Experiment
### Inference 속도를 올리기위한 노력
- Text Detection Model을 실시간에 강점인 One-Stage Yolo Model을 사용하여 Inference 속도를 높임
- Super Resolution SwinIR x4 Model의 경우 Inference 속도측정결과 Crop Image 한장당 약 1초가 소요되어, x2 Model로 변경후 Crop Image 한장당 0.5초로 소요시간 1/2단축
- Text Recognition Model을 기존 clovaai의 Start-Net에서 SVTR으로 변경하여 Inference속도를 대폭끌어올림

### Accuracy 를 높이기 위한 노력
- 기존 OCR은 Text Detection과 Text Recognition을 진행하여 OCR를 진행하는데, 이점에서 OCR의 성능을 올리고자
Text Detection -> Crop Image -> Crop Image에 대해 각각 Super Resolution 진행후 -> Text Recognition 하여 Text Recognition에서 성능을 높이고자고시도함
- Post Processing을 도입하여 Text Recognition에서 나온결과값의 정확도를 최대한 높일려고 시도함
- Pre Processing을 통해 글자를 잘 인식하도록 OpenCV로 Image 조작
- Text Detection Yolo의 여러가지 버전과 모델 Yolov5,Yolov6,YoloX,Yolov7 학습
- Text Recognition에서 Custom Dataset을 Data Augmentation을 진행하여 데이터수를 늘려서 학습함
- 학습 DataSet의 여러번 변경

## DataSet
AI Hub 데이터셋으로 학습을 진행하였음, 그과정에서 Data Augmentation을 진행하여 데이터수를 늘려서 학습함
