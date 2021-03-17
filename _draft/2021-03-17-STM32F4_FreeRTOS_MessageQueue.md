---
date: 2021-03-17
title: "STM32F407_FreeRTOS_7_메세지큐(MessageQueue)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_7_메세지큐(MessageQueue)
=============

오늘은 메세지큐(MessageQueue)에 대해 정리했습니다.    

Task간 Data를 공유하기 위해 할 수 있는 가장 쉬운 방법은    
공유 Data를 Global Resource로 만들어 사용하는 것입니다.    

하지만, 이럴 경우 상호배제(Mutual Exclusion)기법 등이 필요한 경우가 생깁니다.

굳이 Task간 Data를 공유만할 건데 Global Resource로 만들어서 문제가 생길 수 있는    
가능성을 만들 필요는 없습니다.    

여기서 나온 개념이 메세지큐(MessageQueue)입니다.

# 1. 메세지큐(MessageQueue)
## 1.1 메세지큐(MessageQueue) 란?
* 태스크 메세지(데이터)를 주고받기 위한 Queue형태의 오브젝트


# 2. 메세지큐(MessageQueue) API 정리

# 3. 메세지큐(MessageQueue)를 활용한 예제
