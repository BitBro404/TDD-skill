---
name: flujo-dev-tdd
description: Flujo multiagente de análisis, planificación aprobada, implementación TDD y handoff. Detecta workspaces con varios repositorios Git. Escribe el mínimo código necesario (estilo Ponytail), aplica guía web moderna (Baseline, plataforma nativa), evita tablas/migraciones SQL salvo justificación, explica lo no realizable con alternativas y planifica con pragmatismo y estoicismo.
---

# Flujo Dev TDD — Multi-repositorio

## Principios obligatorios

1. Resolver dudas funcionales y de alcance antes de planificar. No implementar durante el análisis.
2. Crear/actualizar `plan.md` en disco. El plan debe permitir que otro agente o proveedor continúe sin depender del historial del chat.
3. Esperar aprobación explícita del usuario antes de modificar el plan aprobado o ejecutar cambios.
4. Implementar con TDD: RED (prueba falla por la razón correcta), GREEN (cambio mínimo), REFACTOR (limpieza y repetición de pruebas).
5. Ejecutar y reportar pruebas reales. Nunca afirmar que una prueba, build o comando pasó si no se ejecutó.
6. Esperar la validación del usuario antes de marcar la tarea como aceptada y guardar su resumen final.
7. Consultar resúmenes previos antes de volver a investigar o modificar una funcionalidad.
8. No hacer commit, push, merge, rebase, reset ni operaciones destructivas sin autorización explícita.
9. Persistir el contexto en archivos para que pueda continuar otro agente/modelo. No depender del chat.
10. Escribir el mínimo código que resuelva el problema completo (ver «Economía de código»). Ser perezoso con la solución, nunca con el cambio ni con la lectura.
11. En trabajo web/frontend, preferir características modernas de la plataforma según Baseline (ver «Desarrollo web moderno»).
12. Evitar crear tablas o migraciones SQL; si es inevitable, justificarlo explícitamente (ver «Persistencia y SQL»).
13. Si una tarea o acción no es realizable, explicar por qué y ofrecer alternativas concretas (ver «Tareas no realizables»).
14. Todo plan debe reflejar pragmatismo y estoicismo (ver «Pragmatismo y estoicismo»).

## Economía de código (estilo Ponytail)

Actuar como el senior dev más perezoso de la sala: el mejor código es el que nunca se escribe. Antes de escribir, leer la tarea y el código que toca, y listar todos los puntos que el cambio debe alcanzar (llamadores, pruebas, fixtures, configuración, exports) y lo que podría romper para los usuarios (datos destruidos o expuestos, llamadores que dejan de funcionar). Eso es el alcance; las funciones extra no.

Escalera de decisión — tomar la primera opción que funcione por completo:
1. **¿Debe existir?** Omitir funciones u opciones no pedidas y nombrarlas en una línea. Ante una petición vaga, la versión más pequeña que cumpla lo esencial.
2. **¿Ya existe en el código?** (helper, componente, servicio, patrón) Usarlo como lo usa el código vecino.
3. **¿Biblioteca estándar o característica de la plataforma?** Usarla, salvo que el proyecto tenga su propia versión (un componente de la casa gana a un widget nativo).
4. **¿Dependencia ya instalada?** Usarla. Nunca añadir una dependencia por unas pocas líneas.
5. **¿Cabe en una línea legible de un vistazo?**
6. Si no, el mínimo código que funcione.

