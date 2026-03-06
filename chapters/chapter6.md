# 6장. Snakemake를 이용한 워크플로우 관리

## 6.1 워크플로우란?

생명정보학 분석은 보통 여러 단계를 순서대로 수행한다. 예를 들어 RNA-seq 분석은 다음과 같은 흐름을 따른다:

1. FASTQ 파일 품질 확인 (FastQC)
2. 어댑터 트리밍 (Trim Galore)
3. 레퍼런스 게놈에 정렬 (STAR)
4. 발현량 정량 (featureCounts)
5. 차등 발현 분석 (DESeq2)

이 과정을 매번 수동으로 실행하면 시간이 오래 걸리고, 실수가 생기기 쉽다. 터미널에서 명령을 하나씩 입력하다 보면 순서를 잘못 바꾸거나, 이전 단계의 출력 파일 이름을 틀리거나, 새 샘플이 추가되었는데 일부 단계만 다시 실행하는 등의 문제가 생긴다. 샘플이 3개뿐이라면 수동으로 관리할 수 있겠지만, 50개 샘플을 다룬다면 사실상 불가능하다.

**Snakemake는 이러한 분석 파이프라인을 자동화하는 워크플로우 관리 도구**이다. Python 기반 문법을 사용하여 각 분석 단계의 입력과 출력을 정의하면, Snakemake가 자동으로 실행 순서를 결정하고 필요한 단계만 실행해 준다.

### Snakemake의 장점

- **재현성**: 같은 Snakefile로 언제든 동일한 분석을 반복할 수 있다. 논문 리뷰어가 "분석을 다시 돌려보라"고 요청해도, 명령 하나로 전체 파이프라인을 재실행할 수 있다.
- **자동 의존성 관리**: 어떤 단계를 먼저 실행해야 하는지 자동으로 판단한다. 정렬 결과가 없으면 정렬부터 실행하고, 트리밍 결과가 없으면 트리밍부터 실행한다.
- **병렬 실행**: 독립적인 단계는 동시에 실행하여 시간을 절약한다. sample_A의 정렬과 sample_B의 정렬은 서로 의존하지 않으므로 동시에 실행할 수 있다.
- **부분 재실행**: 중간에 실패하면 실패한 단계부터 다시 시작한다. 50개 샘플 중 3개에서만 에러가 났다면, 성공한 47개는 건드리지 않고 3개만 다시 처리한다.

### 다른 워크플로우 도구와의 비교

생명정보학에는 Snakemake 외에도 여러 워크플로우 도구가 있다.

| 도구 | 언어 | 특징 |
|------|------|------|
| **Snakemake** | Python | 생명정보학에서 가장 널리 사용, Conda 통합 |
| **Nextflow** | Groovy/DSL | 클라우드 환경에 강점, nf-core 생태계 |
| **WDL** | 자체 DSL | Broad Institute 개발, Terra 플랫폼 연동 |
| **CWL** | YAML/JSON | 표준 규격, 이식성 높음 |

이 책에서는 Python 문법과 가장 유사하여 진입 장벽이 낮고, Bioconda와의 통합이 뛰어난 Snakemake를 사용한다.

## 6.2 설치

5장에서 설치한 micromamba를 사용하여 Snakemake를 설치한다:

```bash
pip install snakemake
```

> **참고**: 복잡한 생명정보학 파이프라인에서는 Conda와 함께 사용하는 것이 일반적이다. 각 rule에 독립적인 Conda 환경을 지정할 수 있어, STAR는 STAR의 환경에서, DESeq2는 R의 환경에서 실행하는 것이 가능하다. 이렇게 하면 서로 다른 도구의 의존성이 충돌하는 문제를 피할 수 있다.

## 6.3 Snakemake 핵심 개념

### Rule (규칙)

Snakemake의 기본 단위는 **rule**이다. 하나의 rule은 **입력 → 처리 → 출력**을 정의한다. 요리 레시피에 비유하면, "재료(input)를 가져와서 조리(shell)하고 완성된 요리(output)를 내놓는다"는 구조이다.

```python
rule fastqc:
    input:
        "data/{sample}.fastq.gz"
    output:
        "results/fastqc/{sample}_fastqc.html"
    shell:
        "fastqc {input} -o results/fastqc/"
```

이 rule은 "data 폴더의 FASTQ 파일을 입력으로 받아 FastQC를 실행하고, 결과 HTML을 results/fastqc 폴더에 저장한다"는 의미이다. Snakemake는 output 파일이 이미 존재하면 해당 rule을 건너뛴다. 불필요한 재실행을 자동으로 방지하는 것이다.

