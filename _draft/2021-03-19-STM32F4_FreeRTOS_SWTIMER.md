---
date: 2021-03-19
title: "STM32F407_FreeRTOS_9_S/W 타이머(Software Timer)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_9_S/W 타이머(Software Timer)
=============

FreeRTOS에 대한 기본적인 내용들은 거의 정리된거 같습니다.

이번 포스팅은 S/W타이머(Software Timer)에 대해 정리하였습니다.

저번에 예제로 DelayUntil()함수로 구현한 Periodic Task와 맥락은 비슷하지만,

S/W Timer는 Periodic, One-Shot 모두 쓸 수 있고 DelayUntil()보다 정확하다는 점이 있습니다.

그 외의 여러 이점이 있으니 자주 애용할거 같습니다.

# 1. S/W 타이머(Software Timer)
## 1.1 S/W 타이머(Software Timer) 란?
* FreeRTOS에서 제공하는 Task 유형 중 하나.
* Timer 기능을 탑재한 Task(Callback Function)를 만들 수 있는 기능.
    * 원하는 시기에 원하는 Period로 Timer를 Start 및 Stop 가능.
    * Periodic, One-Shot 모두 지원.
    * Callback은 Timer Service의 Context에서 실행.
    * 단, Callback함수 내에서는 Block을 시도해서는 안됨.
        * vTaskDelay (), vTaskDelayUntil ()을 호출,    
          Queue Semaphore API 사용 시, Block time 지정

