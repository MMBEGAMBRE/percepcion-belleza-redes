# Percepción de la belleza e influencia de las redes sociales

Proyecto de ciencia de datos · **Evidencia GA1-220501094-01-AA1-EV06 – Diagnosticar la calidad de datos: preparación, exploración y documentación**

**Aprendiz:** Maileth Begambre · Tecnólogo en Análisis y Desarrollo de Software (ADSO), SENA

## Descripción y propósito

El proyecto estudia la relación entre la exposición a contenido de belleza en redes sociales, la comparación de la apariencia física y los cambios que jóvenes de 18 a 25 años reportan en su percepción de la belleza y en sus preferencias de moda o maquillaje. Da continuidad a las evidencias EV02 (pregunta y variables) y EV03 (búsqueda de fuentes de datos).

Esta entrega hace el **diagnóstico inicial de calidad** de los datasets seleccionados con Python y Pandas: carga, inspección de la estructura, revisión de faltantes, tipos de datos e inconsistencias, y propuesta de acciones de limpieza. **No se modifica ningún dato.**

## Pregunta analítica

> ¿Qué relación existe entre la frecuencia de exposición a contenido de belleza en redes sociales, la comparación de la apariencia con otras personas y los cambios que reportan los jóvenes de 18 a 25 años en su percepción de la belleza y sus preferencias de moda o maquillaje?

El análisis separa tres dimensiones (**exposición**, **comparación** y **resultado percibido**), es descriptivo y exploratorio, y no afirma causalidad.

## Datasets

