# 🚀 Cómo subir tu proyecto a GitHub

Guía fácil para publicar todo en GitHub.

---

## PASO 1: Descargar estos archivos

1. Descarga los 3 archivos markdown:
   - `README.md`
   - `setup-guide.md`
   - `case-study.md`
   - `.gitignore`

2. En tu computadora, en la carpeta `proyecto-01-forms-sheets-email/`:
   - Pega los 3 archivos en la raíz
   - Renombra el `.gitignore` (quita el prefijo "proyecto-01-")

Debería verse así:
```
proyecto-01-forms-sheets-email/
├── README.md
├── setup-guide.md
├── case-study.md
├── .gitignore
├── assets/
│   ├── screenshot-1.png
│   ├── screenshot-2.png
│   └── ...
└── workflows/
    └── proyecto-01-workflow.json
```

---

## PASO 2: Crear repositorio en GitHub

### 2.1 Crear una cuenta GitHub (si no tienes)
1. Ve a: https://github.com
2. Haz clic en "Sign up"
3. Crea cuenta con tu email

### 2.2 Crear nuevo repositorio
1. En GitHub, haz clic en el **"+"** (arriba derecha)
2. Selecciona: **"New repository"**
3. Rellena:
   ```
   Repository name: automation-portfolio
   Description: 20 proyectos de automatización con n8n y Make
   Public: SÍ (para que sea visible)
   Initialize with README: NO (ya tienes)
   ```
4. Haz clic en **"Create repository"**

---

## PASO 3: Conectar Git en tu computadora

### 3.1 Instalar Git (si no tienes)

**Windows/Mac/Linux**:
1. Ve a: https://git-scm.com/download
2. Descarga e instala (siguiendo pasos por defecto)
3. Abre Terminal/PowerShell

### 3.2 Configurar Git (primera vez)

En Terminal, ejecuta:
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu-email@gmail.com"
```

---

## PASO 4: Subir tu proyecto

### 4.1 Abrir Terminal en la carpeta correcta

**En Windows (PowerShell)**:
1. Abre tu carpeta `automation-portfolio`
2. Click derecho en la carpeta vacía
3. "Open in Terminal" (o similar)

**En Mac/Linux**:
1. Abre Terminal
2. Navega: `cd ~/Documentos/automation-portfolio`

### 4.2 Inicializar repositorio local

En la Terminal, ejecuta:
```bash
git init
git add .
git commit -m "Initial commit: Proyecto 1 completado"
```

### 4.3 Conectar con GitHub

En GitHub, en tu nuevo repositorio, verás instrucciones. Cópia y ejecuta (algo como):

```bash
git branch -M main
git remote add origin https://github.com/TU-USUARIO/automation-portfolio.git
git push -u origin main
```

**Nota**: Reemplaza `TU-USUARIO` con tu usuario de GitHub.

### 4.4 Autenticar (GitHub te pedirá contraseña)

GitHub te pedirá tu contraseña. En **2024+**, usa **Personal Access Token**:

1. En GitHub, arriba derecha → Settings
2. "Developer settings" → "Personal access tokens"
3. "Tokens (classic)" → "Generate new token"
4. Dale permisos en "repo"
5. Copia el token
6. Pega en Terminal cuando pida contraseña

---

## PASO 5: Verificar que subió

1. Ve a tu repositorio en GitHub
2. Actualiza la página (F5)
3. Deberías ver tu carpeta `proyecto-01-forms-sheets-email/` con los archivos

✅ **¡Listo! Tu proyecto está en GitHub**

---

## Próximos pasos

### Agregar más proyectos
Repite lo mismo para Proyectos 2-20 en la misma carpeta:

```
automation-portfolio/
├── proyecto-01-forms-sheets-email/
│   ├── README.md
│   └── ...
├── proyecto-02-slack-airtable/
│   ├── README.md
│   └── ...
├── proyecto-03-...
└── README-PRINCIPAL.md (índice general)
```

### Crear README general
En la raíz de `automation-portfolio/`, crea un `README.md` con índice de todos los proyectos.

---

## 💡 Comandos Git útiles

Después de hacer cambios locales:

```bash
# Ver qué cambió
git status

# Agregar cambios
git add .

# Guardar cambios
git commit -m "Descripción del cambio"

# Subir a GitHub
git push
```

---

## 🔒 Importante: NO subir archivos sensibles

Estos archivos **NUNCA** deben ir a GitHub:
- ❌ Archivos JSON con credenciales
- ❌ Archivos .env con secretos
- ❌ Contraseñas
- ❌ API keys

El archivo `.gitignore` previene esto.

---

## ¿Problemas?

### Error: "fatal: not a git repository"
**Solución**: 
```bash
git init
```

### Error: "Permission denied"
**Solución**: Usa Personal Access Token en lugar de contraseña

### No veo cambios en GitHub
**Solución**: 
1. Asegúrate de hacer `git push`
2. Actualiza GitHub (F5)
3. Verifica rama: debe ser `main`

---

## Próximos pasos
1. ✅ Subir Proyecto 1
2. ➡️ Hacer Proyecto 2
3. ➡️ Repetir para Proyectos 3-20
4. ➡️ Tu portafolio estará impresionante para Workana

**¡Adelante!** 🚀
