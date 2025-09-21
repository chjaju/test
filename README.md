<div align="center">

<img width="250" alt="atio-logo" src="https://github.com/user-attachments/assets/e34f2740-0182-4e34-b56c-ff6eb3e9fce4">

# Atio

**🛡️ Safe Atomic File Writing Library for Python**

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)](LICENSE)
[![PyPI](https://img.shields.io/badge/PyPI-2.1.0-orange.svg)](https://pypi.org/project/atio/)
[![Documentation](https://img.shields.io/badge/Documentation-Read%20the%20Docs-blue.svg)](https://seojaeohcode.github.io/atio/)
[![Discord](https://img.shields.io/badge/Discord-Community-5865F2?logo=discord&logoColor=white)](https://discord.gg/EVxgByVh)

![Pandas](https://img.shields.io/badge/pandas-2.0+-green.svg?style=for-the-badge&logo=pandas&logoColor=white) ![Polars](https://img.shields.io/badge/polars-1.0+-orange.svg?style=for-the-badge&logo=polars&logoColor=white) ![NumPy](https://img.shields.io/badge/numpy-1.20+-red.svg?style=for-the-badge&logo=numpy&logoColor=white) ![PyArrow](https://img.shields.io/badge/pyarrow-17.0+-purple.svg?style=for-the-badge&logo=apache-arrow&logoColor=white) ![SQLAlchemy](https://img.shields.io/badge/sqlalchemy-2.0+-blue.svg?style=for-the-badge&logo=sqlalchemy&logoColor=white) ![OpenPyXL](https://img.shields.io/badge/openpyxl-3.1+-green.svg?style=for-the-badge&logo=openpyxl&logoColor=white)

</div>

---

## 📋 Table of Contents

- [🎯 Overview](#-overview)
- [🚀 30-Second Quick Start](#-30-second-quick-start)
- [📊 Supported Formats & Libraries](#-supported-formats--libraries)
- [🏗️ Architecture](#️-architecture)
- [⚡ Performance Comparison](#-performance-comparison)
- [💡 Real-World Use Cases](#-real-world-use-cases)
- [🎯 Core Features](#-core-features)
- [🔧 Advanced Usage](#-advanced-usage)
- [🛠️ Installation](#️-installation)
- [📚 Documentation & Examples](#-documentation--examples)
- [🏆 Why Choose Atio?](#-why-choose-atio)
- [📄 License](#-license)

---

## 🎯 Overview

**Atio** is a Python library that prevents data loss and ensures safe file writing. Through atomic writing, it protects existing data even when errors occur during file writing, and supports various data formats and database connections.

### ✨ Why Atio?

- 🔒 **Zero Data Loss**: Atomic operations guarantee file integrity
- ⚡ **High Performance**: Minimal overhead with maximum safety
- 🔄 **Auto Rollback**: Automatic recovery when errors occur
- 📊 **Universal Support**: Works with Pandas, Polars, NumPy, and more
- 🎯 **Simple API**: Drop-in replacement for existing code

## 🚀 30-Second Quick Start

```bash
pip install atio
```

```python
import atio
import pandas as pd

# Create sample data
df = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie"],
    "age": [25, 30, 35],
    "city": ["Seoul", "Busan", "Incheon"]
})

# Safe atomic writing
atio.write(df, "users.parquet", format="parquet")
# ✅ File saved safely with atomic operation!
```

## 📊 Supported Formats & Libraries

| Format | Pandas | Polars | NumPy | Description |
|--------|--------|--------|-------|-------------|
| **CSV** | ✅ | ✅ | ✅ | Comma-separated values |
| **Parquet** | ✅ | ✅ | ❌ | Columnar storage format |
| **Excel** | ✅ | ✅ | ❌ | Microsoft Excel files |
| **JSON** | ✅ | ✅ | ❌ | JavaScript Object Notation |
| **SQL** | ✅ | ❌ | ❌ | SQL database storage |
| **Database** | ❌ | ✅ | ❌ | Direct database connection |
| **NPY/NPZ** | ❌ | ❌ | ✅ | NumPy binary formats |
| **Pickle** | ✅ | ❌ | ❌ | Python serialization |
| **HTML** | ✅ | ❌ | ❌ | HTML table format |

## 🏗️ Architecture

### Atomic Writing Process

```mermaid
graph LR
    A[Data Object] --> B[Temp File]
    B --> C[Validation]
    C --> D[Atomic Replace]
    D --> E[Success Flag]
    
    C -->|Error| F[Rollback]
    F --> G[Original File Preserved]
    
    style A fill:#e1f5fe
    style E fill:#c8e6c9
    style F fill:#ffcdd2
    style G fill:#c8e6c9
```

### Key Components

- **🛡️ Atomic Operations**: Temporary file → Validation → Atomic replacement
- **🔄 Rollback Mechanism**: Automatic recovery on failure
- **📈 Progress Monitoring**: Real-time progress for large files
- **📋 Version Management**: Snapshot-based data versioning
- **🧹 Auto Cleanup**: Automatic cleanup of temporary files

## ⚡ Performance Comparison

| Method | 100K Rows | 1M Rows | Safety | Version Control | Memory Usage |
|--------|-----------|---------|--------|----------------|--------------|
| **Atio write()** | 0.58s | 6.01s | ✅ | ❌ | Low |
| **Atio Snapshot** | 1.16s | 12.68s | ✅ | ✅ | Medium |
| Pandas | 0.57s | 5.71s | ❌ | ❌ | Low |
| Polars | 0.01s | 0.12s | ❌ | ❌ | Very Low |

*Benchmark results on SSD storage with random data*

## 💡 Real-World Use Cases

### 🔥 Data Pipeline Protection
```python
# ETL pipeline with automatic rollback
try:
    atio.write(processed_data, "final_results.parquet", format="parquet")
    print("✅ Pipeline completed successfully")
except Exception as e:
    print("❌ Pipeline failed, but original data is safe")
    # Original file remains untouched
```

### 🧪 Machine Learning Experiments
```python
# Version-controlled experiment results
atio.write_snapshot(model_results, "experiment_v1", mode="overwrite")
atio.write_snapshot(improved_results, "experiment_v1", mode="append")

# Rollback to previous version if needed
atio.rollback("experiment_v1", version_id=1)
```

### 📊 Large Data Processing
```python
# Progress monitoring for large datasets
atio.write(large_df, "big_data.parquet", 
          format="parquet", 
          show_progress=True)
# Shows: ⠋ Writing big_data.parquet... [ 45.2 MB | 12.3 MB/s | 00:15 ]
```

## 🎯 Core Features

### 1. **Atomic File Writing**
```python
# Safe writing with automatic rollback
atio.write(df, "data.parquet", format="parquet")
# Creates: data.parquet + .data.parquet._SUCCESS
```

### 2. **Database Integration**
```python
# Direct database storage
from sqlalchemy import create_engine
engine = create_engine('postgresql://user:pass@localhost/db')
atio.write(df, format="sql", name="users", con=engine, if_exists="replace")
```

### 3. **Version Management**
```python
# Snapshot-based versioning
atio.write_snapshot(df, "my_table", mode="overwrite")  # v1
atio.write_snapshot(new_df, "my_table", mode="append") # v2

# Read specific version
df_v1 = atio.read_table("my_table", version=1)
```

### 4. **Progress Monitoring**
```python
# Real-time progress for large files
atio.write(large_df, "data.parquet", 
          format="parquet", 
          show_progress=True,
          verbose=True)
```

## 🔧 Advanced Usage

### Multi-Format Support
```python
import polars as pl
import numpy as np

# Polars DataFrame
pl_df = pl.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6]})
atio.write(pl_df, "data.parquet", format="parquet")

# NumPy Arrays
arr = np.random.randn(1000, 100)
atio.write(arr, "array.npy", format="npy")

# Multiple arrays
atio.write({'arr1': arr, 'arr2': arr*2}, "arrays.npz", format="npz")
```

### Error Handling & Recovery
```python
# Automatic rollback on failure
try:
    atio.write(df, "data.parquet", format="parquet")
except Exception as e:
    print(f"Write failed: {e}")
    # Original file is automatically preserved
```

### Performance Monitoring
```python
# Detailed performance analysis
atio.write(df, "data.parquet", format="parquet", verbose=True)
# Output:
# [INFO] Temporary directory created: /tmp/tmp12345
# [INFO] Writer to use: to_parquet (format: parquet)
# [INFO] ✅ File writing completed (total time: 0.1234s)
```

## 🛠️ Installation

### Basic Installation
```bash
pip install atio
```

### With Optional Dependencies
```bash
# For Excel support
pip install atio[excel]

# For database support
pip install atio[database]

# For all features
pip install atio[all]
```

### Development Installation
```bash
git clone https://github.com/seojaeohcode/atio.git
cd atio
pip install -e .
```

## 📚 Documentation & Examples

### 📖 Documentation
- **[Complete Documentation](https://seojaeohcode.github.io/atio/)** - Full API reference
- **[Quick Start Guide](https://seojaeohcode.github.io/atio/quickstart.html)** - Get started in minutes
- **[Advanced Usage](https://seojaeohcode.github.io/atio/advanced_usage.html)** - Power user features

### 🎯 Examples
- **[Basic Usage](examples/example_atomicwriter_trace.py)** - Simple file operations
- **[Progress Monitoring](examples/example_progress.py)** - Large file handling
- **[Snapshot Management](examples/example_snapshot.py)** - Version control
- **[Performance Testing](examples/test_basic_vs_verbose.py)** - Benchmarking

### 🧪 Test Scenarios
- **[Keyboard Interrupt](tests/scenarios/1_keyboard_interrupt/)** - Ctrl+C safety
- **[Out of Memory](tests/scenarios/2_oom/)** - Memory failure handling
- **[CI/CD Pipeline](tests/scenarios/3_ci_failure/)** - Automated deployment safety

## 🏆 Why Choose Atio?

### ✅ **Data Safety First**
- **Zero data loss** even during system failures
- **Automatic rollback** on any error
- **File integrity** guaranteed by atomic operations

### ⚡ **Performance Optimized**
- **Minimal overhead** (1.1-1.2x vs native libraries)
- **Progress monitoring** for large files
- **Memory efficient** processing

### 🔧 **Developer Friendly**
- **Drop-in replacement** for existing code
- **Simple API** with powerful features
- **Comprehensive documentation** and examples

### 🌐 **Universal Compatibility**
- **Multiple data formats** (CSV, Parquet, Excel, JSON, etc.)
- **Multiple libraries** (Pandas, Polars, NumPy)
- **Database integration** (SQL, NoSQL)

## 📄 License

This project is distributed under the **Apache 2.0 License**. See the [LICENSE](LICENSE) file for details.

---

<div align="center">

**🛡️ Atio** - Because your data deserves to be safe

[![GitHub stars](https://img.shields.io/github/stars/seojaeohcode/atio?style=social)](https://github.com/seojaeohcode/atio)
[![GitHub forks](https://img.shields.io/github/forks/seojaeohcode/atio?style=social)](https://github.com/seojaeohcode/atio)
[![GitHub watchers](https://img.shields.io/github/watchers/seojaeohcode/atio?style=social)](https://github.com/seojaeohcode/atio)

</div>
