# LOSTWARE v7.5-MINI

LOSTWARE es un prompt operativo para detectar videojuegos de computadora perdidos, regionales u olvidados, con un sesgo fuerte hacia la arqueología digital y la preservación. Su arquitectura combina filtrado por parámetros obligatorios, calibración de rareza, búsqueda acotada por plantillas y una salida rígida orientada a catalogar hallazgos con trazabilidad mínima pero útil.

## Propósito del sistema

El objetivo de LOSTWARE no es “encontrar juegos raros” en general, sino **identificar títulos históricos que encajen con una región y un género concretos**, y que además presenten señales de obscuridad documental, circulación limitada o posible pérdida. El prompt está diseñado para evitar resultados populares, sobreexpuestos o fácilmente accesibles en tiendas modernas. También busca separar títulos verdaderamente relevantes de ruido generado por agregadores, SEO o referencias mainstream.

## Alcance funcional

El sistema trabaja sobre juegos de computadora, no sobre consolas ni mobile. Su marco temporal base es 1989–1999, salvo que el usuario especifique otro período. Acepta comerciales, shareware, freeware, demos jugables, betas, prototipos, cancelados, títulos recuperados y material de preservación disperso en índices o archivos. La lógica general privilegia la rareza histórica, la documentación localizada y la disponibilidad incierta por encima de la fama o la calidad comercial.

## Flujo operativo

La ejecución está dividida en seis bloques:

1. **Extracción de parámetros**: identifica obligatoriamente `GENRE` y `REGION`.
2. **Definición del marco de búsqueda**: fija era, plataformas válidas y tipos de título admitidos.
3. **Calibración de obscuridad**: evalúa si un candidato es realmente marginal, fronterizo o mainstream.
4. **Plantillas de consulta**: genera búsquedas con variables reemplazables, sin inventar estructuras nuevas.
5. **Reglas de salida**: obliga a responder en formato cerrado, con secciones predefinidas.
6. **Verificación final**: comprueba que ningún título mainstream haya entrado en la lista principal y que exista un párrafo de descartados.

## Variables que definen el comportamiento

### Variables obligatorias

- `GENRE`: el género objetivo del juego.
- `REGION`: la región geográfica o cultural de referencia.

Si falta `GENRE`, el sistema debe detenerse con un error exacto. Si falta `REGION`, debe hacer lo mismo. Esta puerta de entrada evita búsquedas ambiguas y obliga a que toda exploración tenga una intención semántica clara.

### Variables contextuales

- `ERA`: ventana temporal de búsqueda. Por defecto, 1989–1999.
- `PLATFORMS`: solo computadoras de hogar y PC; excluye consolas y mobile.
- `VALID TITLES`: lista amplia de estados y formatos aceptables.
- `LANGUAGE`: el idioma de la consulta debe coincidir con la lengua oficial principal de la región.
- `ITERATIONS`: máximo 3 rondas de búsqueda.
- `VERIFICATION`: un título solo se considera confirmado si aparece en al menos 2 dominios raíz independientes.
- `GENRE PURITY`: no se permite ampliar ni sustituir el género original.

### Variables de clasificación

- `PRIMARY`: candidato altamente probable y suficientemente obscuro.
- `BORDERLINE`: candidato ambiguo o con evidencia mixta, pero aún relevante.
- `MAINSTREAM`: candidato demasiado conocido o sobreexpuesto.
- `Status`: Released, Cancelled, Prototype, Beta o Unknown.
- `Availability`: Accessible, Hard to find o Possible Lost Media.

## Arquitectura de razonamiento

La arquitectura mental del prompt está basada en filtros sucesivos. Primero obliga a validar que el pedido sea operativo. Luego restringe el universo de búsqueda a un rango histórico y tecnológico concreto. Después aplica un sistema de señales positivas y negativas para medir obscuridad documental.

Ese diseño hace que LOSTWARE no dependa solo de “si existe el juego”, sino de **cómo circuló, dónde aparece documentado y con qué grado de visibilidad histórica**. En otras palabras, no busca simplemente títulos poco famosos, sino títulos que quedaron en los bordes del archivo, en revistas regionales, CDs de shareware, BBS, FTP dumps, índices de preservación o compilaciones sin reseñas individuales.

## Modelo de obscuridad

El prompt usa dos familias de indicadores.

### Indicadores positivos

Un candidato gana puntos de obscuridad si:

- Solo aparece en revistas regionales o foros locales.
- Está documentado solo en un idioma no inglés.
- Se distribuyó por shareware CDs, BBS o FTP.
- Su estado de prototipo, beta o cancelado fue confirmado por una fuente primaria.
- Figure solo en índices de preservación o compilaciones sin cobertura crítica propia.

