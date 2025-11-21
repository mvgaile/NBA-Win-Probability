# 📊 NBA Win Probability Model  
![Python](https://img.shields.io/badge/Python-3.11-blue.svg)
![PyTorch](https://img.shields.io/badge/PyTorch-2.x-EE4C2C.svg)
![License](https://img.shields.io/badge/License-Apache%202.0-green.svg)
![Conda](https://img.shields.io/badge/Conda-Environment-success.svg)
![API](https://img.shields.io/badge/Uses-NBA_Stats_API-orange.svg)
### *A fully engineered, data-driven win probability system built from scratch (PBP → Features → Neural Net → Calibration → Visualization).*

This project implements a complete NBA Win Probability (WP) pipeline using play-by-play data from the NBA Stats API and sportsbook moneylines. It includes:

- Full data engineering for multi-season PBP  
- Odds standardization and cleaning  
- Game-state construction  
- Neural network model training  
- Probability calibration (Isotonic Regression)  
- A real-time single-game win probability generator  
- ESPN-style visualizations with team-colored curves & score margin overlay  

The system outputs smooth, calibrated, high-quality win probability graphs similar to ESPN, Inpredictable, and professional analytics platforms.

---

## 🚀 Features

### 🏗 Full Data Pipeline
- Downloads and caches multi-season PBP and game summaries  
- Handles NBA API rate limits (requires batching)  
- Resolves inconsistent team labeling in sportsbook data  
- Cleans missing scores, malformed fields, and extreme odds entries  

### 🔄 Game-State Engine
- Converts raw PBP events into chronological game states  
- Computes:
  - `seconds_remaining_game` (reg + OT)  
  - Possession state  
  - Free throw metadata  
  - Score differential  
  - Betting-derived priors  
  - Time transformations (`t_log`, `t_inv`)  
  - Clutch indicators  

### 🧠 Machine Learning Model  
- MLP (PyTorch) using engineered features  
- Trained with grouped splits to prevent game leakage  
- Calibrated with isotonic regression  
- Produces highly accurate and well-calibrated probabilities  

**Validation metrics:**  
- **Brier:** ~0.142  
- **Log Loss:** ~0.43  
- **AUC:** ~0.868  

These are strong results—comparable to or better than many public WP models.

### 📈 Visualizations  
Includes a win probability graph:

- Line segments colored with home/away team colors  
- 50% threshold line  
- Quarter/OT break markers  
- Score margin overlay (secondary axis)  
- Can be flipped to display away team win prob instead

---

## 📁 Repository Structure
- /csvs/                  --- Cached play-by-play and odds (not included in repo)
- /pbp_raw/               --- Raw PBP data saved during training (not included in repo)
- wp_train_public.ipynb   --- Full end-to-end training notebook
- wp_graph_public.ipynb   --- Single-game win probability visualization
- nn_winprob_aux.pkl      --- Model scaler, calibration layer, and feature list
- nn_winprob_model.pt     --- Trained Win Probability Model (PyTorch)
- environment.yml         --- Working conda environment
- LICENSE                 --- Apache 2.0
- README.md

## 📝 Installation

For best results, use Conda. Pip installation is not guaranteed to reproduce the same environment.

Create a conda environment:

```bash
conda env create -f environment.yml
conda activate nba_wp_nc
```

## ⚙️ Usage

### **Training the model** (OPTIONAL)

Open:

wp_train_public.ipynb

This notebook:

- Downloads and cleans the raw play-by-play and betting data  
- Builds the full training dataset of engineered game states  
- Trains and calibrates the neural network model  
- Saves the model artifacts:
  - `nn_winprob_model.pt`
  - `nn_winprob_aux.pkl`

##### Be very careful using this notebook. It is very easy to accidentally downloads lots of data. One code block was converted to raw text in order to prevent accidentally processing lots of data.
---

### **Generating win probability for a single game**

Open:

wp_graph_public.ipynb

Provide:

```python
GAME_ID = "0022500262"              
HOME_TEAM_MONEYLINE = 240       
AWAY_TEAM_MONEYLINE = -300          
```

This notebook:

- Downloads the PBP data for just the selected game  
- Constructs game states from event data  
- Loads the trained model + scaler + calibration layer  
- Produces a win probability graph


## 📘 NBA API Notes

- The NBA Stats API rate-limits after approximately 600 requests.
- Large data pulls must be staggered or cached locally.
- The training pipeline is designed so downloads can be resumed safely without repeating completed work.
