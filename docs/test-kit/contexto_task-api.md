# CONTEXTO_PROYECTO — `task-api`

> Proyecto dummie para validación del motor My AI Engine en OpenCode.
> No es un proyecto real. Usar exclusivamente para pruebas del motor.

---

## 1. Identificación

- **Nombre:** task-api
- **Repositorio (WSL2):** `/home/lrivas/proyectos/task-api/`
- **Responsable técnico:** riv4wi
- **Estado del proyecto:** ☑ greenfield (en construcción)

---

## 2. Stack

| Capa | Tecnología | Versión |
|---|---|---|
| Lenguaje principal | PHP | 8.2 |
| Framework | Laravel | 11 |
| Base de datos | MySQL | 8.0 |
| Cache / colas | Redis | 7.x |
| Runtime / OS host | Ubuntu 22.04 sobre WSL2 | — |
| ORM / acceso a datos | Eloquent | — |
| Autenticación | Laravel Sanctum | — |
| Logging | Monolog (Laravel default) | — |

---

## 3. Entornos

| Entorno | Host | OS | Notas |
|---|---|---|---|
| Dev local | WSL2 Ubuntu | Ubuntu 22.04 | MySQL local + Redis local |
| Producción | — | — | Pendiente definir |

---

## 4. Rutas críticas

- **Código fuente (WSL2):** `/home/lrivas/proyectos/task-api/`
- **Código fuente (Windows UNC):** `\\wsl.localhost\Ubuntu\home\lrivas\proyectos\task-api`
- **Configuración:** `config/`
- **Logs:** `storage/logs/`
- **Motor IA:** `my-ai-engine/` (preset_laravel.md + contexto_task-api.md)

---

## 5. Arquitectura

- **Patrón general:** ☑ monolito modular

**Estructura de directorios:**
```
task-api/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   └── TaskController.php
│   │   └── Requests/
│   │       ├── StoreTaskRequest.php
│   │       └── UpdateTaskRequest.php
│   ├── Models/
│   │   └── Task.php
│   ├── Actions/
│   │   ├── CreateTaskAction.php
│   │   ├── UpdateTaskAction.php
│   │   └── DeleteTaskAction.php
│   └── DTOs/
│       └── TaskDTO.php
├── config/
│   └── tasks.php
├── database/
│   └── migrations/
├── routes/
│   └── api.php
└── my-ai-engine/
    ├── preset_laravel.md
    └── contexto_task-api.md
```

- **Convenciones de naming:** PascalCase clases, camelCase métodos, snake_case columnas BD.
- **Sin patrón Repository** — Actions acceden a Eloquent directamente.

---

## 6. Base de datos

- **Motor + versión:** MySQL 8.0.
- **Tabla principal:**

```sql
tasks (
  id          BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  title       VARCHAR(255)    NOT NULL,
  description TEXT,
  status      ENUM('pending','in_progress','done') DEFAULT 'pending',
  priority    TINYINT UNSIGNED DEFAULT 1,  -- 1=baja, 2=media, 3=alta
  due_date    DATE,
  created_at  TIMESTAMP,
  updated_at  TIMESTAMP
)
```

---

## 7. Glosario de dominio

| Término | Significado |
|---|---|
| Task | Tarea del sistema |
| Status | Estado: pending, in_progress, done |
| Priority | Prioridad: 1=baja, 2=media, 3=alta |

---

## 8. Antipatrones prohibidos en este proyecto

- `env()` directo en código de aplicación (solo en `config/`).
- Lógica de negocio en Controllers — solo orquestación.
- `Carbon::now()` sin timezone.
- `mixed` o ausencia de tipo en métodos públicos.
- Acceso a Eloquent directo desde Controllers.
- `dd()` / `dump()` en commits.

---

## 9. Zonas de información

- **🔴 Roja:** credenciales de BD, APP_KEY.
- **🟢 Verde:** todo lo demás (es proyecto dummie de pruebas).

---

## 10. Presets activos

- ☑ `preset_laravel.md` (en `my-ai-engine/preset_laravel.md`)

---

## 11. Reglas operativas del proyecto

- **Cuándo preguntar primero:** siempre que falte información sobre el comportamiento esperado de una feature.
- **Cuándo proceder sin preguntar:** aplicar PSR-12, `strict_types`, imports ordenados.
- **Decisiones ya tomadas que NO se cuestionan:**
  - Stack: Laravel 11 / PHP 8.2 / MySQL 8.0.
  - Arquitectura: Actions + DTOs + Models. Sin repositorios.
  - Sanctum para autenticación.
  - Enum nativo PHP para status de Task.

---

## 12. Notas libres

- Proyecto exclusivamente para validar el comportamiento del motor My AI Engine en OpenCode.
- No conectar a bases de datos reales ni deployar en ningún servidor.
