# 5장. Scanpy를 이용한 단일세포 데이터 분석

## 5.1 단일세포 분석이란?

전통적인 bulk RNA-seq은 수천~수백만 개의 세포를 한꺼번에 분석하여 **평균적인** 유전자 발현을 측정한다. 이 방식은 조직 전체의 발현 경향을 파악하는 데는 유용하지만, 조직 안에 어떤 종류의 세포가 있는지, 각 세포 유형에서 어떤 유전자가 활성화되어 있는지는 알 수 없다. 마치 과일 주스를 마시면서 어떤 과일이 들어갔는지는 알 수 있지만, 각 과일의 비율은 알 수 없는 것과 비슷하다.

단일세포 RNA-seq(scRNA-seq)은 **개별 세포 하나하나**의 유전자 발현을 측정한다. 이를 통해 다음과 같은 질문에 답할 수 있다:

- 이 조직에는 어떤 종류의 세포들이 있는가?
- 각 세포 유형의 비율은 어떻게 되는가?
- 특정 질환에서 어떤 세포 유형이 변화하는가?
- 세포들 사이의 발달 경로(trajectory)는 어떠한가?

10x Genomics의 Chromium 플랫폼 덕분에 한 번의 실험으로 수천~수만 개의 세포를 동시에 분석할 수 있게 되었고, 이제 단일세포 분석은 생명과학 연구의 핵심 기술로 자리잡았다. Scanpy는 이러한 단일세포 데이터를 분석하기 위한 Python 패키지다.

## 5.2 패키지 설치

```bash
pip install scanpy anndata mudata
```

- **scanpy**: 단일세포 분석의 핵심 패키지. 전처리, 클러스터링, 시각화 등을 지원
- **anndata**: h5ad 파일 형식을 다루는 패키지. Scanpy의 데이터 구조
- **mudata**: h5mu 파일(멀티오믹스 데이터)을 다루는 패키지

Scanpy는 **scverse** 생태계의 일부이다. scverse는 단일세포 데이터 분석을 위한 Python 패키지 모음으로, Scanpy를 중심으로 squidpy(공간 전사체), scvi-tools(딥러닝 기반 분석), muon(멀티오믹스) 등이 포함된다. 하나의 일관된 데이터 구조(AnnData)를 공유하므로, 여러 패키지를 조합하여 분석할 수 있다.

## 5.3 AnnData — 단일세포 데이터 구조

### h5ad 파일이란?

h5ad는 단일세포 데이터의 **표준 저장 형식**이다. HDF5 기반으로, 대용량 데이터를 효율적으로 저장하고 읽을 수 있다. 10,000개의 세포와 20,000개의 유전자를 포함하는 데이터를 CSV로 저장하면 수 GB가 되지만, h5ad로 저장하면 수십~수백 MB로 줄어든다.

하나의 h5ad 파일에는 다음 정보가 모두 담겨 있다:

| 속성 | 설명 | 예시 |
|------|------|------|
| **`X`** | 유전자 발현 매트릭스 (세포 × 유전자) | 10,000 세포 × 20,000 유전자 |
| **`obs`** | 세포(행)에 대한 메타데이터 | cell_type, sample_id, condition |
| **`var`** | 유전자(열)에 대한 메타데이터 | gene_name, highly_variable |
| **`obsm`** | 세포의 임베딩 좌표 | UMAP, t-SNE, PCA 좌표 |
| **`uns`** | 비구조화 데이터 | 색상 팔레트, 분석 파라미터 |

![AnnData 객체의 구조를 나타낸 다이어그램](../assets/ch5-01-anndata-structure.png)

이 구조의 장점은 **하나의 파일에 분석에 필요한 모든 것이 담겨 있다**는 점이다. 발현 데이터, 세포 메타데이터, 분석 결과(UMAP 좌표, 클러스터 레이블 등)가 하나의 객체에 통합되어 있으므로, 데이터를 주고받을 때 파일 하나만 전달하면 된다.

