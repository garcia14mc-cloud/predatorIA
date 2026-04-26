# PredatorIA

Estudio web para crear imagenes y videos con un super agente IA.

## Abrir en tu computadora

```bash
node server.js
```

Luego abre:

```text
http://localhost:4173
```

Si usas el runtime de Codex:

```powershell
C:\Users\Carlo\.cache\codex-runtimes\codex-primary-runtime\dependencies\node\bin\node.exe server.js
```

## Configurar IA

La clave vive en `.env`:

```env
OPENAI_API_KEY=tu_clave
OPENAI_TEXT_MODEL=gpt-5
OPENAI_VIDEO_MODEL=sora-2
OPENAI_VIDEO_SECONDS=5
PUBLIC_SITE_URL=https://tudominio.com
FREE_SIGNUP_CREDITS=100
IMAGE_CREDIT_COST=1
VIDEO_CREDIT_COST=5
RATE_LIMIT_MAX=12
RATE_LIMIT_WINDOW_MS=60000
STRIPE_SECRET_KEY=sk_test_tu_clave
STRIPE_PRICE_STARTER=price_starter
STRIPE_PRICE_CREATOR=price_creator
STRIPE_PRICE_STUDIO=price_studio
DATA_DIR=./data
```

La app usa:

- `POST /api/agent/plan` para planificar el trabajo creativo.
- `POST /api/generate/image` para imagen.
- `POST /api/generate/video` para video.
- `GET /api/video/:id` para consultar estado de video.
- `GET /api/health` para verificar si esta en modo `openai` o `mock`.

## Usuarios y creditos

- Registro gratis: 100 creditos iniciales.
- Imagen: 1 credito.
- Video de 5 segundos: 5 creditos.
- Imagen + video: 6 creditos.
- Starter: 500 creditos por $3.99.
- Creator: 2,000 creditos por $12.99.
- Studio: 5,000 creditos por $24.99.

Este precio bajo esta pensado para atraer creadores y dejar que prueben la herramienta rapido. Ajusta `IMAGE_CREDIT_COST`, `VIDEO_CREDIT_COST` y `FREE_SIGNUP_CREDITS` cuando quieras cambiar la estrategia.

## Pagos

El endpoint `POST /api/billing/checkout` esta preparado para Stripe.

- Si configuras `STRIPE_SECRET_KEY` y los `STRIPE_PRICE_*`, redirige a Stripe Checkout.
- Si no configuras Stripe, funciona en modo demo y suma creditos para probar el flujo.

Para produccion, usa webhooks de Stripe para acreditar creditos solo despues de pago confirmado.

## Antes de abrirla al publico

Revisa estos puntos:

- Define un presupuesto de uso en tu proveedor IA.
- Mantén `RATE_LIMIT_MAX` bajo al principio para evitar gasto inesperado.
- Prueba con prompts cortos antes de activar video, porque video consume mas.
- Cambia `PUBLIC_SITE_URL` cuando tengas el dominio final.
- Rota la API key si alguna vez fue compartida fuera del servidor.

## Publicarla para usuarios

Esta app necesita servidor Node porque guarda la API key del lado servidor. No la publiques como sitio estatico puro si quieres usar IA.

Opcion recomendada: Render.

1. Sube este proyecto a GitHub.
2. En Render crea un `Web Service`.
3. Conecta el repositorio.
4. Build command: deja vacio o usa `npm install`.
5. Start command: `npm start`.
6. Agrega variables de entorno:
   - `OPENAI_API_KEY`
   - `OPENAI_TEXT_MODEL`
   - `OPENAI_VIDEO_MODEL`
   - `OPENAI_VIDEO_SECONDS`
   - `PUBLIC_SITE_URL`
   - `RATE_LIMIT_MAX`
7. Health check path: `/api/health`.
8. Cuando Render te de una URL, ponla en `PUBLIC_SITE_URL`.

## Ponerla en Google

Cuando ya tenga dominio publico:

1. Abre Google Search Console.
2. Agrega tu dominio o URL.
3. Verifica propiedad.
4. Envia este sitemap:

```text
https://tudominio.com/sitemap.xml
```

La app tambien publica:

```text
https://tudominio.com/robots.txt
```

Google puede tardar dias o semanas en indexar. Enviar el sitemap ayuda, pero no garantiza posicion inmediata.

## Seguridad

- No subas `.env` a GitHub.
- Mantén `OPENAI_API_KEY` solo en variables de entorno del servidor.
- Si una clave se comparte en chat o repositorio, rotala desde el panel del proveedor.
