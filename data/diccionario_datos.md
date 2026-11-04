# Diccionario de datos – Encuesta propia "Belleza y redes sociales" (18 a 25 años)

> **Estado:** diseño. La encuesta **todavía no se ha aplicado** y no existe archivo de respuestas.
> Variables tomadas de la sección 6 de la evidencia GA1-220501094-01-AA1-EV02.
> Este diccionario describe el archivo **esperado**; en el diagnóstico (EV06) se compara contra los datos reales.

## Cómo leer la tabla
- **Pregunta:** texto que se escribe en el formulario.
- **Opciones:** respuestas permitidas, en el orden en que se muestran.
- **Tipo de dato esperado:** cómo debería verse la columna en Pandas después de la preparación.
- **Codificación:** número que se asigna a cada opción para analizarla. Google Forms exporta el **texto** de la opción, por lo que la codificación se aplica en la etapa de limpieza.

## Variables de control

| Columna | Pregunta | Opciones | Tipo esperado | Codificación |
|---|---|---|---|---|
| `id_respuesta` | (no se pregunta; se crea al preparar los datos) | — | int | 1, 2, 3… único por fila |
| `marca_temporal` | (la genera Google Forms) | — | datetime | — |
| `consentimiento` | ¿Aceptas participar de forma voluntaria y anónima en esta encuesta? | Sí / No | category | 1 = Sí, 0 = No. Si es No, la persona no responde el resto |
| `ciudad_residencia` | ¿En qué ciudad o municipio vives actualmente? | Medellín / Otro municipio de Antioquia / Otro departamento de Colombia | category | 1, 2, 3. Sirve para delimitar la población (Medellín o Colombia) según dónde se aplique |

## Variables del EV02 (sección 6)

| Columna | Dimensión | Pregunta | Opciones | Tipo esperado | Codificación |
|---|---|---|---|---|---|
| `edad_rango` | Datos generales | ¿En qué rango de edad estás? | 18-20 años / 21-23 años / 24-25 años | category ordinal | 1 = 18-20, 2 = 21-23, 3 = 24-25 |
| `tiempo_redes_dia` | Exposición | En un día normal, ¿cuánto tiempo aproximado pasas en redes sociales? | Menos de 1 h / 1–3 h / Más de 3 h | category ordinal | 1 = Menos de 1 h, 2 = 1–3 h, 3 = Más de 3 h |
| `frec_contenido_belleza` | Exposición | ¿Con qué frecuencia ves contenido de belleza, moda o maquillaje en redes sociales? | Nunca / Rara vez / Algunas veces / Frecuentemente / Todos los días | int ordinal | 1 = Nunca … 5 = Todos los días |
| `tipos_contenido` | Exposición | ¿Qué tipo de contenido de belleza ves? (puedes marcar varias) | Maquillaje / Moda / Cuidado de piel / Cuerpos y fitness / Filtros y edición de fotos / Diversidad corporal | texto multirespuesta | Se exporta en una celda separada por comas. Al preparar: una columna 0/1 por opción (`tc_maquillaje`, `tc_moda`, `tc_piel`, `tc_fitness`, `tc_filtros`, `tc_diversidad`) |
| `frec_comparacion` | Comparación | ¿Con qué frecuencia comparas tu apariencia con la de las personas que ves en ese contenido? | Nunca / Rara vez / A veces / Casi siempre / Siempre | int ordinal | 1 = Nunca … 5 = Siempre |
| `cambio_preferencias` | Resultado percibido | En los últimos meses, ¿han cambiado tus preferencias de moda, maquillaje o cuidado personal por el contenido que ves en redes? | Sí / No / No estoy seguro(a) | category | 1 = Sí, 2 = No, 3 = No estoy seguro(a) |
| `tipo_cambio` | Resultado percibido | (Opcional, solo si respondió Sí) ¿Qué tipo de cambio notaste? | Respuesta corta | texto libre | Sin codificar. Se espera vacío cuando `cambio_preferencias` ≠ Sí |
| `influencia_amistades` | Resultado percibido (círculo social) | ¿Cuánta influencia tienen tus amistades en lo que consideras bello? | Nada / Poca / Algo / Bastante / Mucha | int ordinal | 1 = Nada … 5 = Mucha |
| `influencia_familia` | Resultado percibido (círculo social) | ¿Cuánta influencia tiene tu familia en lo que consideras bello? | Nada / Poca / Algo / Bastante / Mucha | int ordinal | 1 = Nada … 5 = Mucha |
| `influencia_pareja` | Resultado percibido (círculo social) | ¿Cuánta influencia tiene tu pareja en lo que consideras bello? | Nada / Poca / Algo / Bastante / Mucha / No aplica | int ordinal | 1 = Nada … 5 = Mucha; "No aplica" = valor faltante justificado (no es un error) |
| `ideal_belleza` | Resultado percibido | "Mi idea de lo que es bello ha cambiado por lo que veo en redes sociales." | Totalmente en desacuerdo / En desacuerdo / Ni de acuerdo ni en desacuerdo / De acuerdo / Totalmente de acuerdo | int ordinal (Likert) | 1 = Totalmente en desacuerdo … 5 = Totalmente de acuerdo |
| `satisfaccion_apariencia` | Resultado percibido | ¿Qué tan satisfecho(a) te sientes hoy con tu apariencia? | Nada satisfecho(a) / Poco / Algo / Bastante / Muy satisfecho(a) / Prefiero no responder | int ordinal | 1 = Nada … 5 = Muy satisfecho(a); "Prefiero no responder" = faltante voluntario |

## Reglas éticas (EV02)
- No se piden nombre, correo, teléfono ni fotografías. Si el formulario guarda correos, esa columna se elimina antes de publicar el archivo.
- Las preguntas sensibles (satisfacción con la apariencia, pareja) permiten no responder.
- Las preguntas de cambio e influencia son **autorreportadas**: miden lo que la persona percibe, no un cambio medido.

## Qué revisar en el diagnóstico de calidad cuando existan respuestas
- Que solo haya respuestas con `consentimiento = Sí`.
- Que los valores de cada columna estén dentro de las opciones permitidas.
- Que `tipo_cambio` esté vacío cuando `cambio_preferencias` no sea "Sí".
- Distinguir faltantes **justificados** ("No aplica", "Prefiero no responder") de faltantes **no esperados**.
- Respuestas duplicadas (mismo contenido y marca temporal muy cercana).
- Número de respuestas por `ciudad_residencia`, para decidir cómo describir la población.
