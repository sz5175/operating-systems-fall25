# Homework 04 – Concurrency


---

## Overview

This homework extends a non-thread-safe `parallel_hashtable.c` into multiple increasingly synchronized and optimized versions.
The progression includes:

1. **Unsafe baseline:** Demonstrates data races causing lost insertions/retrievals.
2. **Mutex version:** Adds a global mutex to fix all races.
3. **Spinlock version:** Replaces mutexes with spinlocks and compares performance.
4. **Optimized mutex version:** Adds per-bucket locking so that retrievals and some insertions can run in parallel.

All detailed written answers, graphs, timing tables, and overhead calculations are included in **`hw4_report.pdf`** (included in this folder).

---

## Files in This Folder

### `parallel_hashtable.c`
- Original unsafe implementation (no synchronization).
- Used as the baseline for:
  - Lost-key analysis 
  - Timing measurements 
  - Overhead comparisons

### `parallel_mutex.c`
- Introduces a **global mutex** to protect all table operations.
- Guarantees **correctness** (0 keys lost).
- Used for the *Mutex* section and overhead vs. the unsafe baseline.

### `parallel_spin.c`
- Replaces mutexes with **pthread spinlocks**.
- Still correct (0 keys lost).
- Performance worsens as thread count increases (busy-waiting).
- Used for the *Spinlock* section and overhead vs. both unsafe and mutex versions.

### `parallel_mutex_opt.c`
- Uses **per-bucket locks** for finer-grained parallelism.
- Multiple retrieves can run simultaneously.
- Inserts to different buckets can run in parallel.
- Improves performance over the global-mutex version.

### `partner.txt`
- Contains partnership information (to be filled separately).
- Not referenced inside this README.

### `hw4_report.pdf`  
(Must be added by the student before submission.)

Contains:
- Explanations of lost entries in the unsafe version
- Short answers for all theory questions:
  - Mutex explanation
  - Spinlock intuition + reasoning
  - Why retrieval can be parallelized
  - When insertions can be parallelized
- **Both graphs** required:
  - Unsafe vs Mutex
  - Unsafe vs Mutex vs Spinlock
- Overhead tables and percentage calculations
- Summary of performance trends

---

## How to Compile

Run these inside `homework04/`:

```bash
gcc -pthread parallel_hashtable.c   -o parallel_hashtable
gcc -pthread parallel_mutex.c       -o parallel_mutex
gcc -pthread parallel_spin.c        -o parallel_spin
gcc -pthread parallel_mutex_opt.c   -o parallel_mutex_opt
```

---

## How to Run All Tests

Each program takes **one argument**: the thread count.

### Unsafe baseline
```bash
./parallel_hashtable 1
./parallel_hashtable 2
./parallel_hashtable 4
./parallel_hashtable 8
./parallel_hashtable 16
./parallel_hashtable 32
./parallel_hashtable 64
./parallel_hashtable 128
./parallel_hashtable 256
```

### Mutex
```bash
./parallel_mutex 1
./parallel_mutex 2
./parallel_mutex 4
./parallel_mutex 8
./parallel_mutex 16
./parallel_mutex 32
./parallel_mutex 64
./parallel_mutex 128
./parallel_mutex 256
```

### Spinlock
```bash
./parallel_spin 1
./parallel_spin 2
./parallel_spin 4
./parallel_spin 8
./parallel_spin 16
./parallel_spin 32
./parallel_spin 64
./parallel_spin 128
./parallel_spin 256
```

### Optimized Mutex
```bash
./parallel_mutex_opt 1
./parallel_mutex_opt 2
./parallel_mutex_opt 4
./parallel_mutex_opt 8
./parallel_mutex_opt 16
./parallel_mutex_opt 32
./parallel_mutex_opt 64
./parallel_mutex_opt 128
./parallel_mutex_opt 256
```

---

## Thread Counts Used for Plots

As per TA instructions, I used the following:

```
1, 2, 4, 8, 16
```

(These show the clearest trend and match the requirement “multiples of 2 up to 256,” but graphs use the common subset.)

For each run, the following values were recorded:

- Insert time → from:
  `[main] Inserted ... seconds`
- Retrieve time → from: 
  `[main] Retrieved ... seconds`

**Total time = insert time + retrieve time**

These totals were used for plots and for computing overhead.

---

## Overhead Calculation (Summarized)

Documented fully in the PDF.

Formulas used:

### Mutex vs Unsafe
```
(mutex_total - unsafe_total) / unsafe_total * 100%
```

### Spinlock vs Unsafe
```
(spin_total - unsafe_total) / unsafe_total * 100%
```

### Spinlock vs Mutex
```
(spin_total - mutex_total) / mutex_total * 100%
```

The overhead tables for all thread counts are included in `hw4_report.pdf`.

---

## Notes & Assumptions

- Runtime fluctuates slightly per run due to OS scheduling.
  The PDF includes representative, stable values.
- All timing measurements use the **program's printed times**, not `time -v`.
  This method is consistent across all implementations.
- Thread output order (e.g., `[thread 25]`) is not sequential and not required to be.
  The assignment only requires correctness and timing.

---

## End of README

All explanations, graphs, tables, and written answers are in **hw4_report.pdf** included alongside this README.

