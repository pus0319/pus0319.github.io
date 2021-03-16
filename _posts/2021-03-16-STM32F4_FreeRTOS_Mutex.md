---
date: 2021-03-16
title: "STM32F407_FreeRTOS_6_뮤텍스(Mutex)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_6_뮤텍스(Mutex)
=============

세마포어(Semaphore)에 이어 뮤텍스(Mutex)에 대해 정리하였습니다.    

뮤텍스를 정리하면서 서로 다른 Priority의 Multitasking 시, 일어날 수 있는    

우선순위 역전(Priority Inversion)과 이를 해결하기 위한 우선순위 상속(Priority Inheritance)에 대해    

정리하였습니다.

# 1. 뮤텍스(Mutex)
## 1.1 뮤텍스(Mutex) 란?
* Binary Semaphore 중 하나
* 기본적인 동작은 Binary Semaphore와 같으나 **우선순위 상속 메커니즘** 이 포함됨.
    * Priority가 더 높은 다른 Task가 동일한 Mutex에 대해 'take' 시도 시,    
      Mutex를 이미 가지고 있는 Task가 더 높은 Priority를 가지게 됨.
    * Mutex를 'give'시, Task는 상속해제(원래 Priority로 돌아감)
    * 상호 배제에 사용되는 Mutex를 얻은 Task는 항상 마지막에 Mutex를 'give' 해야함.

# 2. 뮤텍스(Mutex) API 정리

![image](https://user-images.githubusercontent.com/79636864/111270958-e1a54d80-8673-11eb-91a2-5e05bd7ef74a.png)

# 3. 뮤텍스(Mutex)를 활용한 예제
## 3.1 우선순위 역전(Priority Inversion)과 이를 해결하기 위해 Mutex를 사용한 우선순위 상속(Priority Inheritance)
* Priority가 각 각다른 총 3개의 Task에서 일어날 수 있는 경우를 예제로 작성했습니다.

### 3.1.1 소스코드
* 먼저 아래와 같이 소스코드를 작성하였습니다.

~~~c
void MX_FREERTOS_Init(void) {
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";
  char * myTask03_Argument = "Taskname : myTask03";

  /* Create the mutex(es) */
  /* definition and creation of myMutex01 */
  osMutexDef(myMutex01);
  myMutex01Handle = osMutexCreate(osMutex(myMutex01));

  /* definition and creation of defaultTask */
  osThreadDef(myTask01, LEDTask_LD4, osPriorityRealtime, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  /* definition and creation of myTask02 */
  osThreadDef(myTask02, LEDTask_LD3, osPriorityAboveNormal, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  /* definition and creation of myTask03 */
  osThreadDef(myTask03, LEDTask_LD5, osPriorityNormal, 0, 128);
  myTask03Handle = osThreadCreate(osThread(myTask03), (void *)myTask03_Argument);

  printf("OK\r\n");
}
~~~

* CMSIS_RTOS_API를 이용하여 뮤텍스를 definition 하였습니다.    
~~~c
osMutexDef(myMutex01);
~~~

* 그후 뮤텍스를 Create하고 Return 값을 전용 Handle에 대입하였습니다.
* 전용 handle을 통해 해당 뮤텍스를 제어할 수 있습니다.    
~~~c
myMutex01Handle = osMutexCreate(osMutex(myMutex01));
~~~

* 총 3개의 Task를 아래와 같이 작성하였습니다.

~~~c
void LEDTask_LD4(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  int i;
  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  for(;;)
  {
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_SET);
	task1_LED_Active_Flag = 1;
	for(i=0;i<15;i++)
	{
		if(i==7)
		{
			//if(osSemaphoreWait(myBinarySem01Handle,osWaitForever) == osOK)
			if(osMutexWait(myMutex01Handle,osWaitForever) == osOK)
			{
				
			}
		}
		HAL_Delay(1);
	}
	
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	task1_LED_Active_Flag = 0;	
	//osSemaphoreRelease(myBinarySem01Handle);
	osMutexRelease(myMutex01Handle);
		
	osDelay(100);
  }
}

void LEDTask_LD3(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  int i;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  for(;;)
  {
 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_SET);
 	  task2_LED_Active_Flag = 1;
	  for(i=0;i<25;i++)
	  {
		  HAL_Delay(1);
	  }
 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_RESET);
 	  task2_LED_Active_Flag = 0;  

	osDelay(100);
  }
}

