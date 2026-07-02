# Actualizar landings/campañas de La Carolina publicadas en Netlify

Lección de sesión: la usuaria pidió agregar una sección nueva a una landing ya publicada (`campana-conductores-la-carolina.netlify.app`). El flujo efectivo fue editar el HTML local, verificar presencia de anchors/contenido y redeployar a Netlify.

## Flujo recomendado

1. **Ubicar el sitio local**
   - Las landings de campaña suelen quedar como carpeta standalone con `index.html` y `assets/`.
   - Para esta campaña el patrón usado fue: `/root/.hermes/document_cache/la-carolina-meta-ads-site/`.

2. **Modificar el HTML existente, no recrearlo desde cero**
   - Mantener branding, navegación, logos y CSS existentes.
   - Insertar nuevas secciones con un `id` claro para enlaces directos: `#pasoapaso`, `#formulario`, etc.
   - Si la sección debe ser accesible desde menú, agregar su enlace en `<nav class="nav">`.

3. **Para secciones paso a paso**
   - Escribir instrucciones operativas para una persona no experta.
   - Orden recomendado: preparación → configuración → publicación → revisión → optimización.
   - Incluir checklist antes de publicar.
   - Evitar tecnicismos innecesarios y explicar qué NO escoger cuando sea importante (ej. no usar Interacción si el objetivo es leads calificados).

4. **Verificación antes de deploy**
   - Confirmar que el `id` nuevo existe en el HTML.
   - Confirmar que el enlace del menú apunta al `id` correcto.
   - Confirmar que el archivo sigue cargando logos desde `assets/`.

5. **Deploy a Netlify**
   - Usar el mismo `site_id` si ya existe para no cambiar el enlace público.
   - Comando patrón:
     ```bash
     npx --yes netlify-cli deploy --dir /ruta/site --prod --site <site_id> --message "Descripción breve del cambio" --json
     ```
   - Verificar con una petición HTTP que el HTML publicado contiene el texto o anchor agregado.

6. **Responder a la usuaria**
   - Dar el link público principal y el link directo al anchor nuevo.
   - Resumir qué se agregó en bullets cortos.

## Pitfalls

- No prometer “ya quedó” sin verificar que el contenido nuevo aparece online.
- No cambiar el dominio si la usuaria pidió “agregar algo a este link”. Debe mantenerse el mismo sitio Netlify.
- Si es una sección educativa para no expertos, priorizar instrucciones concretas sobre teoría de marketing.