![image](https://user-images.githubusercontent.com/79636864/111714346-c9f8df80-8894-11eb-98fc-c84af4009081.png)

## 1.2 S/W 타이머(Software Timer) 쓰임새(예시)
1. 각 Task에 대한 Watchdog 기능으로 사용.
    * 각 Task가 실행 완료 시, Timer를 Start(expiry할 때까지 갱신)
    * 만약, Task 하나가 expiry를 갱신하지 못할 시, Task가 중지되었고 판단. 해당 Task를 deinit하여 재시작.
2. Delay함수 대신 사용.
     * Delay함수는 해당 Task의 state를 Blocked으로 전환하기때문에 'Task가 죽어있다'라고 해도 무방.
     * Timer Task를 이용하여 expiry를 Delay Time으로 설정해서 사용한다면 Task의 state를 바꾸지 않아도 됨.
3. 그외 예시 추가바람.

# 2. S/W 타이머(Software Timer) API 정리
## 2.1 Software timer definitions

![image](https://user-images.githubusercontent.com/79636864/111715011-4213d500-8896-11eb-951f-1ceda5459223.png)

* configUSE_TIMERS 가 1 일 경우에만 사용 가능.
* (중요)주목해야할 점은 configTIMER_TASK_PRIORITY가 일반 TASK보다 높아야 정확한 Timing으로 동작할 수 있음.

## 2.2 xTimerCreate()

![image](https://user-images.githubusercontent.com/79636864/111715290-d9792800-8896-11eb-811d-1c5aa57cd71c.png)

## 2.3 xTimerChangePeriod()

![image](https://user-images.githubusercontent.com/79636864/111715357-fd3c6e00-8896-11eb-8886-c57d7bf86ee0.png)

## 2.4 xTimerStop()

![image](https://user-images.githubusercontent.com/79636864/111715415-1d6c2d00-8897-11eb-99c7-efcae25bbf1c.png)

## 2.5 xTimerDelete()

![image](https://user-images.githubusercontent.com/79636864/111715436-2957ef00-8897-11eb-8504-9a8bf584e1f2.png)

# 3. S/W 타이머(Software Timer)를 활용한 예제
## 3.1 S/W 타이머(Software Timer)를 이용한 Periodic 및 One-Shot Task 구현.
### 3.1.1 소스코드
* 먼저 아래의 내용과 같이 Timer Task의 Priority를 구현된 모든 Task보다 높게 설정해줍니다.    
  Timer Task가 구현된 모든 Task를 preempt 할 수 있게 설정하여 정확한 Timing으로 동작하도록 하고자합니다.

~~~c
#define configTIMER_TASK_PRIORITY  ( 4 )
~~~

* 아래와 같이 소스코드를 작성하였습니다.

~~~c
void MX_FREERTOS_Init(void) {
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";

  osTimerDef(myTimer01, Callback01);
  myTimer01Handle = osTimerCreate(osTimer(myTimer01), osTimerPeriodic, NULL);

  osTimerDef(myTimer02, Callback02);
  myTimer02Handle = osTimerCreate(osTimer(myTimer02), osTimerOnce, NULL);

  osMessageQDef(myQueue01, 16, sTaskMessage);
  myQueue01Handle = osMessageCreate(osMessageQ(myQueue01), NULL);

  osThreadDef(myTask01, LEDTask_LD4, osPriorityRealtime, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  osThreadDef(myTask02, LEDTask_LD3, osPriorityNormal, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  printf("OK\r\n");
}
~~~

* 먼저 S/W Timer의 name, Callback함수에 대해 definition을 합니다.

~~~c
  osTimerDef(myTimer01, Callback01);
~~~

* S/W Timer를 생성합니다.

~~~c
  myTimer01Handle = osTimerCreate(osTimer(myTimer01), osTimerPeriodic, NULL);
~~~

* 아래는 Task들의 소스코드입니다.

~~~c
void LEDTask_LD4(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  sTaskMessage *pRMessage;
  osEvent eRValue;
  int i;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  xLastCurrentTime = osKernelSysTick();

  osTimerStart(myTimer01Handle,1000);

  for(;;)
  {
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_SET);
	task1_LED_Active_Flag = 1;

	eRValue = osMessageGet(myQueue01Handle,0);
	if(eRValue.status == osEventMessage)
	{
		pRMessage = eRValue.value.p;
		printf("ReceiveMessage from LD3 OK \r\n");
		printf("string : %s",pRMessage->String);
		printf("X : %d \r\n",pRMessage->uX);
		printf("Y : %d \r\n",pRMessage->uY);
		printf("Z : %d \r\n",pRMessage->uZ);
	}
	else if((eRValue.status == osEventTimeout) || (eRValue.status == osOK))
	{
		printf("ReceiveMessage from LD3 Timeout \r\n");
	}
	
	for(i=0;i<10;i++)
	{
		HAL_Delay(1);
	}

  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	task1_LED_Active_Flag = 0;	


	osDelayUntil(&xLastCurrentTime,1000);
  }
}

void LEDTask_LD3(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  sTaskMessage sMessage;
  int i;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  sMessage.String = "Hello. My Name is LD3\r\n";
  sMessage.uX = 10;
  sMessage.uY = 20;
  sMessage.uZ = 30;

  xLastCurrentTime = osKernelSysTick();
  for(;;)
  {
 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_SET);
 	  task2_LED_Active_Flag = 1;

	  (sMessage.uX)++;
  	  (sMessage.uY)++;
	  (sMessage.uZ)++;

	  osMessagePut(myQueue01Handle,(uint32_t)(&sMessage),0);
	  printf("SendMessage : LD3 -> LD4 \r\n");

	  for(i=0;i<300;i++)
	  {
		HAL_Delay(1);
	  }

 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_RESET);
 	  task2_LED_Active_Flag = 0;  

 	osDelayUntil(&xLastCurrentTime,1000);
  }
}
~~~

* Task1은 loop 진입 전, Periodic Timer1 Task를 1000ms Period로 Start 합니다.    


* 아래는 외부 Button이 눌렸을 때 EINT ISR에서 호출하는 CallBack 함수 및 Timer CallBack 함수 입니다.

~~~c
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
	gtask_set_value = 1;
	printf("[%02d:%02d:%02d:%02d:%03d]\r\n", m_SysTime.DAY,
											  m_SysTime.HOUR,
											   m_SysTime.MIN,
											  m_SysTime.SEC,
											  m_SysTime.MSEC);

	osTimerStart(myTimer02Handle,100);
	gtask_set_value = 0;
	portYIELD_FROM_ISR(pdTRUE);
}
void Callback01(void const * argument)
{
	gSWTIMER_P_Flag = 1;
	printf("P\r\n");
	gSWTIMER_P_Flag = 0;	
}
void Callback02(void const * argument)
{
	gSWTIMER_O_Flag = 1;
	printf("O\r\n");
	gSWTIMER_O_Flag = 0;	
}
~~~

* 외부 Button이 눌렸을 경우, One-Shot Timer2 Task를 100ms expiry후 호출하도록 합니다.
* 각 각의 Timer Callback 함수는 호출했을 경우, printf() 출력 동작을 합니다.    

### 3.1.2 결과 및 고찰
* 아래는 결과에 대한 Log 출력입니다.

![image](https://user-images.githubusercontent.com/79636864/111716940-6671b080-889a-11eb-95c3-ba1cb2299901.png)

![image](https://user-images.githubusercontent.com/79636864/111716949-6a9dce00-889a-11eb-8387-f1a6aae64401.png)

* 위의 설명과 같이 출력됨을 확인하였습니다.

# 4. 정리

FreeRTOS에서 지원하는 S/W Timer에 대해 정리했습니다.

어떻게 쓰느냐에 따라서 여러 경우에 응용할 수 있고    
그 어떤 API보다 강력한 API인거 같습니다.

추가 내용 및 예제는 주기적으로 update할 예정입니다.





