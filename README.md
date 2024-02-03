# 지능지수 및 경제지표 활용한 누적수익률 
# VS 
# S&P 500 ETF 누적수익률

## 선정이유

```
"지능의 역설"

지능이 높을수록 우리 몸에 탑재된 DNA에 반하는 행동을 한다.   
DNA From 선사시대 환경   
보통의 인간은 위험회피적인 성향을 가지고 있지만, 지능이 높을수록 그렇지 않게 행동할 확률이 크다.   
지능지수가 높아짐에 따라 투자 시장에서는 경기의 일반적인 흐름과 반대되는 행위자들이 많아질 것이다.   
따라서 경기가 좋지 않을 때 보통은 주식을 팔지만 지능지수가 높아짐을 확인하면 사람들은 위험을 감수하고 팔지 않을 수 있다.   
그럼 여기에서 차익거래를 실현하는 것이다.   

경기 하락 + 지능지수 높음 - 주식 매수 비율 상승
경기 하락 + 지능지수 낮음 - 주식 매수 비율 하락   
경기 상승 + 지능지수 높음 - 주식 매도 비율 상승
경기 상승 + 지능지수 낮음 - 주식 매도 비율 하락   
```

### 데이터 수집 기간: 2002.01.01 ~ 2023.05.28

### 데이터 수집

  * USA 인플레이션, 실업률, 금리 월 데이터 수집(from FRED)

  * 아래 회사는 선사시대 때 할 수 없는 것들의 회사(from yahoofinace)
    
  * 알코올 회사: Diageo plc, Anheuser-Busch InBev SA/NV, Heineken Holding N.V., China Resources Beer (Holdings) Company Limited (0291.HK), Pernod Ricard SA
                Molson Coors Beverage Company, Asahi Group Holdings, Ltd.,  Thai Beverage Public Company Limited, Kirin Holdings Company, Limited   

  * 커피 회사: Starbucks Corporation, Nestlé S.A., The Coca-Cola Company, Tata Consumer Products Limited   

  * 담배 회사: Philip Morris International Inc, British American Tobacco p.l.c., Imperial Brands PLC, Altria Group, Inc., KT&G Corporation    
              The Eastern Company, ITC Limited, Godfrey Phillips India Limited

  * 전기 회사: Duke Energy Corporation, Exelon Corporation, NextEra Energy, Inc., The Southern Company, Dominion Energy, Inc.     
              American Electric Power Company, Inc., Xcel Energy Inc., Entergy Corporation, PG&E Corporation, PPL Corporation   
    
### 데이터 전처리
* 4개 칼럼(Alcohol, Coffee, Smoking, Electronic)
* 각 칼럼에 해당하는 기업의 종가를 행으로 표시
* 이를 수익률로 변환


![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/9e69b17a-daa5-4de3-b1c0-6afeb0bd10aa)

![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/83b79d98-616d-46cc-b851-04c38dfb4448)

![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/268d7c2b-ed95-42b1-8c67-0064479cfc3b)

### EDA

* 각 칼럼마다 누적 수익률 보면 고점 대비 떨어지는 구간 있음. 이 때 사람들은 위험회피적인 성향이 더 강해질 것이라 가정
<img width="479" alt="image" src="https://github.com/Developerinsight/intelligence-index/assets/123748877/80f962a4-f372-40dd-88cd-0c43086a2438">


### 요인분석

* 데이터를 축소하는 변수의 정제과정이다. 즉 여러 가지 항목들을 비슷한 항목으로 묶는 것으로, 여러 변수 사이 존재하는 상호관계를 분석하여 타당성 검정하고 공통으로 속해있는 차원이나 요인들을 밝혀내어 변수 축소하는 작업   
* 활용방안: 타당성 검정(측정 도구가 정확히 측정했는지를 알아보기 위해 측정변수들이 동일한 요인으로 묶이는지 검정)   
* 변수 축소(변수들의 상관관계가 높은 것끼리 묶어서 변수를 정제), 변수제거(변수의 중요도를 나타내는 요인적재량이 0.4미만이면 설명력이부족한 요인으로 판단하여 제거), 요인 분석에서 얻어지는 결과를 이용해 상관분석이나 회귀분석의 설명변수로 활용

* 3개의 주성분이 데이터 80% 이상 설명력 가짐
* Factor 1: Coffee, Electronic
* Factor 2: Alcochol
* Factor 3: Smoking
  
![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/a576edf8-4344-4f22-80d7-78c6fb6c818e)


### 회귀분석
* 표준오차는 오차의 공분산 행렬이 올바르게 지정
* Factor 3은 p-value > 0.05 이기에 통계적으로 유의미하지 않음
* 따라서 매매시그널 = Factor1*coef_1 + Factpr2 * Coef_2
  
![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/0b3c76b6-5653-44a0-9787-c6c89fa5a62e)


### 지능지수(사용자함수)
```
for sig1, sig2 in zip(market_signal.iloc[:,-1],intelligence_index[:-2]): # 경기 국면 및 지능지수 
    # 주식 매도 비율 상승
    # 경기 상승하면 매수 비율 올라가나 지능지수도 오르기에 반대로 매도 비율 상승
    if sig1 >0 and sig2 >0:  
        lst.append(-1)

    # 주식 매도 비율 하락
    # 경기 상승하면 매수 비율 올라가나 지능지수 내려가기에 매도 비율 하락
    elif sig1 >0 and sig2 <0: 
        lst.append(1)

    # 주식 매수 비율 상승
    # 경기 하락하면 매도 비율 올라가나 지능지수 오르기에 반대로 매수 비율 상승
    elif sig1 < 0 and sig2 >0: 
        lst.append(1)

    # 주식 매수 비율 하락
    # 경기 하락하면 매도 비율 올라가나 지능지수 내려가기에 매수 비율 하락
    elif sig1 < 0 and sig2 <0: 
        lst.append(-1)
```

### 결과 도출
* 모멘텀 기반 매매 및 경기 시그널  → 매매결정 모델 생성

* 백테스팅 결과 → 특정 년도까지 유사한 패턴, 전체 기간 저조한 실적
  * 주황색 선이 S&P 500 ETF 누적 수익률
  * 파랑색 선이 사용자함수 누적 수익률
     
* 다른 독립변수 → 모델 설명력 높이는 방안 모색

* 모델 안정성 → 다른 기간 백테스팅

![image](https://github.com/Developerinsight/intelligence-index/assets/123748877/bfdc07f4-1731-4709-83dd-8581616fbe77)
