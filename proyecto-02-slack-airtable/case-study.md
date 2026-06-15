# Caso de Estudio — Slack → Airtable + Reacción Automática

## El cliente (ejemplo ilustrativo)

**Empresa:** TechSupport Solutions
**Sector:** Soporte técnico para pymes
**Tamaño:** 12 empleados
**Contacto:** Laura Méndez, Coordinadora de Soporte

## El problema

El equipo de soporte de TechSupport Solutions recibía consultas de clientes a través de un canal de Slack. El proceso era completamente manual:

- Un agente leía cada mensaje y lo copiaba a una hoja de cálculo.
- No había forma rápida de saber qué consultas ya habían sido registradas.
- Se perdían mensajes en horas de alto volumen.
- No existía un registro consultable para análisis posteriores.

Laura estimaba que el equipo dedicaba **alrededor de 5 horas semanales** solo a copiar y organizar consultas manualmente.

## La solución propuesta

Un workflow automatizado que:

1. **Detecta** cada mensaje publicado en el canal de Slack de consultas.
2. **Guarda** automáticamente el autor, el mensaje, la fecha y el canal en una base de datos de Airtable.
3. **Reacciona** con un ✅ en el mensaje original para indicar visualmente que ya fue registrado.

## Implementación

Se construyó el workflow en n8n con cuatro pasos: recepción del mensaje (Webhook), confirmación a Slack (Respond to Webhook), almacenamiento (Airtable) y confirmación visual (reacción de Slack).

La integración Slack → Airtable quedó **completamente funcional y probada**: cada mensaje se guarda con sus cuatro campos estructurados (autor, contenido, timestamp, canal), listo para filtrar, ordenar y analizar.

## Resultados esperados

| Métrica | Antes | Después |
|---------|-------|---------|
| Tiempo de registro por consulta | ~2 min (manual) | Instantáneo (automático) |
| Consultas perdidas | Frecuentes | Cero |
| Registro consultable | No | Sí (Airtable) |
| Tiempo semanal en registro manual | ~5 horas | ~0 horas |

## Valor del proyecto

Para un cliente real, una automatización de este tipo se cotiza entre **$250 y $500 USD**, según la complejidad de los campos y las reglas de negocio. El retorno de inversión es claro: recuperar ~5 horas semanales de trabajo manual justifica el costo en pocas semanas.

## Aprendizaje técnico clave

Este proyecto consolidó el manejo de webhooks, autenticación OAuth, scopes de permisos y mapeo de campos entre sistemas: una base que se reutiliza en prácticamente cualquier integración entre aplicaciones.

---

*Nota: Este caso de estudio es ilustrativo, con fines de portafolio. Los datos del cliente son ficticios.*
