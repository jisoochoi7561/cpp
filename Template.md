# Template

cpp의 템플릿개념을 공부한다.

자바에서는..제네릭?같은 느낌인가? 아직 잘은 모르겠지만 stack과 vector를 구현할 때 쓴다는걸 보니 비슷할거같기도 하고.

vector는 가변길이 배열이라고 생각하면 편하다고 한다.

사실 java에서도 vector라는 단어를 몇번 들어본 적은 있다. ararylist같은거의 superclass이었던가?레거시였던가? 어쨌든 좀 로우한 레벨에 있었던 기분.

모두코드에서 구현해준 벡터코드를 보면서 얘기해보자

```cpp
class Vector { //벡터클래스
  std::string* data; //벡터가 저장할 타입은 string포인터이다.이거 string배열인듯.data[0]이런식으로 0번째 원소 뽑아낼듯?
  int capacity;
  int length;//capacity와 length가 다르다.아마 capacity는 벡터의 길이(빈공간 포함)이고 length는 빈공간제외겠지.

 public:
  // 생성자
  Vector(int n = 1) : data(new std::string[n]), capacity(n), length(0) {} //n(디폴트는 1)길이의 배열을 만든다.

  // 맨 뒤에 새로운 원소를 추가한다.
  void push_back(std::string s) {
    if (capacity <= length) {
      std::string* temp = new std::string[capacity * 2]; //2배씩 크기가 증가하는 배열.이거완전 arraylist인데
      for (int i = 0; i < length; i++) {
        temp[i] = data[i];
      }

      delete[] data;
      data = temp;
      capacity *= 2;
    }

    data[length] = s;
    length++;
  }

  // 임의의 위치의 원소에 접근한다.
  std::string operator[](int i) { return data[i]; }

  // x 번째 위치한 원소를 제거한다.
  void remove(int x) {
    for (int i = x + 1; i < length; i++) {
      data[i - 1] = data[i];
    }
    length--;
  }

  // 현재 벡터의 크기를 구한다.
  int size() { return length; }

  ~Vector() {
    if (data) {
      delete[] data;
    }
  }
};
```

오 이거 ㄹㅇ 그냥 arraylist인데



이 vector는 현재 string밖에 저장하지 못한다.

만약 int vector를 만들려면 string을 int로 바꿔주면 되긴한데

그러면 매번 vector를 새로 만들수는 없다

이거 완전 제네릭이네.

사실 제네릭과 템플릿은 개념적으로는 동일하다(컴파일러가 컴파일타임에 타입을 써줌).

다만 언어가 다르니 구조가 다르고 그런것은 있다.

```cpp
template <typename T>
class Vector {
  T* data;
  int capacity;
  // ...
```

이렇게 써주면 된다. T가 어떠한 typename인 템플릿인자이다. 그냥 깡으로 T를 쓰면 안되고, template <typename T>를 써주고 그 아래에 그거를 쓰는 클래스를 써주어야 한다.

``` cpp
template <class T> 는 template <typename T>와 정확히 동일하다.하지만 typename을 쓴다(클래스라고 하면 int같은건 안될거같자나).
```

```cpp
Vector<int> int_vec;
```

이런식으로 하면 T에 int가 전달된다.

이러면 컴파일러는 T를 int로 치환한 코드를 찍어낸다.

이처럼 T에 실제로 타입을 전달해줘서 일반적인 형태에서 개별적인 형태의 코드를 생성하는 것을 **클래스 템플릿 인스턴스화 (class template instantiation)** 라고 합니다.

인스턴스화 되지 않은 템플릿은 (이 경우 vector)는 코드가 생성되지 않는다.



## 템플릿 특수화

템플릿인자로 받은 타입이 특정타입일 경우 다르게 동작하고 싶다면 템플릿 특수화를 사용한다.

```
template <typename A, typename B, typename C>
class test {};
//일반적인 템플릿
```

```cpp
template <typename B>
class test<int, B, double> {};
//A가 int C가 double일 때의 특수한 경우의 템플릿
```

```cpp
template <>
class test<int, int, double> {};
//B까지 int일 경우 특수한경우의 구현
```