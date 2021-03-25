---
date: 2021-03-25
title: "STM32F746_TouchGFX_FreeRTOS_3_TouchGFX Basic Concepts"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_3_TouchGFX Basic Concepts
=============

본격적으로 TouchGFX를 시작하기에 앞서 Basic Concepts에 대한 내용을 정리하였습니다.

TouchGFX를 처음 사용하는 입장에서 이름 그대로 중요하고 basic한 알찬 내용들이였습니다.

아래의 홈페이지를 참고하여 정리하였습니다.

[TouchGFX_support](https://support.touchgfx.com/docs/basic-concepts/embedded-graphics)

# 1. Embedded Graphics
* TouchGFX : STM32 기반 MCU를 사용하나 임베디드 시스템에서 사용할 수 있는 GUI Application
* graphics : 60FPS로 실행되는 UI가 있는 대화형 응용 프로그램.    

## 1.1 The four main parts
* graphic 작업을 하는데 직접적인 관련이 있는 4가지 요소    

![image](https://user-images.githubusercontent.com/79636864/112457707-5cbae200-8d9f-11eb-9afd-e0539896e530.png)    

* 그 외의 부가적인 요소들도 포함될 수 있음.    

### 1.1.1 MCU
* Flash에서 이미지를 읽고 그것을 RAM에 Write함.
* 이 작업을 주기적이고 무한으로 진행.
* STM32에는 LTDC, Chrom-ART, Chorm-GRC 등의 H/W가 내장되어있음.    

### 1.1.2 RAM
* 계산된 이미지(FrameBuffer)가 저장되는 곳.

### 1.1.3 Flash
* 내부플래시는 그래픽 데이터를 모두 담을 수 있을만한 용량이 충분하지 않기 때문에    
  외부플래시를 추가하는 것이 대부분임.    
* 



