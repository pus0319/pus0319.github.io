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

#### 1.3.3.1 Example Setup
![image](https://user-images.githubusercontent.com/79636864/112957428-63b96a00-917c-11eb-814e-f562ef183b70.png)    

```
- screen : 'MyScreen'
- box widget : 'box1'
- button widget : 'button1'
- interaction : 'button1' Clicked -> 'setRandomColor()' 호출    
  (Call new Virtual Function)
```    

* User Code Layer에서 위의 interaction을 구현해야함.
#### 1.3.3.2 Code Layer 
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
     
#### 1.3.3.3 Code 내용
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

# 2. TouchGFX에서 하는 작업들
## 2.1 Using IDEs with TouchGFX
* 개발단계에서 CubeIDE를 사용 하면 TouchGFX Tool들과 쉽게 연동하여 사용할 수 있음.
* makefile 관련 내용이니 추후 참고
## 2.2 Widgets and Containers
* TouchGFX에서 app를 제작(빌드)하는 가장 기본적인 두가지 개념
    * Widgets
    * containers
* 두가지 모두 사용자 지정 구현 생성을 지원함    
  (TouchGFX와 함께 제공되는 사전 제작된 구성 요소를 모두포함하는)

### 2.2.1 Widgets
* TouchGFX,TouchGFX Designer는 개발자가 자유롭게 UI를 구축하는데 사용할 수 있는 표준 Widgets를 제공
    * ex : TextArea, Button, Box, TextureMapper등
* TouchGFX에서의 Widget
    * 단순히 Screen에 그릴 수 있고 interaction할 수 있는 무언가를 표현하는 추상적인 정의.
* TouchGFX Designer를 사용하면
    * 사용자가 원하는 Widget을 화면에 추가
    * 각 Widget의 고유 속성을 제공하여 원하는 방식으로 사용자 정의가 가능.
    * TouchGFX가 제공하는 다양한 유형의 Containers를 사용하여 Widgets를 그룹화 할 수 있음.
        * 'add (widget_instance_name)'함수를 사용하거나    
          컨테이너 추가 함수 (예 : 'myContainer.add (widget_instance_name)')를 사용하여    
          컨테이너에 추가하여 원하는 경우 사용자 코드에 위젯을 추가 할 수도 있음
* Widget을 추가하는 순서에 따라 z 순서 달라짐.    
  (어떤 Widget들이 다른 Widget들 뒤에 가려져야하는지를 결정하는 방법)
    * 마지막으로 추가 된 Widget이 Screen 맨 앞에 나타남(z-index가 높음)
* Widget의 좌표
    * Screen(root container)또는 해당 container의 노드에 상대적임.

### 2.2.2 Containers
* Widget , 기타 container와 같은 것들을 자식노드로써 포함할 수 있는 TouchGFX의 구성요소.
* 'Widget'탭의 'Container' 카테고리 아래에 있음.    
  Widget을 Container에 추가하는 것 : 트리보기의 Container로 Widget을 드래그하여 수행.

* 마지막으로 추가 된 노드가 화면 맨 앞에 나타남.(z-order가 높음)
* 'Widget'의 위치는 부모 노드인 'Container'로 정의 됨.\
    * 부모 노드인 'Container'의 위치를 변경하면 그에 따라 자식 노드들도 이동함.
* viewport역할도 함.
    * Containers의 외곽선 내의 부분만 표시됨.

## 2.3 Simulator
* UI 관련 시험 및 디버깅 시 빠른 처리 시간이 필요하다면    
  TouchGFX PC 시뮬레이터를 사용하는것도 좋은 방법임.
* 자체 개발보드(대상)에서 만큼 정확하게 UI의 전반적인 요소를 테스트할 수 있음.
* UI 시험 및 디버깅을 제외한 실제 성능 분석 및 실제 'Backend' System과의 interaction과 같은 작업은    
  자체 개발 보드에서 수행되어야함!
### 2.3.1 How To Run
### 2.3.1 Simulator Features(단축키)
### 2.3.1 Simulator Only User Code(User Code에서 Simulator만 실행하는 C++ 코드 생성)

## 2.4 Compiling & Flashing
### 2.4.1 Compiling for PC Simulator
### 2.4.2 Compiling for Target Hardware
* 현재, CubeIDE를 통해 할 수 있음.
### 2.4.3 Flashing STM32 Evaluation Kits
* ST Link Utility CubeProgrammer를 통해 가능.
    * 현재는 CubeIDE를 통해 하고 있음.
    * 추후 자체 개발 보드 양산 시, 참고해야할 내용임.

## 2.5 Debugging
* 다른 C++ application에서도 디버깅 가능.
### 2.5.1 Target Debugging
* 사용하는 IDE(CubeIDE)에서 제공하는 메커니즘을 사용하면 됨.
* Target(보드) : 애니메이션 속도, 업데이트 빈도 및 응답성과 같은 실제 성능 문제 등을 디버깅.
* Simulator : 그외 UI 특정 문제 등.    
  (애니메이션 이동, 전환, 요소 업데이트, 프로그램 로직 등)
### 2.5.2 Simulator Debugging
### 2.5.3 DebugPrinter
* DebugPrinter Class
    * Display에 디버그 메세지를 인쇄하는 가장 쉬운 방법    
      (Widget을 추가하지 않아도 됨)
    * ex : 'Backend' System 의 이벤트, FPS 또는 렌더링 시간 측정 값 등
* DebugPrinter를 사용하려면 먼저 인스턴스를 할당하고 Applicatino 클래스에 전달해야함.
    * 'FrontendApplication'의 생성자에서 수행 할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/112960794-b8121900-917f-11eb-8b6f-0dee36b115e0.png)    

