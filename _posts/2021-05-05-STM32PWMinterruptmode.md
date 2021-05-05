---
date: 2021-04-27
title: "STM32_PWM_Interrupt_MODE"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_PWM_MODE
=============

STM32에 사용하는 PWM을 interrupt로 사용하는 방법을 정리.    

# 1. PWM 초기 설정
* 아래와 같이 사용하는 Timer의 channel을 PWM Generation으로 설정    

![image](https://user-images.githubusercontent.com/79636864/117086706-4b92c700-ad88-11eb-9c8a-99e6a9038b6b.png)    

* 관련 Configuration을 설정    

![image](https://user-images.githubusercontent.com/79636864/117086733-5ea59700-ad88-11eb-9436-f1087e8344a9.png)    

1. Counter Settings
    * TIM Clock = Datasheet를 참고하여 어떤 clock를 사용하는지(APB1,APB2 등)을 체크할 것.
    * Prescaler : 1
    * Period Count : 35999
    * TIM Tick = (1 / TIM Clock) * (Prescaler + 1) * (Period Count + 1)
    * PWM Frequency = (1 / TIM Tick)

2. PWM Generation Channel
    * mode
        * mode 1 : PWM Duty Level이 High
        * mode 2 : PWM Duty Level이 Low
    * Pulse : PWM Duty를 설정할 수 있음.
    * Fast mode : PWM Fast mode 여부

* interrupt mode를 사용하여 callback이 호출되도록 함.    

![image](https://user-images.githubusercontent.com/79636864/117087452-70883980-ad8a-11eb-852a-60448178efa5.png)    


# 2. PWM code Example
## 2.1 init
* 아래의 코드를 사용하여 PWM interrupt mode를 init함.
~~~c++
/*...*/
HAL_TIM_PWM_Start_IT(&htim2,TIM_CHANNEL_3);
/*...*/
~~~    

## 2.2 start
* PWM Duty를 조절할 수 있는 별도의 함수를 구현    
~~~c++
FUNC_StatusTypeDef PWM_SetValue(uint32_t cp_channel, uint8_t persent)
{
	TIM_OC_InitTypeDef sConfigOC = {0};
	uint16_t value = 0;
	//uint16_t maxvalue = 35999;
	//double unitvalue = 359.99;
	uint16_t maxvalue = 36000;
	double unitvalue = 360.00;


	if((persent < 0) || (persent > 100))
	{
		return FUNC_ERROR;
	}

	if((cp_channel != TIM_CHANNEL_3) && (cp_channel != TIM_CHANNEL_4))
	{
		return FUNC_ERROR;
	}

	value = (uint16_t)(maxvalue - (uint16_t)(unitvalue * persent));

	//__disable_irq();
	HAL_TIM_PWM_Stop(&htim2, cp_channel);
    sConfigOC.OCMode = TIM_OCMODE_PWM1;
    sConfigOC.Pulse = value;
    if (HAL_TIM_PWM_ConfigChannel(&htim2, &sConfigOC, cp_channel) != HAL_OK)
    {
	    //Error_Handler();
    	__enable_irq();
    	return FUNC_ERROR;
    }
    if(HAL_TIM_PWM_Start(&htim2, cp_channel) != HAL_OK)
    {
  	    //Error_Handler();
    	__enable_irq();
  	    return FUNC_ERROR;
    }
    //__enable_irq();

    return FUNC_OK;
}
~~~    

## 2.3 callback
* 아래의 코드와 같이 callback 함수를 사용하여 PWM Pulse 출력의 1cycle이 끝나면    
  PWM Duty를 변경하도록 구현
  
~~~c++
void HAL_TIM_PWM_PulseFinishedCallback(TIM_HandleTypeDef *htim)
{
	sCP *pCP;
	uint32_t bCh;

	if(htim == &htim2)
	{
		if(htim->Channel == HAL_TIM_ACTIVE_CHANNEL_3)
		{
			pCP = &CP_B;
			bCh = TIM_CHANNEL_3;
		}
		else if(htim->Channel == HAL_TIM_ACTIVE_CHANNEL_4)
		{
			pCP = &CP_C;
			bCh = TIM_CHANNEL_4;
		}
		else
		{
			return;
		}

		if(pCP->PWM_Duty != pCP->PWM_DutyBK)
		{
			if(FUNC_OK != PWM_SetValue(bCh, pCP->PWM_Duty))
			{
				printf("PWM_SetValue ERROR \r\n");
			}
			else
			{
				pCP->PWM_DutyBK = pCP->PWM_Duty;
			}
		}
	}
}
~~~ 
