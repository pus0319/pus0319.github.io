---
date: 2021-04-08
title: "STM32F746_TouchGFX_FreeRTOS_6_TouchGFX UI 입출력 구현"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_6_TouchGFX UI 입출력 구현
=============

실제로 UI 개발 실무에 적용하는 UI 입출력 관련 내용을 정리하였습니다.    

구체적으로 제품의 H/W부분(Backend)과 UI의 입출력(통신) 방법에 대한 내용입니다. 

연습용으로 진행하고 있는 Application을 통해 구체적인 Example에 대한 내용도 추가하였습니다.

더 자세하고 개념적인 내용이 있는 아래의 링크를 참고하면서 보면 좋습니다.

[TouchGFX_Backend Communication](https://support.touchgfx.com/docs/development/ui-development/touchgfx-engine-features/backend-communication)    
[TouchGFX_UIDel 정리](https://pus0319.github.io/embedded_freertos/F746TGRTOS_TG_UIDel/)

# 0. Model-View-Presenter Design Pattern(MVP 아키텍처)와 Backend System 구조

![image](https://user-images.githubusercontent.com/79636864/112955071-03292d80-917a-11eb-9089-60b6512706c6.png) 

* UI입출력 구현이 처음이라면 위의 그림에 대한 내용을 숙지하는 것이 유리합니다.
* 여기서 Backend Module은 실제제품의 H/W Component(센서,chipset,구동MCU의 IO 등 등)과    
  실제제품과 외부로 통신하는 다른 제품이 될 수도 있습니다.
* TouchGFX UI는 사용자의 입장에서는 단순히 LCD 패널(+터치패널)안의 버튼, 이미지, 애니매이션으로    
  구성되어있다고 보이지만, 이러한 눈에 보이는 것들은 'Model','Presenter','View' Class가 유기적으로 연결되어있는    
  MVP 아키텍처로 되어있습니다.
  
# 1. UI-->Backend(UI 출력)
## 1.1 전체흐름.
* UI의 조작(Button누름, Silder조작 등)을 통해 Backend로 신호(통신)을 보내 제어하는 방법입니다.    

![image](https://user-images.githubusercontent.com/79636864/113949489-5e2de500-984a-11eb-9de4-828a44b7d2a6.png)    

1. 먼저, View Class에서 사용자 또는 다른 특수 조건에 따른 Event를 수집합니다.    
    * 사진의 예시는 사용자가 Silder(UI)를 조작했을 시(User Event).
    * User Event외에 특수한 조건의 Event들 모두 가능합니다.    
      (결국엔 만들기 나름.)
2. User Event이기 때문에 ViewBase.cpp에 정의된 Callbackhandler()를 호출 하고    
   User Event 처리를 위한 method를 호출합니다.
3. User Event method에서 Presenter Class에게 User가 UI를 조작했다는 내용을 전달합니다.
    * View Class는 Presenter Class의 주소값을 알고 있는 변수를 가지고 있습니다.    
        * 'presenter' 입니다.
    * Presenter Class의 관련 User Event를 처리하는 method를 호출하면 됩니다.
4. 호출된 Presenter Class의 User Event 처리 관련 method에서    
   Backend가 알 수 있는 Value로 Parsing하여 Model Class에게 그 Value를 전달합니다.
    * 마찬가지로 Presenter Class는 Model Class의 주소값을 알고 있는 변수를 가지고 있습니다.
        * 'model' 입니다.
    * Model Class의 Backend로 해당 Value를 전달해주는 method를 호출하면 됩니다.
5. 호출된 Model Class의 method에서는 전달해야하는 Value를 Backend가 받을 수 있도록    
   처리합니다.
   * 보통, FreeRTOS를 많이 쓰기 때문에 Task간 통신을 위해 사용하는    
     'Message queue'를 많이 쓰는 편입니다.
6. Backend는 UI의 Model Class에서 보낸 Value를 수시로 체크하여    
   보낸 Value가 있을 경우, 그에 따른 H/W적인 동작을 수행합니다.
   
## 1.2 Example(구체적 설명)
* 실제 Application에 적용한 예제소스를 통한 예시를 아래에 정리하였습니다.
* 예시 : Silder(UI)를 조작하여 Moter(Backend)의 속도를 PWM을 통해 제어하기.

### 1.2.1 View Class    
~~~c++
/* ViewBase.cpp */
void ManualVCTLScreenViewBase::sliderValueChangedCallbackHandler(const touchgfx::Slider& src, int value)
{
    if (&src == &sliderRPMCTL)
    {
        //ChangeMVPersentValue
        //When sliderRPMCTL value changed call virtual function
        //Call ChangeMVPersentValue
        SetMVPersentValue(value);
    }
}

/* View.cpp */
void ManualVCTLScreenView::SetMVPersentValue(int value)
{
	int temp = (value / 10);
	
	presenter->MVPersentSliding(value);
	Unicode::snprintf(MVPersentValueBuffer, 5, "%d", temp);
	MVPersentValue.invalidate();
}
~~~    

* 먼저, TouchGFX Designer를 통해 Silder를 만들고 interaction으로 Silder가 Changed됬을 시,    
  SetMVPersentValue() method를 호출하도록 하였습니다.
    * presenter->MVPersentSliding(value);를 통해 
