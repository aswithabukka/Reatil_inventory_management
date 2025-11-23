# Feature Engineering with MPI4py - Instructions

## Problem
The Jupyter notebook kernel dies when processing 23.8M rows due to memory exhaustion.

## Solution
Use MPI4py to process data in parallel batches across multiple CPU cores.

---

## How It Works

### Strategy
1. **Split work by store-item combinations** (~221,400 pairs)
2. **Each MPI process handles a subset** of store-item pairs
3. **Process independently** (no communication overhead)
4. **Combine results** at the end
5. **Create train-test split** and save final files

### Benefits
- ✅ **No memory crashes** - Each process handles smaller data
- ✅ **Faster processing** - Parallel execution across cores
- ✅ **Same results** - Identical to sequential processing
- ✅ **Scalable** - Add more processes for faster execution

---

## Installation

### 1. Install MPI4py
```bash
pip3 install mpi4py
```

### 2. Verify Installation
```bash
python3 -c "from mpi4py import MPI; print('MPI4py installed successfully!')"
```

---

## Usage

### Option 1: Using the Shell Script (Easiest)

```bash
# Make script executable
chmod +x run_feature_engineering.sh

# Run with default settings (4 processes)
./run_feature_engineering.sh
```

### Option 2: Direct MPI Command

```bash
# Run with 4 processes
mpiexec -n 4 python3 feature_engineering_mpi.py

# Run with 8 processes (if you have more CPU cores)
mpiexec -n 8 python3 feature_engineering_mpi.py
```

### Option 3: Adjust Number of Processes

Edit `run_feature_engineering.sh` and change:
```bash
NUM_PROCESSES=4  # Change to your desired number
```

**Recommendation**: Use `number of CPU cores - 1`

---

## How Many Processes Should I Use?

### Check Your CPU Cores
```bash
# On Mac/Linux
sysctl -n hw.ncpu

# Or in Python
python3 -c "import os; print(f'CPU cores: {os.cpu_count()}')"
```

### Recommendations
| CPU Cores | Recommended Processes | Processing Time (Estimate) |
|-----------|----------------------|---------------------------|
| 4 cores   | 3 processes          | ~20-30 minutes            |
| 8 cores   | 6-7 processes        | ~10-15 minutes            |
| 16 cores  | 12-14 processes      | ~5-10 minutes             |

**Note**: Leave 1-2 cores free for system operations

---

## What Features Are Created?

### 1. Temporal Features (10 features)
- year, month, day, day_of_week, day_of_year
- week_of_year, quarter
- is_weekend, is_month_start, is_month_end

### 2. Lag Features (4 features)
- sales_lag_1, sales_lag_7, sales_lag_14, sales_lag_28

### 3. Rolling Features (6 features)
- sales_rolling_mean_7, sales_rolling_std_7
- sales_rolling_mean_14, sales_rolling_std_14
- sales_rolling_mean_30, sales_rolling_std_30

### 4. EWMA Features (2 features)
- sales_ewm_7, sales_ewm_14

### 5. Difference Features (2 features)
- sales_diff_1, sales_diff_7

**Total New Features**: ~24
**Total Features**: ~44 (including original 20)

---

## Output Files

After successful execution, you'll get:

### 1. `train_2017_ts_train.parquet`
- Training dataset with all features
- Date range: 2017-01-01 to 2017-08-01
- ~23.3M rows × 44 columns
- Ready for model training

### 2. `train_2017_ts_test.parquet`
- Test dataset with all features
- Date range: 2017-08-02 to 2017-08-15 (last 14 days)
- ~500K rows × 44 columns
- For model evaluation

---

## Monitoring Progress

### What You'll See
```
[Rank 0] Loading dataset...
[Rank 1] Loading dataset...
[Rank 2] Loading dataset...
[Rank 3] Loading dataset...
[Rank 0] Processing 55,350 store-item pairs
[Rank 1] Processing 55,350 store-item pairs
...
[Rank 0] Creating lag features...
[Rank 0]   - Created sales_lag_1
[Rank 0]   - Created sales_lag_7
...
[Rank 0] Saving to train_2017_features_rank_0.parquet...
...
COMBINING RESULTS FROM ALL PROCESSES
Loading train_2017_features_rank_0.parquet...
...
✓ FEATURE ENGINEERING COMPLETE!
```

### Processing Time
- **With 4 processes**: ~20-30 minutes
- **With 8 processes**: ~10-15 minutes

---

## Troubleshooting

### Issue 1: "mpiexec: command not found"
**Solution**: Install MPI
```bash
# On Mac
brew install open-mpi

# On Ubuntu/Debian
sudo apt-get install openmpi-bin openmpi-common libopenmpi-dev
```

