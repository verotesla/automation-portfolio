# Caso de Estudio — Captación de Solicitudes con Fan-out (Typeform → Asana + Slack + Sheets)

## El cliente (ejemplo ilustrativo)

**Empresa:** PixelForge Studio
**Sector:** Agencia de diseño y desarrollo web
**Tamaño:** 6 personas
**Contacto:** Daniel Ortega, director

## El problema

PixelForge capta clientes a través de un formulario en su sitio web. El proceso posterior era manual y se les escapaban oportunidades:

- Las solicitudes llegaban por correo y alguien tenía que copiarlas a una hoja de cálculo.
- El equipo no se enteraba a tiempo de que había llegado una nueva solicitud.
- No se creaba tarea de seguimiento, así que algunas solicitudes se quedaban sin respuesta.
- No había un único lugar donde ver el estado de cada prospecto.

El resultado: prospectos perdidos por demoras y falta de seguimiento, además de tiempo desperdiciado en tareas de copiar y pegar.

## La solución propuesta

Un flujo automatizado que, con cada envío del formulario, dispara **tres acciones a la vez** (patrón fan-out):

1. **Registra** la solicitud en una hoja de Google Sheets (respaldo consultable).
2. **Notifica** al equipo en un canal de Slack al instante.
3. **Crea una tarea** en Asana para asegurar el seguimiento.

Así, ninguna solicitud se pierde: queda archivada, el equipo se entera de inmediato y siempre hay una tarea asignada para darle seguimiento.

## Implementación

Se construyó el workflow en n8n con un disparador de Typeform que se ramifica en tres caminos independientes. Cada rama toma los datos directamente del formulario y los envía a su destino: Google Sheets (registro), Slack (aviso) y Asana (tarea). Las tres se ejecutan en paralelo a partir de un solo evento.

## Resultados esperados

| Métrica | Antes | Después |
|---------|-------|---------|
| Registro de solicitudes | Manual (copiar/pegar) | Automático |
| Tiempo en enterarse de una solicitud | Horas | Instantáneo (Slack) |
| Solicitudes con seguimiento asignado | Inconsistente | 100% (tarea en Asana) |
| Solicitudes perdidas | Frecuentes | Cero |

## Valor del proyecto

Una automatización de captación tipo fan-out se cotiza entre **$400 y $700 USD** en plataformas freelance, según el número de destinos y reglas de negocio. Su atractivo es que automatiza el proceso completo de captación y asignación, conectando varias herramientas que el negocio ya usa. El retorno es directo: cada prospecto recuperado vale mucho más que el costo de la automatización.

## Aprendizaje técnico clave

Este proyecto consolidó el patrón **fan-out**: un único disparador que se ramifica en varias acciones paralelas e independientes. La clave técnica fue que todas las ramas referencian los datos del mismo origen con `$('Typeform Trigger').item.json[...]`, en lugar de encadenarse una tras otra. También reforzó el diagnóstico de errores de integración (Channel ID de Slack, permisos del bot) y el manejo de nombres de campo con caracteres especiales.

---

*Nota: Este caso de estudio es ilustrativo, con fines de portafolio. Los datos del cliente son ficticios.*
