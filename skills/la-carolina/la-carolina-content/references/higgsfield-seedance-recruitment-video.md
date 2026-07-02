# Higgsfield / Seedance — videos de reclutamiento La Carolina

Notas aprendidas durante campaña de reclutamiento de conductores.

## Modelo recomendado

- Para reels verticales con referencias reales de buses/personas: **Seedance 2.0**.
- Parámetros usados como base:
  - `model`: `seedance_2_0`
  - `aspect_ratio`: `9:16`
  - `duration`: 12 s cuando se necesita una secuencia completa; 8–10 s si se busca algo más simple.
  - `resolution`: `720p` como base práctica.
  - `mode`: `std` para mejor calidad, `fast` solo si el usuario prioriza rapidez.
- Seedance 2.0 acepta referencias con roles como `image`, `start_image`, `end_image`, `video`, `audio`.

## Flujo operativo antes de generar

1. Verificar modelo/constraints con `models_explore(action='get', model_id='seedance_2_0')`.
2. Subir referencias con `media_upload`.
3. Hacer PUT/curl de cada archivo local al upload URL.
4. Confirmar con `media_confirm(type='image', media_ids=[...])`.
5. Consultar `balance` y hacer `generate_video(..., get_cost=true)` para preflight.
6. Solo iniciar la generación real si el plan/créditos lo permiten.
7. Si el backend responde `Requires plus plan or higher`, abrir `show_plans_and_credits(intent='upgrade')` y relatar el enlace de upgrade sin intentar regenerar hasta que el usuario confirme el upgrade.

## Plan / créditos

- En sesión de prueba, Seedance 2.0 costó **54 créditos** para 1 video vertical 12 s 720p con referencias.
- Aunque la cuenta tenía créditos, la generación falló en plan **Basic** con: `Requires plus plan or higher`.
- Lección: para Seedance 2.0, no basta con créditos; comprobar también plan. Si está en Basic, avisar que requiere Plus o superior.
- Si el usuario necesita avanzar sin upgrade, usar **Grok Imagine** como fallback básico para image-to-video: `model: grok_video`, `role: start_image`, 9:16, 10 s. En prueba costó 15 créditos y permitió generar un video desde el primer plano del conductor. Para consistencia de identidad, usar como `start_image` un recorte claro del rostro/conductor y pedir explícitamente que ese mismo conductor maneje el bus.
- Si la usuaria necesita avanzar sin upgrade y busca una versión más sencilla, probar **Grok Imagine** como fallback de imagen-a-video: acepta `start_image`, 9:16, duración corta, y en la sesión costó menos créditos. No ofrece la misma consistencia multi-referencia de Seedance, así que usarlo solo para animar una imagen de partida clara y reforzar en el prompt “mismo conductor / mismo rostro / no cambiar actor”.

## Referencias visuales útiles para reclutamiento

Priorizar pocas imágenes fuertes:

- Logo oficial transparente.
- Bus exterior completo/lateral (`DSC_0953` cuando esté disponible).
- Bus frontal (`DSC_0874` / `DSC_0878`).
- Interior/volante (`DSC_0010`).
- Posts previos de campaña para conservar estética, especialmente si la usuaria dice que ya dio dos imágenes.

## Prompt base para video reclutamiento

Estructura recomendada para reel 9:16:

1. 0–2 s: bus La Carolina avanzando; texto grande: `BUSCAMOS CONDUCTORES`.
2. 2–5 s: conductor entrando al bus o tomando el volante; texto: `TU EXPERIENCIA AL VOLANTE`.
3. 5–8 s: interior/volante, operación real; cajas doradas: `RESPONSABILIDAD`, `LICENCIA VIGENTE`, `ACTITUD DE SERVICIO`.
4. 8–12 s: cierre con bus + conductor + logo; CTA: `POSTÚLATE HOY`; slogan: `Transporte con corazón`.

Mantener rojo `#C22219`, dorado `#DCBE61`, negro `#222222`, blanco; flechas `>>>`, línea de ruta/carretera y bloques diagonales.

## Consistencia de personaje / conductor

Cuando la usuaria pida que el conductor del inicio sea el mismo al final, no basta con describirlo en el prompt. Para reforzar identidad visual:

- Usar la misma referencia del conductor como `start_image` y también como `end_image` cuando el modelo lo permita.
- En el prompt, repetir explícitamente: “same driver from the opening shot appears again in the final shot; same face, same uniform, same person”.
- Si el primer resultado cambia el rostro/persona, regenerar con la estructura start/end más rígida antes de darlo por final.

## Entrega del video final

Después de que el job termine:

1. Tomar el `result_url`/CloudFront URL desde `job_status`.
2. Descargar el MP4 a `/root/.hermes/document_cache/` con nombre claro, por ejemplo `video_reclutamiento_la_carolina_<variante>.mp4`.
3. Verificar tamaño no cero.
4. En Telegram, entregar el archivo con `MEDIA:/root/.hermes/document_cache/<archivo>.mp4` y añadir el enlace directo solo como respaldo.

## Pitfall de texto y logos

Seedance puede deformar textos y logos. Para una pieza final profesional:

- Usar Seedance para la base visual y movimiento.
- No depender de Seedance para textos pequeños, requisitos extensos o logo perfecto.
- Si el resultado visual sirve, añadir textos/CTA/logo exactos después en Canva, CapCut o Premiere.
