# Proyecto 7: Weather API → Google Sheets + Dashboard

![Status](https://img.shields.io/badge/Estado-Completado-success)
![Dificultad](https://img.shields.io/badge/Dificultad-Intermedia-orange)
![Tiempo](https://img.shields.io/badge/Tiempo-3%20horas-blue)
![Herramientas](https://img.shields.io/badge/Herramientas-n8n%20%7C%20OpenWeatherMap%20%7C%20Google%20Sheets-purple)

[English](./README.md) | 🌐 **Español**

## Descripción

Automatización que obtiene datos del clima en vivo para tres ciudades mexicanas (Ciudad de México, Guadalajara, Monterrey) desde una API pública, los registra en un histórico de Google Sheets y los visualiza en un dashboard. Se ejecuta de forma programada, por lo que el histórico del clima se construye automáticamente con el tiempo.

Este proyecto introduce una habilidad clave: **consumir una API pública directamente** con el nodo HTTP Request, sin depender de una integración prearmada. Eso abre la puerta a miles de servicios.

## Casos de uso

- **Planeación operativa:** negocios afectados por el clima (logística, agricultura, eventos) que monitorean condiciones en varias ubicaciones.
- **Monitoreo multi-ubicación:** comparar métricas de varias ciudades o sitios en un solo lugar.
- **Recolección de datos históricos:** construir una serie de tiempo desde cualquier API pública para análisis posterior.
- **Reportes automáticos:** alimentar un dashboard en vivo sin captura manual.

## Stack técnico

| Componente | Herramienta | Función |
|------------|-------------|---------|
| Disparador | Schedule Trigger | Ejecuta la consulta periódicamente |
| Lista de ciudades | Nodo Code | Define las 3 ciudades como items separados |
| Llamada API | HTTP Request | Obtiene el clima de OpenWeatherMap |
| Almacenamiento | Google Sheets | Registra el histórico |
| Visualización | Gráficos de Sheets + dashboard HTML | Muestra los datos |

## Arquitectura del workflow

```
┌──────────┐   ┌────────────┐   ┌──────────────┐   ┌──────────────────┐
│ Schedule │──▶│ Code       │──▶│ HTTP Request │──▶│ Google Sheets    │
│ Trigger  │   │ (3 ciudades)│   │ (Weather API)│   │ (Append Row)     │
└──────────┘   └────────────┘   └──────────────┘   └──────────────────┘
                  3 items          3 resultados        3 filas
```

## Concepto clave: el nodo HTTP Request

A diferencia de los nodos prearmados (Slack, Airtable), el **HTTP Request** llama a cualquier API pública. Es una de las herramientas más poderosas de n8n, porque elimina la dependencia de tener una integración dedicada.

La llamada a OpenWeatherMap usa parámetros de consulta:

| Parámetro | Valor | Propósito |
|-----------|-------|-----------|
| `q` | `{{ $json.ciudad }}` | Ciudad a consultar (una por item) |
| `appid` | API key | Autenticación |
| `units` | `metric` | Devuelve la temperatura en Celsius |

## Procesar varios items

El nodo Code devuelve tres items (uno por ciudad). n8n procesa los tres automáticamente en los siguientes nodos, así que una sola ejecución obtiene y registra las tres ciudades a la vez.

## Datos registrados en Google Sheets

| Columna | Origen |
|---------|--------|
| Fecha | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| Ciudad | `{{ $json.name }}` |
| Temperatura | `{{ $json.main.temp }}` |
| Sensación | `{{ $json.main.feels_like }}` |
| Humedad | `{{ $json.main.humidity }}` |
| Descripción | `{{ $json.weather[0].description }}` |

> Nota: `weather` es una lista, por eso `[0]` toma el primer elemento. `main` es un objeto anidado con los valores de temperatura.

## Visualización

Dos dashboards complementarios:

1. **Gráficos de Google Sheets (en vivo):** barras de temperatura y humedad por ciudad, más una comparativa temperatura-vs-sensación. Se actualizan solos al agregar filas.
2. **Dashboard HTML (pieza visual):** página con tarjetas por ciudad codificadas por color (azul = frío, ámbar = templado, rojo = caluroso). Pieza de muestra para el portafolio.

## Conceptos aprendidos

- **Nodo HTTP Request:** consumir cualquier API pública con parámetros de consulta.
- **Autenticación de API** vía key como parámetro (sin OAuth).
- **Procesar varios items** en una sola ejecución.
- **Acceso a datos anidados y listas** (`main.temp`, `weather[0].description`).
- **Manejo de unidades** (`units=metric` para Celsius).
- Construir **dashboards** en Google Sheets y como página HTML.

## Cómo implementarlo

Ver [`setup-guide.md`](./setup-guide.md) para la guía paso a paso completa (en inglés).

## Valor freelance

Una automatización de recolección de datos multi-ubicación con dashboard tiene un valor estimado de **$300–600 USD** en plataformas freelance. La habilidad del HTTP Request es especialmente valiosa porque se generaliza a cualquier API.

## Capturas

Ver la carpeta [`/assets`](./assets) para las capturas del workflow y los dashboards.

## Contacto

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
