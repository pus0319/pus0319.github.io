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
#### 2.3.2.2 Viewing Position and Color Inversion
* 사용자가 Display를 보는 방향을 고려해야함.(LCD 의 시야각) 
* 시야각이 낮은 방향으로 볼 시, 색상 반전 및 흐릿함
* SWV 필름을 추가하면 시야각을 늘리는데 도움이 될 수 있음.
#### 2.3.2.3 Display Lifetime
* 기준 : 25도에서 절반 밝기에 도달할때가지의 시간
#### 2.3.2.4 Pixel density
* 인치 또는 평방 인치당 표시되는 픽셀 수.
* PPI^2 : pixels per square inch
#### 2.3.2.5 Dynamic color range
* 대조되는 두색상(검/흰)간의 비율
#### 2.3.2.6 Anti-aliasing
* 픽셀 밀도가 너무 낮으면 계단효과가 나타날 수 있음.    
  안티 앨리어싱은 이미지의 계단현상을 매끄럽게 만들 수 있음.

### 2.3.3 Touch / Non-touch displays
* Single finger touch만 가능.
* TouchGFX 에서는 multi touch 지원 하지 않음.
* 저항성 터치는 기계적 압력에 의해 활성화되는 간단한 기술이며 ADC 또는 간단한 터치 컨트롤러 만 필요함.

