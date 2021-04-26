---
date: 2021-04-26
title: "STM32_IWDG,WWDG"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_IWDG,WWDG
=============

STM32는 사용되는 Clock에 따른 2가지의 Watchdog가 있음.    

IWDG와 WWDG를 동시에 사용 시,    

Main Clock이 오동작 시, Main Clock외의 Clock으로 동작하는 Watchdog(IWDG)를 이용하여,    

MCU System을 강제로 Reset할 수 있음.    

이러한 동작을 통해 MCU 동작의 신뢰성을 높일 수 있음.

# 1. IWDG(Independent Watchdog)
* 자체 전용 LSI(Low Speed Clock)에 의해 동작하는 WDG.
* Main Clock에 완전히 독립적인 프로세스로 감시해야할 경우 활용하면 됨.
* 단, 타이밍 정확도는 WWDG보다 낮음.    

## 1.1 IWDG Parameter
### 1.1.1 Non Window
* IWDG Clock : LSI Clock
* Prescaler : IWDG Clock을 Prescale 하는 값.
* down-counter reload : Prescale IWDG Clock 당 1씩 다운 카운팅함.    
                        값이 0이 될 경우, IWDG Reset이 발생.    
                        
* IWDG Reset Time = (1/IWDG Clock * Prescaler) * down-counter reload
                        
### 1.1.2 Window
* IWDG Clock : LSI Clock
* Prescaler : IWDG Clock을 Prescale 하는 값.
* window value : down-counter value값이 window value값보다 낮을 경우, IWDG 카운터 값을 Refresh 할 수 있음.(Refresh allowed)    
                 그 외는 Refresh 할 수 없음(Refresh not allowed)
* down-counter value : Prescale IWDG Clock 당 1씩 다운 카운팅함.    
                       값이 0이 될 경우, IWDG Reset이 발생.    
   
### 1.1.3 STM32CubeMX Setting
1. HSE Clock 활성화(H/W 회로에 맞게 Setting)
2. IWDG 기능 활성화
3. LSI Clock 값 확인
4. 
# 2. WWDG
https://m.blog.naver.com/PostView.nhn?blogId=eziya76&logNo=220987832475&proxyReferer=https:%2F%2Fwww.google.com%2F
