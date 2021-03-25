---
date: 2021-03-25
title: "STM32F746_TouchGFX_FreeRTOS_3_TouchGFX Basic Concepts"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_3_TouchGFX Basic Concepts
=============

본격적으로 TouchGFX를 시작하기에 앞서 Basic Concepts에 대한 내용을 정리하였습니다.

TouchGFX를 처음 사용하는 입장에서 이름 그대로 중요하고 basic한 알찬 내용들이였습니다.

아래의 홈페이지를 참고하여 정리하였습니다.

[TouchGFX_support](https://support.touchgfx.com/docs/basic-concepts/embedded-graphics)

# 1. Embedded Graphics
* TouchGFX : STM32 기반 MCU를 사용하나 임베디드 시스템에서 사용할 수 있는 GUI Application
* graphics : 60FPS로 실행되는 UI가 있는 대화형 응용 프로그램.    

## 1.1 The four main parts
* graphic 작업을 하는데 직접적인 관련이 있는 4가지 요소    

![image](https://user-images.githubusercontent.com/79636864/112458599-4d886400-8da0-11eb-88d3-49920a920e8b.png)    


* 그 외의 부가적인 요소들도 포함될 수 있음.    

### 1.1.1 MCU
* Flash에서 이미지를 읽고 그것을 RAM에 Write함.
* 이 작업을 주기적이고 무한으로 진행.
* STM32에는 LTDC, Chrom-ART, Chorm-GRC 등의 H/W가 내장되어있음.    

### 1.1.2 RAM
* 계산된 이미지(FrameBuffer)가 저장되는 곳.

### 1.1.3 Flash
* 내부플래시는 그래픽 데이터를 모두 담을 수 있을만한 용량이 충분하지 않기 때문에    
  외부플래시를 추가하는 것이 대부분임.    
* Memory mapping이 되어 플래시에서 직접 읽고 RAM에 쓸 수 있음.

### 1.1.4 Display
* RAM에 저장된 계산된 이미지(FrameBuffer)는 MCU에 의해 일정한간격(60FPS)으로    
  LCD 패널에 전송.    
  
  
# 2. Color Formats
* Display의 pixel을 칭함.
* Framebuffer에 값이 저장됨.    

## 2.1 Color

![image](https://user-images.githubusercontent.com/79636864/112459054-c1c30780-8da0-11eb-9087-6010f1798716.png)    

### 2.1.1 Grayscale(회색조 컬러)
* R=G=B
    * ex : rgb(89,89,89)    rgn(32,32,32)    


### 2.1.2 Opacity(불투명도)
* 불투명도가 있는 색상을 RGBA color라고 함.
    * A : alpha    

![image](https://user-images.githubusercontent.com/79636864/112459311-08b0fd00-8da1-11eb-9767-51882c8b7fc5.png)    

## 2.2 Color depth
* FrameBuffer에 저장된 여러 색상에 대한 bit 값임.
* bpp : bits per pixel
    * ex : 24bpp = 2^24 = 16777216(16.7M) = RGB888(Red,Green,Blue 모두 0~255)
    * ex : 16bpp = 2^16 = 65536(65K) = RGB565(Red,Blue는 0~31, Green은 0~63)    

## 2.3 Formats
### 2.3.1 Image formats

![image](https://user-images.githubusercontent.com/79636864/112459880-a9072180-8da1-11eb-871e-cc31f6cf5a47.png)    

### 2.3.2 Text formats
* glyphs : 자형. 글자의 모양을 가리킴.    

![image](https://user-images.githubusercontent.com/79636864/112459983-c805b380-8da1-11eb-9433-dcd126be511c.png)    

## 2.4 Visual quality
* RGB8888 대신 RGB565를 사용하는것이 바람직.
    * 일반적으로 메모리 요구사항을 준수하면서 가장 좋은 시각적 품질을 제공해야하기 때문    

### 2.4.1 Dithering
* 음영이나 색을 나타내는 것. 
    * 이미지의 시각적 품질을 향상시키기 위해 사용.    

## 2.5 Performance
* 이미지 data가 많은 변환이 일어나지 않고 프레임버퍼에 다소간 복사될 수 있음.
* Designer 프로그램은 .png 이미지를 사용.
* 그 이미지를 효율적인 이미지 형식 중 하나로 변환    

### 2.5.1 Alpha blending
* 이미지에 완전히 투명하지않거나 불투명하지 않은 픽셀이 포함 된 경우,    
  픽셀을 backgrounddp Alpha blending해야함.    
  
## 2.6 Other image formats
* runtime 동안 다른 이미지 포맷을 지원해야할 경우,    
  **dynamic bitmaps** 를 TouchGFX에서는 지원함.    
  
# 3. Framebuffer
* 디스플레이에 표시할 다음 이미지의 메모리 조각으로써 그래픽 엔진에 의해 update 됨.
* RAM의 연속적인 부분임.    

![image](https://user-images.githubusercontent.com/79636864/112460910-bf61ad00-8da2-11eb-9ac0-5b2b34196edc.png)    

* Framebuffer의 각 index항목은 Pixel bits 값이 들어있음.    

![image](https://user-images.githubusercontent.com/79636864/112461041-e3bd8980-8da2-11eb-9f7c-224848719e22.png)    

## 3.1 Display
* Framebuffer Size = Display Active Area Pixel width * height    

![image](https://user-images.githubusercontent.com/79636864/112461208-11a2ce00-8da3-11eb-8da7-7a8a2de37c72.png)    

## 3.2 Location of framebuffer
* Framebuffer는 MCU 내부 또는 외부 RAM에 배치할 수 있음.    

![image](https://user-images.githubusercontent.com/79636864/112461467-47e04d80-8da3-11eb-8170-537dbee0f1b7.png)    

* Internal RAM
    * 장점 : 읽기, 쓰기가 빠름.
    * 단점 : 용량이 부족함.
* External RAM
    * 장점 : 용량이 많음.
    * 단점 : 읽기, 쓰기가 Internal RAM보단 다소 느림.
        * caching을 하여 빠르게 사용 가능.

## 3.3 Display with embedded RAM(LCD 패널에 RAM이 있는 경우)
* memory mapping은 당연히 불가능.
* FrameBuffer는 기본적으로 MCU 내부 또는 외부 RAM에 배치,    
  이 FrameBuffer를 LCD 패널의 RAM에 전달 가능(특정 조건에 맞을 경우)    
  
## 3.4 Amount of framebuffers
### 3.4.1 One Framebuffer
* LCD 패널로 전송될 모든 Pixel들을 정확하게 보유하기 충분.
* LCD 패널에 RAM이 없는 경우 최소 하나의 Framebuffer가 필요.    

### 3.4.2 More than one(two) Framebuffer
* 하나의 Framebuffer : 다음 결과의 이미지를 만드는데 사용.
* 또다른 하나의 Framebuffer : LCD Panel로 전송하는데 사용.
* 찢어짐등의 화면 전환이 나오지 않기 위해 사용.(자연스러운 화면 전환)

### 3.4.3 Less than one Framebuffer
* LCD 패널에 RAM이 있는 경우만 사용.

## 3.5 Memory consumption
![image](https://user-images.githubusercontent.com/79636864/112462301-3f3c4700-8da4-11eb-90c4-2a4d865ff3a1.png)    

# 4. Graphics Engine
* 역할 : 임베디드 장치와 연결된 LCD 패널에 그림을 그리는 것.

## 4.1 Scene model
* Immediate mode graphics engines
    * LCD 패널에 직접 그릴 수 있도록 해주는 API로 구성
    * ex : Adstar 등
* Retained mode graphics engines
    * 표시되는 그래픽 구성 요소의 추상 모델을 조작할 수 있음.
    * 이것이 TouchGFX의 형식
* 사용자가 조작할 수 있는 모델을 제공 ->    
  이 모델에 최적화된 렌더링 메소드 호출 집합으로 변환하는 작업을 처리.    

![image](https://user-images.githubusercontent.com/79636864/112462731-bf62ac80-8da4-11eb-8e37-b3e53f9bf1ed.png)    

* Retained mode 장점
    1. Ease of use : 내부 Model에서 method를 호출하여 지정. 실제 드로잉 작업을 고려하지 않음.
    2. Performance : Scene model를 분석하고 화면에 Model을 실현하는데 필요한 그리기 호출에 적합함.
    3. State management : Scene model의 어느 부분이 활성화 되었는지 추적 가능.
* Retained mode 단점
    1. Memory consumption : 메모리 차지 영역이 있음.
 
### 4.1.1 Manipulate the model
* widgets(위젯) : 컴퓨터 사용자가 상호작용하는 인터페이스 (=control)
* scene model의 위젯을 설정하고 조작하여 표시된 그래픽이 업데이트 됨.

### 4.1.2 Issue drawing commands
* scene model의 렌더링(외관을 이해할 수 있도록 실물 그대로 그린 완성 예상도)할때    
  드로잉 API를 활용함.
* 새로운 유형의 위젯으로 scene model를 확장 시, 드로잉 API를 이용한 그리기 가능.    
  (custom widget)

# 5. Main loop
TouchGFX의 메인루프에서 하는 세가지 주요 활동
















