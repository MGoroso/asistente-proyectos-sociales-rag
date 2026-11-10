# Asistente Inteligente de Proyectos Sociales — RAG local

**Autora:** Magalí Goroso  
**Carrera:** Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial  
**Modalidad:** Trabajo práctico individual  
**Fecha:** 10 de noviembre de 2026

## ¿Qué es este proyecto y por qué existe?

Este proyecto implementa un asistente de consulta documental mediante **generación aumentada por recuperación (RAG)**. Su objetivo es facilitar preguntas en lenguaje natural sobre tres documentos vinculados con convocatorias de proyectos sociales, violencia de género y servicios sociales de la Ciudad de Buenos Aires, mostrando los fragmentos que se recuperaron como contexto de cada respuesta.

El sistema funciona **localmente**: usa Ollama para generar representaciones vectoriales y respuestas, ChromaDB para buscar fragmentos relevantes y Gradio como interfaz de usuario. No requiere una API de generación de texto externa.

## Documentos utilizados

- `2026 Instructivo Convocatoria Proyectos Sociales.pdf` — 37 fragmentos.
- `20260827_GUIA VIOLENCIA DE GENERO 2026.pdf` — 123 fragmentos.
- `20260921_GUIA DE SERVICIOS SOCIALES 2026.pdf` — 331 fragmentos.

**Total:** 491 fragmentos en la colección `programas_sociales_bge_m3`.

## Tecnologías y metodología

- **Python y Jupyter Notebook:** desarrollo y ejecución.
- **pypdf:** extracción de texto de los PDF durante la preparación de la base.
- **LangChain / RecursiveCharacterTextSplitter:** división de documentos en fragmentos de 1.000 caracteres con superposición de 200.
- **Ollama + `bge-m3`:** embeddings para la indexación y las consultas.
- **ChromaDB:** almacenamiento persistente y búsqueda vectorial de los fragmentos.
- **Ollama + `granite4:latest`:** generación de respuestas a partir del contexto recuperado.
- **Gradio:** interfaz web local para preguntas, respuestas y fuentes.

### Flujo de una consulta

1. El usuario escribe una pregunta en Gradio.
2. `bge-m3` transforma la pregunta en un vector.
3. ChromaDB recupera los **3 fragmentos** más cercanos semánticamente.
4. Se construye un contexto que incluye el texto, el archivo y la página.
5. Granite genera una respuesta con instrucciones de utilizar solo el contexto y reconocer información ausente.
6. Gradio muestra la respuesta y los fragmentos recuperados con sus metadatos.

Los fragmentos mostrados **no constituyen una verificación automática** de cada afirmación del modelo.

## Estructura del repositorio

```text
TP_RAG/
├── README.md
├── requirements.txt
├── .gitignore
├── rag_proyecto_limpio.ipynb       # Versión de ejecución y demostración
├── datos/                          # Tres PDF de referencia (si su redistribución está permitida)
└── chroma_db/                      # Base vectorial persistida, necesaria para el notebook limpio
```

El notebook de desarrollo, el entorno `.venv` y los experimentos descartados no forman parte de la entrega recomendada.

## Instalación y ejecución

1. Instalar **Python 3.12** y **Ollama**.
2. Descargar los modelos en Ollama:

   ```bash
   ollama pull bge-m3
   ollama pull granite4:latest
   ```

3. Verificar que Ollama esté ejecutándose y disponible en `http://localhost:11434`.
4. Desde la carpeta del proyecto, crear y activar un entorno virtual e instalar dependencias:

   **Windows (PowerShell):**

   ```powershell
   py -3.12 -m venv .venv
   .\.venv\Scripts\python.exe -m pip install -r requirements.txt
   ```

5. Abrir `rag_proyecto_limpio.ipynb` en VS Code o Jupyter, seleccionar el intérprete de `.venv` y ejecutar las celdas en orden.
6. Abrir la URL local que muestra Gradio (habitualmente `http://127.0.0.1:7860`).

**Importante:** `rag_proyecto_limpio.ipynb` **no reconstruye la base**. Necesita que `chroma_db/` esté en la raíz del proyecto, con la colección ya indexada. Si se comparte el repositorio sin esa carpeta, el notebook no podrá realizar consultas. Antes de publicar, verificar el tamaño de la base y que GitHub permita subirla; si no es viable, habrá que añadir un procedimiento reproducible de indexación.

## Pruebas realizadas

Las siguientes cinco consultas se probaron en la versión de desarrollo. En la versión limpia se verificó además una consulta sobre el máximo de proyectos.

| Tipo | Pregunta | Resultado observado |
|---|---|---|
| Con respuesta | ¿Cuántos proyectos sociales puede presentar una organización? | Respondió: hasta 3 proyectos sociales. |
| Con respuesta | ¿Qué servicios de asesoramiento y contención ofrece la Línea 144? | Respuesta pertinente; detalles por contrastar con fragmentos. |
| Con respuesta | ¿Qué servicios ofrece la Ciudad de Buenos Aires para personas en situación de calle? | Identificó CIS, Red de Atención y Línea de Atención Social Inmediata; verificar datos concretos. |
| Sin respuesta documental | ¿Quién ganó el Mundial de Fútbol de 2022? | Indicó que no encontró información en los documentos. |
| Sin respuesta documental | ¿Cuál es el precio actual del dólar en Argentina? | Indicó que no encontró información en los documentos. |

## Limitaciones y posibles mejoras

- Los modelos se ejecutaron localmente principalmente en **CPU**, con tiempos de respuesta elevados.
- Algunas respuestas fueron demasiado generales o mezclaron objetivos institucionales con recomendaciones prácticas.
- La recuperación de fragmentos relevantes no garantiza que el modelo los interprete correctamente.
- Se podrían evaluar modelos más eficientes, una recuperación con reordenamiento (*reranking*) y controles adicionales de fundamentación.

## Alcance

Este asistente es un **prototipo académico** de consulta documental. Las respuestas pueden contener errores y no reemplazan la lectura de los documentos originales ni la orientación profesional o institucional.
