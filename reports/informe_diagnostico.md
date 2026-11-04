# Informe técnico: diagnóstico inicial de calidad de datos

**Proyecto:** Percepción de la belleza e influencia de las redes sociales
**Aprendiz:** Maileth Begambre · Tecnólogo en Análisis y Desarrollo de Software (ADSO), SENA
**Evidencia:** GA1-220501094-01-AA1-EV06 – Diagnosticar la calidad de datos: preparación, exploración y documentación
**Fecha:** 4 de noviembre de 2026

Todas las cifras de este informe provienen de [`notebooks/diagnostico_calidad.ipynb`](../notebooks/diagnostico_calidad.ipynb) y de las tablas que ese cuaderno exporta: [`tabla_estructural.csv`](tabla_estructural.csv) y [`tabla_calidad.csv`](tabla_calidad.csv).

---

## 1. Proyecto y pregunta analítica

El proyecto, formulado en las evidencias EV02 y EV03, estudia la relación entre la exposición a contenido de belleza en redes sociales, la comparación de la apariencia física y los cambios que reportan jóvenes en su percepción de la belleza.

> **Pregunta analítica:** ¿Qué relación existe entre la frecuencia de exposición a contenido de belleza en redes sociales, la comparación de la apariencia con otras personas y los cambios que reportan los jóvenes de 18 a 25 años en su percepción de la belleza y sus preferencias de moda o maquillaje?

**Objetivo general.** Analizar, de forma descriptiva y exploratoria, la relación entre la frecuencia de exposición a contenido de belleza en redes sociales, la comparación de la apariencia con otras personas y los cambios que reportan jóvenes de 18 a 25 años en su percepción de la belleza y sus preferencias de moda o maquillaje.

**Objetivos específicos.**
1. Describir la frecuencia de exposición y los tipos de contenido de belleza que consumen las personas encuestadas.
2. Caracterizar la frecuencia con la que comparan su apariencia con la de otras personas.
3. Describir los cambios que reportan en sus preferencias de moda o maquillaje, su ideal de belleza, su satisfacción con su apariencia y la influencia de su círculo social.
4. Explorar diferencias en la comparación y en los cambios percibidos entre grupos de exposición baja, media y alta, sin afirmar causalidad.

El análisis separa tres dimensiones: **exposición**, **comparación** y **resultado percibido**. Es descriptivo y exploratorio, y no se afirmará causalidad.

## 2. Descripción de los datasets

