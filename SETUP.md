# Guía rápida de configuración — MIEDE Personal

## ⚡ De cero a producción en 15 minutos

```
GitHub ──► Vercel ──► App en línea
                         │
                    Supabase (usuarios)
                    EmailJS  (correos)
                    G.Sheets (resultados)  ← opcional
```

---

## Paso 1 — Subir a GitHub (2 min)

```bash
git init
git add .
git commit -m "MIEDE Personal v8"
git remote add origin https://github.com/TU-USUARIO/miede-personal.git
git push -u origin main
```

---

## Paso 2 — Desplegar en Vercel (1 min)

1. vercel.com → **Add New Project**
2. Seleccionar el repo → Framework: **Other**
3. **Deploy** → copiar la URL pública

---

## Paso 3 — Crear la tabla de usuarios en Supabase (5 min)

```
supabase.com → New Project → SQL Editor → pegar SQL del README → Run
```

Copiar:
- Project URL: `https://xxxx.supabase.co`  
- Anon Key: `eyJhbGc...`

---

## Paso 4 — Configurar la app (2 min)

1. Abrir la URL de Vercel
2. Login → **"⚙ Configurar base de datos"**
3. Pestaña **Usuarios** → pegar URL + Anon Key → Guardar
4. Recargar → aparece **formulario de primer administrador**
5. Crear cuenta de admin → ¡listo!

---

## Paso 5 — Crear usuarios (1 min por usuario)

**Opción A — Registro libre:** el usuario va a la app, pestaña "Crear cuenta"

**Opción B — Admin crea usuario:** Panel 👥 Usuarios → "+ Nuevo" → llenar datos → "Crear usuario"

---

## Variables que necesitarás tener a mano

| Variable | Dónde conseguirla |
|---|---|
| Supabase URL | Project Settings → API → Project URL |
| Supabase Anon Key | Project Settings → API → anon public |
| EmailJS Public Key | emailjs.com → Account → General |
| EmailJS Service ID | emailjs.com → Email Services |
| EmailJS Template ID | emailjs.com → Email Templates |
| Google Apps Script URL | Sheets → Extensiones → Apps Script → Implementar |

---

## ¿Problemas frecuentes?

**"La tabla miede_users no existe"**  
→ Ejecuta el SQL del README en Supabase SQL Editor

**"Error al guardar en Google Sheets"**  
→ Verifica que el despliegue sea "Cualquier persona" (no solo usuarios de Google)

**"El radar chart no aparece"**  
→ Completa todas las 32 preguntas antes de enviar

**"No llega el correo de bienvenida"**  
→ Verifica EmailJS en Panel Usuarios → EmailJS → "Enviar prueba"
