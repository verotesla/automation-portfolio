# Proyecto 2: Slack → Airtable + Reacción Automática

![Status](https://img.shields.io/badge/Status-Funcional%20(con%20limitaci%C3%B3n%20documentada)-yellow)
![Dificultad](https://img.shields.io/badge/Dificultad-Intermedia-orange)
![Tiempo](https://img.shields.io/badge/Tiempo-4%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Slack%20%7C%20Airtable-purple)

## Descripción

Automatización que captura mensajes enviados en un canal de Slack y los almacena automáticamente en una base de datos de Airtable, con una reacción automática de confirmación en el mensaje original.

Este proyecto demuestra la integración entre una herramienta de comunicación (Slack), una herramienta de orquestación de workflows (n8n) y una base de datos colaborativa (Airtable), conectadas mediante webhooks y autenticación OAuth.

## Casos de uso reales

- **Soporte al cliente:** capturar consultas de un canal de Slack en una base de datos para darles seguimiento.
- **Gestión de leads:** registrar solicitudes de un canal de ventas en un CRM ligero.
- **Tickets internos:** centralizar peticiones de equipos en una tabla consultable.
- **Registro de incidencias:** documentar reportes que llegan por Slack en un repositorio estructurado.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Origen de datos | Slack | Canal donde se publican los mensajes |
| Orquestación | n8n (Cloud) | Recibe, procesa y enruta los datos |
| Recepción | Webhook | Punto de entrada de los eventos de Slack |
| Almacenamiento | Airtable | Base de datos donde se guardan los registros |
| Confirmación | Slack Reaction | Reacción automática en el mensaje original |

## Arquitectura del workflow

```
┌──────────┐     ┌─────────────────────┐     ┌─────────────────┐     ┌──────────────┐
│ Webhook  │────▶│ Respond to Webhook  │────▶│ Create a record │────▶│ Add reaction │
│ (Slack)  │     │   (verificación)    │     │   (Airtable)    │     │   (Slack)    │
└──────────┘     └─────────────────────┘     └─────────────────┘     └──────────────┘
   Recibe            Confirma a Slack            Guarda datos          Reacciona ✅
```

## Estado de los componentes

| Componente | Estado | Notas |
|------------|--------|-------|
| Webhook (recepción) | ✅ Funcional | Recibe y procesa los datos correctamente |
| Respond to Webhook | ✅ Funcional | Responde a Slack con el primer item recibido |
| Create a record (Airtable) | ✅ Funcional y probado | Guarda Author, Message, Timestamp y Channel |
| Add reaction (Slack) | ⏳ Pendiente de datos en vivo | Requiere un mensaje real para reaccionar (ver Limitaciones) |

## Conceptos aprendidos

- Configuración de **webhooks** como punto de entrada de un workflow.
- Manejo de **OAuth2** y **scopes** (permisos) en una app de Slack.
- Uso del nodo **Respond to Webhook** para verificación de endpoints.
- **Mapeo de campos** (field mapping) entre el payload de origen y las columnas de destino.
- Creación y configuración de **tokens de acceso personal** en Airtable.
- Diferencia entre **Test URL** y **Production URL** en n8n.
- Estructura del payload de eventos de Slack (`text`, `user`, `ts`, `channel`).

## Limitaciones conocidas

La reacción automática de Slack (último nodo) requiere un **mensaje real** publicado en el canal para funcionar. Durante el desarrollo se utilizaron datos de prueba (*mock data*) para validar el flujo, lo cual permite confirmar que la lógica Webhook → Airtable funciona al 100%, pero impide ejecutar la reacción porque el *timestamp* de prueba no corresponde a ningún mensaje existente en Slack.

Para activar la reacción en producción se requiere:
1. **Publicar el workflow** en n8n (estado activo) para que la *Production URL* responda.
2. **Verificar el Event Subscription** de Slack contra esa URL activa.
3. Enviar un mensaje real al canal, que generará un *timestamp* válido para la reacción.

Esta limitación es propia del entorno de desarrollo y no representa un error del diseño del workflow.

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa.

## Valor para clientes (Workana)

Proyectos de este tipo (integración Slack + base de datos) tienen un valor estimado de **$250–500 USD** en plataformas freelance, dependiendo del número de campos, transformaciones de datos y reglas de negocio requeridas.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas del workflow funcionando.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
