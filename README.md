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

asistente-proyectos-sociales-rag/
├── README.md
├── requirements.txt
├── .gitignore
├── asistente_rag_proyectos_sociales.ipynb
├── datos/
│   ├── 2026 Instructivo Convocatoria Proyectos Sociales.pdf
│   ├── 20260827_GUIA VIOLENCIA DE GENERO 2026.pdf
│   └── 20260921_GUIA DE SERVICIOS SOCIALES 2026.pdf
└── chroma_db/
    └── Base vectorial ChromaDB

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

5. Abrir `asistente_rag_proyectos_sociales.ipynb` en VS Code o Jupyter, seleccionar el intérprete de `.venv` y ejecutar las celdas en orden.
6. Abrir la URL local que muestra Gradio (habitualmente `http://127.0.0.1:7860`).

**Nota:** El repositorio incluye la base vectorial ChromaDB con los 491 fragmentos previamente indexados. Esto permite realizar consultas sobre los documentos sin necesidad de repetir el proceso de generación de embeddings. Para ejecutar el asistente, se deben instalar las dependencias, descargar los modelos de Ollama y conservar la estructura de carpetas.

## Pruebas realizadas


## Pruebas realizadas

Se realizaron pruebas funcionales para evaluar la recuperación de información, la generación de respuestas y el comportamiento del asistente ante consultas ajenas a los documentos disponibles.

| Tipo de consulta | Pregunta | Resultado observado |
|---|---|---|
| Proyectos sociales | ¿Cuántos proyectos sociales puede presentar una organización? | Respondió correctamente que una organización puede presentar hasta 3 proyectos sociales. |
| Violencia de género | ¿Qué servicios de asesoramiento y contención ofrece la Línea 144? | Recuperó información sobre asistencia gratuita, confidencial y atención permanente. |
| Servicios sociales | ¿Qué servicios ofrece la Ciudad de Buenos Aires para personas en situación de calle? | Identificó Centros de Inclusión Social, la Red de Atención y la Línea de Atención Social Inmediata. |
| Consulta fuera de contexto | ¿Quién ganó el Mundial de Fútbol de 2022? | Indicó que no encontró esa información en los documentos. |
| Consulta fuera de contexto | ¿Cuál es el precio actual del dólar en Argentina? | Indicó que no encontró esa información en los documentos. |

Las pruebas permitieron verificar el funcionamiento del asistente con consultas relacionadas con las fuentes documentales y con preguntas ajenas a su contenido.
  

## Limitaciones y posibles mejoras


## Limitaciones y posibles mejoras

Durante el desarrollo y las pruebas del asistente se identificaron algunos aspectos susceptibles de mejora:

- **Tiempos de respuesta:** la ejecución local de los modelos, principalmente mediante CPU, genera tiempos de procesamiento elevados.
- **Precisión de las respuestas:** en determinadas consultas se observaron respuestas generales o información que no respondía directamente a la pregunta.
- **Recuperación de información:** aunque el sistema identifica fragmentos relevantes mediante búsqueda semántica, la calidad de las respuestas depende de la información recuperada y de su interpretación por parte del modelo.

Como posibles mejoras futuras, se contempla optimizar los tiempos de procesamiento, evaluar modelos de lenguaje más eficientes e incorporar técnicas adicionales para mejorar la selección de fragmentos relevantes.
  

## Alcance


## Resultados y conclusiones

El desarrollo permitió implementar un asistente inteligente basado en la arquitectura RAG (Retrieval-Augmented Generation), capaz de responder consultas en lenguaje natural a partir de documentación vinculada con proyectos sociales, servicios sociales y recursos de asistencia ante situaciones de violencia de género.

Mediante la integración de Python, Ollama, ChromaDB, LangChain y Gradio, se construyó una solución que funciona localmente, sin depender de servicios externos de generación de texto.

Las pruebas realizadas permitieron comprobar el funcionamiento de la recuperación semántica, la generación de respuestas contextualizadas y la identificación de consultas que no corresponden a la información disponible.

El proyecto demuestra cómo la inteligencia artificial generativa puede facilitar el acceso y la consulta de información documental, y constituye una base para futuras mejoras en precisión, rendimiento y experiencia de usuario.
  
