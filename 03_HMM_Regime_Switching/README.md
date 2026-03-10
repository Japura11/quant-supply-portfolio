# 03 · HMM / Regime Switching

**Módulo del framework:** Quant Macro — Regime Detection  
**Campo de origen:** Econometría · Hamilton (1989) · Renaissance Technologies  
**Aplicación Supply Chain:** Detección automática de regímenes de demanda → ROP y SS dinámicos por régimen

---

## Estructura

```
03_HMM_Regime_Switching/
├── 1_Finance_Concept.ipynb     ← HMM sobre retornos S&P 500 (Bull/Bear/Alta Vol)
├── 2_Supply_Adaptation.ipynb   ← HMM sobre demanda semanal real UCI Online Retail
├── README.md
└── data/                       ← generado al ejecutar los notebooks
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_hmm_output.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_rop_comparison.png
    └── supply_hmm_output.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | S&P 500 retornos diarios | Yahoo Finance | `yf.download('^GSPC')` |
| Supply | UCI Online Retail — SKU 85123A | UCI ML Repository | [archive.ics.uci.edu/ml/datasets/Online+Retail](https://archive.ics.uci.edu/ml/datasets/Online+Retail) |

### Cargar el dataset real (Supply)

```python
# Opción 1 — descarga directa
import pandas as pd
url = 'https://archive.ics.uci.edu/ml/machine-learning-databases/00352/Online%20Retail.xlsx'
raw = pd.read_excel(url, engine='openpyxl')

# Opción 2 — archivo local
raw = pd.read_excel('data/Online_Retail.xlsx', engine='openpyxl')

# Agregar SKU 85123A a semanas
sku = raw[(raw.StockCode=='85123A') & (raw.Quantity>0) & (raw.Country=='United Kingdom')]
sku['week'] = pd.to_datetime(sku['InvoiceDate']).dt.to_period('W')
weekly = sku.groupby('week')['Quantity'].sum()
```

En el notebook: configurar `USE_REAL_DATA = True` en la celda de carga.

---

## Modelo

### HMM Gaussiano

$$D_t \mid S_t = k \;\sim\; \mathcal{N}(\mu_k,\, \sigma_k^2) \qquad P(S_t=j \mid S_{t-1}=i) = a_{ij}$$

| Algoritmo | Ecuación clave | Uso operacional |
|-----------|---------------|-----------------|
| **Forward** | $\alpha_t(k) = [\sum_i \alpha_{t-1}(i) \cdot a_{ik}] \cdot b_k(D_t)$ | Régimen actual en tiempo real |
| **Viterbi** | $\delta_t(k) = \max_i[\delta_{t-1}(i) \cdot a_{ik}] \cdot b_k(D_t)$ | Etiqueta histórica por semana |
| **Baum-Welch** | EM: $\hat{\mu}_k = \frac{\sum_t \gamma_t(k) D_t}{\sum_t \gamma_t(k)}$ | Aprender parámetros de los datos |

### Selección de K — BIC

$$BIC = -2 \cdot \log P(D \mid \hat{\lambda}) + (K^2 + 3K) \cdot \log T$$

### Política de inventario dinámica

$$ROP_t = \sum_{k=1}^{K} P(S_t=k \mid D_{1:t}) \times (\mu_k \cdot L + z \cdot \sigma_k \cdot \sqrt{L})$$

---

## Cuándo usar

| Usar cuando... | No usar cuando... |
|---------------|------------------|
| Demand Profile (T2) muestra distribución bimodal | Historia < 52 semanas |
| Cambios de régimen sin fechas conocidas | Un solo régimen claro |
| Temporadas asimétricas e inconsistentes | Demanda con tendencia monotónica sin regímenes |
| Eventos no programados (promos, crisis) | Se necesita forecast — HMM clasifica, no predice |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy
# Para datos reales:
pip install openpyxl yfinance
```

---

## Conexión con otros modelos

- **Entrada:** Demand Profile bimodal (T2) → trigger para HMM
- **Salida:** Si σ dentro de cada régimen tiene clusters → **GARCH (T4)**
- **Salida:** Si regímenes tienen memoria larga → **Hurst + ARFIMA (T6)**

---

## Referencias

- Hamilton, J.D. (1989). A new approach to the economic analysis of nonstationary time series. *Econometrica*, 57(2), 357–384.
- Baum, L.E. et al. (1970). A maximization technique in statistical estimation. *Ann. Math. Stat.*, 41(1).
- Chen, F. & Lee, H. (2017). Regime-dependent demand forecasting. *IJPE*, 185, 56–67.
- Dua, D. & Graff, C. (2019). UCI Machine Learning Repository. University of California, Irvine.
