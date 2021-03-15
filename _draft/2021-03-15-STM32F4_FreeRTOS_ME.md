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
여러 Task들이 서로 정보를 주고 받을 수 있는 방법 중에 가장 쉬운 방법은 공유자원(Shared Resource)을 이용하는 것입니다.    
* 공유자원 : 여러 Task들이 공통적으로 사용 및 접근할 수 있는 모든 것을 말함.
    * Global variable(전역변수)
    * 함수 및 Public형의 클래스/메소드
    * 각종 Register(I/O)
    * 그 외 Local로 사용할 수 있는 모든 것들

* 공유자원을 쓰는 것은 괜찮은데, 여러 Task 사이의 경쟁이나 다른 이유에 의해 공유자원이 손실되는 상황이 발생합니다.
    * 어느 한 Task에서 공유자원을 **접근중에** 다른 Task(Priority가 높은 Task)가 강제로 공유자원을 사용하는 경우
* Kernel System에서는 위의 경우로 인한 데이터 손실을 막아야합니다.
* 그래서 나온 개념이 **상호배제(Mutual exclusion)**입니다.

# 1. 상호배제(Mutual exclusion)
## 1.1 상호배제(Mutual exclusion)란?
* 모든 Task가 접근하여 사용할 수 있는 공유자원의 활용에 대해 하나의 Task만 할 수 있도록 하는 개념.
* Task가 공유자원을 활용하는 과정은 아래와 같습니다.
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
        * 보통, Local Variable만 사용하는 함수.
        * Local Variable의 경우, 각 Task의 Stack에 각 각 올라가기 때문에 서로 별개의 변수가 됩니다.
* 비재진입(Non-Reentrancy)
    * 공유자원을 사용하는 함수의 경우, 여러 Task들의 접근으로 인해 **Sequence가 분리**되어 공유자원값이 꼬여버리는 자원
    * **Sequence가 분리**되어 여러 Task들이 진입을 할 경우, 데이터 손실의 우려가 매우 큼.
* Critical Section : **Sequence가 분리**될 경우, 데이터 손실 등의 오류를 일으킬 수 있는 구간.
* 위와 같이 비재진입성을 가진 자원에 접근 할 경우, Critical Section 구간에서 여러 Task들이 접근할 경우, 데이터 손실 등의 문제가 발생.


# 2. 상호배제 구현방법
## 2.1 Interrupt Enable/Disable
* 가장 손쉬운 방법 중 하나.
* ISR이 일어나지 않도록 해서 CPU점유율이 Kernel Scheduler로 넘어가지 않게 하기.
    * CPU점유율이 다른 Task로 넘어가는 경우를 원초적으로 방지.
* Interrupt Disable 시간이 너무 길면 시스템의 응답성에 영향을 줄 수 있음.
    * 많은 데이터 복사 등의 시간이 많이 걸리는 작업이라면 다시 한번 생각해봐야함.
### 2.1.1 예시
* ARM-Cortex-M Series에서는 아래와 같은 Interrupt Enable/Disable 함수를 지원합니다.
~~~c
__enable_irq();
~~~

~~~c
__disable_irq();
~~~

## 2.2 Test - And - Set(TAS)
* 제가 전경/배경 System에서 자주 썼던 방법입니다.
* Kernal System에서는 자주 쓰지는 않을 것 같습니다.(세마포어가 있음)
* 마찬가지로, 위의 2.1방법도 같이 병행해야함.
* 함수가 공유자원을 Access하기전에 별도의 Flag 변수를 점검하고 그 변수의 값에 따라서    
  Access 여부를 정함.
### 2.2.1 예시
~~~c
void Function_A(void)
{
   if(A_Flag == 0)
   {
      A_Flag = 1;
      __disable_irq();
      _Uses_A(); // 공유자원 활용(Access ~ Exit)
      A_Flag = 0;
      __enable_irq();
   }
   else
   {
      // 공유자원을 활용할 수 없다.
   }
}
~~~


## 2.3 Scheduler Lock/Unlock
* preemptive Kernel 한정으로 적용 가능
* 무조건 ISR이 공유자원을 접근하지 않는 경우에만 적용 가능.
* 거의 사용하지 않을 거 같음. 이런것도 있구나 하고 알기만 하기
### 2.3.1 예시

