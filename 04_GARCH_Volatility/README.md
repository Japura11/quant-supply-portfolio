# 04 · GARCH / Volatilidad Condicional

**Módulo del framework:** Econometría financiera — Volatilidad Condicional  
**Campo de origen:** Engle (1982) ARCH · Bollerslev (1986) GARCH · Nobel Economía 2003  
**Aplicación Supply Chain:** Safety Stock dinámico — σ_t sube en campaña, baja en temporada normal

---

## Estructura

```
04_GARCH_Volatility/
├── 1_Finance_Concept.ipynb     ← GARCH/EGARCH sobre tipo de cambio PEN/USD (BCRP)
├── 2_Supply_Adaptation.ipynb   ← GARCH sobre residuos de demanda Alicorp Aceites
├── README.md
└── data/                       ← generado al ejecutar los notebooks
    ├── finance_eda.png
    ├── finance_dashboard.png
    ├── finance_garch_output.csv
    ├── finance_vol_forecast.csv
    ├── supply_eda.png
    ├── supply_dashboard.png
    └── supply_garch_output.csv
```

---

## Dataset

| Notebook | Dataset | Fuente | Acceso |
|----------|---------|--------|--------|
| Finance | Tipo de cambio PEN/USD (serie PD04637PD) | BCRP | API pública sin autenticación |
| Supply | Demanda semanal Aceites & Grasas Alicorp | Simulado calibrado con BCRP | Incluido en notebook |

### API BCRP — acceso directo

```python
import urllib.request, json

serie = 'PD04637PD'  # TC venta PEN/USD diario
url   = f'https://estadisticas.bcrp.gob.pe/estadisticas/series/api/{serie}/json/2018-1/2024-12/ing'

with urllib.request.urlopen(url) as r:
    data = json.loads(r.read())

records = data['periods']
# Otras series útiles del BCRP:
#   PD04638PD — TC compra PEN/USD
#   PN01210PM — IPC mensual Lima
#   PN01288PM — IPC alimentos
#   PD04649XD — Índice de volatilidad cambiaria
```

En el notebook: la celda de datos intenta la API automáticamente. Si no hay internet, usa la simulación calibrada.

---

## Modelo

### GARCH(1,1) — Bollerslev (1986)

$$\sigma_t^2 = \omega + \alpha \cdot \epsilon_{t-1}^2 + \beta \cdot \sigma_{t-1}^2 \qquad \alpha + \beta < 1$$

$$\bar{\sigma}^2 = \frac{\omega}{1-\alpha-\beta} \qquad \sigma_{t+h|t}^2 = \bar{\sigma}^2 + (\alpha+\beta)^{h-1}(\sigma_{t+1}^2 - \bar{\sigma}^2)$$

### EGARCH — Nelson (1991)

$$\ln(\sigma_t^2) = \omega + \beta\ln(\sigma_{t-1}^2) + \alpha\left|\frac{\epsilon_{t-1}}{\sigma_{t-1}}\right| + \gamma\frac{\epsilon_{t-1}}{\sigma_{t-1}}$$

$\gamma < 0$ en FX: depreciaciones más volátiles que apreciaciones.  
$\gamma > 0$ en Supply: picos de campaña más disruptivos que caídas.

### Safety Stock dinámico

$$SS_t = z_{SL} \cdot \sigma_t \cdot \sqrt{L}$$

El GARCH no modela la demanda — modela la **varianza de los residuos del forecast**.

---

## Cuándo usar

| Usar cuando... | No usar cuando... |
|---------------|------------------|
| Test ARCH p < 0.05 sobre residuos | Test ARCH no significativo |
| Clusters de variabilidad visibles en serie | Varianza aproximadamente constante |
| Campañas o eventos generan shocks temporales | Serie demasiado corta (< 104 obs.) |
| SS fijo genera stockouts en campaña y overstock en baja | Un solo régimen sin heteroscedasticidad |

---

## Requisitos

```bash
pip install numpy pandas matplotlib scipy
```

No requiere `arch` ni `statsmodels` — estimación MLE implementada desde cero.

---

## Conexión con otros modelos

- **Entrada:** HMM (T3) estima σ fija por régimen → GARCH modela la varianza **dentro** de cada régimen
- **Salida:** Si residuos no son gaussianos → **HAR-RV (T5)** — varianza realizada no paramétrica
- **Salida:** Si colas muy pesadas → **EVT / Quantile Regression (T7)**

---

## Referencias

- Engle, R.F. (1982). Autoregressive conditional heteroscedasticity. *Econometrica*, 50(4), 987–1007.
- Bollerslev, T. (1986). Generalized autoregressive conditional heteroskedasticity. *Journal of Econometrics*, 31(3), 307–327.
- Nelson, D.B. (1991). Conditional heteroskedasticity in asset returns. *Econometrica*, 59(2), 347–370.
- BCRP (2024). Estadísticas — Series de tiempo. https://estadisticas.bcrp.gob.pe