### 기본 사용법

```python
import scanpy as sc
import anndata as ad

# h5ad 파일 읽기
adata = sc.read_h5ad("pbmc_10k.h5ad")

# 데이터 구조 확인
print(adata)
# AnnData object with n_obs × n_vars = 10000 × 20000
#     obs: 'cell_type', 'sample', 'condition'
#     var: 'gene_name', 'highly_variable'
#     obsm: 'X_pca', 'X_umap'

# 세포 메타데이터 확인 (pandas DataFrame)
adata.obs.head()

# 유전자 메타데이터 확인
adata.var.head()

# 특정 세포 유형만 선택
t_cells = adata[adata.obs["cell_type"] == "T cell"]

# 특정 유전자의 발현량 확인
adata[:, "CD3E"].X.toarray()
```

`adata.obs`는 pandas DataFrame이므로, 4장에서 배운 필터링과 정렬을 그대로 사용할 수 있다. `adata[adata.obs["cell_type"] == "T cell"]`은 T 세포만 골라내는 필터링이다.

### AI에게 요청하는 예시

> "pbmc_10k.h5ad 파일을 읽어서 어떤 cell_type이 있는지, 각 유형별 세포 수를 보여줘"

## 5.4 MuData — 멀티오믹스 데이터

### h5mu 파일이란?

최근에는 하나의 세포에서 RNA와 단백질(CITE-seq), 또는 RNA와 염색질 접근성(10x Multiome)을 동시에 측정하는 **멀티오믹스** 기술이 발전하고 있다. h5mu는 이런 멀티오믹스 데이터를 저장하는 형식이다. 하나의 파일에 여러 종류의 데이터(modality)를 함께 담을 수 있다.

```python
import mudata as md

# h5mu 파일 읽기
mdata = md.read_h5mu("multiome.h5mu")

# 어떤 modality가 포함되어 있는지 확인
print(mdata.mod)
# {'rna': AnnData object with n_obs × n_vars = 5000 × 20000,
#  'atac': AnnData object with n_obs × n_vars = 5000 × 100000}

# 개별 modality 접근
rna = mdata.mod["rna"]    # RNA 데이터 (AnnData)
atac = mdata.mod["atac"]  # ATAC 데이터 (AnnData)
```

![MuData 객체의 구조 다이어그램](../assets/ch5-02-mudata-structure.png)

각 modality는 독립적인 AnnData 객체이므로, RNA 데이터에는 Scanpy를, ATAC 데이터에는 SnapATAC이나 ArchR의 Python 래퍼를 사용하는 식으로 각각에 적합한 분석 방법을 적용할 수 있다.

## 5.5 Scanpy 분석 워크플로우

단일세포 분석은 보통 다음 순서로 진행된다. 각 단계가 왜 필요한지 이해하면 AI에게 정확한 분석을 요청할 수 있다.

### 품질 관리 (Quality Control)

생 데이터에는 품질이 낮은 세포(죽은 세포, 이중 캡처 등)가 포함되어 있다. 이런 세포를 걸러내지 않으면 분석 결과가 왜곡된다.

주요 QC 지표:
- **유전자 수(n_genes_by_counts)**: 한 세포에서 검출된 유전자가 너무 적으면(예: 200개 미만) 품질이 낮은 세포일 가능성이 높다
- **미토콘드리아 유전자 비율(pct_counts_mt)**: 미토콘드리아 유전자의 비율이 높으면(예: 20% 이상) 세포가 손상되었을 가능성이 있다. 죽어가는 세포에서 세포질 RNA는 빠져나가지만 미토콘드리아 RNA는 남아 있기 때문이다

```python
# 미토콘드리아 유전자 비율 계산
adata.var["mt"] = adata.var_names.str.startswith("MT-")
sc.pp.calculate_qc_metrics(adata, qc_vars=["mt"], inplace=True)

# QC 시각화
sc.pl.violin(adata, ["n_genes_by_counts", "total_counts", "pct_counts_mt"])
```

