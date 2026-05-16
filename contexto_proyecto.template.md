# CONTEXTO_PROYECTO — `<NOMBRE_PROYECTO>`

> Plantilla a rellenar por proyecto. Se carga como `[CONTEXTO_PROYECTO]` en cualquier sesión que toque este código. Dejar vacíos los campos no aplicables; eliminar secciones completas si no aplican al proyecto.

---

## 1. Identificación

- **Nombre:** 
- **Repositorio:** 
- **Wiki / docs:** 
- **Responsable técnico:** 
- **Estado del proyecto:** ⬜ greenfield · ⬜ migración legacy · ⬜ mantenimiento · ⬜ otro: 

---

## 2. Stack

| Capa | Tecnología | Versión |
|---|---|---|
| Lenguaje principal | | |
| Framework | | |
| Base de datos | | |
| Cache / colas | | |
| Servidor web | | |
| Runtime / OS host | | |
| ORM / acceso a datos | | |
| Autenticación | | |
| Logging | | |

---

## 3. Entornos

| Entorno | Host | OS | Notas |
|---|---|---|---|
| Dev local | | | |
| Staging | | | |
| Producción | | | |

---

## 4. Rutas críticas

- **Código fuente:** 
- **Configuración:** 
- **Logs:** 
- **Storage / uploads:** 
- **Documentación / wiki:** 
- **Scripts de automatización:** 

---

## 5. Arquitectura

- **Patrón general:** ⬜ monolito modular · ⬜ hexagonal · ⬜ microservicios · ⬜ DDD · ⬜ otro: 
- **Estructura de directorios principal:**
  ```
  
  ```
- **Convenciones de naming:** 
- **Dependencias clave entre módulos:** 

---

## 6. Base de datos

- **Motor + versión:** 
- **Esquemas / conexiones múltiples:** 
- **Vistas pre-armadas relevantes** *(usar antes que reescribir joins crudos):*
  | Vista | Reemplaza join sobre | Notas |
  |---|---|---|
  | | | |
- **Estructuras auxiliares / tablas hija** *(EAV u otras — documentar si aplica):*
  | Tabla | Propósito | Relación |
  |---|---|---|
  | | | |
- **Tablas de auditoría:** 
- **Convenciones de nombres de tablas/columnas:** 
- **Bind variables y patrones de query:** 
- **Serialización JSON:** *(indicar si aplica patrón EAV + `json_encode()` en PHP por restricciones del motor)*

---

## 7. Glosario de dominio

| Término / abreviación | Significado |
|---|---|
| | |

---

## 8. Antipatrones prohibidos en este proyecto

> Cosas que el código legacy hace y que NO se replican en el nuevo:

- 
- 
- 

---

## 9. Zonas de información (clasificación AI_CONTEXT)

- **🔴 Roja** *(nunca sale del entorno local):* 
- **🟡 Amarilla** *(sale fragmentada, nunca completa):* 
- **🟢 Verde** *(sin restricción):* 

---

## 10. Presets activos

Marca los que aplican; el agente debe cargar esos archivos junto al `master_prompt.md`:

- ⬜ `preset_laravel.md`
- ⬜ `preset_python.md`
- ⬜ `preset_oracle.md`
- ⬜ otros: 

---

## 11. Reglas operativas del proyecto

- **Cuándo preguntar primero:** 
- **Cuándo proceder sin preguntar:** 
- **Decisiones ya tomadas que NO se cuestionan en este proyecto:** 
- **Personas a notificar ante decisiones de arquitectura:** 

---

## 12. Notas libres

> Cualquier cosa que un agente nuevo necesite saber para no perder tiempo.
