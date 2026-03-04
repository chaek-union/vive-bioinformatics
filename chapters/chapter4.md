# 4장. Python 데이터 분석 기초

## 4.1 왜 Python인가?

생명정보학에서 Python은 사실상 표준 프로그래밍 언어이다. 데이터 처리, 시각화, 통계 분석, 머신러닝까지 거의 모든 작업을 Python 생태계 안에서 수행할 수 있다.

바이브 코딩에서는 이 패키지들의 문법을 암기할 필요가 없다. 대신 **각 패키지가 무엇을 할 수 있는지**를 알고, AI에게 정확히 요청하는 것이 중요하다. 이 장에서는 핵심 패키지들의 역할과 주요 개념을 소개한다.

## 4.2 패키지 설치

WSL 환경에서 필요한 패키지를 설치한다:

```bash
pip install pandas numpy matplotlib seaborn scipy
```

> **팁**: AI에게 "pandas로 CSV 파일 읽어서 히스토그램 그려줘"라고 요청하면, AI가 알아서 import문과 코드를 작성해준다. 하지만 **어떤 패키지가 어떤 역할을 하는지** 알아야 AI의 결과물이 맞는지 판단할 수 있다.

## 4.3 pandas — 테이블 데이터 처리

pandas는 표(테이블) 형태의 데이터를 다루는 패키지이다. 엑셀 스프레드시트를 Python에서 다룬다고 생각하면 된다.

### 핵심 개념

- **DataFrame**: 행과 열로 이루어진 2차원 테이블. pandas의 핵심 자료구조이다.
- **Series**: DataFrame의 한 열(column). 1차원 데이터이다.
- **Index**: 각 행을 식별하는 라벨.

### 주요 작업

```python
import pandas as pd

# CSV 파일 읽기
df = pd.read_csv("gene_expression.csv")

# 데이터 미리보기
df.head()          # 상위 5행
df.shape           # (행 수, 열 수)
df.describe()      # 기초 통계량

# 열 선택
df["gene_name"]              # 한 열
df[["gene_name", "logFC"]]   # 여러 열

# 조건 필터링
significant = df[df["pvalue"] < 0.05]

# 정렬
df.sort_values("logFC", ascending=False)

# 새 열 추가
df["neg_log_p"] = -np.log10(df["pvalue"])
```

![pandas DataFrame을 Jupyter Notebook에서 출력한 예시 스크린샷](../assets/ch4-01-pandas-dataframe.png)

### AI에게 요청하는 예시

> "gene_expression.csv 파일을 읽어서 pvalue가 0.05 미만인 유전자만 필터링하고, logFC 기준으로 내림차순 정렬해서 상위 20개를 보여줘"

이 요청을 하려면 **pvalue, logFC가 무엇인지**, **필터링과 정렬이라는 개념**을 알아야 한다. 코드 문법은 몰라도 되지만, 데이터의 의미는 사람이 이해하고 있어야 한다.

## 4.4 NumPy — 수치 연산

NumPy는 대규모 수치 데이터를 빠르게 처리하는 패키지이다. pandas의 내부에서도 NumPy를 사용한다.

### 핵심 개념

- **ndarray**: N차원 배열. 같은 타입의 데이터를 담는 고성능 자료구조이다.
- **브로드캐스팅**: 크기가 다른 배열 간 연산을 자동으로 확장하는 기능.
- **벡터 연산**: 반복문 없이 배열 전체에 연산을 한 번에 적용한다.

### 주요 작업

```python
import numpy as np

# 배열 생성
arr = np.array([1, 2, 3, 4, 5])
matrix = np.zeros((100, 100))    # 100x100 영행렬

# 기본 연산
arr.mean()      # 평균
arr.std()       # 표준편차
arr.max()       # 최댓값

# 벡터 연산 (반복문 불필요)
log_values = np.log2(arr + 1)    # 모든 원소에 log2 적용

# 난수 생성
random_data = np.random.normal(0, 1, size=1000)  # 정규분포
```

