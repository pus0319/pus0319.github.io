---
date: 2021-04-08
title: "STM32F746_TouchGFX_FreeRTOS_6_TouchGFX UI 입출력 구현"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_6_TouchGFX UI 입출력 구현
=============

실제로 UI 개발 실무에 적용하는 UI 입출력 관련 내용을 정리하였습니다.    

구체적으로 제품의 H/W부분(Backend)과 UI의 입출력(통신) 방법에 대한 내용입니다. 

연습용으로 진행하고 있는 Application을 통해 구체적인 Example에 대한 내용도 추가하였습니다.

더 자세하고 개념적인 내용이 있는 아래의 링크를 참고하면서 보면 좋습니다.

[TouchGFX_Backend Communication](https://support.touchgfx.com/docs/development/ui-development/touchgfx-engine-features/backend-communication)    
[TouchGFX_UIDel 정리](https://pus0319.github.io/embedded_freertos/F746TGRTOS_TG_UIDel/)

# 0. Model-View-Presenter Design Pattern(MVP 아키텍처)와 Backend System 구조

![image](https://user-images.githubusercontent.com/79636864/112955071-03292d80-917a-11eb-9089-60b6512706c6.png) 

* UI입출력 구현이 처음이라면 위의 그림에 대한 내용을 숙지하는 것이 유리합니다.
* 여기서 Backend Module은 실제제품의 H/W Component(센서,chipset,구동MCU의 IO 등 등)과    
  실제제품과 외부로 통신하는 다른 제품이 될 수도 있습니다.
* TouchGFX UI는 사용자의 입장에서는 단순히 LCD 패널(+터치패널)안의 버튼, 이미지, 애니매이션으로    
  구성되어있다고 보이지만, 이러한 눈에 보이는 것들은 'Model','Presenter','View' Class가 유기적으로 연결되어있는    
  MVP 아키텍처로 되어있습니다.
  
# 1. UI-->Backend
# 1.1 전체흐름.

