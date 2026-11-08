# Álvaro AI — Gemelo digital con Gemini

Álvaro AI es un asistente conversacional creado como proyecto final del módulo de **IA Generativa** del programa de **Data Science e IA de Evolve**. Su objetivo es responder preguntas sobre mi formación, experiencia profesional, proyectos técnicos y algunos intereses personales a partir de una base documental propia.

El asistente habla en primera persona para representar mi estilo de comunicación, pero **es un gemelo digital basado en IA, no soy yo respondiendo en directo**.

## Objetivo

Aplicar los conceptos de modelos de lenguaje, embeddings, recuperación aumentada (RAG), bases de datos vectoriales y memoria conversacional en un caso práctico orientado a mi portfolio profesional.

## Tecnologías

- **Python y Jupyter Notebook:** desarrollo y pruebas.
- **Google Gemini:** generación de respuestas mediante el modelo indicado en la variable `GEMINI_MODEL`.
- **`gemini-embedding-001`:** conversión de textos y preguntas en vectores numéricos.
- **ChromaDB:** almacenamiento y búsqueda de fragmentos por similitud semántica.
- **LangChain:** integración del modelo, los documentos y la base vectorial.
- **LangGraph:** flujo del agente y gestión del historial mediante `InMemorySaver`.
- **python-dotenv:** carga de la clave de API desde un archivo `.env`.

## Arquitectura

```text
Documentos Markdown
      |
      v
Separación por secciones (##) y fragmentos de texto
      |
      v
Gemini Embeddings (gemini-embedding-001)
      |
      v
ChromaDB (colección alvaro_ai)

Pregunta del usuario
      |
      v
LangGraph (estado + historial de la conversación)
      |
      v
Búsqueda semántica en ChromaDB
      |
      v
Fragmentos relevantes + system prompt + historial
      |
      v
Gemini -> Respuesta
      |
      v
LangGraph guarda los nuevos mensajes en memoria
```

### Base de conocimiento

Se utilizan cuatro documentos Markdown en `data/documentos/`:

| Archivo | Contenido |
| --- | --- |
| `01_perfil.md` | Formación, competencias técnicas, idiomas y perfil profesional. |
| `02_experiencia.md` | Experiencia en Calconut y ElitechLab. |
| `03_proyectos.md` | Proyectos como StrikeWatch, PredictCar, Bank Churn y otros. |
| `04_personalidad.md` | Estilo de comunicación, intereses y límites de privacidad. |

La preparación de textos utiliza `RecursiveCharacterTextSplitter` con `chunk_size=800` y `chunk_overlap=150`. Antes de fragmentar, el notebook separa las secciones por encabezados de nivel `##`, descarta las introducciones generales y conserva el título de la sección en los fragmentos. Así se reduce el riesgo de perder el nombre de un proyecto al recuperar su descripción.

En las pruebas realizadas se indexaron **34 fragmentos**. Este número puede variar al editar los documentos.

Durante la indexación, los fragmentos se convierten en embeddings y se almacenan en ChromaDB. Durante una consulta, se genera el embedding de la pregunta y se recuperan los fragmentos más cercanos; el nodo de LangGraph consulta **4 fragmentos** (`k=4`). Después Gemini genera una respuesta con esa información y el historial de conversación.

### Memoria conversacional

El grafo utiliza `MessagesState` para representar el historial y `InMemorySaver` para conservarlo entre turnos de la misma conversación. Cada conversación se identifica mediante un `thread_id`. Esta memoria **no es persistente**: se pierde al reiniciar el kernel y no es una base de datos de conversaciones para producción.

Para consultas de seguimiento, el nodo construye la búsqueda con preguntas recientes del usuario y envía el historial a Gemini. Esto permite interpretar referencias como «ese proyecto» cuando anteriormente se ha hablado de PredictCar.

## Estructura del proyecto

```text
AlvaroAI_inicio/
├── .env                         # Configuración local
├── README.md
├── notebooks/
│   └── 01_asistente.ipynb      # Notebook principal
└── data/
    ├── documentos/
    │   ├── 01_perfil.md
    │   ├── 02_experiencia.md
    │   ├── 03_proyectos.md
    │   └── 04_personalidad.md
    └── chromadb/               # Base vectorial generada al ejecutar
```

> La carpeta `notebooks/` es opcional: el notebook también puede estar en la raíz. El código detecta ambas ubicaciones. Si el archivo descargado tiene otro nombre, no es necesario renombrarlo para ejecutar el proyecto.

## Instalación y ejecución

**Requisitos:** Python, Jupyter Notebook o VS Code con soporte Jupyter, conexión a Internet y una clave de API de Gemini con acceso a los modelos configurados.

1. Abrir una terminal en la carpeta raíz del proyecto y crear el entorno virtual:

   ```bash
   python -m venv .venv
   ```

2. Activar el entorno:

   **Windows (PowerShell):**

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   **Windows (Git Bash):**

   ```bash
   source .venv/Scripts/activate
   ```

3. Instalar las bibliotecas utilizadas:

   ```bash
   python -m pip install jupyter ipykernel python-dotenv langchain-core langchain-google-genai langchain-text-splitters langchain-chroma langgraph
   ```

