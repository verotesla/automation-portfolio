# Proyecto 5: Typeform → Asana + Slack + Google Sheets (Fan-out)

![Status](https://img.shields.io/badge/Status-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-Intermedia-orange)
![Tiempo](https://img.shields.io/badge/Tiempo-4%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Typeform%20%7C%20Asana%20%7C%20Slack%20%7C%20Sheets-purple)

## Descripción

Automatización que captura las respuestas de un formulario de Typeform y, con un solo envío, dispara **tres acciones en paralelo**: registra la solicitud en Google Sheets, notifica al equipo en Slack y crea una tarea de seguimiento en Asana.

Este proyecto introduce el patrón **fan-out** (un disparador → múltiples acciones simultáneas), fundamental en automatizaciones reales donde un mismo evento debe propagarse a varios sistemas a la vez.

## El patrón Fan-out

A diferencia de un flujo lineal (un paso tras otro), aquí el disparador se ramifica en tres caminos independientes que se ejecutan a la vez:

```
                          ┌──────────────────────┐
                     ┌───▶│ Google Sheets        │  Registro / respaldo
                     │    │ (Append Row)         │
┌─────────────────┐  │    └──────────────────────┘
│ Typeform Trigger│  │    ┌──────────────────────┐
│ (form enviado)  │──┼───▶│ Slack (Send Message) │  Aviso al equipo
└─────────────────┘  │    └──────────────────────┘
                     │    ┌──────────────────────┐
                     └───▶│ Asana (Create Task)  │  Tarea de seguimiento
                          └──────────────────────┘
```

**Ventaja:** las tres acciones son independientes. Si una fallara, las otras dos igual se ejecutan. Y todas parten del mismo dato de origen (el formulario), referenciado con `$('Typeform Trigger')`.

## Casos de uso reales

- **Captación de leads:** una solicitud web genera registro, aviso al equipo de ventas y tarea de seguimiento, sin trabajo manual.
- **Onboarding de clientes:** un formulario de alta dispara el archivo del dato, la notificación al equipo y la tarea de configuración.
- **Gestión de tickets:** una solicitud de soporte se registra, se avisa y se asigna como tarea, todo a la vez.
- **Inscripciones a eventos:** cada registro se guarda, se notifica y genera una tarea de confirmación.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Formulario | Typeform | Captura la solicitud |
| Orquestación | n8n (Cloud) | Recibe y distribuye a 3 destinos |
| Disparador | Typeform Trigger | Se activa con cada envío del formulario |
| Registro | Google Sheets | Guarda la solicitud (respaldo consultable) |
| Notificación | Slack | Avisa al equipo en un canal |
| Seguimiento | Asana | Crea una tarea en un proyecto |

## Campos del formulario

| Pregunta (Typeform) | Tipo |
|---------------------|------|
| ¿Cuál es tu nombre? | Short Text |
| ¿Cuál es tu correo electrónico? | Email |
| Tipo de servicio | Multiple Choice (Consultoría, Automatización, Soporte) |
| Descripción | Long Text |

## Qué hace cada rama

**Rama 1 — Google Sheets (Append Row):** guarda una fila con Fecha, Nombre, Email, Servicio y Descripción. La fecha se genera con `$now` en formato legible.

**Rama 2 — Slack (Send Message):** publica en el canal un mensaje formateado con los datos de la solicitud:
```
🔔 Nueva solicitud de servicio
👤 Nombre: ...
📧 Email: ...
🛠️ Servicio: ...
📝 Descripción: ...
```

**Rama 3 — Asana (Create Task):** crea una tarea en el proyecto "Solicitudes de Servicio" con título "Nueva solicitud: [Nombre] - [Servicio]" y las notas con email y descripción.

## Detalle técnico: referencias al trigger

Como las tres ramas parten del mismo disparador, todas toman los datos directamente del trigger en lugar del nodo anterior:

```javascript
{{ $('Typeform Trigger').item.json["¿Cuál es tu nombre?"] }}
```

Los nombres de campo usan sintaxis de corchetes y comillas `["..."]` porque las preguntas de Typeform contienen espacios, acentos y signos de interrogación.

## Conceptos aprendidos

- **Patrón fan-out:** un disparador ramificado en múltiples acciones paralelas.
- Conexión de **Typeform** vía Personal Access Token con scopes personalizados (mínimo privilegio).
- Referencias a un nodo específico con `$('Nombre del nodo').item.json[...]`.
- Manejo de nombres de campo con caracteres especiales (corchetes y comillas).
- Integración de **Asana** (Create Task) vía OAuth2, con asignación a un proyecto.
- Reutilización de credenciales entre proyectos (Slack y Google del portafolio).
- Diagnóstico de errores de Slack: `channel_not_found` (Channel ID incorrecto) y bot removido del canal.

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa.

## Valor para clientes (Workana)

Una automatización fan-out que conecta un formulario con CRM/gestión de tareas y notificaciones tiene un valor estimado de **$400–700 USD** en plataformas freelance. Es muy demandada porque automatiza el proceso completo de captación y asignación de trabajo, eliminando pasos manuales propensos a errores.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas del workflow y las 3 ramas funcionando.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
