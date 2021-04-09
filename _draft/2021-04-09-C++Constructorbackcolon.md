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
2. 멤버 변수 **bar**를 