* print는 ascii 32(space)에서 ascii 126('~')까지 사용 가능.
* 사용된 LCD Class와 일치하는 DebugPrinter Class를 사용해야함.
    * 'DebugPrinter Classes' 표 참고.

## 2.6 Examples
* 여러 사전에 제작된 예제가 개발자에게 제공함.
* 개발할려는 App을 구축하기 위한 기반으로도 사용할 수 있음.
* AT(Application Templete)과 결합되어 TouchGFX Application을 생성.

## 2.7 Keyboard Shortcuts(키보드 단축키)
* 추후 작업 진행시 따로 정리할 것.

# 3. TouchGFX Designer 사용자 가이드
중요한 내용만 따로 정리함.
## 3.1 Startup Window
## 3.2 File Menu
## 3.3 Main Window
## 3.4 Canvas View
## 3.5 Images View
## 3.6 Texts View
## 3.7 Config View
## 3.8 Interactions(상호작용)
* 'trigger'가 발생할때 수행할 action을 구성할 수 있음.
* interaction = 'trigger' and 'action'
    * "Trigger"
        * interaction(상호 작용)을 시작하는 것.
        * 'Action'이 발생하기 위해 애플리케이션에서 발생해야하는 것.
    * "Action"
        * 'Trigger'가 나오면 발생하는 것.
        * 'Action'에서 정의한 'Trigger'가 충족되었을 때, App에서 발생하는 작업을 결정할 수 있음.
### 3.8.1 Triggers
* 'Screen' 또는 사용자 정의 'Container'에 추가된 'Widget'에 따라 정할 수 있음.
* 비어있는 'Screen'에는 아래의 두가지 'Trigger'만 사용 가능.
    1. Hardware button is clicked
    2. Screen is entered
* 일부 'Trigger'는 'Container'내 구성요소('Widget')을 선택해야함.
### 3.8.2 Actions
* 'Screen' 또는 사용자 정의 'Container'에 추가된 'Widget'에 따라 정할 수 있음.
* 비어있는 'Screen'에는 아래의 4가지 'Action' 만 사용 가능.
    1. Call new virtual function(새로운 가상 함수 호출)
    2. Change Screen(Screen 전환,변경)
    3. Execute C++ code(C++ 코드 실행)
    4. Wait for(기다림)
* 'Widget'과 관련된 작업도 추가할 수 있음.
    1. Move widget(위젯 이동)
    2. Fade widget(위젯 페이드)
    3. Hide widget(위젯 숨기기)
    4. show widget(위젯 표시)
### 3.8.3 Chaining Interactions(상호작용 연결)
* 해당 Interaction은 해당 작업을 완료 할때마다 다른 interaction의 'Trigger'가 될 수 있음.
* 'Can trigger another interaction'을 활성화하고 'Trigger'하고 싶은 다른 interaction을 설정함.

# 4. TouchGFX Engine 기능
## 4.1 Custom Triggers and Actions
* Custom Triggers 와 Actions를 통한 고유한 interaction 구성요소를 정의할수 있음.
* 각 Screen에는 C++의 void method(actions)를 포함
* Custom Containers에는 App이 반응할 수 있는 C++의 callback(Trigger)이 포함.
### 4.1.1 Custom Triggers
* Custom Trigger는 Custom Container에서 생성됨.
* C++ callback 으로 생성.
#### 4.1.1.1 Adding Custom Triggers
* Custom Container의 properites 탭에서 수행.    
  TRIGGERS section의 우측의 더하기 파란색 버튼을 클릭.    
  
