---
date: 2021-03-19
title: "STM32F407_FreeRTOS_9_S/W 타이머(Software Timer)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_9_S/W 타이머(Software Timer)
=============

FreeRTOS에 대한 기본적인 내용들은 거의 정리된거 같습니다.

이번 포스팅은 S/W타이머(Software Timer)에 대해 정리하였습니다.

저번에 예제로 DelayUntil()함수로 구현한 Periodic Task와 맥락은 비슷하지만,

S/W Timer는 Periodic, One-Shot 모두 쓸 수 있고 DelayUntil()보다 정확하다는 점이 있습니다.

그 외의 여러 이점이 있으니 자주 애용할거 같습니다.

# 1. S/W 타이머(Software Timer)
## 1.1 S/W 타이머(Software Timer) 란?
* FreeRTOS에서 제공하는 Task 유형 중 하나.
* Timer 기능을 탑재한 Task(Callback Function)를 만들 수 있는 기능.
    * 원하는 시기에 원하는 Period로 Timer를 Start 및 Stop 가능.
    * Periodic, One-Shot 모두 지원.
    * Callback은 Timer Service의 Context에서 실행.
    * 단, Callback함수 내에서는 Block을 시도해서는 안됨.
        * vTaskDelay (), vTaskDelayUntil ()을 호출,    
          Queue Semaphore API 사용 시, Block time 지정

![image](https://user-images.githubusercontent.com/79636864/111714346-c9f8df80-8894-11eb-98fc-c84af4009081.png)

## 1.2 S/W 타이머(Software Timer) 쓰임새(예시)
1. 각 Task에 대한 Watchdog 기능으로 사용.
    * 각 Task가 실행 완료 시, Timer를 Start(expiry할 때까지 갱신)
    * 만약, Task 하나가 expiry를 갱신하지 못할 시, Task가 중지되었고 판단. 해당 Task를 deinit하여 재시작.
2. Delay함수 대신 사용.
     * Delay함수는 해당 Task의 state를 Blocked으로 전환하기때문에 'Task가 죽어있다'라고 해도 무방.
     * Timer Task를 이용하여 expiry를 Delay Time으로 설정해서 사용한다면 Task의 state를 바꾸지 않아도 됨.
3. 그외 예시 추가바람.

# 2. S/W 타이머(Software Timer) API 정리
## 2.1 Software timer definitions

![image](https://user-images.githubusercontent.com/79636864/111715011-4213d500-8896-11eb-951f-1ceda5459223.png)

* configUSE_TIMERS 가 1 일 경우에만 사용 가능.
* (중요)주목해야할 점은 configTIMER_TASK_PRIORITY가 일반 TASK보다 높아야 정확한 Timing으로 동작할 수 있음.

## 2.2 xTimerCreate()

![image](https://user-images.githubusercontent.com/79636864/111715290-d9792800-8896-11eb-811d-1c5aa57cd71c.png)

## 2.3 xTimerChangePeriod()

![image](https://user-images.githubusercontent.com/79636864/111715357-fd3c6e00-8896-11eb-8886-c57d7bf86ee0.png)

## 2.4 xTimerStop()

![image](https://user-images.githubusercontent.com/79636864/111715415-1d6c2d00-8897-11eb-99c7-efcae25bbf1c.png)

## 2.5 xTimerDelete()

![image](https://user-images.githubusercontent.com/79636864/111715436-2957ef00-8897-11eb-8504-9a8bf584e1f2.png)





