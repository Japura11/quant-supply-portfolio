# 07 · Quantile Regression + Extreme Value Theory (EVT)

**Módulo del framework:** Riesgo de cola — Modelado no paramétrico de percentiles extremos  
**Campo de origen:** Koenker & Bassett (1978) QR · Gumbel (1935) + Balkema-de Haan-Pickands (1974) EVT  
**Aplicación Supply Chain:** Safety Stock sin supuesto de normalidad — honesto en percentiles > 95%

---

## Estructura

```
07_Quantile_EVT/
├── 1_Finance_Concept.ipynb     ← QR + EVT sobre pérdidas INDECI Perú (huaycos, inundaciones, El Niño)
├── 2_Supply_Adaptation.ipynb   ← QR + EVT sobre errores demanda Alicorp (3 SKUs)
├── README.md
└── data/
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_indeci_losses.csv
    ├── finance_mep.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_demand_errors.csv
    └── supply_evt_summary.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | INDECI SINPAD — pérdidas económicas desastres Perú | INDECI (simulado calibrado) | https://sinpad.indeci.gob.pe/ |
| Supply | Errores forecast demanda Alicorp (3 SKUs) | Simulado calibrado | Notebook |

---

## Modelo

### Quantile Regression — Koenker & Bassett (1978)

$$\hat{\beta}_\tau = \arg\min_\beta \sum_{i=1}^n \rho_\tau(y_i - x_i\beta) \qquad \rho_\tau(u) = u(\tau - \mathbf{1}_{u<0})$$

No asume distribución — estima directamente el percentil $\tau$ condicional.

### Peaks Over Threshold (POT) — GPD

$$P(X-u \leq y \mid X > u) \to H(y;\sigma,\xi) = 1 - \left(1+\frac{\xi y}{\sigma}\right)^{-1/\xi}$$

### Cuantil extremo

$$Q_p = u + \frac{\hat{\sigma}}{\hat{\xi}}\left[\left(\frac{n}{n_u \cdot p}\right)^{\hat{\xi}} - 1\right]$$

### Regla de selección de método

| Kurtosis | Método SS | Cuándo |
|----------|-----------|--------|
| < 3 | Normal: $z\cdot\sigma\cdot\sqrt{L}$ | Distribución simétrica |
| 3–8 | QR empírica | τ < 0.98, historia ≥ 104 sem |
| > 8 | EVT/POT | τ > 0.98, colas muy pesadas |

---

## Cuándo usar

| Usar | No usar |
|------|---------|
| Kurtosis alta (> 3) | Distribución simétrica cerca de Normal |
| Percentiles objetivo > 95% | Historia < 52 semanas |
| Campañas con impacto heterogéneo por cuantil | SKU sin covariables relevantes |
| ξ̂ > 0 en MEP | ξ̂ < 0 (cola acotada) |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy
```

No requiere dependencias especiales — todo implementado desde cero.

---

## Conexión con otros modelos

- **Entrada:** GARCH (T4) estima σ_t → QR usa σ_t como covariable
- **Entrada:** Hurst (T6) confirma colas pesadas → EVT las modela
- **Salida:** Cointegración (T8) — cuando múltiples SKUs con colas pesadas tienen demandas cointegradas, optimizar SS conjuntamente

---

## Referencias

- Koenker, R. & Bassett, G. (1978). Regression quantiles. *Econometrica* 46(1), 33–50.
- Coles, S. (2001). *An Introduction to Statistical Modeling of Extreme Values*. Springer.
- Balkema, A. & de Haan, L. (1974). Residual life time at great age. *Ann. Prob.* 2(5).
- INDECI (2024). SINPAD — Sistema Nacional de Información para la Gestión del Riesgo. Lima.
