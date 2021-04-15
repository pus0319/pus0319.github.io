---
date: 2021-04-15
title: "제어시스템과 PID 제어 정리"
categories: Embedded_HW
tags: jekyll
toc: true  
toc_sticky: true 
---

제어시스템과 PID 제어 정리
=============

제어시스템 개론과 PID 제어에 대한 전반적인 내용 정리.

# 0. 제어시스템
## 0.1 제어시스템의 개론
제어시스템을 이해하려면 아래의 기본적인 개념들에 대한 정의를 알고 있어야함.    

1. 시스템(System)
    * 특정 동작을 수행하는 모든 것을 통칭하는 말
2. 플랜트( **P** , plant, 제어대상시스템, system to be controlled, process)
    * 어떤 기능을 수행하는 물리적 시스템
    * **제어하는 대상** 을 지칭함.
        * ex : 모터, 자동차, 비행기, 선박 등 등
    * 입력(input, 제어신호( **u** ), control input)과 출력(output, 제어변수( **y** ), system output)을 가짐.
3. 제어기( **C** ,controller, system to control, 보상기, compensator, DSP)
    * 제어 목표를 수행하고자 고안된 시스템
    * 기준입력( **r** )(엄밀히 말하면 오차( **e** ))을 통해 제어신호( **u** )를 생성하여 플랜트( **P** )를 구동
4. 되먹임제어(Feedback Control, 궤환제어)
    * 플랜트( **P** )의 출력( **y** )를 사용하여 제어신호를 생성하는 기법
    * 외란(disturbance)이 존재할 때, 출력( **y** )과 기준입력( **r** )을 비교하여    
      그 차이를 감소시키도록 하는 작동.
5. 강인성(robustness, 견실성)
    * 아래의 몇가지 경우의 조건에도 불구하고, 원하는 성능을 유지하는 능력
    1. 불확실성(uncertainty)
    2. 외란(disturbance)
        *  출력( **y** )에 나쁜 영향을 미치게 하는 신호
    3. 잡음(noise)
6. **제어시스템(Control System)**
    * 제어기( **C** )와 플랜트( **P** )가 결합된 형태의 시스템을 통칭하는 용어
    * 제어변수( **y** )를 측정, 측정값이 기준입력( **r** )에 가까워지도록    
      제어신호를 만드는 동작을 수행함.
    * 강인성(robustness)을 보장하기 위해 되먹임제어(Feedback Control, 폐루프(Closed-loop))도 구성함.    

![image](https://user-images.githubusercontent.com/79636864/114800515-d2c9cc00-9dd4-11eb-8cf2-234ddc2a94b7.png)    


## 0.2 제어시스템의 목표    
### ● 제어변수( **y** )가 기준입력( **r** )에 가까워지도록 제어기( **C** )를 설계하는 것!
### ● 강인성(robustness)을 보장할 것!  
### ● 폐루프제어(Closed-loop)의 단점인 안정도를 고려할 것!


1. 제어시스템의 장점
    * 불확실성(uncertainty) 과 외란(disturbance)에 강함
    * 덜 정확하고 저비용 부품으로 성능 보장
2. 제어시스템의 단점
    * 안정도를 고려해야함.    

# 1. PID 제어
## 1.1 PID 제어의 정의
### ● 기준입력( **r** )과 측정한 출력( **y** )의 차이인 오차( **e** )를 이용한
### ● 비례(Proportional, P), 적분(Integral, I), 미분(Differential, D)을 통해    
### ● 제어에 필요한 제어값(MV : Manipulated Variable)을 계산하여    
### ● 제어신호( **u** )를 만드는 제어기    

![image](https://user-images.githubusercontent.com/79636864/114802890-6e5d3b80-9dd9-11eb-8326-be266f217c4e.png)    

* 일반적인 PID제어기는 3개의 항(P, I ,D)를 더해서 제어값(MV)을 계산하도록 구성.    

![image](https://user-images.githubusercontent.com/79636864/114803325-34d90000-9dda-11eb-9be9-838b53c161ae.png)    


## 1.2 P제어
