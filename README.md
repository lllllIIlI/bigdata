# IQ와 환경적 요인의 상관관계
##### 빅데이터 프로젝트 202144102 최민주

## 1. 데이터 전처리
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px

df1 = pd.read_csv('avgIQpercountry.csv')
df2 = pd.read_csv('IQ_level.csv')
df3 = pd.read_csv('quality_of_life.csv')
df2.drop(['IQ'], axis=1, inplace=True)
df4 = pd.merge(df1, df2, how='left', left_on='Country', right_on='country')
df5 = pd.merge(df4, df3, how='left', on='country')
df5.drop(['popularity', 'Continent', 'country', 'climate', 'costs', 'rank'],
         axis=1, inplace=True)
df5.rename(columns = {'HDI (2021)' : 'HDI', 'Average IQ' : 'IQ',
                      'Mean years of schooling - 2021' : 'Mean years of schooling',
                      ' GNI - 2021' : 'GNI', 'Population - 2023' : 'Population',
                      'education_expenditure' : 'education expenditure'}, inplace = True)
df5.set_index('Rank', inplace=True)

print(df5.isnull().sum()) // 결측치 확인
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
# print(df5)
# print(df5.isnull().sum())
