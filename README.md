# Traxxo Landing

Landing pública de Traxxo — `www.traxxo.ai`.

Sitio estático servido en Vercel. Captura solicitudes de acceso anticipado en Supabase (`public.leads_notify`).

## Fase actual: waitlist-only

El registro público en `app.traxxo.ai` está cerrado hasta tener ≥4 proyectos. La landing solo permite apuntarse a la lista de espera con email + consentimiento de privacidad. El resto de detalles (objetivo, presupuesto, urgencia…) se piden por correo cuando se contacta al lead.

## Estructura

```
traxxo-landing/
├── index.html              # landing minimal oscura con animación Matrix
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

Lluvia tipo Matrix con **tokens SEO/IA** (`SEO`, `CTR`, `H1`, `META`, `GA4`, `GSC`, `LCP`, `LLM`, `RAG`, `CRAWL`, `BACKLINK`, `NOFOLLOW`, `TRAFFIC`, `200`, `301`, `404`…). Los caracteres aceleran y brillan más cerca del cursor.

Implementación en canvas 2D, vanilla JS. Honra `prefers-reduced-motion`.

## Experimentos UI cerrados

Se probaron y retiraron dos experimentos visuales del home público:

1. `Traxxo Touch Field`.
2. Selector de apariencia `auto / claro / oscuro`.

Motivo de cierre:

- El Touch Field competía con la animación Matrix.
- El selector de apariencia añadía complejidad visual innecesaria.
- La versión clara no mejoró el home público.
- La decisión final es conservar el home oscuro minimalista existente.

Estado actual:

- No existe `#traxxo-touch-field`.
- No existe `#traxxo-touch-canvas`.
- No existe `.theme-switch`.
- No existe selector `auto / claro / oscuro` en el home.
- No existe lógica `localStorage` para tema.
- No existe modo claro en el home público.

## Contrato UI del home público

El home público de Traxxo debe mantenerse como una landing minimal, oscura, centrada y waitlist-only.

Reglas de protección:

- Mantener el wordmark `traxxo` como foco visual principal.
- Mantener el formulario de email + privacidad como única conversión pública.
- Mantener `login →` como acceso externo a `app.traxxo.ai`.
- Mantener footer simple con contacto, términos y privacidad.
- Mantener modo oscuro como única apariencia del home público.
- No añadir Touch Field ni efectos circulares sobre el Matrix.
- No añadir selector de apariencia al home público.
- No añadir secciones comerciales largas al home actual.
- No añadir lógica de onboarding en esta landing.
- No añadir dashboard, demo, pricing ni explicación completa del producto todavía.
- No tocar Supabase salvo cambio explícito de captación de leads.
- No convertir la landing en app.
- Cualquier experimento visual nuevo debe hacerse fuera del home público o en rama/preview específica antes de producción.

Capas visuales actuales:

- `#fx`: canvas Matrix de fondo.
- `.stage`: contenido principal centrado.
- `.top`: barra superior con marca y login.
- `.foot`: footer legal/contacto.

Criterio de aceptación:

El usuario debe poder entrar, entender que es acceso anticipado, dejar email, aceptar privacidad y usar login sin interferencias visuales ni técnicas.

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
- Acento: `#22d3ee`
- Borders/superficies: `#1f1f23`, `#0d0d10`, `#111113`
- Mono dim: `#71717a` / `#a1a1aa`
