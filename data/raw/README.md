# Datos originales (`data/raw/`)

Los archivos de esta carpeta se guardan **tal como se descargaron**. No se editan a mano: cualquier preparación se hará en una etapa posterior y en otra carpeta.

| Archivo en el repositorio | Dataset | ¿Incluido en GitHub? |
|---|---|---|
| `pritchard_2021_social_media_body_image.sav` | Pritchard (2021) | Sí (CC BY 4.0) |
| `engeln_2020_instagram_facebook.sav` | Engeln et al. (2020) | Sí (CC BY 4.0) |
| `dane/BASE DEFINITIVA ENTIC HOGARES 2024.csv` | DANE ENTIC Hogares 2024 | **No** (ver instrucciones) |
| `encuesta/` | Encuesta propia | **No** (aún no aplicada; se publicará solo anonimizada) |

## 1. Pritchard (2021) – Social Media and Body Image

- **Cita:** Pritchard, M. (2021). *Social Media and Body Image* (Versión 1) [Conjunto de datos]. Mendeley Data. https://doi.org/10.17632/49v6fx7ssg.1
- **Institución:** Boise State University, College of Arts and Sciences.
- **Descripción de la ficha:** preguntas a estudiantes universitarios sobre uso de redes sociales, percepción de su impacto en la imagen corporal, autoobjetivación, interiorización y comparación de apariencia.
- **Licencia:** Creative Commons Attribution 4.0 International (CC BY 4.0).
- **Archivo original:** `Social Media and Body Image, emerging adults.sav` (renombrado en este repositorio). Mendeley publica el archivo dos veces con el mismo contenido; se usa una sola copia.
- **SHA-256:** `715b778d357b458674a17549ea044143e77402e34ac41ff032c476d955229757`

## 2. Engeln, Zola, Imundo y Loach (2020) – Instagram vs. Facebook

- **Cita:** Engeln, R., Zola, A., Imundo, M., & Loach, R. (2020). *Data for: Compared to Facebook, Instagram Use Causes More Appearance Comparison and Greater Body Dissatisfaction in College Women* (Versión 1) [Conjunto de datos]. Mendeley Data. https://doi.org/10.17632/w6hwvkc7b7.1
- **Descripción de la ficha:** experimento que compara siete minutos de Instagram, siete minutos de Facebook y siete minutos de un juego (control) en mujeres universitarias de EE. UU.
- **Licencia:** CC BY 4.0.
- **Archivo original:** `deidentified data file 8.26.19.sav` (renombrado en este repositorio).
- **SHA-256:** `e700635a950de1e0d2e8216c832aa5857189498b2f0c1998bd5d393ea3d6dba1`

## 3. DANE – ENTIC Hogares 2024 (complementario, no incluido)

- **Cita:** Departamento Administrativo Nacional de Estadística (DANE). (2025). *Encuesta de Tecnologías de la Información y las Comunicaciones en Hogares – ENTIC Hogares 2024* [Microdatos]. https://microdatos.dane.gov.co/index.php/catalog/909
- **Por qué no se sube:** el CSV pesa unos 110 MB (GitHub no acepta archivos de más de 100 MB) y la ficha del catálogo no declara una licencia abierta; remite a la Ley 1032 de 2006 y a protocolos de acceso del DANE.
- **Uso en el proyecto:** solo contexto colombiano sobre uso de internet y redes sociales (no mide belleza ni comparación de apariencia).

### Cómo obtenerlo

1. Entrar a https://microdatos.dane.gov.co/index.php/catalog/909/get-microdata y descargar `BDATOS-ENTIC_H-2024.zip` (33,5 MB).
2. Crear la carpeta `data/raw/dane/` y descomprimir allí el ZIP. Solo se necesita `BASE DEFINITIVA ENTIC HOGARES 2024.csv` (separador `;`).
3. Opcional: descargar el diccionario de datos `DSO-ENTIC_H-DDT-001.xlsx` desde la pestaña *Materiales relacionados* del mismo catálogo y guardarlo en `data/raw/dane/`. El CSV no trae etiquetas; el significado de cada código (`IV12R13C1`, etc.) está en ese diccionario.
4. SHA-256 del ZIP descargado el 4 de noviembre de 2026: `95a17ea9aff0fb8a14a45f563446a79773189a44c09ac39ae8437947bb5a4f3d`

## 4. Encuesta propia (pendiente)

- Diseño de variables: [`../diccionario_datos.md`](../diccionario_datos.md). Encabezados esperados: [`../plantilla_encuesta.csv`](../plantilla_encuesta.csv).
- Cuando existan respuestas, el CSV exportado de Google Forms se guardará en `data/raw/encuesta/`, sin correos ni datos personales.
- **No se deben agregar respuestas de ejemplo ni simuladas en esta carpeta.**
