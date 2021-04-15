---
date: 2021-04-15
title: "STM32F746_TouchGFX_FreeRTOS_8_DC모터 PID 위치제어 구현"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_8_DC모터 PID 위치제어 구현
=============

DC모터의 속도표시에 이어 DC모터의 PID 위치제어 기본에 대해 구현하고    

이를 UI로 표시하는 동작을 구현하였습니다.

kp, Ki, Kd, 목표각도 크기를 UI에 입력 후 적용하여    

START를 누르면 입력한 값에 따른 동작을 수행합니다.    

그래프에는 현재각도에 대한 값을 표현합니다.    

![image](https://user-images.githubusercontent.com/79636864/114819747-e38c3900-9df8-11eb-84ca-b6f961a8c98b.png)    

![image](https://user-images.githubusercontent.com/79636864/114819812-fbfc5380-9df8-11eb-92ea-9a1fdbc8fea7.png)    

전에 작성한 내용과 같이 보시는 것을 추천합니다.    

[PID제어내용정리](https://pus0319.github.io/embedded_control/PIDCONTROL/)    



# 1. PID 위치 제어 기본코드
FreeRTOS환경에서 PID 위치 제어기(Function)을 만든 예제입니다.    

Basic Task 환경에서 PID 위치 제어기(Function)을 주기적으로 호출하여 구현하였습니다. 
~~~c++

typedef struct _T_DC_PID_CONTROL_
{
  double dP;	// Kp
  double dI;	// Ki
  double dD;	// Kd
  int dAngle;	// 목표각도
  int dAngleSign;	// 0(+) : CCW , 1(-) : CW
}sDCPIDCTLMessage;	// <1>

typedef struct _T_DC_PID_ERROR_
{
  double Proportional;	// P값
  double Integral;	// I값
  double Differential;	// D값

  double LastError;	// D값 계산을 위한 단위시간(dt)전의 Error값
  uint32_t LastmsTick;	// Scheduling Latency를 고려한 단위시간(dt) 계산에 사용하는
                        // 마지막에 저장한 FreeRTOS 절대 Tick 값.
}sDCPIDERROR;		// <2>

/* main.c */

sDCPIDCTLMessage DCPIDCTLMsg;
sDCPIDERROR DCPIDERROR;

void PIDCTL_Task(void *argument)
{
  double CurrentMovedAngle;

  /***/

  if(PID_CTL_Parameter_Update_Flag)
  {
    Set_PIDCTL(&DCPIDCTLMsg);
  }
  
  /***/
  
  if(PID_POS_Start_Flag)	// <3>
  {
    ResetCurrentMovedEncoder();	
    ResetPIDERRORValue(&DCPIDERROR);
    Start_Motor();
    DCPIDERROR.LastmsTick = osKernelGetTickCount();
  }
  
  /***/
  
  if(Is_Start_Motor() == 1)	// <4>
  {
    PID_Relative_Cal_Encoder_to_Angle(&CurrentMovedAngle);	// <5>

    if(PIDPOSController(DCPIDCTLMsg,CurrentMovedAngle,&DCPIDERROR))	// <6>
    {
      //PID Pos Control Complete.
      //send CompleteMessage to UI
      DCPIDSTATEMsg.uState = MSTOP;
    }
      
    osMessageQueuePut(PIDCANGLEQueueHandle,(&CurrentMovedAngle),0,0);    // <7>  
  }
  
  osDelay(10);	// <8>
}
~~~    

* 먼저, kp, Ki, Kd, 목표각도 등에 대한 구조체(UI로부터 Update됨)<1>와    
  PID 제어기에서 단위 시간(dt)마다 갱신하는 각종 계산 값에 대한 구조체<2>가 필요합니다.
  
* PID Parameter 설정 후, <3>과 같이 UI로부터 START신호를 받으면,    
  움직인 각도(현재각도)값과 PID 을 Reset하고 모터의 Break를 해제합니다.    
  초기에 단위시간(dt)가 너무 크게 나오지 않도록 FreeRTOS 절대 Tick값으로 대입합니다.
  
* 모터의 Break를 해제 후, 본격적으로 PID 위치 제어기를 사용합니다.
    * 단위시간(dt) = 약 10Ticks(10ms)
        * <8>과 같이 PID 위치 제어기를 단위시간(dt)마다 주기적으로 실행하도록 합니다.
        * 10Ticks마다 실행하도록 설정했지만,    
          Multitasking환경에서 실제로는 10Ticks보다 살짝 더 높습니다.    
	  이러한 현상을 **Scheduling Latency** 라고 합니다.
	* 그래서, **Scheduling Latency를 고려한 실제 단위시간(dt)** 을    
	  PID 위치 제어기 안에서 따로 구해야합니다.    
	  (자세한 내용은 PID 위치 제어기 함수 내용 참고)

* PID 위치 제어기를 수행하기에 앞서, <5>와 같이 Count된 Encoder 값을    
  움직인 현재각도 값으로 변환합니다.

* <6>과 같이 PID 위치 제어기를 통해 PID 위치제어를 수행합니다.    
  PID 위치 제어기에 필요한 argument는 크게 아래와 같습니다.
    1. PID parameter(Kp, Ki, Kd, 목표각도값) : DCPIDCTLMsg
    2. 현재각도 값 : CurrentMovedAngle
    * 단위 시간(dt)마다 갱신하는 각종 계산 값 또한 필요합니다.

* PID 위치제어를 수행 후 현재각도 값을 UI에 표시하기 위해 <7>과 같이    
  UI System으로 현재각도 값을 Message queue를 이용하여 전송합니다.    
  
아래부터는 실제 PID 위치 제어기의 구현 내용입니다.    

~~~c++
uint8_t PIDPOSController(sDCPIDCTLMessage aDCPIDCTLMsg, double aCurrentMovedAngle, sDCPIDERROR* pDCPIDERROR)
{
	/******** <9> ********/
	double kP = aDCPIDCTLMsg.dP;
	double kI = aDCPIDCTLMsg.dI;
	double kD = aDCPIDCTLMsg.dD;
	double SetMovedAngle = (double)(aDCPIDCTLMsg.dAngle * ((aDCPIDCTLMsg.dAngleSign == 0)?(1):(-1)));
	uint32_t CurrentmsTick = osKernelGetTickCount();
	double ElapsedmsTick;
	double mstosec = 0.001;
	double OutputPID;
	/*********************/
	
	/******** <9> ********/	
	if(CurrentmsTick > pDCPIDERROR->LastmsTick)
	{
		ElapsedmsTick = (double)(CurrentmsTick - pDCPIDERROR->LastmsTick);
	}
	else if(CurrentmsTick < pDCPIDERROR->LastmsTick)
	{
		ElapsedmsTick = (double)(0xFFFFFFFF - pDCPIDERROR->LastmsTick + CurrentmsTick);
	}
	else
	{
		ElapsedmsTick = 1;
	}
	ElapsedmsTick = mstosec * ElapsedmsTick;
	/*********************/

	/******** <10> ********/	
	pDCPIDERROR->Proportional = SetMovedAngle - aCurrentMovedAngle;
	/*********************/
	
	/******** <11> ********/
	pDCPIDERROR->Integral += ((pDCPIDERROR->Proportional) * ElapsedmsTick);
	/*********************/
	
	/******** <12> ********/
	pDCPIDERROR->Differential = (((pDCPIDERROR->Proportional) - (pDCPIDERROR->LastError)) / ElapsedmsTick);
	/*********************/

	/******** <13> ********/
	OutputPID = kP*(pDCPIDERROR->Proportional) + kI*(pDCPIDERROR->Integral) + kD*(pDCPIDERROR->Differential);
	/*********************/
	
	/******** <14> ********/
	if(OutputPID > 0)
	{
		HAL_GPIO_WritePin(DCMDIR_GPIO_Port, DCMDIR_Pin, GPIO_PIN_RESET);//CCW
		if(OutputPID > 1000)
		{
			OutputPID = 1000;
		}
	}
	else if(OutputPID < 0)
	{
		HAL_GPIO_WritePin(DCMDIR_GPIO_Port, DCMDIR_Pin, GPIO_PIN_SET);//CW
		if(OutputPID < -1000)
		{
			OutputPID = -1000;
		}
		OutputPID = fabs(OutputPID);
	}
	DCM_PWM_SetValue((uint16_t)OutputPID);
	/*********************/

	/******** <15> ********/	
	pDCPIDERROR->LastmsTick = CurrentmsTick;
	pDCPIDERROR->LastError = pDCPIDERROR->Proportional;
	/*********************/
	
	return 0;
}
~~~    

* 먼저 <8>와 같이 PID 위치 제어기에 사용해야하는 각종 parameter 값을 읽습니다.
* <9>와 같이 앞서 언급드린 대로 실제단위시간(dt)를 계산합니다.
    * **실제단위시간(dt) =**    
      **현재시간(CurrentmsTick)** - **이전에 수행한 PID 제어값(MV)계산 후의 절대시간(LastmsTick)**
    * osKernelGetTickCount();를 통해 받는 값은 unsigned long type의 FreeRTOS의 절대 Tick 값입니다.    
      FreeRTOS의 절대 Tick 값은 0xFFFFFFFF 이후 OverFlow되어 0x00000000되기 때문에 이를 고려하여    
      실제단위시간(dt)가 잘못 계산되는 일이 없도록 해야합니다.
    * 실제 Tick값은 ms단위이기 때문에 0.001을 곱셈하여 sec단위로 바꿨습니다.    
      (굳이 단위전환하지 않고 Gain 값을 적절하게 하면 되긴합니다.)
      
* 본격적으로 <10> 부터 <13>까지 PID 제어값(MV)계산을 수행합니다.
1. <10>과 같이 P를 계산합니다. 전에 소개해드린 P의 공식은 아래와 같습니다.    

![image](https://user-images.githubusercontent.com/79636864/114825345-120e1200-9e01-11eb-8456-d68ceff48226.png)    

* 여기서 **e(t)** 는 경과시간에 따른 오차(Error)입니다.
* **e(t)** 는 시간에 대한 함수로 표현하였지만,    
  실제로는 아래와 같이 구할 수 있습니다.
    * **e(t)** == 기준입력( **r** , 목표값) - 측정된 출력( **r_now**, 실제측정값)
    * 코드에서는 기준입력은 'SetMovedAngle' 이고 측정된 출력은 'aCurrentMovedAngle' 입니다.

2. <11>과 같이 I를 계산합니다. 전에 소개해드렸던 I의 공식은 아래와 같습니다.    

![image](https://user-images.githubusercontent.com/79636864/114826145-1686fa80-9e02-11eb-8e86-f99ae003c271.png)    

* 적분항은 코드로 어떻게 구현해요? 라는 의문점이 들 것입니다.
* 위의 I의 공식은 더 정확하게 표현하면 '정적분' 이고 
  '정적분'은 아래와 같이 **리만합의 극한** 을 통해 표현할 수 있습니다.    
  
![image](https://user-images.githubusercontent.com/79636864/114829538-286a9c80-9e06-11eb-8c54-7cbdfe9cf59a.png)    

* 코드에서 **e(tn)** 은 앞서 계산한 P 이고, **Δt** 는 앞서 계산한 실제단위시간(dt) 입니다.

3. <12>와 같이 D를 계산합니다. 전에 소개해드렸던 D의 공식은 아래와 같습니다.    

![image](https://user-images.githubusercontent.com/79636864/114829846-84352580-9e06-11eb-8a06-01ab00bbe33b.png)    

* 그럼 미분항은 코드로 어떻게 구현해요? 라는 의문점 또한 들 것입니다.
* 위의 D의 공식의 의미는 **오차변화율** 을 구하는 것입니다.    

![image](https://user-images.githubusercontent.com/79636864/114833416-85685180-9e0a-11eb-8e6f-22fc9ae128dd.png)    

* 코드에서 **e(tnow)** 는 앞서 계산한 P 이고, **e(t)** 는 이전에 계산한 P 이며,    
  **Δt** 는 앞서 계산한 실제단위시간(dt) 입니다.
  
4. <13>과 같이 P,I,D 값을 각 각의 Gain에 곱하여 모두 더하여 최종적인 MV값(OutputPID)을 구합니다.

* OutputPID는 위의 예제 기준으로 엄밀히 말하면    
  내가 움직이고 싶은 각도값을 모터(플랜트)가 알아먹을 수 있도록 변환한 값입니다.    
``` 나의 목표각도는 이렇고 현재각도는 이러한데 단위시간(dt)동안 'OutputPID'만큼 모터를 돌려라 ```    
    * 'OutputPID'값을 그대로 DC 모터를 제어하기 위한 PWM신호 값을 설정하는데 사용하기 때문에    
      <14>와 같이 정해진 PWM 신호 범위 내에 DC 모터를 제어하도록 해야합니다.
      
* <15>와 같이 D를 계산하기 위해 앞서 계산한 P을 이전 계산한 P로,    
  '현재시간'을 '이전시간'으로 사용하기 위해 대입하여 저장합니다.
  




