# Running Data Analysis on HPC Cluster with SLURM

This guide explains how to run the complete data analysis pipeline on an HPC cluster using SLURM job scheduler.

---

## 📁 Files Overview

### Python Scripts
1. **`01_data_cleaning_eda.py`** - Data cleaning and exploratory data analysis
2. **`02_feature_engineering.py`** - Time series feature engineering

### SLURM Job Scripts
1. **`01_data_cleaning_eda.slurm`** - SLURM job for Part 1
2. **`02_feature_engineering.slurm`** - SLURM job for Part 2

---

## 🚀 Quick Start

### Step 1: Upload Files to HPC Cluster

```bash
# From your local machine, upload files to cluster
scp train_2017_clean.parquet username@cluster.edu:/path/to/project/
scp 01_data_cleaning_eda.py username@cluster.edu:/path/to/project/
scp 02_feature_engineering.py username@cluster.edu:/path/to/project/
scp 01_data_cleaning_eda.slurm username@cluster.edu:/path/to/project/
scp 02_feature_engineering.slurm username@cluster.edu:/path/to/project/
```

### Step 2: SSH into Cluster

```bash
ssh username@cluster.edu
cd /path/to/project/
```

### Step 3: Create Logs Directory

```bash
mkdir -p logs output_data output_plots
```

### Step 4: Edit SLURM Scripts

Edit the SLURM scripts to match your cluster configuration:

```bash
nano 01_data_cleaning_eda.slurm
```

**Update these lines:**
- `#SBATCH --partition=standard` → Your partition name
- `#SBATCH --mail-user=your_email@example.com` → Your email
- Module load commands (if your cluster uses modules)

Do the same for `02_feature_engineering.slurm`.

### Step 5: Submit Jobs

```bash
# Submit Part 1
sbatch 01_data_cleaning_eda.slurm

# Wait for Part 1 to complete, then submit Part 2
sbatch 02_feature_engineering.slurm
```

---

## 📊 Resource Requirements

### Part 1: Data Cleaning & EDA
| Resource | Requirement | Notes |
|----------|-------------|-------|
| **Time** | 2 hours | Usually completes in 30-60 min |
| **Memory** | 32 GB | Increase if OOM errors occur |
| **CPUs** | 4 cores | For pandas operations |
| **Storage** | ~2 GB | For plots and intermediate data |

### Part 2: Feature Engineering
| Resource | Requirement | Notes |
|----------|-------------|-------|
| **Time** | 4 hours | Usually completes in 1-2 hours |
| **Memory** | 64 GB | **Most critical!** Increase to 96-128GB if needed |
| **CPUs** | 8 cores | More cores help with groupby operations |
| **Storage** | ~500 MB | For final processed datasets |

---

## 🔧 Cluster-Specific Configuration

### Finding Your Partition Names

```bash
# List available partitions
sinfo

# Or
scontrol show partition
```

Common partition names:
- `standard`, `general`, `batch`
- `short`, `medium`, `long` (by time limit)
- `cpu`, `gpu` (by resource type)

### Checking Available Resources

```bash
# Check your account limits
sacctmgr show user $USER withassoc format=user,account,partition,maxjobs,maxsubmit

# Check partition limits
scontrol show partition YOUR_PARTITION_NAME
```

### Module System

If your cluster uses modules, add these lines to SLURM scripts:

```bash
# Example for common HPC setups
module purge
module load python/3.9
module load gcc/11.2.0

# Or for Anaconda
module load anaconda3/2023.03
```

### Virtual Environment

If using a virtual environment:

```bash
# Create venv (one-time setup)
python3 -m venv ~/myenv
source ~/myenv/bin/activate
pip install pandas numpy matplotlib seaborn scipy scikit-learn pyarrow

# In SLURM script, add:
source ~/myenv/bin/activate
```

### Conda Environment

If using conda:

```bash
# Create conda env (one-time setup)
conda create -n dataanalysis python=3.9
conda activate dataanalysis
conda install pandas numpy matplotlib seaborn scipy scikit-learn pyarrow

# In SLURM script, add:
conda activate dataanalysis
```

---

## 📝 Monitoring Jobs

### Check Job Status

```bash
# View your jobs
squeue -u $USER

# Detailed job info
scontrol show job JOB_ID

# View job history
sacct -j JOB_ID --format=JobID,JobName,Partition,State,Elapsed,MaxRSS
```

