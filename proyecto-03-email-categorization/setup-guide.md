# Guía de Configuración — Proyecto 3: Email → Categorización + Etiquetas Gmail

Esta guía documenta el proceso completo para construir el workflow de clasificación automática de correos, incluyendo los errores comunes y cómo resolverlos.

---

## Requisitos previos

- Cuenta en [n8n.cloud](https://n8n.cloud) (trial de 14 días suficiente)
- Una cuenta de Gmail
- Tiempo estimado: 2–3 horas

---

## Parte 1 — Crear las etiquetas en Gmail

Las etiquetas deben existir antes de automatizar. En Gmail (web):

1. Barra lateral izquierda → bajar hasta **"Más"** → **"Crear etiqueta nueva"**.
2. Crear estas 5 etiquetas:
   - `🔴 Urgente`
   - `💼 Clientes`
   - `🧾 Facturación`
   - `🤝 Soporte`
   - `📰 Newsletters`

> 💡 Anota exactamente cómo las escribiste (con emoji incluido), porque en n8n deberás referirlas igual.

---

## Parte 2 — Crear el workflow en n8n

1. Crear un nuevo workflow.
2. Nombrarlo: `Email Categorization`.

---

## Parte 3 — Nodo 1: Gmail Trigger

1. **Add first step** → buscar `Gmail` → sección **Triggers** → **"On message received"**.
2. **Credential:** crear una nueva (Gmail OAuth2 API) → **"Connect my account"** / "Sign in with Google".
3. Autorizar con la cuenta de Gmail y aceptar los permisos.
4. **Poll Times:** dejar el valor por defecto (cada minuto).

> ⚠️ **Error común de OAuth (`access_denied`):** si al autorizar la ventana se cierra y aparece "Insufficient parameters for OAuth2 callback" o `access_denied`, suele deberse a tener varias cuentas de Google activas en el navegador. **Solución:** usar una **ventana de incógnito**, iniciar sesión solo en n8n y autorizar con una única cuenta. También verificar que las ventanas emergentes estén permitidas.

---

## Parte 4 — Nodo 2: Switch (clasificador)

1. Del Gmail Trigger, agregar nodo **Switch**.
2. **Mode:** Rules.
3. Crear **5 Routing Rules**, una por categoría.

### Configuración de cada regla (versión mejorada)

Para cada regla:

1. **Operador:** cambiar de "contains" a **Boolean → is true**.
2. **Campo de valor:** poner en modo **Expression** y escribir la expresión de la categoría.

**Regla 1 — Urgente:**
```javascript
{{ ["urgente","urgent","asap","error","no funciona"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Regla 2 — Clientes:**
```javascript
{{ ["propuesta","cotización","cotizacion","presupuesto","proposal","quote","contratar"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Regla 3 — Facturación:**
```javascript
{{ ["factura","invoice","pago","payment","recibo","transferencia","cobro"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Regla 4 — Soporte:**
```javascript
{{ ["ayuda","help","soporte","support","duda","consulta","how to"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

**Regla 5 — Newsletters:**
```javascript
{{ ["newsletter","boletín","boletin","promoción","promocion","unsubscribe","oferta","descuento"].some(p => ($json.Subject || "").toLowerCase().includes(p)) }}
```

### Opciones del Switch

Bajar a **Options → Add option** y activar:
- **"Send data to all matching outputs":** ON (permite que un correo reciba varias etiquetas).
- *(Opcional)* **"Fallback Output":** para enrutar correos que no coincidan con ninguna categoría.

> Nota: con las expresiones que usan `.toLowerCase()`, la opción "Ignore Case" ya no es estrictamente necesaria, porque la conversión a minúsculas se hace dentro de la propia expresión.

---

## Parte 5 — Nodos 3 a 7: Aplicar etiquetas (Gmail)

Desde **cada salida** del Switch, agregar un nodo Gmail:

1. Del punto de salida correspondiente, arrastrar → buscar `Gmail` → **acción "Add Label to Message"**.
2. **Resource:** Message.
3. **Operation:** Add Label.
4. **Message ID:** seleccionar el `id` del mensaje que viene del trigger → `{{ $json.id }}`.
   - ⚠️ Usar el `id` del mensaje, NO `historyId`, NO `threadId`, NI los IDs internos de n8n (`workflow.id`, `$execution.id`).
5. **Label:** seleccionar la etiqueta correspondiente de la lista (🔴 Urgente, 💼 Clientes, etc.).

Repetir para las 5 categorías.

---

## Parte 6 — Publicar y probar

1. **Publicar/activar** el workflow (botón "Publish"). **Esto es imprescindible:** un workflow solo reacciona a correos nuevos cuando está activo. Si solo se ejecuta manualmente, procesa datos viejos cargados, no los correos que lleguen después.
2. Enviarse correos de prueba con distintas palabras clave en el asunto ("urgente", "invoice", "ayuda", "oferta"...).
3. Verificar en Gmail que cada correo reciba su etiqueta correspondiente.

---

## Errores encontrados y soluciones

| Error / Situación | Causa | Solución |
|-------------------|-------|----------|
| `access_denied` al conectar Gmail | Varias cuentas de Google activas | Usar ventana de incógnito con una sola cuenta |
| El correo llega pero no se etiqueta | Workflow no estaba publicado/activo | Publicar el workflow; solo así escucha correos nuevos |
| Detecta "urgente" pero no "URGENTE" | Comparación sensible a mayúsculas | Usar `.toLowerCase()` en la expresión |
| No se puede agregar segunda condición (OR) en una regla | Esta versión del Switch permite una condición por regla | Usar expresión con `.some()` para evaluar varias palabras |
| Confusión con el campo Message ID | Existen varios "id" en los datos | Usar el `id` del mensaje (`{{ $json.id }}`), no historyId ni threadId |

---

## Posible mejora futura

- **Clasificación con IA** (Proyecto 10): en lugar de palabras clave, usar un modelo que entienda el contenido y la intención del correo, capturando casos que las reglas no detectan.
- **Búsqueda también en el cuerpo del correo**, no solo en el asunto.
- **Categoría "Sin clasificar"** vía Fallback Output, para revisar correos que ninguna regla capturó.
