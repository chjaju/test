<div align="center">

<img src="https://img.shields.io/badge/ATIO-3776AB?style=for-the-badge&logo=python&logoColor=white" width="200" />

<b>안전한 원자적 파일 쓰기와 데이터베이스 쓰기를 위한 파이썬 라이브러리</b><br>
<b>🚀 `pip install atio`</b>

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
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

## 📚 빠른 시작

### 기본 파일 쓰기

```python
import atio
import pandas as pd

# DataFrame 생성
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Seoul", "Busan", "Incheon"]
})

# 안전한 파일 저장
atio.write(df, "users.parquet", format="parquet")
atio.write(df, "users.csv", format="csv", index=False)
```

### 데이터베이스 쓰기

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
atio.write(df, format="sql", name="products", con=engine)
```

### 버전 관리가 있는 스냅샷 쓰기

```python
# 테이블 형태로 버전 관리하며 저장
atio.write_snapshot(df, "my_table", mode="overwrite", format="parquet")

# 기존 데이터에 추가 (append 모드)
new_data = pd.DataFrame({"name": ["David"], "age": [40], "city": ["Daejeon"]})
atio.write_snapshot(new_data, "my_table", mode="append", format="parquet")
```

### 테이블 데이터 읽기

```python
# 최신 버전 읽기
latest_data = atio.read_table("my_table", output_as="pandas")

# 특정 버전 읽기
version_1_data = atio.read_table("my_table", version=1, output_as="pandas")

# Polars 형식으로 읽기
polars_data = atio.read_table("my_table", output_as="polars")
```

### 오래된 데이터 정리

```python
from datetime import timedelta

# 7일 이상 된 데이터 정리 (dry-run 모드)
atio.expire_snapshots("my_table", keep_for=timedelta(days=7), dry_run=True)

# 실제 삭제 실행
atio.expire_snapshots("my_table", keep_for=timedelta(days=7), dry_run=False)
```

### 진행률 표시

```python
# 대용량 데이터 처리 시 진행률 표시
atio.write(large_df, "big_data.parquet", format="parquet", show_progress=True)
```

## 🔧 API 참조

### `atio.write()`

데이터 객체를 안전하게 파일 또는 데이터베이스에 저장합니다.

```python
atio.write(obj, target_path=None, format=None, show_progress=False, verbose=False, **kwargs)
```

### `atio.write_snapshot()`

버전 관리가 있는 테이블 형태로 데이터를 저장합니다.

```python
atio.write_snapshot(obj, table_path, mode='overwrite', format='parquet', **kwargs)
```

### `atio.read_table()`

테이블에서 데이터를 읽어옵니다.

```python
atio.read_table(table_path, version=None, output_as='pandas')
```

### `atio.expire_snapshots()`

오래된 스냅샷과 고아 파일을 정리합니다.

```python
atio.expire_snapshots(table_path, keep_for=timedelta(days=7), dry_run=True)
```

#### 매개변수

- **obj**: 저장할 데이터 객체 (pandas.DataFrame, polars.DataFrame, numpy.ndarray 등)
- **target_path** (str, optional): 파일 저장 경로 (파일 기반 쓰기 시 필수)
- **table_path** (str): 테이블 저장 경로 (스냅샷 쓰기 시 필수)
- **format** (str, optional): 저장 형식 ('csv', 'parquet', 'excel', 'json', 'sql', 'database' 등)
- **mode** (str): 스냅샷 모드 ('overwrite', 'append')
- **version** (int, optional): 읽을 버전 번호 (None이면 최신 버전)
- **output_as** (str): 출력 형식 ('pandas', 'polars')
- **keep_for** (timedelta): 보관 기간
- **dry_run** (bool): 실제 삭제 여부 (True면 미리보기만)
- **show_progress** (bool): 진행률 표시 여부 (기본값: False)
- **verbose** (bool): 상세한 성능 진단 정보 출력 여부 (기본값: False)
- **kwargs**: 각 쓰기 함수에 전달될 추가 키워드 인자

#### 지원 형식

| 형식 | 설명 | 필수 인자 |
|------|------|-----------|
| `csv` | CSV 파일 | `target_path` |
| `parquet` | Parquet 파일 | `target_path` |
| `excel` | Excel 파일 | `target_path` |
| `json` | JSON 파일 | `target_path` |
| `sql` | SQL 데이터베이스 | `name` (테이블명), `con` (DB 커넥션) |
| `database` | 데이터베이스 (Polars) | `table_name`, `connection_uri` |

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

## 🤝 기여하기

1. 이 저장소를 포크합니다
2. 기능 브랜치를 생성합니다 (`git checkout -b feature/amazing-feature`)
3. 변경사항을 커밋합니다 (`git commit -m 'Add amazing feature'`)
4. 브랜치에 푸시합니다 (`git push origin feature/amazing-feature`)
5. Pull Request를 생성합니다

## 📄 라이선스

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## 🐛 버그 리포트

버그를 발견하셨나요? [Issues](https://github.com/seojaeohcode/atio/issues) 페이지에서 리포트해 주세요.

## 📞 지원

질문이나 제안사항이 있으시면 언제든지 연락해 주세요:

- GitHub Issues: [https://github.com/seojaeohcode/atio/issues](https://github.com/seojaeohcode/atio/issues)

---

<div align="center">

**Atio** - 안전하고 빠른 데이터 쓰기 라이브러리 🚀

</div> 