### Monitor Running Job

```bash
# Watch output in real-time
tail -f logs/01_data_cleaning_eda_JOBID.out

# Check errors
tail -f logs/01_data_cleaning_eda_JOBID.err

# Monitor memory usage
sstat -j JOB_ID --format=JobID,MaxRSS,AveCPU
```

### Cancel Job

```bash
scancel JOB_ID
```

---

## 🔍 Troubleshooting

### Issue 1: Out of Memory (OOM)

**Symptoms:**
- Job killed with "Out of Memory" error
- Exit code 137 or similar

**Solutions:**
```bash
# Increase memory in SLURM script
#SBATCH --mem=96GB  # or 128GB

# Reduce CPU count (uses less memory per thread)
#SBATCH --cpus-per-task=4
```

### Issue 2: Job Timeout

**Symptoms:**
- Job killed after reaching time limit
- Status shows "TIMEOUT"

**Solutions:**
```bash
# Increase time limit
#SBATCH --time=06:00:00  # 6 hours

# Or use a different partition with longer limits
#SBATCH --partition=long
```

### Issue 3: Module Not Found

**Symptoms:**
- "module: command not found"
- Python or package import errors

**Solutions:**
```bash
# Option 1: Use full path to Python
/usr/bin/python3 script.py

# Option 2: Load correct modules
module avail  # See available modules
module load python/3.9

# Option 3: Use conda/venv (see above)
```

### Issue 4: Permission Denied

**Symptoms:**
- "Permission denied" when running script
- Cannot create directories

**Solutions:**
```bash
# Make scripts executable
chmod +x 01_data_cleaning_eda.py
chmod +x 02_feature_engineering.py

# Check directory permissions
ls -ld /path/to/project/
```

### Issue 5: Input File Not Found

**Symptoms:**
- "FileNotFoundError: train_2017_clean.parquet"

**Solutions:**
```bash
# Check file exists
ls -lh train_2017_clean.parquet

# Check you're in correct directory
pwd

# Update SLURM script to use absolute paths
python3 /full/path/to/01_data_cleaning_eda.py
```

---

## 📤 Output Files

### After Part 1 (Data Cleaning & EDA)

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

### After Part 2 (Feature Engineering)

```
output_data/
├── train_2017_ts_train.parquet  ← Main output for modeling
├── train_2017_ts_test.parquet   ← Main output for evaluation
└── feature_list.csv
```

---

## 💾 Downloading Results

### Download to Local Machine

```bash
# Download processed datasets
scp username@cluster.edu:/path/to/project/output_data/train_2017_ts_train.parquet .
scp username@cluster.edu:/path/to/project/output_data/train_2017_ts_test.parquet .

# Download all plots
scp -r username@cluster.edu:/path/to/project/output_plots/ .

# Download all output data
scp -r username@cluster.edu:/path/to/project/output_data/ .
```

### Using rsync (Faster for Large Files)

```bash
rsync -avz --progress username@cluster.edu:/path/to/project/output_data/ ./output_data/
```

---

## 🔄 Workflow

### Sequential Execution (Recommended)

```bash
# 1. Submit Part 1
JOB1=$(sbatch --parsable 01_data_cleaning_eda.slurm)
echo "Part 1 Job ID: $JOB1"

# 2. Submit Part 2 with dependency (waits for Part 1 to complete)
sbatch --dependency=afterok:$JOB1 02_feature_engineering.slurm
```

### Check Progress

```bash
# Watch job queue
watch -n 10 squeue -u $USER

# Check completion
squeue -u $USER | grep -E "COMPLETED|RUNNING|PENDING"
```

---

## ⚙️ Advanced Options

### Array Jobs (For Multiple Datasets)

If you have multiple datasets to process:

```bash
#SBATCH --array=1-5

# In script, use $SLURM_ARRAY_TASK_ID
INPUT_FILE="train_${SLURM_ARRAY_TASK_ID}_clean.parquet"
```

### Email Notifications

```bash
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --mail-user=your_email@example.com
```

You'll receive emails when:
- Job starts (BEGIN)
- Job completes (END)
- Job fails (FAIL)

### Job Dependencies

```bash
# Part 2 only runs if Part 1 succeeds
sbatch --dependency=afterok:JOB_ID 02_feature_engineering.slurm

# Part 2 runs regardless of Part 1 status
sbatch --dependency=after:JOB_ID 02_feature_engineering.slurm
```

