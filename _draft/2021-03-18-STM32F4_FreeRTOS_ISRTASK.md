---
date: 2021-03-18
title: "STM32F407_FreeRTOS_8_인터럽트 태스크(Interrupt Task)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_8_인터럽트 태스크(Interrupt Task)
=============

이번 포스팅은 ISR에 대해 정리하였습니다.

작성한 모든 Task보다 높은 Priority를 가지는 것은    

Interrupt Task라고 할 수 있는 ISR 입니다.

ISR에서도 FreeRTOS API를 사용할 수 있는데 

이에 대한 자세한 내용을 정리하였습니다.

# 1. ISR(Interrupt Service Routine) Task
## 1.1 Task vs ISR

![image](https://user-images.githubusercontent.com/79636864/111572520-14725180-87ec-11eb-9cee-85f73b2e22b6.png)

* 중요한 점은 ISR의 호출이 Task의 Context Switching을 수행하도록 해줌.
    * 단, 우선순위 선점 관련 Task Scheduling은 하지 않음.

## 1.2 FreeRTOS Interrupt Priority Configuration

![image](https://user-images.githubusercontent.com/79636864/111572526-1805d880-87ec-11eb-859c-16ab753d1719.png)

* 중요한 점은 ISR안에서 FreeRTOS API 호출 시, 'FromISR' 전용 API를 호출해야함.
    * 단, Cortex-M의 NVIC Interrupt 중, LIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY 값보다 낮은    
      ISR에서는 호출하면 안됨.

## 1.3 Cortex-M의 NVIC Interrupt

![image](https://user-images.githubusercontent.com/79636864/111572537-1cca8c80-87ec-11eb-9e51-5949ac06eee0.png)

# 2. 관련 API
## 2.1 'FromISR'

* 각 API의 내용 참고.

## 2.2 portYIELD_FROM_ISR(xHigherPriiorityTaskWoken)

![image](https://user-images.githubusercontent.com/79636864/111572667-65824580-87ec-11eb-9bb0-b00607024b83.png)


# 3. 정리

ISR Task에 대해 정리했습니다.
예제 등의 크게 참고해야할 내용은 없는 것 같습니다.
CMSIS_RTOS_API에서는 ISR Task에서 호출 시, 'FromISR' 전용 함수를 호출하도록 되어있기 때문에
크게 문제는 없을 거 같습니다.

