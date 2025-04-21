# Fire Plume Polygonization Benchmark

This repository contains code and data for a synthetic "burn-day" experiment, comparing three strategies for extracting fire perimeters from time-indexed raster data:

1. **Real (Ground-Truth) Origins**  
   Counts the true number of ignition events recorded by the simulator (initial center + spot-fire jumps).

2. **Top-Down Inference**  
   Contours only the final aggregated burn mask to infer origin count, raw polygon count, and total burned area.

3. **Improved Bottom-Up Inference**  
   Contours each daily mask, injects controlled noise, filters out small clusters, and unions the results to infer the same three metrics with temporal fidelity and noise resilience.

---

## Getting Started

### Requirements

- Python 3.8 or higher  
- NumPy  
- Pandas  
- SciPy  
- scikit-image  
- Shapely  
- Matplotlib  

Install via pip:

```
pip install numpy pandas scipy scikit-image shapely matplotlib
```

### Code Overview

- `simulate_burn_day_with_origins(...)`  
  Runs the 2D fire spread + spot-fire model, returns a "burn day" array and the ground-truth origin count.

- `fit_polygons_top_down_full(...)`  
  Contours the final burn mask, returns (origins, polygon_count, area).

- `fit_polygons_bottom_up_improved(...)`  
  Contours daily masks with noise injection & cluster filtering, returns the same three metrics.

- `panel_across_noise_all_methods(...)`  
  Sweeps a range of noise levels, runs multiple replicates, and produces:  
  - A **3x6 panel** of density plots (`noise_panel.png`) showing distributions of:  
    - Origin counts  
    - Raw polygon counts  
    - Total burned area  
    across six noise levels and three methods  
  - A **CSV table** (`noise_metrics.csv`) listing every replicate's noise level, method, and metrics.

---

## Usage

1. **Clone this repository**  
   ```
   git clone git@github.com:your-org/fire-plume-benchmark.git
   cd fire-plume-benchmark
   ```

2. **Run the analysis**  
   ```
   python panel_noise_analysis.py
   ```  
   This will generate:  
   - `noise_panel.png` (3x6 KDE panel)  
   - `noise_metrics.csv` (raw metrics table)

3. **Inspect results**  
   - View the panel to compare how "Real," "Top-Down," and "Imp BU" perform under increasing noise.  
   - Load the CSV into Excel, Python, or R for further statistical analysis.

---

## Configuration

All sweep parameters are configurable at the bottom of `panel_noise_analysis.py`:

```
panel_across_noise_all_methods(
    rows=256,
    cols=256,
    days=60,
    wind_strength=1.0,
    max_jump_distance=10,
    noise_levels=[0,10,20,30,40,50],
    min_cluster_size=10,
    replicates=200,
    out_png="noise_panel.png",
    out_csv="noise_metrics.csv"
)
```

- `rows, cols`: raster dimensions  
- `days`: simulation duration  
- `wind_strength`, `max_jump_distance`: fire-spread parameters  
- `noise_levels`: list of "noise per day" values to sweep  
- `min_cluster_size`: cluster-filter threshold for Improved Bottom-Up  
- `replicates`: number of Monte Carlo runs per noise level  

---

## License

This code is released under the MIT License. Feel free to adapt and integrate into your own fire-mapping workflows.

