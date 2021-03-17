---
date: 2021-03-17
title: "STM32F407_FreeRTOS_7_메세지큐(MessageQueue)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_7_메세지큐(MessageQueue)
=============

오늘은 메세지큐(MessageQueue)에 대해 정리했습니다.    

Task간 Data를 공유하기 위해 할 수 있는 가장 쉬운 방법은    
공유 Data를 Global Resource로 만들어 사용하는 것입니다.    

하지만, 이럴 경우 상호배제(Mutual Exclusion)기법 등이 필요한 경우가 생깁니다.

굳이 Task간 Data를 공유만할 건데 Global Resource로 만들어서 문제가 생길 수 있는    
가능성을 만들 필요는 없습니다.    

여기서 나온 개념이 메세지큐(MessageQueue)입니다.

# 1. 메세지큐(MessageQueue)
## 1.1 메세지큐(MessageQueue) 란?
* 태스크 메세지(데이터)를 주고받기 위한 Queue형태의 오브젝트

![image](https://user-images.githubusercontent.com/79636864/111404099-8d50ab00-8711-11eb-8e22-89456bebfc1a.png)

* 여러 Task는 하나의 Queue에 item을 넣을 수 있음.
    * 실제로는 넣을 item의 Data를 복사하여 Queue의 Back에 넣음(Enqueue).
* 마찬가지로 여러 Task는 하나의 Queue에 item을 가져올 수 있음.
    * 가장 먼저 들어있는 item을 Front까지 가져와서 가져올 item의 Data를 복사하여 Task에 넘김.
* Queue가 꽉찬 경우, item을 넣을 수 없음.
    * item을 Put시, 꼭 확인할 것.

## 1.2 메세지큐(MessageQueue) vs 메일박스(MailBox)

![image](https://user-images.githubusercontent.com/79636864/111404119-93468c00-8711-11eb-81c1-d05f80e13b57.png)

* 공통점
    * 모두 Queue로 구성.
* 차이점
    * 메세지큐(MessageQueue)
        * Get(), Put(), Peek() 등과 같이 기본적인 API 제공
    * 메일박스(MailBox)
        * Get(), Put(), Peek() + Memory Blocks를 alloc / free 기능 제공
        * 서로 다른 Task 간에 pointer만을 이동 시켜서 사용 가능함.  

# 2. 메세지큐(MessageQueue) API 정리
## 2.1 xQueueCreate()

![image](https://user-images.githubusercontent.com/79636864/111404221-ba9d5900-8711-11eb-9743-e43080889f96.png)

## 2.2 xQueueSend()

![image](https://user-images.githubusercontent.com/79636864/111404247-c852de80-8711-11eb-8123-ab953e41cb6d.png)

## 2.3 uxQueueMessagesWaiting()

![image](https://user-images.githubusercontent.com/79636864/111404269-cee15600-8711-11eb-81b0-a2614d90185f.png)

## 2.4 xQueueReceive()

![191](https://user-images.githubusercontent.com/79636864/111408643-ebcd5780-8718-11eb-9858-e33323869628.png)

# 3. 메세지큐(MessageQueue)를 활용한 예제
## 3.1 메세지큐(MessageQueue) 기본
2개의 서로 다른 Task가 Message를 주고 받는 가장 기본적인 예제입니다.    
하나의 Task가 메세지를 보내면, 다른 Task가 그 메세지를 받고 printf()로 출력하는 예제입니다.

### 3.1.1 소스코드
* 먼저, 주고 받을 메세지 struct를 정의합니다.

~~~c
typedef struct _T_MESSAGE
{
	char * String;
	uint16_t uX;
	uint16_t uY;
	uint16_t uZ;
}
sTaskMessage;
~~~

* 아래와 같이 init합니다.

~~~c
void MX_FREERTOS_Init(void) {
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";
  char * myTask03_Argument = "Taskname : myTask03";

  osMessageQDef(myQueue01, 16, sTaskMessage);
  myQueue01Handle = osMessageCreate(osMessageQ(myQueue01), NULL);

  osThreadDef(myTask01, LEDTask_LD4, osPriorityRealtime, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  osThreadDef(myTask02, LEDTask_LD3, osPriorityNormal, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  printf("OK\r\n");
}
~~~

* MessageQueue를 사용하기 위해 item의 sizeof() 및 최대 item 수를 정의합니다.

~~~c
  osMessageQDef(myQueue01, 16, sTaskMessage);
~~~

* MessageQueue를 생성하고 관련 Handle을 Return 받습니다.
* 이 Handle은 생성한 MessageQueue를 제어하는데 사용합니다.

~~~c
  myQueue01Handle = osMessageCreate(osMessageQ(myQueue01), NULL);
~~~

* 2개의 Task에 대해 아래와 같이 소스코드를 작성합니다.

~~~c
void LEDTask_LD4(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  sTaskMessage *pRMessage;
  osEvent eRValue;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  xLastCurrentTime = osKernelSysTick();
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

  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	task1_LED_Active_Flag = 0;	


	osDelayUntil(&xLastCurrentTime,500);
  }
}

void LEDTask_LD3(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;

  sTaskMessage sMessage;

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

	  osMessagePut(myQueue01Handle,(uint32_t)(&sMessage),100);
	  printf("SendMessage : LD3 -> LD4 \r\n");

 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_RESET);
 	  task2_LED_Active_Flag = 0;  

 	osDelayUntil(&xLastCurrentTime,500);
  }
}
~~~

* LEDTask_LD4()는 Message Receive, LEDTask_LD3()는 Message Send에 대한 기본 동작의 내용이    
  들어있습니다. 

### 3.1.2 결과 및 고찰
* 실제 printf() 출력 Log에 대한 결과입니다.

![image](https://user-images.githubusercontent.com/79636864/111409273-f805e480-8719-11eb-96db-352a2aa93074.png)

* 고찰
    1. Message Receive를 하는 Task1이 먼저 시작하고 Queue에 메세지가 없는 것을 확인하여    
       'osOK'를 Return 하였습니다.
        * 만약, Timeout으로 설정 시, Timeout동안 해당 Task는 Blocked state로 전환됩니다.    
          Timeout동안에도 Queue에 메세지가 없는 경우 'osEventTimeout'을 Return 합니다.
    2. Message Send를 하는 Task2가 시작되고 Queue에 메세지를 Put후 Delay함수에 의해 Blocked state로 전환됩니다.
    3. 다시 Task1이 시작되고 이번엔 Queue에 메세지가 있는 것을 확인하였고    
       해당 메세지를 printf()로 출력합니다.
    4. 그후 Task2에서는 다른 Value의 메세지를 보내고    
       Task1은 그 메세지를 받아 출력하는 동작을 반복합니다.
  
 # 4. 정리
 메세지큐의 정의, API, 예제를 정리했습니다.    
 추가 내용이나 예제가 있을 시 update하겠습니다.
 


