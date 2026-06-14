# Guía: Subir el Proyecto 2 a GitHub usando VS Code

VS Code tiene Git integrado, así que es más fácil que usar PowerShell. Sigue estos pasos.

---

## Paso 0 — Preparar la carpeta del proyecto

En tu computadora, dentro de `automation-portfolio`, crea esta estructura:

```
automation-portfolio\
├── proyecto-01-forms-sheets-email\   (ya existe)
└── proyecto-02-slack-airtable\        (NUEVO)
    ├── README.md
    ├── setup-guide.md
    ├── case-study.md
    ├── .gitignore
    ├── assets\        (capturas de pantalla)
    └── workflows\     (JSON exportado de n8n)
```

### Renombrar los archivos descargados

Después de descargar, renómbralos así (quita el prefijo `proyecto-02-`):

| Archivo descargado | Renombrar a |
|--------------------|-------------|
| `proyecto-02-README.md` | `README.md` |
| `proyecto-02-setup-guide.md` | `setup-guide.md` |
| `proyecto-02-case-study.md` | `case-study.md` |

> El `.gitignore` que ya tienes en la raíz del repo sirve para todo; no necesitas uno nuevo por proyecto, pero puedes copiarlo si quieres.

---

## Paso 1 — Abrir la carpeta en VS Code

1. Abre **VS Code**.
2. Menú **File → Open Folder...**
3. Selecciona la carpeta `automation-portfolio` (la raíz, no la del proyecto 2).
4. Si VS Code pregunta si confías en los autores, di **"Yes, I trust the authors"**.

---

## Paso 2 — Ver los cambios en Source Control

1. En la barra lateral izquierda, haz clic en el ícono de **Source Control** (parece tres puntos conectados por líneas, o presiona `Ctrl+Shift+G`).
2. Verás la lista de archivos nuevos del proyecto 2 bajo "Changes".

---

## Paso 3 — Preparar y confirmar (commit)

1. En la parte superior del panel de Source Control hay una caja de texto que dice **"Message"**.
2. Escribe un mensaje descriptivo, por ejemplo:
   ```
   Proyecto 2: Slack a Airtable - documentacion completa
   ```
3. Haz clic en el botón **"Commit"** (o presiona `Ctrl+Enter`).
4. Si te pregunta si quieres "Stage all changes", di **"Yes"** (eso prepara todos los archivos).

---

## Paso 4 — Subir a GitHub (push)

1. Después del commit, el botón cambiará a **"Sync Changes"** o verás **"Push"**.
2. Haz clic en **"Sync Changes"** (o en los tres puntos `...` → **Push**).
3. Si pide autenticación, VS Code abrirá el navegador para que inicies sesión en GitHub. Autoriza.

> 💡 Si ya configuraste el repositorio antes con PowerShell, VS Code reconoce automáticamente el `origin` y sube al mismo repo.

---

## Paso 5 — Verificar

1. Abre tu navegador.
2. Ve a: **https://github.com/verotesla/automation-portfolio**
3. Deberías ver la carpeta `proyecto-02-slack-airtable` con sus archivos.

---

## Antes de subir: exportar el workflow de n8n

Para que el proyecto quede completo, exporta el workflow:

1. En n8n, abre tu workflow `Slack to Airtable`.
2. Menú de los tres puntos (arriba derecha) → **Download**.
3. Se descargará un archivo `.json`.
4. Guárdalo en la carpeta `proyecto-02-slack-airtable\workflows\`.

> ⚠️ Antes de subir el JSON, **revisa que no contenga tokens ni secretos**. n8n normalmente exporta sin credenciales, pero ábrelo con VS Code y verifica que no haya `xoxb-...` ni tokens de Airtable. Si los hay, bórralos antes de subir.

---

## Capturas recomendadas para /assets

Para que el portafolio se vea profesional, agrega capturas de:

- El workflow completo en n8n (los 4 nodos conectados).
- El nodo Airtable ejecutado con éxito (el ✅ verde).
- La tabla de Airtable con los datos guardados.
- Los scopes configurados en la app de Slack.

---

## Resumen de comandos (si prefieres la terminal de VS Code)

Si en algún momento quieres usar la terminal integrada (`Ctrl+ñ` o `Terminal → New Terminal`):

```bash
git add .
git commit -m "Proyecto 2: Slack a Airtable - documentacion completa"
git push
```

¡Listo! Con esto el Proyecto 2 queda documentado y publicado.