### Wildcard (와일드카드)

`{sample}` 같은 와일드카드를 사용하면 **여러 샘플에 같은 규칙을 자동 적용**할 수 있다. 예를 들어 `sample_A.fastq.gz`, `sample_B.fastq.gz`, `sample_C.fastq.gz`가 있으면, 위 rule이 세 파일 모두에 자동으로 실행된다.

와일드카드는 Snakemake의 가장 강력한 기능 중 하나이다. 수동으로 파이프라인을 실행할 때는 각 샘플마다 명령을 반복해야 하지만, 와일드카드를 사용하면 rule 하나로 모든 샘플을 처리할 수 있다. 새 샘플이 추가되더라도 rule을 수정할 필요가 없다.

### DAG (방향성 비순환 그래프)

Snakemake는 rule 간의 의존 관계를 **DAG(Directed Acyclic Graph)**로 자동 구성한다. 한 rule의 출력 파일이 다른 rule의 입력 파일이면 자동으로 순서가 결정된다. 예를 들어 trim rule의 출력이 align rule의 입력이면, Snakemake는 반드시 trim을 먼저 실행한다.

DAG의 "비순환(Acyclic)"은 의존 관계가 원형으로 돌지 않는다는 뜻이다. A → B → C → A처럼 순환하면 무한 루프에 빠지므로, Snakemake는 이를 자동으로 감지하여 에러를 발생시킨다.

![Snakemake DAG 시각화 예시](../assets/ch6-01-snakemake-dag.png)

## 6.4 Snakefile 작성

### 기본 구조

Snakefile은 프로젝트 루트에 `Snakefile`이라는 이름으로 작성한다. `rule all`은 최종적으로 원하는 결과 파일을 지정하는 특수 rule이다. Snakemake는 이 rule의 input에 명시된 파일들을 생성하기 위해 필요한 모든 중간 단계를 자동으로 역추적하여 실행한다.

```python
# 샘플 목록 정의
SAMPLES = ["sample_A", "sample_B", "sample_C"]

# 최종 목표 지정
rule all:
    input:
        expand("results/counts/{sample}.counts.txt", sample=SAMPLES)

# 1단계: 품질 확인
rule fastqc:
    input:
        "data/{sample}.fastq.gz"
    output:
        "results/fastqc/{sample}_fastqc.html"
    shell:
        "fastqc {input} -o results/fastqc/"

# 2단계: 트리밍
rule trim:
    input:
        "data/{sample}.fastq.gz"
    output:
        "results/trimmed/{sample}_trimmed.fastq.gz"
    shell:
        "trim_galore {input} -o results/trimmed/"

# 3단계: 정렬
rule align:
    input:
        fastq="results/trimmed/{sample}_trimmed.fastq.gz",
        index="ref/genome_index"
    output:
        "results/aligned/{sample}.bam"
    threads: 4
    shell:
        "STAR --runThreadN {threads} "
        "--genomeDir {input.index} "
        "--readFilesIn {input.fastq} "
        "--outSAMtype BAM SortedByCoordinate "
        "--outFileNamePrefix results/aligned/{wildcards.sample}"

# 4단계: 발현량 정량
rule count:
    input:
        bam="results/aligned/{sample}.bam",
        gtf="ref/genes.gtf"
    output:
        "results/counts/{sample}.counts.txt"
    shell:
        "featureCounts -a {input.gtf} -o {output} {input.bam}"
```

`expand()` 함수는 와일드카드를 구체적인 값으로 확장한다. `expand("results/counts/{sample}.counts.txt", sample=SAMPLES)`는 `["results/counts/sample_A.counts.txt", "results/counts/sample_B.counts.txt", "results/counts/sample_C.counts.txt"]`로 펼쳐진다. `rule all`에서 이 세 파일을 요구하면, Snakemake는 각 파일을 만들기 위해 count → align → trim 순서로 역추적하여 전체 파이프라인을 실행한다.

`threads: 4`는 해당 rule이 CPU 4개를 사용한다는 선언이다. Snakemake는 전체 가용 코어 수(`--cores`)를 고려하여 동시에 실행할 수 있는 rule 수를 자동으로 조절한다.

### 실행

