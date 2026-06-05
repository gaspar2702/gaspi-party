# 🎲 Carrera contra el Phishing — Multiplayer Edition
## Guía de configuración completa

---

## ¿Qué incluye este proyecto?

| Archivo | Quién lo usa | Descripción |
|---|---|---|
| `host.html` | Profesor (proyector) | Tablero Mario Party, marcador, control del juego |
| `player.html` | Estudiantes (celular) | Elegir personaje, responder preguntas |
| `supabase_schema.sql` | Tú, una vez | Base de datos en la nube (gratis) |

---

## Paso 1 — Crear cuenta en Supabase (gratis)

1. Ve a **https://supabase.com** → **Start for free**
2. Crea un nuevo proyecto (ej: `phishing-game`)
3. Guarda la contraseña de la base de datos

---

## Paso 2 — Ejecutar el esquema SQL

1. En Supabase → **SQL Editor** → **New query**
2. Pega todo el contenido de `supabase_schema.sql`
3. Click **Run** (▶)
4. Deberías ver: `Success. No rows returned`

---

## Paso 3 — Obtener credenciales

En Supabase → **Project Settings** → **API**:

- **Project URL** → algo como `https://abcdefgh.supabase.co`
- **Anon public key** → empieza con `eyJ...`

Guarda estos dos valores.

---

## Paso 4 — Habilitar Realtime

En Supabase → **Database** → **Replication** → habilitar las tablas:
- ✅ `game_sessions`
- ✅ `players`
- ✅ `game_events`

---

## Paso 5 — Desplegar los archivos HTML

### Opción A: GitHub Pages (recomendado, gratis)
1. Crea un repositorio en GitHub
2. Sube `host.html` y `player.html`
3. En Settings → Pages → Branch: main
4. Tu URL será: `https://tuusuario.github.io/turepositorio/`

### Opción B: Netlify (drag & drop)
1. Ve a **https://netlify.com** → **Add new site** → **Deploy manually**
2. Arrastra la carpeta con ambos HTML
3. Obtienes una URL como `https://abc123.netlify.app`

### Opción C: Local (solo para pruebas)
Abre los HTML directamente en el navegador. Funciona si todos están en la misma red.

---

## Paso 6 — Jugar

### El profesor (en el proyector):
1. Abre `host.html`
2. Ingresa la URL y clave de Supabase → **Conectar**
3. El sistema genera un **código de sala de 4 letras** (ej: `KRTX`)
4. Comparte ese código (o la URL directa) con los estudiantes
5. Cuando todos estén conectados → **¡Comenzar Partida!**
6. Control del tablero: lanzar dado, ver respuestas en tiempo real

### Los estudiantes (en el celular):
1. Abren `player.html` (o escanean QR)
2. Primera vez: ingresan URL y clave de Supabase
3. Ingresan el **código de sala**
4. Eligen nombre y personaje
5. ¡A jugar!

---

## Mecánicas del juego

### Tablero (25 casillas estilo Mario Party)
| Casilla | Efecto |
|---|---|
| 🏁 Inicio | Casilla 0, todos empiezan aquí |
| Normal | Solo responde pregunta |
| ⭐ Bonus (c/5) | +1 estrella automática + pregunta |
| 💀 Peligro | Retrocede 3 casillas + pregunta |
| 🏆 Meta | ¡El primero en llegar gana! |

### Preguntas
- Todos los estudiantes responden desde su celular
- ⏱ 30 segundos por pregunta
- ✅ Respuesta correcta: +1 ⭐
- ❌ Respuesta incorrecta: -2 casillas

### Puntaje final
- Por estrellas acumuladas (corrección + bonus)
- En empate: posición en el tablero

---

## Banco de preguntas (20 preguntas incluidas)

| Categoría | Color | Temas |
|---|---|---|
| 📧 General | Rojo | Señales de phishing, credential harvesting |
| 🎯 Tipo de ataque | Celeste | Smishing, Vishing, Whaling, BEC, Spear Phishing |
| 🔗 Inspección URL | Amarillo | Typosquatting, HTTPS, dominios falsos |
| 🛡 Defensa | Verde | 2FA, gestores de contraseñas, reporte de incidentes |
| 🧪 Técnico | Violeta | SPF, DKIM, DMARC |

### Agregar preguntas
En Supabase → **Table Editor** → `question_bank` → **Insert row**

O con SQL:
```sql
INSERT INTO question_bank (category, emoji, question, options, correct_index, explanation, difficulty)
VALUES (
  'general',
  '🎣',
  '¿Tu pregunta aquí?',
  '["Opción A","Opción B","Opción C","Opción D"]',
  1,  -- índice 0-3 de la respuesta correcta
  'Explicación de por qué esta es la respuesta.',
  1   -- dificultad: 1=fácil, 2=medio, 3=difícil
);
```

---

## Solución de problemas

| Problema | Solución |
|---|---|
| "Sala no encontrada" | Verificar que el código de sala sea exacto (4 letras) |
| Los cambios no se ven en tiempo real | Habilitar Realtime en Supabase (Paso 4) |
| Error de CORS | Asegurarse de que los HTML estén en HTTPS, no en `file://` |
| "Could not generate room code" | Limpiar partidas antiguas: `UPDATE game_sessions SET status='ended'` |

---

## Estructura de la base de datos

```
game_sessions ──┬── players (uno por estudiante)
                └── game_events (log de todo lo que ocurre)
                
question_bank (independiente, banco de preguntas)
```

---

## Notas de seguridad

- La **Anon key** es segura de usar en el cliente (está diseñada para eso)
- Las Row Level Security policies permiten leer/escribir a cualquiera con la URL y clave
- Para producción, considera agregar autenticación de profesor con email/password de Supabase Auth

---

*Creado para educación en ciberseguridad 🔐*
