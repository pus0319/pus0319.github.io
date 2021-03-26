---
date: 2021-03-26
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
    * Application의 그래픽(UI Model)을 Framebuffer에 렌더링하는 것.

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
* Collect events(Collect) : 터치스크린, 물리적버튼, 백엔드 시스템의 메세지 등을 수집하고 필터링하여 Application에 전달.
    * TouchGFX AL(abstracion layer)에서 처리함. 
    * Section : TouchGFX AL Development
* Update scene model(Update) : 수집된 이벤트에 따른 모델의 위치, 색상, 애니메이션, 이미지 등을 업데이트
    * 전적으로 Application에서 처리해야함.
    * Section : UI Development
* Render scene model(Render) : 업데이트된 모델의 일부를 다시 그리고 LCD 패널에 나타나게 함.    
    * TouchGFX가 알아서 처리함.
    * LCD 플랫폼에 따라 Framebuffer의 Pixel값을 전송하는 방식이 다름.    
* Wait
    * LCD 패널과의 동기화 구현을 위해 사용.
    * HAL에서 Display로부터 'go' 신호가 올때까지 기다림.    


![image](https://user-images.githubusercontent.com/79636864/112558643-911bb600-8e12-11eb-8ddf-384699a1aca1.png)    

* pseudo code the main loop inside the TouchGFX graphics engine
~~~c
while(true)
{
  collect();    // Collect events from ouside
  update();     // Update the aaplication ui model
  render();     // Render new updated graphics to the framebuffer
  wait();       // Wait for 'go' from display
}
~~~    

## 5.1 Collect
* 외부환경에서 이벤트를 수집, 수집한 이미지를 샘플링하여 Application에 전달.
* 원시적인 터치 이벤트(raw touch events)의 구분.
    1. Click : 사용자가 Display에 손가락을 누름.
    2. Drag : Click하는 동안 패널에서 손가락을 움직임.
    3. Gestures : Drag를 한방향으로 빠르게 움직였다가 놓음(swipe)
* 이벤트는 활성화된 위젯에 전달.
* Tick 이벤트도 전달
    * 새 프레임 작성 시 또는 시간 기반 동작에 대해
    * 예시 : 애니메이션을 구동하거나 특정시간이 경과한 후 일시 중지 화면으로 변경하는 것.

## 5.2 Update
* 수집된 이벤트를 반영하도록 UI를 업데이트.
    * 기본원칙 : 엔진이 이벤트에 대해 Application(UI Model의 Screen 및 위젯 개체)에 알리는 것.
* 어떤 Screen이 현재 활성화 상태인지 알고 이 개체에 전달.
* Application은 Screen 또는 위젯의 속성을 변경하고 위젯에 다시 그리기를 요청함.
* 위젯은 이벤트에 대한 응답으로 변경된 상태에 대해 그래픽 엔진에 다시 그리기를 요청함.
* Application자체에서도 이벤트 반응 가능.
    * TouchGFX Designer에서 위젯에 대한 자체 상호작용(interaction) 구성.
        * 상호 작용(interaction)을 사용하여 다른 위젯을 표시하는 경우,    
          Application은 그래픽 엔진에게 다시 그리기를 요청해야함.
    * Screen의 이벤트에 반응
        * 이벤트 핸들러는 Screen의 가상함수(virtual functions)
        * Application의 Screen에서 다시 이벤트 구현 가능.
        * ex : 위젯에 터치가 있는지 여부에 관계없이 Screen을 터치할때마다 작업 수행.    

* Screen class event handlers(code)
* Time based updates(code)
* Requesting redraw(code)
    * .invalidate()
* update 단계 결과 : 다시 그릴 영역(invalidated area)    


## 5.3 Render
* 다시 그릴 영역의 목록을 실행하고 그린 영역의 목록을 포함하는 위젯을    
  FrameBuffer에 넣음.
* 그래픽 엔진에 의해서 자동으로 처리
* invalidated area를 하나씩 처리
* 수집한 그려야하는 위젯 목록들에 대해 그래픽 엔진이 각 위젯의 draw method를 호출함.
    * 순서 : Background -> 맨 위의 위젯.
    * draw method는 위젯의 state를 사용함.

## 5.4 wait
* 다음 프레임을 업데이트, 렌더링하기전에 신호를 긷림.
    * 렌더링과 LCD 패널 Displaying을 동기화
    * 프레임을 고정된 속도로 랜더링
        * 60FPS의 경우, 2초 애니메이션은 120FPS로 완료되도록 프로그래밍 해야함.
* 그래픽 엔진이 대기중일 시, CPU점유율은 Application의 priority가 낮은 다른 스레드(Task)에 사용.

## 5.5 Handling the framebuffers
### 5.5.1 Two framebuffers
* 프레임을 프레임 버퍼로 그리는 동안 다른 프레임 버퍼는 LCD 패널로 전송되고 표시됨.    


![image](https://user-images.githubusercontent.com/79636864/112560876-6849ef80-8e17-11eb-9b97-c1e697245290.png)    

* application에서 프레임을 업데이트 하지 않는 경우(화면 변화가 없을 경우)    
  동일한 프레임 버퍼가 후속 프레임에서 다시 전송.    

![image](https://user-images.githubusercontent.com/79636864/112560898-7435b180-8e17-11eb-83df-8ff77a646647.png)    
  
```
'Frame2'에서 아무것도 전송하지 않으므로 그래픽 엔진은 'Frame3'에서 FB2를 다시 전송함.
```    

* 60FPS기준, 새 프레임을 렌더링하는데 16.6667ms를 초과할 경우 이전과 같은 동일한 프레임 전송    


![image](https://user-images.githubusercontent.com/79636864/112561072-d1316780-8e17-11eb-8843-da9d5ee970ea.png)    


```
'Frame1'의 랜더링이 16.667ms 이상이 걸리므로 이전의 프레임 버퍼인 FB1을 전송하고    
'Frame3'에서 새 프레임인 FB2를 전송함.
이의 경우, 프레임이 손실되었다고 말함.
```    

### 5.5.2 One framebuffers
![image](https://user-images.githubusercontent.com/79636864/112561232-21102e80-8e18-11eb-848b-e956879998ea.png)    

# 6. Performance
* 고성능 = 원하는 그래픽 효과와 애니메이션 + 높은 프레임 속도
* 'Collect'와 'Update'의 소요 시간은 일반적으로 1ms 미만.
    * 'Render'시간 고려 시, 'Collect'와 'Update'시간을 포함.
* 60FPS기준, 새 프레임을 렌더링하는데 16.6667ms를 초과할 경우 30FPS가 됨.
    * 이의 경우, 애니메이션이 유창하게(자연스럽게) 보이지 않을 수 있음.
* 프레임 속도를 정기적으로 확인하고 모니터링 하는 것이 유용함.    

## 6.1 Measuring the Rendering Time(렌더링 시간 측정)
* transfer FB -> Render 종료까지의 시간 측정.
* GPIO Class를 이용하는 방법.
* 내부 타이머를 이용하여 체크하는 방법.    

## 6.2 Counting the Lost Frames(손실된 프레임 카운팅)
* 그래픽엔진은 'Collect'-'update'-render'의 마지막 단계에서 발생한 프레임 전송 수를 계산함.    

## 6.3 Compensating for Lost Frames(손실된 프레임 보상)
* Wait it out : 그냥 기다림. 애니메이션이 부자연스러울 수 있음.
* skip some frames : 일부 프레임 건너뛰기.(프레임 손실 시, 일부 프레임 자동으로 건너뛰도록 지시 가능.)

## 6.4 What Afferts the Rendering Time?(렌더링 시간에 영향을 미치는 요소)
### 6.4.1 업데이트된 부품의 크기
* 애니메이션 영역을 줄려 렌더링 타임 줄이기
### 6.4.2 레이어링(위젯의 겹침) 사용
* 다시 그려야하는 영역(invalidated area)에 레이어(서로 겹쳐있는 위젯들)이 많을 수록 렌더링 시간이 길어짐.
### 6.4.3 위젯의 복잡성
* Box < image < Text < Rotated or scaled images < Geometric elements < Transparency(투명성)
* 그려야하는 영역(invalidated area)에서 시간이 많이 걸리는 요소 일수록 렌더링 시간이 길어짐.
### 6.4.4 렌더링에 사용 가능한 하드웨어 지원
* 일부 STM32 MCU에는 Chrom-ART(DMA2D)라는 그래픽 가속기가 포함되어 있음.

## 6.5 What Afferts the Rendering Time?(렌더링 타임을 고려해야하는 경우)
* 느린 FPS의 경우

## 6.6 Tips To Get Good Performance(좋은 성능을 얻기 위한 TIP)
1. 변경되지 않은 것을 다시 그리지 말 것.(invalidated 하지 말 것.)
2. 품질과 속도 간의 균형 찾기
3. 하드웨어 기능 활용하기
    * ex : Chrom-ART(그래픽 가속기)
4. 계산된 그래픽을 이미지로 바꾸기.
    * 속도 : 지오메트리 원 < 이미지 원
5. FPS 미세 조정.
    * ex : 렌더링 시간이 FPS시간을 초과 시, FPS시간을 낮춰서(새로고침주기시간은 길어짐).    


# 7. Operating Systems
* Interleaving : 다른 Task와 UI Task의 접근시간을 최소화 하기 위해 여러 모듈로 나누는 작업.
    * 메모리의 banks로 분리한 경우도 Interleaving 개념을 도입한 예시
* 별도의 타이밍 요구사항과 함께 Background에서 실행되는 고급 기능이 포함되면    
  요구사항을 지원하면서 두 작업을 하나로 통합하는 것은 어려워짐.
* 새 프레임을 계속 그려야하는 그래픽 엔진 Task가 다른 Task가 실행 되는 동안 일시 중지하면 프레임 속도가 감소.
    * UI작업과 다른 복잡한 작업을 수동으로 Interleaving 하기 어려움.    

## 7.1 RTOS
* RTOS를 사용하면 여러 독립적이지만 협력적인 Task로 Application을 구성할 수 있음.
* 높은 priority와 낮은 priority의 Task로 나눌 수 있음.
* RTOS based tasks example    
![image](https://user-images.githubusercontent.com/79636864/112562441-9bda4900-8e1a-11eb-9259-c18fca4f669e.png)    

```
처리할 데이터가 있을 때 bt_comm_task(high)가 실행,    
그렇지 않으면 GUI Task 실행,    
GUI Task가 Blocked state일 때,    
music Task , bt_appl_task(low)를 실행.    
-> Scheduler가 Time 분배를 처리함.    
Low Priority인 Task를 실행할 수 있는 Time이 많이 있음을 의미.
```    

### 7.1.1 Task communication
* MultiTasking 환경에서 Task간 통신을 할 수 있는 안전한 방법도 필요함.
* message queue를 사용함.
* RTOS의 우선순위 기반 스케쥴링 정책에 의해서 High Priority인 GUI Task가 완료 후    
  Low Priority인 Task들을 실행하여 수신메세지를 처리함.    
  
### 7.1.2 Handling interrupts
* 인터럽트 핸들러에서도 message queue를 보낼 수 있음.
* Semaphore 와 mutex 도 가능.

### 7.1.3 TouchGFX OS Wrappers
* TouchGFX는 FreeRTOS 환경에서 테스트하여 개발되었음.
* Single Message queue를 사용하여 Display Controllor와 동기화,    
  Semaphore를 사용하여 FrameBuffer에 대한 액세스를 보호함.
* OS Wrappers Class의 method는 문서 참고.    

## 7.2 Non-RTOS
* RTOS를 사용하지 않으면 MCU 부하를 증가시킬 수 있으며 TouchGFX와 함께 다른 Task를 실행하기 어려움.
* UI가 실행되는 동안 다른 Task를 수동으로 구현해야함.

### 7.2.1 Model::tick
* 매 프레임(1프레임) 마다 한번씩 Model 클래스에서 Task 검사를 수행하는 것.
* 이 방법을 사용하면 모든 Task가 모든 프레임에서 한번씩 실행 됨.
* 단, Task 실행 시간이 렌더링 시간에 추가됨.

### 7.2.2 OSWrappers
* OSWrappers 클래스의 후크를 사용하는 것.
* 그래픽 엔진은 이벤트를 기다려야할 때 아래의 클래스에서 method를 호출함. 이때 다른 Task를 수행하는데 사용 가능.    
```
다른 Task들은 1ms 정도의 작은 단계로 나누는 것이 중요함. 그렇지 않으면 UI 성능이 저하함.
```    

# 8. Memory Usage

