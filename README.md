# 🧊 Smart Food Storage Analytics
### Shelf-Life Prediction Under Real-World Storage Conditions

![Python](https://img.shields.io/badge/Python-3.8%2B-blue) ![XGBoost](https://img.shields.io/badge/Model-XGBoost-orange) ![R²](https://img.shields.io/badge/R²-0.9366-brightgreen) ![MAE](https://img.shields.io/badge/MAE-15.8%20days-yellow)

Manufacturer shelf-life labels assume perfect storage. In reality, temperature, humidity, and packaging dramatically change how long food stays safe. This project predicts **expected shelf life in days** given a product's chemical properties and current storage conditions — enabling smarter inventory decisions and proactive cold-chain monitoring.

> **Data note:** The dataset is synthetic, generated to reflect established food-science relationships (Arrhenius kinetics, water activity theory, pH pathogen thresholds). It is intended for pipeline demonstration purposes.

---

## Results

| Model | R² | CV R² (5-fold) | MAE |
|---|---|---|---|
| Linear Regression | 0.74 | 0.73 | 43.7 days |
| Random Forest (Tuned) | 0.93 | 0.93 | 17.1 days |
| **XGBoost (Tuned)** | **0.94** | **0.94** | **15.8 days** |

- 64% MAE reduction over baseline
- CV R² within 0.001 of test R² — no overfitting
- MAE = 1.7% of the full shelf-life range (1–946 days)

---

## How to Run

**1. Clone the repo**
```bash
git clone https://github.com/YOUR_USERNAME/smart-food-storage-analytics.git
cd smart-food-storage-analytics
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Launch the notebook**
```bash
jupyter notebook Smart_Food_Storage_Analytics.ipynb
```

Run all cells top to bottom. No additional configuration needed.

---

## Project Structure

```
├── Smart_Food_Storage_Analytics.ipynb  # Full pipeline — all steps end to end
├── food_shelf_life_4000.csv            # Synthetic dataset (4,000 samples)
├── requirements.txt                    # Python dependencies
├── results.md                          # All model outputs and predictions
└── outputs/                            # Generated charts (PNGs)
```

---

## Pipeline

| Step | What happens |
|---|---|
| **1. Data Cleaning** | Remove 55 duplicates, 51 impossible outliers, impute 1,467 missing values, fix 13 label typos → 3,463 clean rows |
| **2. EDA** | Target distribution, shelf life by category, temperature effect, packaging comparison, correlation heatmap |
| **3. Feature Engineering** | Spoilage Risk Index, Acidity Flag (pH < 4.6), Low Moisture Flag, Temp × Humidity Index |
| **4. Preprocessing** | Log-transform target, train/test split (80/20), StandardScaler fit on train only |
| **5. Modelling** | Linear Regression (baseline), Random Forest, XGBoost — tuned with RandomizedSearchCV + 5-fold CV |
| **6. Evaluation** | R², CV R², MAE, RMSE, residuals, actual vs predicted, feature importance |
| **7. Business Metrics** | Waste reduction analysis, inventory planning, cold-chain simulation |

---

## Dataset

| Property | Detail |
|---|---|
| Rows | 4,000 raw → 3,463 after cleaning |
| Categories | Bakery, Beverages, Condiments, Dairy, Meat, Produce, Seafood, Snacks |
| Target | `Shelf_Life_Days` — expected shelf life under given conditions (range: 1–946 days) |
| Source | Synthetic — generated to reflect food-science principles |

**Features:** Storage_Temp_C, Water_Activity, Relative_Humidity_pct, pH, Moisture_Content_pct, Packaging_Type, Preservative_Conc_pct, Protein_Content_pct, Fat_Content_pct, Food_Category

---

## Top Predictors

| Feature | Importance | Why it matters |
|---|---|---|
| Water Activity | 39.7% | Gold-standard microbial growth metric |
| Low Moisture (engineered) | 22.3% | Moisture < 15% strongly inhibits spoilage |
| Protein Content | 10.2% | Proteolysis rate indicator |
| Acidity Flag (engineered) | 4.6% | pH < 4.6 is the FDA pathogen inhibition threshold |