![QC 바이올린 플롯](../assets/ch5-03-qc-violin-plots.png)

```python
# 품질이 낮은 세포 제거
adata = adata[adata.obs["n_genes_by_counts"] > 200]
adata = adata[adata.obs["pct_counts_mt"] < 20]
```

### 정규화 및 전처리

각 세포마다 시퀀싱 깊이(total read count)가 다르다. 어떤 세포는 10,000개의 리드를, 다른 세포는 50,000개의 리드를 가질 수 있다. 정규화는 이 차이를 보정하여 세포 간 공정한 비교를 가능하게 한다.

```python
# 정규화: 모든 세포의 총 카운트를 10,000으로 맞춤
sc.pp.normalize_total(adata, target_sum=1e4)
# log 변환: 발현량의 분포를 정규분포에 가깝게 만듦
sc.pp.log1p(adata)

# 고변동 유전자 선택: 세포 간 발현 차이가 큰 유전자만 선택
sc.pp.highly_variable_genes(adata, n_top_genes=2000)
sc.pl.highly_variable_genes(adata)
```

**고변동 유전자(HVG, Highly Variable Genes)** 선택이 중요한 이유가 있다. 2만 개의 유전자 중 대부분은 모든 세포에서 비슷하게 발현되거나 아예 발현되지 않는다. 세포 유형을 구분하는 데 실제로 기여하는 유전자는 일부에 불과하다. HVG 선택은 이런 정보성 높은 유전자만 골라내어 이후 분석의 효율과 정확도를 높인다.

### 차원 축소

단일세포 데이터는 세포 × 유전자의 고차원 매트릭스이다. 2,000개의 유전자를 사용한다면 각 세포는 2,000차원 공간의 한 점이다. 이 고차원 데이터를 사람이 볼 수 있는 2차원으로 축소해야 시각화가 가능하다.

```python
# PCA: 2,000차원 → 50차원으로 압축 (주요 변동 성분 추출)
sc.tl.pca(adata)
sc.pl.pca_variance_ratio(adata, n_pcs=50)

# 이웃 그래프 구축 + UMAP: 50차원 → 2차원으로 시각화
sc.pp.neighbors(adata, n_pcs=30)
sc.tl.umap(adata)
```

PCA(주성분 분석)는 데이터의 변동을 가장 잘 설명하는 축을 찾아 차원을 줄인다. UMAP(Uniform Manifold Approximation and Projection)은 고차원에서 가까운 세포들이 2차원에서도 가깝게 위치하도록 배치하는 알고리즘이다. 비슷한 유전자 발현 패턴을 가진 세포들이 UMAP에서 군집을 형성한다.

### 클러스터링

차원 축소 후, 유사한 세포들을 그룹(클러스터)으로 묶는다. 각 클러스터는 하나의 세포 유형에 대응할 가능성이 높다.

```python
# Leiden 클러스터링
sc.tl.leiden(adata, resolution=0.5)

# UMAP에 클러스터 표시
sc.pl.umap(adata, color="leiden")
```

![UMAP 플롯](../assets/ch5-04-umap-clusters.png)

`resolution` 파라미터는 클러스터의 세분화 정도를 조절한다. 값이 클수록 더 많은 클러스터가 생성되고, 작을수록 큰 그룹으로 묶인다. 적절한 resolution은 데이터와 연구 목적에 따라 다르며, 여러 값을 시도해보면서 생물학적으로 의미 있는 결과를 찾아야 한다.

### 세포 유형 주석

클러스터링 결과에 생물학적 의미를 부여하는 단계이다. 각 클러스터에서 특이적으로 높게 발현되는 **마커 유전자**를 확인하고, 이를 바탕으로 세포 유형을 결정한다.

```python
# 클러스터별 마커 유전자 확인
sc.tl.rank_genes_groups(adata, groupby="leiden")
sc.pl.rank_genes_groups(adata, n_genes=10)

# 특정 마커 유전자로 UMAP 시각화
sc.pl.umap(adata, color=["CD3E", "CD14", "MS4A1", "NKG7"])
```

