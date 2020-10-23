# 생성자

```cpp
Photon_Cannon::Photon_Cannon(int x, int y) {
  std::cout << "생성자 호출 !" << std::endl;
  hp = shield = 100;
  coord_x = x;
  coord_y = y;
  damage = 20;
}
```

기본적인 생성자의 모습이다. 클래스이름(인자) {초기화 내용}

## 복사생성자

``` Photon_Cannon(const Photon_Cannon& pc);```

복사생성자의 모습이다.

인자를 레퍼런스로 받아서, 포인터처럼*을 해줄 필요없이 자연스럽게 사용가능하고, const를 붙여서 혹시모를 수정을 방지하고 있다.

```cpp
Photon_Cannon pc3 = pc2; //이것은 복사연산으로 바뀐다.
Photon_Cannon pc3(pc2); //이것과 정확히 같다.
//복사연산은, 생성자 라는 것을 기억하자. 생성될 때 사용되는 것이 복사.
```

```cpp
pc3 = pc2; //이것은 대입연산이다.평범하다.
```



복사생성자는 디펄트 복사생성자가 있다. 매우 원시적인 모습이다.

```cpp
Photon_Cannon::Photon_Cannon(const Photon_Cannon& pc) {
  hp = pc.hp;
  shield = pc.shield;
  coord_x = pc.coord_x;
  coord_y = pc.coord_y;
  damage = pc.damage;
  name = pc.name;
}
// 단순히 모든 필드를 대입연산 해준다.
// 이 때,얕은복사(주소복사)가 일어나기 때문에, 같은 변수를 두 객체가 공유할 수 있다. 그러면 문제가 발생한다.
```

```cpp
Photon_Cannon::Photon_Cannon(const Photon_Cannon &pc) {
  std::cout << "복사 생성자 호출! " << std::endl;
  hp = pc.hp;
  shield = pc.shield;
  coord_x = pc.coord_x;
  coord_y = pc.coord_y;
  damage = pc.damage;

  name = new char[strlen(pc.name) + 1]; //따로 할당을 해줘서 깊은복사를 해서 문제를 해결하였다.
  strcpy(name, pc.name);
}
```

## 초기화리스트

```cpp
Marine::Marine() : hp(50), coord_x(0), coord_y(0), damage(5), is_dead(false) {}//새로운 모양의 생성자.
```

```cpp
(생성자 이름) : var1(arg1), var2(arg2) {} ':'뒤에 오는 것들을 초기화리스트라고 부른다.
```

기본적으로 {중괄호안에서 초기화}와 다른점은, 중괄호의 경우 먼저 만듬->초기화 를 실행한다면, 초기화 리스트는 만들면서 동시에 초기화를 한다.

```cpp
int a = 10; //초기화 리스트의 방식
```

```cpp
int a; // 디폴트 생성자가 수행된다(int의 경우에는 잘 모르겠다..아마 0 이거나 이상한 쓰레기 값이겠지.)
a = 10; //기존 생성자의 초기화 방식.
```

이것은 특히 const 값들의 경우 기존생성자의 방식으로는 초기화를 할 수없기 떄문에 유용하다. 레퍼런스역시.

```cpp
const int a;
a= 10; //이런 방식은 안되잔아!
int& ref;  
ref = c;   //이런 방식은 안되잔아!
```

## static

스태틱 키워드를 붙여주면 그것은 스택(함수)이 아니라 static공간에 들어간다. 따라서 함수 종류후에도 유지된다.

클래스 내부에서 선언하면서 바로 직접 스태틱변수를 초기화 시킬 수는 없다(사실 자바에서는 된다.너무 신경쓰지 말고 그냥 cpp를 만들다보니 이렇게 된거라고 보면 될듯).

스태틱은 객체가 부를 수가 없다. 어느 객체의 소유도 아니기 때문.

(클래스)::(static 함수) 형식으로 콜한다.

## this

this는 자기자신을 가리키는 '포인터'이다.

사실 모든 멤버함수나 변수를 쓸 때에는 this가 들어있다고 생각하면된다.

```
ex)hp = _hp 는 사실 this->hp=_hp이다.
```

따라서 Marine에서 this를 리턴한다면, 리턴형은 Marine*(포인터)가 되어야 한다.

```
return this; //this는 포인터타입.
```

Marine을 리턴하고 싶다면, *this를 리턴해야 한다.

```
return *this //이것은 Marine타입.
```

다만 Marine을 리턴한다면, 이것은 Marine의 '값' 리턴이다.

따라서 우리가 생각하는 this Marine 객체를 리턴한게 아니다.

Marine&리턴타입으로 함수를 선언해주어야 레퍼런스로 리턴할 수 있다.

그것을 Marine으로 받으면 

myNewMarine = Marin (값에의한복사/복사생성자호출)이 될 것이고(따라서 myNewMarine은 Marin과 다른 객체이다)

Marine&으로 받아야 우리가 생각하는 제대로 된 myNewMarine = Marin(별명) 이 된다 (따라서 myNewMarine은 Marin과 같은 객체이다).

사실 cpp의 레퍼런스가 자바에서는 없었다보니 조금 헷갈리긴 한다.

## 임시객체

레퍼런스가 아니라 값을 return 하는 경우, 임시객체를 리턴한다.

x = 1

return x 를 (return type = int)하면,

x를 고대로 넘겨주는 것이 아니라(그렇게 하려면 레퍼런스를 넘겨줘야하고, 포인터를 도입해도 된다.) x의 값과 똑같은 임시객체 temp를 만든 후 temp의 값을 받는 리턴하고 곧바로 temp가 삭제된다고 보면 된다.



