[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)]()
[![Python](https://img.shields.io/badge/Python-3.10-blue.svg)]()
[![Paper](https://img.shields.io/badge/Paper-Under%20Review-green.svg)]()
# Active Thermography for Non-destructive Determination of Bud Mortality for Grapes: System Design and Capability

This directory contains all code, data organization, and documentation related to **Manuscript I**, which focuses on the **design, implementation, and validation of an active thermography system** for non-destructive grape-bud mortality assessment.

---

## 🎯 Research Objectives

- Engineer and document a custom, synchronized pulsed-thermography acquisition system and companion software to collect standardized heating–cooling sequences at the bud scale
-  Quantify separability by extracting informative waveform features and applying a principled statistical analysis plan (mixed-effects modeling) to compare intact vs damaged buds within cultivars
- Investigate mechanisms and factors that can explain differences in thermal responses and guide protocol design

---

## 🔧 Hardware System

The active thermography system consists of a synchronized heating and thermal imaging setup designed for repeatable, high-resolution measurements.

**Core components include:**
- Pulsed heat excitation source
- High-resolution thermal camera
- Custom mounting and alignment fixtures
- Hardware triggering and synchronization

📷 **Hardware System overview**

<img src="readme_files/hardware_system.png" width="300" alt="Hardware System">

## 💻 Software System

Two graphical user interfaces (GUIs) were developed to support data collection and quality control:

### 1️⃣ Acquisition GUI
- Controls heating pulse timing
- Synchronizes thermal image acquisition
- Ensures consistent acquisition parameters across experiments

### 2️⃣ Validation GUI
- Real-time visualization of thermal frames
- ROI placement and inspection
- Metadata annotation and sample tracking

<img src="readme_files/guis.png" width="700" alt="GUIs">

---

## 📊 Data Products

### Thermal ROI Data
- Format: `.npy`
- Shape: `21 × 21 × 600` (spatial × spatial × time)
- One file per grape bud sample

Each file represents the spatiotemporal thermal response of a single bud during a controlled heating–cooling cycle.

### Metadata & Feature Tables
Stored as `.csv` files containing:
- Experimental metadata (`cultivar`, `treatment`, `dev_stage`, `cane`, `segment`)
- Mortality ground truth
- Extracted waveform features
- PCA components derived from time-series data

---

## 🎥 Example Thermal Responses

Below is a qualitative comparison of thermal behavior between viable and non-viable buds.

- **Viable bud:** smooth rise during heating, gradual decay during cooling
- **Non-viable bud:** altered slope, delayed response, or suppressed amplitude

📈 **Mean time-series curve comparison**

<img src="readme_files/mean_curve_compare.png" width="300" alt="Mean Curve Comparison">

---

## 🧩 ROI Extraction & Preprocessing

### Region of Interest (ROI) Extraction
- Bud-centered ROIs extracted from thermal frames
- Fixed spatial size to ensure consistency across samples

<img src="readme_files/roi_extract.png" width="700" alt="ROI Extraction Example">

### Temporal Normalization
- Frame-wise normalization to control for baseline temperature variation
- Ensures comparability across sessions and cultivars

<img src="readme_files/normalization.png" width="600" alt="Normalization Comparison">

### Feature Engineering
Features extracted from heating and cooling phases include:
- Rising and falling slopes
- Peak temperature
- Temporal variability metrics

<img src="readme_files/waveform_extraction.png" width="250" alt="Waveform Feature Extraction">

---

## 📐 Statistical Analysis

To evaluate whether thermal responses differ systematically between viable and non-viable buds, we apply:

- Mixed-effects modeling to account for:
  - Cultivar
  - Treatment
  - Experimental round
  - Cane and segment effects
- Within-cultivar comparisons to isolate mortality effects

<img src="readme_files/statistical_test_results.png" width="300" alt="Statistical Test Results">

These analyses establish the **statistical separability** of mortality states and motivate the modeling work in Manuscript II.

---

## 📌 Notes

This manuscript focuses on **system engineering, experimental validation, and statistical capability analysis**.
No machine-learning model benchmarking or predictive performance comparisons are reported here.

---

## 🗂️ Code Structure & Function

All source code lives under `code/` and is organized into three parts that mirror the experimental pipeline.

```
code/
├── DataAcquisition/
│   ├── dataAcquisitionGUI.py   # Part 1 — main data acquisition GUI used for collecting the data
│   └── arduino_control.py      # utility code for manual relay control testing
├── DataValidation/
│   ├── main.py                 # Part 2 — data validation GUI entry point
│   ├── app_window.py           # Main validation / annotation window
│   ├── data_process.py         # Signal processing utilities
│   ├── experiment_params.py    # Centralised parameter config
│   ├── mpl_canvas.py           # Matplotlib-in-PyQt5 canvas widget
│   ├── cache.py                # Computation cache helper
│   └── data_analysis.py        # Additional analysis helpers
├── data_analysis/
│   └── statistics/
│       ├── abnormals.py        # Master list of excluded samples
│       ├── abnormal_remover.ipynb
│       ├── preprocess_csv.ipynb
│       ├── roi_data_generate.ipynb
│       ├── plot_curves.ipynb
│       ├── glme_stage2–6.r     # Statistics Test R files for all experiments
│       └── flow_chart.ipynb
├── sound.py
├── requirements.txt
└── README.md
```

---

## Citation

If you use this repository in academic research, please cite:

```bibtex
@article{zhai2026thermography,
  title={Active Thermography for Non-destructive Determination of Bud Mortality for Grapes},
  author={Zhai, Guangxun and Jiang, Yu and Owens, John and Vanden Heuvel, Justine and Zoubi, Alan},
  journal={Under Review},
  year={2026}
}
```

---

## License

This project is released under the MIT License.

See [LICENSE](LICENSE) for details.

