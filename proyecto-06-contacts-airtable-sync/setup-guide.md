# Guía de Configuración — Proyecto 6: Sincronización Bidireccional Contacts ↔ Airtable

Esta guía documenta cómo construir una sincronización bidireccional segura entre Google Contacts y Airtable, con dos workflows.

---

## Requisitos previos

- Cuenta en [n8n.cloud](https://n8n.cloud)
- Cuenta de Google con contactos
- Cuenta de Airtable
- Tiempo estimado: 4–5 horas

---

## Parte 1 — Tabla en Airtable

Crear una tabla `Contactos` con estos campos:

| Campo | Tipo |
|-------|------|
| Nombre | Single line text |
| Email | Email o Single line text |
| Teléfono | Phone o Single line text |
| Empresa | Single line text |
| Última actualización | Single line text |
| Sincronizado a Google | **Checkbox** |

> El campo "Última actualización" se deja como texto y se formatea desde n8n con Luxon (`$now.toFormat(...)`), para tener control total sobre el formato.

---

## WORKFLOW 1 — Google Contacts → Airtable

### Paso 1: Schedule Trigger
Crear workflow `Google Contacts to Airtable`. Agregar **Schedule Trigger** (cada hora, o más seguido para pruebas).

### Paso 2: Get many contacts
1. Agregar **Google Contacts → Get Many**.
2. **Credential:** OAuth2 (requiere permiso de Contacts; si ya hay credencial de Google, puede necesitar reautorización).
3. **Return All:** activado.
4. **Fields:** seleccionar Names, Email Addresses, Phone Numbers, Organizations.

> ⚠️ Error común: `personFields mask is required`. Se resuelve seleccionando los Fields. Google obliga a especificar qué datos traer.

### Paso 3: Filter (solo contactos con email)
1. Agregar nodo **Filter** entre Get contacts y Airtable.
2. Condición: el email NO está vacío.
   - Valor: `{{ Object.values($json.emailAddresses || {})[0]?.[0] }}`
   - Operador: **is not empty**

> ⚠️ Sin este filtro, los contactos sin email causan el error "Record must include columns to merge on".

### Paso 4: Airtable Upsert
1. Agregar **Airtable → Create or Update (Upsert)**.
2. **Credential:** Airtable (dar acceso a la base Contactos al reconectar).
3. **Base:** Contactos. **Table:** la tabla.
4. **Columns to match on:** **Email**.
5. Mapear (según la estructura real de Google People):

| Columna | Expresión |
|---------|-----------|
| Email | `{{ Object.values($json.emailAddresses || {})[0]?.[0] }}` |
| Nombre | `{{ $json.names?.displayName }}` |
| Teléfono | `{{ $json.phoneNumbers?.mobile?.[0] }}` |
| Empresa | `{{ $json.organizations?.name }}` |
| Última actualización | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |

> 💡 Importante: inspeccionar el JSON crudo de Get contacts antes de mapear. La estructura real puede diferir de lo esperado (ej. `names` es objeto directo, no lista).

6. Ejecutar y verificar en Airtable. Ejecutar de nuevo: NO debe duplicar (upsert idempotente).

---

## Preparación del anti-bucle (una sola vez)

Antes de construir el Workflow 2, marcar como **Sincronizado a Google ✅** todos los contactos que YA vinieron de Google (los que están en Airtable). Así solo quedan "no sincronizados" los que nacen en Airtable.

Esto se hace una vez, manualmente en Airtable, para establecer un punto de partida limpio.

---

## WORKFLOW 2 — Airtable → Google Contacts

### Paso 1: Schedule Trigger
Crear workflow separado `Airtable to Google Contacts`. Agregar **Schedule Trigger**.

### Paso 2: Airtable Search (no sincronizados)
1. Agregar **Airtable → Search**.
2. **Base:** Contactos. **Table:** la tabla.
3. **Return All:** activado.
4. **Filter By Formula:**
   ```
   NOT({Sincronizado a Google})
   ```

> Esta fórmula (sintaxis de Airtable) trae solo los registros con la casilla desmarcada. Es el corazón del anti-bucle.

### Paso 3: Google Contacts Create
1. Agregar **Google Contacts → Create**.
2. **Given Name:** `{{ $json.fields.Nombre }}`
3. En **Additional Fields**:
   - **Email → Value:** `{{ $json.fields.Email }}` (Type: Home, valor fijo)
   - **Phone → Value:** `{{ $json.fields.Teléfono }}` (Type: Mobile, valor fijo)

> ⚠️ Error común: poner la misma expresión en Type y Value. El **Type** es la etiqueta (home/mobile) y va fijo o vacío; el **Value** es el dato real. El email va solo en Email→Value; el teléfono solo en Phone→Value.

### Paso 4: Airtable Update (marcar como sincronizado)
1. Agregar **Airtable → Update**.
2. **Base:** Contactos. **Table:** la tabla.
3. **Columns to match on:** id.
4. **id (using to match):** `{{ $('Search records').item.json.id }}`
5. **Sincronizado a Google:** activar (true).

> Esto cierra el anti-bucle: marca el contacto recién creado para que no se vuelva a procesar.

---

## Pruebas finales

1. Ejecutar Workflow 2 → crea en Google los no sincronizados y los marca.
2. Ejecutar de nuevo → "No output data" (no hay nada sin sincronizar). ✅ Anti-bucle confirmado.
3. Crear un contacto nuevo en Airtable (sin marcar) → ejecutar → se crea en Google y se marca.
4. Verificar en Google Contacts que el contacto aparece completo (nombre, email, teléfono).

---

## Errores encontrados y soluciones

| Error / Situación | Causa | Solución |
|-------------------|-------|----------|
| `personFields mask is required` | No se especificaron los campos | Seleccionar Fields (Names, Emails, Phones, Orgs) |
| Base "Contactos" no aparece | La credencial no tenía acceso a esa base | Reconectar Airtable y agregar la base |
| Expresiones devuelven `undefined` | Estructura real distinta a la esperada | Inspeccionar el JSON crudo y ajustar las rutas |
| "Record must include columns to merge on" | Contactos sin email + match por email | Agregar Filter "email is not empty" |
| Search trae todos los registros | Ningún contacto estaba marcado | Marcar manualmente los de Google una vez |
| Email/teléfono mal en Google | Expresión en Type en vez de Value | Email→Value y Phone→Value; Type fijo |

---

## Posibles mejoras futuras

- **Resolución de conflictos:** manejar ediciones simultáneas (última escritura gana o merge por campo).
- **Actualización bidireccional de existentes** (no solo creación de nuevos).
- **Borrado sincronizado:** reflejar eliminaciones entre sistemas.
- **Separar nombre y apellido** (Given Name / Family Name) en lugar de un solo campo.
