---
date: 2021-05-05
title: "STM32_UART_Interrupt_MODE"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_UART_Interrupt_MODE
=============

STM32에 사용하는 UART의 RX,TX 모두 interrupt 모드로 사용하는 방법을 정리.  

# 1. UART 초기 설정
* 아래의 사진과 같이 mode 및 Configuration을 설정.    

![image](https://user-images.githubusercontent.com/79636864/117088096-70893900-ad8c-11eb-9b18-cff6fed1d334.png)    

1. Mode
    * Asynchronous : 비동기화
        * 양 Device 간 기준 Clock 신호 line 등의 동기화 요소를 사용하지 않음.
        * 대신 Baud Rate를 사용
    * Synchronous : 동기화
        * 양 Device 간 동기화 요소를 사용.
2. Configuration
    * Baud Rate : 통신 속도. 단위는 Bit per Second
    * Word Length : 실제 데이터 길이
    * Parity : 통신 신뢰성을 위한 요소
    * Stop Bit : 실제 데이터를 모두 보냈을 경우 보내는 신호의 길이

* 아래의 사진과 같이 Interrupt mode로 설정하여 각종 Callback을 사용할 것임.    

![image](https://user-images.githubusercontent.com/79636864/117088288-fdcc8d80-ad8c-11eb-8f1a-8f955cb7afdc.png)    

# 2. UART code Example
## 2.1 init
* init 코드는 CubeMX에서 기본적으로 만들어줌.

## 2.2 Rx interrupt mode
* 아래의 코드는 UART Rx 데이터를 1번 받았을 때,    
  설정한 버퍼에 저장하고 **1번** interrupt 호출을 하도록 설정하는 함수임.    
  
~~~c++
/*...*/
  HAL_UART_Receive_IT(gHAL_UART[index].phuart_ch, &(gHAL_UART[index].RX.BUF[gHAL_UART[index].RX.BUF_Head]), 1);
/*...*/
~~~    

* 위의 함수는 아래의 과정으로 사용하면 됨.
    1. HAL_UART_Receive_IT() 로 인터럽트를 설정
    2. 인터럽트가 걸리면 길이만큼 데이터를 저장
    3. 인터럽트 안걸리게 다시 초기화
    4. 위의 모든 과정이 처리되면(Callback 함수가 호출되면) 다시 HAL_UART_Receive_IT()를 호출하여 RX 대기.

* 아래의 코드는 실제 Callback 함수의 내용임.    

~~~c++
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart)
{
	/*
	 *  When 1 byte is RX, it is stored in a dedicated buffer and interrupt callback is called.
	 */

	uint8_t index;

	if(huart->Instance == USART1)
	{
		index = _UART_CH_DEBUG_;
	}
	if(huart->Instance == USART2)
	{
		index = _UART_CH_DM_;
	}
	if(huart->Instance == USART3)
	{
		index = _UART_CH_UI_;
	}

	u8BUF_Plus_Index(&(gHAL_UART[index].RX.BUF_Head));

	/*
	 * Since it is initialized so that the interrupt does not take place,
	 * it must be set to take interrupt repeatedly.
	 */
	HAL_UART_Receive_IT(gHAL_UART[index].phuart_ch, &(gHAL_UART[index].RX.BUF[gHAL_UART[index].RX.BUF_Head]), 1);

}
~~~    

* 위의 Callback 함수안의 코드는 RX한 1byte 씩 임시버퍼(gHAL_UART)에 저장하는 동작을 함.
* RX Data가 1byte가 넘을 경우, 모든 Data를 임시버퍼(gHAL_UART)에 넣을 때가지 주기적으로 호출됨.
* 위와 같은 방법으로 구현하면 놓치는 RX Data 없이 동작을 구현 가능함.

## 2.3 Tx interrupt mode
* 아래의 코드는 설정한 버퍼의 1byte를 Tx 후, Tx가 완료되면 **1번** interrupt 호출을 하도록 설정하는 함수임.    

~~~c++
/*...*/
	HAL_UART_Transmit_IT(gHAL_UART[index].phuart_ch, &(gHAL_UART[index].TX.BUF[gHAL_UART[index].TX.BUF_Tail]), 1);
/*...*/
~~~    

* 위의 함수는 아래의 과정으로 사용하면 됨.
    1. HAL_UART_Transmit_IT() 로 인터럽트를 설정
    2. TX 동작 완료 시, 인터럽트 처리.
    4. 위의 모든 과정이 처리되면(Callback 함수가 호출되면)    
       설정한 버퍼에 Tx해야되는 Data가 있을 경우, 다시 HAL_UART_Transmit_IT()를 호출하여 1번 부터 반복.    
       설정한 버퍼에 Tx Data를 모두 보낸 경우, HAL_UART_Transmit_IT()를 호출하지 않음.

* 아래의 코드는 실제 Callback 함수의 내용임.    

~~~c++
void HAL_UART_TxCpltCallback(UART_HandleTypeDef *huart)
{
	uint8_t index;

	if(huart->Instance == USART1)
	{
		index = _UART_CH_DEBUG_;
	}
	if(huart->Instance == USART2)
	{
		index = _UART_CH_DM_;
	}
	if(huart->Instance == USART3)
	{
		index = _UART_CH_UI_;
	}

	u8BUF_Plus_Index(&(gHAL_UART[index].TX.BUF_Tail));

	if(u8BUF_Is_not_Empty(&(gHAL_UART[index].TX)))
	{
		HAL_UART_Transmit_IT(gHAL_UART[index].phuart_ch, &(gHAL_UART[index].TX.BUF[gHAL_UART[index].TX.BUF_Tail]), 1);
	}
}
~~~    

* 임시버퍼(gHAL_UART)의 TX Data가 1byte가 넘을 경우, 임시버퍼(gHAL_UART)의 모든 Data를 TX 할 때까지 주기적으로 호출.
* 위와 같은 방법으로 구현하면 Main loop 상의 UART TX 동작으로 인한 latency를 최소한으로 할 수 있음.    

