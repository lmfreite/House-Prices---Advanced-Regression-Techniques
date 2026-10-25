# House Prices: modelos de regresión

Proyecto de predicción del precio de venta de viviendas del concurso [House Prices - Advanced Regression Techniques de Kaggle](https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques).

## Código fuente

El desarrollo, el preprocesamiento, la evaluación y la generación de archivos de entrega están organizados en el notebook:

- [House Prices - Advanced Regression Techniques.ipynb](House%20Prices%20-%20Advanced%20Regression%20Techniques.ipynb)

El notebook entrena y compara diez modelos:

1. Regresión lineal
2. Ridge
3. Lasso
4. ElasticNet
5. Random Forest Regressor
6. Gradient Boosting Regressor
7. XGBoost Regressor
8. LightGBM Regressor
9. Support Vector Regression (SVR)
10. K-Nearest Neighbors Regressor (KNN)

## Preparación y ejecución

Descarga `train.csv` y `test.csv` desde la página de datos de Kaggle y colócalos en la raíz del proyecto. Después crea y activa el entorno virtual e instala las dependencias:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Abre el notebook usando el kernel de `.venv` y ejecuta sus celdas en orden. Las celdas de limpieza generan `train_limpio.csv` y `test_limpio.csv`; la última celda evalúa los modelos y genera `submission_*.csv`.

Los archivos de datos, las tablas limpias, las entregas, el entorno virtual y los archivos temporales están excluidos de Git mediante `.gitignore`.

## Preprocesamiento y evaluación

- Se revisaron los valores faltantes y se analizaron las correlaciones y distribuciones de las variables.
- Para el entrenamiento, las variables numéricas se imputan con la mediana y se escalan con `StandardScaler`.
- Las variables categóricas se imputan con una categoría de faltante y se convierten a variables indicadoras con `OneHotEncoder`. `handle_unknown='ignore'` permite procesar categorías nuevas que aparezcan en el conjunto de prueba.
- Se reserva el 20% de `train.csv` para validación, usando `random_state=42`.
- Se calculan MAPE, bias, RMSE y R² en el conjunto de validación. Después cada modelo vuelve a entrenarse con todo `train.csv` para predecir `test.csv`.

El preprocesamiento dentro de un `Pipeline` permite que los modelos reciban datos numéricos incluso cuando las columnas originales contienen categorías de texto, por ejemplo `RL`.

## Resultados de validación

Los siguientes resultados corresponden a la división de validación del 20%, no al puntaje del leaderboard de Kaggle. La tabla está ordenada por RMSE ascendente.

| Orden por RMSE | Modelo | MAPE | Bias | RMSE | R² |
|---:|---|---:|---:|---:|---:|
| 1 | Gradient Boosting | 10.10% | -632.95 | 27,638.09 | 0.9004 |
| 2 | XGBoost | 10.25% | -1,313.74 | 28,752.07 | 0.8922 |
| 3 | Random Forest | 10.66% | -1,342.38 | 29,095.29 | 0.8896 |
| 4 | LightGBM | 10.21% | -1,323.02 | 29,505.50 | 0.8865 |
| 5 | Ridge | 11.56% | -1,701.62 | 29,758.54 | 0.8845 |
| 6 | ElasticNet | 11.28% | -2,842.40 | 36,128.96 | 0.8298 |
| 7 | KNN | 13.21% | -4,478.32 | 38,487.36 | 0.8069 |
| 8 | Lasso | 11.97% | -2,915.10 | 42,336.12 | 0.7663 |
| 9 | Regresión lineal | 12.86% | -4,274.48 | 65,296.42 | 0.4441 |
| 10 | SVR | 31.81% | -13,889.31 | 82,355.28 | 0.1158 |

Gradient Boosting obtuvo el mejor resultado de validación en las tres métricas principales reportadas: MAPE más bajo, RMSE más bajo y R² más alto. Este resultado no debe presentarse como el puntaje oficial de Kaggle; no se proporcionó aquí el score de ninguna entrega del leaderboard.

Kaggle evalúa esta competencia mediante RMSLE, el error cuadrático medio entre los logaritmos de los precios. Por eso los resultados de MAPE y RMSE en dólares sirven para comparar esta validación, pero no determinan necesariamente el orden del leaderboard. Un valor menor de RMSLE es mejor. Consulta la [descripción oficial de la evaluación](https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques).

## Cambios realizados y observaciones

- Se añadió imputación de valores faltantes, escalado numérico y codificación one-hot para corregir el error al pasar texto como `RL` a los modelos.
- Se organizó el tratamiento de datos y el estimador en pipelines, para aplicar el mismo preprocesamiento durante validación y predicción.
- Se añadieron métricas de validación y una división fija para comparar los diez modelos bajo los mismos datos.
- En esta corrida, Lasso mostró una advertencia de convergencia. Sus métricas deben interpretarse con cautela; se puede probar un `max_iter` mayor o ajustar `alpha` antes de compararlo de nuevo.
- No se realizó búsqueda sistemática de hiperparámetros. Las configuraciones probadas son las que aparecen en la celda de modelos del notebook.

## Archivos de entrega

Al ejecutar el notebook se generan `submission_01_RegresionLineal.csv` hasta `submission_10_KNN.csv`. Cada archivo contiene las columnas `Id` y `SalePrice` para las 1,459 filas de `test.csv`.
