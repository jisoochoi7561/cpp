

# istream and ostream

![](https://modoocode.com/img/2361DC4954A0CB38040ED8.png)

cpp의 입출력 라이브러리 이다.



가장 기본이 되는 class 는 ios_base클래스이다.

이 클래스는 스트림의 출력과 입력 형식을 담당한다(16진수로 출력할지, 10진수로 입력받을지 등).



ios클래스는 실제로 스트림의 상태를 관리한다.



이 클래스들은 기본적인 기능들을 깔아준다.



# istream

istream은 실제로 우리가 입력을 받을 때 사용하는 클래스이다.(버퍼로 우선 읽고 버퍼의 것들을 입력한다.)

예를 들면 std::cin>>a 가 istream에서 정의된 operator>>를 사용하고 있다.

당연히 operator는 여러가지(기본 자료형)타입에 대해 overload 되어 있다.

아마 새로운 객체를 넣으면 기본적으로는 변환할 수 있는 범위로 변환해서 출력할 것이다.

물론 정확히 할려면  바깥에서 오버로딩을 해주면 된다.

```cpp
istream& operator>>(istream& in, std::string& s)

{
  // 구현한다.
}
```

\>>오퍼레이터는 공백을 만나면 끊어진다. java에서의 scanner.next나 c의 printf와 비슷한 매커니즘인듯.

\>>가 정확히 어떻게 구현되는지 알아보자.

우선은 기본적인 바탕이 된다고 했던 ios 클래스에는 4개의 플래그가 정의 되어 있다. 이렇게 플래그를 이용하는 방식은 여러 경우 쓰이니 기억해두자.

- `goodbit` : 스트림에 입출력 작업이 가능할 때

- `badbit` : 스트림에 복구 불가능한 오류 발생시

- `failbit` : 스트림에 복구 가능한 오류 발생시

- `eofbit` : 입력 작업시에 `EOF` 도달시

타입이 맞지않는 거를 입력받는 경우 ( 예를들어 int a 에 문자를 입력) failbit가 on 되고

동작을 하지않고 (a에 문자를 넣지 않음.난리나잖아) 리턴한다.

이 때 버퍼에는 입력받은 문자가 남아있다.

그리고 >>는 자기자신(그러니까 istream)을 리턴하는데, operator void*() const 함수가 ios 클래스에 정의 되어있어서 while 문의 조건식에 들어가면 보이드 포인터가 나오는데,만약 실패한 경우라면 (failbit나 badbit가 on)  nullptr을 리턴하고,이건 bool값으로 0이라서 응용하면 입력이 성공햇는지 알 수 있다.

```cpp
#include <iostream>
#include <string>

int main() {
  int t;
  while (std::cin >> t) //이것은 입력실패하면 nullptr 즉 false를 리턴함 {
    std::cout << "입력 :: " << t << std::endl;
    if (t == 0) break;
  }
}
```

참고로 실패한경우라면 failbit가 on되어서 istream을 더이상 사용할 수가 없다.

```cpp
#include <iostream>
#include <string>

int main() {
  int t;
  while (true) {
    std::cin >> t;
    std::cout << "입력 :: " << t << std::endl;
    if (std::cin.fail()) {
      std::cout << "제대로 입력해주세요" << std::endl;
      std::cin.clear();            // 플래그들을 초기화 하고
      std::cin.ignore(100, '\n');  // 개행문자가 나올 때 까지 무시한다 //100은 100자까지란 뜻이고 두번째거는 그게 나올떄까지 무시한단거다. 즉 이 코드는 \n이 나올때까지 또는 100자를 채울때까지 버퍼에서 무시한다.
    }
    if (t == 0) break;
  }
}
```

이렇게 하면 istream다시 사용 가능.

## 형식 플래그(format flag) 와 조작자 (Manipulator)

```cpp
std::cin.setf(std::ios_base::hex, std::ios_base::basefield);
```

ios_base 클래스에 정의된 setf함수를 사용해서 입력을 16진수로 받는 코드이다.

이것의 hex는 hex를 형식플래그에 넣으라고 말하는 거고

basefield는 수의 처리방식을 보관하는 basefield를 초기화하라는 것이다(그럼 플래그가 hex니 hex로 되겠네)

별로 중요하진 않은듯.원리를 이해하는 정도로만.

```cpp
std::cin >> std::hex >> t;
```

형식으로 16진수로 입력을 받을 수도 있다.

여기의 hex는 함수이다.위에서 플래그의 값으로 쓰인 hex와는 완전 다르다.

이렇게 스트림을 조작하는 함수를 조작자라고 한다.

```cpp
std::ios_base& hex(std::ios_base& str); //hex함수가 정의되어 있다.
istream& operator>>(ios_base& (*pf)(ios_base&));//hex함수를 받는 >>도 정의되어 있다.

std::cin >> std::hex 에서 일어나는일:
istream& operator>>(ios_base& (*pf)(ios_base&))가 실행된다.
    그로인해 pf(이경우에는 hex를) 자기스트림의 ios_Base에다가 실행시키는데
    그로 인해 자기스트림의 ios_base가 위처럼 setf로 hex로 set된다.
즉 조작자는 사실 형식플래그를 다루고 + 뭐 초기화정도 를 간편하게 해주는 것이다.이경우에 그렇단 것이고, 여러가지 방식으로 입력/출력을 바꿀 수 있겠다.
```

예를 들면 std::endl도 조작자 이다.이것은 ostream에 정의된 조작자이긴 해도. 개행문자하나를 출력하고,버퍼를 flush한다.

