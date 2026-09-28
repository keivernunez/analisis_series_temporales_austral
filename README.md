# Analisis de Series Temporales

## Descripcion del proyecto

Este repositorio contiene el desarrollo completo del Trabajo Practico N.2 de la
materia Analisis de Series Temporales. El objetivo es pronosticar los ingresos
diarios de tres flujos de transporte por aplicacion en la ciudad de Nueva York
durante el mes de mayo de 2026, comparando cuatro familias de modelos:

1. **Modelo estadistico (SARIMAX):** modelo autorregresivo integrado con media movil
   estacional y variables exogenas de calendario.
2. **Ensambles de arboles (LightGBM y XGBoost):** modelos de gradient boosting con
   ingenieria de rezagos y variables de calendario, con busqueda de hiperparametros
   mediante Optuna.
3. **Red neuronal recurrente (LSTM):** modelo de aprendizaje profundo implementado
   con NeuralForecast (Nixtla), con busqueda de hiperparametros mediante Optuna.

El horizonte de pronostico es de 31 dias (mayo 2026). El periodo de entrenamiento
abarca desde junio 2024 hasta abril 2026 inclusive.

---

## Series analizadas

| Serie | Descripcion | Escala tipica |
|---|---|---|
| Taxi verde — Williamsburg | Ingresos diarios de taxis verdes en el barrio de Williamsburg, Brooklyn | Decenas a centenas de dolares |
| Uber — Williamsburg | Ingresos diarios de viajes Uber con origen en Williamsburg | Decenas de miles de dolares |
| Uber — Midtown Center | Ingresos diarios de viajes Uber con origen en Midtown Manhattan | Cientos de miles de dolares |

---

## Estructura del repositorio

```
analisis_series_temporales_austral/
|
+-- TP2/
|   |
|   +-- AST_Script_TP2_Grupo2.ipynb   # Notebook principal con todo el desarrollo
|   +-- seccion_10_lstm.html          # Documento academico HTML sobre el modelo LSTM
|   |
|   +-- data/
|   |   +-- ingresos_diarios.csv      # Dataset procesado: ingresos diarios por serie
|   |
|   +-- raw/
|   |   +-- green_taxi_williamsburg_trips_24-25.zip   # Datos crudos taxis verdes 2024-2025
|   |   +-- green_taxi_williamsburg_trips_25-26.zip   # Datos crudos taxis verdes 2025-2026
|   |   +-- uber_midtown_center_trips_24-25.zip       # Datos crudos Uber Midtown 2024-2025
|   |   +-- uber_midtown_center_trips_25-26.zip       # Datos crudos Uber Midtown 2025-2026
|   |   +-- uber_williamsburg_trips_24-25.zip         # Datos crudos Uber Williamsburg 2024-2025
|   |   +-- uber_williamsburg_trips_25-26.zip         # Datos crudos Uber Williamsburg 2025-2026
|   |
|   +-- cv_lstm.csv                   # Cache de resultados de validacion cruzada LSTM
|   +-- resultados_lstm.csv           # Metricas finales del modelo LSTM en test
|
+-- .venv/                            # Entorno virtual Python (no versionado en produccion)
```

---

## Contenido de la notebook

La notebook `AST_Script_TP2_Grupo2.ipynb` esta organizada en las siguientes secciones:

| Seccion | Contenido |
|---|---|
| 1. Carga y exploracion de datos | Importacion del dataset, estadisticas descriptivas, visualizacion de las series |
| 2. Analisis de estacionariedad | Tests ADF y KPSS para las tres series |
| 3. Analisis de autocorrelacion | Funciones ACF y PACF, test de Ljung-Box |
| 4. Descomposicion de series | Descomposicion STL para identificar tendencia y estacionalidad |
| 5. Ingenieria de variables | Creacion de rezagos, variables de calendario y festivos |
| 6. Particion de datos | Division temporal train (jun. 2024 — abr. 2026) / test (may. 2026) |
| 7. Modelo SARIMAX | Identificacion, estimacion, diagnostico y pronostico |
| 8. Modelo LightGBM | Entrenamiento con Optuna, validacion cruzada y evaluacion en test |
| 9. Modelo XGBoost | Entrenamiento con Optuna, validacion cruzada y evaluacion en test |
| 10. Modelo LSTM | Verificacion de requisitos, arquitectura, Optuna, CV y evaluacion en test |
| 11. Comparacion de modelos | Tabla y graficos comparativos de RMSE, MAE y MAPE entre todos los modelos |

