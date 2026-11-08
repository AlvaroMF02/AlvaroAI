# Álvaro AI — primer prototipo RAG

Asistente de portfolio con Gemini, embeddings de Gemini, ChromaDB, LangGraph y memoria conversacional.

## Estado
**Prototipo inicial, todavía sin ejecutar contra Gemini.** Los cuatro archivos de `data/` son borradores para revisar. No cumplen por sí solos el volumen aproximado de 20 páginas de texto solicitado como alternativa a 3 documentos. Aunque ya hay cuatro documentos, es recomendable ampliar especialmente proyectos y metodología para demostrar contenido sustancial.

## Instalación paso a paso (Bash en VS Code)

Abre la carpeta `AlvaroAI_inicio` en VS Code y, desde la terminal Bash situada en la raíz del proyecto, sigue estos pasos. Se recomienda Python 3.11 o 3.12.

### 1. Crear el entorno virtual `.venv`

```bash
python -m venv .venv
```

Si `python` no está disponible, prueba `python3 -m venv .venv` (Linux/WSL) o `py -m venv .venv` (Windows).

### 2. Activar el entorno virtual

**Git Bash en Windows:**

```bash
source .venv/Scripts/activate
```

**Bash en Linux o WSL:**

```bash
source .venv/bin/activate
```

Verás `(.venv)` al principio del prompt. Si estás usando VS Code, selecciona este intérprete de Python también para el notebook.

### 3. Instalar las dependencias

```bash
python -m pip install -r requirements.txt
```

**Nota:** instalar `requirements.txt` no crea ni `.venv` ni `.env` automáticamente.

### 4. Crear y configurar el archivo `.env`

```bash
cp .env.example .env
```

Abre el nuevo archivo `.env` en VS Code y sustituye `pon_tu_clave_aqui` por tu clave de Google AI Studio:

```dotenv
GOOGLE_API_KEY=tu_clave_real_de_gemini
GEMINI_MODEL=gemini-2.5-flash
GEMINI_EMBEDDING_MODEL=models/gemini-embedding-001
```

Las dos líneas de modelos ya vienen en `.env.example`; puedes dejarlas como están. Nunca compartas tu clave ni subas `.env` a GitHub. La clave se obtiene en https://aistudio.google.com/api-keys.

### 5. Comprobar que se carga la clave

```bash
python -c "from dotenv import load_dotenv; import os; load_dotenv(); print('API configurada:', bool(os.getenv('GOOGLE_API_KEY')))"
```

Si aparece `API configurada: True`, Python está leyendo la variable. **Esto no comprueba todavía que la clave sea válida ni que Gemini responda.**

### 6. Ejecutar el notebook

Abre `notebooks/01_asistente.ipynb`, selecciona como kernel el Python de `.venv` y ejecuta las celdas desde el principio. Las primeras llamadas reales a Gemini necesitan conexión a Internet y están sujetas a las cuotas de la API.

## Estructura
- `src/rag.py`: cargador, fragmentación, embeddings, vector store, recuperación, grafo y memoria.
- `data/`: documentos iniciales de conocimiento y estilo.
- `notebooks/01_asistente.ipynb`: demo, cinco preguntas y test de memoria.

## Justificación del prompt
Separa la identidad simulada del usuario real, exige fundamentación documental, reconoce lagunas, trata el contexto recuperado como información y ajusta el tono a un asistente accesible. La personalidad no reemplaza hechos verificables.

## Limitaciones
- Chroma está en memoria y se reconstruye al reiniciar: sirve para el MVP; añadiremos persistencia cuando se consolide la carga.
- `InMemorySaver` mantiene contexto entre turnos de la misma ejecución, pero no entre reinicios.
- No hay streaming ni despliegue web todavía.
- Faltan pruebas reales con la API, revisión de documentación y validación de respuestas.
- No subir `.env`, datos privados ni información confidencial de empresas.

## Pruebas
Ejecutar preguntas de cobertura, memoria y desconocimiento. Comprobar manualmente las respuestas frente a las fuentes. Para la entrega final añadir resultados observados, no inventados, y captura o demostración real.
