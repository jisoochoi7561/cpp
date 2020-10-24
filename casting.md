## casting

캐스팅은 c에서는

```cpp
int_variable = (int)float_variable;
```

로 구현하였다.

하지만 이것은 c스타일이고(cpp에서도 동작은 하는듯) cpp에서는 다른 방식을 채택한다.

후술함.



## inheritance

class ClassName : AccessModifier? ClassName { … };



Access Modifier

 ○ public  //모든경우 접근가능

○ protected  //그 클래스/상속받은 클래스에서 접근가능

○ private(디펄트) //그클래스만 접근가능



상속할경우

1. base의 private은 상속되지 않는다.
2. public으로 상속받으면 public은 public으로 protected는 protected로 상속된다.
3. protected로 상속받으면 둘다 protected로 상속된다.
4. private으로 상속받으면 둘다 private으로 상속된다
5. 일반적으로는 public상속을 쓴다







subclass의 생성자 만드는 법 :

1.base class의 생성자를 쓴다

2.추가적인 초기화를 한다.



```
Bus(int i): Car(i),name("hi"){}
```



## cpp type casting

● static_cast ○ 다른타입으로 변환 ○ 컴파일 타임에 유효한지 확인함(디버그하기 좋다).일반적으로 생각하는 캐스팅.

 ● dynamic_cast ○다른타입으로 변환 ○ 런타임에 유효성확인

● reinterpret_cast ○ 다른타입으로 변환  ○ 유효성확인안함.그래도 가끔은 유용하게쓰기가능.

● const_cast ○ const를 없애줌(상수를 변수로 만들어서 수정하게 해줌)



subclass는 암시적으로 상위클래스로 변환할 수 있음. 이를 Upcasting이라고 함.스태틱캐스트임.

```
Car* car = new Bus(); bus*가 car*가 되었다.
```

일반적으로 upcasting은 안전하다.

그러나 cpp에서는 그렇지 않다.

cpp에서는  상속을 public으로 받아야만 upcasting이 typesafe하다.

왜냐하면 upcasting이 안전한 이유는 하위클래스는 상위클래스를 대체할 수 있기 때문인데

```
Bus는 Car가 할 수 있는 일을 모두 할수 있다. 달리기,주차,이름 등등
```

public이 아니라 다른걸로 상속받으면 예를들어 private으로 받으면 car의 run은 public이라 밖에서 달리게 할 수 있는데 bus는 밖에서 달리게 할 수 없는 상황이 발생한다.



## static dispatch

```
Car* car = new Bus(); 
car->run()
이 때 car은 car이므로 car클래스에 선언되어있는 run을 하게 된다.
즉 이 car은 bus가 할 수 있는 일은 하지 못한다(car니까).
```

이것을 static dispatch라고 한다.

즉 어떤 함수가 불러질지가 컴파일타임에 결정이 된다.

car는 컴파일타임에 Car이니까 당연히 Car의 run을 호출한다.



이에 반대되는 dynamic dispatch가 있다.

런타임에 부를 함수를 결정한다.

따라서 런타임에 실제변수의 타입을 체크한다.

그러므로 런타임 오버헤드가 발생한다(런타임에 연산량이 늘어난단 뜻)

그렇다면 car가 bus가 하는일,예를 들면 returnMoney()를 하려면 어떻게 해야할까



## downcasting

다운캐스팅은 업캐스팅과 달리 반드시 명시적으로 해줘야한다.

다운캐스팅은 기본적으로 위험하다.(car을 bus로 그냥 바꾸면 제대로 동작하지않을 가능성이 높다)

bus->car->bus로 바꾼다면 문제가없겟지만

bus->car->Taxi로 바꾸면 프로그램이 엉망이 될 가능성이 있다.

그러니 주의해서 써야한다.

```
(Type) Expression 형식으로 쓰면 된다.
 ((Bus*)car)->run()
```

이 문법 역시 c스타일이다. 후에 cpp스타일이 나온다.



## class  hierarchy 

클래스 계층구조는 컴파일타임에 컴파일러에 의해 생성된다.

스태틱 캐스트는 이 계층구조를 기반으로 에러를 판별한다.

```
static_cast<Type>(Expression)
```

클래스계층구조를 보고, 변환하고자 하는 타입이 연결되어 있는가를 확인한다.

클래스계층구조에는 방향성이 있어서taxi->car>bus는 연결되어있다고 보지않는다.



## RunTime Type Information (RTTI)

실제로 런타임에 무슨 타입인지를 확인하는 정보이다.

런타임에실행되므로 런타임오버헤드 발생

```
Car* car = new Bus(); 
```

이것은 RTTI에서 car = Bus타입 이라고 인식된다.



## dynamic_cast

```
dynamic_cast<Type>(Expression)
```

Expression의 RTTI를 확인한다.

그것이 Type이거나 subType이라면 제대로 cast해주고,아니라면 nullput을 리턴한다.

따라서 이것은 컴파일에러를 발생시키지 않는다(애초에 컴파일할 떄 연산도 안한다).



다이내믹캐스트를 쓰기위해서는 baseclass가 polymorphic type이어야 하고, 따라서  virtual keyword로 선언된 소멸자가 있어야 한다.

이는 중요하기 떄문에 후술한다.



## reinterpert cast

```
reinterpret_cast<Type>(Expression)
```

그냥 깡으로 캐스트한다.컴파일 에러같은거 없다. 실행될 때는 있겠지

## const_cast

```
const_cast<Type>(Expression)
```

상수를 변수로 만든다. 타입과 expression이 적절한 상수-변수 관계가 아니라면 컴파일에러.

하지만 이걸 쓸 일은 거의 없다. 상수를 바꾼다면 그걸 왜 상수로 만들어놨는데.