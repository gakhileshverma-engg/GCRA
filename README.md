# GCRA: Grid-Centric Rotational Analysis for Fingerprint Recognition

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](GCRA_Fingerprint_Pipeline.ipynb)

**Paper:** *A Statistical Grid and Rotational Analysis Model for Fingerprint Recognition: Theoretical Framework and Proof-of-Concept Validation*  
**Author:** Akhilesh Verma, Department of CSE, AKGEC Ghaziabad  
**Contact:** vermaakhilesh@akgec.ac.in  
**Journal:** Multimedia Tools and Applications (Springer)

---

## What This Repository Contains

| File | Description |
|------|-------------|
| `GCRA_Fingerprint_Pipeline.ipynb` | Complete Jupyter Notebook — data generation, pipeline, matching, visualizations |
| `requirements.txt` | Python dependencies |
| `README.md` | This file |

---

## Algorithm Overview

The GCRA model transforms a fingerprint scan into a compact
**9-dimensional statistical feature vector** through 5 stages:

```
Fingerprint Image
      ↓
Minutiae Extraction  (edge detection + thinning)
      ↓
Bounding-Box Normalisation  →  200×200 canonical image
      ↓
Centroid Computation  (Cx, Cy) = mean of all minutiae
      ↓
Grid Overlay + Rotational Accumulation
   K×K grid (K = 3, 5, 7), rotated 0°→355° in 5° steps
      ↓
Statistical Descriptors: Variance, Skewness, Kurtosis
      ↓
Feature Vector f ∈ ℝ⁹  →  Template Index / Matching
```

### Key Equations

**Centroid:**
$$C_x = \frac{1}{n}\sum x_i, \quad C_y = \frac{1}{n}\sum y_i$$

**Accumulated count matrix:**
$$\mathbf{A}^{(K)} = \sum_{\phi \in \{0°,5°,...,355°\}} \mathbf{G}^{(K)}(\phi)$$

**Variance:**
$$\sigma^2_K = \frac{1}{K^2}\sum_{j=1}^{K^2}(a_j - \bar{a})^2$$

**Skewness:**
$$\gamma^{(1)}_K = \frac{1}{K^2}\sum_{j=1}^{K^2}\left(\frac{a_j - \bar{a}}{\sigma_K}\right)^3$$

**Kurtosis (excess):**
$$\gamma^{(2)}_K = \frac{1}{K^2}\sum_{j=1}^{K^2}\left(\frac{a_j - \bar{a}}{\sigma_K}\right)^4 - 3$$

**Similarity score:**
$$s(\mathbf{f}_q, \mathbf{f}_s) = -\|\mathbf{f}_q - \mathbf{f}_s\|_2$$

---

## Quick Start

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/gcra-fingerprint.git
cd gcra-fingerprint
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Run the notebook
```bash
jupyter notebook GCRA_Fingerprint_Pipeline.ipynb
```

Or open directly in **Google Colab** — no installation needed.

---

## Using Real Fingerprint Data

This repository uses **synthetic data** for proof-of-concept.
To use real fingerprint data (FVC2000, FVC2002, NIST SD14):

**Step 1:** Download a dataset:
- FVC2000: http://bias.csr.unibo.it/fvc2000/
- FVC2002: http://bias.csr.unibo.it/fvc2002/
- NIST SD14: https://www.nist.gov/srd/nist-special-database-14

**Step 2:** Extract minutiae using an open-source tool:
- [MINDTCT (NIST NBIS)](https://www.nist.gov/services-resources/software/nist-biometric-image-software-nbis)
- [SourceAFIS (Java/Python)](https://sourceafis.machinezoo.com/)

**Step 3:** Replace the data generator in the notebook:
```python
# Instead of:
scans = generate_all_scans(CONFIG)

# Use:
import numpy as np

def load_minutiae_from_mindtct(filepath):
    """Load minutiae from MINDTCT .min output file."""
    minutiae = []
    with open(filepath) as f:
        for line in f:
            parts = line.strip().split()
            if len(parts) >= 2:
                minutiae.append([float(parts[0]), float(parts[1])])
    return np.array(minutiae)

scans = [load_minutiae_from_mindtct(f'scan_{i}.min') for i in range(5)]
```

The rest of the pipeline runs **unchanged**.

---

## Computational Complexity

| Stage | Complexity |
|-------|-----------|
| Minutiae extraction | O(W·H) |
| Bounding box + centroid | O(n) |
| Grid rotation + counting | O(n·R·L) |
| Statistical descriptors | O(K²) |
| **Total** | **O(W·H + n·R·L)** |
| Storage per identity | **O(1)** — fixed 9-dim vector |

With W=H=200, n=50, R=72, L=3: ~50,800 operations → **near real-time**.

---

## Comparison with Existing Methods

| Method | Rotation Robust. | Noise Robust. | Complexity |
|--------|-----------------|---------------|------------|
| Single-Minutia CGTC | Low | Low | O(n) |
| Minutiae Pair | Medium | Medium | O(n²) |
| Delaunay Triplet | High | Low | O(n³) |
| MCC | High | Medium | O(n·C) |
| CNN-based | High | High | High (train) |
| **GCRA (this work)** | **High** | **Medium** | **O(n·R·L)** |

---

## Limitations

- Currently validated on **synthetic data only**
- Real-world benchmark evaluation (FVC2000/2002, NIST SD14) is planned future work
- No formal FAR/FRR/EER metrics reported yet
- Partial fingerprint handling not yet implemented

See **Section 7 of the paper** for the full experimental validation roadmap.

---

## Citation

If you use this code, please cite:
```bibtex
@article{verma2024gcra,
  title   = {A Statistical Grid and Rotational Analysis Model for
             Fingerprint Recognition: Theoretical Framework and
             Proof-of-Concept Validation},
  author  = {Verma, Akhilesh},
  journal = {Multimedia Tools and Applications},
  year    = {2024},
  publisher = {Springer}
}
```

---

## License

MIT License — free for academic and research use with attribution.
