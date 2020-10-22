# new 와 delete

```cpp
int* p = new int; //p에게 int공간 메모리를 할당해주고, 그걸 가리키게 한다.
  *p = 10;//p가 가리키는 int공간의 값이 10이 된다.
```

여기서 보면 new의 반환은 포인터라는 것을 알  수있다.

자바의 new역시 포인터(자바는 포인터란 개념은 없지만)이다.

그래서 자바에서 new로 생성한 객체들은 마치 포인터처럼, 함수에다가 넣어주면 값의 복사가 아니라 주소의 복사가 일어났다.

그리고 그걸 레퍼런스타입이라고 불렀다.

cpp의 레퍼런스와는 다르다.cpp의 레퍼런스는 그냥 별명이다.



여기서 자바와 cpp에서 변수 a를 바꾸는 방법을 보자.

```cpp
//값에 의한 복사
int main(){
    int a= 1;
    inc(a);
    printf(a);
}
int inc(int argu){
    argu = argu+1;
    return argu
}
이 함수는 동작하지 않는다는 것을 알 것이다.argu에는 a의 값 복사가 일어나고, 그 안에서 뭘하든 argu와 관련이 있다.a자체에는 손댈 수 없다.
    int b = inc(a) 하면 b=2는 되겠지만 a=2와는 분명히 다르다.
```

이것은 포인터로 구현가능하다

```cpp
//포인터
int main(){
    int a= 1;
    inc(&a);
    printf(a);
}
int inc(int* argu){
    *argu = *argu+1;
    return 0;
}
a의 주소값을 argu포인터에 넘겨주었고, 거기서 메모리의 값을 바꿔버렸다.이것은 명백히 a가 2가 되어있다.
```

레퍼런스를 쓰면 더 간단해진다

```cpp
//레퍼런스
int main(){
    int a= 1;
    inc(a);
    printf(a);
}
int inc(int &argu){
    argu = argu+1;
    return argu
}
여기서 argu는 a의 또 다른 이름이다. &argu = a. 즉 a = a+1이 정확히 작동한다. 변수 a는 2가 되어있다.
```

자바에서는 이것이 불가능하다(내가 배운범위에서는).

```java
//값에 의한 복사
public static void main(String[] args){
    int a= 1;
    inc(a);
    System.out.println(a);
}
public static int inc(int argu){
    argu = argu+1;
    return argu
}
이것은 값 복사와 정확히 같은 코드이다.
```



```java
//new 를 사용하면 pointer긴 하다.
public static void main(String[] args){
    Integer a= new Integer(1);
    inc(a);
    System.out.println(a);
}
public static Integer inc(Integer argu){
    argu = argu.add(1);
    return argu
}
이것은 cpp의 레퍼런스 코드보다는 포인터 코드와 비슷한 것이다.다만 이것이 실패하는 것은 좀 납득하기 어렵다. 이것은 Integer가 자바가 정의한 좀 특별한 wrapper 클래스라 예상과 다른 방식으로 작동하는 것이다.여기서 a는 평범한 객체라고 하기 좀 그렇긴하다.그러나 분명 레퍼런스는 맞다..
    
    이것은 myInteger클래스를 만들어서 쓰면 원하는대로 a를 바꿀 수 있긴 할 것이다.
```



```java
public static class myInteger{
    int a;
    myInteger(int a){
        this.a = a;
    }
    int add(int b){
        a+=b;
    }
}
public static void main(String[] args){
    Integer a= new myInteger(1);
    inc(a);
    System.out.println(a);
}
public static myInteger inc(myInteger argu){
    argu = argu.add(1);
    return argu;
}
이것은 cpp의 포인터 코드와 비슷하게 작동할 것이다.
```



자바는 garbage collector가 메모리들을 자동해제하지만

cpp는 delete로 해제해줘야 한다.

new 로 배열을 생성해줬다면 delete도 배열을 지워야 한다.

```cpp
int *list = new int[arr_size];
```

```cpp
delete[] list;
```