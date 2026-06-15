# Caso de Estudio — Email → Categorización Automática + Etiquetas Gmail

## El cliente (ejemplo ilustrativo)

**Profesional:** Carlos Ramírez
**Actividad:** Consultor freelance de marketing digital
**Situación:** Trabaja solo y gestiona toda su comunicación por correo

## El problema

Carlos recibe entre 40 y 60 correos diarios, mezclados sin ningún orden en su bandeja de entrada: propuestas de clientes potenciales, facturas de proveedores, solicitudes de soporte de clientes actuales, newsletters a las que está suscrito y correos urgentes que requieren respuesta inmediata.

El problema principal no era el volumen, sino la **falta de priorización**:

- Los correos urgentes se perdían entre newsletters y promociones.
- Tardaba en encontrar correos de facturación al final del mes.
- No distinguía rápido entre un cliente nuevo (propuesta) y uno existente (soporte).
- Dedicaba tiempo cada mañana solo a "ordenar" mentalmente su bandeja.

## La solución propuesta

Un workflow que clasifica automáticamente cada correo entrante y le aplica una etiqueta de color según su tipo, apenas llega:

- 🔴 **Urgente** — correos que requieren atención inmediata
- 💼 **Clientes** — propuestas y prospectos
- 🧾 **Facturación** — pagos, facturas y recibos
- 🤝 **Soporte** — solicitudes de ayuda de clientes actuales
- 📰 **Newsletters** — boletines y promociones

## Implementación

Se construyó el workflow en n8n con un disparador de Gmail que detecta cada correo nuevo, un nodo Switch que evalúa el asunto contra listas de palabras clave (en español e inglés), y cinco nodos de Gmail que aplican la etiqueta correspondiente.

La clasificación se diseñó para ser **flexible**: detecta múltiples palabras por categoría, ignora mayúsculas y minúsculas, y permite que un mismo correo reciba varias etiquetas cuando aplica (por ejemplo, una factura urgente).

## Resultados esperados

| Métrica | Antes | Después |
|---------|-------|---------|
| Tiempo diario ordenando correo | ~20 min | ~0 min |
| Correos urgentes detectados al instante | No | Sí (etiqueta 🔴) |
| Bandeja organizada por tipo | No | Sí (automático) |
| Esfuerzo manual de clasificación | Diario | Ninguno |

## Valor del proyecto

Una automatización de organización de correo se cotiza entre **$200 y $400 USD** en plataformas freelance. Es un servicio de alta demanda porque resuelve un dolor universal (la bandeja desordenada) con una inversión baja y un beneficio diario tangible.

## Aprendizaje técnico clave

Este proyecto consolidó el manejo del nodo Gmail (lectura y etiquetado), la lógica condicional con el nodo Switch y, sobre todo, el uso de **expresiones** para evaluar múltiples criterios de forma eficiente, en lugar de crear decenas de condiciones manuales. También reforzó una lección fundamental de n8n: un workflow debe estar **publicado y activo** para reaccionar a eventos en tiempo real.

---

*Nota: Este caso de estudio es ilustrativo, con fines de portafolio. Los datos del cliente son ficticios.*
