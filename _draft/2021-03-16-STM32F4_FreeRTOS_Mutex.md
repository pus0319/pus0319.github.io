---
date: 2021-03-16
title: "STM32F407_FreeRTOS_6_뮤텍스(Mutex)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_6_뮤텍스(Mutex)
=============

세마포어(Semaphore)에 이어 뮤텍스(Mutex)에 대해 정리하였습니다.    

뮤텍스를 정리하면서 서로 다른 Priority의 Multitasking 시, 일어날 수 있는    

우선순위 역전(Priority Inversion)과 이를 해결하기 위한 우선순위 상속(Priority Inheritance)에 대해    

정리하였습니다.

# 1. 뮤텍스(Mutex)
## 1.1 뮤텍스(Mutex) 란?
* Binary Semaphore 중 하나
* 기본적인 동작은 Binary Semaphore와 같으나 **우선순위 상속 메커니즘** 이 포함됨.
    * Priority가 더 높은 다른 Task가 동일한 Mutex에 대해 'take' 시도 시,    
      Mutex를 이미 가지고 있는 Task가 더 높은 Priority를 가지게 됨.
    * Mutex를 'give'시, Task는 상속해제(원래 Priority로 돌아감)
    * 상호 배제에 사용되는 Mutex를 얻은 Task는 항상 마지막에 Mutex를 'give' 해야함.

# 2. 뮤텍스(Mutex) API 정리

![image](https://user-images.githubusercontent.com/79636864/111270958-e1a54d80-8673-11eb-91a2-5e05bd7ef74a.png)

# 3. 뮤텍스(Mutex)를 활용한 예제
## 3.1 
