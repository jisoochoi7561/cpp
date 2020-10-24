# Dynamic dispatch

dynamic dispatch는 왜 필요한가?

가장 필수적인 예시는 바로 소멸자이다.

바로 전 렉처에서, dynamic cast 에는 virtual 소멸자가 필요함을 강조했었다.



virtual은 dynamic dispatch를 하기위한 키워드이다. 그럼 버추얼 생성자가 아니라면 어떻게 되는가?

*Car car = *bus("hihi i'm newbus")라고 해보자.

이 car가 소멸될 때, virtual이 없다면, static dispatch가 될 것이고, 즉 car의 소멸자를 call할 것이다.

그렇다면 bus가 소멸될 때 '노선'를 지웠어야 되는데, car의 소멸자가 호출되므로 노선이 memory leak을 유발할 것이다.

virtual을 붙여주면, 런타임에 dynamic dispatch를 하게 되고, car가 사실 Bus타입이어서 Bus의 소멸자를 호출해야된다는 것을 알 게 되는 것이다.

이를 구현하는 방식에 vtable 등의 개념이 들어간다.



# vtable and vptr

vtable은 virtual이 들어간 각 클래스마다 생성된다.

컴파일러가 생성해준다.

그리고 각 객체에는 vptr이라는 것을 컴파일러가 생성해서 넣어준다.

이 vptr은 vtable을 가리키고 있다.

vtable은 static array로, 클래스의 버츄얼 메소드들을 가리키는 포인터를 갖고 있다.

메소드를 콜하면, 객체의 vptr이 그것의 vtable을 참조하고,거기서 알맞는 메소드를 가져와서 실행한다.

이런 방식으로 dynamic dispatch를 구현한다.



물론 다른방식으로 구현할 수도 있다.

예를 들면, dynamic cast와 if문을 열심히 쓰며는 비슷하게 구현은 가능하다.

하지만 virtual을 쓰는 것이 훨씬 낫겟지.



bass에 virtual을 붙여주면 그걸 상속받는 애들도 암묵적으로 virtual이 붙어있다.



다이내믹 디스패치는, 리시버(A.run()에서 A)가 포인터/레퍼런스 일 때 생각대로 작동한다.

무슨말이냐면

Car* car = new Bus()하면

car은 Car포인터이지만 따라가보면 Bus이므로 다이내믹디스패치가 잘 작동한다. car->run() // bus is running //if run is virtual

Car& car = Bus역시 마찬가지이다. //car.run() //bus runs. //if run is virtual

하지만 

Car car = Bus는 값이므로 추정해도 Car이다.따라서 안된다. //car.run() car is absolutely car with bus values.so car runs.



## override

오버라이드 한 함수에는 맨뒤에 오버라이드를 붙여준다.

이것은 사실 아무런 의미가 없다. (자바에서는 아예 어노테이션이 되어버린다)

다만 컴파일러가 체크해주는것이 가능해진다.

그러므로 쓰자.



## pure virtual

어떤 bassclass들은 그저 하위클래스들의 상위개념일 뿐이고, 구체적인 함수내용등은 필요하지 않을 수 있다.

그럴 경우 virtual int methodname(int arg) = 0형식으로 pure virtual function을 구현할 수 있다.

이런 pure virtual function을 가진 클래스를 abstract class 라고 부른다.



## Polymorphic class

virtual과 관련된 클래스들을 Polymorphic class라고 부른다.

bass,sub 모두 그렇다.

다시말하지만 Polymorphic 클래스의 경우 base 클래스의 소멸자는 반드시 virtual이어야 한다.아니면 메모리 leak이 발생할 것이다.

물론 ㄹㅇ 쏙빼닮은 subclass라면 발생안할 수도 있긴함..그래도 붙이자.







