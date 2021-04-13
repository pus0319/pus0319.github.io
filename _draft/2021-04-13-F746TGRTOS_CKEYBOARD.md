---
date: 2021-04-13
title: "STM32F746_TouchGFX_FreeRTOS_7_TouchGFX CustomKeyboard 구현"
categories: Embedded_FreeRTOS
tags: jekyll
toc: true  
toc_sticky: true 
---

STM32F746_TouchGFX_FreeRTOS_6_TouchGFX CustomKeyboard 구현
=============

TouchGFX에서 제공하는 Application templete Example demo인 Custom Keyboard를    

현재 진행하는 Project에 구현하는 방법을 정리하였습니다.    

구현 시, 아래와 같은 휴대폰 등의 기기에서 볼 수 있는 Keyboard를 사용할 수 있습니다.    

![image](https://user-images.githubusercontent.com/79636864/114504879-82365f80-9c6a-11eb-962d-ce1d64851656.png)    


# 0. Custom Keyboard Example 생성
* TouchGFX Designer에서 Custom Keyboard Demo를 생성합니다.
    * 진행하는 Project에 Custom keyboard의 Images,Texts,Fonts 들을    
      그대로 사용하는 것이 빠름.    
      
![image](https://user-images.githubusercontent.com/79636864/114505482-6a131000-9c6b-11eb-95ca-f76bca25a679.png)    

# 1. 진행하는 Project에 Custom keyboard assets 가져오기
* 아래의 경로의 각 assets폴더들의 내용을 현재 진행하는 Project의 동일한 폴더이름에 저장.    
```
\'KeyboardDemo'\assets\fonts    
\'KeyboardDemo'\assets\images    
\'KeyboardDemo'\assets\texts는 TouchGFX Designer에서 직접 작성함.    
```    

* TouchGFX Designer에서 Custom keyboard Demo의 ```Resources``` 와 ```Typographies``` 의 내용을    
  현재 진행하는 Project에 똑같이 작성    
  
** Resources
![image](https://user-images.githubusercontent.com/79636864/114507038-8a43ce80-9c6d-11eb-8566-c7f055429c2b.png)    

aa



