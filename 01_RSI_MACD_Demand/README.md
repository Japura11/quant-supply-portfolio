# 01 · RSI / MACD / Bollinger Bands

**Módulo del framework:** Trading Arsenal — Indicadores de Momentum  
**Campo de origen:** High-Frequency Trading / Análisis Técnico Financiero  
**Aplicación Supply Chain:** Detección de momentum de demanda + Safety Stock dinámico

---

## Estructura

```
01_RSI_MACD_Demand/
├── supply_scenario.ipynb     ← RSI/MACD sobre demanda semanal (Walmart M5)
├── origin_scenario.ipynb     ← RSI/MACD sobre precio financiero (S&P 500 / SPY)
├── README.md
└── data/                     ← generado al ejecutar los notebooks
    ├── supply_signals_output.csv
    ├── supply_dashboard.png
    ├── supply_policy.png
    ├── origin_dashboard.png
    └── origin_drawdowns.png
```

---

## Dataset

| Escenario | Dataset | Fuente | Acceso |
|-----------|---------|--------|--------|
| Supply | Walmart M5 Competition — FOODS_3_090_CA_1 | Kaggle M5 Forecasting Accuracy | [kaggle.com/c/m5-forecasting-accuracy](https://www.kaggle.com/c/m5-forecasting-accuracy) |
| Origin | S&P 500 / SPY ETF — precios diarios cierre | Yahoo Finance | `yfinance.download('SPY', start='2019-01-01')` |

> Los notebooks incluyen simulaciones reproducibles con los estadísticos reales de cada dataset para ejecución sin conexión a internet.

---

## Modelo

### RSI — Relative Strength Index (Wilder, 1978)

$$RSI_t = 100 - \frac{100}{1 + \frac{\bar{G}_{14}}{\bar{L}_{14}}}$$

| RSI | Trading | Supply Chain |
|-----|---------|--------------|
| > 70 | Sobrecomprado → probable corrección | Demanda caliente → riesgo STOCKOUT |
| < 30 | Sobrevendido → probable rebote | Demanda fría → riesgo OVERSTOCK |

### MACD (Appel, 1979)

$$MACD = EMA_{12} - EMA_{26} \qquad Signal = EMA_9(MACD)$$

- **Cruce alcista** → aumentar orden de reabastecimiento  
- **Cruce bajista** → reducir próxima orden

### Bollinger Bands (Bollinger, 1983)

$$BB_{\pm} = SMA_{20} \pm 2\sigma_{20}$$

- **Safety Stock dinámico:** $SS_t = z \cdot \sigma_{rolling,t} \cdot \sqrt{L}$  
  Se adapta a la volatilidad real de cada período en vez de usar σ histórico fijo.

---

## Requisitos

```bash
pip install numpy pandas matplotlib seaborn
# Para datos reales (origin_scenario):
pip install yfinance
```

---

## Referencias

- Wilder, J.W. (1978). *New Concepts in Technical Trading Systems*. Trend Research.
- Appel, G. (1979). *The Moving Average Convergence-Divergence Method*.
- Bollinger, J. (2001). *Bollinger on Bollinger Bands*. McGraw-Hill.
- Makridakis, S. et al. (2022). M5 accuracy competition. *International Journal of Forecasting*, 38(4).
- Brock, W., Lakonishok, J., & LeBaron, B. (1992). *Journal of Finance*, 47(5), 1731–1764.
