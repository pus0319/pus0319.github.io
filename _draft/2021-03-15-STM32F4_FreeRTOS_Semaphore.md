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
* 세마포어 획득(,Wait,Pend)을 통해 공유자원에 대해 활용 권리 획득.
* 세마포어 반환(,Send,Post)을 통해 공유자원에 대해 활용 권리 반환.
## 1.2 세마포어(Semaphore) 분류
* 세마포어 반환 및 획득 할 수 있는 Task수에 따라
    * 1회 : Binary Semaphore , Mutex
    * 1회 초과 : Counting Semaphore
## 1.3 세마포어(Semaphore) 

# 2. 뮤텍스(Mutex)
## 2.1 뮤텍스(Mutex)란?
* 세마포어와는 같지만, Count 수가 아닌 Lock/UnLock으로 제어하는 방식.

