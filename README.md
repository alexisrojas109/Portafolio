# Portafolio

El objetivo de este proyecto es predecir el nivel de PIB de distintos países a partir de indicadores económicos, sociales y demográficos obtenidos del Banco Mundial.

El trabajo se realizará en tres etapas principales:

- Etapa 1: Análisis descriptivo e imputación de datos

- Etapa 2: Reducción de dimensionalidad con PCA

- Etapa 3: Modelación mediante algoritmos de clasificación


### Etapa I: Recopilación y Exploración Inicial de Datos

En esta primera fase, nos enfocamos en la adquisición y un primer vistazo al conjunto de datos. Los pasos clave fueron:

1.  **Descarga de Datos**: Utilizamos la librería `wbgapi` para descargar 65 indicadores económicos, sociales y demográficos del Banco Mundial para el año 2023. Estos indicadores fueron seleccionados en base a un diccionario predefinido (`dicc_indicadores_wb`).
2.  **Filtrado de Países**: Se identificó que el dataset inicial contenía agregados regionales y mundiales. Para asegurar que solo trabajáramos con datos de países individuales, filtramos el DataFrame utilizando una lista de códigos ISO obtenidos de la información de economías del Banco Mundial, excluyendo las entidades agregadas.
3.  **Análisis Exploratorio Inicial (`.tail()`, `.info()`)**: Realizamos un análisis rápido de las últimas filas del DataFrame y examinamos la información general (`.info()`) para comprender la estructura del dataset, los tipos de datos y la presencia inicial de valores nulos.

### Etapa II: Preprocesamiento de Datos y Reducción de Dimensionalidad

Esta etapa fue crucial para preparar los datos para los modelos de clasificación, abordando la calidad y la estructura de las variables:

1.  **Manejo de Valores Faltantes (NA)**:
    *   Eliminamos columnas con más del 15% de valores nulos, ya que imputar un porcentaje tan alto podría introducir sesgos significativos.
    *   Las columnas con un porcentaje de nulos inferior al 15% fueron imputadas utilizando la mediana de cada variable. La mediana fue elegida por su robustez ante la presencia de valores atípicos.
2.  **Identificación y Tratamiento de Outliers**: Aplicamos el método IQR (Rango Intercuartílico) para identificar valores atípicos. Se determinó que estos valores, aunque numerosos en algunas variables, no representaban errores de datos sino diferencias inherentes entre las economías globales. Por lo tanto, no se eliminaron, ya que la estandarización posterior mitiga su impacto en los modelos.
3.  **Visualización del PIB**: Se generaron histogramas, boxplots y un mapa coroplético para visualizar la distribución del PIB (`NY.GDP.MKTP.PP.KD`). Esto confirmó la presencia de valores atípicos en el extremo superior y mostró las brechas económicas a nivel mundial.
4.  **Análisis de Multicolinealidad**: Se creó una matriz de correlación visualizada con un heatmap, que confirmó la existencia de multicolinealidad severa entre las variables predictoras.
5.  **Discretización de la Variable Objetivo**: La variable continua `NY.GDP.MKTP.PP.KD` fue transformada en una variable discreta, `Clase_PIB`, categorizándola en 5 quintiles ('Low', 'Medium-Low', 'Medium', 'Medium-High', 'High'). Esto preparó el dataset para problemas de clasificación.
6.  **Estandarización para PCA**: Se estandarizaron las variables numéricas (excluyendo la variable objetivo discreta y el PIB original) para asegurar que el PCA se realizara correctamente, sin que las variables con mayores escalas dominaran el análisis.
7.  **Aplicación de PCA**: Se realizó un Análisis de Componentes Principales (PCA). Basándonos en la varianza explicada acumulada, se seleccionaron los primeros 5 componentes principales, que capturaron aproximadamente el 74% de la varianza total. Esto redujo significativamente la dimensionalidad y abordó la multicolinealidad.

### Etapa III: Implementación y Comparación de Modelos de Clasificación

En la fase final, nos dedicamos a construir y evaluar modelos, tanto con los datos originales como con los componentes principales:

1.  **Modelos sin Reducción de Dimensionalidad (Baseline)**:
    *   **Random Forest Classifier**: Se entrenó un modelo Random Forest utilizando todas las variables predictoras originales (sin PCA). Los datos se dividieron en conjuntos de entrenamiento y prueba (70/30) con estratificación. Este modelo sirvió como baseline.
    *   **K-Nearest Neighbors (KNN)**: Se implementó un modelo KNN con los mismos datos originales y división, con el objetivo de compararlo con Random Forest y evaluar su rendimiento como baseline.
2.  **Modelos con Reducción de Dimensionalidad (PCA)**:
    *   **Random Forest Classifier con PCA**: Se entrenó un Random Forest utilizando los 5 componentes principales obtenidos en la Etapa II. Los datos también se dividieron en conjuntos de entrenamiento y prueba con estratificación.
    *   **K-Nearest Neighbors (KNN) con PCA**: Se implementó un modelo KNN utilizando los 5 componentes principales.
3.  **Evaluación y Comparación**: Para cada uno de los cuatro modelos, se calcularon y mostraron:
    *   La precisión (Accuracy) del modelo.
    *   Un reporte de clasificación detallado (Precision, Recall, F1-Score por clase).
    *   Matrices de confusión visualizadas con heatmaps.
4.  **Conclusiones**: Se observó que la aplicación de PCA tuvo un impacto negativo en el rendimiento de ambos modelos, sugiriendo que los componentes principales no retuvieron suficiente información discriminante. El **Random Forest Classifier sin PCA** fue el modelo con mejor desempeño, logrando la mayor precisión y un buen equilibrio en las métricas de clasificación.