> **바이브 코딩에서의 활용**: NumPy 자체를 직접 쓸 일은 많지 않지만, AI가 생성하는 코드에 자주 등장한다. `np.log2`, `np.mean` 같은 표현이 나왔을 때 무엇을 하는 코드인지 이해할 수 있으면 충분하다.

## 4.5 Matplotlib — 기본 시각화

Matplotlib은 Python의 가장 기본적인 시각화 패키지이다. 거의 모든 종류의 그래프를 그릴 수 있다.

### 핵심 개념

- **Figure**: 전체 그림 영역. 하나의 Figure 안에 여러 그래프를 배치할 수 있다.
- **Axes**: 개별 그래프 영역. 실제로 데이터가 그려지는 공간이다.
- **Subplot**: Figure를 격자로 나누어 여러 그래프를 배치하는 방식.

### 주요 그래프 유형

```python
import matplotlib.pyplot as plt

# 산점도 (Scatter plot)
plt.scatter(df["logFC"], df["neg_log_p"])
plt.xlabel("Log Fold Change")
plt.ylabel("-log10(p-value)")
plt.title("Volcano Plot")
plt.savefig("volcano.png", dpi=300)
plt.show()
```

![Matplotlib으로 그린 Volcano Plot 예시](../assets/ch4-02-matplotlib-volcano.png)

```python
# 히스토그램
plt.hist(df["logFC"], bins=50)
plt.xlabel("Log Fold Change")
plt.ylabel("Frequency")
plt.show()
```

```python
# 여러 그래프 한 번에 (Subplot)
fig, axes = plt.subplots(1, 2, figsize=(12, 5))
axes[0].hist(df["logFC"], bins=50)
axes[0].set_title("Distribution of logFC")
axes[1].scatter(df["logFC"], df["neg_log_p"], s=1)
axes[1].set_title("Volcano Plot")
plt.tight_layout()
plt.savefig("combined.png", dpi=300)
```

![Subplot으로 두 개의 그래프를 나란히 배치한 예시](../assets/ch4-03-matplotlib-subplot.png)

### AI에게 요청하는 예시

> "logFC와 -log10(pvalue)로 volcano plot 그려줘. significant한 유전자(pvalue < 0.05, |logFC| > 1)는 빨간색으로 표시하고, 나머지는 회색으로. 그래프 해상도는 300 dpi로 저장해줘"

## 4.6 Seaborn — 통계 시각화

Seaborn은 Matplotlib 위에 구축된 **통계 시각화 전문 패키지**이다. 더 적은 코드로 보기 좋은 통계 그래프를 만들 수 있다.

### Matplotlib과의 차이

| | Matplotlib | Seaborn |
|---|---|---|
| **수준** | 저수준 (세밀한 제어) | 고수준 (간결한 코드) |
| **스타일** | 기본 스타일 단순함 | 기본 스타일이 깔끔함 |
| **통계 기능** | 직접 구현 필요 | 회귀선, 분포 등 내장 |
| **DataFrame 연동** | 수동 | 직접 지원 |

### 주요 그래프 유형

```python
import seaborn as sns

# 박스 플롯 — 그룹별 분포 비교
sns.boxplot(data=df, x="cell_type", y="expression")
plt.xticks(rotation=45)
plt.show()
```

![Seaborn 박스 플롯 예시 — 세포 유형별 유전자 발현량 분포](../assets/ch4-04-seaborn-boxplot.png)

```python
# 히트맵 — 유전자 발현 매트릭스 시각화
sns.heatmap(expression_matrix, cmap="RdBu_r", center=0)
plt.title("Gene Expression Heatmap")
plt.show()
```

![Seaborn 히트맵 예시 — 유전자 발현 매트릭스](../assets/ch4-05-seaborn-heatmap.png)

```python
# 바이올린 플롯 — 분포의 형태까지 표현
sns.violinplot(data=df, x="condition", y="expression")
plt.show()
```

```python
# 산점도 + 회귀선
sns.regplot(data=df, x="gene_A", y="gene_B")
plt.show()
```

