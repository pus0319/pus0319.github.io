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


# 1. PID 위치 제어 기본코드
FreeRTOS환경에서 PID 위치 제어기(Function)을 만든 예제입니다.    

~~~c++

typedef struct _T_DC_PID_CONTROL_
{
	double dP;
	double dI;
	double dD;
	int dAngle;
	int dAngleSign;	// 0(+) : CCW , 1(-) : CW
}sDCPIDCTLMessage;

typedef struct _T_DC_PID_ERROR_
{
	double Proportional;
	double Integral;
	double Differential;

	double LastError;
	uint32_t LastmsTick;
}sDCPIDERROR;

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
  
  if(PID_POS_Start_Flag)
  {
 			  ResetCurrentMovedEncoder();
			  Start_Motor();
			  DCPIDERROR.LastmsTick = osKernelGetTickCount();
  }
  
  /***/
  
  if(Is_Start_Motor() == 1)
  {
		  PID_Relative_Cal_Encoder_to_Angle(&CurrentMovedAngle);

		  if(PIDPOSController(DCPIDCTLMsg,CurrentMovedAngle,&DCPIDERROR))
		  {
			  //PID Pos Control Complete.
			  //send CompleteMessage to UI
			  DCPIDSTATEMsg.uState = MSTOP;
		  }
      
		  osMessageQueuePut(PIDCANGLEQueueHandle,(&CurrentMovedAngle),0,0);       
  }
  
  osDelay(10);
}
~~~    

* 
