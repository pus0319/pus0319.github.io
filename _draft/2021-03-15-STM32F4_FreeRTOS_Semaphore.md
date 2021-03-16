---
date: 2021-03-15
title: "STM32F407_FreeRTOS_5_세마포어(Semaphore)"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F407_FreeRTOS_5_세마포어(Semaphore)
=============

이전 포스팅은 Kernel System을 사용하는데 앞서 기본적으로 알아야하는 개념인    
상호배제(Mutual exclusion)에 대해 정리했습니다.

이번 포스팅은 상호배제에서 소개해드렸던 세마포어(Semaphore)에 대한 내용을 정리하고자합니다.

세마포어(Semaphore)는 상호배제외에도 쓰이는 다른 경우도 있으니 참고하면 될 것 같습니다.

# 1. 세마포어(Semaphore)
## 1.1 세마포어(Semaphore)란?
* 깃발 이란 뜻.
* 저장장치 내의 값을 통해 공유자원(Global Resource)에 대한 접근을 제어하는 방식.
* 세마포어 획득(Take,Wait,Pend)을 통해 공유자원에 대해 활용 권리 획득.
* 세마포어 반환(Give,Send,Post)을 통해 공유자원에 대해 활용 권리 반환.

![image](https://user-images.githubusercontent.com/79636864/111145471-9e8f9f80-85cb-11eb-8e2b-2827de2d4e39.png)

* 모든 Task는 Semaphore를 획득해야만 2.와 같이Global Resource에 접근 가능하다.
* 모든 Task는 Semaphore에 '열쇠'(Count)가 있을 경우, 1.과 같이 '열쇠'를 얻을 수 있다.(추상적인 표현임)

![image](https://user-images.githubusercontent.com/79636864/111145767-f75f3800-85cb-11eb-8ad1-b63df748a43b.png)

* Task3이 Semaphore의 '열쇠'를 얻은 상태.
* Task3만 2.와 같이Global Resource에 접근 가능하고 나머지 Task는 Task3이 Semaphore를 반환할때까지    
  설정 Delay Tick 만큼 Blocked state로 Wait하고 있다.
* 만약, 설정 Delay Tick만큼 경과해도 Task3이 Semaphore를 반환하지 않을 경우,    
  Wait하는 동작을 하지 않게된다.

## 1.2 세마포어(Semaphore) 분류
* 세마포어 반환 및 획득 할 수 있는 Task수에 따라
    * 1회 : Binary Semaphore, Mutex(추후 정리)
    * 1회 초과 : Counting Semaphore

# 2 세마포어(Semaphore) API 정리
## 2.1 vSemaphoreCreateBinary()
![image](https://user-images.githubusercontent.com/79636864/111146817-36da5400-85cd-11eb-8674-d9db1f94c48e.png)

## 2.2 xSemaphoreCreateCounting()
![image](https://user-images.githubusercontent.com/79636864/111146871-45287000-85cd-11eb-9031-39eaf0eac903.png)

## 2.3 xSemaphoreTake()
![image](https://user-images.githubusercontent.com/79636864/111149132-d26cc400-85cf-11eb-8652-076a2c5eaf7e.png)

## 2.4 xSemaphoreTakeFormISR()
![image](https://user-images.githubusercontent.com/79636864/111146971-5f624e00-85cd-11eb-919c-616c4a6a775b.png)

## 2.5 xSemaphoreGive()
![image](https://user-images.githubusercontent.com/79636864/111147012-67ba8900-85cd-11eb-813b-923e7d4d7870.png)

## 2.6 xSemaphoreGiveFormISR()
![image](https://user-images.githubusercontent.com/79636864/111147043-7012c400-85cd-11eb-9214-ebf64c5482a3.png)

## 2.7 vSemaphoreDelete()
![image](https://user-images.githubusercontent.com/79636864/111147080-76a13b80-85cd-11eb-995e-428afebe69c2.png)

* 나머지 API는 지속적으로 Update 예정

# 3 세마포어(Semaphore)를 활용한 예제
# 3.1 Periodic_MultiTasking 환경에서의 세마포어 활용 및 고찰
* 저번에 예제로 활용했던 Periodic_MultiTasking에서 세마포어를 활용하고    
  관련 Log를 출력하여 고찰을 해봤습니다.
  
# 3.1.1 소스코드
* 먼저 아래와 같이 소스코드를 작성하였습니다. 세마포어 외의 설명은 생략하겠습니다.
    * freertos.c
~~~c
void MX_FREERTOS_Init(void) {
  /* USER CODE BEGIN Init */
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";
  char * myTask03_Argument = "Taskname : myTask03";
  
  //생략
  
  /* Create the semaphores(s) */
  /* definition and creation of myBinarySem01 */
  osSemaphoreDef(myBinarySem01);
  myBinarySem01Handle = osSemaphoreCreate(osSemaphore(myBinarySem01), 1);

  /* Create the thread(s) */
  /* definition and creation of myTask01 */
  osThreadDef(myTask01, LEDTask_LD4, osPriorityRealtime, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  /* definition and creation of myTask02 */
  osThreadDef(myTask02, LEDTask_LD3, osPriorityHigh, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  /* definition and creation of myTask03 */
  osThreadDef(myTask03, LEDTask_LD5, osPriorityNormal, 0, 128);
  myTask03Handle = osThreadCreate(osThread(myTask03), (void *)myTask03_Argument);

  /* USER CODE BEGIN RTOS_THREADS */
  /* add threads, ... */

  printf("OK\r\n");
  /* USER CODE END RTOS_THREADS */

}
~~~

* CMSIS_RTOS_API를 이용하여 세마포어를 definition하였습니다.
~~~c
  osSemaphoreDef(myBinarySem01);
~~~

* 그후 세마포어를 Create하고 Return 값을 전용 Handle에 대입하였습니다.
* 전용 handle을 통해 해당 세마포어를 제어할 수 있습니다. 
~~~c
  myBinarySem01Handle = osSemaphoreCreate(osSemaphore(myBinarySem01), 1);
~~~

* 그후, 총 3개의 Periodic_Task를 아래와 같이 수정하였습니다.    
  (3개의 Task의 parameter는 이 전의 예제와 같기 때문에 설명은 생략하였고    
   세마포어만 설명하고자 하기 때문에 1개의 Task만 설명합니다.)
~~~c
void LEDTask_LD4(void const * argument)
{
  /* USER CODE BEGIN LEDTask_LD4 */
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  int i;
  //const TickType_t xPeriod = pdMS_TO_TICKS(500);

  printf("%s. pr : %d",temp,(int)osThreadGetPriority(NULL));
  printf("....loop start. \r\n");

  xLastCurrentTime = osKernelSysTick();
  /* Infinite loop */
  for(;;)
  {
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_SET);
    task1_LED_Active_Flag = 1;
    if(osSemaphoreWait(myBinarySem01Handle,0) == osOK)
    //if(osSemaphoreWait(myBinarySem01Handle,osWaitForever) == osOK)
    {
      for(i=0;i<15;i++)
      {
        gtask_set_value = 1;
        HAL_Delay(1);
      }
      osSemaphoreRelease (myBinarySem01Handle);
    }
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	  task1_LED_Active_Flag = 0;	
    
	  osDelayUntil(&xLastCurrentTime,100);
  }
}
~~~

* 각 Task의 실행시간(C) 동안, 공통된 Global Variable를 활용(변수대입)하도록 추가하였습니다.
* 각 Task의 실행시작 전, 세마포어를 통해 공통된 Global Variable를 접근할 수 있는지 체크 후,
  접근이 가능하면 실행시간(C)동안 Global Variable를 활용합니다.

# 3.1.2 출력 및 고찰
* 각 Task의 실행시작 시에만 Global Variable를 활용권(세마포어)을 'Give'하여 실행동작을 할 수 있으며
  어떤 Task가 Global Variable를 활용하는 동안 다른 Task가 접근하여 활용 시, Data Error로 가정하겠습니다.

* 먼저 소스코드에서 세마포어 기능을 빼고 출력한 Log입니다.

![image](https://user-images.githubusercontent.com/79636864/111245805-8b6ee500-8648-11eb-81b8-12558fc80894.png)

* 고찰
    1. 예상대로, Task3의 실행시간(C)동안, Task1과 Task2에 preempt합니다.
    2. preempt하면서 Task3이 활용중인 Global Variable에 대해 Task1과 Task2가 접근하면서    
       Data Error를 발생시킵니다.

* 소스코드에 세마포어 기능을 아래와 같은 내용으로 추가 후 출력한 Log입니다.
    * 세마포어 Wait time : 0 (Task 실행 동작 종료)

![image](https://user-images.githubusercontent.com/79636864/111245835-975aa700-8648-11eb-9c1e-e78ff601b4b7.png)

* 고찰
    1. Task3의 실행시간(C)동안, Task1만 preempt하였고    
       Task3의 실제 실행시간(C)이 parameter C와 거의 유사(약 1ms 차이)하게 나왔습니다.
       * Task1이 preempt하였지만 Global Variable를 활용권(세마포어)을 'Take' 하지 못해서 실행동작을 하지 않았고    
         Task1을 완료처리 후 바로 CPU 점유권을 Task3에 넘겨줬기 때문에 Task3의 나머지 실행시간(C)동안    
         Task1의 실행시작을 하지 않게 되었습니다.(Task3의 나머지 실행시간(C) < Task1 주기(P))
    2. Task1이 preempt하는 동안(짧은 시간) 이미 Task3이 Global Variable를 활용권(세마포어)을 쥐고 있었기 때문에
       Task1은 Global Variable을 활용하지 못했습니다.
    3. 모든 Task의 주기(P)는 이상이 없음을 확인하였습니다.
    4. 결론적으로 모든 Task가 접근하여 사용할 수 있는 Global Variable의 활용에 대해 하나의 Task만 할 수 있게됨을    
       확인할 수 있었습니다. **(Global Variable에 대한 상호배제 구현)**
 
* 추가적으로, 소스코드에 세마포어 기능을 아래와 같은 내용으로 변경 후 출력한 Log입니다.
    * 세마포어 Wait time : osWaitForever (Task 실행 동작은 무조건 완료 후 종료)

![image](https://user-images.githubusercontent.com/79636864/111245858-a17ca580-8648-11eb-84d5-c226795acd69.png)

* 고찰
    1. Task3의 실행 중, Task1이 preempt하였고 Global Variable를 활용권(세마포어)을 'Take'할 수 있을 때까지    
       대기(Wait) 합니다.
    2. Task3의 실행이 끝나고 바로 Task1이 활용권을 'Take'합니다.
        * 이때, 대기하고 있었던 Task1의 실행동작을 시작하게 되지만 Task3은 완료처리를 하지 못했습니다.    
          (실행은 다 했는데 완료처리를 하지 못한 상황)
    3. Task1의 실행이 끝났고 바로 Task1이 Global Variable를 활용권(세마포어)을 얻어 실행을 시작합니다.
        * FreeRTOS는 우선순위 기반 Schduling을 하기 때문에 가장 High Priority이고 Ready state인 Task    
          CPU점유권을 넘겨줍니다.
        * Task1의 실행이 끝난 시점에서 Task1의 실제 P 값이 parameter P 값보다 컸기 때문에 바로 Ready state로    
          전환이 되어 위의 조건이 충족되었습니다.
        * 하지만, Task1이 바로 CPU점유권을 확보했기 때문에 여전히 Task3은 완료처리를 하지 못했습니다.  
    4. Task1의 실행이 끝나고 Ready state인 Task2의 동작 완료 후 드디어 Task3이 완료처리를 하였습니다.
    5. High Priority인 Task1이 Low Priority인 Task3에 의해 Delay가 되었고    
       모든 Task가 그 영향을 받게 된 것을 확인하였습니다.
    6. 결론적으로 모든 Task에 대해 **Global Variable에 대한 상호배제 구현** 은 확인했지만    
       주기(P)는 이상이 있음을 확인하였습니다.
    7. Periodic_MultiTasking 환경에서 세마포어 Wait time을 osWaitForever형식으로 구현 한다면,    
       위와 같은 현상이 일어날 가능성이 매우 높은 점에 대해 고려해야합니다.

# 3.2 동일한 Priority의 Task간 동기화(Synchronization)를 위한 세마포어 활용 및 고찰
* 동일한 Priority의 Task에 대해 FreeRTOS는 라운드 로빈 Scheduling 방식을 적용합니다.    
  이에 따라 동일한 Priority의 Task들이 동시에 Global Resouce를 접근하여 활용하는 경우도 발생할 수 있습니다.
* 그래서 동일한 Priority의 Task들 간의 동기화(Synchronization)를 구현하여 Global Resouce의 Data Error를 방지하는 것이 필요합니다.

# 3.2.1 소스코드
* 먼저 아래와 같이 소스코드를 작성하였습니다. 세마포어 외의 설명은 생략하겠습니다.
    * freertos.c
~~~c
void MX_FREERTOS_Init(void) {
  /* USER CODE BEGIN Init */
  char * myTask01_Argument = "Taskname : myTask01";
  char * myTask02_Argument = "Taskname : myTask02";
  char * myTask03_Argument = "Taskname : myTask03";
  
  //생략
  
  /* Create the semaphores(s) */
  /* definition and creation of myBinarySem01 */
  osSemaphoreDef(myBinarySem02);
  myBinarySem02Handle = osSemaphoreCreate(osSemaphore(myBinarySem02), 1);

  /* Create the thread(s) */
  /* definition and creation of myTask01 */
  osThreadDef(myTask01, LEDTask_LD4, osPriorityNormal, 0, 128);
  myTask01Handle = osThreadCreate(osThread(myTask01), (void *)myTask01_Argument);

  /* definition and creation of myTask02 */
  osThreadDef(myTask02, LEDTask_LD3, osPriorityNormal, 0, 128);
  myTask02Handle = osThreadCreate(osThread(myTask02), (void *)myTask02_Argument);

  /* definition and creation of myTask03 */
  osThreadDef(myTask03, LEDTask_LD5, osPriorityNormal, 0, 128);
  myTask03Handle = osThreadCreate(osThread(myTask03), (void *)myTask03_Argument);

  /* USER CODE BEGIN RTOS_THREADS */
  /* add threads, ... */

  printf("OK\r\n");
  /* USER CODE END RTOS_THREADS */

}
~~~

* 3개의 동일한 Priority의 Task를 생성하였습니다.

* 각각의 Task는 loop 진입 전, Global Resouce인 'printf()'를 사용합니다.
~~~c
void LEDTask_LD4(void const * argument)
{
  char * temp = (char *)argument;
  TickType_t xLastCurrentTime;
  int i;

  if(osSemaphoreWait(myBinarySem02Handle,osWaitForever) == osOK)
  {
	printf("abcdefghijklmnopqrstu\r\n");
	osSemaphoreRelease (myBinarySem02Handle);
  }

  xLastCurrentTime = osKernelSysTick();
  /* Infinite loop */
  for(;;)
  {
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_SET);
		task1_LED_Active_Flag = 1;
	//if(osSemaphoreWait(myBinarySem01Handle,0) == osOK)
	//if(osSemaphoreWait(myBinarySem01Handle,osWaitForever) == osOK)
	//{
		for(i=0;i<15;i++)
		{
			gtask_set_value = 1;
			HAL_Delay(1);
		}
		//osSemaphoreRelease (myBinarySem01Handle);
	//}
  	HAL_GPIO_WritePin(GPIOD,LD4_Pin, GPIO_PIN_RESET);
	task1_LED_Active_Flag = 0;	

		
	osDelayUntil(&xLastCurrentTime,100);
  }
}
~~~

* 각 Task가 출력하는 printf()는 모두 다릅니다.

~~~c
//Task1
printf("abcdefghijklmnopqrstu\r\n");
~~~

~~~c
//Task1
printf("abcdefghijklmnopqrstu\r\n");
~~~

~~~c
//Task1
printf("abcdefghijklmnopqrstu\r\n");
~~~

# 3.2.2 출력 및 고찰
* 먼저, 세마포어 부분을 지우고 출력한 결과입니다.

![image](https://user-images.githubusercontent.com/79636864/111250222-4fd81900-8650-11eb-8c13-96fc6ba105c8.png)

* 고찰
    1. Task3의 printf()만 출력되는 것을 확인하였습니다.
    2. 동일한 Priority의 Task들이 라운드 로빈 방식으로 쪼개서 printf()에 접근을 하다보니    
       모든 Task들의 printf()가 출력되지 않고 마지막에 printf()의 buffer에 접근한 Task의 내용이    
       출력됨을 확인하였습니다.
 
* 세마포어 부분을 살리고 출력한 결과입니다.

![image](https://user-images.githubusercontent.com/79636864/111250228-549ccd00-8650-11eb-9489-2bd36de6873e.png)

* 고찰
    1. 모든 Task의 printf()만 출력되는 것을 확인하였습니다.
    2. 하나의 Task가 printf()를 모두 활용한 후, 다음, 또 그 다음 Task가 활용하는 것을 확인하였습니다.
    3. 3개의 Task를 정해진 순서대로 동기화를 하고자 한다면, 세마포어를 추가로 사용하여 구현해야합니다.

# 4. 정리
세마포어의 정의 및 활용에 대해 정리하였습니다.    
예제의 경우는 추가적인 내용이 있을 시 지속적으로 update할 예정입니다.