void LEDTask_LD5(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  int i;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  for(;;)
  {
  	  HAL_GPIO_WritePin(GPIOD,LD5_Pin, GPIO_PIN_SET);
	  task3_LED_Active_Flag = 1;
	  //if(osSemaphoreWait(myBinarySem01Handle,osWaitForever) == osOK)
	  if(osMutexWait(myMutex01Handle,osWaitForever) == osOK)
	  {
		  for(i=0;i<50;i++)
		  {
			  HAL_Delay(1);
		  }
	  }
	  HAL_GPIO_WritePin(GPIOD,LD5_Pin, GPIO_PIN_RESET);
	  task3_LED_Active_Flag = 0; 

	  //osSemaphoreRelease(myBinarySem01Handle);
	  osMutexRelease(myMutex01Handle);
	  osDelay(400);
  }
}
~~~

* Task1(High Priority)과 Task3(Low Priority)이 세마포어(뮤텍스)를 이용하는 동작이 있습니다.

### 3.1.2 출력 및 고찰

* 먼저 세마포어로 구현했을 때 결과는 아래와 같습니다.

![image](https://user-images.githubusercontent.com/79636864/111273738-85dcc380-8677-11eb-9d54-06a78af29342.png)

* 고찰
    1. Task3이 세마포어를 'take' 한 후, Task1은 세마포어를 Take 하기 위해 Wait 하고 있습니다.
    2. Task1이 Wait 상태를 벗어나기 위해서는 어서 Task3이 세마포어를 'give'해야하는데    
       그 중간에 Task2가 실행되고맙니다.
    3. High Priority의 Task1이 실행 중인데 Medium Priority의 Task2가 실행한 상황이 됩니다.    
       Kernel의 Scheduling 정책 상, Priority가 가장 높은 것부터 실행동작을 해야하는데 이를 거스르게 되었습니다.    
       이를 **우선순위 역전(Priority Inversion)** 이라 합니다.
    4. 결국, Task2가 실행종료되고 Task3이 실행종료 후 세마포어를 'give' 한후 Task1가 나머지를 실행 후 종료합니다.
    5. 가장 중요한 Task1이 Priority가 낮은 Task2가 실행되는 것을 차마 볼 수 밖에 없는 아이러니한 상황이였습니다.

* 세마포어로 구현된 내용을 그대로 뮤텍스로 바꾸면 결과는 아래와 같습니다.

![image](https://user-images.githubusercontent.com/79636864/111273771-90975880-8677-11eb-84a3-22e75e15e689.png)

* 고찰
    1. Task3이 뮤텍스를 'take' 한 후, Task1은 뮤텍스를 'take' 하기 위해 Wait 하고 있습니다.    
       이때, 뮤텍스를 가지고 있는 Task3의 Priority가 뮤텍스를 'take'하기 위해 Wait하는 Task1보다 더 높아지게 됩니다.    
       이를 **우선순위 상속(Priority Inheritance)** 이라 합니다.
    2. Task3은 모든 Task들 중 Very High Priority를 가지게 되었고 이로 인해 Task2는 Task3보다 Priority가 낮기 때문에    
       실행을 할 수 없게 됩니다.
    3. Task3이 실행 종료되고 뮤텍스를 'give'합니다. 이때, Task3의 Priority는 다시 원상 복구 됩니다.
    4. Wait하고 있던 Task1이 뮤텍스를 'take'하고 나머지를 실행후 종료합니다.
    5. Task1이 종료후 한참전에 Ready state로 있었던 Task2가 실행 후 종료합니다.
    6. 가장 중요한 Task1이 Priority가 낮은 Task2보다 먼저 실행 후 종료한 것을 확인할 수 있었습니다.

# 4. 정리

뮤텍스의 정의 및 활용에 대해 정리하였습니다.    
우선순위 역전의 현상에서 이를 해결하는 것 외에도 상호배제를 위해 사용하는 것으로도 좋은 방법임을 느꼈습니다.    
예제의 경우는 추가적인 내용이 있을 시 지속적으로 update할 예정입니다.





