---
date: 2021-03-31
title: "STM32F746_TouchGFX_FreeRTOS_5_TouchGFX UI Development"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_5_TouchGFX UI Development
=============

'Development'에 대한 내용을 중에 미처 정리하지 못한 내용입니다.

실제로 UI 개발 실무에 직접적인 관련이 있는 내용을 정리하였습니다.

아래의 링크의 내용을 참고하였습니다.

[TouchGFX_support](https://support.touchgfx.com/docs/basic-concepts/embedded-graphics)

# 0. UI Development Introduction
* 어떻게 TouchGFX App이 개발이 되는지에 대한 방법을 자세히 설명.
* Software Architecture
    * 소프트웨어 아키텍처    
      TouchGFX App의 전체 구조(아키텍처)및 디자인,    
      TouchGFX Designer에서 생성된 코드와 사용자 코드 간의 관계를 설명.
* Working with TouchGFX
    * TouchGFX 에서 하는 작업에 대한 설명.    
      PC 시뮬레이터에서 지원되는 수많은 IDE에 이르기까지    
      TouchGFX App 개발 및 프로세스에 사용되는    
      다양한 tool의 Workflow에 대한 정보
* Designer User Guide
    * TouchGFX Designer 유저 가이드    
      TouchGFX Designer 의 다양한 구성 요소를 사용하는 방법에 대한    
      광범위한 가이드와 tip, 요령이 포함.
* TouchGFX Engine Features
    * TouchGFX Engine 기능.    
      비트맵 캐싱, 부분 프레임 버퍼, 다국어 지원 등과 같은    
      다양한 TouchGFX 엔진 기능에 대한 정보를 포함.
* UI 구성요소.    
    * 위젯(Widgets)에서 컨테이너(containers)에 이르기까지    
      TouchGFX에 있는 모든 UI 구성요소에 대한 정보
* Scenarios
    * 개발자가 실행할 수 있는 다양한 시나리오와 해결방법이 포함.    

# 1. Model-View-Presenter Design Pattern (MVP 아키텍처)
* TouchGFX UI 아키텍처 패턴 : MVP(Model-View-Persenter).
    * MVC(Model-View-Controller)에서 파생된 개념.
* MVP 패턴 주요 장점.
    1. 우려 사항 분리    
       코드를 별도 부분으로 나눔, 각 부분은 각자의 책임이 있음.    
       코드가 더 간단, 재사용 가능, 유지관리가 쉬움.
    2. 단위 테스트.    
       UI의 로직(persenter)이 시각적 레이어(View)와 분리되어 있음.    
       이로 인해 단위 별 테스트하기 쉬움.
* MVP의 3가지 Class
    * "Model"
        * 사용자 인터페이스에서 표시되거나 작동 할 데이터를 정의하는 인터페이스입니다.


