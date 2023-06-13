# single-household-dwelling
이 프로젝트는 1인 가구를 위한 부동산 추천 시스템입니다!
## 1.문제 정의:

1인 가구가 매년 늘어나고 있는 현 시점에 전체 가구 3가구 중 한 가구는 1인 가구일 정도로 일인 가구의 수가 많이 늘어났음

1인 가구 중에서도 대부분을 차지하는 연령은 주로 사회초년생인 청년층임



그렇다면 1인 가구가 가지는 가장 큰 고민은 무엇일까?

1인 가구의 월 평군 소비 지출액이 19.5%가 바로 주거 지출!

-> 주거 지출을 줄이되 내가 원하는 조건을 최대한 맞춰서 주거지를 선택할 수 있을까?

이런 호기심을 기반으로 1인 가구 주거지 추천이라는 주제를 잡게 되었습니다!!

여기에서 사람들이 선호하는 주거 기준을 ['치안', '편의시설', '녹지', '대중교통', '전월세]로 지정하여

팀원들과 역할을 분담하여 데이터 분석을 진행하였습니다!!



## 2. 데이터 수집



데이터 수집은 데이터를 찾아 직접 파일을 다운 받는 식으로 진행하였습니다!

수집된 데이터와 데이터 출처는 아래와 같습니다!!
![image](https://github.com/gaeju/single-household-dwelling/assets/100760127/66957261-25ab-4c77-bbfd-9bc14178cbac)



3. 데이터 정제 및 전처리


제가 맡아 진행했던 부분은 전월세입니
(1) 전월세 데이터 가져오기
![image](https://github.com/gaeju/single-household-dwelling/assets/100760127/d925f26e-b03a-414e-be2d-f8c5babd5c13)

지도 시각화를 위해 필요한 법정동 코드: 자치구 코드 + 법정동 코드 - 뒤에 '00' = 법정동 코드
새롭게 법정동 코드를 만들어주었습니다.

(2) 필요 없는 컬럼 제거
['자치구코드','지번구분코드', '지번구분', '본번','부번','층', '계약일','건축년도','건물명', '계약기간', '신규계약구분','갱신청구권사용','종전보증금','종전임대료']
제거해주었습니다.

(3) 결측치
결측치가 없어 결측치 제거는 패스가 되었습니다.

(4) 전월세 구분
전세와 월세를 따로 구분, 팀원들과 컬럼명을 맞추기 위해 [자치구명-> 자치구 / 법정동명 -> 법정동] 으로 바꿔주었습니다.
**월세 전처리**
- 1인 가구를 위한 주거지 추천을 목표로 하기 때문에 평수의 최대치를 1인 가구 기준으로 18평, 60m**2으로 지정하고 평당가로 변경
- 보증금, 월세 기준으로 각각 이상치 제거
- 사람들이 원하는 평수를 추천해주기 위해 평수를 3개의 크기로 나누어줌

**전세 전처리**
- 1인 가구를 위한 주거지 추천을 목표로 하기 때문에 평수의 최대치를 1인 가구 기준으로 18평, 60m**2으로 지정하고 평당가로 변경
- 전세금 기준으로 이상치 제거
- 사람들이 원하는 평수를 추천해주기 위해 평수를 3개의 크기로 나누어줌


## 3. EDA 진행

## 4. 분석 결과 활용
그렇다면 이걸로 무엇을 할 수 있을까?

(1) 원하는 법정동을 넣으면 동에 해당하는 전세가와 월세가를 평당가 평균 별로 출력해주는 함수(앞서 구현한 것들을 하나의 함수로 요약)
1 dong_price라는 함수 선언해주기
2 필요한 모듈을 함수 안에 구현해주기
3 데이터를 함수 안에서 가져오게 하기
4 새로운 법정동 코드 만들기(합치기)
5 월세만 따로 전처리
6 전세만 따로 전처리
7 size에 따른 월세 평당가의 평균 구하기 
8 size에 따른 전세 평당가의 평균 구하기 
9 만들어둔 월세와 전세가가 하나의 데이터 프레임으로, 또 크기 별로 출력될 수 있도록 merge와 concat을 이용하여 묶어줍니다.

(2)원하는 조건에 근접한  '동'을 출력
프로젝트 녹지, 치안, 교통, 편의시설 별로 점수를 내어 이용자가 원하는 조건과 원하는 가격에 맞게 추천해 줄 수 잇는 함수 구현이 필요했음
제가 맡은 전월세가는 "고객이 원하는 가격대를 입력하면 거기에 걸맞는 동을 출력"을 해주는 게 맞지 않나라는 결론이 나와 
뒤늦게 새로운 함수를 구현을 해주었습니다.

조건 리스트
-전/월세 중에 뭘 원함?

-원하는 보증금의 평당가 최소/최대가가 얼마야?

-월세를 선택했다면 월세 최대/최소는 얼만데?

if 문을 이용하여 월세와 전세의 경우를 나눠줍니다.

**1.월세**

1 월세인 경우는 첫번째로 month_df를 자치구와 동 컬럼별로 groupby하여 평균을 내줍니다.

2 그 다음 처음 받았던 질문에 맞춰 원하는 금액 변수로 조건을 만들어 주고 (보증금 최대/최소가가 얼마인지)

3 조건에 맞는 데이터 프레임을 변수에 저장
4 데이터 프레임에서 필요한 '자치구', '보증듬편당가(만원), '월세평당가(만원)' 컬럼만 뽑아 출력해 줍니다.


**2 전세인 경우(else)**

1 month_df를 자치구와 동 컬럼별로 groupby하여 평균을 내주기

2 처음 받았던 질문에 맞춰 원하는 금액 변수로 조건을 만들어 주고 (보증금 최대/최소가가 얼마인지)

3 조건에 맞는 데이터 프레임을 변수에 저장

4 데이터 프레임에서 필요한 '자치구', '보증금평당가(만원), '월세평당가(만원)' 컬럼만 뽑아 출력

