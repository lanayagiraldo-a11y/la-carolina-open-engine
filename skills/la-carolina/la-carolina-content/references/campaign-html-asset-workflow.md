# La Carolina — flujo para HTML de campaña y piezas aprobadas

Usar cuando la usuaria esté construyendo una campaña visual de La Carolina y pida “subir posts al HTML”, “guardar opción X”, “cuando terminemos todas las fotos”, o “subir al link”.

## Principios operativos

- Tratar cada imagen aprobada como **asset final**, no como borrador.
- Guardar las piezas aprobadas en una carpeta estable del sitio, preferiblemente:
  - `assets/campaign-posts-approved/`
- Nombrar por orden y función, por ejemplo:
  - `post-01-buscamos-conductores.jpg`
  - `post-02-experiencia-volante.jpg`
  - `post-03-requisitos-para-postularte.jpg`
- Si la usuaria envía “opción 2” para un post ya aprobado, reemplazar el archivo canónico del post, no crear una variante suelta que el HTML no use.
- Actualizar el `index.html` para que referencie los archivos canónicos aprobados, no rutas temporales de `image_cache`.
- Verificar que los archivos referenciados en el HTML existan antes de entregar.

## Preferencias de la usuaria para La Carolina

- Las piezas deben verse **muy realistas**, no genéricas, caricaturescas ni de mockup.
- La usuaria espera que las piezas aprobadas se vayan acumulando para el **link final**.
- En Telegram, si aún no se puede actualizar el link público por falta de autorización de despliegue, entregar un ZIP actualizado como respaldo y explicar brevemente qué falta para publicar.

## HTML de campaña

Cuando se acumulen posts finales:

1. Copiar los posts aprobados a `assets/campaign-posts-approved/` con nombres canónicos.
2. Sustituir o crear una sección de “Posts aprobados” / “Copies listos” que muestre los posts en orden.
3. Usar captions cortos que indiquen el rol de cada pieza:
   - Post 1: pieza directa de vacante.
   - Post 2: pieza emocional de respaldo/confianza.
   - Post 3: requisitos para postularse.
4. Mantener branding La Carolina: rojo, dorado, negro, blanco, títulos grandes y lectura móvil.
5. Empaquetar el sitio si no hay despliegue activo.

## Carrusel de reclutamiento

Para carruseles de reclutamiento de conductores, generar 6 piezas coherentes:

1. Portada: “¿Quieres trabajar como conductor?” / “La Carolina te está buscando”.
2. Licencia vigente: preferiblemente C2 o C3.
3. Experiencia conduciendo: bus, buseta, taxi, camión o servicio público.
4. Responsabilidad: cumplimiento, respeto y compromiso.
5. Buena actitud de servicio: transportar personas con respeto y corazón.
6. Cierre: “Postúlate hoy” / “Haz parte de La Carolina” / “Transporte con corazón”.

El prompt debe insistir en: realismo fotográfico, conductor realista con uniforme blanco, bus dorado/negro, alto contraste, textos grandes legibles, logo consistente y no deformado.