```bash
# 드라이런 (실제 실행 없이 계획 확인)
snakemake -n

# 실행 (4개 작업 병렬)
snakemake --cores 4

# DAG 시각화
snakemake --dag | dot -Tpng > dag.png
```

드라이런(`-n`)은 실제로 명령을 실행하지 않고, 어떤 rule이 어떤 순서로 실행될지 보여준다. 복잡한 파이프라인을 실행하기 전에 반드시 드라이런으로 계획을 확인하는 습관을 들이는 것이 좋다.

![snakemake -n 드라이런 결과](../assets/ch6-02-snakemake-dryrun.png)

## 6.5 주요 기능

### Conda 환경 통합

생명정보학 파이프라인에서는 서로 다른 도구가 서로 다른 의존성을 요구하는 경우가 많다. STAR는 C++ 라이브러리가 필요하고, DESeq2는 R과 Bioconductor가 필요하다. 이 두 환경을 하나의 Conda 환경에 넣으면 충돌이 발생할 수 있다.

Snakemake는 각 rule에 독립적인 Conda 환경을 지정할 수 있다. `--use-conda` 플래그를 붙여 실행하면, Snakemake가 각 rule에 지정된 YAML 파일을 읽어 자동으로 Conda 환경을 생성하고 활성화한다.

```python
rule deseq2:
    input:
        counts=expand("results/counts/{sample}.counts.txt", sample=SAMPLES)
    output:
        "results/deseq2/results.csv"
    conda:
        "envs/deseq2.yaml"
    script:
        "scripts/deseq2.R"
```

```yaml
# envs/deseq2.yaml
name: deseq2
channels:
  - conda-forge
  - bioconda
dependencies:
  - r-base=4.3
  - bioconductor-deseq2
```

이 구조의 장점은 환경 정의가 코드와 함께 버전 관리된다는 점이다. 1년 후에 파이프라인을 다시 실행해도 동일한 버전의 R과 DESeq2가 설치된다.

### Config 파일

분석 파라미터를 Snakefile과 분리하여 관리할 수 있다. Snakefile은 "어떻게 분석할 것인가"를, config 파일은 "무엇을 분석할 것인가"를 담당한다. 새로운 프로젝트에서 같은 파이프라인을 사용하고 싶다면, Snakefile은 그대로 두고 config.yaml만 수정하면 된다.

```yaml
# config.yaml
samples:
  - sample_A
  - sample_B
  - sample_C

reference:
  genome: "ref/genome.fa"
  gtf: "ref/genes.gtf"

params:
  threads: 4
  min_quality: 20
```

```python
# Snakefile에서 config 사용
configfile: "config.yaml"

SAMPLES = config["samples"]

rule trim:
    input:
        "data/{sample}.fastq.gz"
    output:
        "results/trimmed/{sample}_trimmed.fastq.gz"
    params:
        quality=config["params"]["min_quality"]
    shell:
        "trim_galore -q {params.quality} {input} -o results/trimmed/"
```

### 로그 파일

각 rule의 실행 로그를 별도로 저장하면 에러 발생 시 원인을 파악하기 훨씬 쉽다. 50개 샘플을 처리하다가 sample_37에서 에러가 났다면, `logs/align/sample_37.log`만 확인하면 된다.

```python
rule align:
    input:
        "results/trimmed/{sample}_trimmed.fastq.gz"
    output:
        "results/aligned/{sample}.bam"
    log:
        "logs/align/{sample}.log"
    shell:
        "STAR --readFilesIn {input} > {log} 2>&1"
```

`2>&1`은 표준 에러(stderr)를 표준 출력(stdout)으로 리다이렉트하여 모든 출력을 로그 파일에 기록하겠다는 의미이다.

## 6.6 바이브 코딩으로 Snakefile 작성하기

Snakemake의 문법을 완벽하게 익히는 것보다, **분석 파이프라인의 흐름을 이해하고 AI에게 정확히 설명하는 것**이 더 중요하다. Claude Code에게 Snakefile을 요청할 때, 핵심은 각 분석 단계의 순서와 도구의 역할을 아는 것이다.

### AI에게 요청하는 예시

> "RNA-seq 파이프라인을 Snakemake로 만들어줘. 입력은 data/ 폴더의 FASTQ 파일이고, FastQC → Trim Galore → STAR 정렬 → featureCounts 순서로 처리해줘. 샘플 목록은 config.yaml에서 읽어오게 하고, 각 단계마다 로그 파일을 남겨줘"

