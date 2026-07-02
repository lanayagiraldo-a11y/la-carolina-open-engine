# La Carolina — lecciones de entrega de campañas HTML/Netlify

Notas condensadas de una sesión creando una landing HTML para campaña Meta Ads de reclutamiento de conductores.

## Entrega por Telegram

- Para HTML, no confiar solo en escribir `MEDIA:` en la respuesta final: puede no aparecer como archivo para la usuaria.
- Enviar activamente con `send_message` y `[[as_document]]` cuando sea descargable.
- Mejor paquete para HTML: ZIP con `index.html` + carpeta `assets/`.
- Entregar respaldo en DOCX/PDF cuando la usuaria necesita leer o descargar sin abrir HTML.
- Si la usuaria dice “no aparece”, corregir primero con un nuevo envío de archivo; no seguir explicando que “ya está”.

## Netlify

- Si un deploy anónimo queda protegido/401, crear o usar un sitio autenticado del equipo de la usuaria y desplegar con `--site <site_id>`.
- Verificar el enlace final con HTTP 200 y verificar también los assets principales del sitio, especialmente logos.

## Logo transparente

- Para landing pages de La Carolina, usar logo PNG transparente en hero/portadas sobre fondo oscuro, rojo o dorado.
- Evitar una caja blanca detrás del logo cuando la usuaria pida integración visual de marca.
- Si se parte de JPG con fondo blanco, crear PNG transparente y guardarlo como activo reutilizable antes de actualizar el HTML.
