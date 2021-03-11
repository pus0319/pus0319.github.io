---
date: 2021-03-11
title: "STM32F407_FreeRTOS_2_Task/Scheduler/Context Switching"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_2_Task/Scheduler/Context Switching
=============
앞선 포스팅은 기본 내용 중 커널 시스템과 전경/배경 시스템에 대해서 더 자세하게 정리해봤습니다.    
이제 FreeRTOS를 사용하기 위해 본격적으로 Task와 Scheduler, Context Switching에 대해 자세하기 정리했습니다.

# 1. Task
## 1.1 Task이란?
![image](https://user-images.githubusercontent.com/79636864/110773132-c0bdb080-829f-11eb-8b27-6d7cdd3e9182.png)
* Task는 위의 사전에서 나오는 정의와 같이 일련의 명령어들로 구성된 독립적인 실행 단위입니다. 스레드 또는 프로세스를    
  하나의 실행 단위로 보는데 FreeRTOS에서는 스레드로 보시면 될 거 같습니다.
* 여러 동작에 대한 실시간 성능을 만족시키기 위해 전체 프로그램을 Scheduling이 가능한 작은 단위로 쪼갠 것으로    
  생각해도 될 거 같습니다.
* 앞서 말씀드렸듯이, 하나의 Task에는 각기 다른 하나의 loop(Background System)을 가지고 있습니다.    

## 1.2 Task state machine
* state machine이란 각 각의 state들을 어떤 조건에 따라 연결해 놓은 것입니다. 일종의 Diagram으로 보셔도 무방합니다.
* 당연하게도 Task에 대한 여러 state들을 어떤 조건에 따라 연결해 놓은 Diagram으로 알아두시면 되겠습니다.
![image](https://user-images.githubusercontent.com/79636864/110773994-ac2de800-82a0-11eb-9a29-b584e24e2088.png)
![image](https://user-images.githubusercontent.com/79636864/110774087-c49e0280-82a0-11eb-9292-79a3e241e453.png)


