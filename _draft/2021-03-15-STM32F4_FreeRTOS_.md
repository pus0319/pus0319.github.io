---
date: 2021-03-15
title: "STM32F407_FreeRTOS_4_상호배제(Mutual exclusion)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_4_상호배제(Mutual exclusion)
=============

이전 포스팅은 각종 Task예제를 통해 STM32CubeMX를 통해 생성한    
CMSIS_RTOS_API의 FreeRTOS 코드 내용 및 log 출력값을 정리했습니다.

이번 포스팅은 Kernel System을 사용하는데 앞서 기본적으로 알아야하는 개념인    
상호배제(Mutual exclusion)에 대해 정리해볼까 합니다.

# 0. 서론
여러 Task들이 서로 정보를 주고 받을 수 있는 방법 중에 가장 쉬운 방법은 공유자원(Local Resource)을 이용하는 것입니다.    
* 공유자원 : 여러 Task들이 공통적으로 사용 및 접근할 수 있는 모든 것을 말함.
    * Local variable(전역변수)
    * 함수 및 Public형의 클래스/메소드
    * 각종 Register(I/O)
    * 그 외 Local로 사용할 수 있는 모든 것들


* 공유자원을 쓰는 것은 괜찮은데, 여러 Task 사이의 경쟁이나 다른 이유에 의해 공유자원이 손실되는 상황이 발생합니다.
    * 어느 한 Task에서 공유자원을 **접근중에** 다른 Task(Priority가 높은 Task)가 강제로 공유자원을 사용하는 경우
* Kernel System에서는 위의 경우로 인한 데이터 손실을 막아야합니다.
* 그래서 나온 개념이 상호배제(Mutual exclusion)입니다.

# 1. 상호배제(Mutual exclusion)
## 1.1 xTaskCreate()
