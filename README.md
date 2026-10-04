# Proyecto Final de Grado - Ingeniería en Informática (Universidad de Palermo)

# 🦟 Machine Learning para la Predicción de Brotes de Dengue en CABA

## Descripción del Proyecto
Este proyecto presenta el diseño, desarrollo y prueba de concepto de un modelo predictivo basado en Machine Learning para anticipar 
el inicio de ciclos epidémicos de dengue en la Ciudad Autónoma de Buenos Aires (CABA). 
Este modelo propone una transición hacia una vigilancia proactiva utilizando series temporales y datos abiertos. 

## Arquitectura y Metodología
El trabajo contrasta el rendimiento de tres arquitecturas evaluadas bajo la métrica de Área Bajo la Curva ROC (AUC).

1. **LightGBM (Gradient Boosting)**
2. **Máquinas de Vectores de Soporte (SVM)**
3. **Redes Neuronales Recurrentes (RNN)**

### Feature Engineering
Para que el algoritmo logre capturar los tiempos biológicos del vector *Aedes aegypti* y el Período de Incubación Extrínseco (PIE), el *pipeline* incorpora:
* **Retardos temporales (Lags) de 2 a 4 semanas**
* **Deltas y Promedios Móviles (4 y 8 semanas)**
* **Imputación Direccional (LOCF)**

## Tecnologías y Dependencias
El ecosistema de desarrollo se basa en el lenguaje **R**. Las librerías y paquetes principales incluyen:

* `tidyverse`, `data.table` (Procesamiento y ETL)
* `lightgbm`, `e1071`, `caret` (Entrenamiento de ensambles y SVM)
* `torch`, `luz` (Ecosistema de Deep Learning nativo para R)
* `rBayesianOptimization` (Búsqueda probabilística de hiperparámetros óptimos)
* `zoo` (Imputación y manejo de series temporales)

## Uso e Implementación
1. Clonar el repositorio
2. Asegurar que los datasets `.csv` se encuentren en el directorio de entrada correspondiente (`/input`).
3. El pipeline ejecutará el preprocesamiento, el *Time Series Split* estricto para evitar *data leakage*, y la normalización (*Min-Max Scaling*)
5. Durante el entrenamiento, la búsqueda de hiperparámetros utilizará Optimización Bayesiana y *Grid Search*. Las mejores configuraciones se guardan en archivos `.txt` para reducir tiempo de computo en futuras ejecuciones.
8. Las predicciones finales se validarán sobre el *Test Set* independiente para generar las métricas de clasificación finales (AUC).