![마커 유전자별 UMAP 플롯](../assets/ch5-05-umap-marker-genes.png)

예를 들어 CD3E가 높게 발현되는 클러스터는 T 세포, CD14가 높은 클러스터는 단핵구(monocyte), MS4A1(CD20)이 높은 클러스터는 B 세포일 가능성이 높다. 이런 마커 유전자는 면역학, 세포생물학 등 도메인 지식에 기반한다.

### 결과 저장

```python
# h5ad 파일로 저장 — 모든 분석 결과가 함께 저장됨
adata.write_h5ad("pbmc_analyzed.h5ad")
```

## 5.6 바이브 코딩으로 단일세포 분석하기

### 실전 대화 예시

단일세포 분석의 전체 과정을 AI와 대화하며 진행할 수 있다:

> **사용자**: "pbmc_10k.h5ad 파일을 읽고 QC 해줘. 미토콘드리아 비율 20% 이상, 유전자 수 200개 미만인 세포는 제거해줘"
>
> **사용자**: "정규화하고 고변동 유전자 2000개 선택해서 PCA, UMAP 해줘"
>
> **사용자**: "Leiden 클러스터링 해주고, 각 클러스터의 마커 유전자 상위 5개씩 보여줘"
>
> **사용자**: "CD3E, CD14, MS4A1, NKG7 마커로 봤을 때 각 클러스터가 어떤 세포 유형인지 UMAP에 표시해줘"

이 대화에서 사용자는 코드를 한 줄도 작성하지 않았지만, **각 단계가 왜 필요한지, 어떤 파라미터를 사용해야 하는지**를 알고 있었기 때문에 정확한 지시를 내릴 수 있었다.

### 알아야 할 핵심 개념

코드를 외울 필요는 없지만, AI에게 올바른 지시를 내리려면 다음 개념들을 이해해야 한다:

| 개념 | 설명 |
|------|------|
| **QC (Quality Control)** | 품질이 낮은 세포를 걸러내는 과정. 미토콘드리아 비율, 유전자 수가 주요 지표 |
| **정규화 (Normalization)** | 세포 간 시퀀싱 깊이 차이를 보정하는 과정 |
| **고변동 유전자 (HVG)** | 세포 간 발현 차이가 큰 유전자. 세포 유형 구분의 핵심 |
| **PCA** | 고차원 데이터를 주요 성분으로 압축. 노이즈 제거 효과도 있음 |
| **UMAP** | 고차원 데이터를 2D로 시각화. 비슷한 세포끼리 가깝게 배치 |
| **클러스터링** | 유사한 세포를 그룹으로 묶는 과정. Leiden 알고리즘이 표준 |
| **마커 유전자** | 특정 세포 유형을 구분하는 유전자. 도메인 지식이 필요 |

## 5.7 정리

- **단일세포 RNA-seq**: 개별 세포 수준에서 유전자 발현을 측정하는 기술. bulk RNA-seq과 달리 세포 유형별 분석이 가능
- **h5ad**: 단일세포 데이터의 표준 파일 형식. 발현 매트릭스, 세포/유전자 메타데이터, 임베딩 좌표를 하나의 파일에 저장
- **h5mu**: 멀티오믹스 데이터 형식. 여러 modality(RNA, ATAC 등)를 하나의 파일에 통합
- **AnnData**: h5ad의 Python 객체. `adata.obs`(세포 정보), `adata.var`(유전자 정보), `adata.X`(발현 매트릭스)로 구성
- **Scanpy 워크플로우**: QC → 정규화 → 고변동 유전자 선택 → PCA → UMAP → 클러스터링 → 세포 유형 주석
- **바이브 코딩의 핵심**: 분석 파이프라인의 각 단계가 **왜 필요한지**를 이해하고, AI에게 단계별로 지시하는 것
