# Guía de Configuración — Proyecto 2: Slack → Airtable + Reacción

Esta guía documenta el proceso completo para construir el workflow, incluyendo los errores comunes que pueden aparecer y cómo resolverlos.

---

## Requisitos previos

- Cuenta en [n8n.cloud](https://n8n.cloud) (trial de 14 días suficiente)
- Cuenta en [Airtable](https://airtable.com) (trial de 14 días suficiente)
- Workspace en [Slack](https://slack.com) (gratuito)
- Tiempo estimado: 3–4 horas

---

## Parte 1 — Crear el workspace y canal de Slack

1. Crear un workspace en Slack (ej. `automation-test`).
2. Crear un canal: `#consultas`.

---

## Parte 2 — Crear la app de Slack

1. Ir a [https://api.slack.com/apps](https://api.slack.com/apps).
2. **Create New App** → **From scratch**.
3. Nombre: `n8n-automation`. Seleccionar el workspace.

### Configurar los Scopes (permisos)

En **OAuth & Permissions** → **Bot Token Scopes**, agregar:

```
app_mentions:read
channels:read
chat:write
incoming-webhook
reactions:write
users:read
```

### Obtener las credenciales

- **Bot User OAuth Token** (empieza con `xoxb-`): en *OAuth & Permissions*.
- **Signing Secret**: en *Basic Information* → *App Credentials*.

> ⚠️ Guardar ambos en un lugar seguro. Nunca subirlos a GitHub.

---

## Parte 3 — Crear la base de Airtable

1. Crear una base llamada `Slack Messages`.
2. En la tabla, crear los siguientes **campos (columnas)**:

| Campo | Tipo |
|-------|------|
| Author | Single line text |
| Message | Long text |
| Timestamp | Date |
| Channel | Single line text |

> 💡 **Error común:** crear los nombres como *filas* en lugar de *columnas*. Deben ser campos (columnas), no registros (filas).

### Obtener el token de Airtable

1. Avatar → **Account** → sección **API** → **Go to developer hub**.
2. **Create new token** (Personal Access Token).
3. Permisos: `data.records:read`, `data.records:write`, `schema.bases:read`.
4. Asociar la base `Slack Messages`.
5. Copiar y guardar el token.

> Nota: las API keys clásicas de Airtable fueron descontinuadas en 2024. Se usan *Personal Access Tokens*.

---

## Parte 4 — Construir el workflow en n8n

El workflow final tiene **4 nodos** en este orden:

```
Webhook → Respond to Webhook → Create a record (Airtable) → Add a reaction (Slack)
```

### Nodo 1 — Webhook

1. Agregar nodo **Webhook**.
2. **HTTP Method:** POST.
3. **Respond:** cambiar de "Immediately" a **"Using 'Respond to Webhook' Node"**.

> Esto es clave: Slack envía un parámetro `challenge` al verificar la URL, y necesita una respuesta controlada.

### Nodo 2 — Respond to Webhook

1. Agregar nodo **Respond to Webhook**.
2. **Respond With:** "First Incoming Item".

### Nodo 3 — Create a record (Airtable)

1. Agregar nodo **Airtable → Create a record**.
2. **Credential:** Connect to Airtable (autorizar vía OAuth y seleccionar la base).
3. **Base:** `Slack Messages`.
4. **Table:** `Table 1`.
5. **Mapping Column Mode:** Map Each Column Manually.
6. Mapear los campos con el payload de Slack:

| Campo Airtable | Dato de Slack |
|----------------|---------------|
| Author | `user_name` (o `user`) |
| Message | `text` |
| Timestamp | `ts` |
| Channel | `channel` |

> 💡 **Error común:** si los campos no aparecen, usar la flecha "ir a datos" para forzar la actualización, o reseleccionar Base y Table.

### Nodo 4 — Add a reaction (Slack)

1. Agregar nodo **Slack → Add a reaction**.
2. **Credential:** Slack account.
3. **Channel:** modo **"By ID"** → ingresar el Channel ID (ej. `C0BABMRGAEA`).
4. **Message Timestamp:** el `ts` del mensaje.
5. **Emoji Code:** `thumbsup` (no usar `+1`, debe ser el nombre del emoji).

> Para obtener el Channel ID: clic en el nombre del canal en Slack → ver "About" → el ID empieza con `C`.

---

## Parte 5 — Conectar Slack con n8n (Event Subscriptions)

1. **Publicar/activar** el workflow en n8n (para que la *Production URL* responda).
2. En n8n, copiar la **Production URL** del nodo Webhook.
3. En Slack → **Event Subscriptions** → activar → pegar la URL en **Request URL**.
4. Slack verificará la URL con un *challenge*.
5. En **Subscribe to bot events**, agregar: `message.channels`.
6. Guardar y **reinstalar la app** en el workspace.

---

## Errores encontrados y soluciones

| Error | Causa | Solución |
|-------|-------|----------|
| "access_denied" en OAuth | Sesión/permisos | Usar ventana de incógnito; volver a iniciar sesión |
| "Could not load list" (canales) | Faltan scopes | Agregar scopes y reinstalar la app |
| "Your URL didn't respond with challenge" | Webhook en modo "Immediately" | Cambiar a "Respond to Webhook Node" |
| Campos de Airtable no aparecen | Sincronización | Forzar refresco / reseleccionar Base y Table |
| "Emoji code is required" / "Could not get parameter" | Código de emoji inválido | Usar `thumbsup`, no `+1` |
| Reacción no funciona con mock data | Timestamp ficticio | Requiere un mensaje real (ver Limitaciones en README) |

---

## Limitación de entorno (importante)

Con *mock data*, los nodos Webhook → Airtable funcionan perfectamente, pero la reacción de Slack no puede ejecutarse porque el *timestamp* de prueba no corresponde a un mensaje real. En producción, con el workflow publicado y un mensaje real en el canal, la reacción funciona sin cambios adicionales.
