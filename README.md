# Mi plan de entrenamiento

App web (adaptable a móvil) de entrenamiento: plan de **25 semanas, 3 días por semana, un ejercicio por día**. Cada usuario se registra, guarda su perfil privado, marca los días hechos, valora la dificultad (0-10) y descarga **MiPlan.pdf** (una hoja A4 para imprimir y tachar los días). Un administrador ve un resumen por usuario.

## Stack
- Frontend: Vite + JavaScript (sin framework) · `jspdf` para el PDF
- Backend: Supabase (Auth + PostgreSQL con Row Level Security)
- Despliegue: GitHub Pages con GitHub Actions (`.github/workflows/deploy.yml`)

## Estructura
```
data/exercises.json   Catálogo propio (nombre, parte del cuerpo, series, reps/segundos, descripción)
src/plan.js           Genera el plan de 25 semanas a partir del catálogo
src/pdf.js            MiPlan.pdf (A4, una hoja)
src/main.js           Interfaz: registro/login, perfil, plan, panel admin
supabase/schema.sql   Tablas, RLS, trigger de alta y vista del administrador
```

## Puesta en marcha
1. Crea un proyecto en [Supabase](https://supabase.com) y ejecuta `supabase/schema.sql` en el SQL Editor.
2. Copia `.env.example` a `.env` y rellena `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY` (Project Settings → API).
3. `npm install` y `npm run dev`.
4. Regístrate en la app y nómbrate administrador con la consulta comentada al final de `schema.sql`.

## Despliegue
En GitHub: Settings → Pages → Source: *GitHub Actions*, y en Settings → Secrets → Actions añade `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY`. Cada push a `main` publica la web. Añade la URL publicada en Supabase → Authentication → URL Configuration.

## Privacidad (importante)
| Dato | Quién lo ve |
|---|---|
| Altura, peso, género | Solo el propio usuario (RLS en `profiles`) |
| Progreso y dificultad por sesión | Solo el propio usuario |
| Nombre, satisfacción general, nº de ejercicios completados | Usuario y administrador (vista `admin_summary`) |

El administrador **no tiene acceso** a las tablas, solo a la vista `admin_summary`. Los usuarios no pueden cambiar su propio rol (permisos por columna). Nunca subas claves `service_role` al repositorio; la clave `anon` es pública por diseño y la seguridad recae en RLS.

## Contenido y licencias
El catálogo es **contenido propio**. El material de DAREBEE (CC BY-NC-ND, que prohíbe incluirlo en apps) **no debe copiarse** al repositorio: ni imágenes, ni textos, ni planes. Solo se puede enlazar; el campo `darebeeUrl` de cada ejercicio está para eso (ahora `null`). Si se amplía el catálogo con otras fuentes, comprueba su licencia antes.

## Pendiente (roadmap)
- [ ] Contador de series/repeticiones y temporizador por serie y de descanso, con pitido (versión web anterior)
- [ ] Política de privacidad, consentimiento y borrado/exportación de datos (RGPD)
- [ ] PWA (instalable en el móvil) y pruebas
- [ ] Ampliar el catálogo; enlaces a Darebee si responden afirmativamente
- [ ] Elegir licencia del repositorio
