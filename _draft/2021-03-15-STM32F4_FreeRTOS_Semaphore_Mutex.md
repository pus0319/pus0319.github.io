---
date: 2021-03-15
title: "STM32F407_FreeRTOS_5_세마포어(Semaphore),뮤텍스(Mutex)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_5_세마포어(Semaphore),뮤텍스(Mutex)
=============

이전 포스팅은 Kernel System을 사용하는데 앞서 기본적으로 알아야하는 개념인    
상호배제(Mutual exclusion)에 대해 정리했습니다.

이번 포스팅은 상호배제에서 소개해드렸던 세마포어(Semaphore),뮤텍스(Mutex)에 대한 내용을 정리하고자합니다.

세마포어(Semaphore),뮤텍스(Mutex)는 상호배제외에도 쓰이는 다른 경우도 있으니 참고하면 될 것 같습니다.

# 1. 세마포어(Semaphore)
## 1.1 세마포어(Semaphore)란?
* 깃발 이란 뜻.
* 저장장치 내의 값을 통해 공유자원(Global Resource)에 대한 접근을 제어하는 방식.
* 세마포어 획득(Take,Wait,Pend)을 통해 공유자원에 대해 활용 권리 획득.
* 세마포어 반환(Give,Send,Post)을 통해 공유자원에 대해 활용 권리 반환.

![image](https://user-images.githubusercontent.com/79636864/111145471-9e8f9f80-85cb-11eb-8e2b-2827de2d4e39.png)

* 모든 Task는 Semaphore를 획득해야만 2.와 같이Global Resource에 접근 가능하다.
* 모든 Task는 Semaphore에 '열쇠'(Count)가 있을 경우, 1.과 같이 '열쇠'를 얻을 수 있다.(추상적인 표현임)

![image](https://user-images.githubusercontent.com/79636864/111145767-f75f3800-85cb-11eb-8ad1-b63df748a43b.png)

* Task3이 Semaphore의 '열쇠'를 얻은 상태.
* Task3만 2.와 같이Global Resource에 접근 가능하고 나머지 Task는 Task3이 Semaphore를 반환할때까지    
  설정 Delay Tick 만큼 Blocked state로 Wait하고 있다.
* 만약, 설정 Delay Tick만큼 경과해도 Task3이 Semaphore를 반환하지 않을 경우,    
  Wait하는 동작을 하지 않게된다.

## 1.2 세마포어(Semaphore) 분류
* 세마포어 반환 및 획득 할 수 있는 Task수에 따라
    * 1회 : Binary Semaphore
    * 1회 초과 : Counting Semaphore

# 2 세마포어(Semaphore) API 정리
## 2.1 vSemaphoreCreateBinary()
![image](https://user-images.githubusercontent.com/79636864/111146817-36da5400-85cd-11eb-8674-d9db1f94c48e.png)

## 2.2 xSemaphoreCreateCounting()
![image](https://user-images.githubusercontent.com/79636864/111146871-45287000-85cd-11eb-9031-39eaf0eac903.png)

## 2.3 xSemaphoreTake()
![image](https://user-images.githubusercontent.com/79636864/111146936-570a1300-85cd-11eb-8c05-aa22f8dda074.png)

## 2.4 xSemaphoreTakeFormISR()
![image](https://user-images.githubusercontent.com/79636864/111146971-5f624e00-85cd-11eb-919c-616c4a6a775b.png)

## 2.5 xSemaphoreGive()
![image](https://user-images.githubusercontent.com/79636864/111147012-67ba8900-85cd-11eb-813b-923e7d4d7870.png)

## 2.6 xSemaphoreGiveFormISR()
![image](https://user-images.githubusercontent.com/79636864/111147043-7012c400-85cd-11eb-9214-ebf64c5482a3.png)

## 2.7 vSemaphoreDelete()
![image](https://user-images.githubusercontent.com/79636864/111147080-76a13b80-85cd-11eb-995e-428afebe69c2.png)






