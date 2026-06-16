# Proyecto 4: Bot de Telegram → Google Sheets + Respuestas Inteligentes

![Status](https://img.shields.io/badge/Status-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-B%C3%A1sica-green)
![Tiempo](https://img.shields.io/badge/Tiempo-3%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Telegram%20%7C%20Google%20Sheets-purple)

## Descripción

Bot de Telegram que registra automáticamente todos los mensajes recibidos en una hoja de Google Sheets y responde de forma diferenciada según el contenido del mensaje. Combina captura de datos (logging) con un sistema de respuestas automáticas basado en palabras clave.

Cuando un usuario escribe al bot, el mensaje se guarda en Sheets (con fecha legible, usuario, contenido y ID de chat) y el bot responde con un mensaje adecuado según detecte saludos, consultas de precio, horario, soporte o ubicación.

> 📌 **Nota de diseño:** el plan original de este proyecto contemplaba WhatsApp como canal de entrada. Se optó por Telegram porque su API es más accesible (no requiere verificación de negocio ni la WhatsApp Cloud API de Meta), lo que permite enfocarse en los conceptos de automatización. La integración con WhatsApp está contemplada en proyectos posteriores del portafolio (13 y 19).

## Casos de uso reales

- **Atención al cliente automatizada:** responder preguntas frecuentes (precio, horario, ubicación) sin intervención humana.
- **Registro de consultas:** mantener un historial de todos los mensajes recibidos, consultable y ordenado.
- **Recepción de pedidos o reportes:** captar mensajes de un canal de Telegram y archivarlos automáticamente.
- **Bot de primer contacto:** filtrar y orientar a los usuarios antes de derivarlos a un agente humano.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Canal de entrada/salida | Telegram (bot) | Recibe mensajes y envía respuestas |
| Orquestación | n8n (Cloud) | Captura, guarda, clasifica y responde |
| Disparador | Telegram Trigger | Se activa con cada mensaje nuevo |
| Almacenamiento | Google Sheets | Registra cada mensaje |
| Clasificación | Switch (con expresiones) | Decide la respuesta según palabras clave |
| Respuesta | Telegram (Send Message) | Envía el mensaje adecuado |

## Arquitectura del workflow

```
┌────────────────┐   ┌──────────────────┐   ┌────────────┐
│ Telegram       │──▶│ Google Sheets    │──▶│  Switch    │
│ Trigger        │   │ (Append Row)     │   │ (6 salidas)│
│ (nuevo mensaje)│   │ guarda el mensaje│   └─────┬──────┘
└────────────────┘   └──────────────────┘         │
                                                   ├─▶ Telegram: Saludo
                                                   ├─▶ Telegram: Precio
                                                   ├─▶ Telegram: Horario
                                                   ├─▶ Telegram: Ayuda
                                                   ├─▶ Telegram: Ubicación
                                                   └─▶ Telegram: Por defecto (Fallback)
```

## Respuestas del bot

| Si el mensaje contiene... | El bot responde... |
|---------------------------|--------------------|
| hola, buenas, hi, hello | Saludo de bienvenida |
| precio, costo, cuánto, cotización | Información de precios |
| horario, abierto, atención, hora | Horario de atención |
| ayuda, soporte, problema, help | Mensaje de soporte |
| ubicación, dónde, dirección | Ubicación del negocio |
| *(cualquier otra cosa)* | Confirmación de recepción (Fallback) |

## Datos que se registran en Google Sheets

| Columna | Contenido | Origen |
|---------|-----------|--------|
| Fecha | Fecha y hora legible (dd/MM/yyyy HH:mm:ss) | Timestamp de Telegram convertido |
| Usuario | Nombre del remitente | `message.from.first_name` |
| Mensaje | Texto del mensaje | `message.text` |
| ChatID | ID del chat (para responder) | `message.chat.id` |

## Lógica de clasificación

Cada ruta del Switch usa una expresión que evalúa una lista de palabras clave de una sola vez. Importante: el Switch recibe los datos **después** de pasar por Google Sheets, por lo que evalúa el campo `Mensaje` (nombre de la columna), no el `message.text` original de Telegram:

```javascript
{{ ["hola","buenas","hi","hello"]
   .some(p => ($json.Mensaje || "").toLowerCase().includes(p)) }}
```

El Switch está configurado con un **Fallback Output** que captura cualquier mensaje que no coincida con ninguna categoría, garantizando que el bot siempre responda algo.

## Conversión de fecha (timestamp → legible)

Telegram entrega la fecha como un timestamp Unix (un número de segundos). Se convierte a formato legible con la librería Luxon integrada en n8n:

```javascript
{{ DateTime.fromSeconds($json.message.date).toFormat('dd/MM/yyyy HH:mm:ss') }}
```

Esto transforma `1781556829` en `15/06/2026 14:30:45`.

## Conceptos aprendidos

- Creación de un bot de Telegram con **BotFather** y conexión por token.
- Configuración del **Telegram Trigger** (recepción de mensajes).
- Guardado de datos en **Google Sheets** (Append Row) con mapeo de campos.
- **Conversión de fechas** con `DateTime` (Luxon): de timestamp Unix a formato legible.
- Respuesta diferenciada con el nodo **Switch** y un **Fallback Output**.
- Envío de mensajes con Telegram y desactivación de la firma automática de n8n.
- **Lección clave:** los datos cambian de estructura al pasar por cada nodo. El Switch evalúa lo que sale de Google Sheets (`Mensaje`), no el `message.text` original. Siempre revisar el panel INPUT de cada nodo para usar los nombres de campo correctos.

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa.

## Valor para clientes (Workana)

Un bot de Telegram con registro y respuestas automáticas tiene un valor estimado de **$250–450 USD** en plataformas freelance. Es atractivo para pequeños negocios que quieren atención automatizada de primer contacto y un historial ordenado de consultas, sin costos de plataformas más complejas.

> 💡 Evolución: este bot responde por reglas de palabras clave. Una versión con IA conversacional (que entiende lenguaje natural y mantiene contexto) está contemplada en los proyectos 13 y 16 del portafolio.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas del workflow y el bot funcionando.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
