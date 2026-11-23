# Fase 2 del Proyecto

# Laboratorio 1: Python científico reproducible y legible

## 1. ¿Qué problema resuelve el proyecto?
Este proyecto implementa las primeras fases de la metodología CRISP-DM para el desarrollo de una herramienta computacional basada en aprendizaje profundo destinada a clasificar automáticamente la severidad de la retinopatía diabética mediante fotografías de fondo de ojo:

- **Fase 1 (Entendimiento del Negocio):** Estructura el problema clínico, define los requisitos del sistema, establece los criterios de éxito y deja documentada la base conceptual para el posterior desarrollo del modelo predictivo.
- **Fase 2 (Entendimiento de los Datos):** Implementa un pipeline modular, reproducible y testeable para la exploración del dataset. Esto incluye la limpieza estandarizada de datos (eliminación de anomalías), el análisis de métricas, la generación del reporte de calidad de datos (DQR), la exportación de estadísticas en JSON/CSV y la visualización automatizada de distribuciones clave (grados de retinopatía y riesgo de edema macular).

## 2. ¿Qué versión de Python solicita?
- Python: >= 3.13

## 3. Instrucciones de ejecución y flujo de trabajo reproducible
Para asegurar la reproducibilidad y verificar la calidad del código, el proyecto utiliza **uv** para la gestión de dependencias y entornos. Ejecute los siguientes comandos en orden en su terminal:

*Sincronizar el entorno virtual y las dependencias:*
   ```bash
   uv sync --locked

*Verificar el estilo y calidad del código con Ruff:*
   uv run ruff check .
   --> uv run ruff check --fix      
   uv run ruff format --check .
   --> uv run ruff format .

*Descargar y configurar los datos del dataset IDRiD:*
    uv run python scripts/download_data.py

*Ejecutar la suite de pruebas:*
    uv run pytest

*Reproducir el pipeline completo:*
    uv run python scripts/reproduce.py

*Para compilar el informe:*
*Fase 1:*
    latexmk -pdf -outdir=build/reports docs/fases_crisp_dm/informe/informe_fase1.tex
    Start-Process "build/reports/informe_fase1.pdf" 
*Fase 1-2:*
    latexmk -pdf -outdir=build/reports docs/fases_crisp_dm/informe/informe_fases_1_2.tex
    Start-Process "build/reports/informe_fases_1_2.pdf" 
*Fase 1-2-3:*
    latexmk -pdf -outdir=build/reports docs/fases_crisp_dm/informe/informe_fases_1_2_3.tex
    Start-Process "build/reports/informe_fases_1_2_3.pdf" 


La preparación se ejecuta después de asociar etiquetas e imágenes. El `Training Set` oficial se estratifica por `Retinopathy grade`: 70 % queda en `train` y el 30 % restante se agrega al `Testing Set` oficial para formar el conjunto final `test`. Por tanto, `test.csv` contiene dos procedencias identificadas en `test_origin`: `heldout_from_training` y `official_testing_set`. No se crea un conjunto `validation` independiente en este enfoque. Los histogramas RGB se calculan únicamente con las imágenes del `train.csv` resultante (el 70 %), nunca con las imágenes de test.

```bash
uv sync --locked
uv run pytest
uv run python scripts/reproduce.py
```

El pipeline requiere que se hayan descargado y ubicado los datos IDRiD en `data/raw/Disease_Grading/`, con `Groundtruths/` y `Original_Images/Training Set/`, `Original_Images/Testing Set/`. Para obtenerlos mediante el script del proyecto: `uv run python scripts/download_data.py` (la descarga puede requerir acceso/autenticación a Kaggle).

Artefactos principales: `data/processed/abt_v20261012/` (manifiestos y arreglos `.npy` por split), `results/phase3/` (distribución, validación anti-fuga, resumen de preprocesamiento y resumen general), `results/phase3/histograms/` (resumen de histogramas) y `figures/phase3/histograms/` (figuras). Las cifras reales solo se generan al ejecutar el pipeline sobre los datos descargados; no deben rellenarse a mano.

**Importante:** el preprocesamiento actual es determinista: conversión a RGB, resize con padding y división por 255. No estima medias/desviaciones ni ajusta transformaciones usando test.

Para compilar el informe integrado (desde la raíz del repositorio, después de la ejecución para incluir resultados reales):

```bash
latexmk -pdf -outdir=build/reports docs/fases_crisp_dm/informe/informe_fases_1_2_3.tex
```

El pipeline genera `results/phase3/phase3_results.tex`; al compilar después de la ejecución, el informe incorpora los conteos y verificaciones reales disponibles.
