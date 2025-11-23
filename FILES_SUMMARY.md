# Project Files Summary

## 📁 All Files Created for Your Project

---

## 🎯 Main Analysis Files

### Python Scripts (Complete Pipeline)
1. **`01_data_cleaning_eda.py`**
   - Data cleaning and exploratory data analysis
   - Creates plots and summary statistics
   - Outputs: `data_preprocessed.parquet`, plots, statistics

2. **`02_feature_engineering.py`**
   - Time series feature engineering
   - Creates lag, rolling, EWMA features
   - Outputs: `train_2017_ts_train.parquet`, `train_2017_ts_test.parquet`

### SLURM Job Scripts (For HPC Clusters)
3. **`01_data_cleaning_eda.slurm`**
   - SLURM job for Part 1
   - Resources: 32GB RAM, 4 CPUs, 2 hours

4. **`02_feature_engineering.slurm`**
   - SLURM job for Part 2
   - Resources: 64GB RAM, 8 CPUs, 4 hours

---

## 🚀 Alternative Implementations

### MPI4py Version (For Parallel Processing)
5. **`feature_engineering_mpi.py`**
   - Parallel feature engineering using MPI4py
   - Splits work across multiple processes
   - Prevents memory crashes

6. **`run_feature_engineering.sh`**
   - Shell script to run MPI version
   - Configurable number of processes

### GCP/Cloud Version
7. **`setup_mpi_gcp.sh`**
   - Setup script for Google Cloud Platform
   - Installs OpenMPI and mpi4py

8. **`setup_mpi.sh`**
   - Setup script for Mac/local machines
   - Installs dependencies via Homebrew

---

## 📚 Documentation Files

### Main Documentation
9. **`PROJECT_SUMMARY.md`** ⭐
   - **Complete project overview**
   - Dataset description
   - All features explained
   - Model recommendations
   - Next steps for team

10. **`SLURM_INSTRUCTIONS.md`**
    - Detailed guide for HPC clusters
    - Troubleshooting tips
    - Resource optimization

11. **`README_SLURM_QUICK.md`**
    - Quick start guide for SLURM
    - Essential commands only

12. **`GCP_INSTRUCTIONS.md`**
    - Guide for Google Cloud Platform
    - VM setup and configuration

13. **`FEATURE_ENGINEERING_README.md`**
    - MPI4py implementation guide
    - Performance tips

---

## 📓 Jupyter Notebooks

14. **`data_analysis.ipynb`**
    - Original comprehensive notebook
    - All sections included
    - May crash with large dataset

15. **`data_analysis_optimized.ipynb`**
    - Memory-optimized version
    - Essential features only
    - Better for large datasets

---

## 📊 Output Files (Generated After Running)

### From Part 1 (Data Cleaning & EDA)
```
output_data/
├── summary_statistics.csv
├── correlation_matrix.csv
└── data_preprocessed.parquet

output_plots/
├── 01_unit_sales_distribution.png
├── 02_correlation_matrix.png
├── 03_temporal_trends.png
├── 04_store_type_analysis.png
├── 05_product_family_analysis.png
└── 06_promotion_impact.png
```

### From Part 2 (Feature Engineering)
```
output_data/
├── train_2017_ts_train.parquet  ← For model training
├── train_2017_ts_test.parquet   ← For model evaluation
└── feature_list.csv
```

---

## 🎯 Which Files to Use?

### For HPC Cluster (SLURM):
✅ Use:
- `01_data_cleaning_eda.py`
- `02_feature_engineering.py`
- `01_data_cleaning_eda.slurm`
- `02_feature_engineering.slurm`
- `SLURM_INSTRUCTIONS.md`

### For Local Machine (Memory Issues):
✅ Use:
- `feature_engineering_mpi.py`
- `run_feature_engineering.sh`
- `setup_mpi.sh` (Mac) or `setup_mpi_gcp.sh` (Linux)
- `FEATURE_ENGINEERING_README.md`

### For Google Cloud Platform:
✅ Use:
- `feature_engineering_mpi.py`
- `setup_mpi_gcp.sh`
- `GCP_INSTRUCTIONS.md`

### For Jupyter Notebook:
✅ Use:
- `data_analysis_optimized.ipynb` (recommended)
- Or `data_analysis.ipynb` (if you have enough RAM)

### For Understanding the Project:
✅ Read:
- `PROJECT_SUMMARY.md` ⭐ (Start here!)
- `FILES_SUMMARY.md` (This file)

---

## 📋 File Dependencies

### Execution Order:

**Option 1: SLURM (HPC)**
```
1. 01_data_cleaning_eda.slurm
   ↓
2. 02_feature_engineering.slurm
```

**Option 2: MPI4py (Local/GCP)**
```
1. setup_mpi.sh (or setup_mpi_gcp.sh)
   ↓
2. run_feature_engineering.sh
```

**Option 3: Jupyter**
```
1. data_analysis_optimized.ipynb
   (Run all cells)
```

---

## 🔍 File Sizes

