---
date: 2021-03-29
title: "STM32F746_TouchGFX_FreeRTOS_4_TouchGFX Development"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_4_TouchGFX Development
=============

TouchGFX 개발을 시작하기전에 어떠한 과정이 있는지에 대한 큰그림과    

그 과정에서 내가 해야되는/알아야되는 구체적인 내용을 정리하기 위해    

아래의 홈페이지의 'Development'에 대한 내용을 정리하였습니다.

[TouchGFX_support](https://support.touchgfx.com/docs/basic-concepts/embedded-graphics)

# 1. Main Activites
* 자체 개발 보드를 기반으로 개발 하는 경우, 아래의 활동들을 하게 됨.   
  
(Main Activites)
![image](https://user-images.githubusercontent.com/79636864/112817366-512c2b80-90bd-11eb-830d-80f62d0d7f87.png)   

* 위에서 설명한 각 activities는 아래와 같은 Components를 생성함.   
![image](https://user-images.githubusercontent.com/79636864/112817502-791b8f00-90bd-11eb-94e2-d732b4ef968b.png)


```
TouchGFX 엔진은 활동을 통한 Component가 아님. TouchGFX 프로젝트 구성시 바로 사용 가능.
```   
 
## 1.1 Hardware Selection
### 1.1.1 Prototyping
* UI 프로토타입 제작은 STM32 평가 키트를 이용하면 신속하게 시작가능.
### 1.1.2 Custom Hardware
* 각종 고려해야할 사항이 많음. Section **Hardware Selection**을 참고.
* 자체 개발 보드와 유사한 STM32 평가 키트를 선택하고 UI 개발의 첫번째 단계에서 사용하는 것이 일반적.    

## 1.2 Board Bring Up
### 1.2.1 CubeMX
### 1.2.2 Application Templates (ATs)
* STM32 평가 키트 중 하나에 대한 기존의 Application Templates(AT)를 기반으로 프로젝트 시작 가능.
* CubeMX 구성을 기반으로 함. 다른 peripherals에 대한 추가 및 수정 가능.   

## 1.3 TouchGFX AL Development
* 개발 보드(Display+Board init Code) 위에서 TouchGFX엔진을 실행하는데 중요한 요소.
* H/W를 추상화하고 TouchGFX 엔진이 보드에서 실행되도록 하는 소프트웨어 계층.(S/W Layer)
### 1.3.1 TouchGFX Generator
* 직접 Code에 TouchGFX AL의 일부를 작성할 것임.
* 구현할 빈 함수(Function)을 생성하여 AL Development 단계에서 도움을 줌.
* TouchGFX AL이 작동하려면 보드 초기화 코드가 올바르게 수행되고    
  MCU, 외부 RAM, 디스플레이 등이 올바르게 구성되어 있는지 확인하는 것이 중요함.
* TouchGFX Designer에서 사용할 수 있는 표준 STM32 평가 키드 중 하나에 대한    
  Applicatino Template(AT)를 기반으로 제작 가능.
* AT에는 각 구성 요소에 필요하나 모든 TouchGFX AL 코드가 포함되어있음.
* AL 개발에 대한 내용은 다음 Section **TouchGFX AL Development**을 참고

## 1.4 UI Development
* 대부분의 프로젝트 개발 시간을 보내는 곳.
* 사용자가 실재로 보고 사용하는 부분을 구성할 UI코드를 생성함.
### 1.4.1 TouchGFX Designer
* TouchGFX Designer, IDE, text편집기 등을 사용할 것임.
* TouchGFX Designer에서 Application의 Screen을 설정, 디자인 및 생성하고    
  UI Application 의 주요부분을 C++ 코드로 생성.
* App 로직(이벤트 처리, 시스템의 UI가 아닌 부분과의 통신)의 경우    
  IDE 또는 text편집기를 사용하여 TouchGFX Designer에서 생성된 code와    
  공존하고 interaction(상호작용)하는 C++ code를 작성.
### 1.4.2 Application Templates
* 프로토타입을 작성 중이거나 빠르게 하고 싶다면    
  PC 기반 TouchGFX  Simulator를 기반으로 App를 만들거나    
  표준 STM32 평가 키트를 이용한 AT를 사용할 수 있음.
### 1.4.3 UI templates
* TouchGFX 데모 또는 예제 중 하나를 선택하여 UI 에 대한 영감을 얻을 수 있을 것임.
### 1.4.4 Custom Hardware
* 자체 개발 보드와 예제의 해상도(Resolution)이 일치하면 자체 개발 보드에서도 실행되어야함.
* 이 단계에의 전체 설명은 **UI Development** Section 참고

## 1.5 UI Workflow

![image](https://user-images.githubusercontent.com/79636864/112818496-8f761a80-90be-11eb-9b10-0e48994431c1.png)    

### 1.5.1 Generated Code and User Code
* Board Bring Up, ToucGFX AL Del, UI Del의 세가지 소프트웨어 활동 각각에서 코드를 생성하는 도구를 사용.
* 필요한 모든 코드만 생성하지 않고 사용자가 작성한 코드도 프로젝트에 추가 가능.
* 코드 작성과 도구 사용 사이를 모두 오갈 수 있음.
* 생성된 코드와 사용자 코드는 독립적이며 별도로 업데이트 가능.

### 1.5.2 Change of Compiler/IDE

# 2. Hardware Selection Introduction
![image](https://user-images.githubusercontent.com/79636864/112818770-d6641000-90be-11eb-97b5-320aa04d414c.png)    
MCU, Display(LCD Panel), 외부 메모리, UI 성능 등에 대한 고려사항 정리.
## 2.1 Preliminary Considerations(예비 고려사항)
### 2.1.1 Display Resolution(LCD 패널 해상도)
* STM32 MCU는 일반적으로 16/24bpp에서 최대 XGA 해상도(1024x768)를 지원하고    
  Wide 또는 CircleDisplay와 같은 비표준 해상도도 지원.
* 체크사항
    * 최종 사용자 대상 세그먼트는 무엇입니까?
    * 응용 프로그램에서 작은 텍스트를 많이 사용할 것입니까?
    * 일반적으로 한 화면에 여러 가지 요소를 한 번에 표시 할 예정입니까?
### 2.1.2 Color Depth(bpp)
* 24bpp Display에서 16bpp GUI : OK    
  16bpp Display에서 24bpp GUI : ???
* Color Depth가 높을 수록 필요한 메모리 양이 많아짐.
* 많은 현대 UI 디자인 철학이 평평하고 덜 색상 집약적인 애플리케이션    
  (예 : Google의 머티리얼 디자인)을 중심으로 진행
* 체크사항
    * 실제 이미지를 표시해야합니까?
    * 그레이 스케일 색상 또는 단순한 6/8 bpp가    
      응용 프로그램에 필요한 것을 전달하는 데 정말로 필요한 전부입니까?
    * RAM 및 / 또는 플래시에 제한이 있습니까?
### 2.1.3 Framebuffer Size Calculation(프레임 버퍼 상태 계산)
* Framebuffer Size Calculation    
  : Display width * Display height * (bits per pixel / 8)
* Double Framebuffer 일경우, 위의 공식에서 x2를 해야함.

### 2.1.4 Display
#### 2.1.4.1 Interface
* SPI, LTDC, MIPI-DSI 등의 인터페이스를 사용하는 LCD 패널을 선택 가능.

#### 2.1.4.2 Size
* LCD 패널의 정보가 1-2m 떨어진 곳에서 표시되는 경우, 텍스트, 아이콘 등은 충분히 커야함.

#### 2.1.4.3 Touch
* Capacitive
    * 저항성보다 훨씬 더 높은 터치 감도를 가짐.    
      Drag, Swipe 등의 고급 터치 작업을 필요 시 중요함.    
      현재 가장 많이 사용.
    * 저항성보다 비쌈.    
      장갑을 끼고 작동할 수 없음.
* Resistive
    * 용량성보다 훨씬 더 민감.    
      Display 화면에서 햇빛에 대한 가시성이 좋지 않음.    
      외부 노이즈 등의 의도하지 않은 interaction에 덜 민감하고    
      장갑을 끼고도 작동할 수 있음.
    * 모든 터치 작업에서 Drag, Swipe를 제외한 Click 동작만    
      구현해도된다면 저항성 디스플레이로도 충분함.    
      
### 2.1.5 Animations
복잡한 애니메이션(전체 화면 전환, 회전 및 크기 조정 등) 을 실행하면    
HW 처리량 및 계산 능력이 충분하지 않은 경우 성능에 상당한 영향을 미칠 수 있음.    
* 체크사항
    * 고속 전체 화면 전환이 필요하십니까?
    * 회전 및 크기 조정과 같은 복잡한 텍스처 매퍼 애니메이션이 필요합니까?

### 2.1.6 Mechanical Design Requirements(기구 설계 요구사항)
* 체크사항
    * 작아야되나?
    * 온도에 민감한가?
    * 물 등의 실외 환경 요인에 매우 잘 견여야하는가?
    * 강한 햇빛에서 가시성이 중요한가?

### 2.1.7 Frames Per Second
* FPS가 높을 수록 App가 훨씬 더 매끄러워 보임.
* GUI의 전반적인 성능
    1. MCU, RAM, 플래시, 디스플레이, 인터페이스 처리량
    2. 그래픽 가속기(Chrom-ART)기능을 고려.
* Chrom-ART를 통해 MCU를 오프로딩하므로    
  경우에 따라선 높은 MCU Frequency의 중요성을 감소 시킬 수 있음.
  
![image](https://user-images.githubusercontent.com/79636864/112820372-825a2b00-90c0-11eb-9dc0-bd3f1a42945a.png)    

## 2.2 MCU
* 그래픽용 MCU를 선택 시,
    1. 지원되는 디스플레이 I/F
    2. MCU 패키지
    3. 디스플레이 크기 및 구현 가능한 그래픽 성을 고려해야함.
* 이미지 구성
    * MCU에 통합된 그래픽 가속기의 가용성.
    * 캐시 메모리의 가용성.
* 메모리 액세스 및 대역폭(Access and bandwidth)
    * clock frequency(HCLK) 및 subsystem bus frequency(AHB,APB 등)
    * 내부 플래시 및 내부 RAM 메모리에 대한 접근.
* 그래픽 외에도 app의 다른 측면(모터 제어, 무선 통신)등을 고려해야함.
### 2.2.1 Frequency
* 주파수는 그래픽 애플리케이션 성능에 큰 영향(Screen 새로고침, 화면 및 애니메이션 유동성 등)
* 내부 또는 외부 메모리에서 디스플레이 FrameBuffer로 전송할 수 있는    
  데이터 양과 가능한 계산의 성능에도 영향.
* 높을 수록, 더 많은 데이터 및 더 복잡한 애니메이션 구현 가능.
* STM32제품은 최대 480MHz까지 지원.
* 주파수가 높을 수록 전력 소비가 커짐.
#### 2.2.1.1 Graphic Subsystem Frequency
* 그래픽 하위 시스템 주파수(LCLK)와 CPU 주파수를 구별하는 것이 중요.
* 그래픽 서브 시스템 주파수
    1. 내부 버스의 주파수(AXI)
    2. 그래픽 가속기(Chrom-ART)의 주파수
    3. 내부 및 외부 메모리의 액세스 속도
* 예시
    1. CPU Core : 480MHz.
    2. 64-bit AXI bus Frep : 240MHz
    3. LTDC는 64-bit AXI를 사용하고, 10cycle 동안 8번 transfer 가능
    4. 내부 RAM은 no latency. 0 waite states.
    5. Bandwidth(대역폭) = 240Mhz * 8/10 * 8bytes = 1.536Gbytes/s.    
       (1초당 1.536Gbytes를 transfer가능함.)
    6. 이는, 800x480 32bpp 해상도의 LCD를 I/F할 시, 1000FPS를 보장이 가능함.
       (1.536 * 10^9 / (800 * 480 * 32 / 8) = 1000)

### 2.2.2 Embedded Hardware Acceleration Features
### 2.2.2.1 Chrom-ART
* 그래픽 작업을 수행하는데 도움이 되는 고급 DMA.(DMA2D)
* CPU부하없이 이미지를 조작하고 전송 가능.
* 색상 채우기, 이미지 복사, 혼합 및 픽셀 형식 변환 과같은 대부분의 그래픽 작업.
* Chrom-ART 활성화시, CPU 부하가 82%->4%로 감소하는 예시(STM32F496-EVAL)
### 2.2.2.2 JPEG Hardware Codec
* 사용 예정인 STM32F7에는 인코딩 및 디코딩 이미지와 동영상을 위한 하드웨어 JPEG 코덱을 제공.
* 비디오 파일이나 JPEG 이미지를 Display 시 사용.
* MJPEG를 playing 동안 CPU를 offloading 할 수 있음.    
  (CPU를 사용하지 않고도 처리 가능)
### 2.2.2.3 Chrom-GRC
* 직사각형이 아닌 디스플레이를 처리 시, 프레임 버퍼를 저장하는데 필요한 RAM양을 줄일 수 있음.

### 2.2.3 Internal Flash
* bitmap resource를 사용하는 GUI의 경우,    
  데이터를 저장하기 위해 비휘발성 메모리가 필요함.    
  내부 플래시는 경우에 따라 외부 플래시보다 2배더 빠름.
* 내부플래시 : TouchGFX Framework, Screen definition, UI 로직 등을 저장    
  외부 플래시 : bitmap data를 저장
* TouchGFX 플래시 메모리 요구사항.
    * Framework : 60kbytes to 100kbytes
    * Screen definition and GUI logic : 1 to 100kbytes.

### 2.2.4 Internal RAM
* TouchGFX RAM 요구사항
    * Framework: 10Kbytes to 30Kbytes
    * Widgets: 1Kbytes to 15Kbytes(App에 따라 달라짐)

### 2.2.5 LCD Controller
* 사용될 디스플레이의 I/F와 해상도에 따라 달라짐.
* RTG-TFT 및 MPI-DSI는 고해상도에 자주사용
* SPI 또는 8080/6800은 종종 컨트롤러와 GRAM이 내장되어 있을 시 사용 가능.
* 고해상도 디스플레이는 종종 컨트롤러와 GRAM을 내장하지 않으므로 MCU에서 컨트롤러가 있어야함.    

![image](https://user-images.githubusercontent.com/79636864/112822522-06adad80-90c3-11eb-9b68-200888232c76.png)    

### 2.2.6 Packages & I/O
### 2.2.7 Memory Interfacing
* 적합한 외부 메모리 인터페이스(FMC/FSMC/ 그외 I/O)를 갖춘 올바른 MCU를 선택하는 것이 중요.
#### 2.2.7.1 FMC/FSMC
* SDRAM도 지원함
* 각 Memory Bank에 대해 독립적인 Chip Select가 있음.
    * 여러 종류의 FMC를 I/F 할 수 있음.
* 고속 I/F는 Memory Mapping 모드에서 최대 256MBytes, 간접 모드에서 최대 4GBytes까지 제어 가능.
* 저렴한 외부 플래시 메모리를 소형 패키지에 연결 할 수 있고 사용 핀수를 줄일 수 있음.

### 2.2.8 Cortex-M Cores
#### 2.2.8.1 Cortex®-M0+
* 간단한 구조 와 저렴한 가격.    
  낮은 해상도의 정적 GUI 사용 시
#### 2.2.8.2 Cortex®-M4
* M0+에서 가속화 계산이 포함.    
  DSP 명령어 세트 및 FPU장치가 포함.
#### 2.2.8.3 Cortex®-M7
* 더 복잡한 구조. DSP 명령어 세트 포함.    
  FPU 유닛, 최대 레벨 1 캐시 메모리(16KBytes)
#### 2.2.8.4 Level 1 cache
* 반복적으로 사용되는 CPU의 데이터 및 명령을 저장하여 속도를 높임.

### 2.2.9 Bus architecture
* 모든 마스터(CPU, DMA 등)과 슬레이브(flash, RAM, FSMC, AHB 및 APB 주변장치)를    
  상호 연결하는 32비트 다중 AHB 버스 매트릭스를 제공.

## 2.3 Display
* 임베디드 GUI 제품에 적합한 디스플레이를 선택 시 포함되어야하는 몇가지 고려사항에 중점.
### 2.3.1 Display 종류
#### 2.3.1.1 LCD-TFT
* 박막 트랜지스터를 의미. 활성 매트릭스가 있는 LCD 디스플레이의 변형.
* 가장 많이 쓰는 LCD.
#### 2.3.1.2 MIP
* 픽셀 단위의 메모리.    
  화면에서 무언가 변경 될때만 전력/데이터가 필요한 픽셀 기술을 사용.    
  저전력, 풀 컬러GUI로 실행.
#### 2.3.1.3 ePaper/eInk
* 저 색상 디스플레이.    
  전력 소비가 적고 시야각이 넓고 가독성이 쉬은 Appl에 이상적.
### 2.3.2 Display Interface Overview
* SPI
* Parallel 8080/6800(FMC
* RGB-TFT(LTDC)
* MIPI-DSI(LTDC)
* LVDS
#### 2.3.2.1 Brightness and Backlight
* 밝기 및 백라이트
* 단위 : cad/m^2
* 디스플레이에서 전력을 가장 많이 소비하는 부분.

