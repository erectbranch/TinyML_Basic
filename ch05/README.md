# 5 TinyML "Hello World" 애플리케이션 구축

5장에서는 sine model을 활용하여 작은 라이트 쇼를 만드는 embedded application을 구축할 것이다. x값을 모델에 공급, inference, 결과를 사용하여 LED를 점멸하고 장치에 LCD 디스플레이가 있는 경우, 애니메이션을 제어하는 연속 루프를 설정한다.

다음은 기본적인 TinyML application architecture를 나타낸 그림이다.

![TinyML application](images/tinyML_application_architecture.png)

---

## 5.1 테스트 작성

application code 전에 test를 작성하는 편이 좋다, test는 특정 논리를 보여주는 짧은 code 조각으로, 이를 이용해 예상대로 작동하는지 확인하면서 검증해 나가면 편리하다.

---

### 5.1.1 종속성 불러오기

`#include`를 이용해서 header나 source file을 불러온다. 일반적으로 #include 지시문과 함께 사용하도록 설계된 C++ code는 source file이라는 .cc 파일과 header 파일이라는 .h 파일 두 개로 이루어진다.

> header file(.h)은 code가 program의 다른 부분에 연결될 수 있는 인터페이스를 정의한다. 여기에는 variable, class declaration 같은 것이 있지만 논리는 거의 없다.

> source file(.cc)은 계산과 작업을 수행하는 실제 논리를 구현한다. 예를 들어 micro_interpreter.h의 논리 부분은 micro_interpreter.cc에 포함되어 있다.

```cpp
#include "tensorflow/lite/micro/examples/hello_world/sine_model_data.h"
#include "tensorflow/lite/micro/kernels/all_ops_resolver.h"
#include "tensorflow/lite/micro/micro_error_reporter.h"
#include "tensorflow/lite/micdor/micro_interpreter.h"
#include "tensorflow/lite/micro/testing/micro_test.h"
#include "tensorflow/lite/schema/schema_generated.h"
#include "tensorflow/lite/version.h"
```

이 코드에서 `#include`를 사용하여 가져오는 항목은 다음과 같다.

- tensorflow/lite/micro/examples/hello_world/sine_model_data.h: xxd를 사용하여 훈련, 변환하고 C++ 코드로 바꾼 sine model

- tensorflow/lite/micro/kernels/all_ops_resolver.h: interpreter가 model에서 사용하는 Op를 load할 수 있게 하는 class.

  > 여기서 'Op'(Operation)은 일반적인 programming에서의 Op나 DevOps 등에서 자주 나오는 Ops와는 다른 의미다. TensorFlow Operation의 약자로, tensorflow에서 계산을 수행하는 Core Node(핵심 노드)를 의미한다.

- tensorflow/lite/micro/micro_error_reporter.h: debugging을 위해 오류와 출력을 기록하는 class

- tensorflow/lite/micro/micro_interpreter.h: model을 실행할 microcontroller용 TFLite

- tensorflow/lite/micro/testing/micro_test.h: test 작성을 위한 간단한 framework. 이 파일을 실행하면 test가 이루어진다.

- tendorflow/lite/schema/schema_generated.h: sine_model_data.h의 model data를 이해하는 데 사용되는 TFlite flat buffer의 data structure를 정의하는 schema

- tensorflow/lite/version.h: schema의 현재 version 번호. model이 호환 가능한 version으로 정의되어 있는지 확인할 수 있다.

---

### 5.1.2 test 설정

다음은 test framework에서 사용되는 부분이다.

```cpp
TF_LITE_MICRO_TESTS_BEGIN

TF_LITE_MICRO_TEST(LoadModelAndPerformInference) {
```

C++에서는 **macro** 기능을 이용해 code piece에 이름을 붙이고 다른 곳에서 재사용할 수 있다. 예제 code의 `TF_LITE_MICRO_TESTS_BEGIN`과 `TF_LITE_MICRO_TEST` 두 문장은 모두 macro의 이름이다. 이들은 `micro_test.h` 파일에 정의되어 있다. 이 macro는 나머지 code를 필요한 장치로 감싸서 MCU용 TFLite test framework에서 실행한다. 

> `TF_LITE_MICRO_TEST`라는 두 번째 macro는 argument를 허용한다. 앞서 예시에서는 LoadModelAndPerformInference를 전달했는데, 이는 test 이름이며 test가 실행될 때 결과와 함께 출력된다.

---

### 5.1.3 data 기록 준비

```
// 로깅 설정
tflite::MicroErrorReporter micro_error_reporter;
tflite::ErrorReporter* error_reporter = &micro_error_reporter;
```

> 위처럼 tflite:: 접두어가 붙은 data type을 볼 수 있다. 이는 C++에서 사용하는 namespace로, C++에서는 function, variable, structure(구조체)가 속한 소속에 이름을 붙여줄 수 있다. 예를 들어 namespace가 있다면 다른 프로그래머가 짠 동일한 이름의 function도 구별이 가능할 것이다.

> 마찬가지로 다른 library가 동일한 이름의 function을 제공한다고 해도, TFLite가 제공하는 class와 충돌하지 않는다.

- `MicroErrorReporter`: 'micro_error_reporter.h'에 정의되어 있으며, inference 과정 중에 debug 정보를 기록하는 매커니즘을 제공한다.

  - debug 정보를 출력하기 위해 이 class를 호출하면, MCU용 TFLite interpreter가 debug 정보를 이용해 error를 출력해 준다.

- `ErrorReporter* error_reporter`: 앞서 declaration한 micro_error_reporter의 class에 해당되는 `MicroErrorReporter`는 사실 `ErrorReporter`의 subclass이다. 재정의되지 않은 `ErrorReport` method에 계속 접근하기 위해서는, `MicroErrorReport` instance가 실제 ErrorReporter인 것처럼 처리해야 한다.

  - 즉, `ErrorReporter` pointer를 생성한 뒤 'micro_error_reporter' variable을 가리키면 된다. 할당 code에서 & 기호를 붙인 것은 값이 아니라 pointer를 할당함을 의미한다.

---