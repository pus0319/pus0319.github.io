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
        * UI에 대한 상태 정보 저장.    
          ('View','Presenter' 는 Screen 전환 시 할당 해제됨)
        * 'Backend' System에 대한 인터페이스로 작동.    
          현재 활성 'Screen'으로 이벤트를 전달.
        * 현재 활성화된 'Presenter'를 가리키는 포인터를 갖도록 자동설정.    
          'Model'에서 변경사항이 발생시, 현재활성 'Presenter'에 변경사항 통보.    
          (ModelListener method에 의해 수행)
    * 'View'
        * TouchGFX 'View' Class에 파생된 클래스.
        * 멤버 object로 표시되는 Widgets가 포함.
        * 해당 Screen에 enter 또는 exit 할때 호출되는 'setupScreen', 'tearDownScreen' 함수도 있음.    
          ('setupScreen'함수에서 Widgets를 구성함.)
        * 연결된 'Presenter'에 대한 포인터 포함.    
          (프레임워크에 의해 자동 설정)    
          (ButtonClick 등의 위젯 UI 이벤트를 'Presenter'에게 전달할 수 있음.)
    * 'Presenter'
        * TouchGFX 'Presenter' Class에서 파생된 클래스
        * 현재 활성화된 'Screen'의 비즈니스 로직을 담당.
        * 'Model'에 서 'Backend' 이벤트를 수신,    
          'View'에서 UI 이벤트를 수신하고 취할 조치를 결정.
    
    
## 1.3 Code Structure(Code 구조)
* Generated Code : TouchGFX Designer가 생성한 코드
* User Code : 개발자가 작성한 확장 코드\

### 1.3.1 'Generated Code' vs 'User Code'
* Generated Code와 User Code는 완전 분리되어 있음.
    * Generated Code : 'generated/gui_generated' 폴더에 배치
        * 'generated/gui_generated' 폴더 내 코드는 User Code Class에 대한 기초 클래스임.
    * User Code : 'gui' 폴더에 배치
* TouchGFX Designer app의 3개의 서로 다른 코드 레이어(계층).    

![image](https://user-images.githubusercontent.com/79636864/112956782-c4947280-917b-11eb-80f5-4071ddbf7e7b.png)    

* 각 코드 레이어(계층)에 대한 설명
    * "Engine"
        * TouchGFX에서 제공하는 표준 Class(생성된 Class의 기본 Class로 작동)
    * 'Generated'
        * ToucGFX Designer가 생성하는 Class
        * 절대로 수동으로 편집해서는 안됨.
        * User Class의 기본 Class임.
    * 'User'
        * 개발자가 직접 작성한 코드를 위한 것.
        * 개발자는 이 Layer에서 코드를 자유롭게 넣을 수 있음.
        * TouchGFX Designer에의해 변경되지 않음.
        * 
### 1.3.2 Files Generated by TouchGFX Designer(TouchGFX Designer에서 생성된 파일들에 대한 설명)
* 일반적으로 TouchGFX Designer는 TouchGFX 프로젝트의 'generated'폴더 내에서만    
  파일을 재생성하거 덮어 쓰게되므로 수동으로 편집하지 않는 것이 중요함.
* 그러나, TouchGFX Designer는 컴파일에 필요한 누락된 파일을 생성할 수도있음.
    * 'application.config'
    * 'simulator/main.cpp'
    * 'assents/images/designer' 폴더에 있는 스킨 이미지


### 1.3.3 Exmaple(Code Structure를 설명하기 위한 예시)

### 1.3.3.1 Example Setup
![image](https://user-images.githubusercontent.com/79636864/112957428-63b96a00-917c-11eb-814e-f562ef183b70.png)    

```
- screen : 'MyScreen'
- box widget : 'box1'
- button widget : 'button1'
- interaction : 'button1' Clicked -> 'setRandomColor()' 호출    
  (Call new Virtual Function)
```    

* User Code Layer에서 위의 interaction을 구현해야함.
### 1.3.3.2 Code Layer 
![image](https://user-images.githubusercontent.com/79636864/112957962-ef32fb00-917c-11eb-8361-ee0fbdb8ebd9.png)    

* Generated Code Layer : 
    * 'MyScreenViewBase', 'FrontendApplicationBase','FrontendHeapBase'
    *  TouchGFX Designer에서 변경이 있을 때마다 다시 생성됨.
    *  개발자는 해당 Layer의 Class를 수동으로 편집하면 안됨.
    * 'box1'및 'button1'의 모든 설정은 생성 된 뷰 기본 클래스 'MyScreenViewBase'에서 수행됨.
    * 'Screen' 간 전환을 위한 모든 함수는 생성 된 애플리케이션 기본 클래스 'FrontendApplicationBase'에 있음
    * 적절한 양의 메모리가 할당되었는지 확인은 생성된 heap 기본 클래스 'FrontendHeapBase'에 있음

* User Code Layer
    * 'MyScreenView','MyScreenPresenter','FrontendApplication','Frontendheap'
    * TouchGFX Designer에서 변경한 경우에도 재생성되지 않음.
    * 개발자는 User Code Layer의 Class에서 코드를 자유롭게 추가할 수 있음.
    * 개발자는 User Code Layer의 Class를 자유롭게 편집 가능.    
      이번 예시는 'box1'의 색상을 실제로 변경하기 위한 'setRandonColor'함수를 구현함.
     
### 1.3.3.3 Code 내용
![image](https://user-images.githubusercontent.com/79636864/112958338-5781dc80-917d-11eb-8a56-b73fe7ffdf45.png)    

* ...ViewBase.cpp를 보면 TouchGFX Designer에서 생성한    
  Box widget과 Button Widget의 설정을 볼 수 있음.
* 'buttonCallbackhandler' method 에서 Virtual 함수로 지정된    
  'setRandomColor'의 설정 및 호출을 볼 수 있지만    
  현재, 해당 함수는 아무 작업도 수행하지 않음.    
  
![image](https://user-images.githubusercontent.com/79636864/112958546-8b5d0200-917d-11eb-821b-1cc91de874f7.png)    
* ...ViewBase.hpp를 보면, Virtual 함수로써 'setRandomColor'선언 하였음.

![image](https://user-images.githubusercontent.com/79636864/112958676-adef1b00-917d-11eb-8b8b-4e2a243757b2.png)    
![image](https://user-images.githubusercontent.com/79636864/112958718-b8a9b000-917d-11eb-9746-cc9d7cbf8f41.png)    

* Virtual 함수인 'setRandomColor'함수를    
  기본 클래스(MyScreenViewBase)로부터 상속받은 파생클래스(MyScreenView)에서    
  재정의(오버라이딩)해야함.    
  (MyScreenView.hpp에서 수행)
* 파생 클래스(MyScreenView)에서 'setRandomColor'함수를 정의하고 동작을 구현.

* 실행하면 원하는 동작을 함을 확인.

