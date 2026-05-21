# Preset: Laravel / PHP — `task-api`

> Instancia de `preset_stack.md` para el proyecto `task-api`.
> Carga este archivo junto a `master_prompt.md` y `contexto_task-api.md` en cada sesión.

---

## 1. Versiones mínimas
- **Lenguaje:** PHP 8.2+
- **Framework principal:** Laravel 11

---

## 2. Configuración general

- Usar `config('namespace.clave')` en código de aplicación.
- `env()` **solo** dentro de archivos `/config/*.php`. Nunca en runtime.
- Variables de feature flags en archivos de config dedicados (ej: `config('tasks.debug_tasks')`).
- `.env` nunca versionado. `.env.example` con comentarios por variable.

---

## 3. Estilo de código

- `declare(strict_types=1);` obligatorio en cada archivo PHP.
- **PSR-12** (estilo) + **PSR-4** (autoloading).
- Tipo de retorno explícito en cada método. `mixed` solo con justificación documentada.
- `use` ordenados alfabéticamente; sin imports sin usar.
- Nombres en inglés para infraestructura; español si el dominio lo exige.

---

## 4. Inyección de dependencias / Arquitectura

- **Inyección por constructor**; no facades en lógica de negocio.
- Facades aceptables solo en Controllers delgados o helpers.
- Interfaces para desacoplar implementaciones intercambiables (HTTP clients, mailers).
- **Sin patrón Repository** — acceso a datos directo vía Eloquent desde Actions/Services.

**Estructura de directorios:**
```
app/
├── Http/
│   ├── Controllers/     ← orquestación únicamente
│   └── Requests/        ← validación de input HTTP
├── Models/              ← Eloquent por tabla
├── Actions/             ← un caso de uso = una Action
├── Services/            ← lógica reutilizable
└── DTOs/                ← transporte tipado entre capas (readonly)
```

| Capa | Responsabilidad | Prohibido |
|---|---|---|
| Controller | Orquestar | Eloquent directo, validar input |
| Form Request | Validar input | Lógica de negocio |
| Policy | Autorizar | Lógica de negocio |
| Action / Service | Lógica de negocio | Acceder a HTTP request directo |
| DTO (`readonly`) | Transporte tipado | Mutabilidad |

DTOs: `readonly`, constructores nombrados `fromArray`, `fromModel`, `fromRequest`.

---

## 5. Datos y persistencia

- **Eager loading obligatorio** al iterar relaciones: `with()`. Cero N+1.
- **Migraciones estrictas:** tipos correctos, FKs explícitas, índices compuestos.
- **Estados/flags:** Enum nativo PHP 8.1+ o `tinyint` con valores acotados.
- **Transacciones** controladas en Controller o Action, no en Models.

---

## 6. Validación y autorización

- **Form Requests** para todo input HTTP.
- **Policies** para autorización; nunca lógica de auth en Controllers.
- Mensajes traducidos vía `lang/`. Cero strings duros visibles al usuario.

---

## 7. Manejo de errores y logging

- Excepciones específicas de dominio (`TaskNotFoundException`, `InvalidPriorityException`).
- Nunca `catch (\Exception $e)` sin relanzar o loguear con contexto.
- `Log::error($msg, ['context' => ...])`. Cero `dd()`/`dump()` en código que va a producción.

---

## 8. Fechas

- **Carbon obligatorio.**
- `now()->tz(config('app.timezone'))` — nunca `Carbon::now()` pelado.
- `Carbon::createFromFormat()` explícito al parsear formatos fijos.

---

## 9. Tests

- `php artisan test` como comando estándar.
- Un test por caso de uso en `tests/Feature/`.
- Factories para datos de prueba; nunca datos hardcodeados en tests.

---

## 10. Anti-patrones a evitar

- `env()` directo fuera de `config/`.
- `Carbon::now()` sin timezone.
- Lógica de negocio en Controller, Command o Job.
- Cargar relaciones dentro de bucles sin `with()`.
- `catch (\Exception $e)` genérico sin log.
- `mixed` o ausencia de tipo en métodos públicos de Actions/Services.
- `dd()` / `dump()` en commits.
