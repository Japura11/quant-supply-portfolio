# 06 · Hurst Exponent

**Módulo del framework:** Análisis fractal — Long Memory  
**Campo de origen:** Hidrología (Hurst 1951) → Fractales financieros (Mandelbrot 1971) → Quant trading  
**Aplicación Supply Chain:** Corrección de Safety Stock — $SS_{Hurst} = z\cdot\sigma\cdot L^H$

---

## Estructura

```
06_Hurst_Exponent/
├── 1_Finance_Concept.ipynb     ← R/S Analysis + DFA sobre S&P 500 (fBm simulado)
├── 2_Supply_Adaptation.ipynb   ← Hurst sobre ALICORC1 (BVL) + errores demanda Alicorp
├── README.md
└── data/
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_hurst_output.csv
    ├── finance_hurst_rolling.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    ├── supply_alicorc1.csv
    └── supply_demand_errors.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | S&P 500 retornos (fBm H=0.54) | Simulado | Notebook |
| Supply | ALICORC1.LM retornos diarios | Yahoo Finance | `yfinance` |
| Supply | Errores demanda Alicorp | Simulado calibrado | Notebook |

### Descargar ALICORC1

```python
import yfinance as yf
df  = yf.download('ALICORC1.LM', start='2010-01-01', auto_adjust=True)
ret = df['Close'].pct_change().dropna()
```

El notebook intenta la descarga automáticamente — si no hay internet usa simulación calibrada.

---

## Modelo

### R/S Analysis

$$\frac{R(n)}{S(n)} \approx c \cdot n^H \implies H = \text{slope}\{\log(R/S) \sim \log(n)\}$$

### DFA

$$F(n) \propto n^H \implies H = \text{slope}\{\log F(n) \sim \log(n)\}$$

### Corrección de SS

$$SS_{Hurst} = z \cdot \sigma \cdot L^H \qquad \text{Ratio} = L^{H-0.5}$$

| $H$ | Ratio $L=4$ | Implicación |
|-----|------------|-------------|
| 0.50 | 1.00 | SS clásico válido |
| 0.60 | 1.15 | +15% más SS |
| 0.70 | 1.32 | +32% más SS |
| 0.80 | 1.52 | +52% más SS |

### Conexión ARFIMA

$$d = H - 0.5 \implies \text{ARFIMA}(p, d, q)$$

---

## Cuándo usar

| Usar | No usar |
|------|---------|
| ACF(errores²) lenta (> 20 lags sig.) | Serie < 100 obs. |
| SS clásico genera stockouts sistemáticos | H no sig. diferente de 0.5 |
| HAR-RV (T5) ya confirmó long memory | Cambios estructurales no modelados |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy statsmodels yfinance
```

---

## Conexión con otros modelos

- **Entrada:** HAR-RV (T5) confirmó long memory → Hurst la cuantifica
- **Salida:** $d = H-0.5$ → parámetro ARFIMA
- **Salida:** $H > 0.5$ + colas pesadas → **T7 Quantile Regression + EVT**

---

## Referencias

- Hurst (1951). *Trans. Am. Soc. Civil Eng.* 116.
- Mandelbrot & Van Ness (1968). *SIAM Review* 10(4).
- Peng et al. (1994). *Physical Review E* 49(2).
- Peters (1994). *Fractal Market Analysis*. Wiley.
