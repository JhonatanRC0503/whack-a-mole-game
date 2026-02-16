# 🚀 Desafío 05: GitHub Copilot en el Flujo de Desarrollo

## 📋 Objetivo
Integrar GitHub Copilot en todo el ciclo de vida del desarrollo, no solo en la escritura de código.

---

## ✅ Tareas Completadas

### 1. 🔧 Pipeline CI/CD con GitHub Actions

Hemos creado un pipeline completo que incluye:

#### **Archivo creado:** `.github/workflows/ci.yml`

**Características del Pipeline:**

- **🔍 Validación de Código:**
  - Validación de HTML con `htmlhint`
  - Linting de JavaScript con `jshint`
  - Se ejecuta en cada push y pull request

- **🚀 Despliegue Automático:**
  - Despliega automáticamente a GitHub Pages
  - Solo se ejecuta en el branch `main`
  - Genera URL pública del juego

- **⚙️ Configuración:**
  - Node.js 20
  - Ubuntu latest
  - Concurrencia controlada

---

## 📁 Archivos de Configuración Creados

### 1. `.htmlhintrc` - Reglas de validación HTML
```json
{
  "doctype-html5": true,
  "title-require": true,
  "tag-pair": true,
  "id-unique": true
}
```

### 2. `.jshintrc` - Reglas de linting JavaScript
```json
{
  "esversion": 6,
  "browser": true,
  "maxcomplexity": 10,
  "maxstatements": 50
}
```

---

## 🎯 Cómo Funciona el Pipeline

### **Workflow Visual:**

```
┌─────────────┐
│   git push  │
└──────┬──────┘
       │
       ▼
┌─────────────────────────┐
│  Job 1: VALIDATE        │
│  ✓ Checkout código      │
│  ✓ Setup Node.js        │
│  ✓ Validar HTML         │
│  ✓ Validar JavaScript   │
└──────────┬──────────────┘
           │
           ▼ (solo si es main)
┌─────────────────────────┐
│  Job 2: DEPLOY          │
│  ✓ Setup GitHub Pages   │
│  ✓ Upload artifact      │
│  ✓ Deploy to Pages      │
└──────────┬──────────────┘
           │
           ▼
     🎮 ¡JUEGO EN VIVO!
```

---

## 🚀 Activar GitHub Pages

Para que el despliegue automático funcione:

1. **Ve a tu repositorio en GitHub**
2. **Settings → Pages**
3. **Source:** Selecciona `GitHub Actions`
4. **Guarda los cambios**

---

## 💡 Uso de Copilot para Commit Messages

### **Ejemplo 1: Commit del Pipeline**

**Prompt a Copilot:**
```
Necesito un commit message para agregar un pipeline de CI/CD con GitHub Actions 
que valida HTML/JS y despliega a GitHub Pages
```

**Respuesta de Copilot:**
```bash
git commit -m "ci: add GitHub Actions pipeline for validation and deployment

- Add CI workflow with HTML and JavaScript validation
- Configure automatic deployment to GitHub Pages
- Add htmlhint and jshint configuration files
- Setup validation on push and pull requests to main branch"
```

### **Ejemplo 2: Commit de Features**

**Prompt a Copilot:**
```
Dame un commit message para agregar sistema de dificultad, 
sonidos y botón de reinicio al juego
```

**Respuesta de Copilot:**
```bash
git commit -m "feat: add difficulty levels, sound effects, and restart button

- Implement three difficulty levels (easy/medium/hard)
- Add Web Audio API sound effects on mole hit
- Create restart button with full game reset
- Add bounce animation for moles
- Update UI with difficulty selector buttons"
```

---

## 🧪 Probar el Pipeline Localmente

### **Validar HTML:**
```bash
npm install -g htmlhint
htmlhint index.html --config .htmlhintrc
```

### **Validar JavaScript:**
```bash
npm install -g jshint
jshint --extract=auto index.html --config .jshintrc
```

---

## 📊 Verificar el Pipeline en GitHub

