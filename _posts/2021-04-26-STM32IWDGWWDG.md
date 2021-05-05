---
date: 2021-04-26
title: "STM32_IWDG,WWDG"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_IWDG,WWDG
=============

STM32는 사용되는 Clock에 따른 2가지의 Watchdog가 있음.    

IWDG와 WWDG를 동시에 사용 시,    

Main Clock이 오동작 시, Main Clock외의 Clock으로 동작하는 Watchdog(IWDG)를 이용하여,    

MCU System을 강제로 Reset할 수 있음.    

이러한 동작을 통해 MCU 동작의 신뢰성을 높일 수 있음.

# 1. IWDG(Independent Watchdog)
* 자체 전용 LSI(Low Speed Clock)에 의해 동작하는 WDG.
* Main Clock에 완전히 독립적인 프로세스로 감시해야할 경우 활용하면 됨.
* 단, 타이밍 정확도는 WWDG보다 낮음.    
* Window 일 경우, 정해진 초기 시간 동안 Refresh를 할 수 없음(Refresh not allowed)
    * 만약, 정해진 초기 시간 동안 Refresh를 시도 시, IWDG Reset을 수행함.

## 1.1 IWDG Parameter
### 1.1.1 Non Window
* IWDG Clock : LSI Clock
* Prescaler : IWDG Clock을 Prescale 하는 값.
* down-counter reload : Prescale IWDG Clock 당 1씩 다운 카운팅함.    
                        값이 0이 될 경우, IWDG Reset이 발생.    
                        
* IWDG Reset Timeout(Non Window) = (1/IWDG Clock * Prescaler) * down-counter reload
                        
### 1.1.2 Window
* IWDG Clock : LSI Clock
* Prescaler : IWDG Clock을 Prescale 하는 값.
* window value : down-counter value값이 window value값보다 낮을 경우, IWDG 카운터 값을 Refresh 할 수 있음.(Refresh allowed)    
                 그 외는 Refresh 할 수 없음(Refresh not allowed)
* down-counter value : Prescale IWDG Clock 당 1씩 다운 카운팅함.    
                       값이 Timeout value과 같아질 경우, IWDG Reset이 발생. 
* Timeout value : IWDG Reset이 발생하는 카운트 값.(보통 0x3F, 꼭 Chipset Reference Manual 사용할 것.)
                       
* IWDG 1 Clock time = 1/IWDG Clock * Prescaler   
* Refresh not allowed = (down-counter value - window value) * IWDG 1 Clock time
* IWDG Reset Timeout(Window) = (down-counter value - Timeout value) * IWDG 1 Clock time
   
## 1.2 STM32CubeMX Setting
1. HSE Clock 활성화(H/W 회로에 맞게 Setting)
2. LSI Clock 값 확인    

