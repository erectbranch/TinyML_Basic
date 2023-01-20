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


> header file(.h)은 code가 program의 다른 부분에 연결될 수 있는 인터페이스를 정의한다. 여기에는 변수, 클래스 선언 같은 것이 있지만 논리는 거의 없다.

> source file(.cc)은 계산을 수행하고 작업을 수행하는 실제 논리를 구현한다. 예를 들어 micro_interpreter.h의 논리 부분은 micro_interpreter.cc에 포함되어 있다.

> 하지만 compile 

```cpp
#include "tensorflow/lite/micro/examples/hello_world/sine_model_data.h"
#include "tensorflow/lite/micro/kernels/all_ops_resolver.h"
#include "tensorflow/lite/micro/micro_error_reporter.h"
#include "tensorflow/lite/micdor/micro_interpreter.h"
#include "tensorflow/lite/micro/testing/micro_test.h"
#include "tensorflow/lite/schema/schema_generated.h"
#include "tensorflow/lite/version.h"
```