---

## Resultados principales (test: mayo 2026)

### Taxi verde — Williamsburg

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| SARIMAX | — | — | — |
| LightGBM | — | — | — |
| XGBoost | 98.98 | 61.66 | 40.33 |
| LSTM | 119.47 | 74.36 | 41.30 |

### Uber — Williamsburg

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| SARIMAX | — | — | — |
| LightGBM | — | — | — |
| XGBoost | — | — | — |
| LSTM | 18,528.65 | 13,963.39 | 8.12 |

### Uber — Midtown Center

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| SARIMAX | — | — | — |
| LightGBM | — | — | — |
| XGBoost | — | — | — |
| LSTM | 75,693.13 | 52,421.73 | 18.36 |

> Los valores exactos de todos los modelos se encuentran en la seccion 11 de la notebook
> y en la tabla comparativa generada al ejecutar la celda de comparacion final.

---

## Requisitos del entorno

El proyecto fue desarrollado y ejecutado en Python 3.13 (entorno Google Colab) y
Python 3.14 (entorno local Windows). Las dependencias principales son:

```
pandas
numpy
matplotlib
scipy
statsmodels
scikit-learn
lightgbm
xgboost
optuna
neuralforecast
torch
pytorch-lightning
great_tables
```

Para instalar las dependencias en un entorno local con `uv`:

```bash
uv pip install pandas numpy matplotlib scipy statsmodels scikit-learn lightgbm xgboost optuna neuralforecast great-tables
```

> El paquete `neuralforecast` instala automaticamente `torch` y `pytorch-lightning`
> como dependencias.

---

## Reproducibilidad

Todos los modelos utilizan semilla fija `random_seed = 42` donde es posible.
La notebook esta disenada para ejecutarse de arriba hacia abajo en orden secuencial.
Las secciones de validacion cruzada del LSTM incluyen un mecanismo de cache en CSV
(`cv_lstm.csv`) que evita recomputar los resultados si el archivo ya existe; para
forzar el recalculo se debe activar el flag `FORZAR_RECALCULO_CV_LSTM = True`.

La busqueda de hiperparametros con Optuna es estocastica: los resultados exactos pueden
variar entre ejecuciones incluso con la misma semilla, debido a la naturaleza del
algoritmo TPE. Los hiperparametros optimos encontrados en la ejecucion de referencia
fueron:

**LightGBM / XGBoost:** ver seccion 8 y 9 de la notebook.

**LSTM (35 trials, 200 pasos/trial):**

| Hiperparametro | Valor optimo |
|---|---|
| hidden_size | 32 |
| n_layers | 1 |
| dropout | 0.15 (efectivo: 0.0, dado n_layers = 1) |
| learning_rate | 0.001655 |
| Mejor MAE_CV | 17,550 |

---

## Documentacion adicional

- `TP2/seccion_10_lstm.html`: documento academico en HTML que explica con rigor
  la implementacion y los resultados del modelo LSTM (seccion 10 de la notebook),
  incluyendo fundamento teorico, ecuaciones de las compuertas LSTM, descripcion
  del proceso de optimizacion con Optuna, analisis de metricas y diagnostico de
  residuos. Incluye las figuras generadas por la notebook embebidas directamente
  en el HTML. Pensado para ser incorporado en el informe final del trabajo practico.

---

## Notas sobre los datos crudos

Los archivos en `TP2/raw/` son los datasets originales de viajes descargados de
las fuentes de datos abiertas de la ciudad de Nueva York (TLC Trip Record Data).
El script de preprocesamiento que los convierte en `data/ingresos_diarios.csv`
se encuentra en las primeras celdas de la notebook (seccion 1).

Los archivos raw tienen un tamano considerable (varios cientos de MB sin comprimir)
y pueden no estar incluidos en el repositorio remoto dependiendo de la politica
de tamano de archivos del servidor Git utilizado. En ese caso, los datos procesados
en `data/ingresos_diarios.csv` son suficientes para ejecutar desde la seccion 2
en adelante.

---

*Ultima actualizacion: septiembre de 2026*

