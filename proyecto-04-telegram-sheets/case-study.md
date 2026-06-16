# Caso de Estudio — Bot de Telegram con Registro y Respuestas Automáticas

## El cliente (ejemplo ilustrativo)

**Negocio:** Estudio de Yoga "Equilibrio"
**Tipo:** Pequeño estudio con 2 instructoras
**Situación:** Reciben consultas por Telegram a todas horas, incluso fuera del horario de atención

## El problema

El estudio promociona su Telegram en redes sociales, y recibe un flujo constante de mensajes con las mismas preguntas: precios de las clases, horarios disponibles, dónde están ubicadas y solicitudes de ayuda para reservar.

Los problemas concretos:

- Las instructoras respondían las mismas preguntas decenas de veces al día.
- Muchos mensajes llegaban de madrugada o durante las clases, y quedaban sin respuesta por horas.
- No tenían un registro de cuántas personas consultaban ni qué preguntaban más.
- Perdían posibles clientes que no recibían respuesta a tiempo.

## La solución propuesta

Un bot de Telegram que:

1. **Responde al instante** las preguntas frecuentes (precio, horario, ubicación), las 24 horas.
2. **Registra cada mensaje** en una hoja de cálculo, con fecha, remitente y contenido.
3. **Da una respuesta de cortesía** a cualquier mensaje que no encaje en las categorías, confirmando que fue recibido.

De este modo, las consultas básicas se resuelven solas y las instructoras solo intervienen cuando hace falta atención personalizada.

## Implementación

Se construyó el bot en n8n con un disparador de Telegram que detecta cada mensaje, un nodo de Google Sheets que lo registra (con la fecha convertida a formato legible), y un nodo Switch que evalúa el contenido contra listas de palabras clave para enviar la respuesta adecuada. Un Fallback garantiza que ningún mensaje quede sin contestar.

## Resultados esperados

| Métrica | Antes | Después |
|---------|-------|---------|
| Respuesta a preguntas frecuentes | Manual, con demora | Instantánea, 24/7 |
| Mensajes sin responder | Frecuentes | Cero (siempre hay respuesta) |
| Registro de consultas | No existía | Hoja de cálculo automática |
| Tiempo de las instructoras en repetir respuestas | Alto | Mínimo |

## Valor del proyecto

Un bot de Telegram con registro y respuestas automáticas se cotiza entre **$250 y $450 USD** en plataformas freelance. Para un negocio pequeño, el beneficio es doble: ahorra tiempo del personal y mejora la experiencia del cliente al responder al instante, sin necesidad de contratar una plataforma de atención costosa.

## Aprendizaje técnico clave

Este proyecto integró tres habilidades: captura de mensajes (Telegram Trigger), almacenamiento estructurado (Google Sheets con conversión de fechas vía Luxon) y lógica de respuesta condicional (Switch con Fallback). La lección más valiosa fue entender que **los datos cambian de estructura al pasar por cada nodo**: el Switch debía evaluar el campo que salía de Google Sheets (`Mensaje`), no el dato original de Telegram. Diagnosticar esto mirando el panel de entrada de cada nodo es una habilidad fundamental en automatización.

---

*Nota: Este caso de estudio es ilustrativo, con fines de portafolio. Los datos del cliente son ficticios.*
