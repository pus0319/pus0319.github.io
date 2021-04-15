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
  움직인 각도(현재각도)값을 Reset하고 모터의 Break를 해제합니다.    
  초기에 단위시간(dt)가 너무 크게 나오지 않도록 FreeRTOS 절대 Tick값으로 대입합니다.
  
* 모터의 Break를 해제 후, 본격적으로 PID 위치 제어기를 사용합니다.
    * 단위시간(dt) = 약 10Ticks(10ms)
        * <8>과 같이 PID 위치 제어기를 단위시간(dt)마다 주기적으로 실행하도록 합니다.
        * 10Ticks마다 실행하도록 설정했지만,    
          Multitasking환경에서 실제로는 10Ticks보다 살짝 더 높습니다.    
	  이러한 현상을 Scheduling Latency라고 합니다.
	* 그래서, Scheduling Latency를 고려한 실제 단위시간(dt)을 PID 위치 제어기안에서    
	  따로 구해야합니다.
	  

  