| File | Size | Type |
|------|------|------|
| `01_data_cleaning_eda.py` | ~18 KB | Python script |
| `02_feature_engineering.py` | ~22 KB | Python script |
| `01_data_cleaning_eda.slurm` | ~4 KB | SLURM script |
| `02_feature_engineering.slurm` | ~5 KB | SLURM script |
| `feature_engineering_mpi.py` | ~12 KB | Python script |
| `PROJECT_SUMMARY.md` | ~45 KB | Documentation |
| `SLURM_INSTRUCTIONS.md` | ~25 KB | Documentation |
| `data_analysis.ipynb` | ~80 KB | Jupyter notebook |
| `train_2017_clean.parquet` | ~273 MB | Input data |
| `train_2017_ts_train.parquet` | ~250 MB | Output (generated) |
| `train_2017_ts_test.parquet` | ~15 MB | Output (generated) |

---

## 🎓 Learning Path

### If you're new to this project:

1. **Start here**: Read `PROJECT_SUMMARY.md`
   - Understand the problem
   - See what features were created
   - Learn about next steps

2. **Choose your platform**:
   - HPC Cluster? → Read `SLURM_INSTRUCTIONS.md`
   - Local machine? → Read `FEATURE_ENGINEERING_README.md`
   - Google Cloud? → Read `GCP_INSTRUCTIONS.md`

3. **Run the analysis**:
   - Follow the quick start guide
   - Monitor progress
   - Download results

4. **Next phase**: Model building
   - Use the processed datasets
   - Implement MPI4py for parallel training
   - See `PROJECT_SUMMARY.md` Section 7

---

## 🗂️ File Organization

```
AMS598_project/
│
├── 📊 Input Data
│   └── train_2017_clean.parquet
│
├── 🐍 Python Scripts
│   ├── 01_data_cleaning_eda.py
│   ├── 02_feature_engineering.py
│   └── feature_engineering_mpi.py
│
├── 💻 SLURM Scripts
│   ├── 01_data_cleaning_eda.slurm
│   └── 02_feature_engineering.slurm
│
├── 🔧 Setup Scripts
│   ├── run_feature_engineering.sh
│   ├── setup_mpi.sh
│   └── setup_mpi_gcp.sh
│
├── 📓 Notebooks
│   ├── data_analysis.ipynb
│   └── data_analysis_optimized.ipynb
│
├── 📚 Documentation
│   ├── PROJECT_SUMMARY.md ⭐
│   ├── SLURM_INSTRUCTIONS.md
│   ├── README_SLURM_QUICK.md
│   ├── GCP_INSTRUCTIONS.md
│   ├── FEATURE_ENGINEERING_README.md
│   └── FILES_SUMMARY.md (this file)
│
└── 📁 Output (generated)
    ├── output_data/
    │   ├── train_2017_ts_train.parquet
    │   ├── train_2017_ts_test.parquet
    │   └── *.csv
    ├── output_plots/
    │   └── *.png
    └── logs/
        └── *.out, *.err
```

---

## ✅ Checklist: What You Need

### To Run on HPC Cluster:
- [x] `01_data_cleaning_eda.py`
- [x] `02_feature_engineering.py`
- [x] `01_data_cleaning_eda.slurm`
- [x] `02_feature_engineering.slurm`
- [x] `train_2017_clean.parquet`
- [x] Access to HPC cluster with SLURM

### To Run Locally with MPI:
- [x] `feature_engineering_mpi.py`
- [x] `run_feature_engineering.sh`
- [x] `setup_mpi.sh` (Mac) or `setup_mpi_gcp.sh` (Linux)
- [x] `train_2017_clean.parquet`
- [x] At least 32GB RAM

### To Run in Jupyter:
- [x] `data_analysis_optimized.ipynb`
- [x] `train_2017_clean.parquet`
- [x] Jupyter Lab/Notebook installed
- [x] At least 16GB RAM

---

## 🎯 Quick Decision Guide

**I have access to HPC cluster with SLURM:**
→ Use Python scripts + SLURM jobs

**I'm on my local machine (Mac/Linux):**
→ Use MPI4py version

**I'm on Google Cloud Platform:**
→ Use GCP setup + MPI4py

**I want to use Jupyter:**
→ Use optimized notebook

**I just want to understand the project:**
→ Read PROJECT_SUMMARY.md

---

## 📞 Support

- **For SLURM issues**: Check `SLURM_INSTRUCTIONS.md` troubleshooting section
- **For MPI4py issues**: Check `FEATURE_ENGINEERING_README.md`
- **For GCP issues**: Check `GCP_INSTRUCTIONS.md`
- **For project questions**: Read `PROJECT_SUMMARY.md`

---

## 🎉 Summary

You have **15 files** ready to use:
- ✅ 3 Python scripts (different implementations)
- ✅ 2 SLURM job scripts
- ✅ 3 Setup scripts
- ✅ 2 Jupyter notebooks
- ✅ 5 Documentation files

**Everything is ready!** Choose your platform and start processing. 🚀

---

**Last Updated**: November 22, 2025