### 2.3.4 Displays with RAM
* 일부 LCD에는 1개의 전체 FrameBuffer 크기를 가진 RAM(GRAM)이 포함됨.    
  SPI,FMC 또는 LTD(DSI-host를 통해 MCU와 연결 가능.
  
### 2.3.5 Non-square pixels / Pixel aspect ratio
* 정사각형이 아닌 픽셀은 이미지가 늘어나거나 줄어들 수 있음.

## 2.4 External Memories
* FrameBuffer, bitmap, Font, traslations(번역) 등을 저장하기 위해 외부 메모리가 필요함.    
![image](https://user-images.githubusercontent.com/79636864/112824856-0367f100-90c6-11eb-9fde-9a938323cdab.png)    

### 2.4.1 Non-volatile Memories(비 휘발성 메모리)
* 비트 맵, 글꼴, 번역 및 TouchGFX 애플리케이션 코드와 같은    
  일부 또는 모든 그래픽 데이터 자산을 저장하는 데 사용    
  
![image](https://user-images.githubusercontent.com/79636864/112824946-22668300-90c6-11eb-9d7e-17689d60ee4d.png)    

#### 2.4.1.1 NOR Flash
* 메모리의 모든 Area에 대한 임의 액세스를 허용하는 비 휘발성 메모리.
* ranges : 128Mbits to 2Gbits
* 메모리 매핑 모드에서 사용 가능.
* Serial NOR Flash Memories(Single, Dual, Quad, Octo)
    * 데이터 라인이 많을 수록 성능과 bandwidth가 상승.    
      단, 인터페이싱을 하는데 더 많은 핀이 필요.    

![image](https://user-images.githubusercontent.com/79636864/112825152-6194d400-90c6-11eb-84af-52ffc14dec4f.png)    

* Parallel NOR Flash Memories
    * 성능 및 구성 측면에서 직렬 플래시 메모리와 동일한 장점. 
    * 메모리 매핑 모드에서 구성 가능
    * 핀수와 PCB 의 복잡성에서 차이가 있음.

#### 2.4.1.2 NAND Flash Memories
* 많은 양의 그래픽 자산과 빠른 쓰기 및 지우기 작업이 필요한 그래픽 App에 적합.    
  NAND Flash 안에서 코드 실행은 권장하지 않음.\
* ranges : 1Gbits to 512Gbits

![image](https://user-images.githubusercontent.com/79636864/112825338-99038080-90c6-11eb-9dd1-af829cbfb6ca.png)    

#### 2.4.1.3 eMMC Memories
* NAND 플래시와 동일함.
* eMMC는 플래시 메모리에 대한 표준 인터페이스 및 관리를 제공하는 컨트롤러를 패키지에 포함함.
* ranges : 2Gbits to 128Gbits
* 다른 메모리에 비해 랜덤 읽기 성능이 상대적으로 낮음.
    * 캐시를 추가해야할 수 있음.

![image](https://user-images.githubusercontent.com/79636864/112825477-bfc1b700-90c6-11eb-8f40-cd5512483a5e.png)    


### 2.4.2 volatile Memories(휘발성 메모리)
* 내부 RAM이 부족한 경우, FrameBuffer를 저장하는데 사용됨.    
![image](https://user-images.githubusercontent.com/79636864/112826581-2693a000-90c8-11eb-9cc5-9536730e75be.png)    

#### 2.4.2.1 SRAM
* 전원이 공급되는 동안 bit data를 유지하는 static random Access memory.
* 더 빠른 액세스를 지원하지만 DRAM보다 비싸고 밀도(지원용량)이 더 낮음.
#### 2.4.2.2 SDRAM
* dynamic random-access memory.
* 각 bit data를 커패시터에 저장하므로    
  SRAM에 비해 동일한 양의 데이터를 저장하는데 필요한 물리적 공간이 적음.
* 데이터를 유지하기 위해 지속적인 Refresh가 필요함.(SRAM에 비해 더 많은 전력이 필요)
* ranges : 16Mbits up to 512Mbits
#### 2.4.2.3 PSRAM
* SRAM인터페이스가 있는 DRAM(제어로직)내의 내부 구조를 가진 의사 static ramdom-access memory.
* ranges : 8-256Mbits
* 기존 SDRAM, SRAM에 비해 속도가 빠르고 전력 소비가 낮음.
### 2.4.3 Selection of External RAM Density
* Framebuffer를 외부 RAM에 배치 시,    
  아래의 표는 시장에서 사용 가능한 다양한 RAM밀도에 대한 개요.

![image](https://user-images.githubusercontent.com/79636864/112826934-a15cbb00-90c8-11eb-8075-5aeabd5a05f3.png)    

## 2.5 Board Bring Up
* 새로운 플랫폼에서 TouchGFX 프로그래밍을 시작하는 단계를 보드를 통해 안내.
* Bringing up : 보드의 모든 필수 부품과 해당 드라이버가 올바르게 작동하는지 확인하는 것을 의미.
* 보드의 구성 요소 및 주변장치, 이러한 모든 장치 간의 연결,    
  통신하는 프로토콜 및 각각에 대해 사용 가능하거나 필요한 드라이버 코드를 철저히 이해해야함.

### 2.5.1 Tools of the trade
* STM32CubeMX
* STM32CubeFirmware Pakage
* Vendor datsheets
* Vendor driver code : 시간을 절약하려면 공급 업체에 외부 장치에 대한 예제 코드를 요청해야함.
* TouchGFX와의 직접적인 연관은 없음(종속되지 않음)
    * 단, TouchGFX AL개발을 위한 견고한 기반이 됨.
* H/W 및 Low level S/W가 실제 최종 app에 대한 기대에 따라 작동하는지 확인하는 것.

### 2.5.2 Verification of Functionality(기능 확인)
* Abstraction Layer
    * TouchGFX AL 및 궁극적으로 작동하는 UI App을 빌드하는 기반.
* Test code
    * 보드가 예상대로 작동하지 않는 경우 이동하는 궁극적인 영역.
* 검증 프로그램을 체계적으로 저장하는 것이 매우 유용.

### 2.5.3 Overall Process(전체 프로세스)
#### 2.5.3.1 Create Project(프로젝트 생성)
* CubeMX를 사용하여 특정 MCU에 대한 작업 프로젝트를 생성
* 목표 : CubeMX에서 보드로 플래싱하고 실행할 수있는 프로젝트를 만드는 것.
* CubeMX로 생성 또는 TouchGFX Designer로 생성.(Designer로 생성하는 것이 더 현명함)
* User Code sections
    * 'USER CODE' Section 외부에서 작성한 코드는 CubeMX에 의해 삭제됨.
#### 2.5.3.2 CPU Running(CPU 실행)
* MCU 코어, 내부 RAM 및 플래시가 원하는 클럭 속도로 실행되고 있는지 확인.
* 목표 : 올바른 클럭 구성을 얻기 위해 프로젝트를 수정.
* System Clock
    * HSI,HSE
    * HCLK 값 확인.
    * SystemTimer 설정
* systemtimer를 이용하여 내부 Flash 및 내부RAM 읽기 속도 측정.
* Linker script
    * 링커에게 시스템의 RAM 주소와 Flash 주소를 알려줌.
    * 프로젝트의 필요에 맞게 수정해야함.

* Cache on F7 and H7
    * 테스트 중에는 데이터 캐시를 비활성화 하는 것이 좋음.
    * 프레임 버퍼의 데이터가 메모리가 아닌 캐시에만 기록이 되어서    
      일부 데이터가 디스플레이에 표시되지 않는 상황이 있을 수 있음.
#### 2.5.3.3 Framebuffer in internal RAM(내부 RAM의 Framebuffer)
* 내부 RAM을 사용하여 Display로 Pixel Data를 전송하는 방법.
* Parallel RGB Displays(또는 SPI Display)
* Setting the Framebuffer Address
* Check the Framerate
    * Framerate를 측정하여 성능 평가(HAL_GetTick() 이용)
* Checking the Display Colors
#### 2.5.3.4 External RAM(외부 RAM)
* 외부 SDRAM을 활성화.
* 목표 : 외부 RAM을 활성화 하고 여기에 Data를 읽기/쓰기 하기
* Further configuration
    * 일부 RAM 칩의 경우 추가 장치 별 구성을 수행해야함.
* Testing the RAM
    * RAM TEST는 디버거에서 볼 수 있음. (IDE Debugger의 memory viewer)
* 외부 RAM에 특정 데이터를 쓰는 테스트를 함.
* 외부 RAM의 읽기,쓰기,수정 속도를 테스트.
#### 2.5.3.5 Framebuffer in external RAM(외부 RAM의 Framebuffer)
* 프레임 버퍼를 내부 RAM에서 외부 RAM으로 이동,    
  여전히 Display로 전송이 잘되는지 확인.
* 내부 RAM에서 프레임 버퍼 배열을 제거,    
  외부 RAM에서 프로엠 버퍼를 사용하는 것.
* 프레임 버퍼에 대한 특정 주소를 외부 RAM에 맞게 변경.
#### 2.5.3.6 External addressable flash(외부 Flash, Memory Mapped Flash)
* Memory Mapping 모드에서 외부 Quad 또는 octo SPI Flash를 활성화함.
* 목표 : 외부 Flash를 활성화. Memory Mapping 모드로 변경.    
  데이터 읽기. 데이터 읽기 속도 테스트.
* 플래시를 활성화 후 데이터를 읽어 테스트 할 수 있는 모드.    
  (Cube 패키지에는 이에 대한 예제가 포함)
* 플래시를 활성화 후 Block 모드로 테스트 후 Memory Mapping Mode로 변경해야함.
    * CPU가 플래시에서 직접 데이터를 가져올 수 있음.
* 플래시가 메모리 매핑 모드에 있으면 외부 RAM에 사용 한 것과 유사한 코드로 테스트할 수 있음.
* 이전 단계에서 수행한 메모리 읽기 성능 테스트를 재사용하여 외부 플래시의 성능도 테스트할 것.
#### 2.5.3.7 External block mode flash(외부 Flash block mode, Non-Memory Mapped Flash)
* NAND플래시(eMMC,SD Card)와 같은 Non-Memory Mapping Flash 메모리로 작업시,    
  TouchGFX가 내부에 저장된 Resource를 사용하고자 한다면 드라이버를 개발해야함.
* image 저장을 위한 Non-Memory Mapping Flash 사용 Section은 다음 링크 참고.
* 목표 : 매핑되지 않은 플래시 메모리의 위치에서 여러 바이트를 읽고    
  이를 배열에 저장할 수 있는 드라이버를 만듬.
* MCU의 FMC를 이용함.
* TouchGFX AL을 개발하는데 사용.
#### 2.5.3.8 Hardware acceleration(하드웨어 가속)
* Chrom-ART (DMA2D) 그래픽 가속기는 메모리에서 이미지 데이터의 일부를 전송하여    
  프레임 버퍼에 그리거나 구성 할 수 있음.
* DMA2D는 Chrom-ART의 code name임.
* 목표 : Chrom-ART를 활성화하고이를 사용하여 데이터를 읽고 쓰는 것.
* CubeMX의 'Multimedia -> DMA2D'
#### 2.5.3.9 Touch controller(터치 컨트롤러)
* 터치 컨트롤러에서 터치 좌표를 읽을 수 있어야함. TouchGFX AL 계층을 개발하는데 사용.
* 터치 컨트롤러와 통신하도록 MCU 구성(I2C)    
  터치 컨트롤러와의 통신 드라이버 코드 작성.
* 나중에 TouchGFX와 통합 할 때 필요한 드라이버 기능을 개발해야함.    
  이 함수는 터치가 있으면 true를 반환하고 그렇지 않으면 false를 반환해야하며    
  좌표도 제공해야함.
* 여러 Touch Point(finger Point) 제공 시,    
  대부분의 경우 첫번째 터치 포인트를 선택함.
* TouchGFX AL Del Section에서 터치 관련 값을 TouchGFX으로 보내는 방법에 대해 설명함.
* TouchGFX App과 동일한 스레드에서 코드가 실행되는 경우,    
  1ms 이내에 샘플링 터치가 가능해야함.
#### 2.5.3.10 Physical buttons(물리적 버튼 및 기타 백엔드 이벤트)
* 물리적버튼은 TouchGFX Designer에서 Trigger로 사용할 수 있는 External Event로 작동하거나    
  단순히 app Backend에서 Event로 활용가능.
* TouchGFX HAL 및 App 개발에 사용할 수 있는 코드를 개발하는 것.
* TouchGFX App과 동일한 스레드에서 코드가 실행되는 경우,    
  1ms 이내에 버튼의 상태를 polling이 가능해야함.
#### 2.5.3.11 Flash Loader
* 외부 플래시로 데이터를 load 하는 방법.

## 2.6 TouchGFX AL Development Introduction
* TouchGFX Abstraction Layer(AL) : Board init code 와 TouchGFX Engine 사이의 S/W 구성 요소.    
* 주요 : TouchGFX 엔진을 기본 H/W 및 OS와 함께 연결하는 것.
* 기본 H/W 및 OS의 세부사항을 추상화.
* AL은 HAL(하드웨어 추상화 계층) 과 OSAL(운영체제 추상화 계층)으로 구성.
* AL의 원칙과 책임에 대한 소개 TouchGFX 엔진과 interaction하는 방법 소개
* Abstraction Layer Architecture Section
    * AL의 구조(architecture)를 자세히 설명,    
      hook : TouchGFX 엔진과 AL 사이 interaction 지점.
* Generator User Guide Section
    * TouchGFX Generator를 사용하여 AL 구현의 기반을 만드는 방법 및 더 복잡한 문제 설명
* 특정 하드웨어 설정에 대한 AL을 만드는 방법에 대한 구체적인 자세한 예를 제공.
### 2.6.1 AL의 책임
* TouchGFX Engine Main Loop
    1. **Collect** input(Touch coordinates, Buttons)
    2. **Update** the Scene Model
    3. **Render** the Scene Model to the Framebuffer
1. 실제로 Framebuffer Data를 Display로 전송 및 외부 입력 수집.
2. 메인루프와 Display(LCD 패널)사이의 동기화.    
   디스플레이에 프레임 버퍼 데이터를 올바르게 전송되고 표시될 수 있도록    
   Display의 실제 업데이트 빈도 및 Wait상태와 동기화.
3. 프레임 버퍼 메모리 접근 여부에 대한 책임.    
   프레임 버퍼 메모리 영역의 접근은 AL 통해 진행.
* 디테일한 AL의 책임 정리
    1. TouchGFX Engine 메인 루프를 디스플레이 전송과 동기화
    2. 터치 및 물리적 버튼 이벤트 보고
    3. 프레임 버퍼 액세스 동기화
    4. 사용 가능한 다음 프레임 버퍼 영역 보고
    5. 렌더링 작업 수행
    6. 표시할 프레임 버퍼 전송 처리
* TouchGFX AL은 S/W 모듈임.    
  자체 스레드(유사한것도)없음.    
  TouchGFX Engine 메인 루프에서 호출 된 특정 hooks(기능) 또는 인터럽트를 통해 작업을 수행해야함.
  
![image](https://user-images.githubusercontent.com/79636864/112913074-ef5ad880-9133-11eb-8b7c-b5b584acbbfc.png)    

* AL 개발 시 기본 H/W 및 OS에서 AL의 책임을 다룰 수 있도록 hook를 구현해야함.
* AL 개발 시 필요한 경우 특정 지점에서 활성화되도록 인터럽트를 설정할 수 있음.
* 이러한 인터럽트 설정도 AL 개발의 일부

### 2.6.2 예시 : Two Framebuffer - LTDC
* LTDC가 있는 MCU에서 두개의 Framebuffer를 갖는 경우,    
  디스플레이가 새 프레임을 수신할 준비가 될 때마다 'I1'이 실행되도록    
  LTDC VSYNC 인터럽트에 반응하도록 AL을 설정.    
  (메인루프와 디스플레이를 동기화)    
![image](https://user-images.githubusercontent.com/79636864/112913385-7740e280-9134-11eb-8ab6-07ef7c469b5a.png)    

### 2.6.3 Abstraction Layer Architecture
* 일반적으로 RTOS(OSAL)를 통해 AL(HAL)의 HW부분 또는    
  TouchGFX 엔진과 동기화되는 AL의 부분에서 구현.
* AL 개발자가 수동으로 구현해야하는 나머지 부분은    
  TouchGFX Generator를 통한 코드 주석 및 알림을 통해 지적.
#### 2.6.3.1 Abstraction Layer Classes
* HAL은 "HAL Class"의 구체적인 하위 클래스를 통해 TouchGFX 엔진에 의해 액세스됩니다.
* TouchGFX Generator는 CubeMX의 구성을 반영하는 HAL과 CMSIS V1 및 V2용 RTOS 모두 생성 가능.    
![image](https://user-images.githubusercontent.com/79636864/112913541-cbe45d80-9134-11eb-8ea8-86009b174352.png)    
#### 2.6.3.2 Synchronize TouchGFX Engine main loop with display transfer
디스플레이 전송과 TouchGFX 엔진 메인 루프의 동기화
* 이 단계의 기본 아이디어 :
    * Render가 완료 시 TouchGFX Engine main loop를 차단하여    
      더이상 프레임이 생성되지 않도록 함.
* RTOS는 디스플레이가 준비되면 차단된 TouchGFX Engine에 신호를 보내 프레임 생성을 계속함.
* OSAL은 개발자가 "OSWrappers::signalVSync"를 호출 할 때    
  엔진이 대기하는 세마포어에 신호를 보낼 수있는 함수    
  "OSWrappers::signalVSync"를 정의함.
* 렌더링 완료 hook
    * 렌더링 완료 후크 인 OSWrappers :: waitForVSync는    
      렌더링이 완료된 후 TouchGFX 엔진에 의해 호출됨.
    * OSWrappers :: signalVSync가 신호를 받으면(또는 OSWrappers :: waitForVSync에서 사용되는 세마포어 / 큐가 신호를 받으면)    
      TouchGFX는 다음 애플리케이션 프레임 렌더링을 시작함.
* Display ready hookk
    * Display Ready 신호는 Display Controller, Display 자체 또는    
      H/W 타이머의 인터럽트에서 발생해야함.
    * 'OSWrappers::signalVsync'
        * TouchGFX Engine의 차단을 해제하는 Message queue를 넣음.
        * LTDC, 디스플레이 외부 신호 또는 HW 타이머와 같은    
          인터럽트의 HW Level 수준에서 호출되어야함.
#### 2.6.3.3 Report touch and physical button events
터치 및 물리적 버튼 이벤트보고
* 새 프레임을 랜더링 하기전에 TouchGFX Engine은 아래의 인터페이스에서 외부 입력을 수집.
    1. TouchController
    2. ButtonController
* Touch Coordinates(터치 좌표)
    * 터치 컨트롤러의 좌표는 엔진에 의해 클릭, 드래그 및 제스처 이벤트로 변환되어 App에 전달.
    * TouchGFX Engine 렌더링 주기 동안 입력을 수집할 때    
      엔진 "sampleTouch()"는 tc 개체에 대한 함수를 호출함.
    * 터치 좌표 값을 x, y에 할당, 터치가 감지되었는지 체크.
    * 위의 기능을 구현하는 2가지 방법
        * 폴링방식.    
          Touch Read를 위한 i2C 시퀀스는 최대 1ms가 이르는 경우가 많기 때문에 전체 렌더링 시간에 영향을 줌.
        * 인터럽트 방식.    
          터치 하드웨어의 외부 인터럽트에 의해.
* 기타 외부 이벤트
    * Button Controller 인터페이스 인 touchgfx :: ButtonController는    
      하드웨어 신호 (버튼 또는 기타)를 이벤트에 애플리케이션에 매핑하는 데    
      사용할 수 있습니다.
    * 이를 사용하려면 ButtonController 인터페이스를 구현하는 클래스의 인스턴스를 만들고    
      인스턴스에 대한 참조를 HAL에 전달해야함.
    * ButtonController 클래스의 'sample'method는 각 프레임 전에 호출됨.    
      true를 반환하면 'key'값이 현재 화면의 handlekeyEvent 이벤트 핸들러에 전달됨.
#### 2.6.3.4 Synchronize framebuffer access(프레임 버퍼 액세스 동기화)
* TouchGFX Engine은 "OSWrappers"인터페이스를 통해 프레임 버퍼 액세스를 동기화,    
  프레임 버퍼에 액세스 하려는 주변장치(DMA2D)도 동일한 동기화 작업을 수행해야함.
* 세마포어를 사용하여 프레임 버퍼에 대한 액세스를 보호, 그외 다른것도 가능.
* OSWrapper.cpp에는 프레임버퍼 액세스를 동기화하는 method를 보여줌.
* Report the next available framebuffer area(사용 가능한 다음 프레임 버퍼 영역보고)
    * HAL :: getTFTCurrentLine () 
    * 그래픽 엔진이 그릴 위에 저장되는 줄 번호를 반환합니다.
    * 위의 메서드는 HAL 하위 클래스에서 다시 구현할 수 있습니다. 
#### 2.6.3.5 Perfrom Render operations('Render' 작업 수행)
* TouchGFX는 가능한 적은 CPU점유율을 사용하여 UI를 그리는 것.
* HAL 클래스는 많은 STM32 마이크로 컨트롤러 (또는 기타 하드웨어 기능)에있는    
  DMA2D를 추상화하고 이를 그래픽 엔진에서 사용할 수 있도록함.
* 비트 맵과 같은 자산을 프레임 버퍼로 렌더링 할 때 TouchGFX 엔진은 HAL이 비트 맵의    
  일부 또는 전체를 프레임 버퍼로 'blit'할 수 있는지 확인
* 그리기 작업은 CPU에서 처리하지 않고 HAL에 위임됨.
* 엔진은 HAL :: getBlitCaps () 메서드를 호출하여 하드웨어 기능에 대한 설명을 가져옴.
* HAL 하위 클래스는 이를 다시 구현하여 기능을 추가할 수 있음.
* 엔진이 사용자 인터페이스를 그릴 때 HAL 클래스에 대한 작업을 호출함.    
  HAL :: blitCopy는 DMA에 대한 작업을 queue에 넣음.
#### 2.6.3.6 Handle framebuffer transfer to display(프레임 버퍼 전송을 처리하여 디스플레이)
* 프레임 버퍼를 디스플레이로 전송하기 위해 TouchGFX AL에서 "Rendering of area complete"후크가 종종 사용됨.
* 엔진은 프레임 버퍼의 일부 렌더링이 완료되면 AL에서 신호를 보내고    
  AL은 프레임 버퍼를 디스플레이로 전송하는 방법을 선택 가능.
* hook는 virtual function "HAL::flushFrameBuffer(Rect& rect)"
* LTDC 컨트롤러가 있을 경우, 렌더링할때마다 프레임 버퍼를 전송하기 위해 아무것도 할 필요가 없지만,    
  SPI또는 8080과 같은 디스플레이 유형의 경우 프레임 버퍼를 수동으로 전송해야함.

## 2.7 TouchGFX Generator User Guide
* TouchGFX Generator : 개발자가 HW플랫폼에서 실행되도록 TouchGFX를 구성하는데    
  도움이 되는 CubeMX 추가 소프트웨어 구성 요소임.
* TouchGFX HAL,    
  TouchGFX OSAL(RTOS),    
  TouchGFX Configuration.
1. Enabling TouchGFX Generator
2. Generated Code Architecture
    * **생성된 코드의 구조와 개발자가 이를 사용하여 구성 및 동작을 사용자 정의하는 방법을 이해해야함!**
    * CubeMX(C 코드)에서 생성된 "USER CODE Section"을 통해 개발자기 손으로 작성한 사용자 코드를 보호.
    * TouchGFX Generator (C++ code)는 개발자가 손으로 작성한 사용자 코드를 'inheritance'(상속)을 통해 구현가능.    

![image](https://user-images.githubusercontent.com/79636864/112915088-8a55b180-9138-11eb-9584-39759753fd9f.png)   
* Folder 별 설명
    * myproject.ioc : CubeMX Project file
    * Core : main.c and startup code
    * Drivers : CMSIS and MCU family drivers
    * EWARM : IDE project folder. Can be EWARM, MDK-ARM or STM32CubeIDE
    * Middlewares : TouchGFX 라이브러리 / 헤더 파일 및 FreeRTOS와 같은 타사 소프트웨어가 포함되어 있음.
    * ApplicationTemplate.touchgfx.part : .part 파일은 TouchGFX Designer 프로젝트와 관련된 정보    
      (예 : 화면 크기 및 비트 깊이)로 CubeMX에 의해 업데이트됩니다.
    * App : CubeMX에 대한 X-CUBE 인터페이스.    
      app_touchgfx.c기능에 대한 정의를 포함 MX_TouchGFX_Process(void)하고    
      MX_TouchGFX_Init(void)TouchGFX 초기화와 메인 루프를 시작하기 위해 사용됨.
    * target/generated : 이 하위 폴더에는 구성이 변경 될 때 CubeMX가 덮어 쓰는 읽기 전용 파일이 포함되어 있음.    
                         TouchGFXGeneratedHAL.cpp:TouchGFX 클래스의 하위 클래스이며    
                         CubeMX HAL가 현재 구성을 기반으로 생성 할 수있는 코드를 포함.    
                         OSWrappers.cpp(OSAL)에는 TouchGFX Engine과 동기화하는 데 필요한 기능인    
                         TouchGFXConfiguration.cpp이 포함되어 있으며    
                         마지막으로 HAL을 포함하여 TouchGFX를 구성하는 코드가 포함되어 있음
    * target : HAL의 동작을 확장하거나 CubeMX에서 생성 된 구성을 재정의하기 위해    
               사용자가 수정할 수있는 대량의 파일을 포함.    
               STM32TouchController.cpp에는 빈 터치 컨트롤러 인터페이스가 포함되어 있음.    
               TouchGFXHAL.cpp는 TouchGFXGeneratedHAL의 하위 클래스 인 TouchGFXHAL을 정의합니다.
* TouchGFXConfiguration.cpp는    
  **HAL을 구성하는 함수와 TouchGFX의 메인 루프를 시작하는 함수가 포함되어 있음을 아는 것이 중요!**     
  편집 가능한 사용자 클래스에서 추가 구성을 수행 할 수 있음.

3. CubeMX에서 TouchGFX Generator Enable시 UI에 대한 Feature Overview
* Dependencies(종속성) : 구성의 종속성, 경고 또는 구체적인 오류에 대한 개발자 알림이 포함
* Display : 인터페이스, 프레임버퍼, 비트 심도, 너비 및 높이와 같은 디스플레이 관련 설정이 포함.
* Driver : App의 Tick 소스, 그래픽 가속, RTOS와 관련된 여러 기성 드라이버 선택 가능.

4. Generated project
* CubeMX는 다음 구조의 *TouchGFX* 폴더를 생성함.    
![image](https://user-images.githubusercontent.com/79636864/112916059-b96d2280-913a-11eb-8195-985f8d685971.png)    
* 폴더 설명
    * App : TouchGFX를 초기화하고 시작하는 코드가 포함됨.
    * target : 읽기 전용, 생성 된 코드 ( 'generated /'내부) 및 수정 가능한 사용자 클래스    
               (STM32TouchController.cpp, TouchGFXGPIO.cpp 및 TouchGFXHAL.cpp)가 포함.
    * .part : TouchGFX 헤더 파일 및 라이브러리가 포함된    
              전체 TouchGFX 프로젝트를 생성하기 위한 TouchGFX Designer를 사용하여 열림.    
              디자이너가 TouchGFX app 코드를 생성할때 사용하는    
              픽셀 형식 및 캔버스 치수와 같은 관련 app 정보가 포함되어 있음.
5. TouchGFX Designer Project
* TouchGFX Designer로 .part 파일을 열면 개발자에게 구체적인 UI를 로드하거나    
  빈 템플릿에서 시작할 수있는 옵션이 제공.
* TouchGFX Designer에서 Generate Code를 누른 후 TouchGFX 폴더의 구조.
    * 노랑색 영역 : 생성 후 새로운 파일 및 폴더.    

![image](https://user-images.githubusercontent.com/79636864/112916234-208ad700-913b-11eb-80d7-d511510c4018.png)    

* 내가 선택한 IDE를 감지하고 화면 정의, 이미지 및 글꼴 assets파일과 같은    
  새로 생성된파일로 프로젝트 파일을 업데이트함.
* CubeMX, TouchGFX Designer, IDE/Toolchain 간 서로 상호 교환하면서 작업가능
    * CubeMX는 IDE 프로젝트를 드라이버로 업데이트
    * CubeMX는 UI 관련 변경 사항으로 TouchGFX.part파일을 업데이트
    * CubeMX는 TouchGFX가 특정 플랫폼에서 작동하는 데 필요한 TouchGFX Generator를 기반으로    
      HAL 코드(TouchGFX/target/generated/)폴더를 생성함
    * TouchGFX Designer 프로그램은 생성 된 코드로 프로젝트를 업데이트함.

6. Modifying Generated Behavior
* 개발자는 CubeMX에 의해 생성된 HAL 구성 또는 동작을 재정의할 수 있음!    
  (HAL의 클래스 계층 구조로 인해서)
* 예시로, TouchGFXHAL.cpp의 initialize() method에서 기능을 수정하여 추가로 구성할 수 있음.

7. Upgrading Projects(프로젝트 버전 업그레이드)
* TouchGFX Generator 매개 변수는 .ioc 파일 (CubeMX 프로젝트)에 저장됨.    
  TouchGFX Generator의 새 버전이 출시되면 이전 버전의 매개 변수가 새 버전과    
  호환되지 않을 수 있으며 마이그레이션이 필요할 수 있음

## 2.8 LCD 인터페이스에 따른 AL구현 시나리오
* 지금 당장은 LTDC로 사용하고 있기때문에 모두 구현되어있음.
* 추후 정리
### 2.8.1 LTDC/Paralled RGB
### 2.8.2 FMC/SPI Display
### 2.8.3 Framebuffer Strategies
#### 2.8.3.1 Single
#### 2.8.3.2 Double
#### 2.8.3.3 By Allocation
#### 2.8.3.4 By Address

# 3. 정리
TouchGFX의 Development의 내용은 한번에 정리하지 못할정도로 내용이 방대합니다.    
본격적으로 Application에 대한 UI작업을 시작할 것이고    
UI작업을 하면서 UI Devlopment에 대한 전반적인 내용을 정리할 것입니다.
