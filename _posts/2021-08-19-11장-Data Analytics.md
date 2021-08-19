---
title: "데이터베이스(DB) 11장 Data Analytics"
except: "데이터베이스(DB) 에서 빅 데이터를 활용하고 가공하는 Data Analytics에 대해 알아보자."
categories:
  - 데이터베이스
tags:
  - Clustering
  - Classifier
  - Data Cube
  - OLAP
  - Data Mining
  - Regression
  - Cross Tabulation
toc: true
toc_sticky: true
---

# 11장 Data Analytics
## <span style = "color:powderblue"> ※ Data Analytics

### <span style = "color:powderblue"> ※ Decision Support System (Overview)
- 비스니스적 측면에서 선택을 할 때 데이터 기반 선택을 하게 된다
- 데이터 분석은 특정 tool과 SQL 확장으로 간단하게 된다
- 통계적 분석 패키지는 DB로 interface 가능하다
- 데이터 마이닝은 통계적 rule과 패턴을 큰 DB로부터 알아낸다
- Data warehare는 여러 데이터를 보관한다 in single site
- Online Analytical Processing(OLAP)는 상호 관계의 데이터의 분석으로, 데이터를 요약화하고, online에서 볼 수 있도록 한다
- 데이터는 다중차원으로 모델링이 될 수 있도록 한다. (2차원 이상)
	- measure attributes는 몇몇 값들을 measure한다 (그냥 숫자 개수 등등)
	- Dimension attributes는 measure attributes들이 보이는 dimension 규정

<br><br><br>

### <span style = "color:powderblue"> ※ Cross Tabulation

![image](/images/DB-11-01.png)
- 위의 표는 cross tabulation이라고 하며, pivot table이라고도 한다
- dimension attribute의 한가지가 column, 나머지 한가지가 row이다 여기서는 color과 item name이다. 다른건 안보여줌
<br><br><br>

### <span style = "color:powderblue"> ※ Data Cube
- 바로 위에 2가지만 보여준 것은 2가지 요소만 고려하였기 때문
- 그 이상을 보여주기 위해서는 data cube를 이용 가시적으로는 3차원이 다지만 그 이상도 data cube라고 함 ^~^

![image](/images/DB-11-02.png)
<br><br><br>

### <span style = "color:powderblue"> ※ Online Analytical Processing (OLAP)
- Pivoting: cross tab에 쓰였던 차원 바꾸는 것 그러니까 row, col 변경
- slicing: cross tab를 새로 생성하는 것 칼로 자르는 것같이 생각하면 됨
- Rollup: 차원을 줄인 상태에서 컨셉 계층을 올리는 것 (ex. 도시-> 도)
- Drill down: 차원을 줄인 상태에서 컨셉 계층을 줄이는 것(도->도시)

<br><br><br>

### <span style = "color:powderblue"> ※ Data Warehousing (데이터 창고)
- 데이터 소스는 현재 데이터만 있고, 로그마냥 예전 데이터까지는 없음
- 그래서 예전 데이터까지 전~부 있는거를 만들고자 함
- Data warehouse 데이터 창고는 여러군데에서 가져온 정보를 하나의 schema로 저장한 것

![image](/images/DB-11-03.png)
- Data Cleansing
	- 잘못된 내용 지우는 것
	- 여러 소스로부터 주소 merge, 중복 purge
- update를 전파하는 방법
	- Warehouse 스키마는 데이터 소스로부터 가져온 스키마
- data 요약할 것
	- raw 데이터는 너무 커서 요약
	- Aggregate values (총합, 서브합)
	- raw data에 대한 query는 최적화해야 함

![image](/images/DB-11-04.png)
이걸 star schema라고 함 (data warehouse)
<br><br><br>

### <span style = "color:powderblue">※ Data Mining
- 큰 데이터를 조합해서 패턴 찾는 것
- past history에 기반한 예상
- Classification (범주화)
- Regression (특정 대상의 값들을 보아서 해당하는 어떤 value 예측)
- Association (유사한 것 모음)
- Clusters (원형으로 클러스터를 만듬)
<br><br><br>


### <span style = "color:powderblue">※ Classification rules


- Training set : 정답을 이미 다 알고 training을 시키는 세트
- internal node는 paritioning attribute와 partitioning condition에 따라 group화
<br><br><br>


### <span style = "color:powderblue">※ Decision Tree 구축
- classification은 다른 object들을 class 에 들어가도록 돕는다
- decision tree로 표현 가능하다
![image](/images/DB-11-05.png)
(Leaf node 에 label이 붙어있는 것을 알 수 있음)
<br><br><br>


### <span style = "color:powderblue">※ Best Splits
- 제일 나누기 좋은 기준이 무엇인가? 
- Gini, Entropy같은 방법들이 있음. 순수도를(다른 애들이 껴있지 않게) 이용
- Entropy: 데이터가 얼마나 혼잡도가 있느냐
- purity(순수도)

![image](/images/DB-11-06.png)

