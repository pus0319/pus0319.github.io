---
date: 2021-04-27
title: "STM32_PWM_Interrupt_MODE"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_PWM_MODE
=============

STM32에 사용하는 PWM을 interrupt로 사용하는 방법을 정리.    

# 1. PWM 초기 설정
* 아래와 같이 사용하는 Timer의 channel을 PWM Generation으로 설정    

![image](https://user-images.githubusercontent.com/79636864/117086706-4b92c700-ad88-11eb-9c8a-99e6a9038b6b.png)    

* 관련 Configuration을 설정    

![image](https://user-images.githubusercontent.com/79636864/117086733-5ea59700-ad88-11eb-9436-f1087e8344a9.png)    

1. Counter Settings
    * TIM Clock = Datasheet를 참고하여 어떤 clock를 사용하는지(APB1,APB2 등)을 체크할 것.
    * Prescaler : 1
    * Period Count : 35999
    * TIM Tick = (1 / TIM Clock) * (Prescaler + 1) * (Period Count + 1)
    * PWM Frequency = (1 / TIM Tick)

2. PWM Generation Channel
    * mode
        * mode 1 : PWM Duty Level이 High
        * mode 2 : PWM Duty Level이 Low
    * Pulse : PWM Duty를 설정할 수 있음.
    * Fast mode : PWM Fast mode 여부

* interrupt mode를 사용하여 callback이 호출되도록 함.    

![image](https://user-images.githubusercontent.com/79636864/117087452-70883980-ad8a-11eb-852a-60448178efa5.png)    


# 2. PWM code Example
## 2.1 init
* 아래의 코드를 사용하여 PWM 
