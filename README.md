<div width="100%" height="100%" align="center">
  
<h1 align="center">
  <p align="center">초소형 머신러닝 TinyML</p>
  <a href="https://www.hanbit.co.kr/store/books/look.php?p_code=B3963656224">
    <img width="50%" src="cover.jpg" />
  </a>
</h1>
  

<b>피트 워든 · 대니얼 시투나야케 저, 맹윤호 · 임지순 역</b></br>
한빛미디어 · 2020년 08월 20일 출시</br>
[[github](https://github.com/yunho0130/tensorflow-lite)] | [[errata](https://www.hanbit.co.kr/store/books/look.php?p_code=B3963656224)]</b> 

</div>

## :bulb: 목표

- **TensorFlow Lite를 이용한 임베디드 머신러닝을 실습한다.**

  > TFLite를 이용해 MCU상에서 DNN 구축하기.

<br/>

## 🚩 정리한 문서 목록

### 🔨 TinyML model 구축하기(sine wave predictor)

 - [sine wave predictor model 구축하기](https://github.com/erectbranch/TinyML_Basic/blob/master/ch04/sine.ipynb)

   > model 구축: dependencies 가져오기/numpy를 사용한 난수 생성/data split(train, validation, test)/model 정의하기/model training/결과 시각화/fine tuning 

   > model 변환(TFLite): TFLiteConverter, TFLiteConverter.optimizations(quantization), TFLite 변환 model을 이용한 inference 수행(tf.lite.Interpreter)

   > model 변환(C source file): xxd를 사용한 byte array 변환

 - [TFLite model test 작성하기](https://github.com/erectbranch/TinyML_Basic/tree/master/ch05/summary01)

   > test 구축: dependencies 가져오기/test 설정(TF_LITE_MICRO_TEST MACRO)/logging 준비(MicroErrorReporter)/model mapping(GetModel(), TFLITE_SCHEMA_VERSION을 이용한 호환성 검사)/AllOpsResolver 생성/tensor arena 정의/Interpreter 생성(MicroInterpreter, AllocateTensors())/input tensor test(TfLiteTensor, assertion)/inference/output 확인(TfLiteTensor)

   > test 실행 환경 구축, test 실행하기

   > Make를 이용한 빌드 기초

 - [TFLite model test 파일 구조](https://github.com/erectbranch/TinyML_Basic/tree/master/ch05/summary02)

   > main_functions.cc(inference_count, setup(), loop()), output_handler.cc(HandlerOutput()), main.cc(main()), test 빌드와 실행

<br/>

## :mag: 목차

### CHAPTER 1 서론

    1.1 임베디드 장치

    1.2 생태계 변화



### CHAPTER 2 시작하기

    2.1 이 책의 대상 독자

    2.2 개발에 필요한 하드웨어

    2.3 개발에 필요한 소프트웨어

    2.4 이 책에서 배울 내용

 

### CHAPTER 3 머신러닝 빠르게 훑어보기

    3.1 머신러닝이란 무엇인가

    3.2 딥러닝 워크플로

    3.3 마치며

 

### CHAPTER 4 TinyML ‘Hello World’ 시작하기: 모델 구축과 훈련

    4.1 만들고자 하는 시스템

    4.2 머신러닝 도구

    4.3 모델 구축하기

    4.4 모델 학습시키기

    4.5 텐서플로 라이트용 모델 변환

    4.6 마치며

 

### CHAPTER 5 TinyML ‘Hello World’: 애플리케이션 구축

    5.1 테스트 작성

    5.2 프로젝트 파일 구조

    5.3 소스 코드 살펴보기

    5.4 마치며

 

### CHAPTER 6 TinyML ‘Hello World’: 마이크로컨트롤러에 배포하기

    6.1 마이크로컨트롤러란 무엇인가

    6.2 아두이노

    6.3 스파크펀 에지

    6.4 ST마이크로 STM32F746G 디스커버리 키트

    6.5 마치며

 

### CHAPTER 7 호출어 감지: 애플리케이션 만들기

    7.1 만들고자 하는 시스템

    7.2 애플리케이션 아키텍처

    7.3 테스트 코드

    7.4 호출어 듣기

    7.5 마이크로컨트롤러에 배포하기

    7.6 마치며

 

### CHAPTER 8 호출어 감지: 모델 훈련하기

    8.1 새로운 모델 훈련

    8.2 프로젝트에서 모델 사용

    8.3 모델 작동 방식

    8.4 나만의 데이터로 훈련하기

    8.5 마치며

 

### CHAPTER 9 인체 감지: 애플리케이션 만들기

    9.1 만들고자 하는 시스템

    9.2 애플리케이션 아키텍처

    9.3 테스트 코드

    9.4 인체 감지

    9.5 마이크로컨트롤러 배포

    9.6 마치며

 

### CHAPTER 10 인체 감지: 모델 훈련하기

    10.1 연산 환경 선택

    10.2 구글 클라우드 플랫폼 인스턴스 설정

    10.3 훈련 프레임워크 선택

    10.4 데이터셋 구축하기

    10.5. 모델 훈련하기

    10.6 텐서보드

    10.7 모델 평가하기

    10.8 텐서플로 라이트로 모델 내보내기

    10.9 다른 카테고리 훈련

    10.10 아키텍처 이해

    10.11 마치며

 

### CHAPTER 11 마술 지팡이: 애플리케이션 만들기

    11.1 만들고자 하는 시스템

    11.2 애플리케이션 아키텍처

    11.3 단계별 테스트

    11.4 제스처 감지

    11.5 마이크로컨트롤러에 배포

    11.6 마치며

 

### CHAPTER 12 마술 지팡이: 모델 훈련하기

    12.1 모델 훈련하기

    12.2 모델의 작동 방식

    12.3 나만의 데이터로 훈련하기

    12.4 마치며

 

### CHAPTER 13 마이크로컨트롤러용 텐서플로 라이트

    13.1 마이크로컨트롤러용 텐서플로 라이트란 무엇인가

    13.2 빌드 시스템

    13.3 새로운 하드웨어 플랫폼 지원

    13.4 새로운 IDE나 빌드 시스템 지원

    13.5 프로젝트와 저장소 간 코드 변경 사항 통합

    13.6 오픈소스에 기여

    13.7 새로운 하드웨어 가속기 지원

    13.8 파일 포맷 이해

    13.9 텐서플로 라이트 모바일 작업을 Micro에 포팅하기

    13.10 마치며

 

### CHAPTER 14 자신만의 TinyML 애플리케이션 설계하기

    14.1 설계 과정

    14.2 마이크로컨트롤러가 필요할까, 더 큰 장치가 필요할까?

    14.3 무엇이 가능한지 이해하기

    14.4 다른 사람의 발자취 따르기

    14.5 훈련할 모델 찾기

    14.6 데이터 관찰

    14.7 오즈의 마법사 방법론

    14.8 데스크톱에서 먼저 작동시키기



### CHAPTER 15 지연 최적화

    15.1 정말 중요한 문제인지 확인하기

    15.2 하드웨어 변경

    15.3 모델 개선

    15.4 양자화

    15.5 제품 설계

    15.6 코드 최적화

    15.7 연산 최적화

    15.8 오픈소스에 기여

    15.9 마치며

 

### CHAPTER 16 에너지 사용 최적화

    16.1 직관 기르기

    16.2 실제 전력 소모 측정하기

    16.3 모델의 전력 사용량 추정

    16.4 전력 소모 개선

    16.5 마치며

 

### CHAPTER 17 모델과 바이너리 크기 최적화

    17.1 시스템의 한계 이해

    17.2 메모리 사용 측정

    17.3 다양한 문제에 대한 모델 정확도와 크기

    17.4 모델 선택

    17.5 실행 파일 크기 줄이기

    17.6 정말로 작은 모델

    17.7 마치며

 

### CHAPTER 18 디버깅

    18.1 훈련과 배포 사이 정확도 손실

    18.2 수치의 차이

    18.3 알 수 없는 충돌과 중단

    18.4. 마치며



### CHAPTER 19 텐서플로에서 텐서플로 라이트로 모델 포팅하기

    19.1 필요한 Op 이해

    19.2 텐서플로 라이트의 기존 Op 범위 살펴보기

    19.3 전처리 및 후처리를 애플리케이션 코드로 이동

    19.4 Op의 구현

    19.5 Op 최적화

    19.6 마치며

 

### CHAPTER 20 개인 정보, 보안, 배포

    20.1 개인 정보

    20.2 보안

    20.3 배포

    20.4 마치며

 

### CHAPTER 21 파도를 따라잡기 위하여

    21.1 TinyML 재단

    21.2 SIG Micro

    21.3 텐서플로 웹사이트

    21.4 그 밖의 프레임워크

    21.5 트위터

    21.6 TinyML의 친구들

    21.7 마치며

