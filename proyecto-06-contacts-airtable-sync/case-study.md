# Caso de Estudio — Sincronización Bidireccional Contacts ↔ Airtable

## El cliente (ejemplo ilustrativo)

**Empresa:** Inmobiliaria Horizonte
**Sector:** Bienes raíces
**Tamaño:** 8 agentes
**Contacto:** Roberto Salas, gerente comercial

## El problema

Los agentes de Inmobiliaria Horizonte gestionaban sus prospectos en una base de Airtable (compartida, con notas, etapa de venta, propiedad de interés, etc.). Pero a la hora de llamar o escribir por WhatsApp, necesitaban los contactos en sus teléfonos, es decir, en Google Contacts.

El proceso era manual y problemático:

- Cada agente copiaba a mano los prospectos de Airtable a su teléfono.
- Cuando alguien capturaba un contacto en el teléfono, no llegaba a la base compartida.
- Se generaban duplicados y datos desactualizados en ambos lados.
- Se perdía tiempo y se cometían errores de transcripción (teléfonos mal copiados).

Necesitaban que ambos sistemas se mantuvieran en sintonía automáticamente, sin duplicados ni trabajo manual.

## La solución propuesta

Una sincronización bidireccional automática entre Google Contacts y Airtable:

1. **Google → Airtable:** los contactos del teléfono se reflejan en la base compartida, actualizándose sin duplicar.
2. **Airtable → Google:** los prospectos nuevos capturados en la base aparecen automáticamente en Google Contacts, listos para contactar desde el teléfono.

Todo con dos garantías clave: **cero duplicados** (gracias al upsert) y **cero bucles** (gracias a la columna de control).

## Implementación

Se construyeron dos workflows independientes en n8n. El primero trae los contactos de Google y los inserta o actualiza en Airtable usando el email como identificador único (upsert idempotente). El segundo detecta los contactos nuevos creados en Airtable, los crea en Google y los marca como sincronizados para no reprocesarlos, evitando bucles infinitos.

El reto técnico principal fue manejar la estructura anidada e irregular de los datos de Google y diseñar el mecanismo anti-bucle, que es lo que diferencia una sincronización segura de una que se descontrola.

## Resultados esperados

| Métrica | Antes | Después |
|---------|-------|---------|
| Copiado manual de contactos | Diario, por agente | Automático |
| Duplicados | Frecuentes | Cero (upsert) |
| Datos desactualizados | Constante | Sincronizado |
| Errores de transcripción | Comunes | Eliminados |

## Valor del proyecto

Una sincronización bidireccional se cotiza entre **$500 y $900 USD** en plataformas freelance, por su complejidad. No es una integración trivial: requiere manejar duplicados, prevenir bucles y trabajar con estructuras de datos irregulares. Pocos freelancers saben implementarla correctamente, lo que la convierte en un servicio diferenciador y bien pagado.

## Aprendizaje técnico clave

Este fue el proyecto técnicamente más exigente del portafolio hasta este punto. Consolidó tres conceptos de ingeniería de datos: la **idempotencia** (ejecutar sin duplicar, vía upsert), la **prevención de bucles** (con una columna de control de estado) y el **manejo de datos anidados** de APIs reales. También reforzó una práctica profesional esencial: inspeccionar la estructura real de los datos antes de mapearlos, en lugar de asumirla.

---

*Nota: Este caso de estudio es ilustrativo, con fines de portafolio. Los datos del cliente son ficticios.*
