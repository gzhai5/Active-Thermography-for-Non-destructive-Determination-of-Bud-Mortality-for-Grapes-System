## Part 1 — Data Acquisition (`DataAcquisition/`)

### `dataAcquisitionGUI.py`
The primary data-collection application. A PyQt5 window that wraps the FLIR PySpin SDK and an Arduino relay controller to execute precisely timed heating–cooling experiments.

**Key classes:**

| Class | Role |
|---|---|
| `VideoThread` | QThread — streams live thermal video to the preview pane |
| `VideoThread_timed` | QThread — runs a timed acquisition: pre-heat (`t0`), heat (`t1`), cool-down (`t2`), controls the Arduino relay, converts raw Mono16 pixels to radiometric temperature using camera calibration constants (R, B, F, X, α, β, J0, J1), and saves output |
| `App` | Main window — focus control (coarse / fine / ±), parameter entry, auto-incremented sample naming (`cultivar_branch_node`) |
| `SubParameterGUI` | Secondary window for environmental parameters (emissivity, ambient temperature, humidity, distance) |

**Inputs:** Live 480×640 thermal frames (Mono16), GUI parameters (timing, naming, save path), Arduino on COM3.
**Outputs:** `.npy` array (uint16, frames × H × W) + `.cfg` parameter file per sample.

### `arduino_control.py`
Minimal CLI script. Sends `1` / `0` to Arduino pin 12 over serial to manually toggle the heating relay. Used for bench-testing outside the full GUI.

---

## Part 2 — Data Validation & ROI Annotation (`DataValidation/`)

### `app_window.py` — `ThermalAnalysisApp`
Interactive PyQt5 GUI for reviewing raw `.npy` files, selecting bud ROIs, and labelling mortality.

**Key workflows:**
- Load a folder of `.npy` files; navigate frames with a slider.
- Identify the top-N thermally-sensitive pixels (highest σ over a user-defined time window) and overlay them as red dots.
- Click to place ROI centre points; each centre is highlighted with a numbered hollow square.
- Toggle **Alive / Dead** label per sample (`A` key).
- Save ROI centre coordinates or full 7×7 (or 21×21) pixel-region time-series to CSV (`S` / `K` keys).
- Multi-plot mode for side-by-side curve comparison across ROIs.

**Keyboard shortcuts:** `Space` = next file · `Z` = revert last point · `A` = toggle label · `K` = save ROI data · `S` = save centre coords.

### `data_process.py`
Pure-function signal-processing module consumed by `app_window.py`.

| Function | Description |
|---|---|
| `find_top_sensitive_pixels()` | Returns the top-N pixel coordinates ranked by standard deviation over a given time window; restricts candidates to the central image region and applies an intensity threshold mask |
| `mask_pixel_filter()` | Masks pixels below threshold and outside the inner 2/3 of the frame |
| `extract_mean_val()` | Given a list of centre points and a radius, returns the frame-wise mean and σ curves for each ROI |

### `experiment_params.py` — `ExperimentParameter`
Dataclass holding system-wide defaults: `fps=30`, heating window bounds, `threshold=9400`, `roi_radius=15`, `interested_points_num=1000`. Changing values here propagates to all dependent modules.

### `mpl_canvas.py` — `MplCanvas`
Thin wrapper around `FigureCanvasQTAgg` that renders frames with a `'hot'` colormap and optionally registers mouse-click callbacks for interactive point selection.

### `cache.py` — `ComputationCache`
Simple container (top_points, std_dev, last_params) to memoize expensive pixel-sensitivity computations across re-renders.

---

## Part 3 — Statistical Analysis (`data_analysis/statistics/`)

### R scripts (`glme_stage2.r` – `glme_stage6.r`)
Each script fits a generalised linear mixed-effects model (GLME) at a progressively refined modelling stage. Random effects account for cultivar, treatment, experimental round, cane, and segment. The results quantify whether thermal features are statistically separable between viable and non-viable buds.

---

## Shared Utility

### `requirements.txt`
Core dependencies: `PyQt5`, `PySpin` (FLIR SDK), `opencv-python`, `numpy`, `matplotlib`, `Pillow`, `pyFirmata`, `pyserial`.