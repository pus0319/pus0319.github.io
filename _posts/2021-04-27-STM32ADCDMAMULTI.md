---
date: 2021-04-27
title: "STM32_ADC_DMA"
categories: Embedded_Firmware
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32_ADC_DMA_Multi
=============

STM32는 사용되는 ADC는 Polling 방식 및 DMA 방식으로 사용할 수 있음.    

그중 DMA 방식으로 사용하는 방법을 정리함.

# 1. ADC, DMA 초기 설정
1. ADC Clock 설정.
    * 데이터 시트를 참고하여 최대로 설정할 수 있는 ADC Clock값을 넘지 않는 선에서 설정.    

![image](https://user-images.githubusercontent.com/79636864/117084788-1afc5e80-ad83-11eb-8291-e307b547666d.png)    

2. ADC 기본 설정.
    * 아래의 Parameter들을 설정.

![image](https://user-images.githubusercontent.com/79636864/117084936-6e6eac80-ad83-11eb-8578-dfc06e422865.png)    

    * Continous conversion mode
        * 한번의 트리거 신호로 하나의 채널 또는 sequencer 에서 설정한 마지막 순서 채널까지    
          ADC 샘플링을 완료하고 다시 처음 채널 순서로 돌아가서 ADC 샘플링을 시작
    * Discontinous conversion mode
        * Sequencer 에서 예를 들어 16 개의 채널을 regular 그룹으로 설정했을때 discontinous    
          conversion number 를 가령 2 로 설정하면 총 8 번의 ADC 시작 trigger 를 받아야 16 개    
          채널의 샘플링을 완료할수 있는 모드.
    * Scan conversion mode
        * 하나의 채널만 ADC 샘플링하지 않고, 다음번 트리거가 오면 sequencer 에서 설정한    
          채널 순서대로 다음 채널 순서의 ADC 샘플링을 진행.
    * Sequencer(Number of conversion)
        * 샘플링 채널 순서와 채널 번호, 샘플링 time 을 설정해서 하드웨어적으로    
          자동으로 순차적으로 이루어질수 있도록 설정
        * 값에 따라서 Rank가 생성됨.
    * Rank
        * 사용하고자하는 channel을 설정.
    * Sampling Time
        * ADC 샘플링 속도를 설정할 수 있는 요소.
        * conversion time에 추가로 각 Rank별로 Sampling Time을 설정하여 샘플링 속도 설정가능.
        * ADC 샘플링 속도 = conversion time + Sampling time

3. ADC DMA 기본 설정.
![image](https://user-images.githubusercontent.com/79636864/117085568-23559900-ad85-11eb-9794-904f6261d327.png)    

    * 모드를 Circular로 해서 반복 동작하도록 설정.
    * Data Width를 원하는 만큼 설정.
    * DMA Request를 내가 사용하는 ADC 채널을 설정.
    * 이제, DMA에 설정한 Sequencer의 모든 Rank의 값들이 메모리(배열)에 저장가능.
    
    
# 2. ADC, DMA Example Code
## 2.1 init
* init함수는 CubeMX를 통해 자동으로 생성됨.
* 먼저, ADC DMA 값을 저장할 전역변수(배열)을 선언.

~~~c++
/*...*/
  __IO uint16_t gADCData[_MAX_ADC_CH_] = {0,};
/*...*/
~~~    
    * _IO는 volatile를 의미하고, 이는 최적화하는 과정에서 주소 값을 바꾸지 못하게 설정.
    * 기본적으로 volatile으로 선언한 변수는 무조건 사용할 때 항상 메모리에 접근하도록 설정되기 때문에    
      컴파일러에서 최적화를 이유로 관련 동작들을 수정하지 않게됨.

* 그후, ADC Calibration 기능을 적용 후, ADC DMA 를 시작함.  
~~~c++
/*...*/
  HAL_ADCEx_Calibration_Start(&hadc1);
  HAL_ADC_Start_DMA(&hadc1, (uint32_t*)gADCData, _MAX_ADC_CH_);
/*...*/
~~~    
    * Calibration_Start 전에는 무조건 ADC는 비활성화 되어있어야함.
    * Calibration_Start를 완료하면 ADC START를 함.
    * ADC값을 DMA에 전송이 완료되거나 half transfer가 되었을 때 인터럽트가 활성화    
      (별도 Callback 함수도 있음)
    * _MAX_ADC_CH_ 값은 초기설정의 Sequencer 값임.    


## 2.2 ADC DMA 값 읽기.
* 별도의 추가 동작 없이 ADC 샘플링이 완료 될때마다 DMA에 실시간으로 저장함.    
~~~c++
/*...*/
  adcvalueRank1 = gADCData[_RANK1_INDEX_];
  adcvalueRank2 = gADCData[_RANK2_INDEX_];
  adcvalueRank3 = gADCData[_RANK3_INDEX_];
/*...*/
~~~    

