# IQ와 환경적 요인의 상관관계
##### 빅데이터 프로젝트 202144102 최민주

## 1. 데이터 시트
1. avgIQpercountry.csv
2. IQ_level.csv
3. quality_of_life.csv

## 2. 데이터 전처리
사용하는 라이브러리
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
```
3개의 데이터시트 read
```
df1 = pd.read_csv('avgIQpercountry.csv')
df2 = pd.read_csv('IQ_level.csv')
df3 = pd.read_csv('quality_of_life.csv')
```
데이터시트 국가명으로 병합, 사용하지 않는 열 제거
```
df2.drop(['IQ'], axis=1, inplace=True)
df4 = pd.merge(df1, df2, how='left', left_on='Country', right_on='country')
df5 = pd.merge(df4, df3, how='left', on='country')
df5.drop(['popularity', 'Continent', 'country', 'climate', 'costs', 'rank'],
         axis=1, inplace=True)
```
열 이름 재정의
```
df5.rename(columns = {'HDI (2021)' : 'HDI', 'Average IQ' : 'IQ',
                      'Mean years of schooling - 2021' : 'Mean years of schooling',
                      ' GNI - 2021' : 'GNI', 'Population - 2023' : 'Population',
                      'education_expenditure' : 'education expenditure'}, inplace = True)
```
순위 열을 인덱스로 지정
```
df5.set_index('Rank', inplace=True)
```
선형 보간법으로 결측치 보간
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

## 3. 히트맵
```
plt.figure(figsize=(10,10))
sns.heatmap(df5.corr(), linewidths=0.1, cmap='coolwarm', linecolor='white', annot=True)
plt.show()
```
-> IQ와 상관관계가 0.6 이상인 열
         = Literacy Rate, HDI, Mean year of schooling, GNI,avg_income, rights, health



상관관계가 낮은 열 제거 (평균 기온)
```
df5.drop(['avg_temp'], axis=1, inplace=True)
```
