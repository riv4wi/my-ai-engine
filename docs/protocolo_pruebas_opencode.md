# Protocolo de Pruebas — My AI Engine
## Proyecto: `task-api` · Plataforma: OpenCode Desktop 1.15.5

---

## Configuración del escenario

Antes de ejecutar las pruebas, definir el escenario de filesystem. El comportamiento del agente en T5 y T7 varía según este parámetro.

### ¿Qué significa Filesystem ON/OFF?

**Filesystem OFF** — el agente trabaja ciego. Solo conoce lo que está en el `AGENTS.md` y lo que vos pegás en el chat. Si pedís ver un archivo, no puede abrirlo — depende de que vos lo pegues o de que él lo haya generado en la sesión actual. Es el modo más portable entre plataformas (Claude web, ChatGPT, Gemini).

**Filesystem ON** — el agente tiene acceso real al disco del proyecto. Puede abrir, leer y escribir archivos directamente. En OpenCode Desktop apuntando a WSL2, el agente opera sobre `/home/lrivas/proyectos/task-api/` como si fuera un desarrollador con acceso a la terminal.

### Cómo funciona el sistema de permisos en OpenCode Desktop (WSL2)

OpenCode solicita permiso explícito la primera vez que intenta acceder a una ruta fuera de su directorio base. El permiso se pide por ruta específica (ej: `/home/lrivas/proyectos/task-api/app/Models/*`).

- **"Permitir una vez"** — acceso solo para la sesión actual. La próxima sesión vuelve a preguntar. Recomendado mientras explorás el comportamiento.
- **"Permitir siempre"** — OpenCode guarda el permiso para ese proyecto. **Verificado en v1.15.5: es por proyecto, no global** — al abrir un proyecto diferente vuelve a solicitar permisos.

> **Comportamiento observado:** OpenCode intenta primero la ruta Windows UNC (`/wsl.localhost/Ubuntu/...`) que falla, luego resuelve correctamente a la ruta WSL2 (`/home/lrivas/proyectos/...`). Es el comportamiento esperado cuando OpenCode corre en Windows pero el proyecto está en WSL2.

### Implicación para las pruebas T5 y T7

| Prueba | Filesystem OFF | Filesystem ON |
|---|---|---|
| T5 | El agente debe mostrar el código del bloque afectado en el chat | El agente lee el archivo real — una referencia con código visible es válida |
| T7 | El agente debe mostrar el DTO en el chat | El agente abre el archivo y muestra el contenido con syntax highlighting |

**En ambos casos aplica la Regla 5:** nunca sustituir código por descripciones o metadata de ubicación sin código visible.

### Marcar el escenario antes de ejecutar

☐ **Filesystem OFF** — agente sin acceso al disco  
☐ **Filesystem ON** — agente con acceso al disco (permisos otorgados)

---

## Preparación

### Paso 1 — Crear la carpeta del proyecto dummie

```bash
mkdir -p ~/proyectos/task-api/my-ai-engine
```

### Paso 2 — Copiar los 3 archivos del motor

```bash
# Núcleo universal (desde my-ai-engine)
cp ~/proyectos/my-ai-engine/master_prompt.md \
   ~/proyectos/task-api/my-ai-engine/

# Preset instanciado para Laravel
cp /tmp/task-api_preset_laravel.md \
   ~/proyectos/task-api/my-ai-engine/preset_laravel.md

# Contexto del proyecto dummie
cp /tmp/contexto_task-api.md \
   ~/proyectos/task-api/my-ai-engine/contexto_task-api.md
```

### Paso 3 — Generar AGENTS.md y abrir en OpenCode

Ejecutar en WSL2 para generar el archivo que OpenCode Desktop lee automáticamente:

```bash
cat ~/proyectos/my-ai-engine/master_prompt.md \
    ~/proyectos/task-api/my-ai-engine/preset_laravel.md \
    ~/proyectos/task-api/my-ai-engine/contexto_task-api.md \
    > ~/proyectos/task-api/AGENTS.md
```

Luego abrir (o reabrir) `~/proyectos/task-api/` en OpenCode Desktop 1.15.5.

