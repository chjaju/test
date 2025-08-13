<div align="center">

<img src="https://img.shields.io/badge/ATIO-3776AB?style=for-the-badge&logo=python&logoColor=white" width="200" />

<b>안전한 원자적 파일 쓰기와 데이터베이스 쓰기를 위한 파이썬 라이브러리</b><br>
<b>🚀 `pip install atio`</b>

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)](LICENSE)
[![PyPI](https://img.shields.io/badge/PyPI-2.0.0-orange.svg)](https://pypi.org/project/atio/)

</div>

---

## 📖 개요

Atio는 데이터 손실을 방지하고 안전한 파일 쓰기를 보장하는 파이썬 라이브러리입니다. 원자적 쓰기(atomic write)를 통해 파일 쓰기 중 오류가 발생해도 기존 데이터를 보호하며, 다양한 데이터 형식과 데이터베이스 연결을 지원합니다.

### ✨ 주요 기능

- 🔒 **원자적 파일 쓰기**: 임시 파일을 사용한 안전한 쓰기
- 📊 **다양한 형식 지원**: CSV, Parquet, Excel, JSON 등
- 🗄️ **데이터베이스 지원**: SQL, Database 직접 쓰기
- 📈 **진행률 표시**: 대용량 데이터 처리 시 진행 상황 모니터링
- 🔄 **롤백 기능**: 오류 발생 시 자동 복구
- 🎯 **간단한 API**: 직관적이고 사용하기 쉬운 인터페이스
- 📋 **버전 관리**: 스냅샷 기반 데이터 버전 관리
- 🧹 **자동 정리**: 오래된 데이터 자동 삭제

## 🚀 설치

```bash
pip install atio
```

## 📚 사용법

### `atio.write()` - 기본 파일/데이터베이스 쓰기

**용도**: 단일 파일 또는 데이터베이스에 데이터 저장

**주요 매개변수**:
- `obj`: 저장할 데이터 (pandas.DataFrame, polars.DataFrame, numpy.ndarray)
- `target_path`: 파일 저장 경로 (파일 쓰기 시 필수)
- `format`: 저장 형식 ('csv', 'parquet', 'excel', 'json', 'sql', 'database')
- `show_progress`: 진행률 표시 여부
- `verbose`: 상세한 성능 정보 출력 여부

#### 기본 파일 쓰기

```python
import atio
import pandas as pd

df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Seoul", "Busan", "Incheon"]
})

# 다양한 형식으로 저장
atio.write(df, "users.parquet", format="parquet")
atio.write(df, "users.csv", format="csv", index=False)
atio.write(df, "users.xlsx", format="excel", sheet_name="Users")
```

#### 데이터베이스 쓰기

```python
import atio
import pandas as pd
from sqlalchemy import create_engine

df = pd.DataFrame({
    "product_id": [101, 102, 103],
    "product_name": ["Laptop", "Mouse", "Keyboard"],
    "price": [1200, 25, 75]
})

# SQL 데이터베이스에 저장
engine = create_engine('postgresql://user:password@localhost/dbname')
atio.write(df, format="sql", name="products", con=engine, if_exists="replace")
```

#### 고급 기능 (진행률, 성능 모니터링)

```python
# 진행률 표시와 함께 저장
atio.write(large_df, "big_data.parquet", format="parquet", show_progress=True)

# 상세한 성능 정보 출력
atio.write(df, "data.parquet", format="parquet", verbose=True)

# Polars DataFrame 사용
import polars as pl
polars_df = pl.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6]})
atio.write(polars_df, "data.parquet", format="parquet")
```

### `atio.write_snapshot()` - 버전 관리가 있는 테이블 저장

**용도**: 버전 관리가 있는 테이블 형태 저장

**주요 매개변수**:
- `obj`: 저장할 데이터
- `table_path`: 테이블 저장 경로
- `mode`: 저장 모드 ('overwrite', 'append')
- `format`: 저장 형식

#### 버전 관리 사용법

```python
# 테이블 형태로 버전 관리하며 저장
atio.write_snapshot(df, "my_table", mode="overwrite", format="parquet")

# 기존 데이터에 추가 (append 모드)
new_data = pd.DataFrame({"name": ["David"], "age": [40], "city": ["Daejeon"]})
atio.write_snapshot(new_data, "my_table", mode="append", format="parquet")
```

### `atio.read_table()` - 테이블 데이터 읽기

**용도**: 테이블에서 데이터 읽기

**주요 매개변수**:
- `table_path`: 테이블 경로
- `version`: 읽을 버전 번호 (None이면 최신)
- `output_as`: 출력 형식 ('pandas', 'polars')

#### 테이블 읽기 사용법

```python
# 최신 데이터 읽기
latest_data = atio.read_table("my_table", output_as="pandas")

# 특정 버전 읽기
version_1_data = atio.read_table("my_table", version=1, output_as="pandas")

# Polars 형식으로 읽기
polars_data = atio.read_table("my_table", output_as="polars")
```

### `atio.expire_snapshots()` - 오래된 데이터 정리

**용도**: 오래된 스냅샷과 고아 파일 정리

**주요 매개변수**:
- `table_path`: 테이블 경로
- `keep_for`: 보관 기간
- `dry_run`: 실제 삭제 여부 (True면 미리보기만)

#### 데이터 정리 사용법

```python
from datetime import timedelta

# 오래된 데이터 정리 (미리보기)
atio.expire_snapshots("my_table", keep_for=timedelta(days=7), dry_run=True)

# 실제 삭제 실행
atio.expire_snapshots("my_table", keep_for=timedelta(days=7), dry_run=False)
```

## 📊 지원 형식

| 형식 | 설명 | 필수 인자 | 예시 |
|------|------|-----------|------|
| `csv` | CSV 파일 | `target_path` | `atio.write(df, "data.csv", format="csv")` |
| `parquet` | Parquet 파일 | `target_path` | `atio.write(df, "data.parquet", format="parquet")` |
| `excel` | Excel 파일 | `target_path` | `atio.write(df, "data.xlsx", format="excel")` |
| `json` | JSON 파일 | `target_path` | `atio.write(df, "data.json", format="json")` |
| `sql` | SQL 데이터베이스 | `name`, `con` | `atio.write(df, format="sql", name="table", con=engine)` |
| `database` | 데이터베이스 (Polars) | `table_name`, `connection_uri` | `atio.write(df, format="database", table_name="table", connection_uri="...")` |

## 🎯 실제 사용 시나리오

### 시나리오 1: 대용량 CSV 저장 중 작업 중단

**문제 상황**: 한 사용자가 Pandas로 대용량 분석 결과를 .csv 파일로 저장하던 중, 예상치 못한 전원 차단이나 커널 강제 종료가 발생했습니다. 결과 파일은 50MB 중 3MB만 저장된 채 손상되었고, 이후 읽기도 되지 않았습니다.

**Atio로 해결**: `atio.write()`는 임시 파일에 먼저 기록 후, 모든 쓰기가 성공해야만 원본과 교체합니다. 따라서 중간에 꺼져도 기존 파일은 보존되고, 손상된 임시 파일은 자동 정리되어 안정성을 확보할 수 있습니다.

### 시나리오 2: 멀티프로세스 환경에서 파일 충돌

**문제 상황**: Python multiprocessing 기반 데이터 수집 파이프라인에서 여러 프로세스가 동시에 같은 파일을 저장하며 충돌이 발생했습니다. 결과적으로 로그 파일이 덮어쓰여 누락되거나, 일부 JSON 파일은 파싱할 수 없는 손상된 형태로 저장됐습니다.

**Atio로 해결**: `atio.write()`의 원자적 교체 방식으로 파일 쓰기를 수행하면, 한 번에 하나의 프로세스만 최종 경로로 이동할 수 있습니다. 이로써 경쟁 조건 없이 충돌 없이 저장이 보장됩니다.

### 시나리오 3: 데이터 파이프라인 검증 불가

**문제 상황**: ETL 작업에서 .parquet 저장이 완료됐는지 여부를 자동 시스템이 판단할 수 없어, 손상되거나 미완성된 데이터를 다음 단계에서 그대로 사용했습니다. 결과적으로 모델 학습 데이터에 결측값이 포함되어 품질 저하가 발생했습니다.

**Atio로 해결**: `atio.write_snapshot()`을 사용하면 저장이 성공적으로 완료된 경우에만 `_SUCCESS` 플래그 파일을 함께 생성합니다. 후속 단계는 `_SUCCESS` 유무를 기준으로 안전하게 파이프라인을 구동할 수 있습니다.

### 시나리오 4: 데이터 버전 관리 부재

**문제 상황**: 머신러닝 모델 학습을 위한 데이터셋이 여러 번 업데이트되면서, 어떤 버전의 데이터로 어떤 모델이 학습되었는지 추적이 불가능했습니다. 실험 결과의 재현성이 떨어지고 모델 성능 비교가 어려워졌습니다.

**Atio로 해결**: `atio.write_snapshot()`과 `atio.read_table()`을 사용하면 데이터의 버전을 자동으로 관리할 수 있습니다. 각 버전별로 스냅샷이 생성되어 언제든지 특정 시점의 데이터로 돌아갈 수 있고, 실험 재현성이 보장됩니다.

### 시나리오 5: 디스크 공간 부족으로 인한 시스템 중단

**문제 상황**: 대용량 데이터 처리 중 디스크 공간이 부족해져서 시스템이 중단되었습니다. 처리 중이던 파일들이 불완전한 상태로 남아있어 디스크 공간을 계속 차지하고, 수동으로 정리해야 하는 상황이 발생했습니다.

**Atio로 해결**: `atio.expire_snapshots()`를 사용하면 설정된 보관 기간보다 오래된 스냅샷과 고아 파일을 자동으로 정리할 수 있습니다. `dry_run=True` 옵션으로 미리 삭제될 파일들을 확인한 후, 안전하게 정리 작업을 수행할 수 있습니다.

### 시나리오 6: 데이터베이스 저장 중 네트워크 오류

**문제 상황**: 분석 결과를 PostgreSQL 데이터베이스에 저장하던 중 네트워크 연결이 끊어져서 저장이 중단되었습니다. 데이터베이스에는 부분적으로만 저장된 테이블이 남아있어 데이터 무결성이 깨진 상태가 되었습니다.

**Atio로 해결**: `atio.write()`의 데이터베이스 저장 기능은 트랜잭션을 사용하여 모든 데이터가 성공적으로 저장되거나 아예 저장되지 않도록 보장합니다. 네트워크 오류 발생 시 자동으로 롤백되어 데이터 무결성을 유지합니다.

### 시나리오 7: 실험 데이터 관리의 복잡성

**문제 상황**: 연구팀에서 여러 실험을 동시에 진행하면서 실험 데이터가 서로 섞이고, 어떤 실험에 어떤 데이터가 사용되었는지 추적이 어려워졌습니다. 실험 결과의 신뢰성이 떨어지고 재현이 불가능한 상황이 발생했습니다.

**Atio로 해결**: `atio.write_snapshot()`을 사용하여 각 실험별로 독립적인 테이블을 생성하고, `atio.read_table()`로 특정 실험의 정확한 데이터를 읽어올 수 있습니다. 실험별 버전 관리와 메타데이터 추적이 자동화되어 연구의 재현성과 신뢰성이 보장됩니다.

### 시나리오 8: 클라우드 스토리밍 중 데이터 손실

**문제 상황**: IoT 센서에서 실시간으로 수집되는 데이터를 스트리밍 처리하던 중 시스템 재시작이나 네트워크 오류가 발생했습니다. 처리 중이던 데이터가 손실되어 중요한 센서 데이터의 연속성이 깨졌습니다.

**Atio로 해결**: `atio.write_snapshot()`을 사용하여 실시간 데이터를 버퍼링하고 주기적으로 원자적으로 저장합니다. 시스템 재시작 후에도 마지막 저장 시점부터 데이터 수집을 재개할 수 있어 데이터 연속성이 보장됩니다.

### 시나리오 9: 대용량 데이터 처리 중 메모리 부족

**문제 상황**: 10GB 이상의 대용량 DataFrame을 처리하다가 메모리 부족으로 프로세스가 강제 종료되었습니다. 처리 중이던 중간 결과들이 모두 손실되어 처음부터 다시 시작해야 하는 상황이 발생했습니다.

**Atio로 해결**: `atio.write()`의 `show_progress=True` 옵션과 함께 청크 단위로 데이터를 처리하여 메모리 사용량을 제어합니다. 각 청크가 성공적으로 저장되면 다음 청크를 처리하므로, 중간에 실패해도 이미 저장된 데이터는 보존됩니다.

### 시나리오 10: 백업 시스템과의 충돌

**문제 상황**: 자동 백업 시스템이 실행되는 동안 대용량 파일을 저장하려고 했는데, 백업 소프트웨어가 쓰기 중인 파일을 백업하려고 시도하여 파일이 손상되었습니다. 백업 파일도 불완전한 상태로 저장되었습니다.

**Atio로 해결**: `atio.write()`의 원자적 교체 방식으로 파일을 저장하면, 백업 시스템이 파일을 읽는 시점에는 항상 완전한 상태의 파일만 존재합니다. 임시 파일은 백업 대상에서 제외되어 충돌 없이 안전한 백업이 가능합니다.

## 📋 사용 예제

### 1. 다양한 파일 형식으로 저장

```python
import atio
import pandas as pd

df = pd.DataFrame({
    "id": range(1000),
    "value": np.random.randn(1000),
    "category": np.random.choice(["A", "B", "C"], 1000)
})

# 다양한 형식으로 저장
atio.write(df, "data.csv", format="csv", index=False)
atio.write(df, "data.parquet", format="parquet")
atio.write(df, "data.xlsx", format="excel", sheet_name="Sheet1")
atio.write(df, "data.json", format="json", orient="records")
```

### 2. 데이터베이스 연결

```python
import atio
import pandas as pd
from sqlalchemy import create_engine

# PostgreSQL 연결
engine = create_engine('postgresql://user:password@localhost/mydb')

df = pd.DataFrame({
    "user_id": [1, 2, 3],
    "username": ["john", "jane", "bob"],
    "email": ["john@example.com", "jane@example.com", "bob@example.com"]
})

# 데이터베이스에 저장
atio.write(df, format="sql", name="users", con=engine, if_exists="replace")
```

### 3. 버전 관리 워크플로우

```python
import atio
import pandas as pd
from datetime import timedelta

# 초기 데이터 저장
df1 = pd.DataFrame({"date": ["2024-01-01"], "value": [100]})
atio.write_snapshot(df1, "daily_data", mode="overwrite")

# 데이터 추가 (append 모드)
df2 = pd.DataFrame({"date": ["2024-01-02"], "value": [150]})
atio.write_snapshot(df2, "daily_data", mode="append")

# 최신 데이터 읽기
latest = atio.read_table("daily_data", output_as="pandas")
print(f"최신 데이터: {latest}")

# 특정 버전 읽기
version_1 = atio.read_table("daily_data", version=1, output_as="pandas")
print(f"버전 1: {version_1}")

# 오래된 데이터 정리 (30일 이상)
atio.expire_snapshots("daily_data", keep_for=timedelta(days=30), dry_run=True)
```

### 4. 대용량 데이터 처리

```python
import atio
import pandas as pd

# 대용량 데이터 생성
large_df = pd.DataFrame({
    "id": range(1000000),
    "value": np.random.randn(1000000)
})

# 진행률 표시와 함께 저장
atio.write(large_df, "large_data.parquet", format="parquet", show_progress=True, verbose=True)
```

## 🛡️ 안전성 보장

Atio는 다음과 같은 방식으로 데이터 안전성을 보장합니다:

1. **임시 파일 사용**: 실제 파일에 직접 쓰지 않고 임시 파일에 먼저 저장
2. **원자적 이동**: 임시 파일이 완성되면 원자적으로 최종 위치로 이동
3. **백업 생성**: 기존 파일이 있는 경우 백업 생성
4. **오류 복구**: 쓰기 실패 시 자동으로 롤백
5. **버전 관리**: 스냅샷 기반으로 데이터 이력 관리
6. **고아 파일 정리**: 더 이상 참조되지 않는 파일 자동 삭제

## 🔍 성능 모니터링

```python
# 상세한 성능 정보 출력
atio.write(df, "data.parquet", format="parquet", verbose=True)
```

출력 예시:
```
[INFO] 임시 디렉토리 생성: /tmp/tmp12345
[INFO] 임시 파일 경로: /tmp/tmp12345/data.parquet
[INFO] 사용할 writer: to_parquet (format: parquet)
[INFO] ✅ 파일 쓰기 완료 (총 소요 시간: 0.1234s)
```

## 📦 의존성

### 필수 의존성
- Python 3.7+
- pandas
- numpy

### 선택적 의존성
- `pyarrow` 또는 `fastparquet`: Parquet 형식 지원
- `openpyxl` 또는 `xlsxwriter`: Excel 형식 지원
- `sqlalchemy`: SQL 데이터베이스 지원
- `polars`: Polars DataFrame 지원

## 📄 라이선스

이 프로젝트는 Apache 2.0 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## 🐛 버그 리포트

버그를 발견하셨나요? [Issues](https://github.com/seojaeohcode/atio/issues) 페이지에서 리포트해 주세요.

## 📞 지원

질문이나 제안사항이 있으시면 언제든지 연락해 주세요:

- GitHub Issues: [https://github.com/seojaeohcode/atio/issues](https://github.com/seojaeohcode/atio/issues)

---

<div align="center">

**Atio** - 안전하고 빠른 데이터 쓰기 라이브러리 🚀

</div> 