### Indicadores negativos

Un candidato pierde obscuridad si:

- Está en Steam, GOG u otra tienda moderna.
- Tiene Wikipedia dedicada en más de dos idiomas.
- Tuvo remaster, remake o re-released oficial.
- Fue cubierto ampliamente por prensa internacional al lanzamiento.

### Regla de clasificación

- 3 o más positivos y 0 negativos: `PRIMARY`.
- Mezcla o duda: `BORDERLINE`.
- 2 o más negativos dominan: `MAINSTREAM`.
- Solo páginas SEO o agregadores IA: descartar.

Este sistema está pensado para evitar falsos positivos y para conservar en el resultado final solo material con potencial de interés archivístico.

## Plantillas de consulta

El prompt no improvisa búsquedas: obliga a usar plantillas. Esto le da consistencia a la exploración y reduce la deriva semántica.

Las cinco plantillas base son:

- Archivos.
- Foros.
- Revistas escaneadas.
- Índices de preservación.
- Cruce con base regional.

Todas usan las variables `GENRE_NATIVE`, `REGION_NATIVE` y, cuando aplica, `YEAR`. Además, el texto de la consulta debe escribirse en el idioma oficial principal de la región objetivo. Esa restricción es importante porque el material marginal suele estar mejor indexado en la lengua local que en inglés.

## Lógica de confirmación

Un hallazgo no se considera estable por existir una sola mención. El sistema distingue entre:

- **Confirmed**: al menos 2 dominios raíz independientes.
- **Unconfirmed**: solo 1 fuente.

Esto es crucial porque muchos títulos olvidados aparecen duplicados en espejos, agregadores o copias del mismo índice. La regla de independencia de dominios busca minimizar la falsa corroboración.

## Formato de salida

La salida solo admite dos modos:

### Case A — No results

Devuelve un error fijo cuando no hay títulos verificados que cumplan el criterio histórico.

### Case B — Results

Incluye:

- Lista de confirmados.
- Lista de no confirmados.
- Párrafo de descartados.
- Estado del título.
- Nivel de disponibilidad.

Este formato está diseñado para que la documentación resultante sea utilizable directamente en GitHub o en una base de conocimiento, sin texto introductorio ni conversación adicional.

## Criterios de descarte

El bloque de “Discarded” no es opcional. Su función es documentar qué títulos aparecieron pero fueron excluidos y por qué. En particular, debe servir para registrar casos mainstream que ensucian la búsqueda, como franquicias conocidas, títulos con gran visibilidad o juegos reeditados en plataformas modernas.

Ese bloque cumple una función metodológica: hace explícito el criterio de exclusión y evita que el resultado final parezca arbitrario.

## Interpretación técnica

LOSTWARE funciona como un **filtro de arqueología digital** más que como un buscador genérico. Su lógica combina tres capas:

1. **Semántica**: género y región obligatorios.
2. **Histórica**: periodo, plataformas y formatos válidos.
3. **Evidencial**: confirmación múltiple, señales de obscuridad y exclusión de mainstream.

La consecuencia es un sistema que premia la documentación débil pero consistente, y penaliza la fama, la disponibilidad moderna y la cobertura excesiva.

## Riesgos y límites

El diseño tiene fortalezas claras, pero también límites:

- Depende mucho de la calidad de la indexación regional.
- Puede perder títulos relevantes si las fuentes están mal digitalizadas.
- La regla de “2 dominios independientes” puede excluir material auténtico pero extremadamente raro.
- La exigencia de idioma local puede ser muy buena para precisión, pero puede reducir cobertura en regiones multilingües.
- La definición de “mainstream” es útil, aunque algo heurística y dependiente del corpus encontrado.

## Recomendaciones de uso

Para trabajar bien con LOSTWARE, conviene que el usuario formule solicitudes concretas como:

- género exacto;
- región exacta;
- período si quiere salir del marco 1989–1999;
- plataforma si desea afinar más allá del set permitido.

También conviene conservar la clasificación `PRIMARY / BORDERLINE / MAINSTREAM`, porque es la pieza que convierte la búsqueda en una herramienta de archivo y no solo en una lista de resultados.

## Estructura documental sugerida para GitHub

Si esta documentación se sube a un repositorio, conviene organizarla como:

- descripción general;
- objetivo;
- alcance;
- variables;
- flujo;
- modelo de clasificación;
- reglas de búsqueda;
- verificación;
- formato de salida;
- límites;
- ejemplos de uso.

Esa estructura mantiene separadas la intención del sistema, su mecánica interna y su contrato de salida, que es exactamente lo que un lector técnico necesita para reutilizar o adaptar el prompt.
'''