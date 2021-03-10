---
date: 2021-03-10
title: "ARM Cortex A/R/M 및 관련 라이브러리 정리"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

ARM Cortex A/R/M 정리
=============
이번 포스팅은 ARM Architecture인 Cortex-A/R/M 각 각에 대해 간단하게(주로 M에 대해선 자세하게) 정리하고

주요 라이브러리(CMSIS 외 Vendor별)를 정리하였습니다.

ARM Cortex-A/R/M 의 특징을 비교하였고 특히 FreeRTOS 공부에 사용중인 M에 대해 자세히 정리하였습니다.

# 1. ARM Cortex A/R/M
## 1.1 ARM Cortex A/R/M 간단 비교
* 아래의 사진은 Cortex A/R/M을 간단하게 비교한 표입니다.

![1](https://user-images.githubusercontent.com/79636864/110550102-65dc6a00-8176-11eb-8fd6-7cc797fdb57c.jpg)

```
1. Cortex-A가 들어있는 MCU의 경우는 PC나 스마트폰과 같은 
   OS(Linux 등)가 들어가고 하이엔드 Quality로 만들어야하는 Application에 쓰입니다.
  - Cortex Series 중에서 가장 뛰어난 Spec임을 알 수 있습니다.
  - 주 기능
    - 고성능 멀티미디어 지원
    - 메모리 관리 장치(MMU) : 리눅스와 같은 운영체제 사용 가능
    - 캐시메모리
    - ARM TrustZone 보안 : CPU Core의 보안 기능 추가(CPU mode 세분화)
      -> 탄생 배경 : ARM MCU 원칩으로 네트워크(TCP/IP) 기능 탑재 
                     -> 외부 해킹에 취약한 문제 발견.  
  - A : Application 의 약자
```

```
2. Cortex-R이 들어있는 MCU의 경우는 Automative(자동화),자동차 의료,LTE 베이스 밴드 모뎀 등의 
   Real-time Application에 쓰입니다.(단, MMU를 지원하지 않기 때문에 일반적인 OS는 올릴 수 없음.)
  - 주 기능
    - Low latency interrupt system
    - 메모리 보호 장치(MPU)
    - 캐시메모리
    - 밀착 결합 메모리(Tightly Coupled Memory)
  - R : Real-time Application의 약자 
```

```
3. Cortex-M이 들어있는 MCU의 경우는 PIC,AVR과 같은(8,16bit) 소형 마이크로프로세서 Target에 쓰입니다.
  - 주 기능
    - 초저전력(Low Power)
    - Low latency interrupt system
    - 메모리 보호 장치(MPU)
    - 중첩 벡터형 인터럽트 컨트롤러(NVIC)
    - Wake up 인터럽트 컨트롤러(WIC)
    - 최신 ARM TrustZone 보안(M23,M33만 해당)
  - M : MircoController의 약자 
```

## 1.2 ARM Cortex M의 각종 프로세스 별 설명

![2_](https://user-images.githubusercontent.com/79636864/110556013-0b94d680-8181-11eb-9adf-baf7552a6b3e.jpg)

## 1.3 ARM Cortex M 명령어 세트(Command Set) 및 Performance에 따른 구분

![image](https://user-images.githubusercontent.com/79636864/110553466-595b1000-817c-11eb-9c82-4a4e7c4e5ec0.png)

![image](https://user-images.githubusercontent.com/79636864/110553538-7abbfc00-817c-11eb-8056-8e9e02de7d29.png)

* 명령어가 많을 수록 CPU 처리속도가 빠름.

([ARM-Cortex-M 정보 관련 링크](https://mccoycomponents.com/blog/view/arm-cortex-m-family-introduction))

## 1.4 개인적인 ARM Cortex M 프로세스 순위(2021/03/10 기준)
* 1순위 : STM32F0, STM32F1, STM32F4, STM32F7, STM32L4
* 2순위 : STM32N7, STM32G0, STM32G4, STM32L5, SMT32L4+
* 3순위 : STM32F2, STM32F3, STM32L0

# 2. ARM Cortex Series 관련 각종 라이브러리 목록 정리
## 2.1 CMSIS(Cortex Microcontroller Software Interface Standard)
* ARM Cortex Architecture 관련 라이브러리. Cortex Series를 사용하는 여러 Vender들의
  MCU 개발 지원을 위한 독립적인 하드웨어 추상화 계층(vendeor-independent hardware abstraction layer)
  및 규약
  
([CMSIS 정보 관련 링크](https://developer.arm.com/tools-and-software/embedded/cmsis))  

### 2.1.1 CMSIS RTOS (v1,v2)
* ARM Cortex MCU에 RTOS 관련 API 사용 지원.
  
## 2.2 HAL
* ST 사 에서 제공하는 MCU별 라이브러리. 기존 Standard 라이브러리에서 개발 편의성을 개선의 목적이 있음.

## 2.3 LL
* ST 사 에서 제공하는 MCU별 라이브러리. HAL 라이브러리 사용 중 Critical한 시간적 제약(us 단위의 제약)
  이 있을 경우, 이를 개선하기 위한 목적으로 사용할 수 있음.

# 3. 정리
* 최근, Crotex-M에 간단한 AI기능을 탑재한 Core를 개발 중인 것으로 알고 있다.(시간날때 찾아볼 것.)
* 위와 같이 포스팅을 작성한 이유는 ARM-Cortex MCU를 이용한 제품 개발 시 
  가격성 및 신뢰성, Application의 요구사항에 가장 충족한 Platform을 찾자는 취지를 잊지 않기 위해서이다.
* ARM-Cortex MCU를 이용한 제품 개발관련해서 컨셉 설정 시작 전, 한 번씩 위의 내용들을 숙지할 것이다.
