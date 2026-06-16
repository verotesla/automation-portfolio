# Guía de Configuración — Proyecto 4: Bot de Telegram → Sheets + Respuestas

Esta guía documenta el proceso completo para construir un bot de Telegram que registra mensajes en Google Sheets y responde según palabras clave.

---

## Requisitos previos

- Cuenta en [n8n.cloud](https://n8n.cloud) (trial de 14 días suficiente)
- Cuenta de Telegram
- Cuenta de Google (para Sheets)
- Tiempo estimado: 2–3 horas

---

## Parte 1 — Crear el bot de Telegram

1. En Telegram, buscar **BotFather** (bot oficial con verificación azul).
2. Iniciar con `/start` y luego enviar `/newbot`.
3. Asignar:
   - **Nombre** del bot (libre, ej. "Vero Automation Bot").
   - **Username** (debe terminar en `bot`, ej. `vero_automation_bot`).
4. BotFather entrega un **token** (ej. `7123456789:AAH...`).
5. Copiar y guardar el token de forma segura.

> ⚠️ Nunca subir el token a GitHub ni compartirlo públicamente.

---

## Parte 2 — Crear el workflow y el Telegram Trigger

1. En n8n, crear un workflow: `Telegram to Sheets`.
2. **Add first step** → `Telegram` → sección Triggers → **"On message"**.
3. **Credential:** crear nueva → pegar el token de BotFather → guardar.

> 💡 La conexión de Telegram es sencilla: solo el token, sin OAuth ni ventanas emergentes.

### Probar el trigger

1. **Execute step** en el trigger (queda escuchando).
2. En Telegram, buscar el bot, darle **Start** y enviarle un mensaje.
3. El mensaje debe aparecer capturado en n8n.

> ⚠️ Un bot no puede recibir mensajes hasta que el usuario inicie la conversación con **Start**.

---

## Parte 3 — Crear la hoja de Google Sheets

1. Crear una hoja: `Telegram Mensajes`.
2. En la fila 1, crear los encabezados (columnas):

| A | B | C | D |
|---|---|---|---|
| Fecha | Usuario | Mensaje | ChatID |

---

## Parte 4 — Nodo Google Sheets (guardar)

1. Agregar nodo **Google Sheets → Append Row**.
2. **Credential:** la cuenta de Google (reutilizable del Proyecto 1).
3. **Document:** seleccionar `Telegram Mensajes`.
4. **Sheet:** la pestaña (Hoja 1 / Sheet1).
5. **Mapear los campos:**

| Columna | Expresión |
|---------|-----------|
| Fecha | `{{ DateTime.fromSeconds($json.message.date).toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| Usuario | `{{ $json.message.from.first_name }}` |
| Mensaje | `{{ $json.message.text }}` |
| ChatID | `{{ $json.message.chat.id }}` |

> La expresión de Fecha convierte el timestamp Unix de Telegram a formato legible usando Luxon (`DateTime`).

---

## Parte 5 — Nodo Switch (clasificador de respuestas)

1. Agregar **Switch** después de Google Sheets.
2. **Mode:** Rules.
3. Crear 5 Routing Rules. En cada una: operador **Boolean → is true**, campo en modo **Expression**.

> ⚠️ **Error común:** olvidar cambiar el operador a **Boolean / is true**. Si se deja en otro operador, la regla no evalúa correctamente y el mensaje cae por una ruta equivocada o por el fallback.

> ⚠️ **Importante:** como el Switch recibe los datos **después** de Google Sheets, las expresiones evalúan el campo `Mensaje` (nombre de la columna), NO `message.text`.

**Regla 1 — Saludo:**
```javascript
{{ ["hola","buenas","hi","hello"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Regla 2 — Precio:**
```javascript
{{ ["precio","costo","cuánto","cuanto","cotización","cotizacion"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Regla 3 — Horario:**
```javascript
{{ ["horario","abierto","atención","atencion","hora"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Regla 4 — Ayuda:**
```javascript
{{ ["ayuda","soporte","problema","help"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

**Regla 5 — Ubicación:**
```javascript
{{ ["ubicación","ubicacion","dónde","donde","dirección","direccion"].some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

4. **Options → Add option → Fallback Output:** activarlo (crea una salida extra para mensajes sin coincidencia).

> Nota: NO activar "Send data to all matching outputs" aquí, porque se quiere UNA respuesta por mensaje.

---

## Parte 6 — Nodos de respuesta (Telegram Send Message)

Desde **cada salida** del Switch (incluido el Fallback), agregar un nodo **Telegram → Send Message**:

1. **Credential:** el bot.
2. **Chat ID:** `{{ $('Telegram Trigger').item.json.message.chat.id }}`
3. **Text:** el mensaje correspondiente a esa categoría.
4. Desactivar la firma: **Add Field → Append n8n Attribution → OFF**.

| Salida | Categoría | Texto sugerido |
|--------|-----------|----------------|
| 0 | Saludo | ¡Hola! 👋 Gracias por escribir. ¿En qué puedo ayudarte? |
| 1 | Precio | 💰 Con gusto te comparto información de precios. Un asesor te contactará pronto. |
| 2 | Horario | 🕐 Nuestro horario es de Lunes a Viernes, 9:00 a 18:00. |
| 3 | Ayuda | 🛠️ Tu mensaje fue registrado y te atenderemos pronto. |
| 4 | Ubicación | 📍 Estamos ubicados en [dirección]. ¡Te esperamos! |
| Fallback | Por defecto | ✅ ¡Mensaje recibido y guardado! Gracias por escribir. |

> 💡 Truco: copiar (Ctrl+C / Ctrl+V) un nodo de Telegram ya configurado y solo cambiar el texto, para no reconfigurar Chat ID y firma cada vez.

---

## Parte 7 — Publicar y probar

1. **Publicar/activar** el workflow.
2. Escribir distintos mensajes al bot desde Telegram y verificar:
   - Que cada mensaje se guarde en Sheets (con fecha legible).
   - Que el bot responda según la palabra clave.
   - Que un mensaje sin palabra clave reciba la respuesta por defecto.

> ⚠️ **Limitación de Telegram:** no se puede escuchar mensajes de prueba ("Execute") y de producción (publicado) al mismo tiempo. Si aparece ese aviso, despublicar para probar manualmente, o probar directamente en producción escribiendo al bot.

---

## Errores encontrados y soluciones

| Error / Situación | Causa | Solución |
|-------------------|-------|----------|
| El bot no recibe mensajes en la prueba | No se inició conversación con el bot | Dar **Start** al bot primero |
| Aviso "can't listen for test executions..." | Telegram no permite test + producción a la vez | Despublicar para probar, o probar en producción |
| El bot responde siempre lo mismo | Las reglas buscaban `message.text` pero tras Sheets el campo es `Mensaje` | Usar `$json.Mensaje` en las expresiones |
| Una regla no clasifica | Operador no estaba en Boolean / is true | Cambiar el operador a **Boolean → is true** |
| Aparece "This message was sent with n8n" | Firma automática activada | Desactivar **Append n8n Attribution** |
| Fecha ilegible (número largo) | Telegram entrega timestamp Unix | Convertir con `DateTime.fromSeconds(...).toFormat(...)` |

---

## Posibles mejoras futuras

- **Respuestas con IA** (proyectos 13/16): entender lenguaje natural en lugar de palabras clave.
- **Buscar también en mayúsculas/variantes** ya cubierto con `.toLowerCase()`.
- **Menú con botones** de Telegram (inline keyboard) para opciones rápidas.
- **Notificación a un segundo canal** (ej. avisar a un administrador cuando llega cierto tipo de mensaje).
