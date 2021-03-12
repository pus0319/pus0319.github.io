---
date: 2021-03-12
title: "STM32F407_FreeRTOS_3_Task/Scheduler API,예제정리"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_3_Task/Scheduler API,예제정리
=============
  
이번 포스팅은 각종 Task예제를 통해 STM32CubeMX를 통해 생성한    
CMSIS_RTOS_API의 FreeRTOS 코드 내용 및 log 출력값을 정리했습니다.

FreeRTOS 외의 코드 내용은 제외하였습니다. 

# 1. Task / Scheduler API 정리
## 1.1 xTaskCreate()
![taskcreate](https://user-images.githubusercontent.com/79636864/110877503-dcb36780-831c-11eb-9d36-bdb1854c9891.jpg)
* Task를 만드는 기본적인 함수입니다.

## 1.2 vTaskDelay()
![image](https://user-images.githubusercontent.com/79636864/110877796-55b2bf00-831d-11eb-99c0-b360ddb8c7c1.png)
* 핵심은 Delay라는 동작이 'Task의 state를 Blocked 함'(CPU의 점유권을 넘긴다) 입니다.
* HAL_Delay와의 차이점
    * Polling 방식으로 Delay 측정(loop 방식)
    * FreeRTOS가 사용하는 Tick Timer보다 더 낮은 우선순위를 가짐.
        * 우선순위가 높은 Tick Timer 등에 의해 멈춤현상이 발생 -> 정확한 Delay를 하지 못함.
    * CPU 리소스를 점유하기 때문에 RTOS 사용 시는 osDelay() 함수를 이용해서 다른 스레드에게    
      제어권을 넘겨주는게 CPU 리소스를 효율적으로 사용 할 수 있음.
    * 실제로, HAL_Delay() 사용 시, 1ms 더 Delay 됨. 1로 해서 500 loop 돌렸는데 1.5초가 나옴.(???)    
      OS_Delay() 사용 시, us대 더 Delay 됨. 1로 해서 500 loop 돌렸는데 0.5초가 나옴.
     
## 1.3 vTaskDelayUntil()
![image](https://user-images.githubusercontent.com/79636864/110877814-6105ea80-831d-11eb-987a-9db8562ae2f8.png)
* Periodic task 구현에 사용할 수 있습닏.
 
## 1.4 xTaskDelete()
![image](https://user-images.githubusercontent.com/79636864/110878477-9fe87000-831e-11eb-8bfb-6f7c658c5adf.png)

## 1.5 vTaskPrioritySet()
![image](https://user-images.githubusercontent.com/79636864/110878509-aa0a6e80-831e-11eb-8eb2-67b6ef292e29.png)
* 'Note' 내용을 꼭 숙지할 것.

## 1.6 vTaskPriorityGet()
![image](https://user-images.githubusercontent.com/79636864/110878552-bee70200-831e-11eb-9e44-ef35bed575c4.png)

```
이외의 필요한 내용은 추후 Update 할 것
```

# 2. Task 예제 정리
## 2.1 MX_FREERTOS_Init()
* STM32CubeMX를 통해 생성하시면 'MX_FREERTOS_Init()'를 생성해줍니다.
~~~c
void MX_FREERTOS_Init(void) {
  /* USER CODE BEGIN Init */
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";
  char * myTask03_Argument = "Taskname : myTask03";
  /* USER CODE END Init */

  /* USER CODE BEGIN RTOS_MUTEX */
  /* add mutexes, ... */
  /* USER CODE END RTOS_MUTEX */

  /* USER CODE BEGIN RTOS_SEMAPHORES */
  /* add semaphores, ... */
  /* USER CODE END RTOS_SEMAPHORES */

  /* USER CODE BEGIN RTOS_TIMERS */
  /* start timers, add new ones, ... */
  /* USER CODE END RTOS_TIMERS */

  /* USER CODE BEGIN RTOS_QUEUES */
  /* add queues, ... */
  /* USER CODE END RTOS_QUEUES */

  /* Create the thread(s) */
  /* definition and creation of defaultTask */
  osThreadDef(myTask01, LEDTask_LD4, osPriorityNormal, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  /* definition and creation of myTask02 */
  osThreadDef(myTask02, LEDTask_LD3, osPriorityAboveNormal, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  /* definition and creation of myTask03 */
  osThreadDef(myTask03, LEDTask_LD5, osPriorityRealtime, 0, 128);
  myTask03Handle = osThreadCreate(osThread(myTask03), (void *)myTask03_Argument);

  /* USER CODE BEGIN RTOS_THREADS */
  /* add threads, ... */

  printf("OK\r\n");
  /* USER CODE END RTOS_THREADS */

}
~~~
* 맨 첫줄의 myTask01~03_Argument는 Task 생성 시, 매개변수로 넣어줄 것들입니다.    
  Task가 loop로 진입 전, 매개변수 값을 printf로 출력할 예정입니다.
* osThreadDef
![image](https://user-images.githubusercontent.com/79636864/110877260-821a0b80-831c-11eb-9337-d1e96ccff0bb.png)

* osThreadCreate에서 실제로 Task를 만들고 Handle을 Return 합니다.

## 2.2 Basic Task
아래와 같이 3개의 Task를 만들었습니다.
* myTask01
~~~c
void LEDTask_LD4(void const * argument)
{
  /* USER CODE BEGIN LEDTask_LD4 */
  char * temp = (char *)argument;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");
  
  /* Infinite loop */
  for(;;)
  {
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_SET);
	task1_LED_Active_Flag = 1;
	while(task1_osdelay_value < 500)
	{
    	osDelay(1);
		task1_osdelay_value++;
	}
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	task1_LED_Active_Flag = 0;	
	while(task1_osdelay_value > 0)
	{
    	osDelay(1);
		task1_osdelay_value--;
	}	
  }
  /* USER CODE END LEDTask_LD4 */
}
~~~
* myTask02
~~~c
void LEDTask_LD3(void const * argument)
{
  /* USER CODE BEGIN LEDTask_LD3 */
  char * temp = (char *)argument;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  /* Infinite loop */
  for(;;)
  {
 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_SET);
 	  task2_LED_Active_Flag = 1;
 	  while(task2_osdelay_value < 1000)
 	  {
 		  osDelay(1);
 		  task2_osdelay_value++;
 	  }
 	  HAL_GPIO_WritePin(GPIOD,LD3_Pin, GPIO_PIN_RESET);
 	  task2_LED_Active_Flag = 0;  
 	  while(task2_osdelay_value > 0)
 	  {
 		  osDelay(1);
 		  task2_osdelay_value--;
 	  }   
  }
  /* USER CODE END LEDTask_LD3 */
}
~~~
* myTask03
~~~c
void LEDTask_LD5(void const * argument)
{
  /* USER CODE BEGIN LEDTask_LD5 */
  char * temp = (char *)argument;

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  /* Infinite loop */
  for(;;)
  {
	  HAL_GPIO_WritePin(GPIOD,LD5_Pin, GPIO_PIN_SET);
	  task3_LED_Active_Flag = 1;
	  while(task3_osdelay_value < 1500)
	  {
		  osDelay(1);
		  task3_osdelay_value++;
	  }
	  HAL_GPIO_WritePin(GPIOD,LD5_Pin, GPIO_PIN_RESET);
	  task3_LED_Active_Flag = 0;  
	  while(task3_osdelay_value > 0)
	  {
		  osDelay(1);
		  task3_osdelay_value--;
	  }   

  }
  /* USER CODE END LEDTask_LD5 */
}
~~~

* 3개의 Task 모두 loop 진입 전 자신의 Priority 및 argument 값을 printf로 출력합니다.
* 간단한 LED ON/OFF 동작 후 일정 시간동안 osDelay() 동작을 수행합니다.
* 만약 3개의 Task 중 Priority가 겹치는 경우가 있을 경우,
  printf 출력이 겹치는 Task에 한해서 안나오는 경우가 발생합니다.    
  이는 동일한 우선순위 함수는 라운드 로빈 방식을 하기 때문에 전역 함수인 printf()이 겹치는 현상입니다.    
  -> 겹치는 Task가 동일한 시간에 printf()를 사용하지 못하게 하기 위해선 'Task간 동기화'를 해줘야합니다.    
     (이는, 추후 세마포어 및 뮤텍스 기능 설명을 통해 다루어 볼 것입니다.)

* 결과

![image](https://user-images.githubusercontent.com/79636864/110880942-c7413c00-8322-11eb-86f0-3a532010c260.png)
![image](https://user-images.githubusercontent.com/79636864/110881239-59e1db00-8323-11eb-93ff-d7ef43297f46.png)
![image](https://user-images.githubusercontent.com/79636864/110881563-e2f91200-8323-11eb-9c11-108d334c6b81.png)
![image](https://user-images.githubusercontent.com/79636864/110881744-32d7d900-8324-11eb-8300-a209d7ae9ea8.png)


* Scheduler가 처음 실행 시, High Priority인 Task부터 수행되는 것을 확인하였습니다.
* 간단한 LED ON/OFF동작이기에 'C'(최악의 실행시간)은 무시해도 됩니다.
* 바로 osDelay()를 실행하기 때문에 preemption도 일어나지 않습니다.
* 결론적으로, 각 Task의 LED 동작 및 Delay Count동작이 주기적으로 수행됨을 확인하였습니다.
* 먼저 수행을 시작한 myTask03의 Delay Count값이 높은 것으로 확인하였고    
  printf()동작이 약 5 Count 갭이 있음을 확인하였습니다.
  