> **⚠️ Importante — el agente NO detecta cambios automáticamente.**
> `AGENTS.md` es un archivo estático que OpenCode lee solo al iniciar la sesión.
> Cada vez que modifiques cualquiera de los 3 archivos del motor, seguir este flujo:
> ```bash
> # 1. Copiar el archivo modificado a su lugar
> cp /tmp/master_prompt.md ~/proyectos/my-ai-engine/master_prompt.md
>
> # 2. Regenerar AGENTS.md
> cat ~/proyectos/my-ai-engine/master_prompt.md \
>     ~/proyectos/task-api/my-ai-engine/preset_laravel.md \
>     ~/proyectos/task-api/my-ai-engine/contexto_task-api.md \
>     > ~/proyectos/task-api/AGENTS.md
>
> # 3. Abrir nueva sesión en OpenCode (no continuar la sesión anterior)
> ```

---

## T1 — Carga y confirmación del motor

**Objetivo:** verificar que el agente leyó los 3 archivos y puede resumir el contexto activo.

**Prompt a copiar:**
```
Confirmá que leíste los archivos del motor. Dame un resumen de:
1. Las 3 reglas más importantes del master_prompt
2. La regla más importante del preset Laravel
3. El stack y arquitectura del proyecto actual
```

**Esperado:**
- Lista reglas reales del `master_prompt.md` — no las inventa.
- Menciona `config()` vs `env()` o `strict_types` del preset.
- Describe stack correcto: Laravel 11, PHP 8.2, MySQL, Actions + DTOs, sin repositorios.
- Sin relleno, sin elogios, sin "Como experto...".

**Incorrecto:**
- Inventa reglas no declaradas.
- Describe stack genérico sin mencionar el proyecto.
- Responde con disclaimers o frases de prefijo.

**Resultado:** ☐ PASS  ☐ FAIL
**Notas:**

---

## T2 — Resistencia a suposiciones (Regla 1)

**Objetivo:** verificar que pregunta antes de actuar cuando falta información.

**Prompt a copiar:**
```
Creá el endpoint para listar tareas.
```

**Esperado:**
- Detecta información faltante.
- Hace **una sola pregunta** antes de proponer el plan.
- Preguntas válidas: ¿con paginación?, ¿filtros por status/priority?, ¿requiere auth Sanctum?
- No genera código de inmediato.

**Incorrecto:**
- Genera el endpoint directamente asumiendo paginación u otros parámetros.
- Hace 3 o más preguntas en un solo turno.
- Pregunta algo que ya está en el contexto (stack, arquitectura).

**⚠️ Nota de sesión:** modificar el motor y recargar archivos dentro de la misma sesión no es suficiente — el agente conserva el contexto previo. Siempre abrir sesión nueva en OpenCode tras regenerar `AGENTS.md`.

**Resultado:** ☑ PASS (sesión nueva) — el agente detectó información faltante, presentó plan y preguntó "¿Procedo?" antes de actuar.
**Notas:** requirió 3 intentos. 1° y 2° fallaron por sesión anterior persistida. 3° (sesión nueva con motor actualizado) PASS. — Plan → aprobación → ejecución (Regla 2)

**Objetivo:** verificar que presenta plan y espera OK antes de escribir código.

**Prompt a copiar:**
```
Creá el CreateTaskAction con validación de título obligatorio
y prioridad entre 1 y 3.
```

**Esperado:**
- Presenta plan breve: qué archivos toca, qué hace cada uno.
- Se **detiene** y espera OK.
- No escribe ninguna línea de PHP sin aprobación.

**Incorrecto:**
- Genera código directamente sin plan.
- Presenta plan y continúa solo sin esperar respuesta.

**Resultado:** ☑ PASS — presentó plan correcto, respetó arquitectura del preset (validación en Form Request, no en Action), preguntó "¿Procedo?" antes de generar código.
**Notas:** El agente corrigió proactivamente la premisa del prompt (validación no va en Action sino en Form Request) sin que se le indicara.

---

## T4 — Regla config() vs env() del preset

**Objetivo:** verificar que aplica la regla del preset al generar código.

**Setup:** responder `OK` al plan de T3 para que genere el `CreateTaskAction`.

**Prompt a copiar (después del OK de T3):**
```
Generá también el archivo config/tasks.php con una clave
debug_tasks que lea del .env la variable TASKS_DEBUG.
```

**Esperado:**
- En `config/tasks.php` usa `env('TASKS_DEBUG', false)` — correcto, estamos dentro de `/config/`.
- En cualquier otro archivo PHP usa `config('tasks.debug_tasks')` — nunca `env()`.
- `declare(strict_types=1);` en cada archivo PHP generado.
- Tipos de retorno explícitos en todos los métodos.

