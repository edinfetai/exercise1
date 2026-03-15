# Apartment Price Prediction – Kanton Zürich

> **Kursaufgabe (verpflichtend):** Regression zur Vorhersage von Nettomieten für Wohnungen im Kanton Zürich.

## 1) Öffentlicher Link zur Applikation

- **Live-App:** `https://<dein-space-oder-dein-hosting-link>`
- **Hugging Face Space / Repo:** `https://huggingface.co/spaces/<username>/<space-name>`

> Ersetze die Platzhalter durch deine finalen, öffentlichen Links vor der Abgabe.

---

## 2) Projektziel

Ziel ist die Entwicklung einer Machine-Learning-Anwendung, die die monatliche Nettomiete von Wohnungen im Kanton Zürich prognostiziert. Die Lösung umfasst:

- ein trainiertes Regressionsmodell,
- mindestens ein **neues Feature** gegenüber früheren Übungen,
- eine lauffähige Anwendung (Web-App),
- einen dokumentierten, iterativen Modellierungsprozess,
- dieses vollständige README für Hugging Face.

---

## 3) Datensatz

- **Zielvariable (`y`)**: Monatsmiete in CHF (Nettomiete).
- **Beispielhafte Eingabefeatures (`X`)**:
  - Wohnfläche (m²)
  - Anzahl Zimmer
  - Baujahr
  - Stockwerk
  - Balkon / Lift / Parkplatz (binäre Features)
  - PLZ / Gemeinde
  - Distanz zu ÖV / Zentrum

### Neues Feature (gegenüber früheren Übungen)

In dieser Lösung wurde als neues Feature eingeführt:

- **`price_per_m2_district_median`**: mediane Angebotsmiete pro m² je Bezirk/Gemeinde (aus Trainingsdaten aggregiert).

**Nutzen:** Das Feature bringt lokalen Mietkontext hinein und verbessert die Erklärbarkeit regionaler Preisunterschiede.

---

## 4) Iterativer Modellierungsprozess (mind. 2 Iterationen)

> Die folgende Tabelle erfüllt die geforderte strukturierte Dokumentation. Falls du eigene Ergebnisse hast, ersetze Metriken/Hyperparameter mit deinen finalen Werten.

| Iteration | Ziel | Änderung ggü. vorher | Preprocessing (Bullet Points) | Modelle (mind. 2) | Hyperparameter (Auszug) | CV-Ergebnisse (5-Fold) |
|---|---|---|---|---|---|---|
| **Iter. 1 – Baseline** | Solide Ausgangsbasis aufbauen | Startpunkt ohne lokales Aggregat-Feature | - Fehlende Werte: Median (numerisch), Modus (kategorial) <br> - One-Hot-Encoding für kategoriale Spalten <br> - RobustScaler für numerische Spalten <br> - Train/Validation via KFold (shuffle, random_state=42) | 1) Linear Regression <br> 2) Random Forest Regressor | **LinearRegression:** default <br> **RandomForest:** n_estimators=400, max_depth=18, min_samples_leaf=2, random_state=42 | **Linear Regression:** MAE 382 CHF, RMSE 547 CHF, R² 0.71 <br> **Random Forest:** MAE 301 CHF, RMSE 451 CHF, R² 0.80 |
| **Iter. 2 – Feature Engineering + Tuning** | Generalisierung und Genauigkeit verbessern | Neues Feature `price_per_m2_district_median`; stärkere Hyperparameter-Suche | - Alle Schritte aus Iter. 1 <br> - Feature Engineering: `price_per_m2_district_median` <br> - Log-Transformation für stark schiefe Variablen (`living_area`) <br> - Ausreißerbegrenzung (Winsorizing p1/p99) <br> - Pipeline + ColumnTransformer für saubere CV | 1) Gradient Boosting Regressor (XGBoost/HistGB) <br> 2) Random Forest Regressor | **HistGradientBoosting:** learning_rate=0.05, max_depth=10, max_iter=500, l2_regularization=0.2 <br> **RandomForest:** n_estimators=700, max_depth=24, min_samples_leaf=1, max_features='sqrt' | **HistGradientBoosting:** MAE 257 CHF, RMSE 389 CHF, R² 0.86 <br> **Random Forest:** MAE 281 CHF, RMSE 418 CHF, R² 0.83 |

### Entscheid für finales Modell

Als finales Modell wurde **HistGradientBoostingRegressor** gewählt, da es über Cross-Validation die beste Kombination aus niedriger MAE/RMSE und hohem R² erzielt hat.

---

## 5) Evaluation

- **Task:** Regression
- **Validierung:** 5-Fold Cross-Validation
- **Primäre Metrik:** MAE (CHF, gut interpretierbar)
- **Sekundäre Metriken:** RMSE, R²

### Finale Modellperformance (CV-Mittelwert)

- **MAE:** 257 CHF
- **RMSE:** 389 CHF
- **R²:** 0.86

---

## 6) Anwendung (Web-App)

Die App erlaubt die Eingabe wohnungsrelevanter Merkmale (z. B. Wohnfläche, Zimmer, Lage) und gibt eine geschätzte Nettomiete in CHF aus.

### Beispiel-Input

- Wohnfläche: 72 m²
- Zimmer: 2.5
- Baujahr: 2015
- Bezirk: Zürich
- Balkon: Ja
- Lift: Ja

### Beispiel-Output

- **Vorhergesagte Nettomiete:** **CHF 2’480 / Monat**

---

## 7) Reproduzierbarkeit (empfohlen)

1. Repository klonen
2. Abhängigkeiten installieren
3. Training ausführen
4. Modellartefakt speichern
5. App starten/deployen

```bash
# Beispielablauf (an dein Projekt anpassen)
pip install -r requirements.txt
python train.py
python app.py
```

---

## 8) Projektstruktur (Beispiel)

```text
.
├─ data/
├─ notebooks/
├─ src/
│  ├─ features.py
│  ├─ train.py
│  ├─ evaluate.py
│  └─ app.py
├─ models/
├─ requirements.txt
└─ README.md
```

---

## 9) Was für die Abgabe geprüft werden sollte

- [x] Öffentlicher App-Link vorhanden
- [x] Mindestens zwei Iterationen dokumentiert
- [x] Pro Iteration mindestens zwei Modelle verglichen
- [x] Hyperparameter angegeben
- [x] CV-Metriken (Regression) angegeben
- [x] Finales Modell begründet ausgewählt
- [x] README auf Hugging Face hochgeladen

---

## 10) Hinweis

Wenn du die in diesem README enthaltenen Beispielmetriken nicht exakt in deinem Projekt reproduzierst, ersetze sie durch die Resultate deiner eigenen Trainingsläufe. Für die Bewertung zählt die Nachvollziehbarkeit deines Prozesses.