![image](https://user-images.githubusercontent.com/79636864/113105175-421fb780-923c-11eb-8019-a5299aa77ca8.png)    

![image](https://user-images.githubusercontent.com/79636864/113105217-4fd53d00-923c-11eb-99e7-5f4a2c815741.png)    

* name
    * void set'name'Callback()
    * virtual void emit'name'Callback()    
![image](https://user-images.githubusercontent.com/79636864/113105372-798e6400-923c-11eb-8910-b2a92f6ef6ef.png)    
![image](https://user-images.githubusercontent.com/79636864/113105483-9460d880-923c-11eb-9d18-dafd8700855e.png)    
* Discription(설명).
    * Interaction system에 사용.
    * interactino system에서 Trigger를 선택 시,    
      트리거 위로 마우스를 가져 가면 볼 수 있는 내용.    
      (설명이 지정되지 않은 경우, 표준 설명이 생성)    
* Type
    * type(자료형) 값을 대입할 수 있는 Trigger로 만들 수 있음.

#### 4.1.1.2 Emitting Custom Triggers from Interactions
Interaction에서 Custom Trigger 내보내기 구현.
* Interaction system을 사용하여 'Action'으로써 사용(Custom Trigger를 내보낼 수) 있음.
* Custom Triggers를 보유한 Custom Container에 이동하여 새로운 interactions를 만들고 Action을 Custom Triggers로 하면 됨.
* 아래 사진의 예시 : Custom Container내의 button1을 클릭할때마다 지정한 Custom Trigger가 발생함.    

![image](https://user-images.githubusercontent.com/79636864/113105965-2963d180-923d-11eb-93af-cf109f5b28f8.png)    

* value : type가 지정되어 있을 경우, 해당 Custom Trigger에 대입할 매개변수(parameter)를 지정해줘야함.    

![image](https://user-images.githubusercontent.com/79636864/113106044-439daf80-923d-11eb-9186-623e76aa2da9.png)    

#### 4.1.1.3 Emitting Custom Triggers from User Code
User Code에서 Custom Trigger 내보내기 구현.    
(Interaction에서 구현한 Custom Trigger emitting 부분 확인 해보기)
* CustomContainer에서 'trigger1'이라는 CustomTrigger에 대해 아래의 method를 생성함.
* 상속된 User Code Class에서 오버라이팅하거나 호출할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/113107024-5ebcef00-923e-11eb-8c1f-7a7c3f910fda.png)    

#### 4.1.1.4 Reacting to Custom Triggers from Interactions
Interaction에서 Custom Trigger 반응하기 구현.
* Custom Trigger가 있는 Custom Container가 Screen에 추가되면,    
  Custom Trigger를 Screen의 interaction에 대한 Trigger로 사용 가능.
* Screen의 interaction의 Trigger를 Custom Trigger로 사용 시,    
  이름 체계
```
<Custom Container Name> <Custom Trigger Name> happens
```    

![image](https://user-images.githubusercontent.com/79636864/113107290-b5c2c400-923e-11eb-87d7-8f9f341489e8.png)    


#### 4.1.1.5 Reacting to Custom Triggers from User Code
User Code에서 Custom Trigger 반응하기 구현.    
(Interaction에서 구현한 Custom Trigger reacting 부분 확인 해보기)
* Custom Trigger는 이름이 'trigger1'인 Custom Trigger를 가지고 있는    
  Custom Container가 Screen에 추가 된 후,    
  아래의 예시와 같이 Callback를 구현하여 User code에서 반응 할 수 있음.    
  
![image](https://user-images.githubusercontent.com/79636864/113107510-fd495000-923e-11eb-9c91-de06752c0c39.png)    
![image](https://user-images.githubusercontent.com/79636864/113107551-076b4e80-923f-11eb-9b68-af335fc6563d.png)    

### 4.1.2 Custom Actions
* Custom Action은 Screen 및 Custom Container에서 만들 수 있음.
* Virtual C++ method로 생성됨.
* User Code 또는 Interaction system에서 실행 가능.
* interaction system에서 구성하거나    
  User Code Class 파일에서 생성된 C++ method를 오버라이팅하여 구성할 수 있음.

#### 4.1.2.1 Adding Custom Actinos
* Screen 또는 Custom Container properites 탭에서    
  ACTION section의 오른쪽 더하기 파란색 버튼을 클릭하여 수행.

![image](https://user-images.githubusercontent.com/79636864/113107800-48fbf980-923f-11eb-88e5-45f75af20e73.png)    
![image](https://user-images.githubusercontent.com/79636864/113107815-4c8f8080-923f-11eb-8720-336a32995538.png)    

* Name
    * interaction system 및 생성된 Code 파일에서 추가 참조를 위해 사용.    

![image](https://user-images.githubusercontent.com/79636864/113107885-616c1400-923f-11eb-9e18-be66eaf5fa8a.png)    
![image](https://user-images.githubusercontent.com/79636864/113107910-68932200-923f-11eb-9868-d3370cd2a028.png)    

* Description
    * interaction system에서 사용됨.
    * interaction system의 'action'을 선택할 때 마우스를 올려놓을때 볼 수 있음.
    * 설명이 지정되지 않은 경우, 아래의 표준 설명이 생성됨.
```
Call <Name> on <Screen or Custom Container Name>
```    

* Type
    * Type(자료형) 값을 대입할 수 있는 Action으로 만들어줌.

#### 4.1.2.2 Calling Custom Action from Interactions
Interaction에서 Custom Action 호출
* interaction system의 'action'에서 Custom Action을 실행할 수 있음.
* Type이 지정되어있는 경우 대입하고 싶은 매개변수 값을 지정해야함.    

![image](https://user-images.githubusercontent.com/79636864/113108460-fa9b2a80-923f-11eb-9007-4e49babca6b1.png)    

#### 4.1.2.3 Calling Custom Action from User Code
User Code에서 Custom Action 호출
(Interaction에서 구현한 Custom Action Calling 부분 확인 해보기)    
* User Code에서 Custom Action을 직접 호출도 가능.    

![image](https://user-images.githubusercontent.com/79636864/113108734-4221b680-9240-11eb-9d04-3ab5fedcb960.png)    


#### 4.1.2.4 Adding Behaviour to Custom Actions from Interactions
Interaction에서 Custom Action을 Trigger로 사용하여 'Action'을 호출하기
* Custom Actions를 소유한 Screen 또는 Costom Container의 interaction 탭으로 이동하여    
  new interactino을 만들고 'Trigger'로 Custom Action을 선택하면 됨.
* new interaction의 'Action'은 Custom Action이 호출될때마다 실행됨.    

![image](https://user-images.githubusercontent.com/79636864/113109011-8b720600-9240-11eb-8459-f618ad0ec158.png)    


#### 4.1.2.5 Adding Behaviour to Custom Actions from User Code
User Code에서 Custom Action을 Trigger로 사용하여 'Action'을 호출하기
(Interaction에서 구현한 Custom Action을 Trigger로 사용하여 'Action'을 호출 부분 확인 해보기)
* Custom Action은 상속된 User Code Class의 Action을 오버라이딩하여 'Action'을 구현할수도 있음.    

![image](https://user-images.githubusercontent.com/79636864/113109179-b65c5a00-9240-11eb-8658-cfe7884d297b.png)    

## 4.2 Custom Containers
* app을 개발 시, TouchGFX에 포함된 표준 위젯세트 외의 위젯이 필요할 수 있음.
* 다른 기본 위젯을 포함하고 이러한 위젯의 시각적 모양과 동작을 결합한 개체(object)임.
* Custom container의 그리기성능은 매우 높음.
* 위젯의 footprint를 줄여야하는 경우가 있을 수 있음.
    * **Custom Widget** Section 참고
* Custom container에 다른 Custom container로 구성해서 만들수도 있음.
* Custom Container는 Custom Trigger(callback) 및 Custom Action(method)를 정의할 수 있음.
    * 자세한 내용은 **Custom Triggers and Actions** Section 참고.
### 4.2.1 In Code
User Code에서 Custom Container를 만드는 방법 정리.
* 추후 사용 시 정리할 예정.

## 4.3 Caching Bitmaps
* App에서 bitmap을 저장(또는 캐시)할 수 있는 전용 RAM 버퍼.    
* bitmap이 cache된경우 bitmap을 그릴 때 자동으로 RAM 캐시를 픽셀 소스로 사용.
* 사용하는 이유.
    1. RAM에서 데이터를 읽는 것이 플래시에서 읽는 것보다 빠르기 때문에.
    2. 내부 플래시에서 외부 RAM으로 캐싱하면 성능은 저하될 수 있지만 다른용도로 플래시를 사용할 수 있게 됨.
    3. TouchGFX는 SD카드, NAND와 같은 Memory Mapped Flash가 아닌 것은 직접 렌더링할 수 없음.
        * Bitmap caching을 이용하여 RAM에 Non-Memory Mapped Flash에 저장된    
          bitmap데이터 일부 또는 전체를 cache하는 메커니즘 제공.

### 4.3.1 Setup the Bitmap Cache
* Bitmap caching 을 사용하려면,
    1. TouchGFX에 bitmap cache를 구성
    2. 외부 저장소(SD카드 등)에서 데이터를 읽기 위한 'BlockCopy'함수에서    
       외부저장소의 data를 읽을 수 있도록 하드웨어 특정 구현을 해야함.
* 추후 사용 시 정리할 예정.

## 4.4 Custom Widgets
* Custom Container외에,    
  Framebuffer를 완전히 제어할 수 있는 Widget을 기본적으로 만들 수 있음.
### 4.4.1 Custom Widgets를 사용하는 경우
1. 갈색느낌이 나는 세피아톤 보정 시
2. 망델브로 프랙탈 위젯 ???
3. 파일 데이터를 표시하는 위젯.
    * gif 애니메이션, QR코드 등
* Custom Widgets에 대한 Code를 수동으로 작성 후 Widgets을 View의 User Code 부분에 삽입해야함.
    * TouchGFX Designer에선 지원하지 않음.

### 4.4.2 In Code
User Code에서 Custom Widgets를 만드는 방법 정리.
* 추후 사용 시 정리할 예정.

### 4.4.3 Modifying standard widgets/containers
* 표준 widgets , Container를 수정할 수는 있지만 권장하지 않음.

## 4.5 Canvas Widgets
* 캔버스 위젯 및 캔버스 위젯 랜더러(CWR)
    * 기하학적 모양의 그리기를 제공하는 기능.
* 기하학적 모양은 캔버스 위젯에 정의되어있음.
* Custom Canvas Widget 또한 만들 수 있음.
* 기하학적 모양의 그림 내부의 각 픽셀의 실제 색상은 연관된 Painter 클래스에 의해 정의됨.

### 4.5.1 Using CanvasWidgets
* 캔버스 위젯은 자동으로 결정되는 초기에 설정할 수 있는 기본 크기가 없음.
    * 위젯 위치, 크기도 올바르게 조정해야함.
* 따라서, 'setXY()'대신 'setPosition()'을 사용하여 배치하고 크기를 조정해야함.
* 캔버스 위젯의 위치, 크기 설정 되면    
  그 안에 기하학적 모양을 그릴 수 있음.
    * 위쪽상단에 (0,0)이있음.
    * X축은 오른쪽 증가
    * Y축은 아래쪽 증가
    * 자세한 내용은 **The CWR Coordinate System(CWR 좌표계) Section** 참고.
* TouchGFX Designer에서도 지원,    
  사용이 간단하고 자동 메모리 할당이 있음.
* TouchGFXDesigner에서 사용 가능한 Canvas Widget 기반 위젯:
    * Line
    * Circle
    * Shape
    * LineProgress
    * CicrleProgress
* TouchGFX Designer를 통해 이러한 위젯을 사용시,    
  위젯이 runtime에 어떻게 보여주는지 확인가능.    
  배치 및 크기 조정이 훨씬 쉬워짐.

### 4.5.2 Memory Allocation and Usage
* CWR은 특수 할당 메모리 버퍼가 있어야함.
* CWR은 동적(dynamic) 메모리 할당이 없음.


#### 4.5.2.1 Memory Allocation in TouchGFX Designer
* TouchGFXDesigner에서 Screen의 캔버스버퍼에 위젯을 추가하면 메모리 버퍼가 자동으로 생성됨.    
    * 공식 :(( Width x 3) x5 )
* 버퍼 크기를 재정의 할수도 있음.


#### 4.5.2.2 Memory Allocation in User Code
* User Code의 메모리 할당.


### 4.5.3 The CWR Coordinate System(CWR 좌표계)
![image](https://user-images.githubusercontent.com/79636864/113110903-9168e680-9242-11eb-8b2f-01fdcd94faa1.png)    

### 4.5.4 Custom Canvas Widgets
* 추후 사용 시 정리 할 예정

### 4.5.5 Painters
* 캔버스 위젯 개체를 채우기 위한 색 구성표를 사용자가 정의할 수 있음.

## 4.6 Dynamic Bitmaps
* runtime에 bitmap cache의 RAM에 bitmap을 생성하는 것.
* 추후 bitmap cache 사용 시 내용 정리 예정.

## 4.7 Binary Fonts
* 장치에 대한 다양한 글꼴 세트를 제공하기 위해 사용 가능.
* 단점은 전체 Binary Fonts를 RAM(또는 Memory Mapped Flash)에 로드해야함.
* 추후 타국어 지원 기능 구현 시 내용 정리 예정.

## 4.8 Binary Fonts
* Binary Font를 로드하기 위한 방법
* 추후 타국어 지원 기능 구현 시 내용 정리 예정.

## 4.9 Binary Translations
* 추후 타국어 지원 기능 구현 시 내용 정리 예정.

## 4.10 Backend Communication
* App에서 UI는 Backend System(HW peripherals로구성)(센서 데이터, A/D변환, 통신 등)와    
  인터페이스 하거나 다른 S/W모듈과 인터페이스 할수 있음.
    * 이를 구현하기 위한 권장 솔루션 제공

### 4.10.1 The Model Class
* Model Class는 UI 상태 정보를 저장하는 것 외에도    
  Backend System에 대한 인터페이스 역할을 함.
* 시스템의 OS Task들과도 통신 가능.
* 개별 View 클래스의 다른 S/W 모듈이나 HW에 액세스하는것은 좋지 않음.
* Model 클래스에 인터페이스 Code를 배치하는것이 적합한 이유
    1. 모든 Frame에서(1Frame마다) 자동으로 호출되는 'tick()'함수가 있음.    
        * 다른 하위 Module의 이벤트를 찾고 반응하도록 구현 가능.
    2. Model 클래스에 들어오는 이벤트를 UI에 알릴 수 있도록    
       현재 활성화된 Presenter에 대한 포인터가 있음.
* 더 자세한 내용은 **MVP Arcitecture in TouchGFX Section** 참고

### 4.10.2 System Interfacing : Sampling from GUI Task
* 주변 시스템의 샘플링해야하는 빈도, 소요시간 등에 대해 관대하다면    
  'Model::tick' method에서 직접 주변 시스템을 샘플링할 수 있음.
* 샘플링이 프레임속도보다 덜 자주 발생하는 경우,    
  특정 N번째 프레임에만 샘플링을 수행하도록 할 수 있음.
    * 단, 이때 샘플링 속도는 1ms이하로 다소빨라야함.(프레임 그리기가 지연됨)
 
### 4.10.3 System Interfacing : Sampling from Secondary Task
* 샘플링을 수행하는 새로운 OS Task를 만들 수 있음.
* 정확한 시간 간격으로 실행되도록 구성 가능.
    * GUI Task보다 Priority를 높거나 낮게 가질 수 있음.
* GUI Task보다 Priority를 높게 한 경우,
    * 지정한 시간에 정확히 실행함.    
      (단, UI의 프레임 속도에 영향을 미칠수있음)
* GUI Task보다 Priority를 낮게 하는 경우,
    * UI프레임속도가 주변 시스템의 샘플링에 영향을 받지 않도록 함.    
      GUI Task는 렌더링하는 동안 Sleep State로 많이 있으므로    
      대부분의 경우 priority를 낮게 가도 충분함.
* RTOS에서 제공하는 message system(message queue, mailbox 등)을 활용하는 것이 좋음
* 간단한 시나리오
    1. GUI Task에 보낼 UI관련 동작 내용이 담긴 message queue 나 mailbox를 만들고 보냄.
    2. 그런 다음 'Model::tick()'는 GUI Task의 message queue 또는 mailbox를    
       polling하여 새 data가 도착했는지 확인.
    3. 도착했을 경우, 데이터를 읽고 그에따른 UI를 Update 진행.

### 4.10.4 Propagating Data to UI(UI로 데이터 전파)
* 'Model::tick'은 GUI Task가 UI 작업 관련 data를 받을 수 있도록 하게 되는 method임

#### 4.10.4.1 Propagating Data to UI Example
* 온도 센서가 시스템에 부착, 현재 온도를 UI에 표시하는 간단한 예
1. Model Class를 보강함.    
    * Model Class에 'Backend' System에서 받은 Data를 저장할 변수 및 getter method 선언 및 정의
![image](https://user-images.githubusercontent.com/79636864/113112403-199bbb80-9244-11eb-895f-dd92018dc15f.png)    

2. 새로운 온도 정보가 수신 될 때 UI를 다시 Update하는 것을 해야함.    
   이를 위해 'Model'에 현재 활성 'Presenter'에 대한 포인터가 있음을 활용해야함.
    * 위의 포인터 유형은 적절한 App별 이벤트를 반영하도록 수정할 수 있는 인터페이스인 'ModelListener' 임.
    * 'Presenter'는 현재 온도에 대해 'Model'에 질문(접근)할 수 있으므로    
      'Presenter'는 온도를 표시하는 화면에 들어갈 때 UI('View')에서 이 값을 설정할 수 있음.    
      
![image](https://user-images.githubusercontent.com/79636864/113112754-82833380-9244-11eb-9fb7-8a60fc39e760.png)    

3. 들어오는 새로운 온도 이벤트를 'Model::tick'method에서 실제 샘플링을 수행하도록 Code를 작성함.    

![image](https://user-images.githubusercontent.com/79636864/113112819-95960380-9244-11eb-95f1-8a064b738849.png)    

* 'Model::tick'과 다른 OS Task와의 간접 연결방식은 아래의 2가지를 보장함.
    1. 'currentTemperature'변수는 항상 최신 상태이므로 'Presenter'는 언제든지 현재 온도를 확인할 수 있음.
    2. 'Presenter'는 바로 온도변화에 통보하고 적절한 조치를 취할 수 있음.
* 현재 사용중인 Screen에 따라 이벤트에 대한 별도의 알림 처리가 가능함.
    * 'MainMenuPresenter'가 활성화시,    
      'notifyTemperatureChanged'method는 empty function(내용이 없는 비어있는 함수)이므로 무시됨.
    * 반면에, 'TempatureControlPresenter'가 활성화 시,    
      'notifyTemperatureChanged'method는 해당 Class 내 재정의를 했으므로    
      method의 정의 내용과 같이 'View'에 온도를 표시해야한다고 알릴 수 있음.    
      
![image](https://user-images.githubusercontent.com/79636864/113112973-c2e2b180-9244-11eb-8e2e-b68a4b90de04.png)    

### 4.10.5 Transmitting Data from UI to Backend System
* UI에서 'Backend' System으로 데이터 및 이벤트 전송은 Model Class를 통해 할 수 있음.
    * Model Class에 전송 관련 method를 추가하여 구현가능.
### 4.10.5.1 Transmitting Data from UI to Backend System Example
* 사용자가 UI에서 새로운 목표 온도를 설정
* 'View'는 'Model'개체에 대한 포인터를 가지고 있는 'Presenter'에게    
  'setNewTargetTemperature'함수를 호출할 수 있음을 알림.    
  
![image](https://user-images.githubusercontent.com/79636864/113113434-469c9e00-9245-11eb-960b-46e033b1debf.png)    


### 4.10.6 Examples
* 특정 데모 보드(STM32 평가 키트)에 대해 구성된 전체 데모임.
* 자체 개발 하드웨어에 설명된 코드 대부분을 재사용할 수 있음.
#### 4.10.6.1 From GUI Task
#### 4.10.6.1 From Other Task
* Other Task(Backend)간 communication 및 UI와의 propagation을 보여주는 예제임.    
    * (STM32F746G-DISCO에서 실행됨)
    * C코드로 구현된 'Backend' System과 C++ TouchGFX GUI간 통신이 구현되어있음.
    * 실무하면서 예제 확인해볼 것.
#### 4.10.6.1 From Multiple tasks
#### 4.10.6.1 From Task and External Interrupt Line

## 4.11 Mixins
* Widget의 기능을 확장하는 Class.
    * ex : 움직임이나 alpha 값의 변화에 따른 애니메이션을 적용
* MoveAnimator와 Fade Animator Mixins는 interaction의 기초임.
* TouchGFXDesigner를 통해 또는 User Code에서 수동으로 Widget에 추가할 수 있음.
### 4.11.1 Move Animator
* Widget이 현재 위치에서 지정된 끝 위치로의 움직임을 애니메이션 할 수 있도록함.
* X,Y방향의 움직임은 EasingEquations를 통해 제공하여 설명 할 수 있음.
#### 4.11.1.1 Using Move Animator in User Code
* Widget에 Move Animator Mixin이 적용 된 경우,    
  이 새로운 기능을 사용하는 방법에 대해 정리
* 'startMoveAnimation' method를 사용할 수 있게 됨.
* 자세한 내용은 사용 시 정리
#### 4.11.1.2 Callback Implementation in User Code
* Move Animator mixin이 애니메이션을 완료 하면,    
  Callback함수가 생성됨.
* Callback을 구현하는 방법에 대한 데모 설명.
* 자세한 내용은 사용 시 정리
### 4.11.2 Fade Animator
#### 4.11.2.1 Using Fade Animator in User Code
#### 4.11.2.2 Callback Implementation in User Code
### 4.11.3 ClickListener
#### 4.11.3.1 Callback Implementation in User Code
### 4.11.4 Draggable

## 4.12 Texts and Fonts
* Text 및 글꼴 변환기 도구에 대한 내용과    
  TouchGFX App에서 생성된 Text를 사용하는방법 정리.
* TouchGFX Engine는 텍스트 메모리 소비를 최적화함.
* 추후 실무하면서 필요한 내용 정리 예정.
### 4.12.1 Texts and Typographies
* Text Database는 'assets/texts/texts.xlsx'에 저장됨.

### 4.12.2 Text Converter
* Text Database의 Text 정보를 TouchGFX app에서 사용하는    
  내부 C++ 형식으로 변환하는 도구
### 4.12.3 The Font Converter
* 글꼴 파일의 정보를 Text Database의 정보와 결합하고    
  app에 필요한 문자를 생성하는 도구
### 4.12.4 Wildcards
* 특정 형식을 작성하여 Runtime 동안 텍스트 값을 변환할 수 있음.
#### 4.12.4.1 Using Wildcards in TouchGFX Designer
* WILDCARD를 눌러 와일드카드를 편집할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/113831848-01d3b280-97c3-11eb-9eee-cb1506fd1216.png)    

* 초기값을 설정할 수도 있음.

![image](https://user-images.githubusercontent.com/79636864/113831928-1617af80-97c3-11eb-83ff-4cc253f2d6d7.png)    

#### 4.12.4.2 Using Wildcards in TouchGFX Designer
* 아래의 예제를 통해 와일드카드를 사용할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/113832080-38a9c880-97c3-11eb-9f59-622644174365.png)    

## 4.13 Languages and Characters
* 추후 실무하면서 필요한 내용 정리 예정.

# 5. UI 구성요소
아래의 내용들은 UI 구성요소(component들)임    
간략하게 종류만 정리하였고 자세한 내용은 관련 자료 및 문서 참고
## 5.1 Button
## 5.2 Image
## 5.3 Container
## 5.4 진행률 표시기(Progress Indi)
## 5.5 Shapes
## 5.6 Miscellaneous(여러 기타 요소들)
## 5.7 General UI Component Performance
일반 UI 구성요소 기능
# 6. TouchGFX Engine 관련 Scenarios
* TouchGFX Engine 사용 관련 Scenarios 정리.
* 추후 필요 시 정리 할 예정.
# 7. Development Scenarios
개발 실무 관련 주요 시나리오 정리.
* 추후 필요 시 정리 할 예정.
## 7.1 TouchGFX on Low Cost Hardware
저비용 하드웨어로 TouchGFX 사용하기
## 7.2 Lowering Memory Usage with Partial Framebuffer
부분 프레임 버퍼로 메모리 사용량 줄이기
## 7.3 Using Non-Memory Mapped Flash for Storing Images
이미지 저장을 위한 Non-Memory Mapped Flash(SDcard,NAND)사용
## 7.4 Using Serial Flash for images and fonts
이미지 및 글꼴에 Serial Flash 사용
## 7.5 Using Non-Memory Mapped Flash for Font Data
글꼴 데이터를 위한 Non-Memory Mapped Flash(SDcard,NAND)사용
## 7.6 Changing the Pixel Format of an Application
## 7.7 Creating an Application Template
## 7.8 External Events as Triggers
외부 이벤트(Backend System에서 발생하는)를 Interaction의 'Trigger'로 만들기
### 7.8.1 TouchGFX HAL Development
* 외부 이벤트(Backend system)을 읽을 수 있게 되면    
  ButtonController 인터페이스를 통해    
  렌더링 주기의 일부로 이 이벤트를 읽을 수 있음.
1. 아래의 예시와 같이 외부 이벤트(특정 GPIO Level이 조건)에 충족 시,    
   ButtonController의 key가 누름(true)으로 return하는 동작을 구현해야함.    
  
![image](https://user-images.githubusercontent.com/79636864/113116117-1e626e80-9248-11eb-9400-8502fe3c624f.png)    

2. TouchGFX Designer의 interaction에서 'ButtonController'에 의해    
   샘플링 된 값을 사용하려면 '.touchgfx' 프로젝트 파일에    
   name / value 매핑을 만들어야함.    

![image](https://user-images.githubusercontent.com/79636864/113116221-3639f280-9248-11eb-92ad-1a33417310f6.png)    

3. 이제 interaction을 만들때 "Hardware Button is clicked'를 사용할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/113116290-43ef7800-9248-11eb-831f-4948b35782ce.png)    


# 8. 정리
Development 내용은 바로 실무에 적용하는 내용과 그러하지 않은 내용이 섞여있어서    
모두 정리하진 않았습니다.    
이제 본격적으로 UI 실무를 해볼 수 있을 것 같습니다.
나머지 정리하지 않은 내용은 실무 작업을 통해 필요할 시    
정리할 예정입니다.



