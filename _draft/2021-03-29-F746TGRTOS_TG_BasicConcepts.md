---
date: 2021-03-29
title: "STM32F746_TouchGFX_FreeRTOS_4_TouchGFX Development"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_4_TouchGFX Development
=============

TouchGFX 개발을 시작하기전에 어떠한 과정이 있는지에 대한 큰그림과    

그 과정에서 내가 해야되는/알아야되는 구체적인 내용을 정리하기 위해    

아래의 홈페이지의 'Development'에 대한 내용을 정리하였습니다.

[TouchGFX_support](https://support.touchgfx.com/docs/basic-concepts/embedded-graphics)

# 1. Main Activites
* 자체 개발 보드를 기반으로 개발 하는 경우, 아래의 활동들을 하게 됨.   
  
(Main Activites)
![image](https://user-images.githubusercontent.com/79636864/112817366-512c2b80-90bd-11eb-830d-80f62d0d7f87.png)   

* 위에서 설명한 각 activities는 아래와 같은 Components를 생성함.   
![image](https://user-images.githubusercontent.com/79636864/112817502-791b8f00-90bd-11eb-94e2-d732b4ef968b.png)


```
TouchGFX 엔진은 활동을 통한 Component가 아님. TouchGFX 프로젝트 구성시 바로 사용 가능.
```   
 
## 1.1 Hardware Selection
### 1.1.1 Prototyping
* UI 프로토타입 제작은 STM32 평가 키트를 이용하면 신속하게 시작가능.
### 1.1.2 Custom Hardware
* 각종 고려해야할 사항이 많음. Section **Hardware Selection**을 참고.
* 자체 개발 보드와 유사한 STM32 평가 키트를 선택하고 UI 개발의 첫번째 단계에서 사용하는 것이 일반적.    

## 1.2 Board Bring Up
### 1.2.1 CubeMX
### 1.2.2 Application Templates (ATs)
* STM32 평가 키트 중 하나에 대한 기존의 Application Templates(AT)를 기반으로 프로젝트 시작 가능.
* CubeMX 구성을 기반으로 함. 다른 peripherals에 대한 추가 및 수정 가능.   

## 1.3 TouchGFX AL Development
* 개발 보드(Display+Board init Code) 위에서 TouchGFX엔진을 실행하는데 중요한 요소.
* H/W를 추상화하고 TouchGFX 엔진이 보드에서 실행되도록 하는 소프트웨어 계층.(S/W Layer)
### 1.3.1 CubeMX
