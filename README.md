# Sentimiento por Marca

Aplicación Streamlit independiente para analizar exclusivamente el impacto reputacional de una marca mencionada en noticias. Esta versión conserva la lógica de la aplicación original:

- detección de la marca, aliases, acrónimos y variaciones ortográficas;
- extracción de contexto centrado en la marca;
- agrupación conservadora de republicaciones para evitar clasificar varias veces la misma noticia;
- propagación de resultados entre noticias equivalentes;
- clasificación `Positivo`, `Negativo` o `Neutro` con confianza y justificación;
- uso del mismo modelo OpenAI y del mismo pipeline `.pkl` opcional.

## Ejecutar localmente

Requiere Python 3.10 o superior.

```bash
python -m venv .venv
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Copia `.streamlit/secrets.toml.example` como `.streamlit/secrets.toml` y completa los valores:

```toml
APP_PASSWORD = "una-clave-local"
OPENAI_API_KEY = "sk-..."
```

Inicia la aplicación:

```bash
streamlit run app.py
```

`OPENAI_API_KEY` sólo es necesario cuando no se carga un modelo PKL. `APP_PASSWORD` protege la aplicación; si no quieres autenticación local, puedes establecer cualquier valor y usarlo al iniciar sesión.

## Formato de entrada

Sube un archivo `.xlsx`. En la interfaz se seleccionan la columna de título y la columna de resumen/aclaración. La detección automática busca nombres como `Título`, `Titulo`, `Titular`, `Headline`, `Resumen`, `Cuerpo` y `Descripción`.

Indica la marca principal y, opcionalmente, aliases separados por `;`, por ejemplo:

```text
Grupo Bancolombia;BanColombia;Bancolombia
```

## Modelos

- Sin PKL: usa el modelo de clasificación OpenAI configurado en `app.py` (`gpt-4.1-nano-2025-04-14`) y embeddings `text-embedding-3-small` para agrupar equivalencias.
- Con PKL: carga un pipeline compatible con `joblib.load()` y que exponga `.predict(textos)`. Las salidas `-1/0/1` y `Negativo/Neutro/Positivo` se normalizan automáticamente.

## Resultado

La tabla y el Excel descargable conservan las columnas originales y agregan:

`Tono IA`, `Confianza Tono`, `Marca encontrada`, `Contexto analizado`, `Coincidencia marca` y `Origen coincidencia`.

El contexto queda limitado a las frases donde aparece la marca o un alias, para que el tono refleje el impacto sobre la marca y no el tono general del artículo.

## Despliegue en Streamlit Community Cloud

1. Sube esta carpeta a un repositorio de GitHub.
2. Selecciona `app.py` como archivo principal.
3. En **Settings > Secrets**, agrega `APP_PASSWORD` y `OPENAI_API_KEY` con el formato TOML mostrado arriba.
4. No subas `.streamlit/secrets.toml`; está ignorado por Git.

