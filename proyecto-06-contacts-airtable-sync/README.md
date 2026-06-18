# Proyecto 6: Sincronización Bidireccional Google Contacts ↔ Airtable

![Status](https://img.shields.io/badge/Status-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-Intermedia-orange)
![Tiempo](https://img.shields.io/badge/Tiempo-5%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Google%20Contacts%20%7C%20Airtable-purple)

## Descripción

Sincronización bidireccional entre Google Contacts y Airtable: los contactos fluyen en **ambos sentidos** de forma automática y segura. Los contactos de Google se reflejan en Airtable (sin duplicar), y los contactos nuevos creados en Airtable se crean en Google Contacts (sin generar bucles infinitos).

Este proyecto resuelve dos de los problemas más difíciles de la sincronización de datos: la **idempotencia** (poder ejecutar el flujo muchas veces sin crear duplicados) y la **prevención de bucles** (evitar que un cambio en un sistema rebote infinitamente entre ambos).

## Arquitectura: dos workflows coordinados

La sincronización bidireccional se implementó con **dos workflows separados**, una decisión de diseño deliberada: workflows independientes son más seguros, más fáciles de mantener y permiten apagar una dirección sin afectar la otra.

### Workflow 1 — Google Contacts → Airtable

```
┌──────────┐   ┌───────────────────┐   ┌────────────────────┐
│ Schedule │──▶│ Get many contacts │──▶│ Airtable (Upsert)  │
│ Trigger  │   │ (Google People)   │   │ match por Email    │
└──────────┘   └───────────────────┘   └────────────────────┘
```

### Workflow 2 — Airtable → Google Contacts

```
┌──────────┐   ┌──────────────────────┐   ┌──────────────┐   ┌─────────────────────┐
│ Schedule │──▶│ Airtable Search      │──▶│ Google       │──▶│ Airtable Update     │
│ Trigger  │   │ (no sincronizados)   │   │ Create       │   │ (marcar ✅)         │
└──────────┘   └──────────────────────┘   └──────────────┘   └─────────────────────┘
```

## Puntos técnicos destacados

### 1. Upsert idempotente (Google → Airtable)

En lugar de "crear siempre" (que generaría duplicados en cada ejecución), se usa **Upsert** (update + insert) con el **Email** como columna de coincidencia. Si el contacto ya existe en Airtable, se actualiza; si no, se crea.

**Resultado:** el workflow se puede ejecutar infinitas veces y los datos quedan limpios, sin duplicados. Esto se llama **idempotencia** y es una propiedad fundamental de una sincronización confiable.

### 2. Prevención de bucles (Airtable → Google)

El mayor riesgo de una sincronización bidireccional es el **bucle infinito**: un cambio en A actualiza B, que vuelve a disparar A, y así sin fin. Esto se resolvió con una **columna de control** en Airtable: `Sincronizado a Google` (checkbox).

- El Workflow 2 solo procesa contactos donde la casilla está **desmarcada** (filtro `NOT({Sincronizado a Google})`).
- Tras crear el contacto en Google, **marca la casilla** ✅.
- En la siguiente ejecución, ese contacto ya no se procesa.

**Resultado:** cada contacto se sincroniza una sola vez. No hay bucles. El sistema es seguro para ejecutarse de forma continua.

### 3. Manejo de datos anidados de una API real

Google People API devuelve datos con estructura anidada e irregular. Se usaron expresiones con acceso seguro (`?.`) para extraerlos sin romper el flujo cuando un campo falta:

```javascript
{{ $json.names?.displayName }}              // nombre
{{ $json.phoneNumbers?.mobile?.[0] }}       // teléfono
{{ Object.values($json.emailAddresses || {})[0]?.[0] }}  // email (estructura irregular)
```

### 4. Filtrado de casos especiales

Muchos contactos no tienen email. Como el Upsert usa el email como identificador, se agregó un nodo **Filter** que deja pasar solo los contactos con email, evitando el error "Record must include columns to merge on".

## Casos de uso reales

- **Equipos de ventas:** capturan leads en Airtable y los quieren disponibles en el teléfono (Google Contacts) para llamar/escribir.
- **Gestión centralizada de contactos:** mantener una base maestra en Airtable sincronizada con la agenda personal.
- **Migración y respaldo:** reflejar contactos entre sistemas sin perder datos ni duplicar.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Disparador | Schedule Trigger | Ejecuta la sincronización periódicamente |
| Origen/destino A | Google Contacts (People API) | Agenda de contactos |
| Origen/destino B | Airtable | Base de datos de contactos |
| Orquestación | n8n (Cloud) | Coordina ambas direcciones |
| Control anti-bucle | Columna checkbox en Airtable | Evita reprocesar contactos |

## Conceptos aprendidos

- **Patrón Schedule + Get/Search:** sincronización por sondeo periódico (polling) cuando no hay trigger de evento.
- **Upsert e idempotencia:** crear o actualizar sin duplicar, usando una columna de coincidencia.
- **Prevención de bucles** con una columna de control de estado.
- **Acceso seguro a datos anidados** con `?.` y manejo de estructuras irregulares de APIs.
- **Filtros (nodo Filter)** para manejar casos especiales (contactos sin email).
- **Filter By Formula de Airtable** (`NOT({campo})`) para consultas selectivas.
- **Arquitectura de dos workflows** para una sincronización bidireccional segura.

## Limitaciones y alcance

Esta implementación cubre la creación de contactos nuevos en ambas direcciones de forma segura. **No** incluye la resolución de conflictos de edición simultánea (cuando el mismo contacto se modifica en ambos sistemas a la vez), que es un problema avanzado de "última escritura gana" o "merge de campos". El diseño actual prioriza la seguridad (sin bucles, sin duplicados) sobre la sincronización de ediciones en tiempo real.

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa de ambos workflows.

## Valor para clientes (Workana)

Una sincronización bidireccional entre sistemas tiene un valor estimado de **$500–900 USD** en plataformas freelance, por su complejidad técnica (manejo de duplicados, bucles y datos anidados). Es un servicio que pocos saben implementar correctamente, lo que lo hace especialmente valioso.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas de ambos workflows funcionando.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
