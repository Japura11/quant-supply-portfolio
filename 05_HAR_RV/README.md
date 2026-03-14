# 05 · HAR-RV / Volatilidad Realizada

**Módulo del framework:** Econometría de alta frecuencia — Heterogeneous Autoregressive RV  
**Campo de origen:** Andersen & Bollerslev (1998) · Corsi (2003)  
**Aplicación Supply Chain:** Safety Stock dinámico con memoria larga — sube ante volatilidad IPC sostenida

---

## Estructura

```
05_HAR_RV/
├── 1_Finance_Concept.ipynb     ← HAR-RV sobre S&P 500 volatilidad realizada
├── 2_Supply_Adaptation.ipynb   ← HAR-RV sobre IPC Alimentos INEI Lima Metropolitana
├── README.md
└── data/                       ← generado al ejecutar los notebooks
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_har_features.csv
    ├── finance_har_forecast.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    └── supply_har_output.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | S&P 500 RV diaria | Oxford-Man RV Library | https://realized.oxford-man.ox.ac.uk/data/download |
| Supply | IPC Alimentos Lima Metropolitana | INEI Perú | https://www.inei.gob.pe/estadisticas/indice-tematico/price-indexes/ |

### Cargar datos INEI reales

```python
import pandas as pd

# Descarga directa
url = 'https://www.inei.gob.pe/media/MenuRecursivo/indices_tematicos/cuadro_001_ipc.xlsx'
df = pd.read_excel(url, skiprows=3, engine='openpyxl')

# Portal alternativo (JSON):
# https://api.apis.net.pe/v2/sunat/ipc
```

En el notebook: configurar `USE_REAL = True` si tienes internet y `openpyxl`.

### Oxford-Man RV Library (Finance)

```python
import pandas as pd
url = 'https://realized.oxford-man.ox.ac.uk/images/oxfordmanrealizedvolatilityindices.zip'
# Columna de interés: rv5 (RV con retornos cada 5 min)
# Ticker S&P 500: .SPX
```

---

## Modelo

### Volatilidad Realizada

$$RV_t = \sum_{j=1}^{M} r_{t,j}^2 \quad \text{(finanzas)} \qquad RV_t^{(d)} = \epsilon_t^2 \quad \text{(supply, proxy)}$$

### HAR-RV — Corsi (2003)

$$\log(RV_{t+1}) = \beta_0 + \beta_d\log(RV_t^{(d)}) + \beta_w\log(RV_t^{(w)}) + \beta_m\log(RV_t^{(m)}) + \epsilon$$

| Escala | Finanzas | Supply Chain |
|--------|---------|-------------|
| Diaria ($d$) | 1 día | 1 semana |
| Semanal ($w$) | 5 días | 4 semanas |
| Mensual ($m$) | 22 días | 13 semanas |

Estimado por **OLS** — sin optimización numérica.

### Safety Stock forward-looking

$$SS_t = z \cdot \sqrt{\widehat{RV}_{t+L}} \cdot \sqrt{L}$$

Usa el forecast de RV para el período de lead time — no la varianza histórica.

---

## GARCH vs. HAR — cuándo usar cada uno

| Criterio | GARCH | HAR-RV |
|----------|-------|--------|
| ACF(RV²) decae rápido (< 5 rezagos) | ✓ preferido | — |
| ACF(RV²) decae lento (> 10 rezagos) | — | ✓ preferido |
| Datos intraperiodo disponibles | No necesario | Mejora la RV |
| Interpretabilidad coeficientes | Media | Alta |
| Complejidad de estimación | MLE no lineal | **OLS** |
| R² OOS típico | 0.45–0.55 | **0.65–0.75** |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy statsmodels
# Para datos reales:
pip install openpyxl requests
```

---

## Conexión con otros modelos

- **Entrada:** GARCH (T4) modela varianza paramétrica → HAR-RV la mide sin supuestos
- **Diagnóstico:** ACF lenta de RV → confirma long memory → justifica HAR
- **Salida:** Si H > 0.5 (Hurst) → memoria larga confirmada → **T6 Hurst / ARFIMA**
- **Salida:** Si distribución de RV tiene colas muy pesadas → **T7 EVT / Quantile Regression**

---

## Referencias

- Andersen, T.G. & Bollerslev, T. (1998). Answering the skeptics. *Journal of Finance* 53(1).
- Corsi, F. (2009). A simple approximate long-memory model of realized volatility. *JFEC* 7(2), 174–196.
- INEI (2024). Índice de Precios al Consumidor de Lima Metropolitana. Lima: INEI.
- Oxford-Man Institute (2024). Realized Library. University of Oxford.
