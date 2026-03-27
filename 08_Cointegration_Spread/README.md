# 08 · Cointegración + Spread Analysis

**Módulo del framework:** Econometría — Long-Run Equilibrium  
**Campo de origen:** Engle & Granger (1987) → Pairs Trading → Stat Arbitrage  
**Aplicación Supply Chain:** Señal de timing de compra entre commodities relacionados + gestión conjunta de inventario de categorías cointegradas

---

## Estructura

```
08_Cointegration_Spread/
├── 1_Finance_Concept.ipynb     ← Pairs Trading BVL: ALICORC1 + BACKUSI1
├── 2_Supply_Adaptation.ipynb   ← Cointegración precios Minagri: Soya/Trigo + Arroz/Maíz
├── README.md
└── data/
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_pairs_output.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_minagri_prices.csv
    └── supply_spread_signals.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | ALICORC1.LM + BACKUSI1.LM BVL | Yahoo Finance | `yfinance` |
| Supply | Precios mayoristas Lima (soya, trigo, arroz, maíz) | Minagri SISAP | https://sistemas.minagri.gob.pe/sisap/portal2/mayorista/ |

### Descargar datos reales

```python
# Finance — BVL
import yfinance as yf
ali = yf.download('ALICORC1.LM', start='2015-01-01', auto_adjust=True)['Close']
bac = yf.download('BACKUSI1.LM', start='2015-01-01', auto_adjust=True)['Close']

# Supply — Minagri SISAP
# Acceso manual: portal SISAP → Precios Mayoristas → Lima → descargar Excel
# O via scraping del portal HTML
```

---

## Modelo

### Test de Engle-Granger (2 pasos)

**Paso 1:** $Y_t = \alpha + \beta X_t + \epsilon_t$ (regresión de cointegración OLS)

**Paso 2:** ADF sobre $\hat{\epsilon}_t$ — si rechaza raíz unitaria → cointegrados

### Spread y Z-score

$$Z_t = Y_t - \hat{\alpha} - \hat{\beta} X_t \qquad z_t = \frac{Z_t - \mu_{Z,roll}}{\sigma_{Z,roll}}$$

### Señales

| Señal | Condición | Acción Supply |
|-------|-----------|---------------|
| Retrasar compra | $z_t > +1.5\sigma$ | Insumo Y caro vs. X → esperar normalización |
| Adelantar compra | $z_t < -1.5\sigma$ | Insumo Y barato → comprar anticipado |
| Neutral | $|z_t| < 1.5\sigma$ | Precio en equilibrio → compra normal |

### VECM — velocidad de ajuste

$$\Delta Y_t = \alpha_Y(Y_{t-1} - \beta X_{t-1} - c) + \epsilon_t$$

$-1/\alpha_Y$ = días para corregir el desequilibrio de precios.

---

## Cuándo usar

| Usar | No usar |
|------|---------|
| Dos series I(1) que se mueven juntas | Series I(0) ya estacionarias |
| Test de cointegración p < 0.05 | Cointegración no significativa |
| Commodities con factor común (FX, materia prima) | Productos sin relación estructural |
| Gestión conjunta de categorías con canibalización | SKUs sin sustitución |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy statsmodels
# Para datos reales BVL:
pip install yfinance
```

---

## Conexión con otros modelos

- **Entrada:** HMM (T3) detecta regímenes → cointegración puede cambiar de régimen
- **Salida:** Factor Models + PCA (T9) — cuando hay N>2 commodities, PCA extrae los factores comunes subyacentes

---

## Referencias

- Engle, R.F. & Granger, C.W.J. (1987). Co-integration and error correction. *Econometrica* 55(2), 251–276.
- Johansen, S. (1991). Estimation and hypothesis testing of cointegration vectors. *Econometrica* 59(6).
- Vidyamurthy, G. (2004). *Pairs Trading: Quantitative Methods and Analysis*. Wiley.
- Minagri (2024). SISAP — Sistema de Información de Abastecimiento y Precios. Lima.
