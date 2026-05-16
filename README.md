# Motor personal de pensamiento — `ai-motor/`

Sistema modular de prompts para estandarizar y escalar el trabajo con asistentes de IA.

> **📖 Empezá por aquí:** abrí `docs/MANUAL_motor_personal.pdf`. Tiene la guía completa (Nota: el PDF debe ser actualizado para coincidir con esta nueva estructura simplificada).

---

## Filosofía del Motor

El "AI Motor" es un framework minimalista que consta de **solo 3 componentes principales**. La idea es que este repositorio sea únicamente el **motor**, mientras que los archivos específicos de cada proyecto (contextos, arquitecturas, base de datos) deben vivir dentro del repositorio de código de cada proyecto individual.

## Los 3 Archivos Núcleo

1. **`master_prompt.md`**: El **núcleo universal e inmutable**. Contiene las instrucciones base, la filosofía de trabajo, el formato de salida y las reglas de seguridad que la IA debe seguir *siempre*, sin importar el proyecto. Se carga en todas las sesiones.
2. **`preset_stack.md`**: Una plantilla para crear módulos de **tecnología**. Define las mejores prácticas, reglas de sintaxis y arquitectura específicas para el lenguaje o framework que se vaya a usar (ej. Laravel, React, Python).
3. **`contexto_proyecto.template.md`**: Es la **plantilla de inicio de proyecto**. Sirve para darle a la IA toda la información vital del proyecto en curso (qué es, qué arquitectura tiene, tareas pendientes, etc.) antes de empezar a programar.

---

## Estructura del Repositorio

```text
ai-motor/
├── README.md                              ← Este archivo
├── master_prompt.md                       ← Núcleo universal (siempre se carga)
├── preset_stack.md                        ← Plantilla base para reglas de stack
├── contexto_proyecto.template.md          ← Plantilla base para el contexto de un proyecto
└── docs/
    └── MANUAL_motor_personal.pdf          ← Manual de uso en profundidad
```

---

## Ejemplo de Flujo de Trabajo en un Proyecto Real

Imagina que estás trabajando en un proyecto llamado **apibml** que usa **Laravel** y **Oracle**.

En lugar de guardar los contextos en esta carpeta de `ai-motor`, guardarás tus archivos instanciados en la carpeta de tu propio proyecto: `/home/user/proyectos/apibml/`.

### 1. Preparación (Una sola vez por proyecto)
Copias las plantillas del motor hacia tu proyecto y las llenas con los datos reales:
- Copias `preset_stack.md` a tu proyecto y lo llamas `preset_laravel.md`, llenándolo con reglas estrictas de PHP y Laravel.
- Copias `contexto_proyecto.template.md` a tu proyecto y lo llamas `contexto_apibml.md`, indicando las rutas, credenciales locales, versiones y antipatrones de ese proyecto específico.

### 2. Carga en una Sesión Diaria
Cuando inicies una nueva conversación con la IA para trabajar en un ticket de **apibml**, le adjuntas estos 3 archivos:
1. `ai-motor/master_prompt.md` *(el motor)*
2. `proyectos/apibml/preset_laravel.md` *(las reglas del stack)*
3. `proyectos/apibml/contexto_apibml.md` *(el estado actual del proyecto)*

Para una sesión genérica (sin proyecto): solo cargas `master_prompt.md`.

---

## Trigger del modo SoT

El protocolo Sketch-of-Thought se activa solo si tu mensaje contiene:
- `modo SoT`
- `/sot`
- O pedís explícitamente "haz un sketch primero"

Sin trigger, el asistente responde directo (formato normal).

---

## Mantenimiento

Versionar esta carpeta en Git como repo personal. **No** agregar aquí contextos de proyecto que tengan datos sensibles; esos pertenecen a los repositorios de sus respectivos proyectos.

Cuando detectes una corrección recurrente genérica que el asistente no aprende → agregala como regla a tu `preset_stack.md` o al `master_prompt.md` según corresponda.

---

Versión 2.0 — Estructura modular simplificada a 3 pilares.
