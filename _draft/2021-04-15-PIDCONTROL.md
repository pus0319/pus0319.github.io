---
date: 2021-04-15
title: "PID 제어 정리"
categories: Embedded_HW
tags: jekyll
toc: true  
toc_sticky: true 
---

PID 제어 정리
=============

PID 제어에 대한 전반적인 내용 정리.

# 0. 제어시스템의 개론
1. 시스템(System)
    * 특정 동작을 수행하는 모든 것을 통칭하는 말
2. 플랜트(P , plant, 제어대상시스템, system to be controlled, process)
    * 어떤 기능을 수행하는 물리적 시스템
    * ***제어하는 대상***
        * ex : 모터, 자동차, 비행기, 선박 등 등
    * 입력(input, 제어입력(u), control input)과 출력(y, output, system output)을 가짐.
3. 제어기(controller, system to control, 보상기, compensator, DSP)
    * 제어 목표를 수행하고자 고안된 시스템
    * 기준입력(r)을 통해 제어입력(u)를 생성하여 플랜트를 구동
4. ```
