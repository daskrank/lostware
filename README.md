# LOSTWARE

[![Version](https://img.shields.io/badge/version-5.7--MINI-blue.svg)](#)
[![Type](https://img.shields.io/badge/type-Structured--Prompt-orange.svg)](#)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Models](https://img.shields.io/badge/models-DeepSeek%20%7C%20Qwen%20%7C%20GLM-purple.svg)](#)

**Descripción técnica:** Un conjunto de instrucciones estructuradas que guían la ejecución de herramientas de búsqueda web por parte de modelos de lenguaje (LLMs), optimizando la recuperación de información histórica de nicho mediante filtrado heurístico y auditoría de fuentes.

**Descripción simple:** Prompt de arqueologia digital para encontrar videojuegos de computadora perdidos, regionales u olvidados de distintas epocas. Filtrando los titulos mainstream o ampliamente conocidos.

---

## Forma de uso

1. Copiá el contenido del archivo [`prompts/lostware-v5.7-mini.md`](prompts/lostware-v5.7-mini.md)
2. Pegalo en la ventana de chat del modelo de lenguaje que elijas.
Modelo recomendado: [DeepSeek](https://chat.deepseek.com).
3. Asegurate de que el modelo tenga acceso activo a una herramienta de búsqueda web.
4. Cuando lo solicite ingresá tu consulta en una sola línea indicando tres parametros:
   - Género
   - Región
   - Período (opcional, por defecto: 1989–1999)

```
[género] [país o región] [período]
```

**Ejemplo:**
```
Shoot em up de Korea
```

El modelo hara la búsqueda de forma autónoma y devolvera el listado de los hallazgos. Se pueden repetir consultas en la misma sesión.

---

## Cómo funciona

```
[Input: GENRE + REGION]
        │
        ▼
[Hard Gatekeeper] ──── parámetro faltante ────► [HALT + mensaje de error]
        │
    (válido)
        ▼
[Búsqueda con templates nativos T1–T5]
        │
        ▼
[Auditoría de indicadores de oscuridad] ──── score ≤ 0 ────► [Descarte]
        │
    (aprobado)
        ▼
[Verificación de nodos: ≥2 dominios independientes]
        │
        ▼
[Self-Audit interno → Output estructurado]
```

**Componentes clave:**

**Hard Gatekeeper** — Si el usuario no provee `GENRE` y `REGION`, el prompt detiene la ejecución antes de cualquier búsqueda. Previene alucinaciones por contexto incompleto.

**Templates de búsqueda nativos (T1–T5)** — Las queries se generan en el idioma oficial de la región objetivo. Accede a registros locales que no aparecen en búsquedas en inglés.

**Scoring heurístico** — Cada candidato acumula puntos positivos (exclusividad regional, distribución por BBS/FTP, documentación no anglófona) y negativos (presencia en Steam/GOG, Wikipedia multilingüe, remasters). Los que no superan el umbral van a la lista de descartados, no desaparecen.

**Verificación de nodos** — Un resultado es `CONFIRMED` solo si aparece en ≥2 dominios raíz independientes. Resultados de una sola fuente quedan como `UNCONFIRMED`.

---

## Resultados de prueba

Pruebas ejecutadas sobre 6 escenarios controlados con la versión V7.5-MINI.
Modelo utilizado: DeepSeek-R1. Fecha: junio 2026.

| ID | Género | Región | Resultado | PRIMARY | BORDERLINE | Descartes | Observación |
| :- | :----- | :----- | :-------- | :------ | :--------- | :-------- | :---------- |
| A1 | JRPG | Japón | CASE B ✓ | 1 | 2 | 4 | Queries en japonés recuperaron títulos PC-98 sin registros en inglés. |
| A2 | Metroidvania | Brasil | CASE A ✓ | 0 | 0 | 0 | El género no existía en la región en la época. Control anti-alucinación exitoso. |
| A3 | Estrategia | Polonia | CASE B ✓ | 2 | 1 | 3 | Tácticos DOS recuperados vía revistas polacas escaneadas. |
| B1 | Aventura gráfica | España | CASE B ✓ | 1 | 2 | 2 | Distribución local escasa; mayoría de títulos con alcance internacional. |
| B2 | Shoot 'em up | Corea del Sur | CASE B ✓ | 2 | 1 | 2 | Shareware recuperado vía BBS coreanas. Queries en hangul fueron clave. |
| B3 | Roguelike | Internacional | CASE B ✓ | 2 | 2 | 2 | Filtrado efectivo de títulos conocidos (ADOM, NetHack). |

**5 de 6 casos** devolvieron resultados verificables. El caso A2 (CASE A) es un éxito del sistema, no un fallo: el prompt no inventó datos donde no los había.

---

## Estructura del repositorio

```
lostware/
│
├── README.md
├── LICENSE
├── CHANGELOG.md
│
├── prompts/
│   ├── lostware-v5.7-mini.md        ← versión oficial actual
│   └── archive/
│       ├── lostware-v6.2.md
│       ├── lostware-v5.8-mini.md
│       └── lostware-v7.5-mini.md
│
├── tests/
│   ├── results-v7.5-mini.html
│   └── cases/
│       ├── a1-jrpg-japan.md
│       ├── a2-metroidvania-brazil.md
│       ├── a3-strategy-poland.md
│       ├── b1-adventure-spain.md
│       ├── b2-shmup-korea.md
│       └── b3-roguelike-international.md
│
└── docs/
    └── scoring-engine.md
```

---

## Limitaciones conocidas

- El modelo puede violar la regla de pureza de género en ronda 2 de búsqueda, ampliando el término sin indicarlo. Los resultados de ronda 2 deben leerse con ese sesgo en mente.
- Fuentes que requieren acceso manual (PDFs de revistas, FTPs privados, archivos BBS) no son accesibles por el modelo. Aparecen como pistas en `UNCONFIRMED`, no como resultados verificados.
- Posts de redes sociales no son fuentes arqueológicas válidas aunque el modelo los incluya. Verificación manual recomendada para cualquier resultado con esa procedencia.
- Si los encuentra, el modelo provee links de informacion y descargas, pero no tiene forma de validarlos como seguros. Es responsabilidad del usuario tomar los recaudos para proteccion contra malware. Aunque mantener el antivirus del sistema actualizado suele ser suficiente, se recomienda escanear los archivos bajados con herramientas online como [Virus Total](https://www.virustotal.com/gui/home/upload). 

---

## Proyecto

Desarrollado por **KRANK** como herramienta de investigacion para el canal **[Intermosh](https://www.youtube.com/@intermosh)** — Videojuegos, IA, experimentos y tecnología desde los márgenes.

---

## Licencia

MIT. Ver [LICENSE](LICENSE).
