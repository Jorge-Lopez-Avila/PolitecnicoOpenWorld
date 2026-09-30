# Primer examen parcial — Pull Request con aseguramiento de calidad

**Desarrollo de aplicaciones móviles nativas** · ESCOM-IPN · Periodo 2027-1 · Grupo 7CV4

> Índice de entrega. Vive en la rama académica `entrega/examen-parcial-1` del fork, **fuera del diff
> del PR** a POW. No reemplaza el README general del proyecto.

## Equipo

| Integrante | Usuario de GitHub | PR propio |
|---|---|---|
| Jorge López Ávila | [@Jorge-Lopez-Avila](https://github.com/Jorge-Lopez-Avila) | _enlace al PR_ |
| _Integrante 2_ | _@usuario_ | _enlace_ |
| _Integrante 3_ | _@usuario_ | _enlace_ |

## Objetivo

Agregar a **Aleks Syntek** como peleador seleccionable en *Titulación por Combate*, con su hoja de
sprites propia, sus datos de animación y su audio de victoria, sin que la app se cierre al atacar.
Validar el cambio con un plan de QA reproducible.

## Alcance

- **Incluye:** registro del peleador (`SfFighterId.ALEKS_SYNTEK`), aparición en el selector
  desbloqueado de inicio, escenario hogar, hoja `AleksSyntek.webp`, datos `aleks_syntek.json` y el audio
  `special_aleks_syntek_win.m4a`.
- **No incluye:** agregarlo como rival de la escalera del arcade, combo propio, poderes extra, cambios
  al multijugador ni a iOS.

## Enlaces de la entrega

| Elemento | Enlace / valor |
|---|---|
| Issue | _`Jorge-Lopez-Avila/PolitecnicoOpenWorld#N`_ |
| Rama de trabajo | [`feature/aleks-syntek-fighter`](https://github.com/Jorge-Lopez-Avila/PolitecnicoOpenWorld/tree/feature/aleks-syntek-fighter) |
| Draft PR (hacia `gabrielhuav/PolitecnicoOpenWorld:main`) | _enlace_ |
| SHA base | `7ed325393f82872c2be94ff2ada46948efa19152` |
| SHA probado | _llenar_ |
| SHA final entregado | _llenar_ |
| Matriz de pruebas | [docs/pruebas.md](pruebas.md) |
| Bitácora por integrante | [docs/bitacora.md](bitacora.md) |
| Evidencias (capturas, videos, Logcat) | [docs/evidencias/](evidencias/) |
| Ejecución de CI (PR Quality Gate) | _enlace a la pestaña Checks / run de Actions_ |
| Revisión técnica | _enlace a la conversación de revisión del PR_ |

## Verificaciones automáticas

| Check | Estado | SHA / run | Qué comprueba | Qué no cubre |
|---|---|---|---|---|
| Unit tests (`assembleDebug`, `testDebugUnitTest`, `testAndroidHostTest`) | _llenar_ | _llenar_ | Que la app compila, el grafo de Hilt y la lógica pura (incluye `SfArcadeCampaignAuditTest`). | Que el personaje se vea y se juegue bien en un dispositivo. |
| Nombres de test KMP (`check_kmp_test_names.sh`) | _llenar_ | _llenar_ | Que los nombres de tests compilen en Kotlin/Native. | La compilación real de iOS. |
| detekt | _llenar_ | _llenar_ | Análisis estático con el baseline del proyecto. | Comportamiento en ejecución. |

CI usa JDK 21, `MAPS_API_KEY` vacío y sin `google-services.json`: no demuestra que mapas ni Firebase
funcionen. El modo de pelea no depende de ellos.

## Conclusiones

_Llenar después del QA: dictamen de calidad (¿se recomienda integrar?), con qué evidencia, y riesgos
que permanecen (hitboxes de plantilla, derechos de imagen de una persona real con arte generado por IA)._

## Uso de herramientas de IA

| Herramienta | Propósito |
|---|---|
| Claude (Anthropic), en Cowork | Recorte y armado de la hoja de sprites, cambios de código para registrar al peleador, conversión del audio y redacción inicial del plan de QA y de este índice. Todo fue revisado y ejecutado por el equipo. |
| ChatGPT (generación de imágenes) | Generación de las hojas de sprites del personaje. |

## Referencias

- Enunciado: *Primer examen parcial — Pull Request con aseguramiento de calidad* (2027-1).
- README de POW y `.github/workflows/pr-quality-gate.yml` del repositorio original.
