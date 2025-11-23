# Quick Start Guide - SLURM Jobs

## 🚀 Run Complete Data Analysis on HPC Cluster

### Files You Have:
- ✅ `01_data_cleaning_eda.py` - Python script for Part 1
- ✅ `02_feature_engineering.py` - Python script for Part 2  
- ✅ `01_data_cleaning_eda.slurm` - SLURM job for Part 1
- ✅ `02_feature_engineering.slurm` - SLURM job for Part 2

---

## 📝 Before You Start

### 1. Edit SLURM Scripts

Open each `.slurm` file and update:

```bash
#SBATCH --partition=standard        # ← Change to your partition
#SBATCH --mail-user=your@email.com  # ← Change to your email
```

### 2. Upload to Cluster

```bash
scp *.py *.slurm train_2017_clean.parquet username@cluster.edu:~/project/
```

---

## ▶️ Run Jobs

### On the Cluster:

```bash
# 1. Create directories
mkdir -p logs output_data output_plots

# 2. Submit Part 1
sbatch 01_data_cleaning_eda.slurm

# 3. Check status
squeue -u $USER

# 4. When Part 1 completes, submit Part 2
sbatch 02_feature_engineering.slurm

# 5. Monitor progress
tail -f logs/02_feature_engineering_*.out
```

---

## ⏱️ Expected Time

- **Part 1**: 30-60 minutes
- **Part 2**: 1-2 hours
- **Total**: ~2-3 hours

---

## 📤 Download Results

```bash
# Download processed datasets
scp username@cluster.edu:~/project/output_data/train_2017_ts_*.parquet .
```

---

## 🎯 What You Get

### Output Files:
1. `train_2017_ts_train.parquet` - Training data (~23.3M rows, ~70 features)
2. `train_2017_ts_test.parquet` - Test data (~500K rows, ~70 features)
3. Plots and statistics in `output_plots/` and `output_data/`

### Features Created:
- ✅ Temporal features (year, month, day_of_week, etc.)
- ✅ Lag features (1, 7, 14, 28 days)
- ✅ Rolling statistics (mean, std, min, max)
- ✅ EWMA features
- ✅ Difference features
- ✅ Store-item aggregations
- ✅ Interaction features
- ✅ And more! (~70 total features)

---

## 🆘 Troubleshooting

### Job Failed?

```bash
# Check error log
cat logs/02_feature_engineering_JOBID.err

# Common fixes:
# - Out of memory? Increase --mem=96GB
# - Timeout? Increase --time=06:00:00
# - Module error? Check module load commands
```

### Check Job Status

```bash
squeue -u $USER              # Current jobs
sacct -j JOBID               # Job history
seff JOBID                   # Job efficiency
```

---

## 📚 Full Documentation

For detailed instructions, see:
- `SLURM_INSTRUCTIONS.md` - Complete guide
- `PROJECT_SUMMARY.md` - Project overview

---

## ✅ Quick Checklist

- [ ] Edited SLURM scripts (partition, email)
- [ ] Uploaded files to cluster
- [ ] Created output directories
- [ ] Submitted Part 1 job
- [ ] Waited for Part 1 to complete
- [ ] Submitted Part 2 job
- [ ] Downloaded results

---

**That's it!** Your data will be ready for modeling in 2-3 hours. 🎉
