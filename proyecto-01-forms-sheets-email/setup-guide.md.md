# Setup Guide - Proyecto 1

Guía técnica paso a paso para configurar el workflow.

---

## ✅ Requisitos previos

- [ ] Cuenta Google (Gmail)
- [ ] n8n.cloud account (free)
- [ ] 45 minutos
- [ ] Navegador moderno (Chrome, Firefox, Edge)

---

## 🔧 Paso 1: Google Cloud Setup (10 minutos)

### 1.1 Crear proyecto
1. Ve a: https://console.cloud.google.com
2. Selector de proyecto (arriba izquierda) → "NEW PROJECT"
3. Nombre: `n8n-automations`
4. Crear

### 1.2 Habilitar APIs
Busca y habilita estas 3 APIs:
1. `Google Forms API` → ENABLE
2. `Google Sheets API` → ENABLE
3. `Gmail API` → ENABLE

### 1.3 Crear Service Account
1. Menú → "Credentials"
2. "CREATE CREDENTIALS" → "Service Account"
3. Nombre: `n8n-sa`
4. Create and Continue
5. Role: "Editor"
6. Continue → Done

### 1.4 Descargar JSON
1. Service Accounts → `n8n-sa@...`
2. Pestaña "KEYS"
3. "ADD KEY" → "Create new key" → "JSON"
4. Descargar (guárdalo seguro)

✅ Google Cloud listo

---

## 📝 Paso 2: Google Form (7 minutos)

### 2.1 Crear formulario
1. forms.google.com
2. Crear nuevo formulario
3. Título: `Consulta de Clientes`
4. Descripción: `Por favor, completa este formulario...`

### 2.2 Agregar campos
1. **Nombre** - Respuesta corta (obligatorio)
2. **Email** - Respuesta corta + Validación Email (obligatorio)
3. **Teléfono** - Respuesta corta (obligatorio)
4. **Mensaje** - Párrafo (obligatorio)

### 2.3 Publicar
1. Botón "Publish" (arriba derecha)
2. Copiar link

### 2.4 Conectar Google Sheets
1. Pestaña "Respuestas"
2. Icono Google Sheets (abajo izquierda)
3. Crear Sheet automáticamente

✅ Google Form + Sheets listo

---

## ⚙️ Paso 3: Configurar n8n (20 minutos)

### 3.1 Crear workflow
1. n8n.cloud → "New Workflow"

### 3.2 Agregar Google Sheets Trigger
1. "+" → Buscar "Google Sheets" → "Google Sheets Trigger"
2. Create new credentials → Service Account JSON
3. Pega contenido del archivo JSON descargado
4. Document: "Consulta de Clientes (Respuestas)"
5. Sheet: "Form Responses 1"
6. Trigger On: "Row Added"
7. Haz clic "Fetch Test Event"

### 3.3 Agregar Gmail Send
1. "+" (a la derecha) → Buscar "Gmail" → "Send Email"
2. Create new credentials → Google OAuth2
3. Autorizar Gmail

### 3.4 Configurar Email
- **To**: Haz clic y selecciona "email" del formulario
- **Subject**: `Confirmación de tu consulta`
- **Message**:
  ```
  Hola,
  
  Gracias por contactarnos.
  Hemos recibido tu consulta y responderemos pronto.
  
  Saludos,
  El equipo
  ```

### 3.5 Publicar
1. Botón "Publish" (arriba derecha)
2. Version Name: `v1.0 - Initial setup`
3. Save

✅ Workflow publicado y vivo

---

## ✅ Paso 4: Probar (5 minutos)

1. Abre tu Google Form
2. Completa los datos
3. Verifica:
   - ¿Datos en Google Sheets? ✅
   - ¿Email recibido? ✅

---

## 🐛 Troubleshooting rápido

| Problema | Solución |
|----------|----------|
| "Unable to sign" | Ventana incógnito o Setup manual JSON |
| Email no llega | Revisar Spam, límite 100/día free tier |
| Datos no en Sheets | Verificar permisos, compartir Sheet con email de service account |
| Workflow no se ejecuta | Verificar que esté Publicado (botón debe estar verde) |

---

## 📚 Links útiles

- [n8n Docs](https://docs.n8n.io)
- [Google Forms API](https://developers.google.com/forms)
- [Gmail API](https://developers.google.com/gmail/api)
- [Ver README completo](./README.md)

---

**¿Problemas? Ver [case-study.md](./case-study.md) para ejemplo real**

**Última actualización**: Junio 2024
