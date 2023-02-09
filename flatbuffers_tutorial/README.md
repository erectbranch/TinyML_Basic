# IDL 개념 정리, FlatBuffers 튜토리얼

> [FlatBuffers란?](https://gompangs.tistory.com/57)

- FlatBuffers 'schema' file 작성하기

- 'flatc' FlatBuffers Compiler 사용하기

튜토리얼 예제에서는 게임 속 몬스터를 다룰 것이다.

---

## 1. IDL(Interface Definition Language)

우선 배경 지식으로 **IDL**(Interface Definition Language) 이해가 필요하다. IDL은 말 그대로 interface를 definition하는 언어이다. IDL은 어떤 언어로 작성된 program이나 객체가, 다른 모르는 언어로 작성된 program과 통신할 수 있도록 해주는 언어를 통칭한다.

> 주로 서로 다른 프로그래밍 언어로 구현된 client/server 사이에서 사용한다. client에서 server에 있는 객체의 operation을 원격으로 호출할 수 있도록 IDL을 통해 interface를 기술한다.

> distributed objects에서는 덩치가 큰 program(개체)를 server만 가지고 있고, client에게는 이 program(개체)를 조작하는 데 필요한 최소한의 코드만 전달한다. 그리고 server에서 program 실행을 담당하게 된다.

IDL에서의 "Interface"는 (객체지향) 프로그래밍 언어에서 사용하는 interface와 비슷한 개념이다. interface는 객체가 가져야 하는 method와 property를 정의하는 것으로, 구현된 객체는 이 interface를 구현해야 한다.

즉, interface를 구현한 객체는 정의된 method와 property를 구현해서 어떠한 상호 작용이 일어날 수 있도록 한다. IDL에서의 interface도 객체 간의 상호 작용을 정의하고, 구현된 객체가 상호 작용을 적절히 구현할 수 있도록 강제한다.

> IDL이 말 그대로 interface를 definition하므로, definition만을 기술하고 실제 구현부는 여기서 기술하지 않는다. 오직 interface만 기술한다.

> [distributed objects에서의 stub, skeleton](https://vascocenter.tistory.com/entry/%EB%B6%84%EC%82%B0%EA%B0%9C%EC%B2%B4-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%97%90%EC%84%9C%EC%9D%98-%EC%8A%A4%ED%85%81stub-%EC%8A%A4%EC%BC%88%EB%A0%88%ED%86%A4Skeleton)

---

## 2.1 FlatBuffers schema

FlatBuffers를 사용하기 위해서는 인터페이스를 정의하는 IDL인 'schema' file을 작성해야 한다. 이 format이 serialize할 각 data structure를 설정한다. 

```cpp
table Monster {
    pos:Vec3;
    mana:short = 150;
    hp:short = 100;
    name:string;
    friendly:bool = false (deprecated);
    inventory:[ubyte];
    color:Color = Blue;
    weapons:[Weapon];
    equipped:Equipment;
    path:[Vec3];
}
```

> 'schema' **IDL**(Interface Definition Language)는 C와 굉장히 비슷하다.

위는 게임 속 몬스터를 정의하는 templete이 담긴 'schema'이다. 위와 같이 정의하고 compile하면 C++, Java, C\# 등 다양한 언어에 맞춰서 class가 자동으로 생성된다. 

아래는 [FlatBuffers 튜토리얼](https://google.github.io/flatbuffers/flatbuffers_guide_tutorial.html)에서 소개하는 IDL 코드 예시 전문이다.

> scalar type `short`는 `int16`, `float`는 `float32`로도 작성이 가능하다.

```cpp
// Example IDL file 

namespace MyGame.Sample;

enum Color:byte { Red = 0, Green, Blue = 2 }

union Equipment { Weapon }

struct Vec3 {
    x:float;
    y:float;
    z:float;
}

table Monster {
    pos:Vec3;
    mana:short = 150;
    hp:short = 100;
    name:string;
    friendly:bool = false (deprecated);
    inventory:[ubyte];
    color:Color = Blue;
    weapons:[Weapon];
    equipped:Equipment;
    path:[Vec3];
}

table Weapon {
    name:string;
    damage:short;
}

root_type Monster;
```

- `namespace MyGame.Sample`

  > 'MyGame' namespace 안에 'Sample' namespace가 만들어진다.

- `enum Color:byte {...}`

  - byte type에 'Color'라는 이름을 갖는 enum이다. 안에 Red, Green, Blue 세 값을 갖는다.
  
  > 그런데 코드에서는 Red = 0, Blue = 2로 정의했지만, Green은 explicit value(명확한 값)가 없다. enum의 특성으로 Green은 implicit value(암묵적인 값)로 1을 갖게 된다.

- `union Equipment { Weapon }`

  - union Equipment는 'Weapon'이라는 table을 갖는다. 

  > 예제는 table 하나만 가지고 있어서 별로 유용하지는 않지만, 실제로는 더 많은 element들을 넣으면 더 유용하게 사용할 수 있다.

- `struct Vec3 {...}`

  - 3차원의 floating pointer를 갖는다.

  - 굳이 struct를 만들어서 Monster table에서 사용하는 이유는, 해당 data structure가 바뀌지 않는 상황이 제일 이상적이기 때문이다.(memory를 덜 사용하고, lookup이 빠르다.)

- `table Monster`

  - 이 FlatBuffer의 main object이다. 'orc' 몬스터를 저장할 templete로 사용될 것이다.

  - `mana:short = 150;`처럼 일부는 초깃값을 지정한다. 지정하지 않으면 scalar fields(int, uint, float 등)는 0, string 및 table들은 null로 초기화된다.

  - `friendly:bool = false (deprecated);`: 이전 버전과의 호환성을 지원하기 위해 삭제 대신 deprecated된 field이다. 코드가 생성되면 이 field에 접근하는 accessor(접근자)는 생성되지 않는다.

- `table Weapon`

  - subtable이다. `table Monster`와 `union Equipment`에서 한 번씩 쓰인다.

- `root_type Monster`

  - root_type으로 serialized data의 root table을 선언한다.

---

## 2.1.1 Tables, Structs

앞서 본 예시처럼 FlatBuffers에서는 객체를 정의하는 방법으로 **Table**을 사용한다. 예시에서는 Monster라는 이름과 필드들로 구성되었다. 각 필드에는 name, type, default value, attributes 등이 있다.

새 필드를 추가하기 위해서는 꼭 table definition의 마지막 부분에 추가해야 하므로 주의한다. 이때 이전 버전의 data도 여전히 제대로 읽을 수 있지만 새 필드는 무시된다. 더 이상 사용하지 않는 필드는 삭제를 할 수는 없지만 deprecated로 표시하여 쓰는 것을 중지시킨다. 

Struct는 Table과 비슷하나 default 값이 없다. 또한 필드를 추가하거나 deprecated시킬 수 없다. 또한 scalar 값이나 다른 struct만 포함할 수 있다. Table보다 memory를 덜 사용하고 access 속도가 훨씬 빠르다.(항상 inline으로 저장된다.)

---

## 2.2 Compiling

compile을 하기 위해서는 FlatBuffers compiler를 설치해야 한다.(flatc) 주의할 점은 이후 dependencies로 넣어주는 library file의 버전을 compile된 결과물의 버전과 일치시켜야 한다.

> 다양한 언어로 compile 결과물을 만들 수 있으며, 주로 `.fbs` 파일 형식으로 compile하나 `.txt` 같은 형식을 사용해도 무방하다.

flatc compiler를 이용한 compile은 다음과 같이 입력한다.

```bash
flatc [ GENERATOR OPTIONS ] [ -o PATH ] [ -I PATH ] [ -S ] FILES...
      [ -- FILES...]
```

예시 코드를 compile하기 위해서는 다음과 같이 입력한다.

```bash
cd flatbuffers/samples
./../flatc --cpp monster.fbs
```

---

## 2.3 Reading and Writing Monster FlatBuffers

위 과정을 거친 FlatBuffers binary를 읽어보자.

우선 'flatc'가 함께 만든 'monster_generated.h'를 include한다. 또한 schema에서 정의한 namespace를 사용하기 위해 'using namespace'를 작성한다.

```cpp
#include "monster_generated.h"

using namespace MyGame::Sample;
```

그 다음 `FlatBufferBuilder` 인스턴스를 만들어야 한다. 아래 코드에서 초깃값으로 1024 bytes를 전달하지만, 필요에 따라 자동적으로 늘어나게 된다. `FlatBufferBuilder` 인스턴스 builder를 이용해서 data를 serializing할 수 있다.

```cpp
flatbuffers::FlatBufferBuilder builder(1024);
```

serialize하기 앞서서 'Weapon' table에 해당되는 'Sword'와 'Axe'를 만들어 보자.

```cpp
// 앞서 튜토리얼 예제에서 작성한 Weapon table
// table Weapon {
//     name:string;
//    damage:short;
// }

auto weapon_one_name = builder.CreateString("Sword");
short weapon_one_damage = 3;

auto weapon_two_name = builder.CreateString("Axe");
short weapon_two_damage = 5;

// CreateWeapon 함수를 이용해서 Weapon table을 만들 수 있다.
// 이처럼 FlatBuffers는 table을 만들 수 있도록 shortcut을 제공한다.
auto sword = CreateWeapon(builder, weapon_one_name, weapon_one_damage);
auto axe = CreateWeapon(builder, weapon_two_name, weapon_two_damage);
```

이번에는 Monster Table의 'orc'를 만들어 보자.

```cpp
// "Orc"라는 Monster 이름을 serialize 
auto name = builder.CreateString("Orc");

// Orc의 인벤토리(획득 가능한 재화 목록)를 나타내는 'vector'를 만든다.
// 각 숫자는 Orc를 사냥하면 얻을 수 있는 item에 대응된다.
unsigned char treasure[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
auto inventory = builder.CreateVector(treasure, 10);
```

이 과정으로 built-in data type인 `string`과 `vector`를 serialize하고 return value를 capture할 수 있다. 이 value는 serialized data의 offset을 나타내며, 이를 이용하면 Monster에 필드를 추가할 때 참조하는 위치를 알 수 있다.

예를 들어 바로 전에 만든 'Weapon' table의 'Sword'와 'Axe'를 생각해 보자. 둘 다 FlatBuffers table로 memory에 offset들이 저장된다. 그러므로 이제 그들의 offset을 포함하는 FlatBuffers vector를 만들 수 있다.

```cpp
// weapon을 `std::vector`에 위치시킨다. 그리고 이들을 FlatBuffer `vector`로 변환한다.
std::vector<flatbuffers::Offset<Weapon>> weapons_vector;
weapons_vector.push_back(sword);
weapons_vector.push_back(axe);
auto weapons = builder.CreateVector(weapons_vector);
```

---

