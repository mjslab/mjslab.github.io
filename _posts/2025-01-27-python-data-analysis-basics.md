---
title: "Python으로 시작하는 데이터 분석"
date: 2025-01-27 16:45:00 +0900
categories: [Data Science, Python]
tags: [Python, 데이터분석, pandas, matplotlib, numpy]
author: mjs
description: "Python을 활용한 기초 데이터 분석 방법과 주요 라이브러리 사용법을 알아봅니다."
---

## 데이터 분석의 시작

데이터 분석은 현대 연구와 비즈니스에서 필수적인 기술이 되었습니다. Python은 강력한 라이브러리들 덕분에 데이터 분석 분야에서 가장 인기 있는 언어 중 하나입니다.

## 핵심 라이브러리

### 1. NumPy - 수치 연산의 기초

```python
import numpy as np

# 배열 생성
arr = np.array([1, 2, 3, 4, 5])
matrix = np.array([[1, 2], [3, 4]])

# 기본 통계
print(f"평균: {np.mean(arr)}")
print(f"표준편차: {np.std(arr)}")
print(f"최댓값: {np.max(arr)}")
```

### 2. Pandas - 데이터 조작과 분석

```python
import pandas as pd

# 데이터프레임 생성
data = {
    'name': ['Alice', 'Bob', 'Charlie', 'Diana'],
    'age': [25, 30, 35, 28],
    'salary': [50000, 60000, 70000, 55000]
}
df = pd.DataFrame(data)

# 기본 정보 확인
print(df.head())
print(df.describe())
print(df.info())

# 데이터 필터링
young_employees = df[df['age'] < 30]
high_salary = df[df['salary'] > 55000]
```

### 3. Matplotlib - 데이터 시각화

```python
import matplotlib.pyplot as plt

# 기본 그래프
plt.figure(figsize=(10, 6))

# 선 그래프
plt.subplot(1, 2, 1)
plt.plot(df['age'], df['salary'], 'bo-')
plt.title('나이별 급여')
plt.xlabel('나이')
plt.ylabel('급여')

# 히스토그램
plt.subplot(1, 2, 2)
plt.hist(df['age'], bins=5, alpha=0.7)
plt.title('나이 분포')
plt.xlabel('나이')
plt.ylabel('빈도')

plt.tight_layout()
plt.show()
```

## 실제 데이터 분석 워크플로우

### 1. 데이터 로드 및 탐색

```python
# CSV 파일 읽기
df = pd.read_csv('data.csv')

# 데이터 크기 확인
print(f"데이터 크기: {df.shape}")

# 결측값 확인
print(df.isnull().sum())

# 데이터 타입 확인
print(df.dtypes)
```

### 2. 데이터 정제

```python
# 결측값 처리
df_clean = df.dropna()  # 결측값 제거
# 또는
df_filled = df.fillna(df.mean())  # 평균값으로 채우기

# 이상값 제거
Q1 = df['salary'].quantile(0.25)
Q3 = df['salary'].quantile(0.75)
IQR = Q3 - Q1
df_no_outliers = df[~((df['salary'] < (Q1 - 1.5 * IQR)) | 
                      (df['salary'] > (Q3 + 1.5 * IQR)))]
```

### 3. 기술 통계 분석

```python
# 그룹별 분석
age_groups = df.groupby('department')['salary'].agg([
    'mean', 'median', 'std', 'count'
])

# 상관관계 분석
correlation_matrix = df.corr()
print(correlation_matrix)
```

## 유용한 팁들

### 1. 판다스 체이닝
```python
result = (df
          .query('age > 25')
          .groupby('department')
          .agg({'salary': 'mean'})
          .round(2)
         )
```

### 2. 플롯 스타일링
```python
plt.style.use('seaborn-v0_8')
plt.rcParams['figure.figsize'] = (12, 8)
plt.rcParams['font.size'] = 12
```

### 3. 메모리 최적화
```python
# 데이터 타입 최적화
df['category'] = df['category'].astype('category')
df['age'] = pd.to_numeric(df['age'], downcast='integer')
```

## 다음 단계

데이터 분석의 기초를 익혔다면 다음 단계로 나아가보세요:

- **Seaborn**: 더 아름다운 통계 시각화
- **Plotly**: 인터랙티브 차트 생성
- **Scikit-learn**: 머신러닝 모델 구축
- **Jupyter Notebook**: 대화형 분석 환경

## 마무리

Python을 활용한 데이터 분석은 무궁무진한 가능성을 제공합니다. 작은 데이터셋부터 시작해서 점차 복잡한 분석으로 확장해 나가세요.

다음 포스트에서는 실제 프로젝트 예제를 통해 더 깊이 있는 분석 방법을 다루겠습니다.


test3
---

*데이터 분석에 대한 궁금한 점이 있으시면 언제든 댓글로 질문해주세요!*