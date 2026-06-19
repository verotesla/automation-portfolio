# Proyecto 3: Email → Categorización Automática + Etiquetas de Gmail

![Status](https://img.shields.io/badge/Estado-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-B%C3%A1sica-green)
![Tiempo](https://img.shields.io/badge/Tiempo-3%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Gmail-purple)

[English](./README.md) | 🌐 **Español**

## Descripción

Automatización que clasifica automáticamente los correos entrantes de Gmail y les aplica etiquetas según su contenido. Cuando llega un correo nuevo, el workflow analiza el asunto, identifica a qué categoría pertenece según palabras clave, y aplica la etiqueta correspondiente en Gmail, todo sin intervención manual.

Este proyecto demuestra el manejo del nodo de Gmail (lectura y etiquetado), lógica de clasificación condicional con el nodo Switch, y el uso de expresiones para evaluar múltiples criterios de forma eficiente.

## Casos de uso

- **Freelancers y consultores:** separar automáticamente correos de clientes, facturación y soporte.
- **Soporte técnico:** identificar y priorizar correos urgentes apenas llegan.
- **Pequeñas empresas:** mantener la bandeja de entrada organizada sin trabajo manual.
- **Profesionales con alto volumen de correo:** reducir el tiempo dedicado a clasificar mensajes.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Origen / destino | Gmail | Recibe los correos y almacena las etiquetas |
| Orquestación | n8n (Cloud) | Detecta, clasifica y etiqueta |
| Disparador | Gmail Trigger | Se activa al recibir un correo nuevo |
| Clasificación | Switch (con expresiones) | Evalúa el asunto contra palabras clave |
| Acción | Gmail (Add Label) | Aplica la etiqueta correspondiente |

## Arquitectura del workflow

```
                              ┌──────────────────┐
                         ┌───▶│ Gmail: 🔴 Urgente │
                         │    └──────────────────┘
                         │    ┌──────────────────┐
                         ├───▶│ Gmail: 💼 Clientes│
┌──────────────┐  ┌──────┴─┐  └──────────────────┘
│ Gmail Trigger│─▶│ Switch │  ┌──────────────────────┐
│ (nuevo email)│  │(5 rutas)├─▶│ Gmail: 🧾 Facturación │
└──────────────┘  └──────┬─┘  └──────────────────────┘
                         │    ┌──────────────────┐
                         ├───▶│ Gmail: 🤝 Soporte │
                         │    └──────────────────┘
                         │    ┌─────────────────────┐
                         └───▶│ Gmail: 📰 Newsletters│
                              └─────────────────────┘
```

## Categorías y palabras clave

| Etiqueta | Palabras clave detectadas |
|----------|---------------------------|
| 🔴 Urgente | urgente, urgent, asap, error, no funciona |
| 💼 Clientes | propuesta, cotización, presupuesto, proposal, quote, contratar |
| 🧾 Facturación | factura, invoice, pago, payment, recibo, transferencia, cobro |
| 🤝 Soporte | ayuda, help, soporte, support, duda, consulta, how to |
| 📰 Newsletters | newsletter, boletín, promoción, unsubscribe, oferta, descuento |

## Lógica de clasificación

En lugar de una sola palabra por categoría, cada ruta del Switch usa una **expresión** que evalúa una lista completa de palabras clave de una sola vez:

```javascript
{{ ["urgente","urgent","asap","error","no funciona"]
   .some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Qué hace esta expresión:**
- Toma la lista de palabras clave de la categoría.
- `.some(...)` devuelve verdadero si **al menos una** palabra coincide.
- `.toLowerCase()` convierte el asunto a minúsculas, de modo que la detección **ignora mayúsculas/minúsculas**.
- `($json.Subject || "")` evita errores si un correo llega sin asunto.
- `.includes(p)` comprueba si el asunto contiene la palabra.

El Switch está configurado con **"Send data to all matching outputs"** activado, lo que permite que un correo reciba **varias etiquetas** si aplica a más de una categoría (por ejemplo, "Factura urgente" → 🧾 Facturación + 🔴 Urgente).

## Conceptos aprendidos

- Configuración del **Gmail Trigger** y autenticación OAuth2 con Google.
- Uso del nodo **Switch** en modo "Rules" para enrutamiento condicional.
- Escritura de **expresiones** en n8n (JavaScript) para evaluar múltiples criterios.
- Manejo de mayúsculas/minúsculas con `.toLowerCase()`.
- Aplicación de etiquetas a mensajes con el nodo **Gmail → Add Label**.
- Diferencia entre **ejecutar manualmente** y **publicar/activar** un workflow.
- Salidas múltiples del Switch ("Send data to all matching outputs").

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa (en inglés).

## Valor freelance

Una automatización de organización de correo de este tipo tiene un valor estimado de **$200–400 USD** en plataformas freelance. Es un servicio muy demandado por profesionales y pymes que reciben alto volumen de correo y necesitan mantener orden sin esfuerzo manual.

> 💡 Evolución: este proyecto clasifica por reglas de palabras clave. Una versión avanzada con clasificación mediante IA (que entiende el contenido y la intención del correo) está planificada como Proyecto 10 del portafolio.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas del workflow funcionando.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
