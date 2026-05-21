# ADN Supabase + Vercel

Sistema ADN con el HTML original como base visual, login por usuario/contraseña, roles, dashboards, administración de usuarios, captura de registros y carga inicial desde Excel.

## Variables de entorno en Vercel

Agrega estas variables en Vercel Project Settings > Environment Variables:

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`
- `USER_EMAIL_DOMAIN` con valor sugerido `adn.local`
- `BOOTSTRAP_TOKEN` con un secreto largo para crear el primer Promotor

El `SUPABASE_SERVICE_ROLE_KEY` solo se usa en funciones serverless. No se expone en el frontend.

## Comandos

```bash
npm install
npm run dev
```

Para desplegar:

```bash
vercel deploy
```

## Primer Promotor

Antes de importar usuarios, crea un Promotor inicial con el token de arranque:

```bash
curl -X POST https://TU-DOMINIO.vercel.app/api/bootstrap-promotor \
  -H "Content-Type: application/json" \
  -d "{\"token\":\"TU_BOOTSTRAP_TOKEN\",\"username\":\"promotor\",\"name\":\"Promotor Principal\",\"password\":\"CAMBIA_ESTA_CONTRASENA\"}"
```

Después inicia sesión en la app con:

- Usuario: `promotor`
- Contraseña: la que enviaste en el comando

## Importar BD.xlsx

Entra como Promotor, abre `Importar`, selecciona el archivo `BD.xlsx` y captura una contraseña temporal para los Desarrolladores de `Hoja2`, porque el archivo proporcionado no trae contraseña para ellos. Los Empresarios toman su contraseña desde la columna `Contraseña`.

El importador:

- Crea primero los Desarrolladores de `Hoja2`.
- Crea los Empresarios de `Hoja1`.
- Asocia cada Empresario al Desarrollador indicado en la columna `Desarrollador`.
- Omite usuarios duplicados y reporta errores por fila.

## Seguridad

La autenticación usa Supabase Auth. La tabla `public.users` mantiene el perfil operativo y deja `password_hash` en `null` porque el hash real lo administra Supabase Auth. Las tablas públicas tienen RLS activo y las operaciones administrativas usan funciones Vercel con `SUPABASE_SERVICE_ROLE_KEY`.

## Estructura

- `index.html`: HTML final basado en el archivo original.
- `css/adn-system.css`: estilos añadidos con la misma identidad visual.
- `js/adn-system.js`: login, roles, dashboards, registros, usuarios e importación.
- `api/*.js`: configuración pública, administración segura, importador y bootstrap.
- `supabase/schema.sql`: esquema, tablas, triggers, roles, permisos y políticas RLS.
