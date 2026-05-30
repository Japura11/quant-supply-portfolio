# Quant-Supply Portfolio

**Framework:** Quant-Supply Framework v4  
**Autor:** Richard Alburquerque — [@Japura11](https://github.com/Japura11)  
**Descripción:** Portafolio de notebooks que aplica modelos cuantitativos de finanzas a Supply Chain. Dos notebooks por modelo: origen financiero + adaptación a Supply Chain con datos peruanos.

> Los notebooks se visualizan en **nbviewer** (GitHub no renderiza `.ipynb` directamente).

| # | Modelo | Finance Concept | Supply Adaptation |
|---|--------|----------------|-------------------|
| 01 | RSI / MACD | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/01_RSI_MACD_Demand/1_Finance_Concept.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/01_RSI_MACD_Demand/2_Supply_Adaptation.ipynb?flush_cache=true) |
| 02 | Volume & Demand Profile | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/02_Volume_Demand_Profile/1_Finance_Concept_VDP.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/02_Volume_Demand_Profile/2_Supply_Adaptation_VDP.ipynb?flush_cache=true) |
| 03 | HMM Regime Switching | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/03_HMM_Regime_Switching/1_Finance_Concept_HMM.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/03_HMM_Regime_Switching/2_Supply_Adaptation_HMM.ipynb?flush_cache=true) |
| 04 | GARCH Volatility | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/04_GARCH_Volatility/1_Finance_Concept_GARCH_Volatily.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/04_GARCH_Volatility/2_Supply_Adaptation_GARCH_Volatily.ipynb?flush_cache=true) |
| 05 | HAR Realized Volatility | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/05_HAR_RV/1_Finance_Concept_HAR_RV.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/05_HAR_RV/2_Supply_Adaptation_HAR_RV.ipynb?flush_cache=true) |
| 06 | Hurst Exponent | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/06_Hurst_Exponent/1_Finance_Concept_Hurst_Exponent.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/06_Hurst_Exponent/2_Supply_Adaptation_Hurst_Exponent.ipynb?flush_cache=true) |
| 07 | Quantile / EVT | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/07_Quantile_EVT/1_Finance_Concept_EVT.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/07_Quantile_EVT/2_Supply_Adaptation_EVT.ipynb?flush_cache=true) |
| 08 | Cointegration Spread | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/08_Cointegration_Spread/1_Finance_Concept_Cointegration_Spread.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/08_Cointegration_Spread/2_Supply_Adaptation_Cointegration_Spread.ipynb?flush_cache=true) |
| 09 | Factor Models / PCA | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/09_Factor_PCA/1_Finance_PCA.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/09_Factor_PCA/2_Supply_PCA.ipynb?flush_cache=true) |
| 10 | Backtesting Framework | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/10_Backtesting_Framework/1_Finance_Backtesting.ipynb?flush_cache=true) | [ver](https://nbviewer.org/github/Japura11/quant-supply-portfolio/blob/main/10_Backtesting_Framework/2_Supply_Backtesting.ipynb?flush_cache=true) |

---

## Estructura del repositorio

```
quant-supply-portfolio/
├── 01_RSI_MACD_Demand/
├── 02_Volume_Demand_Profile/
├── 03_HMM_Regime_Switching/
├── 04_GARCH_Volatility/
├── 05_HAR_RV/
├── 06_Hurst_Exponent/
├── 07_Quantile_EVT/
├── 08_Cointegration_Spread/
├── 09_Factor_PCA/
└── 10_Backtesting_Framework/
```

Cada carpeta contiene:
```
XX_Nombre_Modelo/
├── 1_Finance_Concept.ipynb   ← modelo en su campo financiero de origen
├── 2_Supply_Adaptation.ipynb ← misma lógica aplicada a Supply Chain (datos peruanos)
├── README.md                 ← teoría, fórmulas, dataset, referencias
└── data/                     ← outputs: PNGs y CSVs generados
```

---

## Metodología

Cada modelo sigue el mismo flujo:
1. **Teoría** — origen histórico, intuición, desarrollo matemático
2. **Finance Concept** — dataset financiero real o simulado calibrado
3. **Supply Adaptation** — dataset peruano público (BCRP, INEI, BVL, Minagri)
4. **Dashboard** — 4-5 paneles estandarizados
5. **Conclusiones** — conexión al siguiente modelo

---

## Instalación

```bash
git clone https://github.com/Japura11/quant-supply-portfolio.git
cd quant-supply-portfolio
pip install numpy pandas matplotlib scipy statsmodels scikit-learn arch yfinance
```
