---
date: 2021-03-10
title: "STM32F407_FreeRTOS_1_Kernel System과 전경/배경 시스템"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_1_커널 시스템과 전경/배경 시스템
=============
앞선 포스팅은 본격적으로 FreeRTOS를 시작하기에 앞서 필수로 알아야하는 내용과 알면 좋은 내용과 같은 기본 내용을 간단하게 정리해봤습니다.

이번 포스팅은 기본 내용 중 커널 시스템과 전경/배경 시스템에 대해서 더 자세하게 정리해봤습니다.

# 1. 전경/배경 시스템(Foreground/Background System)
## 1.1 전경/배경 시스템(Foreground/Background System)이란?
* 임베디드 시스템 프로그램이 하나의 SuperLoop로 구성된 전경 프로세스(Foreground Process)와    
  