| Dataset | Fuente y licencia | Formato | Contenido | Relación con la pregunta |
|---|---|---|---|---|
| **Pritchard (2021)** | Mendeley Data, [doi:10.17632/49v6fx7ssg.1](https://doi.org/10.17632/49v6fx7ssg.1). CC BY 4.0 | SPSS `.sav` | Encuesta a estudiantes universitarios de EE. UU. sobre uso de Instagram, Twitter y Snapchat, comparación de apariencia, interiorización de ideales e imagen corporal | Exposición (uso y tiempo en redes), comparación (`Q65`, `PACSR`) y resultado (satisfacción corporal) |
| **Engeln et al. (2020)** | Mendeley Data, [doi:10.17632/w6hwvkc7b7.1](https://doi.org/10.17632/w6hwvkc7b7.1). CC BY 4.0 | SPSS `.sav` | Experimento con mujeres universitarias de EE. UU.: 7 minutos de Instagram, Facebook o un juego de control, con medidas antes y después | Exposición controlada, comparación durante la tarea y satisfacción con la apariencia (BISS) |
| **DANE ENTIC Hogares 2024** | [Catálogo de microdatos del DANE](https://microdatos.dane.gov.co/index.php/catalog/909). Sin licencia abierta declarada | CSV (`;`) | Encuesta oficial de uso de TIC en hogares de Colombia | Contexto colombiano de exposición: uso de internet y redes sociales |
| **Encuesta propia** | Instrumento propio (diseño en `data/diccionario_datos.md`) | CSV previsto | Variables de la sección 6 del EV02 | Fuente principal prevista. **Aún no se ha aplicado** |

Los dos datasets de Mendeley se incluyen en el repositorio porque su licencia lo permite. El del DANE no se publica, porque su CSV pesa 110 MB y la ficha no declara una licencia abierta; `data/raw/README.md` explica cómo descargarlo. Los artículos científicos citados en las evidencias anteriores (Fardouly et al., 2015; Cohen et al., 2017; el estudio de España de 2023) son antecedentes bibliográficos, no datasets.

## 3. Resultados de la inspección estructural

Para cada DataFrame se ejecutaron `head()`, `tail()`, `shape`, `columns`, `dtypes`, `info()`, `describe()` y `describe(include="object")`. Los `.sav` se cargaron con `pd.read_spss(..., convert_categoricals=False)` para conservar los códigos originales, y sus etiquetas se guardaron como diccionarios en `data/diccionarios/`.

| Dataset | Filas | Columnas | Tipos de datos | Observaciones |
|---|---|---|---|---|
| Pritchard (2021) | 404 | 246 | float64: 221, str: 25 | Cada fila es una persona. Las respuestas cerradas son códigos guardados como `float64`. Las respuestas abiertas vacías están como texto `""`. Los nombres de columna son códigos del formulario (`Q16`, `Q65_1`…) |
| Engeln et al. (2020) | 308 | 70 | float64: 70 | Cada fila es una participante, ordenada por grupo (`Condition`). No tiene columnas de texto, así que `describe(include="object")` no aplica |
| DANE ENTIC Hogares 2024 | 148.700 | 377 | float64: 347, int64: 25, str: 5 | Cada fila es una persona; hay 53.343 encuestas distintas (`UID_ENCUESTA`). Las columnas son códigos de pregunta sin etiquetas; su significado está en el diccionario oficial |

**Criterio de lectura de `describe()`.** Casi todas las columnas numéricas son **códigos de respuesta** (por ejemplo, `1 = Never`, `2 = Rarely`). Por eso, de `describe()` solo se interpretaron `count`, `min` y `max`; el `mean` y la `std` de categorías no tienen significado.

## 4. Valores faltantes, tipos de datos e inconsistencias

### 4.1 Valores faltantes

| Dataset | Resultado | Tipo de faltante |
|---|---|---|
| Pritchard | 220 de 246 columnas con nulos (3.130 celdas). Los bloques de Twitter y Snapchat concentran la mayoría: `Q34` solo tiene respuesta cuando `Q32 = Yes` (184 de 184) | **Justificado:** preguntas condicionadas |
| Pritchard | 6.041 de 10.100 celdas de texto contienen `""`, y en 16 de las 25 columnas de texto más de la mitad está vacía; `info()` reporta 0 nulos en ellas | **Oculto:** no lo detecta `isna()` |
| Engeln | Las variables de la tarea tienen 106 nulos, todos en `Condition = 3` (grupo de control); hay 3 nulos adicionales en los grupos 1 y 2 | **Justificado por diseño**, más 3 no esperados |
| Engeln | `BISSTotalPre` tiene 8 nulos, en las mismas filas a las que les falta algún ítem | No esperado, pero explicable |
| DANE | 347 de 377 columnas con nulos, 194 con más de la mitad vacía. `iv12r13c1` tiene respuesta si y solo si `iv10r5c1 = 1` (usa redes). `iv13r20c2` está 100 % vacía | **Justificado:** saltos del cuestionario |

### 4.2 Tipos de datos

- **Pritchard y Engeln:** 200 de las 221 columnas numéricas de Pritchard y 56 de las 70 de Engeln tienen etiquetas de valor, es decir, son categorías guardadas como `float64`. Todos los códigos son enteros.
- **DANE:** `FEX_c`, el factor de expansión, es texto con coma decimal (`149,227493`). Cambiando la coma por punto se convierte el 100 % de los valores, que suman 52.933.447, una magnitud coherente con la población de Colombia. El diccionario declara 376 variables como texto (`VARCHAR2`), pero pandas las leyó como números, y así se perdieron los ceros iniciales en 14.494 filas (Antioquia `05` → `5`, Medellín `05001` → `5001`).

### 4.3 Inconsistencias

- **Pritchard, códigos fuera del diccionario:** 97 variables y 14.503 celdas tienen valores sin etiqueta. En los 40 ítems de **`Q65` (comparación de apariencia)** los datos van de 0 a 4, mientras el diccionario va de `1 = Never` a `5 = Always`; `PACSR` también va de 0 a 4. En `Q64` pasa algo similar (datos 1–5, etiquetas 2–6), y también en `Q15` y `Q23`.
- **Pritchard, escalas:** `Q17` (tiempo en Instagram) no tiene la opción de 30 a 60 minutos, y en `Q17_1` la etiqueta del código 4 está fuera de orden.
- **Pritchard, identificador y redundancias:** el `id` 10720 aparece en 2 filas que difieren en 204 columnas. El género está tres veces (`gender`, `GenderRE` y `filter_$`), y `GenderRE` deja vacías 5 respuestas *Transgender*/*Other*.
- **Engeln, escala BISS:** las etiquetas están fuera de orden (en `BISS4pre`, `4 = Moderately physically unattractive` está entre opciones *attractive*). Además, los ítems van en direcciones opuestas y `BISSTotalPre` no coincide con el promedio simple en ninguna de las 300 filas, así que su cálculo no está documentado.
- **Edades fuera de 18 a 25:** 11 en Pritchard (17 y 26 años) y 1 en Engeln (26 años). El DANE incluye todas las edades (de 0 a 107).
- **DANE, nombres de columna:** están en minúscula y el diccionario los escribe en mayúscula.

**Controles que se cumplen:** no hay filas duplicadas en ningún dataset; en Engeln los porcentajes de tiempo suman exactamente 100 en las 202 filas que los tienen; en el DANE las variables pertinentes no tienen códigos fuera de su dominio.

## 5. Principales hallazgos de calidad por dataset

| Dataset | Problema identificado | Evidencia encontrada | Posible impacto | Acción propuesta |
|---|---|---|---|---|
| Pritchard (2021) | Respuestas abiertas vacías guardadas como texto, no como nulo | 6041 de 10100 celdas de texto son ""; 16 de 25 columnas de texto tienen más de la mitad vacía; info() reporta 0 nulos en ellas | Se subestiman los faltantes; describe(include='object') toma el vacío como respuesta más frecuente | Reemplazar "" y textos solo con espacios por NaN antes de contar faltantes |
| Pritchard (2021) | Faltantes por preguntas condicionadas (justificados) | 220 columnas con nulos; Q34 solo tiene respuesta cuando Q32 = Yes; Q41 solo tiene 54 respuestas (usuarios que publican fotos en Twitter) | Si se tratan como olvidos, se exagera la falta de respuesta y se calculan porcentajes sobre una base equivocada | Documentar el filtro de cada bloque y calcular porcentajes solo sobre quienes debían responder; no imputar |
| Pritchard (2021) | Códigos que no coinciden con el diccionario de etiquetas | 97 variables y 14503 celdas sin etiqueta (bloques Q15, Q23, Q64, Q65); Q65 (comparación) tiene valores 0–4 y etiquetas 1–5; PACSR va de 0 a 4 | Afecta la dimensión de comparación: con el diccionario actual cada respuesta se leería una categoría más abajo | Verificar la codificación con el artículo o instrumento original; corregir las etiquetas (o recodificar) y documentar la decisión |
| Pritchard (2021) | Respuestas categóricas guardadas como float64 | 200 de 221 columnas numéricas tienen etiquetas de valor; todos los códigos son enteros | pandas permite promediar categorías; los mean y std de describe() no tienen sentido | Convertir a Int64 o a category (ordenada en las escalas); describir con frecuencias y medianas |
| Pritchard (2021) | Edades fuera de la población de 18 a 25 años | 11 fuera de rango (17 y 26 años), 1 sin dato, 392 dentro del rango | Se incluirían personas que no pertenecen a la población del proyecto | Filtrar 18 a 25 años y reportar cuántos registros se excluyen |
| Pritchard (2021) | Identificador repetido | id 10720 aparece en 2 filas con 204 columnas distintas; duplicated() = 0 | El id no identifica a una sola persona; puede causar errores al unir o contar | Crear un identificador propio por fila y registrar el caso; no eliminar ninguna de las dos filas |
| Pritchard (2021) | Escalas con categorías faltantes o desordenadas | Q17 pasa de '15-30 min' a '1-2 hr' (no hay 30–60 min); en Q17_1 el código 4 'Once a month' está después de '2-3 times a month' | Imprecisión en el tiempo de uso (exposición) y riesgo de ordenar mal una escala | Agrupar Q17 en los rangos del EV02 (<1 h, 1–3 h, >3 h) señalando la limitación; verificar Q17_1 con el instrumento |
| Pritchard (2021) | Variables redundantes, técnicas o sin documentar | filter_$ = 1 en 235 filas, igual que gender = 2; GenderRE deja vacías 5 respuestas Transgender/Other; 21 variables sin texto de pregunta (puntajes) | Confusión sobre cuál variable usar; pérdida de categorías de género | Usar gender como variable de género; excluir filter_$; documentar los puntajes con el artículo |
| Engeln et al. (2020) | Faltantes por diseño del experimento (justificados) | Las variables de la tarea tienen 106 nulos, todos en Condition = 3 (control); 3 nulos adicionales en los grupos 1 y 2 | Si se analizan los tres grupos juntos, el control aparece como 'sin respuesta' | Analizar las variables de la tarea solo para Condition 1 y 2; revisar las 3 filas con faltantes no esperados |
| Engeln et al. (2020) | Puntajes totales vacíos por ítems faltantes | BISSTotalPre tiene 8 nulos, en las mismas filas con algún ítem BISS 'pre' vacío | Se reduce el número de casos para comparar antes y después | Reportar el número de casos de cada análisis; no imputar |
| Engeln et al. (2020) | Etiquetas de la escala BISS fuera de orden | BISS4pre: 4 = 'Moderately physically unattractive' entre opciones 'attractive'; BISS5pre: 3 = 'About the same' entre opciones 'worse' | Interpretación incorrecta de la satisfacción con la apariencia | Contrastar con el instrumento BISS original y corregir las etiquetas en el diccionario |
| Engeln et al. (2020) | Ítems en direcciones opuestas y total sin documentar | BISS1pre: 1 = muy insatisfecha; BISS2pre: 1 = muy satisfecha; BISSTotalPre coincide con el promedio simple en 0 filas | Promediar los ítems sin invertirlos daría puntajes erróneos | Usar BISSTotalPre/Post documentando su cálculo, o invertir los ítems según el instrumento |
| Engeln et al. (2020) | Edad fuera de rango | 1 participante de 26 años | Incluye a una persona fuera de la población | Filtrar 18 a 25 años |
| Engeln et al. (2020) | Escala con etiquetas solo en los extremos | appearancethoughts solo etiqueta 1 y 7; 173 respuestas con valores 2 a 6 | Bajo: es una escala anclada, pero el significado intermedio no está escrito | Documentar la escala en el diccionario del proyecto |
| DANE ENTIC 2024 | Factor de expansión guardado como texto con coma decimal | FEX_c es str; 148700 de 148700 valores se convierten cambiando ',' por '.'; suman 52,933,447 | No se puede ponderar; los resultados sin ponderar no representan a la población | Convertir con str.replace(',', '.') y pd.to_numeric antes de cualquier estimación |
| DANE ENTIC 2024 | Códigos categóricos leídos como números (se pierden ceros iniciales) | El diccionario declara 376 variables como VARCHAR2; 14494 filas con departamento de un dígito (05 → 5, 05001 → 5001) | Errores al unir con otras fuentes por código DIVIPOLA y riesgo de tratar códigos como cantidades | Leer los códigos territoriales como texto (dtype=str) o completar con zfill; tratar respuestas como category |
| DANE ENTIC 2024 | Nombres de columnas distintos del diccionario | 372 columnas en minúscula y el diccionario en mayúscula; 5 columnas con otro formato (ID, UID_ENCUESTA, UUID, UUID_1, FEX_c) | Las búsquedas y uniones con el diccionario fallan si no se normalizan | Normalizar los nombres (por ejemplo, a minúscula) y conservar el nombre original en el diccionario |
| DANE ENTIC 2024 | Faltantes por saltos del cuestionario y una columna vacía | 347 columnas con nulos, 194 con más de la mitad vacía; iv12r13c1 responde si y solo si iv10r5c1 = 1; iv13r20c2 100 % vacía | Porcentajes mal calculados si se usa el total de filas como base | Calcular cada indicador sobre la población a la que aplica la pregunta; excluir iv13r20c2 |
| DANE ENTIC 2024 | Población y cobertura distintas a las del proyecto | Edades de 0 a 107 años; 17048 filas de 18 a 25 años, 186 en Medellín; ninguna variable sobre belleza, moda o maquillaje | Solo sirve como contexto de exposición; muestra pequeña para Medellín | Filtrar 18 a 25 años, ponderar con FEX_c y usarlo solo como contexto nacional |
| Encuesta propia | Datos todavía no recolectados | No existe archivo de respuestas en data/raw/encuesta/ | No se puede diagnosticar ni responder la parte de moda, maquillaje y población colombiana | Aplicar la encuesta según data/diccionario_datos.md y repetir este diagnóstico con sus respuestas |

### Limitaciones para responder la pregunta analítica

| Limitación | Pritchard | Engeln | DANE |
|---|---|---|---|
| Población | Universitarios de EE. UU. | Mujeres universitarias de EE. UU. | Colombia, todas las edades |
| Variables de moda, maquillaje o belleza | 0 | 0 | 0 |
| Exposición | Instagram, Twitter y Snapchat (sin TikTok) | 7 minutos controlados | Uso general de internet y redes |
| Comparación | Sí, con códigos que no coinciden con el diccionario | Solo durante la tarea | No |
| Resultado percibido | Satisfacción e imagen corporal | Satisfacción antes y después | No |
| Diseño | Encuesta transversal | Experimento | Encuesta transversal oficial |

Las variables de moda, maquillaje o belleza se contaron buscando esas palabras (y "TikTok") en los textos de las preguntas y de las etiquetas de los tres diccionarios. Las cifras del DANE son filas sin ponderar: solo indican cuántos registros hay disponibles, no porcentajes de la población.

## 6. Acciones de limpieza y preparación propuestas

En esta actividad no se ejecuta ninguna limpieza. Estas son las acciones propuestas para la siguiente etapa, ordenadas por prioridad:

| Prioridad | Acción | Dataset |
|---|---|---|
| 1 | Verificar con los instrumentos originales la correspondencia código ↔ etiqueta de `Q65`, `Q64`, `Q15` y `Q23`, y corregir las etiquetas o recodificar, documentando la decisión | Pritchard |
| 1 | Corregir las etiquetas BISS y documentar cómo se calculan `BISSTotalPre` y `BISSTotalPost` | Engeln |
| 2 | Convertir `""` en `NaN` en las respuestas abiertas | Pritchard |
| 2 | Convertir `FEX_c` a número y leer los códigos territoriales como texto | DANE |
| 3 | Filtrar 18 a 25 años y reportar cuántos registros se excluyen | Los tres |
| 3 | Definir la base de cada pregunta condicionada: solo quienes debían responderla | Los tres |
| 4 | Convertir los códigos a `category` o `Int64` y describirlos con frecuencias y medianas | Pritchard, Engeln |
| 4 | Crear un identificador propio en Pritchard; excluir `filter_$` e `iv13r20c2`; normalizar los nombres de columna del DANE | Pritchard, DANE |
| 5 | Aplicar la encuesta propia y diagnosticarla con el mismo cuaderno | Encuesta |

## 7. Conclusiones

1. **Los tres datasets se pueden cargar y usar, pero no están listos para analizar tal como vienen.** La inspección estructural no mostró archivos dañados ni filas duplicadas; los problemas están en cómo se representan los datos.
2. **El problema más grave es de significado, no de cantidad.** En Pritchard, la escala de comparación de apariencia (`Q65`), central para la pregunta del proyecto, tiene códigos que no coinciden con su diccionario; en Engeln, la escala de satisfacción (BISS) tiene etiquetas fuera de orden. Mientras no se verifiquen con los instrumentos originales, esas variables no deben interpretarse.
3. **La mayoría de los faltantes son justificados** (preguntas condicionadas y grupo de control) y no deben imputarse: deben tratarse definiendo bien la base de cada indicador. La excepción son los faltantes ocultos de Pritchard (texto vacío), que hay que convertir en nulos para poder contarlos.
4. **Los códigos son categorías.** En los tres datasets las respuestas están codificadas como números; describirlas con promedios llevaría a conclusiones falsas. Se usarán frecuencias, porcentajes y medianas, como se planteó en el EV02.
5. **Los datos disponibles no alcanzan para responder la pregunta completa.** Ninguno mide cambios en preferencias de moda o maquillaje, y los de Mendeley no representan a jóvenes colombianos. Pritchard y Engeln sirven como antecedentes con datos reales para exposición y comparación, y el DANE como contexto nacional. **El siguiente paso indispensable es aplicar la encuesta propia** (diseñada en `data/diccionario_datos.md`) y diagnosticarla con este mismo cuaderno antes de escribir resultados del proyecto.

## Referencias

- DANE. (2025). *Encuesta de Tecnologías de la Información y las Comunicaciones en Hogares – ENTIC Hogares 2024* [Microdatos]. https://microdatos.dane.gov.co/index.php/catalog/909
- Engeln, R., Zola, A., Imundo, M., & Loach, R. (2020). *Data for: Compared to Facebook, Instagram use causes more appearance comparison and greater body dissatisfaction in college women* (Versión 1) [Conjunto de datos]. Mendeley Data. https://doi.org/10.17632/w6hwvkc7b7.1
- McKinney, W. (2022). *Python for data analysis* (3.ª ed.). O'Reilly Media.
- Pritchard, M. (2021). *Social media and body image* (Versión 1) [Conjunto de datos]. Mendeley Data. https://doi.org/10.17632/49v6fx7ssg.1
