# Preset: `<NOMBRE_DEL_STACK>` (Plantilla)

> **Uso:** Carga este archivo en `[PRESETS_ACTIVOS]` cuando el proyecto utilice esta tecnología. 
> Renombra y rellena este archivo en tu propio repositorio de proyecto, por ejemplo: `preset_react.md` o `preset_node.md`.

---

## 1. Versiones mínimas
- **Lenguaje:** *(ej. PHP 8.2+, Node 20+, Python 3.11+)*
- **Framework principal:** *(ej. Laravel 11, Next.js 14)*

---

## 2. Configuración general

- **Manejo de variables de entorno:** *(ej. Nunca usar `process.env` fuera de archivos config)*
- **Archivos ignorados / sensibles:** *(ej. `.env` nunca versionado)*

---

## 3. Estilo de código

- **Estándar:** *(ej. PSR-12, ESLint / Prettier, PEP8)*
- **Tipado:** *(ej. Tipos de retorno estrictos, TypeScript obligatorio sin `any`)*
- **Convenciones de nombres:** *(ej. variables en camelCase, clases en PascalCase)*

---

## 4. Inyección de dependencias / Arquitectura

- **Patrón esperado:** *(ej. Inyección por constructor, uso de hooks en vez de clases, etc.)*
- **Estructura de directorios recomendada:**
  ```
  /src
    /components
    /services
    /utils
  ```

---

## 5. Datos y persistencia

- **ORM / Query Builder recomendado:** *(ej. Prisma, Eloquent, SQLAlchemy)*
- **Reglas anti-N+1:** *(ej. Obligatorio usar eager loading, DataLoader, etc.)*
- **Migraciones:** *(ej. Nunca alterar el esquema sin un archivo de migración formal)*

---

## 6. Validación y autorización

- **Capa de validación:** *(ej. Zod para inputs, Form Requests en Laravel)*
- **Políticas:** *(ej. Lógica de permisos siempre en middlewares o policies, nunca en los controladores)*

---

## 7. Manejo de Errores y Logging

- **Reglas de excepciones:** *(ej. Crear excepciones personalizadas por dominio, evitar atrapar excepciones globales y silenciarlas)*
- **Logueo:** *(ej. Usar Winston/Pino con formato estructurado)*

---

## 8. Anti-patrones específicos del Stack a evitar

- *(ej. Usar `var` en vez de `let`/`const`)*
- *(ej. Llamadas asíncronas no manejadas (missing `await`))*
- *(ej. Mutar objetos de estado directamente en React)*
