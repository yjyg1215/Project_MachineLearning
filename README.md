# 💙*애니메이션 추천 시스템*💙
OTT 사용자 이탈율 증가를 막기 위해 머신러닝을 활용하여 애니메이션 추천 시스템을 구현

## ✔️*기획 배경*

<img width="400" alt="스크린샷 2023-02-09 오전 12 51 32" src="https://user-images.githubusercontent.com/62207156/217580593-6a528af6-a4ef-416c-bec2-78e75fbaf8ab.png">

→ 사용자 이탈이 OTT 플랫폼 전반적으로 문제가 되고 있음

<img width="400" alt="스크린샷 2023-02-09 오전 12 53 46" src="https://user-images.githubusercontent.com/62207156/217581184-1a9edee5-889f-4a47-a4f4-88059e5aeb64.png">

→ 그러는 와중에 애니메이션 시장은 꾸준히 성장해 한화 약 31조원의 규모에 도달함

<img width="400" alt="스크린샷 2023-02-09 오전 12 56 07" src="https://user-images.githubusercontent.com/62207156/217581905-fd9c18d2-8267-4468-868f-d8560e06d69c.png">

→ 2018년을 기점으로 OTT를 통한 애니메이션 배급이 비디오 배급 방식을 추월했음

### **=> 오 그럼 애니메이션 특화 추천 시스템을 마련해서 이탈율 증가를 막아보자!**

## ✔️*데이터 셋*

<img width="500" alt="스크린샷 2023-02-09 오전 1 05 28" src="https://user-images.githubusercontent.com/62207156/217584485-6c9fd1ba-b9a1-4eb5-86ea-c017e6710af5.png">

- 애니메이션 ID를 기준으로 병합하여 사용
- 100만 row로 샘플링 진행

## ✔️*EDA*

### 데이터 전처리

#### 컬럼 추가

<img width="400" alt="스크린샷 2023-02-09 오전 1 10 33" src="https://user-images.githubusercontent.com/62207156/217585759-2d2f945d-e94d-4045-9034-dddb951fb5f3.png">

→ 각 장르별 컬럼 추가

<img width="300" alt="스크린샷 2023-02-09 오전 1 12 24" src="https://user-images.githubusercontent.com/62207156/217586225-3f553603-facd-4bb7-a83d-2c535a286381.png">

→ 공개 년도 컬럼 추가 (카디널리티 축소를 위해 10년 단위로 묶음)

<img width="228" alt="스크린샷 2023-02-09 오전 1 15 19" src="https://user-images.githubusercontent.com/62207156/217586955-ad9782ba-32b5-4bf8-926f-1cc3f5f63d26.png">

→ 유저들의 평점 평균이 8점인 것을 확인. 8점을 기준으로 추천하는 시스템을 고안하면서 8점 이상의 평점을 부여했는지 여부를 새로운 컬럼으로 추가 (Target)

#### 기타

- 수치형 데이터의 데이터 타입을 정수 및 실수 타입으로 변환
- 평점 결측치를 각 score 컬럼에서 얻은 정보로 채움

### 가설 분석

1. 원작의 형태에 따라 유저들의 평점 평균이 다르다. (O)

<img width="400" alt="image" src="https://user-images.githubusercontent.com/62207156/217588981-36709e6e-4b98-419c-bd37-00d62647b712.png">
 
 → 원작이 소설인 경우 평균적으로 7.75점을 주는 반면, 라디오가 원작인 경우 5.75점으로 상대적으로 2점이나 차이가 나는 것을 확인
 
 2. 애니메이션 타입에 따라 유저들의 평점 평균이 다르다. (O)
 
 <img width="216" alt="스크린샷 2023-02-09 오전 1 23 28" src="https://user-images.githubusercontent.com/62207156/217589648-55cfa443-7bd7-482c-831a-dd910e63a861.png">
 
 → 무비 타입이 7.6점으로 가장 높고, 뮤직 타입이 6.5점으로 가장 낮은 점수를 보임. 영화와 티비 프로그램처럼 익숙한 타입과 그렇지 않은 타입간의 격차도 확인됨
 
 ## ✔️*모델링*
 
 - 추천 여부를 8점을 기준으로 나눌 것이므로 이진분류모델
 - 기준 모델은 최빈 클래스로 지정
 <img width="329" alt="스크린샷 2023-02-09 오전 1 28 20" src="https://user-images.githubusercontent.com/62207156/217590990-435d03a1-0ea4-4d97-8958-b83fcd6f34ae.png">
 
 → 기준 모델의 학습 데이터에 대한 정확도는 0.54
 
 1. XGBoost
 <img width="221" alt="스크린샷 2023-02-09 오전 1 30 19" src="https://user-images.githubusercontent.com/62207156/217591527-73616bc9-9c8a-4ba9-8177-5af346d7057c.png">
 
 → 검증 결과 정확도 0.78로 기준 모델의 베이스라인 통과
 
 2. LightGBM
 <img width="222" alt="스크린샷 2023-02-09 오전 1 30 27" src="https://user-images.githubusercontent.com/62207156/217591553-531e8b1a-6ff9-476f-9cf0-194520ada5f9.png">
 
 → 검증 결과 정확도 0.78로 기준 모델의 베이스라인 통과
 
 **=> 근소하지만 XGBoost가 더 좋은 성능을 보였기 때문에 최종 모델은 XGBoost로 결정**
 
 <img width="684" alt="image" src="https://user-images.githubusercontent.com/62207156/217592586-a7f35a07-da27-42b2-b9ad-77c1fd2f906e.png">
 
 → 최종적으로 교차검증을 통해 평가 데이터의 일반화 성능을 확인. 정확도 0.78로 측정됨
 
## ✔️*모델 해석*

### 순열 중요도

<img width="259" alt="스크린샷 2023-02-09 오전 1 36 12" src="https://user-images.githubusercontent.com/62207156/217593207-6ffeb157-8ce5-44c4-b478-71a1e7a37dc8.png">

→ 각 유저당 평가한 모든 점수들의 평균값이 가장 큰 영향을 줌

------------------------
`Python` `EDA` `Feature Engineering` `Data Manipulation` `Data Visualization` `XGBoost` `LightGBM` `Permutation Importance` `PDP`
