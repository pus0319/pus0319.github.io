---
date: 2021-03-12
title: "STM32F407_FreeRTOS_3_Task 예제정리"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_3_Task 예제정리
=============
  
이번 포스팅은 각종 Task예제를 통해 STM32CubeMX를 통해 생성한    
CMSIS_RTOS_API의 FreeRTOS 코드 내용 및 log 출력값을 정리했습니다.

FreeRTOS 외의 코드 내용은 제외하였습니다. 

# 1. FreeRTOS 초기화
## 1.1 MX_FREERTOS_Init()
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
