# Guía de Configuración — Proyecto 5: Typeform → Asana + Slack + Sheets

Esta guía documenta cómo construir un flujo fan-out: un formulario que dispara tres acciones en paralelo.

---

## Requisitos previos

- Cuenta en [n8n.cloud](https://n8n.cloud)
- Cuenta en [Typeform](https://typeform.com) (plan gratuito)
- Cuenta de Google (Sheets)
- Workspace de Slack con app configurada (del Proyecto 2)
- Cuenta en [Asana](https://asana.com) (plan gratuito)
- Tiempo estimado: 3–4 horas

---

## Parte 1 — Formulario en Typeform

1. Crear un formulario nuevo: `Solicitud de Servicio`.
2. Agregar 4 preguntas:
   - ¿Cuál es tu nombre? (Short Text)
   - ¿Cuál es tu correo electrónico? (Email)
   - Tipo de servicio (Multiple Choice: Consultoría, Automatización, Soporte)
   - Descripción (Long Text)
3. Publicar el formulario.

### Token de Typeform

1. Ir a **Settings → Personal tokens** (admin.typeform.com).
2. **Generate a new token** → elegir **Custom scopes** (mínimo privilegio).
3. Marcar: Accounts Read, Forms Read, Responses Read, Webhooks Read, Webhooks Write, Workspaces Read.
4. Generar y copiar el token.

> 💡 Buenas prácticas: usar "Custom scopes" en lugar de "All scopes". Solo se otorgan los permisos necesarios.

---

## Parte 2 — Typeform Trigger en n8n

1. Crear workflow: `Typeform Multi-destino`.
2. **Add first step** → `Typeform` → **"On form submission"**.
3. **Credential:** pegar el token.
4. **Form:** seleccionar el formulario.
5. Activar **Simplify Answers** y **Only Answers** (datos más limpios).
6. **Test this trigger** → llenar y enviar el formulario → confirmar que llegan los datos.

> El Typeform Trigger crea el webhook automáticamente (por eso se necesitan los scopes de webhooks).

---

## Parte 3 — Rama 1: Google Sheets

1. Crear hoja `Solicitudes de Servicio` con encabezados: Fecha, Nombre, Email, Servicio, Descripción.
2. Desde el **Typeform Trigger**, agregar **Google Sheets → Append Row**.
3. Seleccionar documento y hoja.
4. Mapear:

| Columna | Expresión |
|---------|-----------|
| Fecha | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| Nombre | `{{ $json["¿Cuál es tu nombre?"] }}` |
| Email | `{{ $json["¿Cuál es tu correo electrónico?"] }}` |
| Servicio | `{{ $json["Tipo de servicio"] }}` |
| Descripción | `{{ $json["Descripción"] }}` |

5. Execute step → verificar fila nueva en la hoja.

---

## Parte 4 — Rama 2: Slack

⚠️ El nodo debe salir del **Typeform Trigger**, no de Google Sheets (es una rama paralela).

1. Desde el trigger, agregar **Slack → Send a message**.
2. **Credential:** la de Slack (del Proyecto 2).
3. **Send Message To:** Channel.
4. **Channel:** modo **"By ID"** → pegar el Channel ID (ej. `C0BABMRGAEA`).
5. **Message Text:**
```
🔔 Nueva solicitud de servicio

👤 Nombre: {{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }}
📧 Email: {{ $('Typeform Trigger').item.json["¿Cuál es tu correo electrónico?"] }}
🛠️ Servicio: {{ $('Typeform Trigger').item.json["Tipo de servicio"] }}
📝 Descripción: {{ $('Typeform Trigger').item.json["Descripción"] }}
```
6. Quitar la firma: Options → desactivar "Include link to workflow".
7. Execute step → verificar mensaje en el canal.

---

## Parte 5 — Rama 3: Asana

1. En Asana, crear un proyecto: `Solicitudes de Servicio` (vista List). Borrar las tareas de ejemplo.
2. En n8n, desde el **Typeform Trigger**, agregar **Asana → Create a Task**.
3. **Credential:** conectar vía **OAuth2** (un clic para autorizar).
4. **Workspace:** seleccionar el workspace.
5. **Name:**
```
Nueva solicitud: {{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }} - {{ $('Typeform Trigger').item.json["Tipo de servicio"] }}
```
6. **Notes** (en Additional Fields o campo Notes):
```
📧 Email: {{ $('Typeform Trigger').item.json["¿Cuál es tu correo electrónico?"] }}
📝 Descripción: {{ $('Typeform Trigger').item.json["Descripción"] }}
```
7. **Project Names or IDs:** seleccionar `Solicitudes de Servicio`.
8. Execute step → verificar la tarea creada en Asana.

---

## Parte 6 — Prueba final y publicación

1. **Publicar/activar** el workflow.
2. Llenar y enviar el formulario con datos nuevos.
3. Verificar que un solo envío dispare las 3 ramas:
   - Fila nueva en Google Sheets
   - Mensaje en Slack
   - Tarea nueva en Asana

---

## Errores encontrados y soluciones

| Error / Situación | Causa | Solución |
|-------------------|-------|----------|
| `channel_not_found` en Slack | Channel ID incorrecto, o estaba en modo "From list" | Usar "By ID" con el Channel ID correcto |
| Mensaje de Slack no llega | El bot fue removido del canal | Reagregar el bot: `/invite @n8n-automation` |
| "joined #consultas" en vez del mensaje | El bot se unió al canal al ejecutar | Volver a ejecutar; ya unido, envía el mensaje real |
| Campos no se mapean | Nombres con acentos/espacios | Usar sintaxis `$json["¿Cuál es tu nombre?"]` |
| "Execute previous nodes to view input data" | El nodo no tiene datos del trigger | Ejecutar desde el trigger o usar "Execute workflow" |
| Aparece la firma de n8n | Atribución activada | Desactivar "Include link to workflow" |

---

## Posibles mejoras futuras

- **Asignar la tarea de Asana** a un responsable según el tipo de servicio.
- **Email de confirmación** al cliente (cuarta rama).
- **Filtro/Switch** para enrutar según el tipo de servicio (ej. Soporte va a un canal distinto).
- **Fecha de vencimiento** automática en la tarea de Asana (ej. +3 días).