| Dataset | Fuente | Formato | Filas × columnas | ¿Incluido? | Papel en el proyecto |
|---|---|---|---|---|---|
| Pritchard (2021), *Social Media and Body Image* | [Mendeley Data](https://doi.org/10.17632/49v6fx7ssg.1), CC BY 4.0 | `.sav` | 404 × 246 | ✅ `data/raw/` | Antecedente con datos reales: exposición, comparación e imagen corporal |
| Engeln et al. (2020), *Instagram vs. Facebook* | [Mendeley Data](https://doi.org/10.17632/w6hwvkc7b7.1), CC BY 4.0 | `.sav` | 308 × 70 | ✅ `data/raw/` | Antecedente con datos reales: exposición controlada, comparación y satisfacción |
| DANE ENTIC Hogares 2024 | [Microdatos DANE](https://microdatos.dane.gov.co/index.php/catalog/909) | CSV `;` | 148.700 × 377 | ❌ (ver abajo) | Complementario: contexto de uso de redes en Colombia |
| Encuesta propia | Instrumento propio | CSV | — | ❌ aún no aplicada | Fuente principal prevista. Diseño en [`data/diccionario_datos.md`](data/diccionario_datos.md) |

Las citas, licencias y hashes SHA-256 de cada archivo están en [`data/raw/README.md`](data/raw/README.md).

**¿Por qué el DANE no está en el repositorio?** Su CSV pesa unos 110 MB (GitHub no acepta archivos de más de 100 MB) y la ficha del catálogo no declara una licencia abierta. El cuaderno funciona sin él: si no lo encuentra, omite esas celdas y lo indica.

## Estructura del repositorio

```text
percepcion-belleza-redes/
├── README.md
├── requirements.txt              ← dependencias generadas con pip freeze
├── .gitignore
├── data/
│   ├── raw/                      ← archivos originales, sin modificar
│   │   ├── README.md             ← fuentes, licencias e instrucciones del DANE
│   │   ├── pritchard_2021_social_media_body_image.sav
│   │   ├── engeln_2020_instagram_facebook.sav
│   │   └── dane/                 ← (ignorada por Git) CSV del DANE
│   ├── diccionarios/             ← código → etiqueta de Pritchard y Engeln (generados por el cuaderno)
│   ├── diccionario_datos.md      ← diseño de la encuesta propia
│   └── plantilla_encuesta.csv    ← encabezados esperados de la encuesta (sin datos)
├── notebooks/
│   ├── 00_entorno.ipynb          ← comprobación del entorno
│   └── diagnostico_calidad.ipynb ← carga, inspección y diagnóstico
└── reports/
    ├── informe_diagnostico.md    ← informe técnico
    ├── tabla_estructural.csv     ← generada por el cuaderno
    └── tabla_calidad.csv         ← generada por el cuaderno
```

## Cómo reproducir el diagnóstico

Requisitos: **Python 3.12 o superior** y **Git**. Comandos para Windows PowerShell.

1. Clonar el repositorio y entrar a la carpeta:

   ```powershell
   git clone https://github.com/MMBEGAMBRE/percepcion-belleza-redes.git
   cd percepcion-belleza-redes
   ```

2. Crear y activar el entorno virtual, e instalar las dependencias:

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install -r requirements.txt
   ```

   Si PowerShell bloquea la activación, ejecuta una vez `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. En macOS o Linux, activa con `source .venv/bin/activate`.

3. *(Opcional)* Descargar el DANE siguiendo [`data/raw/README.md`](data/raw/README.md) y dejar el CSV y el diccionario en `data/raw/dane/`.

4. Abrir los cuadernos de una de estas dos formas:
   - **JupyterLab:** con el entorno activo, ejecuta `jupyter lab` y abre `notebooks/`.
   - **VS Code:** abre el `.ipynb`, elige el kernel `.venv` (*Select Kernel*) y usa **Run All**.

5. Ejecutar primero `00_entorno.ipynb`, que debe mostrar las versiones de Python, pandas y pyreadstat. Después ejecutar `diagnostico_calidad.ipynb` completo. El cuaderno regenera `data/diccionarios/` y las tablas de `reports/`.

### Dependencias principales

| Paquete | Para qué |
|---|---|
| `pandas` | Carga e inspección de los DataFrames |
| `pyreadstat` | Lectura de los archivos SPSS `.sav` y de sus etiquetas |
| `openpyxl` | Lectura del diccionario oficial del DANE (`.xlsx`) |
| `jupyterlab` | Ejecución de los cuadernos |

## Resultados

El informe completo está en [`reports/informe_diagnostico.md`](reports/informe_diagnostico.md). Resumen:

- Los tres datasets cargan correctamente y no tienen filas duplicadas, pero **no están listos para analizar tal como vienen**.
- **El problema principal es de significado de los códigos:** en Pritchard, la escala de comparación de apariencia (`Q65`) tiene valores 0–4 y etiquetas 1–5; en Engeln, las etiquetas de la escala de satisfacción (BISS) están fuera de orden.
- La mayoría de los faltantes son **justificados** (preguntas condicionadas y grupo de control). En Pritchard hay faltantes **ocultos**: 6.041 de 10.100 celdas de texto vacías que `info()` no cuenta.
- En el DANE, el factor de expansión (`FEX_c`) es texto con coma decimal y los códigos territoriales perdieron sus ceros iniciales.
- **Ningún dataset mide moda o maquillaje** ni representa a jóvenes colombianos de 18 a 25 años: la encuesta propia es indispensable para responder la pregunta.

## Metodología

Se siguió la metodología de la Guía 4 (*Explorando Instacart*): **código → salida → interpretación** para cada comando, y una tabla de diagnóstico estructural. Instacart solo fue la referencia de método; sus datos no se usan en este proyecto. El entorno se organizó según la guía *Proyecto práctica: entorno y cuaderno*: ramas `main`, `develop` y `feat/*`, con tags por versión.

| Tag | Contenido |
|---|---|
| v0.1 | Estructura, `.gitignore` y entorno virtual |
| v0.2 | pandas, pyreadstat y `requirements.txt` |
| v0.3 | JupyterLab |
| v0.4 | Cuaderno `00_entorno.ipynb` |
| v0.5 | Datasets y documentación de fuentes |
| v0.6 | Carga e inspección estructural |
| v0.7 | Diagnóstico de calidad |
| v0.8 | Informe técnico y README |
