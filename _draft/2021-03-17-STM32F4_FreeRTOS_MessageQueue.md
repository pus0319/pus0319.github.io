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
* 
![image](https://user-images.githubusercontent.com/79636864/111404099-8d50ab00-8711-11eb-8e22-89456bebfc1a.png)

## 1.2 메세지큐(MessageQueue) vs 메일박스(MailBox)

![image](https://user-images.githubusercontent.com/79636864/111404119-93468c00-8711-11eb-81c1-d05f80e13b57.png)

# 2. 메세지큐(MessageQueue) API 정리
## 2.1 xQueueCreate()

![image](https://user-images.githubusercontent.com/79636864/111404221-ba9d5900-8711-11eb-9743-e43080889f96.png)

## 2.2 xQueueSend()

![image](https://user-images.githubusercontent.com/79636864/111404247-c852de80-8711-11eb-8123-ab953e41cb6d.png)

## 2.3 uxQueueMessagesWaiting()

![image](https://user-images.githubusercontent.com/79636864/111404269-cee15600-8711-11eb-81b0-a2614d90185f.png)

## 2.4 xQueueReceive()

![image](https://user-images.githubusercontent.com/79636864/111404291-d7399100-8711-11eb-8433-ca335e8c1285.png)


# 3. 메세지큐(MessageQueue)를 활용한 예제
# 3.1 메세지큐(MessageQueue) 기본

# 3.1.1 소스코드

# 3.1.2 결과 및 고찰