### AI에게 요청하는 예시

> "cell_type별 gene expression의 분포를 violin plot으로 비교해줘. 색상은 pastel 팔레트 사용하고, 각 그룹의 데이터 포인트도 strip plot으로 겹쳐서 보여줘"

## 4.7 SciPy — 과학 계산과 통계 검정

SciPy는 과학 계산에 필요한 다양한 알고리즘을 제공한다. 생명정보학에서는 주로 **통계 검정** 기능을 사용한다.

### 자주 사용하는 통계 검정

```python
from scipy import stats

# t-test — 두 그룹의 평균 비교
t_stat, p_value = stats.ttest_ind(group_a, group_b)
print(f"t-statistic: {t_stat:.4f}, p-value: {p_value:.4e}")

# Mann-Whitney U test — 비모수 검정 (정규분포 가정 불필요)
u_stat, p_value = stats.mannwhitneyu(group_a, group_b)

# Pearson 상관계수
corr, p_value = stats.pearsonr(gene_a_expression, gene_b_expression)
print(f"Correlation: {corr:.4f}, p-value: {p_value:.4e}")

# 다중 검정 보정 (Benjamini-Hochberg)
from scipy.stats import false_discovery_control
adjusted_pvalues = false_discovery_control(p_values, method='bh')
```

### AI에게 요청하는 예시

> "treatment 그룹과 control 그룹의 gene expression을 t-test로 비교해줘. p-value가 0.05 미만인 유전자 목록을 뽑아주고, Benjamini-Hochberg 보정도 적용해줘"

이 요청에서 **t-test가 무엇인지**, **다중 검정 보정이 왜 필요한지**를 이해하고 있어야 AI의 결과를 올바르게 해석할 수 있다.

## 4.8 바이브 코딩 실전: AI와 함께하는 데이터 분석

### 워크플로우

바이브 코딩으로 데이터 분석을 할 때의 일반적인 흐름이다:

1. **데이터 확인**: "이 CSV 파일의 구조를 보여줘" → AI가 `pd.read_csv`와 `df.head()`, `df.describe()` 실행
2. **전처리**: "결측값 제거하고, gene_name 열을 인덱스로 설정해줘" → AI가 `dropna()`, `set_index()` 적용
3. **분석**: "두 그룹 간 차이가 있는 유전자를 찾아줘" → AI가 통계 검정 수행
4. **시각화**: "결과를 volcano plot으로 그려줘" → AI가 Matplotlib/Seaborn으로 시각화
5. **해석**: 결과를 사람이 확인하고, 추가 분석 방향을 지시

### 핵심 포인트

| 사람이 해야 할 일 | AI가 해주는 일 |
|---|---|
| 분석 목표 설정 | 코드 작성 |
| 적절한 분석 방법 선택 | 패키지 import 및 함수 호출 |
| 결과 해석 | 그래프 생성 및 통계량 계산 |
| 생물학적 의미 판단 | 데이터 전처리 및 변환 |

> **핵심**: 코드를 외울 필요는 없다. 하지만 **"박스 플롯은 분포를 비교할 때 쓴다"**, **"t-test는 두 그룹의 평균을 비교한다"**, **"p-value가 작을수록 통계적으로 유의하다"** 같은 개념은 반드시 이해해야 한다. 이것이 AI에게 올바른 지시를 내리고, AI의 결과를 검증하는 힘이 된다.

## 4.9 정리

- **pandas**: 테이블 데이터(CSV, TSV) 읽기, 필터링, 정렬, 집계
- **NumPy**: 수치 배열 연산, 수학 함수 (log, mean, std 등)
- **Matplotlib**: 기본 그래프 (산점도, 히스토그램, 서브플롯)
- **Seaborn**: 통계 시각화 (박스 플롯, 히트맵, 바이올린 플롯)
- **SciPy**: 통계 검정 (t-test, 상관분석, 다중 검정 보정)
- **바이브 코딩의 핵심**: 문법이 아닌 **개념**을 이해하고, AI에게 정확히 요청하는 것
