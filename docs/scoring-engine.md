# Motor de Puntuación

El motor de puntuación es el mecanismo que usa LOSTWARE para clasificar los candidatos encontrados durante la búsqueda. Reemplaza el juicio subjetivo ("¿esto parece lo suficientemente oscuro?") por un conjunto de reglas basadas en evidencia concreta, aplicadas de forma consistente a cada resultado.

---

## Cómo funciona

Cada título candidato arranca con un puntaje base de 0. La evidencia recopilada durante la búsqueda suma o resta puntos. El puntaje final determina si el título aparece en el output y en qué categoría.

```
PUNTAJE = SUMA(indicadores positivos) - SUMA(indicadores negativos)
```

El modelo aplica esta fórmula basándose únicamente en la evidencia encontrada durante la sesión actual. El conocimiento previo del modelo no es evidencia.

---

## Indicadores positivos

| Indicador | Puntos | Notas |
| :--- | :--- | :--- |
| Exclusivo regional — nunca lanzado fuera del territorio de origen | +2 | Debe estar respaldado por ausencia de evidencia de distribución internacional |
| Documentación exclusivamente en un idioma no inglés | +2 | Manuales, reseñas y posts de foros todos en el idioma local |
| Estado de Prototipo, Beta o Cancelado verificado | +2 | Requiere fuente primaria: declaración del desarrollador, anuncio en revista, archivo preservado |
| Distribuido únicamente por BBS, volcados FTP o compilaciones shareware | +2 | Sin caja de venta, sin distribución digital comercial |
| Mencionado solo en una única revista regional, foro local o sitio personal | +1 | Una sola mención en todas las fuentes encontradas |
| Listado en un índice de preservación o compilación de CD sin reseñas individuales | +1 | Presencia en una lista, sin cobertura propia |

Puntaje máximo posible: 10.

---

## Indicadores negativos

| Indicador | Puntos | Notas |
| :--- | :--- | :--- |
| Disponible en Steam, GOG o cualquier tienda digital moderna | -3 | Cualquier disponibilidad comercial actual |
| Existe un remaster, remake o relanzamiento oficial | -2 | Relanzamiento autorizado por el publisher en cualquier formato |
| Artículo de Wikipedia en más de 2 idiomas | -2 | Indica documentación internacional amplia |
| Cubierto por prensa internacional de gaming en su lanzamiento | -2 | Edge, PC Gamer, GameSpot, IGN y equivalentes |
| Publicado por uno de los 20 publishers globales más grandes de la época | -1 | EA, Activision, LucasArts, Sierra, Microprose y similares |

---

## Categorías de clasificación

| Puntaje | Categoría | Comportamiento en el output |
| :--- | :--- | :--- |
| ≥ 5 | `[PRIMARY-HIGH]` | Incluido. Contexto de 2 líneas completo. |
| 3 – 4 | `[PRIMARY]` | Incluido. Contexto de 2 líneas completo. |
| 1 – 2 | `[BORDERLINE]` | Incluido. Etiquetado explícitamente en el campo de contexto. |
| ≤ 0 | `[MAINSTREAM]` | Excluido de la lista principal. Nombrado en el párrafo de Descartados con justificación. |

Los títulos cuya única fuente son sitios generados por IA o páginas SEO de resumen automático se descartan de inmediato sin importar el puntaje. Esas fuentes no aportan evidencia verificable.

---

## Diferencias entre versiones

El motor de puntuación existe en dos formas distintas según la versión de LOSTWARE.

**V7.5-MINI** usa clasificación cualitativa en lugar de puntuación numérica. En lugar de calcular una suma, el modelo evalúa la presencia o ausencia de indicadores y clasifica directamente:

- ≥ 3 positivos, 0 negativos → PRIMARY
- Mixto o poco claro → BORDERLINE
- ≥ 2 negativos que superan a los positivos → MAINSTREAM

Este enfoque consume menos tokens de razonamiento y produce resultados consistentes en casos claros. Es menos confiable para candidatos borderline donde el peso de cada indicador importa.

**V7.5-S Deep Research** usa el motor numérico completo descripto arriba. Los puntajes se calculan de forma explícita y aparecen en el output junto a cada título (`Score: [X]`). Esto hace que la clasificación sea auditable, pero aumenta el costo en tokens por corrida.

---

## Anclas de calibración

Para anclar qué significa "oscuro" en la práctica, ambas versiones incluyen títulos de referencia. Son solo ejemplos — nunca son hallazgos válidos de búsqueda.

**Referencia oscura (rango objetivo):** Planeta Vermelho, Father World, Cruel World, Ali Baba, Xiao-Ao-Jiang-Hu, Cyber Force, Biorobot's Base, B.I.G., Runen, Tears of Fury.

**Referencia mainstream (rango de exclusión):** Another World, Flashback, Prince of Persia, Heart of Darkness, Fade to Black, Earth 2140, Rayman.

Las anclas de calibración definen la vibra, no una lista negra. Un título que se parezca a una referencia mainstream en género o estilo no queda excluido automáticamente — solo su puntaje determina su destino.

---

## Limitaciones conocidas

**Inflación de puntaje con evidencia escasa.** Un título del que no se encuentra casi nada puede sumar +2 simplemente porque su documentación está en otro idioma y aparece en un solo post de foro. La ausencia de evidencia no es lo mismo que evidencia de oscuridad. El párrafo de Descartados y el paso de verificación manual existen para atrapar estos casos.

**Riesgo de calibration leak.** Los títulos de las anclas aparecen en el system prompt. Los modelos con buena memoria de contexto pueden devolverlos como hallazgos de búsqueda en lugar de usarlos como referencia. Si esto ocurre, los resultados deben descartarse. Ver el informe de benchmark para un caso documentado de este fallo en V7.5-S, caso A2.

**Sin crédito parcial.** Cada indicador es binario: o la evidencia lo respalda o no lo hace. Un título distribuido "mayormente" por BBS pero con una copia conocida en venta al público no suma el +2. El modelo tiene instrucciones de aplicar los indicadores de forma conservadora.