- information Gain이라는 개념은 이런 순수도를 이용하는 것으로, 해당 분류를 통해 얼마나 gain, 즉 이득이 생겼는지를 나타낸다. 즉 IG가 크면 클수록 변별력이 좋다고 할 수 있다.

![image](/images/DB-11-07.png)


<br><br><br>


### <span style = "color:powderblue">※ Bayesian Classifier
- Bayes’ 의 Theorem을 이용하는 것으로
![image](/images/DB-11-08.png)
-  P(cj | d)는 d가 cj에 있을 확률
-  P(d | cj)는 cj에서 d를 생성할 확률
-  P(cj)는 cj가 나타날 확률
-  P(d)는 d가 나타날 확률
![image](/images/DB-11-09.png)
- Classification을 rule을 하는 알고리즘 = classifier
<br><br><br>

### <span style = "color:powderblue"> ※ Support Vector Machine Classifier (SVM)

![image](/images/DB-11-10.png)
- margin, 즉 분리하하는 선(기준)이 각 요소들에게서 제일 멀게 되는 거리가 최대화 되어야 함.


<br><br><br>

### <span style = "color:powderblue">※ Regression
- 값에 대한 예측을 하는 것으로 다른 training set으로부터 입력이 x일시 출력 y 가 ax + b의 형식처럼 나오는 것처럼 다른 x’이 입력으로 왔을 때 ax’ + b가 출력이 되는 것을 예측하는 것. 이러한 ax+b는 linear regression이라 하며, 그렇지 않고 더 복잡한 것은 curved fitting이라 한다.
- fitting이 항상 맞다고 할 수는 없으나 정확도를 높이기 위해 존재한다.

<br><br><br>

### <span style = "color:powderblue">※ Association Rules
- 연관 rule => 대상과 연관되어 있는 것을 묶어주는 것. 청바지엔 흰티, 빵에는 우유
- Support: 얼마나 이를 충족하는가
- Confidence: 전제가 참이면 결과는 얼마나 참인가의 척도

<br><br><br>



### <span style = "color:powderblue">※ Clustering
- 군집을 찾기 위해 한가지 점을 잡아서 해당 점이 군집의 중심이 될 수 있는지 찾음 (ex. k 군집화)


<br><br><br>

### <span style = "color:powderblue">※ 다른 종류의 mining
- 텍스트 마이닝
- 데이터 시각화


<br><br><br>
<br><br><br>


# <span style = "color:Yellow">**※ 예상 질문**
## <span style = "color:Orange"> **Q1 . Data Cube와 Cross Tabulation에 대해서 설명하고, OLAP를 통해 정보를 어떻게 view로 보여주는지 설명하시오** </span>

- Cross Tabulation(pivot table)이란, 표로 특성을 row, col로 만들어서 표시하는 형식이다. Cross Tabulation은 2차원으로만 표현하는데 그러한 Cross Tabulation을 3차 이상의 것으로 합친 table을 data cube라고 한다. Cross Tabulation은 OLAP를 이용해 생성 및 가공하는데, Pivoting은 cross tab의 dimension을 바꾸는 것이고(row col), slicing은 정해진 값으로 cross tab을 만드는 것이다. Rollup은 생성된 cross Tab에서 상세 내용의 속성을 더 상위의 것으로 합치는 것을 의미한다(도시 -> 도), 이와 반대되는 개념은 Drill down으로 분리하여 더 큰 tab로 변경하는 것이다(도->도시)

## <span style = "color:Orange"> **Q2 . Data Warehouse란? 데이터를 어떻게 update하는가에 대해서 설명하시오** </span>

- Data warehouse는 데이터를 이전부터 전부 로그마냥 전부 기록하고 하나의 unifed schema에 저장하기 위해 등장하였다. 단순히 현재 상황이 아닌 이전까지의 data를 모두 포함하고 있다는 점에서 warehouse라고 부른다. 데이터를 update하기 위해서는 address를 모두 불러 와서 merge 한후, duplicate를 purge하는 방식으로 update한다.

## <span style = "color:Orange"> **Q3 . Best Split에 대해서 설명하고, 이를 하기 위해 이용한 Regression과 Clustering에 대해 설명하시오** </span>

- Best Split은 각각이 소속될수 있는 카테고리로 잘 분리 가 됐느냐를 위해서 존재하는 척도이다. a의 경우 A그룹에 속하고, b의 경우 B그룹으로 속하는데 이를 수식으로 만들기 위해 Best split을 하고자 한다. Regression은 기존에 있는 다른 값들을 input으로 하여 하나의 값을 output으로 하는 수식을 training 시키면, 다른 수가 input으로 들어올 때 해당하는 output을 출력으로 가질 수 있을것이라는 생각 하에서 등장하였다. Clustering은 군집화로, 각 군집의 중심이 될수 있는 점을 찾아서 거리에 따라 군집화를 이룰수 있는지를 찾아보는 것이다. 이에 대한 예시로는 k clustering이 있다.