### Issue 2: "ImportError: No module named 'mpi4py'"
**Solution**: Install mpi4py
```bash
pip3 install mpi4py
```

### Issue 3: Still Running Out of Memory
**Solution**: Increase number of processes
```bash
# Use more processes to split work further
mpiexec -n 8 python3 feature_engineering_mpi.py
```

### Issue 4: Process Hangs or Freezes
**Solution**: 
1. Kill the process: `Ctrl+C`
2. Check for zombie processes: `ps aux | grep python`
3. Kill if needed: `killall python3`
4. Restart with fewer processes

### Issue 5: "File not found: train_2017_clean.parquet"
**Solution**: Make sure you're in the correct directory
```bash
cd /Users/aswithabukka/Documents/AMS598_project
ls train_2017_clean.parquet  # Should exist
```

---

## Verification

### Check Output Files
```bash
# List output files
ls -lh train_2017_ts_*.parquet

# Check file sizes (should be ~200-300 MB each)
du -h train_2017_ts_train.parquet
du -h train_2017_ts_test.parquet
```

### Verify Data in Python
```python
import pandas as pd

# Load and check train set
train = pd.read_parquet('train_2017_ts_train.parquet')
print(f"Train shape: {train.shape}")
print(f"Date range: {train['date'].min()} to {train['date'].max()}")
print(f"Features: {train.columns.tolist()}")

# Load and check test set
test = pd.read_parquet('train_2017_ts_test.parquet')
print(f"Test shape: {test.shape}")
print(f"Date range: {test['date'].min()} to {test['date'].max()}")

# Check for missing values
print(f"Missing values in train: {train.isnull().sum().sum()}")
print(f"Missing values in test: {test.isnull().sum().sum()}")
```

---

## Performance Tips

### 1. Use SSD Storage
- Processing is I/O intensive
- SSD significantly faster than HDD

### 2. Close Other Applications
- Free up RAM for processing
- Close browser, IDE, etc.

### 3. Monitor System Resources
```bash
# On Mac
top

# Or use Activity Monitor GUI
```

### 4. Adjust Process Count
- More processes = faster but more memory per process
- Fewer processes = slower but less memory pressure
- Find the sweet spot for your system

---

## Next Steps After Feature Engineering

Once you have the processed files:

### 1. Load Data for Modeling
```python
import pandas as pd

train = pd.read_parquet('train_2017_ts_train.parquet')
test = pd.read_parquet('train_2017_ts_test.parquet')
```

### 2. Select Features
```python
# Exclude non-predictive columns
feature_cols = [col for col in train.columns 
                if col not in ['unit_sales', 'date', 'id', 'store_nbr', 'item_nbr']]

X_train = train[feature_cols]
y_train = train['unit_sales']
```

### 3. Train Model (with MPI4py for parallelization)
See `PROJECT_SUMMARY.md` for model training examples

---

## Comparison: Notebook vs MPI4py

| Aspect | Jupyter Notebook | MPI4py Script |
|--------|------------------|---------------|
| Memory Usage | High (all data in memory) | Low (split across processes) |
| Processing Time | N/A (crashes) | 10-30 minutes |
| Scalability | Limited | Excellent |
| Monitoring | Cell-by-cell | Terminal output |
| Debugging | Easy | Moderate |
| Best For | Small datasets | Large datasets |

---

## Files Created

```
AMS598_project/
├── train_2017_clean.parquet              # Input (original data)
├── feature_engineering_mpi.py            # MPI processing script
├── run_feature_engineering.sh            # Convenience script
├── FEATURE_ENGINEERING_README.md         # This file
├── train_2017_ts_train.parquet          # Output (training set)
└── train_2017_ts_test.parquet           # Output (test set)
```

---

## Questions?

### Common Questions

**Q: Can I run this without MPI?**  
A: Yes, but it will likely crash due to memory. MPI is necessary for this dataset size.

**Q: How long does it take?**  
A: 10-30 minutes depending on CPU cores and system resources.

**Q: Can I stop and resume?**  
A: No, you need to run it completely. But it's fast enough that this isn't an issue.

**Q: What if I need more features?**  
A: Edit `feature_engineering_mpi.py` and add your feature functions.

**Q: Can I use this for other datasets?**  
A: Yes! Just change the input filename and adjust feature functions as needed.

---

## Success Criteria

You'll know it worked when:
- ✅ No error messages
- ✅ Two output files created
- ✅ Train file is ~23.3M rows
- ✅ Test file is ~500K rows
- ✅ Both files have ~44 columns
- ✅ No missing values (or only in expected columns)

---

**Ready to run? Execute:**
```bash
./run_feature_engineering.sh
```

**Good luck! 🚀**