![image](https://user-images.githubusercontent.com/79636864/117083748-5ba6a880-ad80-11eb-99a7-400058f82882.png)    

3. IWDG 기능 활성화 및 Parameter 설정할 것.

![image](https://user-images.githubusercontent.com/79636864/117083869-a7595200-ad80-11eb-9241-146a46adc76c.png)    
![image](https://user-images.githubusercontent.com/79636864/117083886-ae806000-ad80-11eb-8104-70d67347ca01.png)    

## 1.3 Example

### 1.3.1 Init
* CubeMX를 이용하여 자동으로 생성함.    

~~~c++
  /* USER CODE END IWDG_Init 1 */
  hiwdg.Instance = IWDG;
  hiwdg.Init.Prescaler = IWDG_PRESCALER_32;
  hiwdg.Init.Reload = 1249;
  if (HAL_IWDG_Init(&hiwdg) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN IWDG_Init 2 */
  if(HAL_IWDG_Refresh(&hiwdg) != HAL_OK)
  {
	 Error_Handler();
  }
  /* USER CODE END IWDG_Init 2 */
~~~    


### 1.3.2 Start, down-counter reload
* Start는 따로 없음. init 함수를 실행하면 자동 start 함.    
* 아래의 코드를 실행하여 iwdg counter를 reload함.
~~~c++
  if(HAL_IWDG_Refresh(&hiwdg) != HAL_OK)
  {
	 Error_Handler();
  }
~~~    

### 1.3.3 IWDG Reset 여부 체크
~~~c++
  /* Check Reset Occurred */
  if(RESET != __HAL_RCC_GET_FLAG(RCC_FLAG_IWDGRST))
  {
	  /* Reset : IWDG */
	  printf("Reset : IWDG \r\n");
    __HAL_RCC_CLEAR_RESET_FLAGS();
  }
~~~ 
* 위의 코드를 통해 어떤 Reset이 발생했는지 알 수 있음.    



# 2. WWDG
* Main System Clock(PCLK1)에 의해 동작하는 WDG.
* 거의 모든 경우에 활용하면 됨.
* 타이밍 정확도 또한 높음.  
* Window 일 경우, 정해진 초기 시간 동안 Refresh를 할 수 없음(Refresh not allowed)
    * 만약, 정해진 초기 시간 동안 Refresh를 시도 시, WWDG Reset을 수행함.
    * 간헐적으로 위와 같은 동작을 수행 시,
      Early Wakeup Interrupt(EWI)기능을 활성화하여 해당 ISR에서 Refresh를 수행할 것.
      
## 2.1 WWDG Parameter
* WWDG Clock : PCLK1
* Prescaler : WWDG Clock을 Prescale 하는 값.
* window value : down-counter value값이 window value값보다 낮을 경우, WWDG 카운터 값을 Refresh 할 수 있음.(Refresh allowed)    
                 그 외는 Refresh 할 수 없으며(Refresh not allowed) Refresh 시도 시 WWDG Reset을 수행함.
* down-counter value : Prescale WWDG Clock 당 1씩 다운 카운팅함.    
                       값이 Timeout value과 같아질 경우, WWDG Reset이 발생. 
* Timeout value : WWDG Reset이 발생하는 카운트 값.(보통 0x3F, 꼭 Chipset Reference Manual 사용할 것.)
                       
* WWDG 1 Clock time = 1 / (WWDG Clock / Prescaler / 4096)
* Refresh not allowed = (down-counter value - window value) * WWDG 1 Clock time
* WWDG Reset Timeout(Window) = (down-counter value - Timeout value) * WWDG 1 Clock time    

## 2.2 STM32CubeMX Setting
1. HSE Clock 활성화(H/W 회로에 맞게 Setting)    
2. PCLK1 값 확인    

![image](https://user-images.githubusercontent.com/79636864/117084311-d8865200-ad81-11eb-8eeb-e244dd95a914.png)    

3. WWDG 기능 활성화 및 제시된 Parameter를 설정할 것.    

![image](https://user-images.githubusercontent.com/79636864/117084351-ee941280-ad81-11eb-8e18-320fdf6dcd9e.png)    
    
## 2.3 Example

### 2.3.1 Init
* CubeMX를 이용하여 자동으로 생성함.    

~~~c++
  /* USER CODE END WWDG_Init 1 */
  hwwdg.Instance = WWDG;
  hwwdg.Init.Prescaler = WWDG_PRESCALER_8;
  hwwdg.Init.Window = 127;
  hwwdg.Init.Counter = 127;
  hwwdg.Init.EWIMode = WWDG_EWI_DISABLE;
  if (HAL_WWDG_Init(&hwwdg) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN WWDG_Init 2 */
~~~    

### 2.3.2 Start, down-counter reload
* Start는 따로 없음. init 함수를 실행하면 자동 start 함.    
* 아래의 코드를 실행하여 iwdg counter를 reload함.    

~~~c++
  if(HAL_WWDG_Refresh(&hwwdg) != HAL_OK)
  {
  	Error_Handler();
  }
~~~     

### 2.3.3 IWDG Reset 여부 체크
~~~c++
  /* Check Reset Occurred */
  if(RESET != __HAL_RCC_GET_FLAG(RCC_FLAG_WWDGRST))
  {
	  /* Reset : WWDG */
	  printf("Reset : WWDG \r\n");
    __HAL_RCC_CLEAR_RESET_FLAGS();
  }
~~~ 
* 위의 코드를 통해 어떤 Reset이 발생했는지 알 수 있음.    

