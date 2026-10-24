# Universidad de Palermo - Ingeniería en Informática
# Trabajo Final de Grado
 
# 🦟 Diseño y prueba de concepto de un modelo predictivo de brotes de dengue en la Ciudad Autónoma de Buenos Aires
 
**Autor:** Carlos Lazzarino
 
## Descripción del proyecto
Este proyecto presenta una prueba de concepto con el diseño, entrenamiento y evaluación de modelos de *Machine Learning* para anticipar la cantidad semanal de casos de dengue en la Ciudad Autónoma de Buenos Aires (CABA) con **2 semanas de anticipación**. Los modelos combinan la evolución reciente de los casos con variables climáticas (temperatura, lluvia y viento) agregadas por semana epidemiológica.
 
## Datos
El dataset consolidado `DATASET_DENGUE.csv` integra fuentes públicas:
 
* **BADATA** (Gobierno de la Ciudad de Buenos Aires)
* **Ministerio de Salud de la Nación:** casos confirmados de dengue notificados por semana epidemiológica y comuna.
* **Boletines Epidemiologicos** de la Ciudad Autónoma de Buenos Aires y del Ministerio de Salud de la Nación: casos confirmados de dengue notificados por semana epidemiológica y comuna.
* **Open-Meteo:** variables climáticas semanales (temperatura media, mínima y máxima, lluvia y viento).

## Diseño experimental
 
### Temporadas y partición
* Cada semana epidemiológica (SE) se fecha por su día de inicio y se asigna a una temporada que va del 1/8 al 31/7.
* **Test:** temporada 2023/24 (SE que comienzan entre el 1/8/2023 y el 31/7/2024).
* **Train:** temporadas anteriores, desde 2018. Las semanas posteriores al 31/7/2024 se descartan.
  
### Variable objetivo y horizonte
* Se predice `log1p(casos)` de la semana *t*; las predicciones se vuelven a cantidad de casos con `expm1`.
* **Horizonte de anticipación: 2 semanas.** Solo se utiliza información disponible hasta *t-2*; el clima de la semana *t* no se incluye como variable.
* 
### Feature Engineering
* **Rezagos** de casos y clima en *t-2*, *t-3* y *t-4*.
* **Tendencia de casos:** crecimiento en escala logarítmica entre semanas.
* **Medias móviles** de casos (4 semanas) y clima (4 y 8 semanas); lluvia acumulada y temperatura media de 12 semanas.
* **Casos acumulados de la temporada** hasta *t-2*, como indicador de la intensidad de la temporada.
* **Imputación LOCF** (solo hacia adelante) para datos climáticos faltantes.
* **Normalización Min-Max** con parámetros calculados exclusivamente sobre el set de entrenamiento.
  
### Modelos
1. **LightGBM:** optimización bayesiana de hiperparámetros (`rBayesianOptimization`); cantidad de árboles definida por *early stopping*.
2. **SVR (kernel radial):** búsqueda con Grid Search de `cost`, `gamma` y `epsilon` (`e1071`).
3. **Red neuronal (MLP con Torch):** perceptrón multicapa con dos capas ocultas, implementado en R con `torch` y `luz`.

### Métricas de evaluación (Test)
* **MAE**, **RMSE** en casos por semana.

## Tecnologías y dependencias
Desarrollado en **R** con herramientas Open Source:
 
* `data.table` y `tidyverse` (manipulación de datos y gráficos)
* `lightgbm` (ensamble de árboles)
* `e1071` (SVR)
* `torch` y `luz` (redes neuronales sin dependencias de Python)
* `rBayesianOptimization` (optimización de hiperparámetros)
* `zoo` (imputación de series temporales)
* `MMWRweek` (conversión de semana epidemiológica a fecha)
 
## Uso
1. Clonar el repositorio o abrir el notebook en Google Colab con el entorno de ejecución configurado en **R**.
2. Copiar `DATASET_DENGUE.csv` en el directorio `./input/`.
3. Ejecutar el notebook principal en orden.
4. En la primera ejecución se instalan los paquetes faltantes y se descarga el backend C++ de Torch (`libtorch`).
5. Los hiperparámetros elegidos se guardan en `./output/` (`params_lgb.rds`, `params_svr.rds`, `params_torch.rds`). Si los archivos existen, se cargan y no se vuelve a optimizar. Si se modifican los datos, la partición o las variables, borrar estos archivos antes de ejecutar.
   
## Estructura del notebook
1. Configuración de entorno y carga de librerías.
2. Carga de datos.
3. Consolidación, partición por temporada y análisis exploratorio.
4. Feature Engineering (horizonte de 2 semanas) y normalización.
5. Entrenamiento con validación temporal (LightGBM, SVR, red neuronal y persistencia).
6. Predicción sobre el set de prueba (temporada 2023/24).
7. Métricas de error.
