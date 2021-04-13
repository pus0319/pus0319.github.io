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
  
* ```Resources```    

![image](https://user-images.githubusercontent.com/79636864/114507038-8a43ce80-9c6d-11eb-8566-c7f055429c2b.png)    

* ```Typographies```    
    * 추가적으로 Custom Keyboard의 입력한 값을 사용하기 위해 별도의 Typograhies를    
      만들어야함.    
      
![image](https://user-images.githubusercontent.com/79636864/114507305-e9a1de80-9c6d-11eb-8eb2-e16a5f0d00f2.png)    

# 2. 진행하는 Project에 Custom keyboard Screen 생성 및 Interaction 생성
## 2.1 Custom Keyboard Screen 생성
* 아래와 같이 Custom keyboard전용 Screen 생성    

![image](https://user-images.githubusercontent.com/79636864/114507690-6f258e80-9c6e-11eb-83a6-701c6290487c.png)    

* 맨우측 상단의 INTERACTIONS에서 아래와 같은 interaction을 생성
1. CancelButtonPressed(취소버튼누를시)
    * 호출한 Screen으로 다시 돌아감.    

![image](https://user-images.githubusercontent.com/79636864/114507854-ad22b280-9c6e-11eb-8d4a-44822e6e4dda.png)    

2. OKButtonPressed(확인버튼누를시)
    * Custom keyboard에서 입력한 내용을 다른 Screen이 확인할 수 있도록 Golbal Array Variable에 저장함.
    * 다른 interaction에서 trigger로 사용할 수 있도록함.

![image](https://user-images.githubusercontent.com/79636864/114507973-d3e0e900-9c6e-11eb-9481-e73bd9462f34.png)    

3. ChangedPrevScreen(OKButtonPressed interaction callback)
    * 호출한 Screen으로 다시 돌아감.    

![image](https://user-images.githubusercontent.com/79636864/114508412-494cb980-9c6f-11eb-903b-57db5a106fc7.png)    


## 2.2 Custom Keyboard Screen을 호출하는 Screen의 Interaction 생성
* Custom Keyboard Screen을 호출하는 Screen에서 Custom Keyboard를 사용하는    
  widgets(ex : 버튼)을 구분하기 위한 Custom Action 생성.
    * 각 각의 widgets를 number로 구분지어 호출하기 위함.    

![image](https://user-images.githubusercontent.com/79636864/114508877-d728a480-9c6f-11eb-9747-c7d4a817b9a5.png)    

* 맨우측 상단의 INTERACTIONS에서 아래와 같은 interaction을 생성
1. Custom Keyboard Screen을 호출하는 widgets들의 Trigger에 따른    
   Custom Action 호출
    * 각각의 widget들은 Value가 모두 달라야함.
    * 다른 interaction에서 trigger로 사용할 수 있도록함.
   
![image](https://user-images.githubusercontent.com/79636864/114509075-10611480-9c70-11eb-8a0d-b72891313715.png)    

2. Custom Action 호출 시, 어떤 widget에서 호출 했는지에 대해 Global Variable에 저장해야함.    
    * 다른 interaction에서 trigger로 사용할 수 있도록함.    

![image](https://user-images.githubusercontent.com/79636864/114509982-1acfde00-9c71-11eb-9e09-1c09405a0df1.png)    

3. 2번의 interaction Callback에서 Custom Keyboard Screen으로 Change    

![image](https://user-images.githubusercontent.com/79636864/114510238-64b8c400-9c71-11eb-82d1-4917f611ebd9.png)    


## 2.3 Custom Keyboard Container 소스파일 추가 및 Project 소스코드 작성
* 아래의 경로의 각 폴더들의 내용을 현재 진행하는 Project의 동일한 폴더 이름에 저장.    
```
\'KeyboardDemo'\gui\src\common\CustomKeyboard.cpp    
\'KeyboardDemo'\gui\include\gui\common\CustomKeyboard.hpp    
\'KeyboardDemo'\gui\include\gui\common\KeyboardKeyMapping.hpp    
\'KeyboardDemo'\gui\include\gui\common\KeyboardLayout.hpp     
```  
* Custom Keyboard Screen의 View.cpp에서 아래의 사진과 같이 소스코드 작성
    1. Custom Keyboard Container hpp 파일 추가
    2. OKButtonPressed interaction에서 호출하는 Virtual Function 선언.
    3. Custom Keyboard 인스턴스 생성.    

![image](https://user-images.githubusercontent.com/79636864/114511327-bc0b6400-9c72-11eb-8aed-55dbbebe3d9b.png)    



