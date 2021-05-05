---
date: 2021-04-27
title: "STM32_ADC_DMA"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_ADC_DMA_Multi
=============

STM32는 사용되는 ADC는 Polling 방식 및 DMA 방식으로 사용할 수 있음.    

그중 DMA 방식으로 사용하는 방법을 정리함.

# 1. ADC, DMA 초기 설정
1. ADC Clock 설정.
    * 데이터 시트를 참고하여 최대로 설정할 수 있는 ADC Clock값을 넘지 않는 선에서 설정.    

![image](https://user-images.githubusercontent.com/79636864/117084788-1afc5e80-ad83-11eb-8291-e307b547666d.png)    

2. ADC 기본 설정.
    * 아래의 Parameter들을 설정.

![image](https://user-images.githubusercontent.com/79636864/117084936-6e6eac80-ad83-11eb-8578-dfc06e422865.png)    

    * 


    