1. **Ve a tu repositorio**
2. **Click en la pestaña "Actions"**
3. **Verás:**
   - ✅ Workflows ejecutándose
   - 📊 Estado de validación
   - 🚀 Resultados de deployment

### **Estados del Workflow:**

| Estado | Icono | Significado |
|--------|-------|-------------|
| Success | ✅ | Pipeline completado exitosamente |
| In Progress | 🔄 | Ejecutando validaciones |
| Failed | ❌ | Error en validación o deployment |

---

## 🎨 Mejoras Futuras del Pipeline

Puedes agregar con ayuda de Copilot:

### **1. Tests Automatizados**
```yaml
- name: 🧪 Run automated tests
  run: npm test
```

### **2. Lighthouse CI (Performance)**
```yaml
- name: 📊 Run Lighthouse
  uses: treosh/lighthouse-ci-action@v10
```

### **3. Notificaciones**
```yaml
- name: 📧 Send Slack notification
  if: failure()
  uses: 8398a7/action-slack@v3
```

### **4. Análisis de Seguridad**
```yaml
- name: 🔒 Security audit
  run: npm audit
```

---

## 🎯 Convenciones de Commit Messages

Usar **Conventional Commits** con ayuda de Copilot:

| Tipo | Uso | Ejemplo |
|------|-----|---------|
| `feat:` | Nueva funcionalidad | `feat: add high scores system` |
| `fix:` | Corrección de bug | `fix: timer now stops at 0 seconds` |
| `docs:` | Documentación | `docs: update README with setup instructions` |
| `style:` | Formato, estilos CSS | `style: improve mole animation smoothness` |
| `refactor:` | Refactorización | `refactor: extract game logic into classes` |
| `test:` | Tests | `test: add unit tests for score calculation` |
| `ci:` | CI/CD changes | `ci: add GitHub Actions workflow` |
| `chore:` | Mantenimiento | `chore: update dependencies` |

---

## 💬 Prompts Útiles para Copilot

### **Para Workflows:**
```
@workspace crea un workflow de GitHub Actions que ejecute tests y despliegue a Netlify
```

### **Para Commit Messages:**
```
Genera un commit message semántico para estos cambios: [describe cambios]
```

### **Para Debugging:**
```
@workspace ¿por qué está fallando mi workflow de GitHub Actions?
```

### **Para Optimización:**
```
Sugiere mejoras para mi pipeline de CI/CD considerando las mejores prácticas
```

---

## 🔥 Desafío Completado

### ✅ **Criterios de Éxito:**

- [x] ✅ Pipeline de CI funcional configurado
- [x] ✅ Validación automática de código
- [x] ✅ Despliegue automático a GitHub Pages
- [x] ✅ Commit messages descriptivos con ayuda de Copilot
- [x] ✅ Archivos de configuración (.htmlhintrc, .jshintrc)

---

## 📚 Recursos Adicionales

- **GitHub Actions Docs:** https://docs.github.com/actions
- **Conventional Commits:** https://www.conventionalcommits.org/
- **HTMLHint Rules:** https://htmlhint.com/docs/user-guide/list-rules
- **JSHint Options:** https://jshint.com/docs/options/

---

## 🚀 Siguiente Paso

**Haz tu próximo commit usando Copilot:**

1. Abre Copilot Chat
2. Escribe: `"Dame un commit message para agregar el pipeline de CI/CD"`
3. Copia el mensaje sugerido
4. Ejecuta:

```bash
git add .
git commit -m "ci: add GitHub Actions pipeline for validation and deployment

- Add CI workflow with HTML and JavaScript validation
- Configure automatic deployment to GitHub Pages
- Add htmlhint and jshint configuration files
- Setup validation on push and pull requests to main branch"

git push origin main
```

---

## 🎮 URL del Juego (Después del Deploy)

Una vez que el workflow se ejecute, tu juego estará disponible en:

```
https://<tu-usuario>.github.io/whack-a-mole-game/
```

¡Comparte el enlace y que otros disfruten del juego! 🎉

---

**¡Desafío 05 Completado! 🏆**
