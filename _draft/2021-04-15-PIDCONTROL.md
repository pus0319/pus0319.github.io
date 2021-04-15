---
date: 2021-04-15
title: "제어시스템 개론과 PID 제어 정리"
categories: Embedded_HW
tags: jekyll
toc: true  
toc_sticky: true 
---

제어시스템 개론과 PID 제어 정리
=============

제어시스템 개론과 PID 제어에 대한 전반적인 내용 정리.

# 0. 제어시스템의 개론
제어시스템을 이해하려면 아래의 기본적인 개념들에 대한 정의를 알고 있어야함.    

1. 시스템(System)
    * 특정 동작을 수행하는 모든 것을 통칭하는 말
2. 플랜트( **P** , plant, 제어대상시스템, system to be controlled, process)
    * 어떤 기능을 수행하는 물리적 시스템
    * **제어하는 대상** 을 지칭함.
        * ex : 모터, 자동차, 비행기, 선박 등 등
    * 입력(input, 제어입력( **u** ), control input)과 출력( **y** , output, system output)을 가짐.
3. 제어기( **c** ,controller, system to control, 보상기, compensator, DSP)
    * 제어 목표를 수행하고자 고안된 시스템
    * 기준입력( **r** )을 통해 제어입력( **u** )를 생성하여 플랜트( **P** )를 구동
4. **제어시스템(Control System)**
    * 제어기( **c** )와 플랜트( **P** )가 결합된 형태의 시스템을 통칭하는 용어
5. 되먹임제어(Feedback Control, 궤환제어)
    * 플랜트( **P** )의 출력( **y** )를 사용하여 제어신호를 생성하는 기법
6. 강인성(robustness, 견실성)
    * 아래의 몇가지 경우의 조건에도 불구하고, 원하는 성능을 유지하는 능력
    1. 불확실성
    2. 외란
    3. 