**Incorrecto:**
- Usa `env('TASKS_DEBUG')` en un Service, Action o Controller.
- Omite `declare(strict_types=1)`.
- Métodos sin tipo de retorno.

**Resultado:** ☑ PASS — `env()` usado correctamente dentro de `config/`, `declare(strict_types=1)` presente, cast a `bool` explícito, acceso en runtime vía `config('tasks.debug_tasks')`.
**Notas:** Archivo mínimo y correcto. Sin antipatrones.

---

## T5 — Código mínimo verificable, nunca metadata (Regla 5)

**Objetivo:** verificar que ante una solicitud sobre un archivo existente, el agente muestra código verificable en lugar de descripciones o metadata.

**Setup:** tener el `CreateTaskAction` generado en T3/T4.

**Prompt a copiar:**
```
Agregá un campo opcional due_date al CreateTaskAction.
```

**Esperado:**
- Muestra el fragmento de código relevante (método o bloque afectado) — no el archivo completo si es grande.
- Si el campo ya existe, muestra el código donde está implementado y pregunta qué específicamente se quiere cambiar.
- Nunca sustituye código por descripciones, tablas de metadata o referencias a números de línea sin código.

**Incorrecto:**
- Responde solo con texto descriptivo: "ya está en la línea 12" sin mostrar código.
- Devuelve una tabla de metadata sin ninguna línea de PHP.
- Dice "no requiere cambios" sin mostrar nada.

**Resultado:** ☑ PASS (tercera iteración con regla ajustada) — mostró el fragmento relevante con código exacto y contexto de línea, luego preguntó qué específicamente cambiar antes de actuar.
**Notas:** Requirió 3 iteraciones para llegar a la redacción correcta de la Regla 5. La versión final "código mínimo verificable, nunca metadata" es más precisa y token-eficiente que "archivo completo siempre".

---

## T6a — Sin trigger → sin SoT

**Objetivo:** verificar que sin trigger no aparece el bloque SKETCH.

**Prompt a copiar:**
```
¿Qué índices de MySQL recomendarías para la tabla tasks
considerando que el filtro más común es por status + due_date?
```

**Esperado:**
- Responde directamente con la recomendación.
- **No** genera bloque `═══ SKETCH ═══`.
- Respuesta concisa en prosa o lista simple.

**Incorrecto:**
- Genera SKETCH sin que se lo pidiera.

**Resultado:** ☑ PASS — respuesta directa sin bloque SKETCH. Técnica, con justificación del orden de columnas. Detectó proactivamente el estado de índices existentes.
**Notas:** Bonus: preguntó "¿Creo una migración?" antes de actuar — Regla 2 aplicada espontáneamente.

---

## T6b — Con trigger SoT → con SoT

**Objetivo:** verificar que con trigger aparece el bloque SKETCH y el breakpoint.

**Prompt a copiar:**
```
modo SoT Diseñá la migración completa para la tabla tasks
con todos sus índices y constraints.
```

> **Nota sobre triggers:** usar `modo SoT` como trigger principal. `/sot` fue ignorado por Big Pickle en la primera ejecución — no es un comando del sistema sino texto plano, y no todos los modelos lo reconocen con la misma fiabilidad. `modo SoT` es semánticamente más explícito en español y resultó más confiable en las pruebas.

**Esperado:**
- Genera bloque `═══ SKETCH ═══` con `[META]`, `[LOGIC]`, `[RESTR]`, `[RIESGO]`.
- Se detiene en `═══ BREAKPOINT ═══` y espera OK.
- No continúa a `═══ SOLUCIÓN ═══` sin aprobación.

**Incorrecto:**
- No genera SKETCH.
- Genera SKETCH + solución juntos sin pausa.

**Resultado:** ☑ PASS — bloque SKETCH completo con [META], [LOGIC], [RESTR], [RIESGO]. Breakpoint activado correctamente con dos decisiones no triviales identificadas. Preguntó antes de proceder.
**Notas:** `/sot` fue ignorado en primera ejecución. `modo SoT` activó el protocolo correctamente en sesión nueva. En una ejecución posterior dentro de una sesión con contexto acumulado, `modo SoT` también falló. La causa exacta no está verificada — las hipótesis son: (1) contaminación de sesión por contexto acumulado de múltiples pruebas, (2) inconsistencia aleatoria del modelo, (3) combinación de ambas. Los sets de consistencia S1 (3/3 PASS) mostraron comportamiento estable en sesiones más limpias, pero no se replicó el escenario exacto del fallo para confirmarlo. **Recomendación:** si `modo SoT` no activa el protocolo, abrir sesión nueva antes de asumir que el motor tiene un problema.

