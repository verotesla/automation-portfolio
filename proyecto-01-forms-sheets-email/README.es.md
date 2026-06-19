# Proyecto 1: Google Forms → Google Sheets + Email Automático

![Status](https://img.shields.io/badge/Estado-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-Básica-green)
![Tiempo](https://img.shields.io/badge/Tiempo-2%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20Google%20Forms%20%7C%20Sheets%20%7C%20Gmail-purple)

[English](./README.md) | 🌐 **Español**

## Descripción

Automatización que captura respuestas de Google Forms, las guarda en Google Sheets y envía un email de confirmación automático al cliente, todo en menos de un minuto y sin intervención manual.

Es la automatización base de miles de pequeños negocios que necesitan capturar consultas, organizar datos y confirmar recepción automáticamente.

## Casos de uso

- **Consultoría:** capturar consultas de potenciales clientes.
- **Servicios:** tomar solicitudes de reservas/citas.
- **E-commerce:** procesar consultas sobre productos.
- **Educación:** registrar estudiantes interesados.
- **Servicios profesionales:** abogados, contadores, psicólogos.

## Stack técnico

| Herramienta | Función | Costo |
|-------------|---------|-------|
| n8n | Orquestación del workflow | Free trial / plan pago |
| Google Forms | Captura de datos | Gratis |
| Google Sheets | Base de datos | Gratis |
| Gmail | Envío de emails | Gratis |

## Arquitectura del workflow

```
┌─────────────┐   ┌────────────────────────┐   ┌─────────────────┐
│ Google Form │──▶│ Google Sheets Trigger  │──▶│ Gmail Send       │
│ (respuesta) │   │ (n8n detecta fila)     │   │ (confirmación)   │
└─────────────┘   └────────────────────────┘   └─────────────────┘
```

## Métricas

| Métrica | Valor |
|---------|-------|
| Tiempo de respuesta | < 30 segundos |
| Tasa de automatización | 100% |
| Disponibilidad | 24/7 |
| Costo | $0 (free tier) |

## Conceptos aprendidos

- Triggers y webhooks.
- Autenticación OAuth2 y Service Accounts.
- Integración de APIs (Google Forms, Sheets, Gmail).
- Mapeo de datos entre nodos.
- Variables dinámicas en workflows.

## Lecciones aprendidas

**Lo que funcionó:** el Google Sheets Trigger es muy estable, los envíos de Gmail son confiables, y la simplicidad hace el workflow fácil de mantener.

**Lo que fue difícil:** el setup de Google Cloud tiene muchos pasos, y la autorización OAuth2 a veces falla al primer intento.

**Mejoras futuras:** validación de emails en tiempo real, categorización automática de consultas, integración con CRM (Salesforce, HubSpot) y un SMS de confirmación opcional.

## Cómo implementarlo

Ver el archivo [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa (en inglés).

## Valor freelance

Es uno de los proyectos más demandados en plataformas freelance, con una tarifa típica de **$200–400 USD**. Clientes pequeños y medianos lo necesitan para capturar y organizar consultas.

## Capturas de pantalla

Ver carpeta [`/assets`](./assets) para las capturas del formulario, la hoja, el email y el workflow.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
