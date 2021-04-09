---
date: 2021-04-09
title: "Constructor(생성자)뒤에 콜론(:)을 쓰는 이유"
categories: C++
tags: jekyll
toc: true  
toc_sticky: true 
---

Constructor(생성자)뒤에 콜론(:)을 쓰는 이유
=============
Constructor(생성자)뒤에 콜론(:)을 쓰는 이유를 간단하게 정리하였습니다.    

아래의 링크를 참고하여 작성하였습니다.    

[Constructor(생성자)뒤에 콜론(:)을 쓰는 이유](https://hashcode.co.kr/questions/629/%EC%83%9D%EC%84%B1%EC%9E%90%EC%97%90%EC%84%9C-%EC%BD%9C%EB%A1%A0%EC%9D%80-%EC%99%9C-%EC%93%B0%EB%8A%94-%EA%B1%B4%EA%B0%80%EC%9A%94)    

#0. 메소드에서 콜론(:)을 쓰는 기본 예시    

~~~c++
#include <iostream>

class Foo {
public:
  int bar;
  Foo(int num): bar(num) {};  // 1.
};

int main(void) {
  std::cout << Foo(42).bar << std::endl;
  return 0;
}
~~~    

1. 의 ```: bar(num)``` 은 초기화 리스트(Initialize list)라 함.
2. 멤버 변수 ```bar``` 를 메소드 ```Foo``` 의 인 ```num``` 로 초기화하는 역할.
3. 생성자도 마찬가지.

#1. 생성자에서 콜론(:)을 쓰는 기본 예시    

~~~c++
class MyClass
{
    public:
        int &i; //레퍼런스 멤버. 초기화 리스트를 써야 함
        int b;
        //Non static const 멤버. 초기화 리스트를 써야 함
        const int k;  

    //생성자 파라미터의 이름이 데이터 멤버랑 같음. 초기화 리스트를 쓸수 있음(선택 가능)
    MyClass(int a, int b, int c):i(a),b(b),k(c)
    {
        /*
        초기화 리스트를 쓰고 싶지 않은 경우
        this->a = a
        같이 써야 함
        */
    }
};

class MyClass2:public MyClass
{
    public:
        int p;
        int q;
        //base class인 MyClass가 default생성자가 없기 때문에 무조건 초기화 리스트에서 초기화해줘야 함
        MyClass2(int x,int y,int z,int l,int m):MyClass(x,y,z),p(l),q(m)
        {
        }

};

int main()
{
    int x = 10;
    int y = 20;
    int z = 30;
    MyClass obj(x,y,z);

    int l = 40;
    int m = 50;
    MyClass2 obj2(x,y,z,l,m);

    return 0;
}
~~~    

* 생성자에서 초기화 리스트를 써야만 하는 상황
    1. 클래스가 레퍼런스를 멤버로 가질 때
    2. ```non static const``` 멤버가 있을 때
    3. default 생성자가 없을 때
    4. ```base class(부모 클래스)``` 를 초기화할 때
    5. 생성자의 parameter의 이름이 멤버랑 같을 때
        * 이때는 this를 써서 해결할 수도 있음.    

* 생성자의 함수{}내에서 초기화하는 것과 초기화 리스트를 쓰는 것의 차이
    * 초기화 리스트에서 초기화를 하는 경우, 생성자가 호출될 때 객체의 생성과 초기화가 한 번에 이루어짐.
    * 생성자 함수 내{}에서 초기화를 하는 경우, 객체가 생성되어, default생성자로 초기화된 상태에서    
      다시 한 번 할당받게 하게 됨. 이 경우엔 default할당-유저할당의 2단계를 거치게 되서 오버헤드가 생김.




