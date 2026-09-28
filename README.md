# Analisis de Series Temporales

## Descripcion del proyecto

Este repositorio contiene el desarrollo completo del Trabajo Practico N.2 de la
materia Analisis de Series Temporales. El objetivo es pronosticar los ingresos
diarios de tres flujos de transporte por aplicacion en la ciudad de Nueva York
durante el mes de mayo de 2026, comparando cinco familias de modelos:

1. **Modelo estadistico (SARIMAX):** modelo autorregresivo integrado con media movil
   estacional y variables exogenas de calendario.
2. **Ensambles de arboles (LightGBM y XGBoost):** modelos de gradient boosting con
   ingenieria de rezagos y variables de calendario, con busqueda de hiperparametros
   mediante Optuna.
3. **Red neuronal recurrente (LSTM):** modelo de aprendizaje profundo implementado
   con NeuralForecast (Nixtla), con busqueda de hiperparametros mediante Optuna.
4. **Libreria Darts:** modelos clasicos y de machine learning (NaiveSeasonal,
   LinearRegression, RandomForest) con validacion cruzada temporal integrada.
5. **AutoML (AutoGluon):** pronostico automatizado con `TimeSeriesPredictor`,
   sin configuracion manual de arquitectura.

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
+-- README.md                                   # Este archivo
|
+-- TP2/
|   |
|   +-- AST_Script_TP2_Grupo2.ipynb   # Notebook principal (147 celdas)
|   +-- seccion_10_lstm.html          # Documento academico HTML sobre el modelo LSTM
|   |
|   +-- data/
|   |   +-- ingresos_diarios.csv      # Dataset procesado: ingresos diarios por serie
|   |
|   +-- raw/
|   |   +-- green_taxi_williamsburg_trips_24-25.zip
|   |   +-- green_taxi_williamsburg_trips_25-26.zip
|   |   +-- uber_midtown_center_trips_24-25.zip
|   |   +-- uber_midtown_center_trips_25-26.zip
|   |   +-- uber_williamsburg_trips_24-25.zip
|   |   +-- uber_williamsburg_trips_25-26.zip
|   |
|   +-- cv_lstm.csv                   # Cache de resultados de validacion cruzada LSTM
|   +-- resultados_lstm.csv           # Metricas finales del modelo LSTM en test
|
+-- .venv/                            # Entorno virtual Python (no versionado en produccion)
```

---

## Contenido de la notebook

La notebook `AST_Script_TP2_Grupo2.ipynb` esta organizada en las siguientes secciones:

| Seccion | Titulo | Contenido principal |
|---|---|---|
| 1 | Configuracion del entorno | Importaciones, configuracion de rutas y parametros globales |
| 2 | Construccion de la serie diaria | Procesamiento de datos crudos, agregacion a nivel diario (06/2024 — 05/2026) |
| 3 | Configuracion de las series | Grafico de evolucion temporal, media movil y varianza movil (ventana 7 dias) |
| 4 | Estabilizacion de la varianza | Transformacion Box-Cox; determinacion del lambda optimo por MLE por serie |
| 5 | Diagnostico de estacionariedad | Tests OCSB, Canova-Hansen, ADF, Phillips-Perron y KPSS; FAS, FAC y FACP |
| 6 | Variables exogenas de calendario | Construccion de variables de dia de semana, festivos, mes y ano |
| 7 | Variables autorregresivas | Rezagos y estadisticos moviles para los modelos ML |
| 8 | Benchmark estadistico: SARIMAX | Grid search con cache, seleccion del modelo por serie, pronostico |
| 9 | Machine Learning: LightGBM y XGBoost | Preparacion de datos, Optuna, pronostico recursivo, CV temporal, importancia de variables, diagnostico de residuos |
| 10 | Redes Neuronales: LSTM | Verificacion de requisitos, preparacion de datos, Optuna, CV temporal, evaluacion en test, diagnostico de residuos |
| 11 | Modelado con Darts | NaiveSeasonal, LinearRegression y RandomForest; CV temporal; evaluacion en test (mayo 2026) |
| 12 | AutoML: AutoGluon | `TimeSeriesPredictor`, pronostico vs. serie real, diagnostico de residuos, comparacion final |

---

## Resultados en test (mayo 2026)

Las metricas a continuacion corresponden a la evaluacion sobre los 31 dias de mayo de 2026.
RMSE y MAE estan expresados en dolares de ingreso diario.

### Taxi verde — Williamsburg

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| LSTM (NeuralForecast) | 119.47 | 74.36 | 41.30 |
| Darts — RandomForest | 155.83 | 88.49 | 60.07 |
| AutoGluon TS | 120.82 | 73.60 | 57.61 |
| LightGBM / XGBoost | ver notebook, seccion 9 | | |
| SARIMAX | ver notebook, seccion 8 | | |

### Uber — Williamsburg

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| LSTM (NeuralForecast) | 18,528.65 | 13,963.39 | 8.12 |
| Darts — RandomForest | 19,792.01 | 15,254.18 | 8.77 |
| AutoGluon TS | 15,325.28 | 11,913.26 | 6.92 |
| LightGBM / XGBoost | ver notebook, seccion 9 | | |
| SARIMAX | ver notebook, seccion 8 | | |

### Uber — Midtown Center

| Modelo | RMSE | MAE | MAPE (%) |
|---|---|---|---|
| LSTM (NeuralForecast) | 75,693.13 | 52,421.73 | 18.36 |
| Darts — LinearRegression | 96,187.53 | 74,204.33 | 20.97 |
| AutoGluon TS | 62,273.16 | 45,363.61 | 11.99 |
| LightGBM / XGBoost | ver notebook, seccion 9 | | |
| SARIMAX | ver notebook, seccion 8 | | |

> Los valores completos de todos los modelos, incluyendo RMSE_CV y el gap CV-Test,
> se encuentran en la tabla comparativa de la seccion 12 de la notebook.

---

## Requisitos del entorno

El proyecto fue desarrollado en Python 3.13 (Google Colab) y Python 3.14 (entorno local Windows).
Las dependencias principales son:

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
darts
autogluon.timeseries
great_tables
```

