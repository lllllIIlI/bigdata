# IQ와 환경적 요인의 상관관계

## 1. 데이터 시트
1. avgIQpercountry.csv
2. IQ_level.csv
3. quality_of_life.csv

## 2. 데이터 전처리
* 사용하는 라이브러리
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
```
* 3개의 데이터 시트 read
```
df1 = pd.read_csv('avgIQpercountry.csv')
df2 = pd.read_csv('IQ_level.csv')
df3 = pd.read_csv('quality_of_life.csv')
```
* 데이터시트 국가명으로 병합, 사용하지 않는 열 제거
```
df2.drop(['IQ'], axis=1, inplace=True)
df4 = pd.merge(df1, df2, how='left', left_on='Country', right_on='country')
df5 = pd.merge(df4, df3, how='left', on='country')
df5.drop(['popularity', 'Continent', 'country', 'climate', 'costs', 'rank'],
         axis=1, inplace=True)
```
* 열 이름 재정의
```
df5.rename(columns = {'HDI (2021)' : 'HDI', 'Average IQ' : 'IQ',
                      'Mean years of schooling - 2021' : 'Mean years of schooling',
                      ' GNI - 2021' : 'GNI', 'Population - 2023' : 'Population',
                      'education_expenditure' : 'education expenditure'}, inplace = True)
```
* 순위 열을 인덱스로 지정
```
df5.set_index('Rank', inplace=True)
```
* 선형 보간법으로 결측치 보간
```
print(df5.isnull().sum()) // 결측치 확인
// interpolate - 결측치 보간(선형 보간)
df5['HDI'].interpolate(method='values', inplace=True)
df5['Mean years of schooling'].interpolate(method='values', inplace=True)
df5['GNI'].interpolate(method='values', inplace=True)
df5['education expenditure'].interpolate(method='values', inplace=True)
df5['avg_income'].interpolate(method='values', inplace=True)
df5['avg_temp'].interpolate(method='values', inplace=True)
df5['stability'].interpolate(method='values', inplace=True)
df5['rights'].interpolate(method='values', inplace=True)
df5['health'].interpolate(method='values', inplace=True)
df5['safety'].interpolate(method='values', inplace=True)
print(df5)
```
![image](https://github.com/lllllIIlI/study/assets/93465102/423218f7-375b-4a65-bfe8-0a3da4ea9ceb)

## 3. 히트맵
```
plt.figure(figsize=(10,10))
sns.heatmap(df5.corr(), linewidths=0.1, cmap='coolwarm', linecolor='white', annot=True)
plt.show()
```
![히트맵](https://github.com/lllllIIlI/study/assets/93465102/0c1dad43-798e-4b14-9663-bf53bb887107) <br>
-> IQ와 상관관계가 높은 열 <br>
         = Literacy Rate, HDI, Mean year of schooling, GNI, avg_income, rights, health, avg_temp <br>
-> Novel Prices은 IQ, 환경적 요인들과 관련 ↓ <br>

* IQ와 다른 요인들간의 상관관계 지수를 데이터시트에 추가
```
heatmap_result = df5.corr()['IQ'].tolist()
heatmap_result.pop(0)
heatmap_result[8] = abs(heatmap_result[8]) // 음수인 avg_temp만 절댓값으로 변환
print(heatmap_result)
```
## 4. HDI, Mean years of schooling, Literacy Rate 연관성
HDI(인간개발지수 - 선진화 정도) <br>
Mean years of schooling (평균 교육 기간) <br>
Literacy Rate (문해력) <br>
```
fig = plt.figure(figsize=(40,10))
ax1 = fig.add_subplot(1,3,1)
ax2 = fig.add_subplot(1,3,2)
ax3 = fig.add_subplot(1,3,3)

sns.lineplot(data=df5, x ='IQ', y='HDI', ax=ax1)
sns.lineplot(data=df5, x = 'IQ', y='Mean years of schooling', ax=ax2)
sns.lineplot(data = df5, x = 'IQ', y = 'Literacy Rate', ax = ax3)
```
![다운로드](https://github.com/lllllIIlI/study/assets/93465102/f4c8abaa-4f2f-44e1-8dee-c6d196ba31af) <br>
-> HDI와 Mean years of schooling 그래프는 유사하지만, Literacy Rate는 조금씩 다름 <br>
-> 유사한 HDI와 Mean years of schooling을 하나로 묶기로 결정

## 5. education expenditure, avg_income, GNI
education expenditure (평균 교육 지출 비용($))) <br>
avg_income (평균 소득($)) <br>
GNI (국민총소득 - 국가의 경제적 활동 수준, 국민 소득 수준) <br>
```
fig = plt.figure(figsize=(40,10))
ax1 = fig.add_subplot(1,3,1)
ax2 = fig.add_subplot(1,3,2)
ax3 = fig.add_subplot(1,3,3)

sns.lineplot(data=df5, x ='IQ', y='education expenditure', ax=ax1)
sns.lineplot(data=df5, x = 'IQ', y='avg_income', ax=ax2)
sns.lineplot(data=df5, x ='IQ', y='GNI', ax=ax3)
```
![다운로드2](https://github.com/lllllIIlI/study/assets/93465102/ec5edf9b-861b-4656-bea2-3a917adc8f11) <br>
-> education expenditure, avg_income, GNI 세 그래프가 유사하여 하나로 묶기로 결정

## 6. 파이차트
* 위에서 하나로 합치기로 한 것들과 하위 세 항목은 기타로 묶음
```
plt.figure(figsize=(8, 8))
labels = df5.columns.tolist()
labels.pop(0)
labels.pop(0)
result_dict = dict(zip(labels, heatmap_result))
result_dict = dict(sorted(result_dict.items(), key=lambda x: x[1], reverse=True))

// HDI, Mean years of schooling
HDI = sum(value for key, value in result_dict.items() if key in list(['HDI','Mean years of schooling']))
del result_dict['Mean years of schooling']
result_dict['HDI'] = HDI

// GNI, education expenditure, avg_income
GNI = sum(value for key, value in result_dict.items() if key in list(['education expenditure','avg_income', 'GNI']))
del result_dict['education expenditure']
del result_dict['avg_income']
result_dict['GNI'] = GNI

// 기타 (하위 3가지)
result_dict = dict(sorted(result_dict.items(), key=lambda x: x[1], reverse=True))

threshold = -3
etc = sum(value for key, value in result_dict.items() if key in list(result_dict.keys())[threshold:])
keys_to_delete = list(result_dict.keys())[threshold:]
for key in keys_to_delete:
    del result_dict[key]
result_dict['etc'] = etc
# print(labels)
# print(result_dict)
```
* 파이차트
![파이차트](https://github.com/lllllIIlI/study/assets/93465102/1328688d-7188-443e-aedb-638007906303)
