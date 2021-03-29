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
 
## 1.1 
