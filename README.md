# Traxxo Landing

Landing pública de Traxxo — `www.traxxo.ai`.

Sitio estático servido en Vercel. Captura solicitudes de acceso anticipado en Supabase (`public.leads_notify`).

## Fase actual: waitlist-only

El registro público en `app.traxxo.ai` está cerrado hasta tener ≥4 proyectos. La landing solo permite apuntarse a la lista de espera con email + consentimiento de privacidad. El resto de detalles (objetivo, presupuesto, urgencia…) se piden por correo cuando se contacta al lead.

## Estructura

```
traxxo-landing/
├── index.html              # landing minimal con Matrix y selector de apariencia
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

## Selector de apariencia

El home público incluye un selector visual de apariencia en la barra superior:

- `auto`: usa la preferencia del sistema operativo (`prefers-color-scheme`).
- `claro`: fuerza la versión clara.
- `oscuro`: fuerza la versión oscura.

La selección se guarda en `localStorage` con la clave `traxxo-theme`.

Reglas de protección:

- El selector es solo UI/frontend.
- No modifica backend.
- No modifica Supabase.
- No modifica formulario.
- No modifica rutas.
- No modifica login.
- No añade onboarding, dashboard ni lógica de app.
- La versión clara debe conservar el carácter minimal de Traxxo.
- El Matrix debe adaptarse al tema sin volverse protagonista.

## Touch Field eliminado

El experimento `Traxxo Touch Field` fue retirado.

Motivo:

- El efecto circular competía con la animación Matrix.
- No era congruente con el lenguaje visual de letras/palabras cayendo.
- La UI debía mantenerse más minimalista.

Estado actual:

- No existe `#traxxo-touch-field`.
- No existe `#traxxo-touch-canvas`.
- No existe script independiente de Touch Field.
- La interacción visual vuelve a depender del Matrix principal.

## Contrato UI del home público

El home público de Traxxo debe mantenerse como una landing minimal, centrada y waitlist-only.

Reglas de protección:

- Mantener el wordmark `traxxo` como foco visual principal.
- Mantener el formulario de email + privacidad como única conversión pública.
- Mantener `login →` como acceso externo a `app.traxxo.ai`.
- Mantener footer simple con contacto, términos y privacidad.
- Mantener selector de apariencia como control secundario, no protagonista.
- No añadir secciones comerciales largas al home actual.
- No añadir lógica de onboarding en esta landing.
- No añadir dashboard, demo, pricing ni explicación completa del producto todavía.
- No tocar Supabase salvo cambio explícito de captación de leads.
- No convertir la landing en app.
- Cualquier efecto visual nuevo debe ser decorativo, reversible y no bloquear interacción.

Capas visuales actuales:

- `#fx`: canvas Matrix de fondo.
- `.stage`: contenido principal centrado.
- `.top`: barra superior con marca, selector de apariencia y login.
- `.theme-switch`: selector `auto / claro / oscuro`.
- `.foot`: footer legal/contacto.

Criterio de aceptación:

El usuario debe poder entrar, entender que es acceso anticipado, dejar email, aceptar privacidad, cambiar apariencia y usar login sin interferencias visuales ni técnicas.

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

Modo oscuro:

- Fondo: `#09090b`
- Texto principal: `#fafafa`
- Acento: `#22d3ee`
- Borders/superficies: `#1f1f23`, `#0d0d10`, `#111113`
- Mono dim: `#71717a` / `#a1a1aa`

Modo claro:

- Fondo: `#f7fafc` / `#f8fafc`
- Texto principal: `#071014`
- Acento: `#0284c7`
- Borders/superficies: `#d7e0ea`
- Texto secundario: `#64748b` / `#334155`
