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
* presenter->MVPersentSliding(value);
    * Presenter Class를 호출합니다.    

### 1.2.2 Presenter Class    

~~~c++
/* Presenter.cpp */
void ManualVCTLScreenPresenter::MVPersentSliding(int value)
{
	model->SetPWMtoHW(value);
}
~~~    

* MVPersentSliding() Method에서 바로    
  model->SetPWMtoHW(value);를 호출합니다.    


### 1.2.3 Model Class    


~~~c++
/* model.cpp */

#ifndef SIMULATOR
#include "cmsis_os.h"
#include "DCMotorPIDController.h"

extern "C"
{
	extern osMessageQueueId_t MVCTLPWMQueueHandle;
}

#endif


Model::Model() : modelListener(0)
{
#ifndef SIMULATOR

#endif	
}

void Model::tick()
{
#ifndef SIMULATOR

#endif	
}

void Model::SetPWMtoHW(int PWMValue)
{
#ifndef SIMULATOR
	sDCManualPWMMessage temp;
	
	if((PWMValue >= 0) && (PWMValue <= 1000))
	{
		temp.uPWMValue = PWMValue;
		if(MVCTLPWMQueueHandle)
		{
			osMessageQueuePut(MVCTLPWMQueueHandle,(&temp),0,0);
		}
	}
	
#endif	
}
~~~    

* SIMULATOR define문을 사용한 이유는 TouchGFX Designer의 PC Simulater 실행 시,    
  Backend와 연결되어있는 환경이 아니므로 Backend 관련 코드 내용 및 동작은    
  PC Simulater 실행 시, 포함되지 않도록 처리하기 위함입니다.
* TouchGFX UI는 C++로 Build를 하기 때문에 C로 Build하는 Backend의 코드를 사용 시,    
  'extern "C" 구문으로 묶어줘야합니다.
* osMessageQueuePut(MVCTLPWMQueueHandle,(&temp),0,0);를 통해 Backend로    
  Event에 대한 Value를 Message queue를 통해 전달합니다.    
  
  

### 1.2.4 FreeRTOS Task(Backend)    

~~~c
/* backend(main).c */
void PIDCTL_Task(void *argument)
{
	sDCManualPWMMessage DCMPWMMsg;
	osStatus_t eRvalue;
	
  for(;;)
  {
	  eRvalue = osMessageQueueGet(MVCTLPWMQueueHandle, &DCMPWMMsg, NULL, 0U);
	  if(eRvalue == osOK)
	  {
		  DCM_PWM_SetValue(DCMPWMMsg.uPWMValue);
	  }
	  osDelay(1);
  }
}
~~~    

* backend인 FreeRTOS Task에서는 1ms Delay간격으로 전달받은 Event를 체크하고    
  전달받은 Event가 있을 경우, 그 Value에 따른 H/W 적인 동작을 수행합니다.
    * 예시에서는 PWM 제어.    


# 2. UI<--Backend(UI 입력)
## 2.1 전체흐름.
* Backend의 Event 또는 state 등을 사용자가 알 수 있도록 LCD 패널의 View(UI)로 전달하는 과정입니다.    

![image](https://user-images.githubusercontent.com/79636864/113952911-9be23c00-9851-11eb-9f67-77cd246ba831.png)    

1. 먼저, Backend에서 UI의 Model Class가 관련 Event를 받을 수 있도록 전달합니다.
    * FreeRTOS환경이기에 Message Queue로 전달합니다.
2. Model Class는 'tick()' method를 통해 주기적으로 Backend에서 보낸 Event를 체크합니다.
    * 'tick()' method는 1 Frame 처리시마다 호출되는 주기적인 method입니다.
3. Backend로부터 보낸 Event가 있을 시, Model Class는 Presenter Class의 주소값을 가지고 있는    
   'modelListener'를 통해 Event의 Value를 전달합니다.
   * 호출하는 modelListener의 method는 Vitual Function으로 그 원형은    
     Presenter Class에서 정의하고 있습니다.(Vitual Function의 overwritting)
4. 호출된 Event의 Value를 전달 받은 Presenter Class는 사용자가 볼수 있도록    
   parsing하여 UI에 표시하도록 View Class에 전달합니다.
5. parsing한 Value를 전달받은 View Class는 실제 LCD 패널의 View에 표시하기 위한    
   동작을 수행합니다.    
   
## 2.2 Example(구체적 설명)
* 실제 Application에 적용한 예제소스를 통한 예시를 아래에 정리하였습니다.
* 예시 : 현재 Moter(Backend)의 RPM을 사용자가 볼 수 있도록 LCD 패널의 View(UI)로 전달하기.

### 2.2.1 FreeRTOS Task(Backend)    

~~~c
/* backend(main).c */
void PIDCTL_Task(void *argument)
{
  for(;;)
  {
	  DCM_Cal_Encoder_to_RPM(&DCM_CAL);
	  
	  osMessageQueuePut(MCALQueueHandle,(&DCM_CAL),0,0);

	  osDelay(1);  
  }
}
~~~    

* Backend에서는 DC Moter의 Encoder 신호를 Read하고 Calculate 하여    
  UI의 Model Class가 읽을 수 있도록 주기적으로 Message Queue를 통해 전달합니다.    
  
### 2.2.2 Model Class    

~~~c++
/* Model.cpp */
#ifndef SIMULATOR
#include "cmsis_os.h"
#include "DCMotorPIDController.h"

extern "C"
{
	extern osMessageQueueId_t MCALQueueHandle;
}

#endif


Model::Model() : modelListener(0)
{
#ifndef SIMULATOR
	HW_RPM = 0;	
#endif	
}

void Model::tick()
{
#ifndef SIMULATOR
	sDCCAL bDCMCAL;
	
	if(osMessageQueueGet(MCALQueueHandle, &bDCMCAL, NULL, 0U) == osOK)
	{
		if(HW_RPM != bDCMCAL.uRPM)
		{
			HW_RPM = bDCMCAL.uRPM;
			if(modelListener != 0)
			{
				modelListener->notifyRPMChanged(HW_RPM);
			}			
		}
	}
#endif	
}

/* ModelListener.hpp */
class ModelListener
{
public:
    ModelListener() : model(0) {}

    virtual ~ModelListener() {}

    /**
     * Sets the model pointer to point to the Model object. Called automatically
     * when switching screen.
     */
    void bind(Model* m)
    {
        model = m;
    }
	
	virtual void notifyRPMChanged(uint16_t newRPM) {}
protected:
    Model* model;
};
~~~    

* Model Class에서는 1Frame 처리 시 호출하는 주기적인 method인    
  'tick()' method에서 Backend가 전송한 Event가 있는지 확인합니다.
* Backend가 전송한 Event 확인 시, modelListener의 관련 method를 통해    
  Event의 Value를 전달합니다.    
* ModelListener.hpp에 Presenter Class가 사용할 method를 Virtual Funtion으로    
  선언 및 정의합니다.
    * virtual void notifyRPMChanged(uint16_t newRPM) {}

### 2.2.3 Presenter Class
