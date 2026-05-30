# 10 · Walk-Forward Backtesting Framework

**Módulo del framework:** Cierre del loop — evaluación objetiva de todas las políticas  
**Campo de origen:** Quant Trading — evaluación de estrategias sistemáticas (Sharpe 1966, Lo 2002)  
**Aplicación Supply Chain:** Tratar cada política de inventario como una estrategia de trading y evaluarla con métricas financieras que el CFO entiende

---

## Estructura

```
10_Backtesting_Framework/
├── 1_Finance_Concept.ipynb     ← Backtesting S&P 500: BH vs. Momentum vs. Mean-Rev vs. Regime
├── 2_Supply_Adaptation.ipynb   ← Backtesting políticas SS Alicorp sobre datos Minagri
├── README.md
└── data/
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_tearsheet.csv
    ├── finance_strategy_rets.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_minagri_data.csv
    ├── supply_tearsheet.csv
    └── supply_pnl_policies.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | S&P 500 retornos diarios (simulado fBm) | Simulado | Notebook |
| Supply | Precios mayoristas aceite Lima + demanda Alicorp | Minagri SISAP (simulado calibrado) | https://sistemas.minagri.gob.pe/sisap |

---

## Métricas

### P&L de la política

$$PnL_t^A = [h(I_t^{base}-D_t)^+ + p(D_t-I_t^{base})^+] - [h(I_t^A-D_t)^+ + p(D_t-I_t^A)^+]$$

### Tearsheet completo

| Métrica | Fórmula | Umbral |
|---------|---------|--------|
| **Sharpe** | $\bar{PnL}/\sigma_{PnL} \cdot \sqrt{52}$ | > 1.0 |
| **Sortino** | $\bar{PnL}/\sigma_{down} \cdot \sqrt{52}$ | > Sharpe |
| **Max Drawdown** | $\min_t(PnL_{cum,t} - \max_{s\leq t}PnL_{cum,s})$ | < 30% ahorro anual |
| **Calmar** | $PnL_{anual} / |MDD|$ | > 1.0 |
| **Hit Rate** | $\#\{PnL_t>0\}/T$ | > 55% |

### Walk-Forward correcto

```
Train[1:t-1] → estimar parámetros → predecir SS_t → medir PnL_t
```

Re-estimar **todos** los parámetros en cada paso — sin lookahead bias.

### Monte Carlo permutation test

```python
null_dist = [sharpe(np.random.permutation(pnl)) for _ in range(5000)]
pval = (null_dist >= obs_sharpe).mean()
# pval < 0.05 → resultado estadísticamente significativo
```

---

## Políticas comparadas

| Política | Modelo | SS calculado como |
|---------|--------|------------------|
| Baseline | Clásico | $z \cdot \sigma_{hist} \cdot \sqrt{L}$ |
| A — GARCH | T4 | $z \cdot \sigma_{GARCH,t} \cdot \sqrt{L}$ |
| B — HMM | T3 | $z \cdot \sigma_{régimen} \cdot \sqrt{L}$ |
| C — QR | T7 | $P_{95}(\epsilon) - \bar{\epsilon}$ (empírico) |
| D — Factor | T9 | $z \cdot \sigma_{hist} \cdot \sqrt{L} \cdot (1+2\sigma_{FX})$ |

---

## El framework completo — integración de los 10 modelos

```
T1  RSI/MACD/Bollinger  →  Señal momentum/reversión
T2  Volume Profile/POC  →  Distribución no paramétrica
T3  HMM Regime          →  Régimen activo en tiempo real
T4  GARCH               →  SS dinámico por varianza condicional
T5  HAR-RV              →  SS forward-looking, memoria larga
T6  Hurst + ARFIMA      →  Corrección SS = z·σ·L^H
T7  QR + EVT            →  SS sin supuesto de normalidad
T8  Cointegración       →  Timing de compra entre insumos
T9  Factor Models/PCA   →  500 SKUs → 3-5 factores
T10 Backtesting         →  Evaluar todo con métricas del CFO ←
```

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy statsmodels
```

No requiere `quantstats` ni `vectorbt` — todo implementado desde cero para transparencia.

---

## Conexión con otros modelos

- **Entrada:** Todos los modelos T1-T9 → cada uno genera una política de SS
- **Salida:** El tearsheet selecciona objetivamente la mejor política (o combinación)
- **Aplicación:** El Sharpe Ratio de la política es el número único para presentar al CFO

---

## Referencias

- Sharpe, W.F. (1966). Mutual fund performance. *JB* 39(1), 119–138.
- Lo, A.W. (2002). The statistics of Sharpe ratios. *FAJ* 58(4), 36–52.
- Bailey, D.H. & López de Prado, M. (2012). The Sharpe ratio efficient frontier. *JPM* 38(3).
- Minagri (2024). SISAP — Precios Mayoristas Lima. Lima: Minagri.