### Resource Reservation

```bash
# Reserve specific nodes
#SBATCH --nodelist=node001,node002

# Exclude specific nodes
#SBATCH --exclude=node003

# Request specific features
#SBATCH --constraint=haswell
```

---

## 📈 Performance Optimization

### 1. Optimal CPU Count

```bash
# Test different CPU counts
#SBATCH --cpus-per-task=4   # Baseline
#SBATCH --cpus-per-task=8   # Better for groupby
#SBATCH --cpus-per-task=16  # May not help much

# Monitor CPU usage
sstat -j JOB_ID --format=AveCPU
```

### 2. Memory Tuning

```bash
# Start conservative
#SBATCH --mem=32GB

# Increase if needed
#SBATCH --mem=64GB  # Usually sufficient
#SBATCH --mem=96GB  # For safety
#SBATCH --mem=128GB # If you have OOM issues
```

### 3. Parallel Processing

For even faster processing, modify scripts to use:
- `joblib` for parallel loops
- `dask` for out-of-core processing
- `multiprocessing` for CPU-bound tasks

---

## 🎯 Best Practices

### 1. Test with Small Sample First

```python
# In Python scripts, add at top for testing:
# df = df.sample(n=100000, random_state=42)  # Test with 100K rows
```

### 2. Use Checkpointing

Save intermediate results:
```python
# After each major step
df.to_parquet(f'checkpoint_{step_name}.parquet')
```

### 3. Log Everything

```python
import logging
logging.basicConfig(
    filename='processing.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)
```

### 4. Clean Up

```bash
# After successful completion, remove intermediate files
rm output_data/data_preprocessed.parquet  # If not needed
rm checkpoint_*.parquet
```

---

## 📊 Expected Timing

Based on typical HPC clusters:

| Step | With 32GB RAM | With 64GB RAM | With 128GB RAM |
|------|---------------|---------------|----------------|
| Part 1 | 45-60 min | 30-45 min | 25-35 min |
| Part 2 | 2-3 hours | 1-2 hours | 45-90 min |
| **Total** | **3-4 hours** | **1.5-2.5 hours** | **1-2 hours** |

---

## 🆘 Getting Help

### Cluster-Specific Help

```bash
# View SLURM documentation
man sbatch
man squeue
man scancel

# Contact your HPC support
# Usually: hpc-support@your-institution.edu
```

### Check Job Efficiency

```bash
# After job completes
seff JOB_ID

# Shows:
# - CPU efficiency
# - Memory efficiency
# - Time used vs requested
```

### Common SLURM Commands Cheat Sheet

```bash
sbatch script.slurm          # Submit job
squeue -u $USER              # View your jobs
scancel JOB_ID               # Cancel job
scontrol show job JOB_ID     # Job details
sacct -j JOB_ID              # Job accounting
sinfo                        # Cluster info
seff JOB_ID                  # Job efficiency
```

---

## ✅ Verification

After both jobs complete successfully:

```bash
# Check output files exist
ls -lh output_data/train_2017_ts_train.parquet
ls -lh output_data/train_2017_ts_test.parquet

# Verify file sizes (should be ~200-300 MB each)
du -h output_data/train_2017_ts_*.parquet

# Check feature count
python3 -c "import pandas as pd; df = pd.read_parquet('output_data/train_2017_ts_train.parquet'); print(f'Features: {df.shape[1]}, Rows: {df.shape[0]:,}')"

# Expected output:
# Features: ~70-80, Rows: ~23,300,000
```

---

## 🎓 Summary

**To run the complete pipeline:**

1. Upload files to cluster
2. Edit SLURM scripts for your cluster
3. Create directories: `mkdir -p logs output_data output_plots`
4. Submit Part 1: `sbatch 01_data_cleaning_eda.slurm`
5. Wait for completion (check with `squeue -u $USER`)
6. Submit Part 2: `sbatch 02_feature_engineering.slurm`
7. Download results when complete

**Total time**: 1.5-4 hours depending on resources

**Output**: Ready-to-use datasets for time series forecasting!

---

**Need help?** Check your cluster's documentation or contact HPC support.

**Ready to start?** 
```bash
sbatch 01_data_cleaning_eda.slurm
```

Good luck! 🚀
