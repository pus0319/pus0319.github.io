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
    * Interactive(사용자<->운영체제 간 상호작용하는)
      (ex : 사용자가 조작할 수 있는 키보드, 마우스, 키패드 등을 사용)
    * Non-Interactive(사용자<->운영체제 간 상호작용하지 않는)
      (ex : 사용자가 조작하지 않고 자동으로 실행)
* 기능에 따른 분류
    * 싱글 유저 – 단일 태스킹 OS (MS DOS)    - 싱글 유저 – 멀티 태스킹 OS (Windows)
    * 멀티 유저 – 멀티 태스킹 OS (UNIX, Linux, Windows Server)
* 데이터 처리방식에 따른 분류
    * 일괄 처리 시스템(Batch System) : 일정양, 일정기간 데이터를 모아서 한번에 처리
    * 실시간 처리 시스템(Read-Time System) : 데이터가 발생하는 즉시 처리
    * 시분할 처리 시스템(Time-Shared System) : CPU의 처리시간을 분할(slice)하여 하나의 처리장치(Processor)에서 다수의 Task를 시간적으로 균등하게 처리
    * 분산 처리 시스템(Distributed Processing System) : 지역적으로 분산된 컴퓨터들을 통신회선으로 연결하여 작업을 분담 처리


