## Pandas 기초 정리

- #### 학습목표

  - ##### pandas 기초를 이해하고 사용한다.

#### 

- #### Refer

  - #### [pandas-tutorial(DOCS)](https://pandas.pydata.org/pandas-docs/stable/10min.html#min)

  - #### [일별데이터를 주별로 묶기](http://jsideas.net/python/2015/08/30/daily_to_weekly.html)

  - #### [axis란?](https://stackoverflow.com/questions/22149584/what-does-axis-in-pandas-mean)

#### 

- #### Import Convention

  - ##### pandas에서 자주 쓰이는 라이브러리들로 import 컨벤션을 다음과 같이 하는 게 편리하다.

  ```python
  from pandas import Series, DataFrame
  import pandas as pd
  import numpy as np
  import matplotlib.pyplot as plt
  ```



- #### Object Creation

  - ##### 대표적인 자료구조 `Series`, `DataFrame`

  - ##### `Series`: index값을 가진 1차원 배열 같은 자료구조

    ```python
    s = Series([1,3,5,6,8])
    ```

  - ##### `DataFrame`: 표 같은 스프레드시트 형식의 자료구조

    - column 값을 key로 하고 index 값의 형식이 같은 `Series` 객체의 모음

    ```python
    dates = pd.date_range('20130101', periods=6)
    df = DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD')) # 6*4 크기의 Frame df생성
    ```

### 

- #### Selection

  - ##### data를 가져오는 기본 방법

  ```python
  df['A']
  df[0:3] # slice 문법 사용 가능
  df['20130102':'20130104']
  ```

  - ##### selection by Label

    - ##### `at`, `loc`

  ```Python
  df.loc[dates[0]] # 선택한 행의 데이터 가져오기
  df.loc['20130102':'20130104',['A','B']] # 선택한 행과 열의 데이터 가져오기
  df.loc[dates[0], 'A'] # 해당 필드의 스칼라 값 가져오기
  df.at[dates[0], 'A'] # 스칼라값에 빠르게 접근 가능?( 위와 동일한 메서드 )
  ```

  - ##### selection by position

    - ##### `iat`, `iloc`

  ```python
  df.iloc[3]
  df.iloc[3:5, 0:2] # row, col
  df.iloc[[1,2,4],[0,2]]
  df.iloc[1, 1]
  df.iat[1, 1] # 스칼라값에 빠르게 접근 가능?( 위와 동일한 메서드 )
  ```

  - ##### label과 position 모두 사용 가능한 `ix`

  ```python
  df.ix[dates[0]]
  df.ix['20130101']
  df.ix[0]
  df.ix[0, 'A'] # ix는  Label 및 Position index로 모두 접근 가능
  ```

  ​

- #### Viewing Data

  - ##### `axis`값이 나오는데 `axis`=0은 행(row), `axis`=1은 열(col)을 의미

  - ##### `axis`값 뿐 아니라 `pandas`에서 row, col값이 `positional arg`로 쓰이는 곳이 있다면 대부분 (row, col)순이다. 

    - Ex) df.ix[row, col] -> row행 col열에 있는 값 선택

  ```python
  df.head(2) # 앞에서 2개의 행(row)만 보여준다.
  df.tail(3) # 뒤에서 3개의 행(row)만 보여준다.
  df.index # 해당 Frame의 index 값 확인
  df.columns # 해당 Frame의 coloumn 값 확인
  df.values # 해당 Frame의 values 값 확인
  df.describe() # 각 열(col)별로 해당 Frame 데이터들의 정해진 통계값들을 보여준다.
  df.T # 전치행열(행과 열 뒤짚기)
  df.sort_index(axis=1, ascending=True) # 열(col)의 key값을 기준으로 오름차순 정렬
  df.sort_values(by='A', ascending=False) # A열의 value을 기준으로 내림차순 정렬
  ```



- #### Missing Data

  - ##### `pandas`는 기본적으로 `np.nan`으로 missing data를 표현한다.

  - ##### reindexing은 index의 수정/추가/삭제가 가능하고 복사된 값을 return한다.

  ```python
  df1 = df.reindex(index=dates[0:4], columns=list(df.columns) + ['E']) #   row를 0~4번째까지 자르고, col에는 'E'열 추가
  df1.loc[dates[0]:dates[1],'E'] = 1
  ```

  - ##### 데이터가 누락된 값이 있는 행(row)을 삭제하는 함수`dropna`

  ```python
  df1.dropna(how='any') # 데이터가 누락된 값이 잇는 행(row)을 삭제한다.
  ```

  - ##### `nan`값을  설정 값으로 채우는 함수 `fillna`

  ```python
  df1.fillna(value=5) # nan값을 해당 값으로 채운다.
  ```

  - ##### 값이 nan인지 아닌지 확인하는 함수 `isna`

  ```python
  pd.isna(df1) # 값이 nan이면 true 아니면 False
  ```

  ​