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
