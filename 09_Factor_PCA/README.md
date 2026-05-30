# 09 · Factor Models + PCA

**Módulo del framework:** Quant Macro — Factor Investing aplicado a Supply Chain  
**Campo de origen:** CAPM (Sharpe 1964) → APT (Ross 1976) → Fama-French (1993) → Factor Investing  
**Aplicación Supply Chain:** Modelar 500+ SKUs con 3-5 factores comunes + stress testing factorial con variables BCRP

---

## Estructura

```
09_Factor_PCA/
├── 1_Finance_Concept.ipynb     ← PCA sobre retornos BVL (7 acciones peruanas)
├── 2_Supply_Adaptation.ipynb   ← Factor Model con BCRP (FX, IPC, BVL) sobre 8 SKUs Alicorp
├── README.md
└── data/
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_bvl_returns.csv
    ├── finance_pca_loadings.csv
    ├── finance_factor_scores.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_demand_skus.csv
    ├── supply_bcrp_factors.csv
    ├── supply_pca_loadings.csv
    └── supply_factor_scores.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | BVL — 7 acciones peruanas | Yahoo Finance | `yfinance` |
| Supply | BCRP — TC, IPC, IPC Alimentos, BVL | BCRP API pública | https://estadisticas.bcrp.gob.pe |
| Supply | Demanda 8 SKUs Alicorp | Simulado calibrado | Notebook |

### Descargar datos BCRP

```python
import urllib.request, json

def fetch_bcrp(serie, inicio='2018-1', fin='2024-12'):
    url = (f'https://estadisticas.bcrp.gob.pe/estadisticas/series/api/'
           f'{serie}/json/{inicio}/{fin}/ing')
    with urllib.request.urlopen(url) as r:
        data = json.loads(r.read())
    records = data['periods']
    # procesar...

# Series clave:
# PD04637PD — TC venta PEN/USD (diario)
# PN01210PM — IPC Lima (mensual)
# PN01288PM — IPC Alimentos (mensual)
# PD04649XD — Índice BVL (diario)
```

El notebook intenta la descarga automáticamente — si no hay internet usa simulación calibrada.

---

## Modelo

### Factor Model

$$D_{i,t} = \mu_i + \sum_{k=1}^K b_{ik} f_{k,t} + \epsilon_{i,t}$$

### PCA

$$\hat{\boldsymbol{\Sigma}} = \mathbf{V}\boldsymbol{\Lambda}\mathbf{V}^T \qquad \mathbf{f}_t = \mathbf{V}_K^T \tilde{\mathbf{D}}_t \qquad VE(K) = \frac{\sum_{k=1}^K \lambda_k}{\text{tr}(\hat{\boldsymbol{\Sigma}})}$$

### Stress test

$$\Delta D_i = b_{i,FX} \cdot \Delta FX$$

Si PEN se deprecia 15%: los SKUs con $b_{i,FX}$ negativo grande (aceites importados) caen más.

### Selección de K

| Criterio | Regla |
|----------|-------|
| Varianza acumulada | $VE(K) \geq 80\%$ |
| Codo (Scree) | Punto donde $\lambda_k$ se aplana |
| Kaiser | $\lambda_k > 1$ (datos estandarizados) |

---

## Cuándo usar

| Usar | No usar |
|------|---------|
| Portafolio ≥ 50 SKUs | Pocos SKUs (< 20) |
| Alta correlación entre SKUs (media > 0.5) | SKUs sin correlación |
| Variables macro disponibles (BCRP) | Sin drivers externos identificados |
| Stress testing de escenarios macro | Solo forecasting puntual |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy scikit-learn statsmodels
# Para datos reales BVL:
pip install yfinance
```

---

## Conexión con otros modelos

- **Entrada:** Cointegración (T8) identifica pares relacionados → Factor Model modela la estructura conjunta
- **Salida:** Factor scores → forecast de factores con ARIMA/GARCH → reconstruir SKUs individuales
- **Salida:** Backtesting (T10) — evaluar el factor model como estrategia vs. baseline univariado

---

## Referencias

- Sharpe, W.F. (1964). Capital asset prices. *Journal of Finance* 19(3).
- Ross, S.A. (1976). The arbitrage theory of capital asset pricing. *JET* 13(3).
- Fama, E.F. & French, K.R. (1993). Common risk factors. *JFE* 33(1).
- BCRP (2024). Estadísticas — Series de tiempo. https://estadisticas.bcrp.gob.pe