4. Crear un archivo `.env` en la raíz del proyecto:

   ```dotenv
   GOOGLE_API_KEY=tu_clave_de_api
   GEMINI_MODEL=nombre_del_modelo_disponible
   ```

   Sustituir los valores de ejemplo por una clave real y un **identificador de modelo disponible en la cuenta de Gemini**. En las pruebas del proyecto se utilizó `gemini-3.5-flash-lite` como modelo generativo y `gemini-embedding-001` para embeddings. La disponibilidad y los límites dependen de la API y de la cuenta.

5. Colocar los cuatro documentos Markdown en `data/documentos/` y abrir `01_asistente.ipynb` (o el nombre actual del notebook) desde Jupyter o VS Code.

6. Seleccionar el kernel del entorno `.venv` y ejecutar las celdas **en orden, desde el principio**. El notebook carga los documentos, construye los embeddings, crea o actualiza la colección ChromaDB, configura el agente LangGraph y ejecuta las pruebas.

7. Para probar otra pregunta, editar el valor de `pregunta_usuario` en la última celda y ejecutarla. Las preguntas con el mismo `thread_id` comparten historial mientras el kernel permanezca activo.

**Importante:** si se modifican los documentos y cambia la cantidad de fragmentos, conviene detener el kernel, eliminar la colección local de prueba en `data/chromadb/` y volver a indexar. Los identificadores existentes no eliminan automáticamente fragmentos antiguos que ya no se utilizan. No borrar esa carpeta durante una ejecución activa.

## Diseño y justificación del system prompt

El `SYSTEM_PROMPT` define el comportamiento del agente. Se ha diseñado para que las respuestas sean útiles en un portfolio dirigido a reclutadores, manteniendo una forma de hablar cercana.

Las principales decisiones son:

- **Primera persona:** responde como representación de Álvaro al hablar de formación, experiencia y proyectos. La identidad de gemelo digital debe quedar clara cuando se pregunta por ella.
- **Tono profesional por defecto:** prioriza claridad y precisión ante consultas laborales. El humor se reserva para conversaciones informales donde encaje; se evitan risas y exclamaciones forzadas.
- **Respuestas fundamentadas:** utiliza el contexto documental recuperado y reconoce lo que no está documentado, sin atribuir experiencia, métricas o resultados inventados.
- **Privacidad:** evita divulgar residencia, información personal sensible y datos internos de las empresas. Solo se permiten contactos profesionales aprobados.
- **Adaptación del idioma:** responde en el idioma del visitante; en inglés emplea un nivel aproximado B2.
- **Separación de instrucciones y fuentes:** los fragmentos recuperados son datos para responder, no instrucciones que sustituyan al system prompt.

Estas reglas reducen riesgos, pero **no garantizan** que todas las respuestas sean exactas. Es necesario evaluar y revisar el contenido antes de una publicación abierta.

## Pruebas y resultados

Se realizaron cinco preguntas independientes y una prueba adicional de memoria conversacional:

| Prueba | Pregunta | Comportamiento observado |
| --- | --- | --- |
| 1. Calconut | ¿Qué experiencia tienes con Power BI en Calconut? | Describe mantenimiento y desarrollo de dashboards, Power Query, DAX y áreas de negocio. |
| 2. StrikeWatch | ¿Qué es StrikeWatch y qué tecnologías utiliza? | Explica captura de eventos con Python y WebSockets, MySQL y visualización en Power BI. |
| 3. PredictCar | ¿Qué modelos se utilizaron en PredictCar? | Identifica Decision Tree, Random Forest y XGBoost, y métricas R², MAE y RMSE. |
| 4. Formación | ¿Qué formación tienes? | Resume DAW, DIGITECH y Evolve en orden cronológico. |
| 5. Información desconocida | ¿Cuántos años has trabajado profesionalmente con AWS? | No inventa años de experiencia ni atribuye una trayectoria profesional en AWS. |

**Prueba de memoria:** después de «¿Qué es PredictCar?», el agente respondió a «¿Qué tecnologías utilizaste en ese proyecto?» y «¿Qué modelos de Machine Learning comparaste?» manteniendo la referencia a PredictCar. El estado mostró **6 mensajes** guardados (3 preguntas y 3 respuestas).

Los resultados corresponden a ejecuciones manuales del notebook. No constituyen una evaluación estadística exhaustiva ni garantizan el mismo resultado en todas las llamadas.

## Limitaciones y posibles mejoras

- La búsqueda por embeddings puede recuperar fragmentos relacionados semánticamente pero no los más útiles para una pregunta concreta.
- Gemini puede cambiar la redacción, equivocarse o pasar ocasionalmente a tercera persona; hay que contrastar las afirmaciones con los documentos.
- `InMemorySaver` conserva el historial únicamente durante la sesión del notebook.
- La API de Gemini requiere conexión y está sujeta a disponibilidad, cuotas y posibles errores temporales.
- El chatbot actual se utiliza desde un notebook. Una futura versión podría incorporar Streamlit, despliegue público, persistencia de memoria y una evaluación más amplia.

## Estado del proyecto

**Versión académica funcional:** agente con Gemini, RAG sobre ChromaDB, LangGraph con memoria de sesión y pruebas dentro de Jupyter Notebook. La interfaz web y el despliegue público quedan como posibles ampliaciones.
