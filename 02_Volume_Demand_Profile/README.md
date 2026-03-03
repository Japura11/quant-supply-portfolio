# 02 · Volume Profile & Point of Control (POC)

**Módulo del framework:** Trading Arsenal — Market Profile  
**Campo de origen:** CME Group · Futuros sobre índices · Market Makers  
**Aplicación Supply Chain:** Demand Profile por SKU → ROP no paramétrico + SS empírico

---

## Estructura

```
02_Volume_Demand_Profile/
├── 1_Finance_Concept.ipynb     ← Volume Profile en E-mini S&P 500 (ES futures)
├── 2_Supply_Adaptation.ipynb   ← Demand Profile en 3 SKUs Alicorp
├── README.md
└── data/                       ← generado al ejecutar los notebooks
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_signals.csv
    ├── finance_rolling_profiles.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_rolling_poc.png
    ├── supply_demand_data.csv
    └── supply_rolling_poc.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | E-mini S&P 500 (ES) OHLCV diario | Yahoo Finance / Interactive Brokers | `yf.download('ES=F')` |
| Supply | 3 SKUs Alicorp — Cuidado Personal (sim. M5) | Walmart M5 Competition | [kaggle.com/c/m5-forecasting-accuracy](https://www.kaggle.com/c/m5-forecasting-accuracy) |

---

## Modelo

### Volume / Demand Profile
$$ POC = \arg\max_{b} freq(b) \qquad VA = \left\lbrace b : \sum_{b \in VA} freq(b) \ge 0.70 \times n_{total} \right\rbrace $$

| Variable | En trading | En Supply Chain |
|----------|-----------|-----------------|
| Eje Y | Precio del activo | Nivel de demanda (und/sem) |
| Eje X | Volumen transado | Frecuencia de semanas |
| POC | Precio más transado | Nivel de demanda más frecuente |
| VAH | Límite superior 70% vol | Límite superior del rango normal |
| VAL | Límite inferior 70% vol | Límite inferior del rango normal |

### Safety Stock no paramétrico

$$SS_{empírico} = P_{95}(\text{demanda}) - POC$$

No asume distribución Normal — directo del histograma empírico. Más robusto que $z \cdot \sigma \cdot \sqrt{L}$ cuando la distribución tiene cola pesada o es bimodal.

### Regla de cambio estructural

$$\Delta POC > 15\% \implies \text{actualizar ROP}$$

---

## Cuándo usar vs. cuándo no

| Usar cuando... | No usar cuando... |
|---------------|------------------|
| Distribución de demanda no Normal | Historia < 52 semanas |
| Distribución bimodal (dos regímenes) | Demanda con tendencia clara (POC histórico desactualizado) |
| Outliers que inflan σ | Se necesita un forecast — el perfil es descriptivo, no predictivo |
| Definir ROP inicial para SKU nuevo (via analogía familiar) | |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy
```

---

## Conexión con otros modelos del framework

- **Perfil bimodal** → trigger para **HMM (T3)** — detecta el régimen actual automáticamente
- **Cola pesada** → trigger para **EVT / Quantile Regression (T7)**
- **Profile Width alto** → trigger para **GARCH (T4)** — modelar la varianza condicional

---

## Referencias

- Steidlmayer, J.P. & Koy, S. (1986). *Markets & Market Logic*. Chicago: Porcupine Press.
- Dalton, J.F., Jones, E.T. & Dalton, R.B. (1990). *Mind Over Markets*. Probus Publishing.
- Silver, E.A., Pyke, D.F. & Thomas, D.J. (2017). *Inventory Management and Production Planning*, 4th ed.
- Syntetos, A.A. et al. (2016). Supply chain forecasting. *EJOR* 252(1), 1–26.
