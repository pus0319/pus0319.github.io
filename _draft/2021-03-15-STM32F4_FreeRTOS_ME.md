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
* 그래서 나온 개념이 **상호배제(Mutual exclusion)**입니다.

# 1. 상호배제(Mutual exclusion)
## 1.1 상호배제(Mutual exclusion)란?
* 모든 Task가 접근하여 사용할 수 있는 공유자원(Local Resource)의 활용에 대해 하나의 Task만 할 수 있도록 하는 개념.
* Task가 공유자원(Local Resource)을 활용하는 과정은 아래와 같습니다.
    1. 공유자원 접근(Access) 및 호출(Call)
    2. 공유자원 시작(Start)
    3. 공유자원 수행(Perform)
    4. 공유자원 종료(End)
    5. 공유자원 탈출(Exit)
* 상호배제(Mutual exclusion)는 공유자원'A'가 어느 Task에 접근(Access)했고 다른 Task가 공유자원'A'를 활용하고자 한다면,    
  공유자원'A'가 탈출(Exit)할 때까지 활용하지 못하게 합니다.
## 1.2 상호배제(Mutual exclusion)가 필요한 경우
### 1.2.1 비재진입(Non-Reentrancy)
* 재진입(Reentrancy) : 언제 어느 시점에서든 호출되어도 문제가 없는 자원(변수 및 함수)
    * ex : Task1이 'A'함수를 호출해서 수행 중, ISR이 걸려서 'A'함수를 호출하고 또 ISR 복귀후 Context Switching에 의해    
      Priority가 높은 Task로 CPU점유율이 넘어 갔는데 거기서 또 'A'함수를 호출해버리고...
    * 위와 같이 진행되어도 아무런 문제가 없는 형태.
        * 보통, Local Variable로 짜여진 함수

