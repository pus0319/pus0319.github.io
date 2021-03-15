---
date: 2021-03-12
title: "STM32F407_FreeRTOS_2_Task/Scheduler/Context Switching"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_2_Task/Scheduler/Context Switching
=============
앞선 포스팅은 기본 내용 중 커널 시스템과 전경/배경 시스템에 대해서 더 자세하게 정리해봤습니다.    
이제 FreeRTOS를 사용하기 위해 본격적으로 Task와 Scheduler, Context Switching에 대해 자세하기 정리했습니다.

# 1. Task
## 1.1 Task이란?
![image](https://user-images.githubusercontent.com/79636864/110773132-c0bdb080-829f-11eb-8b27-6d7cdd3e9182.png)
* Task는 위의 사전에서 나오는 정의와 같이 일련의 명령어들로 구성된 독립적인 실행 단위입니다. 스레드 또는 프로세스를    
  하나의 실행 단위로 보는데 FreeRTOS에서는 스레드로 보시면 될 거 같습니다.
* 여러 동작에 대한 실시간 성능을 만족시키기 위해 전체 프로그램을 Scheduling이 가능한 작은 단위로 쪼갠 것으로    
  생각해도 될 거 같습니다.
* 앞서 말씀드렸듯이, 하나의 Task에는 각기 다른 하나의 loop(Background System)을 가지고 있습니다.    

## 1.2 Task state machine
* state machine이란 각 각의 state들을 어떤 조건에 따라 연결해 놓은 것입니다. 일종의 Diagram으로 보셔도 무방합니다.
* 결론적으로 Task에 대한 여러 state들을 어떤 조건에 따라 연결해 놓은 Diagram으로 알아두시면 되겠습니다.
![image](https://user-images.githubusercontent.com/79636864/110773994-ac2de800-82a0-11eb-9a29-b584e24e2088.png)
![image](https://user-images.githubusercontent.com/79636864/110774087-c49e0280-82a0-11eb-9292-79a3e241e453.png)
* 주로 보셔야할 것은 **CPU의 점유권에 따른 관점**입니다.
    * 앞서 말씀드렸듯이, 각 각의 Task 중 하나의 Task만 CPU의 점유권을 가질 수 있습니다.    
      즉, CPU의 점유권을 가진 Task만이 Running state라고 할 수 있습니다.
    * Running state의 Task가 모든 동작을 완료했다면 다른 Task로 CPU의 점유권을 넘겨야합니다.
      그러기위해선 Task의 state를 Blocked로 바꿔야합니다.
    * 만약 state를 바꾸지 않는다면, 현재 Task보다 높은 Priority의 Task만 CPU의 점유권을 가질 수 있고    
      반대로 현재 Task보다 낮은 Priority의 Task는 영원히 CPU의 점유권을 가질 수 없습니다.
    * 어떤 Task가 지금 Running state이고, 그 Task의 Priority는 무엇이고, 언제 Block state로 전환되는지에    
      대한 내용은 추후 Debugging 시, 매우 중요한 관점이라 생각합니다.    
      ('CPU의 리소스를 최대한 효율적으로 사용하도록 만드는 것이 궁극적인 목표다.' 라고 생각하면서 해야겠습니다.)
```
항상 Task안에 코드를 작성 시, 이러한 점을 염두해두고 해야 될 거 같습니다.
```

## 1.3 Task parameter
* Task도 결국엔 하나의 조그마한 시스템이자 프로그램이라 할 수 있습니다.
    * 입력과 출력이 있고 입력을 처리하는 코드 알고리즘인 유기체(entity)가 있기 때문입니다.
* 그렇기 때문에 Task도 어떻게 만드는가에 따른 특정한 성질들 존재 합니다.    
  즉, parameter가 존재합니다.

![image](https://user-images.githubusercontent.com/79636864/110778482-bf8f8200-82a5-11eb-9f17-3b73e35bf1b1.png)

* T(Task)
    * 보통 Task를 기호로 나타낼 땐 '타우'를 쓰고 아래첨자로 Task numbering을 합니다.
* P(Peroid)    
    * Task가 실행되는 주기를 칭합니다.
    * Periodic Task의 형태일 시, 정의합니다.    
      당연하게도 주기성이 없을 경우, 아무 의미가 없습니다.
    * Priority가 낮은 Task일 경우, Preemption하는(Priority가 높은)Task는 무엇이 있고    
      얼마나 실행되는지 따져봐야되기 때문에 Period를 널널하게 해줘야합니다.
* D(Deadline)
    * Task가 실행되고 종료될때까지의 최대 시간(기한)입니다.
      (여기서 종료는 해당 Task가 CPU의 점유권을 놓았을 때를 말합니다.)    
    * **무조건**정해진 기한 까지 Task를 종료해야합니다.
    * Periodic Task에서 주기성을 보장하기 위해선 'P'보다 초과하도록 만들면 안됩니다.
* C(Worst-execution time)
    * Task의 최악의 실행시간을 나타냅니다.
    * Task가 어떤 유효한 동작을 수행할 시 걸릴 수 있는 최대 시간이라고 생각하면 되겠습니다.    
      (CPU의 점유권을 계속 가지고 있는 시간)
* pr(Priority)
    * Task의 우선순위를 나타냅니다.
    * 값이 높을수록(High) 우선순위가 높습니다.    
      (반대로 값이 낮을수록(Low) 우선순위가 낮습니다.)
* R(Worst-Case Response Time)
    * 전체 프로그램이 시작되고 해당 Task가 실행 후 실행이 끝나는(응답) 시간까지를 말합니다.
    * 여러 Task가 유기적으로 동작 시, 최악의 응답 시간을 측정할 때 활용됩니다.
* 표의 내용을 정리해보면(단위 : ms), Task1은 주기는 10ms, 기한은 10ms, 최악의 실행시간은 3ms, 우선순위는 99이다.    
  라고 할 수 있습니다.
```
추후 수많은 동작들을 Task로 구현 시, Task의 주기성 및 결정성을 최대한 보장하기 위해    
각 Task별 parameter를 정의하고 Design하는 방식으로 해보자는 생각을 가졌습니다. 
```

## 1.4 MultiTasking 이란?
* 말그대로 Task를 Multi로 수행하는 것을 말하고 커널이 수행해줍니다.
* 개발자는 각 각의 Task를 Superloop 방식 코드작성하듯이 하면 됩니다.    
  마치 각 각이 하나의 일만을 한다고 생각하고 작성해도,    
  커널이 알아서 번갈아가며 Task를 효율적으로 수행할 것이라고 생각하면 되겠습니다.
* 하지만, MCU안의 CPU core는 Single 이기에 **진짜로**Task가 동시에 실행된다고 생각하면 안됩니다.
    * 실제로, 1개의 CPU가 엄청 빠른 시간동안에 각 각의 Task를 **번갈아 가면서** 처리합니다.    
      제품을 사용하는 입장에 볼때는 여러 Task를 동시에 처리하는것처럼 느껴지게 됩니다.
    * 요즘엔 소형 MCU도 내부 system clock을 150Mhz까지 채배해서 쓸 수 있도록 많이 보편화되서 가능하게 되었습니다.
* 그럼, 어떻게 그리고 어떤 방식으로 Task를 **번갈아 가면서** 처리하는가?    
  위의 의문을 풀기 위해선 커널의 Scheduler와 문맥전환(Context Switching)을 알아야합니다.
  
# 2. Scheduler
## 2.1 Scheduler란?
* 한정적인 자원을 효율적으로 사용할 수 있도록 Task의 특정 실행 시간에 실행할 Task를    
  선택하는 작업을 하는 주체
    * Task State Swtiching, Context Switching
* 정확한 시간에 적절한 Task가 실행되는 것을 보장합니다.
* FreeRTOS에서의 스케줄링 방식은 아래와 같습니다.
![image](https://user-images.githubusercontent.com/79636864/110783725-26b03500-82ac-11eb-9a44-2daeaa6b51a5.png)

* FreeRTOS에서는 선점형/비선점형을 선택할 수 있습니다.(기본은 선점형으로 사용합니다.)
* 
## 2.2 Non-preemptive Kernel(비선점형 커널) 이란?
* 먼저 CPU 점유권을 점령을 하지 않는 커널
* 무조건 ISR로 뛰어들어가기 전에 돌고 있던 Task로 다시 CPU 점유권을 넘김.
* Multitasking을 구현 시, Task 자신의 state를 Blocked로 바꿔야함.
* 특징
    * 인터럽트 지연시간이 짧음.
    * Task 레벨에서도 비재진입 함수를 사용할 수 있다.
    * Task 응답성이 떨어짐.
        * 현재 Task의 긴 수행시간 때문에 Priority가 높은(매우 중요한) Task가 긴 수행시간만큼 대기할 수 있음.
## 2.3 Preemptive Kernel(선점형 커널) 이란?
* ISR로부터 복귀 할 때 Priority가 가장 높은 Task에게 CPU 점유권을 넘김.
* Task 응답성이 개선됨.
* 가장 높은 Priority의 Task가 언제 CPU의 점유권을 가질 수 있는지 알 수 있음.
* Critical Section, 비재진입 함수의 사용 등에서 문제점 발생.
    * 특정 비재진입 함수를 호출해서 사용 중인 Task에서 Priority가 높은 Task로 CPU 점유권이 넘겨지고    
      그 Task에서 특정 비재진입 함수를 호출해버리면, 데이터가 완전히 꼬여버리는 문제 발생.
        * **상호배제적**으로 공유자원(Local Resource)를 접근해야함.


# 3. Context Switching
## 3.1 Context란?
* Task가 실행되면서 Memory(RAM)에 액세스한 리소스(프로세서 레지스터, 스택 등)로 구성됨.
## 3.2 Context Switching이란?
* Task가 번갈아 실행되면서 프로세서 레지스터와 같은 Context가 잘못된 값이 생성됨을 방지하기 위해    
  일시 중단 직전과 동일한 Context를 가져야합니다.
* Kenrel은 이러한 경우를 보장하고 일시 중단 직전의 Context를 저장하고    
  Task가 다시 시작되면 복원됩니다.
* 결론적으로, 일시 중단된 Task의 Context를 저장하고 재게되는 Task의 Context를 복원하는 과정을 말합니다.
* Context Switching 과정은 따로 자세하게 정리해보겠습니다.
## 3.3 Context Switching이 일어나기 위한 조건
* 모든 ISR(Interrupt Service Routine).
* CPU의 점유율은 Kernel의 Scheduler가 가짐.
* Ready state들의 Task 중에 다음에 서비스를 해줘야할 Task를 결정.
    * Priority가 높은 Task에게 점유율을 넘김.

# 4. 정리
Task, Schduler, Context Switching을 정리해봤습니다.    
다음엔 실제로 FreeRTOS에 사용하는 코드들과 함께 Task관련 여러 예제들을 정리할 것입니다.


