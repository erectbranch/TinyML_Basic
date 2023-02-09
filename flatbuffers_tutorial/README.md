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

namespace MyGame.sample;

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

```bash
flatc [ GENERATOR OPTIONS ] [ -o PATH ] [ -I PATH ] [ -S ] FILES...
      [ -- FILES...]
```

---