이 요청을 하려면 다음을 알아야 한다:
- **분석 단계의 순서**: 왜 트리밍 다음에 정렬을 하는지 (어댑터가 남아 있으면 정렬 정확도가 떨어진다)
- **각 도구의 역할**: FastQC는 품질 확인, Trim Galore는 어댑터 제거, STAR는 게놈 정렬, featureCounts는 유전자별 발현량 계산
- **입출력 파일 형식**: FASTQ(시퀀싱 원본) → trimmed FASTQ → BAM(정렬 결과) → counts(발현량 테이블)

반면, `expand()` 함수의 정확한 문법이나 `threads` 지시자의 사용법은 몰라도 된다. AI가 올바른 Snakemake 문법으로 작성해 준다.

### Snakefile 수정 요청 예시

> "align rule에서 STAR 대신 HISAT2를 사용하도록 바꿔줘"

> "count rule 다음에 DESeq2로 차등 발현 분석하는 rule 추가해줘. R 스크립트는 별도 파일로 분리하고, Conda 환경도 만들어줘"

> "sample_D가 추가됐으니까 config.yaml에 반영해줘"

### 디버깅 요청 예시

> "snakemake 실행하면 align 단계에서 에러가 나. logs/align/sample_A.log 보고 원인 알려줘"

> "드라이런 결과를 보여주고, DAG 그래프도 생성해줘"

에러 로그를 AI에게 보여주면서 "이 에러의 원인이 뭐야?"라고 물어보는 것만으로도 대부분의 문제를 해결할 수 있다. 단, 로그 파일을 저장하는 설정이 되어 있어야 하므로, 처음 Snakefile을 만들 때 반드시 log 지시자를 포함하도록 요청하는 것이 좋다.

## 6.7 단일세포 분석 파이프라인

5장에서 배운 Scanpy 분석도 Snakemake로 자동화할 수 있다. 단일세포 분석은 QC → 정규화 → 클러스터링이라는 순서를 따르므로, 각 단계를 rule로 분리하면 된다.

```python
SAMPLES = ["pbmc_10k", "pbmc_5k"]

rule all:
    input:
        expand("results/{sample}/umap.png", sample=SAMPLES)

rule qc:
    input:
        "data/{sample}.h5ad"
    output:
        "results/{sample}/qc_filtered.h5ad"
    script:
        "scripts/01_qc.py"

rule normalize:
    input:
        "results/{sample}/qc_filtered.h5ad"
    output:
        "results/{sample}/normalized.h5ad"
    script:
        "scripts/02_normalize.py"

rule cluster:
    input:
        "results/{sample}/normalized.h5ad"
    output:
        "results/{sample}/clustered.h5ad",
        "results/{sample}/umap.png"
    script:
        "scripts/03_cluster.py"
```

여기서 `shell` 대신 `script`를 사용하면 Python이나 R 스크립트를 직접 실행할 수 있다. 스크립트 내에서 `snakemake.input[0]`, `snakemake.output[0]`으로 입출력 파일 경로를 참조할 수 있어, 스크립트를 Snakemake와 자연스럽게 연동할 수 있다.

> **팁**: AI에게 "이 Scanpy 분석을 Snakemake 파이프라인으로 변환해줘"라고 요청하면, 기존 분석 코드를 자동으로 rule 단위로 분리하고 Snakefile을 생성해 준다. 주피터 노트북에서 프로토타이핑한 분석을 재현 가능한 파이프라인으로 전환할 때 유용하다.

## 6.8 정리

- **Snakemake**: 생명정보학 분석 파이프라인을 자동화하는 워크플로우 관리 도구
  - 재현성, 자동 의존성 관리, 병렬 실행, 부분 재실행 지원
- **Rule**: 입력 → 처리 → 출력을 정의하는 기본 단위
- **Wildcard**: `{sample}` 같은 패턴으로 여러 샘플에 동일 규칙 적용
- **DAG**: rule 간 의존 관계를 자동으로 파악하여 실행 순서 결정
- **Conda 환경 통합**: 각 rule마다 독립적인 환경을 지정하여 의존성 충돌 방지
- **Config 파일**: 분석 파라미터를 Snakefile과 분리하여 재사용성 확보
- **바이브 코딩의 핵심**: 분석 파이프라인의 **흐름과 각 도구의 역할**을 이해하고, AI에게 단계별로 지시하는 것. `expand()` 문법보다 "왜 트리밍 후에 정렬하는지"를 아는 것이 더 중요하다
