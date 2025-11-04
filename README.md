# Optimización de Eficiencia Aerodinámica (multi‑criterio)

Este repositorio contiene un notebook que optimiza el ángulo de ataque de un alerón de competición por circuito. La metodología actual minimiza una función objetivo multi‑criterio dependiente de pista y resuelve el óptimo con métodos de raíz con bracket (Bisección/Regla Falsa), respetando cotas físicas y el rango observado de datos.

## Metodología (resumen)
- Modelado: regresión polinomial (grado efectivo ≤ 4) de CL(α) y CD(α).
- Objetivo por pista:
  - J(α) = −wE·E(α) + wDF·(DF(Vref) − DFtarget)² + wDR·DR(Vhigh)
  - E(α) = |CL|/CD; DF(V) = ½·ρ·V²·S·|CL|; DR(V) = ½·ρ·V²·S·CD
- Optimización: búsqueda de raíces con bracket sobre dJ/dα en un intervalo físico y observado; si no hay raíz interior, se evalúan extremos.

## Estructura
- optimizacion.ipynb → Notebook principal (modelado, objetivo y optimización por pista).
- datos_entorno_circuitos.csv → Densidad del aire por circuito (ρ) para calcular DF/DR absolutos.
- data/ (opcional) → CSVs de simulación (ver formato abajo).
- results/ (opcional) → salidas (tablas/figuras).

## Requisitos
```bash
pip install -r requirements.txt
```

Mínimo: numpy, pandas, matplotlib. No se requiere scipy; Bisección y Regla Falsa están implementadas en el notebook.

## Datos de entrada
- Simulación (recomendado colocar en data/):
  - Columnas: track, angle_deg, CL, CD
  - Ej.: Monza, 12.0, -0.65, 0.030
- Entorno (raíz): datos_entorno_circuitos.csv
  - Columnas: track, air_density_kg_per_m3
  - Si no hay coincidencia para una pista, se usa una ρ por defecto en el notebook.

## Uso
1) Clona el repo y crea el entorno:
```bash
git clone https://github.com/Brandon-Caceres/Metodos-Numericos.git
cd Metodos-Numericos
pip install -r requirements.txt
```
2) Abre y ejecuta optimizacion.ipynb (Jupyter o Colab).
3) Carga tu CSV de simulación (ver formato) y ejecuta las celdas. El notebook:
- Ajusta CL(α) y CD(α), reporta RMSE.
- Minimiza J(α) por pista con bracket en dJ/dα.
- Muestra αopt, E(αopt), DF(Vref), ΔDF, DR(Vhigh), J y el desglose de términos.

## Resultados esperados (resumen)
- Tabla principal: Track | α_opt [°] | E | DF@Vref [kN] | ΔDF@Vref [kN] | DR@Vhigh [kN] | J
- Tabla de términos: −wE·E | +wDF·(ΔDF)² | +wDR·DR
La suma de términos coincide con J (hasta redondeo). Fuerzas en kN para facilitar lectura.

## Notas de calibración
- Para “bajar” αopt (menos drag): aumenta wDR o baja DFtarget de la pista.
- Para “subir” αopt (más carga): aumenta wDF o sube DFtarget.
- Si el óptimo cae en la frontera, revisa pesos/objetivo o el dominio de datos (J puede ser monótona).

## Citas útiles para ρ y clima
- U.S. Standard Atmosphere, 1976 — [NASA NTRS](https://ntrs.nasa.gov/citations/19770009539)
- Meteostat (meteorología histórica) — [dev.meteostat.net](https://dev.meteostat.net/)
- ERA5 (reanálisis) — [Copernicus CDS](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)

## Autores
Brandon Cáceres · Joaquín Contreras · Josue Huaiquil · Ignacio Muñoz
