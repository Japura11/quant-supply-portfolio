# Quant-Supply Portfolio

**Framework:** Quant-Supply Framework v4  
**Autor:** —  
**Descripción:** Portafolio de notebooks que implementa cada modelo del Quant-Supply Framework: dos escenarios por modelo — Supply Chain (aplicación) y Origin (campo de origen donde el modelo brilla).

---

## Estructura del repositorio

```
quant-supply-portfolio/
│
├── 01_RSI_MACD_Demand/          ← RSI, MACD, Bollinger Bands
├── 02_Volume_Demand_Profile/    ← Point of Control, Value Area 
├── 03_HMM_Regime_Switching/     ← Hidden Markov Models 
├── 04_GARCH_Volatility/         ← GARCH, EGARCH, GJR-GARCH 
├── 05_Realized_Volatility_HAR/  ← RV, HAR-RV 
├── 06_Hurst_MeanReversion/      ← Hurst Exponent, ADF (próximo)
├── 07_Quantile_EVT/             ← Quantile Regression, GEV, GPD (próximo)
├── 08_Cointegration_Spread/     ← Engle-Granger, VECM (próximo)
├── 09_Factor_Models_PCA/        ← PCA, Factor Analysis (próximo)
├── 10_Backtesting_Framework/    ← Sharpe, Drawdown, Walk-Forward (próximo)
│
└── README.md                    ← este archivo
```

---

## Estructura de cada carpeta

```
XX_Nombre_Modelo/
├── supply_scenario.ipynb   ← aplicación en Supply Chain con datos reales
├── origin_scenario.ipynb   ← modelo en su campo de origen
├── README.md               ← teoría, fórmulas, dataset, referencias
└── data/                   ← outputs generados al ejecutar los notebooks
```

---

## Convención de notebooks

| Sección | Contenido |
|---------|-----------|
| **Header Markdown** | Nombre, módulo del framework, dataset, fuente, reproducibilidad |
| **Marco teórico** | Fórmulas clave en LaTeX + interpretación dual (origen / Supply) |
| **Referencias** | Papers originales y literatura de Supply Chain |
| **0. Imports** | Librerías + paleta de colores consistente |
| **1. Datos** | Carga real (con instrucciones) + simulación reproducible |
| **2. Modelo** | Implementación manual con docstrings |
| **3. Visualización** | Dashboard multi-panel estandarizado |
| **4. Análisis** | Métricas de negocio + interpretación |
| **5+. Extras** | Backtesting, política, comparación, exportación |
| **Conclusiones** | Tabla comparativa origen vs Supply + limitaciones |

---

## Datasets públicos utilizados

| Modelo | Dataset Supply | Dataset Origin |
|--------|----------------|----------------|
| RSI/MACD | Walmart M5 Competition (Kaggle) | S&P 500 / SPY ETF (Yahoo Finance) |
| Volume Profile | (próximo) | CME Group futures data |
| HMM | (próximo) | MSCI regímenes de mercado |
| GARCH | (próximo) | EUR/USD tipo de cambio |
| HAR-RV | (próximo) | Oxford-Man Realized Library |
| Hurst | (próximo) | Commodities (FRED) |
| EVT / Quantile | (próximo) | VaR financiero |
| Cointegración | (próximo) | Pairs trading (acciones) |
| Factor Models | (próximo) | Fama-French 3 factors |
| Backtesting | (próximo) | Portfolio completo |

---

## Instalación

```bash
git clone https://github.com/tu-usuario/quant-supply-portfolio.git
cd quant-supply-portfolio

pip install numpy pandas matplotlib seaborn scipy statsmodels
pip install scikit-learn arch statsforecast
# Opcional para datos financieros reales:
pip install yfinance
```

---

## Framework de referencia

Este portafolio implementa el **Quant-Supply Framework v4** — metodología de 5 fases que integra técnicas de Trading Cuantitativo, Banca, Telecomunicaciones, Six Sigma y ML/DL aplicadas a Supply Chain. Ver `quant_supply_v4.docx` para el marco completo.

---

## Progreso

- [x] **01** · RSI / MACD / Bollinger Bands
- [x] **02** · Volume & Demand Profile (Point of Control)
- [x] **03** · HMM / Regime Switching
- [x] **04** · GARCH / EGARCH / Volatilidad condicional
- [x] **05** · Realized Volatility + HAR-RV
- [ ] **06** · Hurst Exponent + Mean-Reversion
- [ ] **07** · Quantile Regression + EVT
- [ ] **08** · Cointegración + Spread Analysis
- [ ] **09** · Factor Models + PCA
- [ ] **10** · Backtesting Framework completo