Para instalar las dependencias en un entorno local con `uv`:

```bash
uv pip install pandas numpy matplotlib scipy statsmodels scikit-learn lightgbm xgboost \
    optuna neuralforecast great-tables darts
```

> La instalacion de `autogluon` se recomienda hacer por separado siguiendo la
> documentacion oficial, ya que requiere instrucciones especificas segun el sistema
> operativo y la disponibilidad de GPU.

---

## Reproducibilidad

Todos los modelos utilizan semilla fija `random_seed = 42` donde es posible.
La notebook esta disenada para ejecutarse de arriba hacia abajo en orden secuencial.

**Mecanismos de cache:**

- Validacion cruzada LSTM: resultados cacheados en `cv_lstm.csv`. Para forzar el
  recalculo activar `FORZAR_RECALCULO_CV_LSTM = True`.
- Grid search SARIMAX: los resultados de la busqueda de ordenes se cachean
  internamente; ver la seccion 8.3 de la notebook.

**Busqueda de hiperparametros con Optuna:**

La busqueda bayesiana (algoritmo TPE) es estocastica: los resultados exactos pueden
variar entre ejecuciones. Los hiperparametros optimos encontrados en la ejecucion de
referencia son los siguientes:

LSTM (35 trials, 200 pasos por trial):

| Hiperparametro | Valor optimo |
|---|---|
| encoder_hidden_size | 32 |
| encoder_n_layers | 1 |
| encoder_dropout | 0.15 (efectivo: 0.0, dado n_layers = 1) |
| learning_rate | 0.001655 |
| scaler_type | robust |
| Mejor MAE_CV (2 ventanas) | 17,550 |

LightGBM y XGBoost: los hiperparametros optimos se registran en los outputs de la
seccion 9 de la notebook.

---

## Documentacion adicional

`TP2/seccion_10_lstm.html`: documento academico en formato HTML que explica con
rigor la implementacion y los resultados del modelo LSTM (seccion 10 de la notebook).
Incluye fundamento teorico con las ecuaciones de las compuertas LSTM, descripcion del
proceso de optimizacion con Optuna, analisis de metricas, diagnostico de residuos y
bibliografia con DOIs verificables. Las figuras generadas por la notebook estan
embebidas directamente en el HTML. Disenado para ser incorporado en el informe final
del trabajo practico.

---

## Notas sobre los datos crudos

Los archivos en `TP2/raw/` son los datasets originales de viajes descargados de la
fuente de datos abiertos de la ciudad de Nueva York (TLC Trip Record Data). El script
de preprocesamiento que los convierte en `data/ingresos_diarios.csv` se encuentra en
la seccion 2 de la notebook.

Los archivos raw tienen un tamano considerable (varios cientos de MB sin comprimir) y
pueden no estar incluidos en el repositorio remoto dependiendo de la politica de tamano
de archivos del servidor Git utilizado. En ese caso, los datos procesados en
`data/ingresos_diarios.csv` son suficientes para ejecutar la notebook desde la
seccion 3 en adelante.

---

*Ultima actualizacion: septiembre de 2026*
