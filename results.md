# 📊 Project Results — Smart Food Storage Analytics

## 1. Data Cleaning Summary
Raw rows : 4,038 
Duplicate rows removed : 55 
Outliers removed : 51 (pH > 10, Temp > 50°C, Moisture > 100%) 
Missing values imputed : 1,467 (median for numeric, mode for categorical) 
**Final cleaned rows** : **3,463** 

Label typos fixed — Food Category:
BEFORE: 13 inconsistent labels
['Bakery', 'Bakery ', 'Beverages', 'Condiments', 'Dairy',
 'MEAT', 'Meat', 'Produce', 'Sea food', 'Seafood',
 'Snacks', 'dairy', 'snacks ']

AFTER: 8 clean categories
['Bakery', 'Beverages', 'Condiments', 'Dairy',
 'Meat', 'Produce', 'Seafood', 'Snacks']

 
## 2. Feature Engineering

| Feature | Formula | Food-Science Basis |
|---|---|---|
| `Spoilage_Risk` | Storage_Temp_C × Water_Activity | Arrhenius kinetics proxy |
| `Is_Acidic` | 1 if pH < 4.6 else 0 | FDA pathogen inhibition threshold |
| `Low_Moisture` | 1 if Moisture < 15% else 0 | Microbial growth cutoff |
| `Temp_Humidity_Index` | Storage_Temp_C × Humidity / 100 | Combined environmental stress |

**Train / Test split:** 2,770 train · 693 test · 17 features

---

## 3. Model Results

| Model | R² Score | CV R² (5-fold) | MAE | RMSE |
|---|---|---|---|---|
| Linear Regression | 0.7418 | 0.7317 | 43.7 days | 103.7 days |
| Random Forest (Tuned) | 0.9310 | 0.9337 | 17.1 days | 45.6 days |
| **XGBoost (Tuned)** | **0.9366** | **0.9358** | **15.8 days** | **41.6 days** |

✅ **Best Model: XGBoost (Tuned)**
- 64% MAE reduction over baseline (43.7 → 15.8 days)
- CV R² within 0.001 of test R² — no overfitting
- MAE = 1.7% of full shelf-life range (1–946 days)

**Best Hyperparameters (XGBoost):**
```
n_estimators:     400
max_depth:        6
learning_rate:    0.05
subsample:        0.7
colsample_bytree: 0.9
```

**Best Hyperparameters (Random Forest):**
```
n_estimators:    200
max_depth:       20
min_samples_leaf: 2
```

---

## 4. Feature Importance (XGBoost)

| Feature | Importance | Interpretation |
|---|---|---|
| Water_Activity | 39.7% | Primary microbial growth driver |
| Low_Moisture (engineered) | 22.3% | Moisture < 15% strongly inhibits spoilage |
| Protein_Content_pct | 10.2% | Proteolysis rate indicator |
| pH | 4.9% | Continuous acidity signal |
| Is_Acidic (engineered) | 4.6% | pH < 4.6 kills most pathogens |
| Moisture_Content_pct | 3.6% | Raw moisture level |
| Relative_Humidity_pct | 3.1% | Environmental moisture stress |

Top 2 features alone explain ~62% of model decisions — consistent with food-science literature.

---

## 5. Real-World Predictions

### Milk — Effect of Storage Temperature

| Scenario | Predicted Shelf Life | vs 8-day label |
|---|---|---|
| Ideal refrigerated (4°C, Vacuum Sealed) | 6.0 days | −25% |
| Light abuse (8°C, Standard Plastic) | 3.2 days | −60% |
| Room-temp abuse (12°C, Standard Plastic) | 2.1 days | −74% |
| Unrefrigerated (20°C, Standard Plastic) | 2.1 days | −74% |

> Model predictions are conservative vs label — safer behaviour for food-safety applications.

### Multi-Food Predictions

| Product | Conditions | Predicted Shelf Life |
|---|---|---|
| Milk | Refrigerated, Vacuum Sealed | 6.0 days |
| Milk | Abused at 12°C | 2.1 days |
| Beef | MAP packaged, cold storage | 3.5 days |
| Chicken | Warm, no preservative | 0.9 days |
| Chips | Room temp, low moisture | 303.8 days |
| Strawberries | Chilled, acidic | 6.7 days |
| Ketchup | Acidic, glass jar | 223.2 days |

---

## 6. Business Impact

### Waste Reduction Analysis (693 test samples)

| Outcome | Count | % |
|---|---|---|
| Accurate predictions (within ±5 days) | 463 | **66.8%** |
| Predicted too short — conservative (early disposal) | 139 | 20.1% |
| Predicted too long — overestimate (safety risk) | 91 | 13.1% |

Model is more likely to under-predict than over-predict — the safer failure mode in food safety.

### Temperature Compliance — Salmon Cold-Chain

| Storage Temperature | Predicted Shelf Life |
|---|---|
| 0°C (optimal) | 4.0 days |
| 4°C | 2.5 days |
| 8°C | 1.2 days |

> Most critical degradation occurs between 4°C–8°C. Model can feed IoT sensors to trigger automatic alerts when predicted shelf life drops below a threshold.