---

## T7 — Tipado estricto y tipos de retorno

**Objetivo:** verificar que el PHP generado cumple las reglas del preset.

**Prompt a copiar:**
```
Generá el TaskDTO con los campos id, title, description,
status, priority y due_date. Usá readonly y constructor
nombrado fromModel.
```

**Esperado:**
- `declare(strict_types=1);` en la primera línea después de `<?php`.
- Todas las propiedades con tipo declarado.
- Clase marcada como `readonly`.
- `fromModel(Task $model): self` con tipo de retorno.
- Sin `mixed`, sin imports sin usar.

**Incorrecto:**
- Ausencia de `declare(strict_types=1)`.
- Propiedades o métodos sin tipo.
- `fromModel` sin tipo de retorno.

**Resultado:** ☑ PASS — con filesystem ON, OpenCode leyó el archivo real del disco y mostró el `TaskDTO` completo con syntax highlighting. `declare(strict_types=1)` confirmado, `readonly class`, 8 propiedades tipadas, `fromModel(Task $task): self` con tipo de retorno.
**Notas:** OpenCode solicitó permisos en cascada (`task-api/*` → `task-api/app/DTOs/*`) antes de leer. Con filesystem OFF este test requeriría verificar `declare(strict_types=1)` con un prompt adicional — ver resultado original en sección de hallazgos.

---

## Resumen de resultados

| # | Prueba | Regla validada | Resultado |
|---|---|---|---|
| T1 | Carga y confirmación del motor | Lectura de contexto | ☑ PASS |
| T2 | Resistencia a suposiciones | Regla 1 | ☑ PASS (sesión nueva) |
| T3 | Plan → aprobación → ejecución | Regla 2 | ☑ PASS |
| T4 | config() vs env() | Preset §2 | ☑ PASS |
| T5 | Código mínimo verificable, nunca metadata | Regla 5 | ☑ PASS (3° iteración) |
| T6a | Sin trigger → sin SoT | Master §4 | ☑ PASS |
| T6b | Con /sot → con SoT + breakpoint | Master §4 | ☑ PASS |
| T7 | Tipado estricto | Preset §3 | ☑ PASS |

---

## Qué hacer con los fallos

| Fallo | Acción correctiva |
|---|---|
| T1 | Verificar que los 3 archivos están cargados en OpenCode. |
| T2 | Agregar antipatrón explícito en `master_prompt.md` §7. |
| T3 | Reforzar Regla 2 con ejemplo negativo en `master_prompt.md`. |
| T4 | Agregar ejemplo negativo en `preset_laravel.md` §2. |
| T5 | Agregar ejemplo negativo en `master_prompt.md` §7. |
| T6a/T6b | Revisar §4 del `master_prompt.md` — descripción del trigger SoT. Si el trigger falla, abrir sesión nueva antes de asumir problema en el motor. |
| T7 | Agregar checklist explícito en `preset_laravel.md` §3. |

---

## Sets de consistencia (post-validación)

Ejecutados para verificar estabilidad de `modo SoT` y flujo de 4 pasos con Big Pickle en OpenCode Desktop 1.15.5.

### S1 — Consistencia de `modo SoT`

| Prueba | Condición | Resultado |
|---|---|---|
| S1a | Sesión nueva | ✅ PASS — SKETCH + BREAKPOINT + preguntas independientes |
| S1b | Misma sesión | ✅ PASS — mismo comportamiento |
| S1c | Misma sesión | ✅ PASS — agrupó preguntas pendientes de turnos anteriores |

### S2 — Consistencia del flujo de 4 pasos

| Prueba | Condición | Resultado |
|---|---|---|
| S2a | Sesión nueva | ✅ PASS — PASO 1→2→3→4 completo |
| S2b | Misma sesión | ✅ PASS — preguntó antes de planificar |
| S2c | Misma sesión | ✅ PASS — detectó dependencia faltante (modelo User inexistente) |

### Conclusión

Ambos comportamientos son **consistentes** en condiciones normales de uso con Big Pickle. El fallo de T6b ocurrió en una sesión con contexto muy acumulado — la causa exacta no está verificada (ver notas de T6b). **Recomendación general:** si un trigger o comportamiento falla, abrir sesión nueva como primer paso de diagnóstico antes de modificar el motor.
