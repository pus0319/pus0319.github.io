---
date: 2021-03-10
title: "STM32F407_FreeRTOS 기본 개념 정리"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS 기본 개념 정리
=============
이번 포스팅은 본격적으로 FreeRTOS를 시작하기에 앞서 필수로 알아야하는 내용과 알면 좋은 내용과 같은

기본 개념을 정리하였습니다.

# 1. OS(Operating System)
## 1.1 OS(Operating System) 이란?
* 컴퓨터 시스템을 구성하는 하드웨어(Hardware)와 응용프로그램(User-Application)의 중간에 위치하여 
  User가 보다 쉽고 간편하게 컴퓨터 시스템을 이용할 수 있도록 제어하고 관리하는 시스템 소프트웨어
  
![image](https://user-images.githubusercontent.com/79636864/110556944-e5703600-8182-11eb-8923-f4a6c26c4d0c.png)

## 1.2 OS(Operating System) 구분
* 사용되는 분야에 따른 분류
    * RTOS : 주로 임베디드 분야
    * GPOS : 주로 범용 PC, 워크스테이션 등
    * Mobile OS : 휴대폰, 스마트워치 등의 휴대기기
* 작업 과정에 따른 분류
    * Interactive(사용자<->운영체제 간 상호작용하는)    (ex : 사용자가 조작할 수 있는 키보드, 마우스, 키패드 등을 사용)
    * Non-Interactive(사용자<->운영체제 간 상호작용하지 않는)    (ex : 사용자가 조작하지 않고 자동으로 실행)
* 기능에 따른 분류
    * 싱글 유저 – 단일 태스킹 OS (MS DOS)
    * 싱글 유저 – 멀티 태스킹 OS (Windows)
    * 멀티 유저 – 멀티 태스킹 OS (UNIX, Linux, Windows Server)
* 데이터 처리방식에 따른 분류
    * 일괄 처리 시스템(Batch System) :    일정양, 일정기간 데이터를 모아서 한번에 처리
    * 실시간 처리 시스템(Read-Time System) :    데이터가 발생하는 즉시 처리
    * 시분할 처리 시스템(Time-Shared System) :    CPU의 처리시간을 분할(slice)하여 하나의 처리장치(Processor)에서 다수의 Task를 시간적으로 균등하게 처리
    * 분산 처리 시스템(Distributed Processing System) :    지역적으로 분산된 컴퓨터들을 통신회선으로 연결하여 작업을 분담 처리

# 2. GPOS(General-Purpose Operating System)
## 2.1 Linux
* 1991년 리누스 토발츠에 의해 시작
* Open Source
* UNIX 기반 커널이 아님 (따로 리눅스 커널이 존재)
* 1994년 GNU Utils를 포함하는 커널 버전 1.0 발표
* IBM, Google, Microsoft 등의 기업에서 리눅스 개발을 지원
* PC, 워크스테이션, 서버, 모바일 등 다양한 곳에서 사용
* Steam이 지원
* 기술지원 비용은 무료가 아님
* 다양한 프로세서에 이식(x86, ARM, MIPS 등)
* 다양한 배포판이 존재(fedora, Ubuntu, Gento, slackware 등)
    * Linux에서는 위를 Root File System 으로 부르기도 한다.

## 2.2 임베디드 Linux
* Open Source로 라이센스 비용이 없음
* 다양한 프로세서에 이식
* 용도와 크기에 맞게 변경 가능
* 안정된 Linux 커널
* Yocto, Linaro, OpenEmbedde,d Buildroot, openWrt, LTIB 등의 빌드시스템 사용 가능.

# 3. RTOS(Real-Time Operating System)
## 3.1 RTOS(Real-Time Operating System) 이란?
* 주로 임베디드 시스템에서 사용되는 실시간 시스템을 위해 개발된 운영체제.
    * Real-Time : 한정된 자원의 환경에서 원하는(제한된) 시간 안에 작업 처리 완료 후 결과를 내야하는 시스템
    * Scheduler를 이용한 Multitasking을 지원
    * 선점형 스케줄링(Preemption Scheduling)
    * Task 우선순위(Priority)를 가짐

## 3.2 RTOS 목적 및 구분
* Multitasking 환경에서 Task의 처리 시간을 일관되게 유지하기 위한(가장 급한 Task들 부터 처리) 용도로 사용함.
* Hard Real-Time
    * 특정 작업을 일정 시간 안에 반드시 처리해야 하는 시스템
      (ex : 군사장비, 의료장비, 위성장비, 비행기 등)
* Soft Read-Time
    * 특정 작업에 대한 시간 제약이 있지만 그렇지 못하더라도 큰 영향이 없는 시스템
      (ex : TV, 세탁기, 라우터 등)

## 3.3 RTOS 구현 방법
* 시분할 시스템 하에서 우선순위 기반 스케줄링을 통해 우선순위가 높은 Task가 먼저 작업을 처리 할 수 있게 해야함.
* 스케줄링(Scheduling) : 운영 체제의 동작 기법 중의 하나로 특정 방법에 따라 프로세서(Task)들에게 CPU에 자원 배정 하는 것.
* 우선순위 기반(Priority Scheduling) : 현재 수행중인 Task보다 높은 Priority를 갖는 Event(ISR 호출 등)가 서비스를 요청 할 때, Task 전환(스케쥴링 작업)을 수행.

## 3.4 RTOS 종류
* vxWorks
    * 미국 윈드리버에서 개발
    * 빠른 멀티태스킹
    * WindSh 쉘 지원
    * 파일 시스템 입출력 지원
    * 다양한 프로세서 지원 (ARM, IA-32, x86-64, MIPS, PowerPC 등)

* FreeRTOS
    * 2003년 Richard Barry가 개발
    * Open Source
    * 2017년 아마존에 인수
    * 35개 이상의 마이크로 컨트롤러 지원(ARM, AVR, ColdFire, IA-32, PIC, MSP430 등)

* mbed-OS
    * ARM에서 개발
    * Open Source
    * IoT Device를 위한 RTOS
    * ARM의 Cortex-M, Cortex-R만 지원

## 3.5 OS 비교
## 3.5.1 Non-OS vs RTOS

![11](https://user-images.githubusercontent.com/79636864/110557705-75fb4600-8184-11eb-9fde-d04a176a31ea.jpg)

## 3.5.2 GPOS vs RTOS

![12](https://user-images.githubusercontent.com/79636864/110557766-96c39b80-8184-11eb-807f-d84f65ab306e.jpg)

## 3.6 RTOS에 쓰이는 기본적인 자료구조(참고)
* Task를 사용하기 위한 2가지 자료 구조에 대한 이해 필요.
## 3.6.1 Stack(스택)
* 나중에 들어온 데이터가 먼저 나간다 (LIFO)
* Push : Stack에 Data를 Input하는 행위(입력)
* Pop : Stack의 Data를 Output하는 행위(출력)
* Task에 관련된 정보를 저장하는데 쓰임.
* Task 내의 User가 작성한 Code를 저장하는데 쓰임.

![image](https://user-images.githubusercontent.com/79636864/110557856-bd81d200-8184-11eb-8ded-f3eb1e1beae5.png)

## 3.6.2 Queue(큐)
* 먼저 들어온 데이터가 먼저 나간다 (FIFO)
* Back position : 데이터가 Queue에 Input되는 위치(Enqueue 한다).
* Front position : 데이터가 Queue에 Output되는 위치(Dequeue 한다).
* Task 간 데이터를 주고 받는데 사용.(Message Queue)

![image](https://user-images.githubusercontent.com/79636864/110557922-dee2be00-8184-11eb-90e2-b770eb5b4cda.png)

## 3.7 RTOS 메모리 구조 기본

![16](https://user-images.githubusercontent.com/79636864/110558052-2701e080-8185-11eb-8067-afc1cf789c93.jpg)

* Memory(RAM)의 Task 영역은 Task의 실제 code 및 Local Variable가 저장 되어 있는 Stack과
  Task의 state, SP, PC, Priority 정보가 있는 TCB(Task Control Block)으로 구성.
* 기본적으로 Task내의 수행 작업이 Stack에 저장.
* SP(Stack Pointer) : 최근에 쌓아 올린 스택 위치를 가리킴. 
* Scheduler는 특정 Event(ex: ISR 호출) 발생 시, 
  Task state, Task Priority 에 따라서 CPU Registers의 Context를 Switching함. 
  
## 3.8 CMSIS_RTOS API / FreeRTOS API(Native API)
* FreeRTOS의 Native API를 바로 쓰는게 아닌 CMSIS_RTOS API를 사용
    * RTOS 또는 API 종류가 바뀌더라도 CMSIS_RTOS API의 코드내용만 바꾸면 되므로(User-Application Code를 안바꿔도됨) 호환성이 좋음.
* ‘cmsis-os.c’ 파일에 구현되어 있음.
    * (Middlewares/Third_Party/FreeRTOS/Source/CMSIS_RTOS)
* STM32CubeMX는 FreeRTOS 코드 생성 시, CMSIS_RTOS API를 이용함.
* User는 어느 API를 이용해도 상관 없음. 
    * 자세한 내용은 developing-applications-on-stm32cube-with-rtos.pdf 참고
[dm00105262-developing-applications-on-stm32cube-with-rtos-stmicroelectronics.pdf](https://github.com/pus0319/pus0319.github.io/files/6112283/dm00105262-developing-applications-on-stm32cube-with-rtos-stmicroelectronics.pdf)










