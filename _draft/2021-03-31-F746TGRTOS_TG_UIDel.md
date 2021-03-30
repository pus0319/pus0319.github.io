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

# 1. Software Arcitecture
## 1.1 Model-View-Presenter Design Pattern (MVP 아키텍처)
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
        * 사용자 인터페이스에서 표시되거나 작동 할 데이터를 정의하는 인터페이스.
        * TouchGFX UI가 아닌부분 (BackendSystem)과의 통신을 수행.
    * 'View'
        * 데이터 ('Model'으로부터)를 표시하고 사용자 명령 (Event)을    
          'Presenter'에게 전달하여 해당 데이터에 대해 작동하는 수동 인터페이스.
    * 'Presenter'
        * 'Model'과'View'에 따라 행동.    
          리포지토리 ('Model')에서 데이터를 검색하고 'View'에 표시 할 형식을 지정.

![image](https://user-images.githubusercontent.com/79636864/112954868-c826fa00-9179-11eb-8601-27fe1b32e2f8.png)    

* Backend System 과의 communication
    * TouchGFX의 Tick마다 한번씩 호출되는 기능만제공.    
      호출되었을 때 필요한 통신을 처리하도록 할 수 있음.    
    * Backend 통신에 대한 자세한 내용은 **Backend Communication** 참고.
      
![image](https://user-images.githubusercontent.com/79636864/112955071-03292d80-917a-11eb-9089-60b6512706c6.png)    

* MVP Class가 TouchGFX UI개발에서 구현되고 사용되는 방법에 대한 자세한내용은 **Code Structure** 참고

## 1.2 The Screen Concept('Screen' 개념)
* TouchGFX App은 Screen을 여러개 가질 수 있음.
* TouchGFX에서의 'Screen'
    * UI 요소(Widgets) 및 관련 비즈니스 로직의 논리적인 그룹.    
      비즈니스 로직에 대한 자세한 내용    
      [business logic???](https://mommoo.tistory.com/67)
    * 'Screen' = 'View' Class + 'Presenter' Class    
      'View' Class는 모든 위젯이 포함되어있음.    
      'Presenter' Class는 'Screen'에 대한 비즈니스 로직이 포함되어있음.
    * 단일 Screen Context내에서    
      (하나의 View Class와 하나의 Presenter Class)로도 가능하지만    
      분할식으로 가는 것이 좋음.
      
### 1.2.1 Defining Screens('Screen'들을 정의하는 방법론)
* 시각적 및 기능적으로 관련이 없는 UI영역은 다른 'Screen'에 유지되어야함.
* 'Main Screen'과 'Setting Screen' 등으로 나누어서 만드는 것이 좋음.
* 또한 'Setting Screen'을 전체 option들을 표시하는 하나의 'Screen'과    
  특정 option을 설정하기 위한 별도의 'Screen'으로 나누는 것이    
  더 유용할수도 있음.

### 1.2.2 Currently Active Screen(현재 활성 'Screen')
* TouchGFX가 'Screen'을 메모리에 할당 시, 한번에 하나의 'View'와 하나의 'presenter'만 활성화 가능.
* 'Backend'(UI 코드를 제외한 모든 동작) 및 HW 주변장치에서 수신되는 Event를 현재 활성 'Screen'으로 위임 가능.
* 원하는 'Screen'에서만 특정 이벤트를 처리하도록 할수 있음.

### 1.2.3 MVP Arcitecture in TouchGFX
* TouchGFX Screen 컨셉
    * 'View', 'Presenter' Class에서 상속(Inheritance)된 Class에 의해    
      전체 MVP 아키텍처와 연결됨.
    * new 'Screen'을 추가할때 new 'View' , 'Presenter' Class를 모두 생성함.
* TouchGFX MVP Class의 구체적인 내용과 책임.
    * "Model"
        * 항상 alive하고 2가지 목적을 가진 Singleton class
        * Singleton class : 최초 한번만 생성하는 class    
          [Singleton class ???](https://jeong-pro.tistory.com/86)

    * 'View'
        * 데이터 ('Model'으로부터)를 표시하고 사용자 명령 (Event)을    
          'Presenter'에게 전달하여 해당 데이터에 대해 작동하는 수동 인터페이스.
    * 'Presenter'
        * 'Model'과'View'에 따라 행동.    
          리포지토리 ('Model')에서 데이터를 검색하고 'View'에 표시 할 형식을 지정.
