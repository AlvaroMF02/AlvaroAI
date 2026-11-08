# Proyectos técnicos de Álvaro

> Resumen de proyectos para el portfolio. StrikeWatch y Bank Churn están descritos en el CV maestro; los demás incluyen información aportada por Álvaro en conversaciones. Algunos detalles técnicos y enlaces todavía están pendientes de verificación.

## StrikeWatch — Detección y visualización de rayos en tiempo real

### Problema

Transformar señales meteorológicas que llegan continuamente en datos almacenados, analizables y visualizables. Sirve como ejemplo de ingestión y procesamiento de datos en tiempo real.

### Funcionamiento

Captura eventos mediante Python y WebSockets, procesa los mensajes recibidos, transforma los datos y los almacena en MySQL. Después permite explorar los registros con dashboards en Power BI mediante filtros geográficos y temporales.

### Tecnologías

Python, WebSockets, MySQL, ETL y Power BI. Álvaro ha trabajado también con bibliotecas asíncronas de Python en el contexto de este proyecto.

### Qué demuestra

Integración de fuentes externas, procesamiento de flujos de eventos, almacenamiento relacional, transformaciones y visualización geoespacial.

## Bank Churn Prediction — Predicción de abandono de clientes

### Problema

Analizar qué factores están relacionados con el abandono de clientes bancarios y construir modelos capaces de clasificar clientes según el riesgo de abandono.

### Proceso

Realizó exploración inicial de datos, revisión de valores nulos y duplicados, análisis de distribuciones y relaciones con la variable objetivo, transformación de variables y preparación para entrenamiento. Comparó Logistic Regression, Decision Tree, Random Forest y XGBoost. Evaluó los modelos con métricas de clasificación como accuracy, precisión, recall, ROC-AUC y matriz de confusión, y realizó visualizaciones en Power BI.

### Tecnologías

Python, pandas, scikit-learn, técnicas de Machine Learning supervisado y Power BI.

### Resultados y limitaciones

El CV maestro menciona una accuracy máxima del 86,7 %, pero este documento no recoge el conjunto de evaluación ni el contexto necesario para interpretar ese resultado. No se trata de una métrica verificada en producción.

### Qué demuestra

Capacidad de desarrollar un flujo de trabajo de ML de extremo a extremo y conectar los resultados con análisis de negocio.

## PredictCar — Predicción del precio de vehículos

### Problema

Estimar el precio de coches a partir de atributos observables mediante modelos de regresión.

### Funcionamiento

Álvaro desarrolló un proyecto de Machine Learning que compara modelos basados en árboles, incluidos Decision Tree, Random Forest y XGBoost, y mide su rendimiento con métricas de regresión como R², MAE y RMSE. Lo complementó con una interfaz web y visualización de resultados.

### Tecnologías

Python, scikit-learn, Flask, TensorFlow.js, Tailwind y Grafana. Las métricas concretas y el tamaño del dataset no están documentados aquí.

## AireVis — Análisis de calidad del aire

### Problema

Estudiar datos de contaminación atmosférica y polen con componentes temporales y ambientales.

### Datos y análisis

Álvaro ha descrito un trabajo con datos CAMS de calidad del aire, incluyendo contaminantes como CO, NO₂, O₃, PM2.5, PM10 y SO₂, además de polen. Preparó datos con fecha y hora y utilizó gráficos para explorar relaciones, como PM2.5 frente a PM10.

### Estado

Descripción basada en conversaciones. El dataset exacto, el periodo, los resultados y el repositorio no están documentados en este archivo.

## CoffeeShopDB — SQL y Power BI

### Problema

Trabajar con información comercial de cafeterías mediante consultas SQL, modelado y visualización de ventas.

### Funcionamiento

Incluye un modelo con tabla de hechos de ventas y dimensiones como productos, categorías, tiendas y empleados. Álvaro realizó ejercicios de SQL y creó informes Power BI con medidas de ventas, cantidades, ticket medio y evolución temporal.

### Tecnologías

SQL, modelado relacional, Power BI y DAX.

## Automatización OCR de tickets

### Objetivo

Experimentar con el procesamiento de fotografías de tickets y el registro estructurado de información.

### Flujo descrito

Imagen enviada a través de WhatsApp, extracción de texto mediante OCR, procesamiento asistido por IA y almacenamiento en Google Sheets mediante un flujo de n8n.

### Estado

Prototipo descrito en conversaciones. No se ha verificado su despliegue público.

## Álvaro AI — Gemelo digital para portfolio (en desarrollo)

### Objetivo

Desarrollar un gemelo digital para el portfolio que pueda responder preguntas sobre la formación, experiencia, proyectos y forma de comunicarse de Álvaro.

### Tecnologías y diseño previsto

Google Gemini como modelo de lenguaje, embeddings de Gemini, ChromaDB como almacenamiento vectorial, recuperación aumentada (RAG), LangGraph para orquestación y memoria de conversación. Inicialmente se desarrollará en un notebook de Jupyter; una interfaz pública se contempla como mejora posterior.

### Estado

En desarrollo como proyecto académico. Su despliegue público y su evaluación final todavía no constan como completados.
