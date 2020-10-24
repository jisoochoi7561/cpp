## friend

```cpp
// B 는 A 의 친구!
friend class B;

// func 은 A 의 친구!
friend void func();
```

프렌드라고 클래스내부에 선언해주면, 프렌드들은 클래스의 private 필드에 접근가능하다.

단 B가 A의 친구라고 A가 B의 친구인 것은 아니다.



## Wrapper

기본 타입을 객체로 다루어야 할 때 wrapper 클래스를 만들어서 사용한다.

이 때 wrapper를 다시 기본타입으로 바꿔줘야 연산등에서 유용하다.

```
operator (변환 하고자 하는 타입) () //타입변환 연산자.
```

```cpp
operator int() { return data; } //Int to int
그 이후에는 디폴트 연산자들에게 잘해달라고 기도한다.(아마 잘해줄거다)
```



## 전위연산자와 후위연산자

```cpp
A& operator++() {
  // A ++ 을 수행한다.
  return *this;
    //즉 ++을 수행한 후 받은걸 그대로 리턴한다.
}
```

```cpp
A operator++(int) {
  A temp(A);
  // A++ 을 수행한다.
  return temp;
    //즉 ++을 수행한 후 A에 저장하고, 수행이전의 A값을 복사해서 저장해논 temp를 리턴한다.
    //따라서 후위연산자가 더 느리고 메모리를 잡아먹는다.	
}
```