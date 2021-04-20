---
date: 2021-04-20
title: "전기자동차_충전기술기본"
categories: Application
tags: jekyll
toc: true  
toc_sticky: true 
---

전기자동차_충전기술기본
=============

전기자동차(이하 EV) 기본에 이어 전기자동차의 충전기술에 대한    

기본적인 상식들에 대해 정리함.    

아래의 유튜브 영상을 주로 하여 내용을 정리함    

{% include video id="s6I4y2M7TdU" provider="youtube" %}    

# 0. 관련 용어

※ OBC (On Board Charger) : 충전기의 AC 입력을 배터리의 DC로 변환하는 장치    
※ SOC (State Of Charge) : 배터리의 잔존 용량    
※ CP (Control Pilot) : 전기차와 전기 충전기(EVSE) 사이에 상호 모니터링을 하는 통신 기능    
※ PD (Proximity Detection) : 충전기가 충전구에 꽂혀있는지 확인 여부 (충전구가 꽂혀있으면 전기차 이동이 불가)    
※ EVSE (Electric Vehicle Supply Equipment) : 전기차 충전기    
※ ICCB (In Cable Control Box) : 전기자동차 홈 충전 장치    

![image](https://user-images.githubusercontent.com/79636864/115357985-2d5d9080-a1f8-11eb-99eb-6482a8c874b4.png)


# 1. EV 충전 기술의 필요성
* EV의 핵심 기술 : 배터리, 모터, BMS(Battery Management System), 충전(Charge) 기술
* 최근, EV 판매 확충에 따라 EV 충전소 확충이 필요해짐.
* 일반적인 완속 충전기 -> 급속 충전기가 대세가 되고 있는 시점.
    * 신축건물에 고속충전기 설치 여부가 중요한 항목으로 포함이 되고 있음.
* 현재 EV 충전은 내연기관 자동차보다 충전이 다소 불편한 상황.    

![image](https://user-images.githubusercontent.com/79636864/115357517-ac05fe00-a1f7-11eb-86ec-89d73626430d.png)    

# 2. EV의 충전기술
![image](https://user-images.githubusercontent.com/79636864/115357582-be803780-a1f7-11eb-9e23-561183d47efb.png)    

## 2.1 충전기술의 전기에 관련된 구분
![image](https://user-images.githubusercontent.com/79636864/115357642-d0fa7100-a1f7-11eb-92d7-98670bd428cc.png)    
![image](https://user-images.githubusercontent.com/79636864/115359971-25065500-a1fa-11eb-8f6f-14620b246e87.png)    
![image](https://user-images.githubusercontent.com/79636864/115360002-2df72680-a1fa-11eb-84c4-f192cf710c1a.png)    

## 2.2 EV Cell Safety System
![image](https://user-images.githubusercontent.com/79636864/115360111-47986e00-a1fa-11eb-9793-d2d0232da84f.png)    

## 2.3 충전기의 유형
![image](https://user-images.githubusercontent.com/79636864/115358111-49f9c880-a1f8-11eb-845b-c5ce37814b4d.png)    

![image](https://user-images.githubusercontent.com/79636864/115358182-5c740200-a1f8-11eb-83f5-8b4df638a5af.png)    

## 2.4 충전기의 다양한 Connector(socket)
![image](https://user-images.githubusercontent.com/79636864/115359893-1324b200-a1fa-11eb-82e1-1ed1445c8a72.png)    
![image](https://user-images.githubusercontent.com/79636864/115360856-fccb2600-a1fa-11eb-8319-393160f1c252.png)    
![image](https://user-images.githubusercontent.com/79636864/115360900-05bbf780-a1fb-11eb-9956-64fb30cdf8b4.png)    

* 다양한 EV 제조사의 충전을 위해 Type 1 (단상)과 Type 2 (3상), 차데모, DC Combo 1 등이 골고루 설치되어야 함.

## 2.5 충전기의 충전 방법
### 2.5.1 완속충전
![image](https://user-images.githubusercontent.com/79636864/115359008-326f0f80-a1f9-11eb-88ae-bb8868b474e1.png)    
![image](https://user-images.githubusercontent.com/79636864/115360394-8d553680-a1fa-11eb-9f7f-24b5daee9b2d.png)    

* 220V 교류 전원을 이용하여 OBC를 통해 DC로 변환하여 배터리를 충전하는 방식. 
* 보통 완충 시간이 4~5시간 정도(아이오닉 전기차 기준).
* 크게 Type 1 (단상)과 Type 2 (3상) 방식으로 나눔(2.4항 참고)
    * Type 1 : 범용 (대부분의 나라에서 사용)
    * Type 2 : 유럽 (르노 자동차의 영향)

### 2.5.2 급속(고속)충전
![image](https://user-images.githubusercontent.com/79636864/115359047-3dc23b00-a1f9-11eb-885f-ffff432bd149.png)    
![image](https://user-images.githubusercontent.com/79636864/115361174-474ca280-a1fb-11eb-8aef-7f58c6b7f61f.png)    

* OBC를 거치지 않고 직류 고전압을 이용하여 바로 고전압 배터리를 충전하는 방식.
* 보통 완충시간이 25분 내외 (아이오닉 전기차, 100kW급 충전기 기준).
* 처음에는 CHAdeMO 방식을 많이 사용함.(2.4항 참고)
    * 미국과 유럽은 완속과 급속을 통합으로 Combo 방식을 규격화하여 충전구가 하나
    * 우리나라도 최근에는 국제적인 추세에 따라 콤보 방식을 채택하여    
      충전구도 하나로 줄이고 충전기에서도 콤보를 가지고 충전할 수 있게 인프라를 구축할 예정.

### 2.5.3 무선충전
![image](https://user-images.githubusercontent.com/79636864/115359084-47e43980-a1f9-11eb-95e6-09c749dd2fbf.png)

## 2.6 EV의 급속(고속)충전기술의 핵심지표
![image](https://user-images.githubusercontent.com/79636864/115358511-b70d5e00-a1f8-11eb-942e-5c64ac989796.png)    

# 3. EV의 배터리 소재에 따른 충전 기술
## 3.1 리튬이온배터리 셀의 충방전 그래프    
![image](https://user-images.githubusercontent.com/79636864/115358635-d3a99600-a1f8-11eb-9e6e-083c2fc03d4f.png)    

## 3.2 최근의 리튬이온배터리의 고속 충전기술(Enevate 사)
![image](https://user-images.githubusercontent.com/79636864/115358764-f176fb00-a1f8-11eb-95b7-d0d270913b16.png)    

## 3.3 EV에 사용하는(특히 인버터)전력 반도체
![image](https://user-images.githubusercontent.com/79636864/115360233-68f95a00-a1fa-11eb-82c2-3c81289e2819.png)    


# 4. EV 충전소에 필요한 요소들
![image](https://user-images.githubusercontent.com/79636864/115359386-95f93d00-a1f9-11eb-9639-be234518b756.png)    
![image](https://user-images.githubusercontent.com/79636864/115359407-9c87b480-a1f9-11eb-8a31-adb2f7618652.png)    

# 5. EV 고속충전
## 5.1 고속충전을 위한 테슬라(TESLA)의 사례
![image](https://user-images.githubusercontent.com/79636864/115359607-cf31ad00-a1f9-11eb-97bf-aa001d01e60f.png)    

## 5.2 Hyundai의 E-GMP Multi-charging System
![image](https://user-images.githubusercontent.com/79636864/115359691-e2dd1380-a1f9-11eb-92ea-ad719195db76.png)    

## 5.3 400V System vs 800V System
![image](https://user-images.githubusercontent.com/79636864/115359776-f5efe380-a1f9-11eb-8375-607bddd129c7.png)    


