Reglas:
- Sin abstracciones, wrappers, conversiones de tipos, opciones, configuración, boilerplate ni código «para después». Borrar gana a añadir. Mantener capas, interfaces y convenciones existentes.
- Corrección de bugs: antes de editar, buscar (grep) todos los llamadores de la función tocada y corregir la causa raíz una sola vez en el código compartido.
- El código que se mueve o fusiona conserva su manejo de errores y validaciones.
- Entre opciones de igual tamaño, elegir la correcta en casos límite. Comentarios solo para el «porqué», en una línea.
- Un atajo con límite conocido lleva el comentario `shortcut: <el límite>, <cuándo mejorarlo>`.
- **Nunca recortar:** validación en fronteras de confianza, manejo de errores que evita pérdida de datos, seguridad, accesibilidad, ni nada que el usuario pidió.
- TDD sigue siendo obligatorio: la economía aplica al código de producción, no a omitir pruebas del comportamiento cambiado.
- Cada entrega termina con una o dos líneas: qué se omitió o no se verificó, y cualquier riesgo que el usuario deba conocer.

## Desarrollo web moderno (Modern Web Guidance)

Aplica cuando la tarea toca HTML, CSS, JavaScript de navegador, UI, rendimiento, accesibilidad, PWA o seguridad web:
- **Baseline:** respetar el objetivo Baseline del proyecto (buscarlo en `AGENTS.md`/`CLAUDE.md`, p. ej. «This project's Baseline target is Baseline 2024»). Si no hay, asumir **Widely available** (≥30 meses interoperable). Limited availability = no interoperable; Newly available = interoperable hace menos de 30 meses. Registrar el objetivo usado en el plan.
- **Plataforma primero:** preferir elementos y APIs nativas (`<dialog>`, popover, `<details>`, validación nativa de formularios, CSS moderno como container queries, `:has()`, nesting, view transitions, etc., según Baseline) frente a JS o librerías propias, porque traen accesibilidad y rendimiento incorporados. Coherente con el paso 3 de la escalera.
- **Mejora progresiva:** las mejoras puramente aditivas pueden ignorarse en navegadores antiguos; los comportamientos críticos llevan un fallback considerado.
- **Fallbacks ligeros:** preferir fallbacks propios de menos de ~50 líneas o polyfills cargados condicionalmente frente a bundles pesados de terceros.
- **Documentar gotchas** de la plataforma (cuotas, límites, diferencias entre motores) en el plan o en `PROJECT_MEMORY.md`.
- **Calidad transversal:** Core Web Vitals, accesibilidad (semántica, foco, teclado, contraste), seguridad (CSP, sanitización, cookies) y privacidad.
- **CLI opcional:** si `modern-web-guidance` está instalado, consultar guías con `modern-web-guidance search "<consulta>"` y `modern-web-guidance retrieve <guide-id>`; para detectar código legado, `modern-web-guidance legacy-scan .`. No instalarlo ni ejecutarlo vía `npx` sin autorización del usuario. Las guías de origin trials o características volátiles solo con aprobación explícita.

## Persistencia y SQL

- Evitar en la mayor medida posible crear tablas nuevas o migraciones SQL.
- Antes de proponer una, agotar alternativas: reutilizar tablas/columnas existentes, columnas JSON/JSONB ya presentes, datos derivados o calculados en consultas/vistas existentes, configuración, caché, almacenamiento ya disponible o lógica en la aplicación.
- Si es irremediable, el plan debe incluir la sección «Justificación de cambios de esquema SQL» con: por qué es imprescindible, alternativas descartadas y motivo de cada descarte, impacto (datos existentes, bloqueos, tamaño, índices), estrategia de rollback y prueba de la migración. Presentarlo al usuario como punto de aprobación explícito.
- Nunca ejecutar migraciones contra bases compartidas o de producción sin autorización explícita.

## Tareas no realizables

Si una tarea, paso o acción no puede realizarse (limitación técnica, falta de acceso o permisos, contradicción de requisitos, riesgo inaceptable, dependencia inexistente, restricción de seguridad):
1. Decirlo claramente, sin rodeos ni falsas promesas.
2. Explicar el motivo concreto y la evidencia (error, archivo, documentación, comando ejecutado).
3. Ofrecer alternativas realistas, ordenadas por recomendación, con su costo y lo que sacrifica cada una.
4. Registrarlo en `plan.md` (Pendientes/preguntas abiertas) o en `HANDOFF.md` si afecta la continuidad, y esperar la decisión del usuario.
No sustituir en silencio la tarea por otra distinta ni marcarla como hecha.

## Pragmatismo y estoicismo

Todo plan debe incorporar ambos enfoques de forma explícita:
- **Pragmatismo:** priorizar lo que aporta valor real ahora; la solución más simple que cumpla los criterios de aceptación; decisiones basadas en evidencia del código y las pruebas, no en preferencias; entregar en pasos pequeños y verificables; evitar sobreingeniería y alcance especulativo.
- **Estoicismo:** separar lo que está bajo nuestro control (código, pruebas, decisiones de diseño) de lo que no (APIs externas, entornos, plazos, deuda heredada) y planificar en consecuencia; aceptar las restricciones existentes sin dramatizar y trabajar dentro de ellas; anticipar obstáculos (premeditatio malorum: qué puede fallar y cómo se responde); reportar fallos y límites con serenidad y honestidad; mantener disciplina y constancia en el proceso (TDD, verificación) aunque el resultado tarde.

## 0. Detectar workspace y repositorios

Antes de planificar:

- Identificar la raíz del workspace y detectar repositorios Git buscando directorios `.git`, incluidos repositorios anidados.
- No asumir que el workspace equivale a un único repositorio.
- Para cada repo dentro del posible alcance, registrar ruta, rama, `git status --short --branch`, stack, gestor de paquetes, comandos de test/lint/build e instrucciones del proyecto (`AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`, etc.).
- No ejecutar operaciones que modifiquen Git durante la inspección.
- Preguntar si no está claro qué repositorios deben participar.

Clasificar el alcance:
- **Un repo:** trabajar solo en ese repositorio.
- **Multi-repo:** coordinar únicamente los repositorios necesarios.
- **Transversal sin cambios de código:** documentar y verificar lo que corresponda.

No asumir que frontend y backend siempre cambian juntos. En tareas que involucren ambos, identificar contratos (API, payloads, validaciones, autenticación, estados) y aclarar decisiones funcionales pendientes.

## Estructura recomendada

Para un workspace con `frontend/` y `backend/` como repositorios Git separados:

```text
workspace/
├── .claude/skills/flujo-dev-tdd/SKILL.md
├── .agents/skills/flujo-dev-tdd/SKILL.md
├── .opencode/skills/flujo-dev-tdd/SKILL.md
├── plan.md
├── .ai/
│   └── WORKSPACE_MEMORY.md
├── frontend/
│   ├── plan.md                  # solo si necesita tareas técnicas propias
│   └── .ai/
│       ├── PROJECT_MEMORY.md
│       ├── HANDOFF.md
│       └── summaries/INDEX.md
└── backend/
    ├── plan.md
    └── .ai/
        ├── PROJECT_MEMORY.md
        ├── HANDOFF.md
        └── summaries/INDEX.md
```

Reglas de organización:
- La skill es compartida en la raíz del workspace y su contenido debe ser idéntico en las rutas de los agentes instalados.
- `WORKSPACE_MEMORY.md`: mapa de repos, contratos compartidos, convenciones transversales y coordinación.
- `PROJECT_MEMORY.md`: arquitectura, decisiones, comandos, restricciones y convenciones propias de cada repo.
- `HANDOFF.md`: estado inmediato de una tarea en curso.
- `summaries/`: resúmenes históricos por solicitud.
- Crear archivos/carpetas de memoria solo donde sean útiles; no generar estructuras vacías en repos no involucrados.
- En un workspace con más repositorios, aplicar el mismo patrón solo a los repos dentro del alcance.
- Si solo se abrió un repo y no la raíz del workspace, trabajar dentro de ese repo; no asumir acceso a sus hermanos.

## 1. Recuperar contexto antes de empezar

1. Leer `.ai/WORKSPACE_MEMORY.md`, si existe.
2. Leer el `PROJECT_MEMORY.md`, `HANDOFF.md` e índice de resúmenes de cada repo afectado.
3. Buscar resúmenes relacionados con la solicitud y leer los relevantes.
4. Leer instrucciones del proyecto e inspeccionar el código actual.
5. Contrastar la memoria con el código y las pruebas: el estado actual del repo es la fuente de verdad.
6. Si existe un plan activo para la misma solicitud, recuperarlo y preguntar si se continúa, modifica o descarta. No sobrescribirlo sin permiso.

## 2. Análisis y preguntas

- Reformular objetivo, comportamiento esperado y alcance detectado.
- Inspeccionar lo necesario para detectar dependencias, riesgos y comportamiento actual.
- Preguntar solo por incertidumbres que no puedan resolverse inspeccionando el proyecto y que cambien alcance, diseño o criterios de aceptación.
- En tareas multi-repo, identificar explícitamente qué repos participan y sus dependencias.
- No escribir código ni modificar archivos de implementación.

## 3. Plan obligatorio en disco

Crear `plan.md` en la raíz del workspace. Si se trabaja con un único repo abierto de forma aislada, crearlo en la raíz de ese repo.

En tareas multi-repo:
- `workspace/plan.md` es el plan coordinador: objetivo, repos participantes, contratos, dependencias y orden de integración.
- Crear `frontend/plan.md`, `backend/plan.md` u otros planes secundarios solo si cada repo requiere pasos técnicos específicos.
- Los planes secundarios enlazan al coordinador con rutas relativas. No duplicar el plan completo.

El plan debe incluir:

```markdown
# Plan: <tarea>

## Objetivo y resultado esperado
## Alcance
- Repositorios incluidos:
- Repositorios excluidos:
## Contexto actual
## Decisiones confirmadas
## Suposiciones y restricciones
## Contratos/dependencias entre repositorios
## Archivos o módulos a inspeccionar/modificar
## Criterios de aceptación
## Estrategia de pruebas
## Tareas
- [ ] Tarea concreta y verificable
  - Repositorio:
  - Archivos/módulos:
  - Prueba RED:
  - Cambio mínimo GREEN:
  - Refactor/verificación:
  - Dependencias:
## Riesgos y casos límite
## Enfoque pragmático y estoico
- Valor mínimo a entregar / qué se omite deliberadamente:
- Bajo nuestro control:
- Fuera de nuestro control y cómo se mitiga:
- Qué puede salir mal y respuesta prevista:
## Economía de código
- Reutilización (código existente, plataforma, dependencias instaladas):
- Funciones/opciones no pedidas que se omiten:
## Web moderna (si aplica)
- Objetivo Baseline:
- Características nativas usadas y fallbacks:
## Justificación de cambios de esquema SQL (solo si son inevitables)
## Tareas no realizables y alternativas (si aplica)
## Pendientes/preguntas abiertas
## Comandos de verificación
## Estado y siguiente paso
## Registro de decisiones
```

Incluir ejemplos de entradas/salidas, payloads, respuestas esperadas, reglas de negocio y comandos cuando sean relevantes. Separar hechos confirmados, suposiciones y pendientes. Las tareas deben ser lo bastante detalladas para que otro agente pueda ejecutarlas sin reconstruir la conversación.

Revisar coherencia y preguntas abiertas, presentar al usuario un resumen del plan, repos afectados, riesgos y verificaciones. **Detenerse y esperar aprobación explícita.** Una petición de cambiar el plan no autoriza a ejecutarlo.

## 4. Ejecución TDD (solo con aprobación)

1. Volver a revisar `git status` de cada repo afectado. Preservar cambios preexistentes del usuario.
2. Seguir el orden y las dependencias del plan.
3. Por cada cambio de comportamiento:
   - **RED:** escribir una prueba que exprese el comportamiento esperado y verificar que falla por el motivo correcto.
   - **GREEN:** implementar el mínimo cambio para pasarla.
   - **REFACTOR:** simplificar/refactorizar sin cambiar el comportamiento y volver a probar.
4. En cambios frontend/backend, verificar ambos lados del contrato y agregar pruebas de integración/contrato cuando el proyecto lo permita.
5. Ejecutar pruebas específicas y luego suites, lint y build pertinentes.
6. Actualizar casillas y estado de `plan.md`.
7. Si aparece una decisión nueva, contradicción o riesgo no contemplado, detener la parte afectada y consultar al usuario.
8. No ocultar fallos preexistentes ni debilitar pruebas/cobertura para obtener un resultado verde.
9. No ampliar silenciosamente el alcance.
10. No hacer commits ni publicar cambios sin autorización.

## 5. Entrega y validación del usuario

Informar cambios separados por repo, pruebas ejecutadas y resultados, lint/build, pruebas no ejecutadas y motivo, riesgos y pasos concretos de validación. Cerrar con una o dos líneas de lo omitido o no verificado y los riesgos que el usuario debe conocer; si algo no fue realizable, explicar el motivo y las alternativas. Esperar confirmación del usuario. Si reporta defectos, reabrir el plan y corregir con TDD.

## 6. Resumen después de la validación

Solo después de que el usuario confirme la validación:

- Crear `<repo>/.ai/summaries/YYYY-MM-DD-nombre-corto.md` en cada repo modificado.
- Actualizar `summaries/INDEX.md`.
- Actualizar `.ai/WORKSPACE_MEMORY.md` con coordinación y contratos compartidos cuando la tarea sea multi-repo.
- Actualizar `PROJECT_MEMORY.md` solo con conocimiento estable y reutilizable.
- Dejar `HANDOFF.md` reflejando el estado real: siguiente paso si sigue abierto, o sin tarea activa si terminó.

Cada resumen debe registrar solicitud, resultado validado, repos/archivos modificados, decisiones, pruebas y resultados, comandos, limitaciones, ruta del plan y pendientes.

## 7. Revisión de trabajos anteriores

Cuando el usuario pida revisar, ampliar o corregir algo:
1. Consultar índices y resúmenes relacionados antes de empezar.
2. Verificar el estado actual del código; no asumir que el resumen sigue vigente.
3. Reutilizar decisiones y pruebas que aún apliquen.
4. No repetir análisis/verificaciones sin motivo si no hubo cambios relevantes.
5. Si el código cambió, documentar diferencias y actualizar el plan antes de nuevos cambios.
6. Mantener resúmenes anteriores como historial.

## 8. Handoff entre agentes o sesiones

Antes de cambiar de modelo/proveedor o cerrar una sesión, actualizar `HANDOFF.md`:

```markdown
# Handoff

## Solicitud y objetivo
## Repositorios dentro del alcance
## Estado actual
## Plan activo (ruta)
## Decisiones confirmadas
## Archivos modificados
## Pruebas ejecutadas y resultados
## Fallos conocidos/bloqueos
## Próximo paso exacto
## Preguntas pendientes
## Qué no se debe repetir
```

Actualizar también el estado del plan. Indicar qué está terminado, en curso y pendiente, con rutas y comandos concretos. Solo resumir o iniciar un chat nuevo después de persistir el contexto. No borrar memoria ni planes para “limpiar” el chat.

## Comportamientos especiales

- Si el usuario dice “mira esto”, “revisa esto” o comparte código sin pedir cambios, responder únicamente lo solicitado; no iniciar implementación automáticamente.
- Si pide explicación, explicar sin crear `plan.md`.
- Si pide un cambio de código, seguir el flujo completo.
- Si pide omitir una fase, valorar el impacto y dejar constancia; nunca saltarse una aprobación requerida para ejecutar.
- Nunca afirmar que se leyó, creó, modificó o ejecutó algo si no ocurrió.
