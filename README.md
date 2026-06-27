<div align="center">

# LOSTWARE
[![Version](https://img.shields.io/badge/version-Core%201-blue.svg)](#) [![Type](https://img.shields.io/badge/type-Structured--Prompt-orange.svg)](#) [![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE) [![Models](https://img.shields.io/badge/models-DeepSeek%20%7C%20Qwen%20%7C%20GLM-purple.svg)](#)

<img src="/assets/isotype.jpg" alt="markdown language" width="200" height="200">
</div>

---

**Descripción técnica:** Lostware es conjunto de instrucciones estructuradas que guían la ejecución de herramientas de búsqueda web por parte de modelos de lenguaje (LLMs), optimizando la recuperación de información histórica de nicho mediante filtrado heurístico y auditoría de fuentes.


**Descripción simple:** Prompt de arqueología digital para encontrar videojuegos de computadora perdidos, regionales u olvidados de distintas épocas. Filtra los títulos mainstream o ampliamente conocidos.

---

## Forma de uso

1. Copiá el contenido del archivo [`prompts/lostware-core-1.md`](prompts/lostware-core-1.md)
2. Pegalo en la ventana de chat del modelo de lenguaje que elijas.  
   Modelo recomendado: [DeepSeek](https://chat.deepseek.com).
3. Asegurate de que el modelo tenga acceso activo a una herramienta de búsqueda web.
4. Cuando lo solicite ingresá tu consulta en una sola línea indicando tres parámetros:
[género] [país o región] [período] (opcional, por defecto: 1989–1999)

**Ejemplo:**

```
plataformas de korea, de 1993 a 1998
```

**Ejemplo:**
```
aventuras rusas
```

El modelo hará la búsqueda de forma autónoma y devolverá el listado de los hallazgos. Se pueden repetir consultas en la misma sesión.

---

## Cómo funciona

![Lostware-Core-1](/assets/diagram.jpg "Diagrama de flujo")


**Componentes clave:**

**Hard Gatekeeper** — Si el usuario no provee `GENRE` y `REGION`, el prompt detiene la ejecución antes de cualquier búsqueda. Previene alucinaciones por contexto incompleto. (Precisión 100% en pruebas).

**Templates de búsqueda nativos (T1–T5)** — Las queries se generan en el idioma oficial de la región objetivo. Accede a registros locales que no aparecen en búsquedas en inglés.

**Pivote lingüístico** — Si la primera ronda en idioma local no da resultados, el sistema cambia automáticamente a inglés manteniendo el nombre de la región. Esto ha demostrado ser crítico para regiones con poca presencia web local.

**Scoring heurístico** — Cada candidato acumula puntos positivos (exclusividad regional, distribución por BBS/FTP, documentación no anglófona) y negativos (presencia en Steam/GOG, Wikipedia multilingüe, remasters). Los que no superan el umbral van a la lista de descartados, no desaparecen.

**Verificación de nodos** — Un resultado es `CONFIRMED` solo si aparece en ≥2 dominios raíz independientes. Resultados de una sola fuente quedan como `UNCONFIRMED`.

---

## Variantes del modelo

| Variante | Perfil | Optimización | Uso |
| :--- | :--- | :--- | :--- |
| **Core 1** | **Stable Release** | Equilibrio entre precisión, consistencia y consumo de tokens. | Uso general.
| **Mini v5.7** | **Ligero / Experimental** | Mayor velocidad y menor uso de tokens, pero sacrifica precisión. | Escaneo masivo para hallazgos rápidos con impresiciones. |
| **S (Deep Research)** | **Alta precisión** | Validación granular, mas lento y mayor consumo de tokens. Experimental. | Reportes investigativos profundos. |

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
│     ├── lostware-core-1.md       ←--- versión oficial
│     └── archive/
│          ├── lostware-core.md
│          ├── lostware-v5.8-mini.md
│          ├── lostware-v6.2.md
│          ├── lostware-v7.5-mini.md
│          └── lostware-v7.5-S-deep-research.md
│
├── tests/
│     ├── results-core-1.html
│     └── results-v5.7-mini.html
└── docs/
      └── scoring-engine.md

```
---

## Limitaciones conocidas

- El modelo v5.7-mini puede violar la regla de pureza de género en ronda 2 de búsqueda, ampliando el término sin indicarlo. Los resultados de ronda 2 deben leerse con ese sesgo en mente. Ver [scoring-engine](/docs/scoring-engine-v5.7-mini.md).

- Fuentes que requieren acceso manual (PDFs de revistas, FTPs privados, archivos BBS) no son accesibles por el modelo. Aparecen como pistas en `UNCONFIRMED`, no como resultados verificados.
- Los posts de redes sociales no son fuentes arqueológicas válidas aunque el modelo las incluya. Verificación manual recomendada para cualquier resultado con esa procedencia.
- Si los encuentra, el modelo provee links de información y descargas, pero no tiene forma de validarlos como seguros. Es responsabilidad del usuario tomar los recaudos para protección contra malware. Aunque mantener el antivirus del sistema actualizado suele ser suficiente, se recomienda escanear los archivos bajados con herramientas online como [Virus Total](https://www.virustotal.com/gui/home/upload).

---
## Proyecto

Desarrollado por **KRANK** como herramienta de investigación para el canal **[Intermosh](https://www.youtube.com/@intermosh)** — Videojuegos, IA, experimentos y tecnología desde los márgenes.

---

## Licencia
MIT. Ver [LICENSE](LICENSE).