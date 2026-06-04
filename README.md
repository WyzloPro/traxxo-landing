# Traxxo Landing

Landing pública de Traxxo — `www.traxxo.ai`.

Sitio estático servido en Vercel. Captura solicitudes de acceso anticipado en Supabase (`public.leads_notify`).

## Fase actual: waitlist-only

El registro público en `app.traxxo.ai` está cerrado hasta tener ≥4 proyectos. La landing solo permite apuntarse a la lista de espera con email + consentimiento de privacidad. El resto de detalles (objetivo, presupuesto, urgencia…) se piden por correo cuando se contacta al lead.

## Estructura

```
traxxo-landing/
├── index.html              # landing minimal con animación matrix + overlay táctil visual
├── en.html                 # landing en inglés (versión anterior, sin actualizar a v2)
├── index-producto.html     # landing comercial completa archivada (v1)
├── en-product.html         # landing comercial en inglés archivada
├── privacidad.html
├── terminos.html
└── favicon.svg
```

## Branches

- `main` → en producción en `www.traxxo.ai`
- `landing-v1-backup` → snapshot completo de la landing comercial anterior (8 campos, chips de objetivo/presupuesto/urgencia, secciones largas). Recuperable en cualquier momento.

## Backend

La landing escribe directamente en Supabase usando la anon key:

- **Proyecto Supabase**: `ghqttckqvjptxhihojey` (eu-west-1)
- **Tabla**: `public.leads_notify`
- **RLS**: política `insert_leads` exige `client_email` con regex válido + `length < 255` + `privacy_consent = true`
- **Trigger**: `on-new-lead` invoca la edge function `send-lead-notifications` (en `traxxo-admin`) que envía email al cliente y notificación al admin vía Resend

Solo dos campos son obligatorios desde la landing actual: `client_email` y `privacy_consent`. El resto (`client_name`, `domain`, `objective`, `context`, `budget`, `urgency`) se pueden insertar pero no se piden en este formulario.

## Animación de fondo

Lluvia tipo matrix con **tokens SEO/IA** (`SEO`, `CTR`, `H1`, `META`, `GA4`, `GSC`, `LCP`, `LLM`, `RAG`, `CRAWL`, `BACKLINK`, `NOFOLLOW`, `TRAFFIC`, `200`, `301`, `404`…). Los caracteres aceleran y brillan más cerca del cursor. Una safe-zone rectangular centrada protege el wordmark, el formulario y el resto del contenido — los tokens no se pintan dentro de esa zona, con un fade suave en los bordes.

Implementación en canvas 2D, vanilla JS, ~500 líneas. Honra `prefers-reduced-motion`.

## Traxxo Touch Field

Overlay visual táctil añadido sobre el home público como capa independiente. Simula una membrana elástica: al mover el cursor o tocar la pantalla aparece una depresión circular, ondas suaves y pequeñas señales alrededor del punto de contacto.

Reglas de protección del cambio:

- Es solo frontend visual.
- No modifica backend.
- No modifica Supabase.
- No modifica formulario.
- No modifica rutas.
- No modifica login.
- No reemplaza el canvas Matrix `#fx`.
- La capa usa `pointer-events: none` para no bloquear inputs, botones ni links.
- Honra `prefers-reduced-motion`.

Nota de testing: en el entorno donde se preparó el cambio no se pudo capturar screenshot automático porque Playwright/Chromium no pudo instalarse; el registry devolvió `403 Forbidden`. La validación visual debe hacerse en preview/producción desde navegador.

## Contrato UI del home público

El home público de Traxxo debe mantenerse como una landing minimal, centrada y waitlist-only.

Reglas de protección:

- Mantener el wordmark `traxxo` como foco visual principal.
- Mantener el formulario de email + privacidad como única conversión pública.
- Mantener `login →` como acceso externo a `app.traxxo.ai`.
- Mantener footer simple con contacto, términos y privacidad.
- No añadir secciones comerciales largas al home actual.
- No añadir lógica de onboarding en esta landing.
- No añadir dashboard, demo, pricing ni explicación completa del producto todavía.
- No tocar Supabase salvo cambio explícito de captación de leads.
- No convertir la landing en app.
- Cualquier efecto visual nuevo debe ser decorativo, reversible y no bloquear interacción.

Capas visuales actuales:

- `#fx`: canvas Matrix de fondo.
- `.stage`: contenido principal centrado.
- `.top`: barra superior con marca y login.
- `.foot`: footer legal/contacto.
- `#traxxo-touch-field`: overlay táctil visual, sin interacción propia.

Criterio de aceptación:

El usuario debe poder entrar, entender que es acceso anticipado, dejar email, aceptar privacidad y usar login sin interferencias visuales ni técnicas.

## Reversión rápida del Touch Field

Para retirar el Touch Field:

1. Eliminar el bloque HTML `#traxxo-touch-field`.
2. Eliminar el CSS asociado.
3. Eliminar el script independiente del Touch Field.

No tocar `#fx`, formulario, Supabase ni estructura del home.

## Deploy

Push a `main` → Vercel auto-deploya en `www.traxxo.ai`.

Para volver a la landing anterior:

```bash
# desde main
git checkout main
git checkout landing-v1-backup -- index.html
git commit -m "revert: vuelve a landing v1"
git push
```

## Stack

- HTML/CSS/JS vanilla, sin frameworks
- [Inter](https://fonts.google.com/specimen/Inter) (wordmark, UI)
- [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) (tokens, etiquetas, footer)
- Supabase JS client desde CDN (jsDelivr)
- Vercel hosting

## Paleta

- Fondo: `#09090b`
- Texto principal: `#fafafa`
- Acento (cyan): `#22d3ee`
- Borders/superficies: `#1f1f23`, `#0d0d10`, `#111113`
- Mono dim: `#71717a` / `#a1a1aa`
