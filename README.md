# MIEDE Personal — Evaluación de Madurez Digital del Líder

> **Modelo MIEDE 2.0** creado por **Juan Carlos Alzate García** · Transformación ExO · Guayaquil, Ecuador

Plataforma web de evaluación de madurez digital para líderes latinoamericanos. Mide 8 pilares, genera diagnóstico personalizado, roadmap de aceleración y stack de herramientas IA por nivel.

---

## 🚀 Despliegue rápido en Vercel (5 minutos)

### 1. Subir a GitHub

```bash
# Clona o crea el repositorio
git init
git add .
git commit -m "MIEDE Personal v8 - initial deploy"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/miede-personal.git
git push -u origin main
```

### 2. Conectar Vercel

1. Ve a **[vercel.com](https://vercel.com)** → Sign up con tu cuenta de GitHub
2. **Add New Project** → selecciona el repositorio `miede-personal`
3. Framework Preset: **Other** (es un sitio estático puro)
4. Build & Output Settings: dejar todo en blanco
5. Clic en **Deploy**

En 30 segundos tendrás una URL como `miede-personal.vercel.app` ✅

### 3. Dominio personalizado (opcional)

En Vercel → Project Settings → Domains → añade tu dominio.

---

## 🗄️ Configurar Supabase (gestión de usuarios)

Supabase es la base de datos que gestiona los registros de usuario. El plan gratuito es suficiente.

### Paso 1 — Crear proyecto

1. Ve a **[supabase.com](https://supabase.com)** → New Project
2. Elige un nombre, una contraseña segura y la región más cercana (us-east-1 para LatAm)
3. Espera ~2 minutos a que el proyecto inicie

### Paso 2 — Crear tabla de usuarios

Ve a **SQL Editor** en el panel izquierdo y ejecuta:

```sql
-- Tabla de usuarios MIEDE Personal
CREATE TABLE miede_users (
  id            uuid primary key default gen_random_uuid(),
  created_at    timestamptz default now(),
  nombre        text not null,
  email         text unique not null,
  username      text unique not null,
  password_hash text not null,
  activo        boolean default true,
  rol           text default 'user' check (rol in ('admin','user')),
  pais          text,
  telefono      text,
  edad          int,
  cargo         text,
  genero        text,
  ecosistema    text,
  ultimo_acceso timestamptz
);

-- Habilitar Row Level Security
ALTER TABLE miede_users ENABLE ROW LEVEL SECURITY;

-- Permitir operaciones con la clave anónima
CREATE POLICY "miede_all" ON miede_users
  FOR ALL USING (true) WITH CHECK (true);
```

### Paso 3 — Tabla de evaluaciones (para guardar resultados)

```sql
-- Tabla de evaluaciones
CREATE TABLE evaluaciones (
  id           bigint generated always as identity primary key,
  created_at   timestamptz default now(),
  nombre       text, email text, telefono text,
  pais         text, edad int, cargo text,
  genero       text, ecosistema text,
  q1  int, q2  int, q3  int, q4  int,
  q5  int, q6  int, q7  int, q8  int,
  q9  int, q10 int, q11 int, q12 int,
  q13 int, q14 int, q15 int, q16 int,
  q17 int, q18 int, q19 int, q20 int,
  q21 int, q22 int, q23 int, q24 int,
  q25 int, q26 int, q27 int, q28 int,
  q29 int, q30 int, q31 int, q32 int,
  score_total  int, porcentaje int,
  nivel        text, titulo text,
  p1 int, p2 int, p3 int, p4 int,
  p5 int, p6 int, p7 int, p8 int,
  fortaleza    text, brecha text
);

ALTER TABLE evaluaciones ENABLE ROW LEVEL SECURITY;
CREATE POLICY "eval_insert" ON evaluaciones FOR INSERT WITH CHECK (true);
CREATE POLICY "eval_select" ON evaluaciones FOR SELECT USING (true);
```

### Paso 4 — Copiar credenciales

Ve a **Project Settings → API**:
- Copia la **Project URL**: `https://xxxx.supabase.co`
- Copia la **anon public key**: `eyJhbGc...`

### Paso 5 — Configurar en la app

1. Abre la app desplegada
2. En la pantalla de login → clic en **"⚙ Configurar base de datos"**
3. Ve a la pestaña **"Usuarios"** e ingresa URL y Anon Key
4. Clic en **"Probar conexión"** → debería aparecer ✓
5. Clic en **"Guardar"**
6. Recarga la página → aparecerá el formulario de **primera configuración** para crear el administrador

---

## ✉️ Configurar EmailJS (envío de credenciales)

EmailJS permite enviar correos de bienvenida con credenciales sin un servidor backend.
Plan gratuito: **200 emails/mes**.

### Paso 1 — Crear cuenta

1. Ve a **[emailjs.com](https://emailjs.com)** → Sign Up gratuito

### Paso 2 — Conectar tu email

1. **Email Services** → Add New Service
2. Elige **Gmail** u **Outlook**
3. Autoriza el acceso → copia el **Service ID** (ej: `service_abc123`)

### Paso 3 — Crear template

1. **Email Templates** → Create New Template
2. Diseña el correo de bienvenida. Usa estas variables:

```
Asunto: Tu acceso a MIEDE Personal está listo

Hola {{to_name}},

Tu cuenta en MIEDE Personal ha sido creada.

Usuario: {{username}}
Contraseña: {{password}}

Accede en: TU-URL.vercel.app

Saludos,
{{app_name}}
```

3. Guarda y copia el **Template ID** (ej: `template_xyz789`)
4. Ve a **Account → General** y copia tu **Public Key** (ej: `user_AbCdEfGh`)

### Paso 4 — Configurar en la app

1. Abre el **Panel de Administración** (botón 👥 Usuarios en la barra superior)
2. Pestaña **EmailJS** → ingresa Public Key, Service ID, Template ID
3. Clic en **"Enviar prueba"** para verificar
4. Clic en **"Guardar"**

---

## 📊 Configurar Google Sheets (resultados de evaluaciones)

### Paso 1 — Crear la hoja

1. Ve a [sheets.google.com](https://sheets.google.com) → Nueva hoja
2. Nómbrala **"MIEDE Evaluaciones"**

### Paso 2 — Apps Script

1. **Extensiones → Apps Script**
2. Borra el código existente y pega:

```javascript
function doPost(e) {
  try {
    var ss = SpreadsheetApp.getActiveSpreadsheet();
    var sheet = ss.getSheetByName("Evaluaciones") || ss.getActiveSheet();
    var data = JSON.parse(e.postData.contents);
    if (sheet.getLastRow() === 0) {
      sheet.appendRow(data.headers);
      sheet.getRange(1,1,1,data.headers.length)
           .setFontWeight("bold")
           .setBackground("#0e0e12")
           .setFontColor("#ffffff");
    }
    sheet.appendRow(data.row);
    return ContentService
      .createTextOutput(JSON.stringify({status:"ok"}))
      .setMimeType(ContentService.MimeType.JSON);
  } catch(err) {
    return ContentService
      .createTextOutput(JSON.stringify({status:"error",msg:err.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

3. **Implementar → Nueva implementación**
   - Tipo: **Aplicación web**
   - Ejecutar como: **Yo mismo**
   - Acceso: **Cualquier persona**
4. Clic en **Implementar** → **Autorizar**
5. Copia la URL que termina en `/exec`

### Paso 3 — Configurar en la app

1. En la app → **⚙ Guardar datos → Google Sheets**
2. Pega la URL del paso anterior
3. **Probar conexión** → **Guardar**

---

## 🔒 Seguridad

- Las contraseñas se almacenan como hash SHA-256 — nunca en texto plano
- Las sesiones expiran automáticamente a las 8 horas
- Supabase usa Row Level Security (RLS)
- El archivo HTML es estático — no hay servidor que comprometer
- Las claves de API se guardan en `localStorage` del navegador del administrador (no en el código fuente)

---

## 📁 Estructura del proyecto

```
miede-personal/
├── index.html          # App completa (single-page, sin dependencias externas)
├── vercel.json         # Configuración de despliegue Vercel
├── .gitignore
└── README.md
```

---

## 🧩 Tecnologías

| Componente | Tecnología |
|---|---|
| Frontend | HTML5 + CSS3 + JavaScript vanilla |
| Gráficos | Chart.js 4.4.1 (CDN) |
| Email | EmailJS Browser 3 (CDN) |
| Base de datos usuarios | Supabase (PostgreSQL) |
| Resultados evaluaciones | Google Sheets / Airtable / Supabase / CSV |
| Autenticación | SHA-256 (Web Crypto API nativa) + localStorage |
| Despliegue | Vercel (estático) |
| Fuentes | Google Fonts (Playfair Display + Plus Jakarta Sans) |

---

## ✏️ Actualizar el contenido

Para editar preguntas, niveles, herramientas IA o cualquier contenido:

1. Abre `index.html` en un editor de texto
2. Busca el array `DIMS` para las preguntas de evaluación
3. Busca `LEVELS` para los niveles de madurez y roadmaps
4. Busca `DEF_TOOLS` para el directorio de herramientas IA
5. Haz commit y push → Vercel despliega automáticamente en ~30 segundos

---

## 👤 Autor

**Juan Carlos Alzate García**  
Founder · Transformación ExO  
Certified ExO Coach · ExO Mindset Award LatAm  
Guayaquil, Ecuador · 2026

---

*Modelo MIEDE 2.0 — Propiedad intelectual de Transformación ExO. Todos los derechos reservados.*
