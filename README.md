# AXON-RT v0.1.2

**Adaptive eXecution Optimization Network - Runtime**

A dtype-aware numerical optimization runtime that delivers **free performance gains** by understanding cache physics.

## 🎯 The Discovery

We proved that **optimal matrix shapes depend on dtype**:

| dtype | Size ~2048 | Speedup | Why |
|-------|------------|---------|-----|
| float32 | 2048 (stay) | — | 128 cache lines (aligned) |
| float64 | **2056** | **+9.1%** | 257 cache lines (breaks conflict) |

This is the first runtime to encode this physics.

## 📊 Validation Results

| Test | Result |
|------|--------|
| Unit tests | 8/8 passed |
| Quantum Dive | dtype hypothesis confirmed |
| Endurance (266 laps) | **94.7% win rate, +9.1% avg** |
| BLAS | OpenBLAS 0.3.29 (Haswell kernel) |

## 🚀 Quick Start

```python
from axon_rt import AxonRT
import numpy as np

# Initialize (loads profile, ~10ms)
rt = AxonRT.auto_detect()

# Get dtype-aware shape recommendation (~0.01ms)
rec = rt.recommend_shape((2048, 2048), dtype=np.float64)
print(rec.recommended)  # (2056, 2056)

rec = rt.recommend_shape((2048, 2048), dtype=np.float32)
print(rec.recommended)  # (2048, 2048)

# Get dtype recommendation for workload
dtype_rec = rt.recommend_dtype("reflex")  # np.float32
dtype_rec = rt.recommend_dtype("accumulation")  # np.float64
```

## 📁 Package Contents

```
axon_rt/
├── core/
│   ├── axon_rt.py          # Main runtime (v0.1.2)
│   └── __init__.py
├── tests/
│   ├── test_axon_rt.py     # Unit tests
│   ├── quantum_dive.py     # dtype hypothesis test
│   └── endurance_test.py   # Stability test
├── benchmarks/
│   ├── benchmark_hardware.py
│   └── comprehensive_test.py
├── profiles/
│   └── alienware_profile.json
├── README.md
├── LICENSE
└── requirements.txt
```

## 🔧 Installation

```bash
# Clone or extract the package
cd axon_rt

# Install dependencies
pip install -r requirements.txt

# Run tests
python -m pytest tests/

# Or run individual test
python tests/test_axon_rt.py
```

## 💻 CLI Usage

```bash
# Show status
python -m axon_rt status

# Get shape recommendation
python -m axon_rt recommend-shape 2048 2048 --dtype float64
# Output: (2056, 2056)

# Get dtype recommendation
python -m axon_rt recommend-dtype reflex
# Output: float32

# Generate hardware profile
python -m axon_rt profile --out my_hardware.json
```

## 🔬 The Physics

```
Cache Line = 64 bytes

float32 (4 bytes/element):
  2048 × 4 = 8192 bytes/row = 128 cache lines ✓ (aligned)

float64 (8 bytes/element):
  2048 × 8 = 16384 bytes/row = 256 cache lines ✗ (conflict!)
  2056 × 8 = 16448 bytes/row = 257 cache lines ✓ (broken)
```

When row size is an exact multiple of cache associativity, different rows compete for the same cache lines. Adding 8 elements (2056) breaks this pattern.

## 📈 Performance

Validated on Alienware m15 R7 (AMD Ryzen 9 6900HX, OpenBLAS):

- **float64 @ 2056**: +9.1% faster than 2048 (sustained over 266 tests)
- **float32 @ 2048**: No change needed (already optimal)
- **float32 speedup**: 1.92x faster than float64

## 📄 License

MIT License - See LICENSE file


---

Created by:
 AXONLang Labs CEO Daniel N Santiago
 AXONLangLABS.com
 .AXON PROGRAM WILL BE REVEALED SOON! (LOOKING FOR A GOOD HOME FOR IT SO I CAN JUST HAVE MORE TIME TO CREATE)

*"Measure YOUR hardware. Apply physics-based rules. Get free speedups."*
