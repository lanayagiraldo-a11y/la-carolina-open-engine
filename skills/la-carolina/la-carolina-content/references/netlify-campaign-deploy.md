# Netlify deploy — landing HTML de campañas La Carolina

Patrón aprendido para actualizar landings estáticas de campaña de La Carolina en Netlify.

## Sitio usado para campaña de conductores

- Nombre Netlify: `campana-conductores-la-carolina`
- URL pública: `https://campana-conductores-la-carolina.netlify.app`
- Site ID: `622f1025-99a0-4425-ab17-90ffcfc6e82d`
- Carpeta local usada en sesión: `/root/.hermes/document_cache/la-carolina-meta-ads-site`

## Flujo recomendado

1. Copiar las piezas aprobadas dentro de la carpeta del sitio, normalmente:
   - `assets/campaign-posts-approved/post-01-...jpg`
   - `assets/campaign-posts-approved/post-02-...jpg`
   - `assets/campaign-posts-approved/post-03-...jpg`
2. Editar `index.html` para referenciar esas rutas relativas.
3. Verificar que el HTML contenga las rutas y que los archivos existan.
4. Si no hay sesión Netlify activa, pedir autorización con:

```bash
npx --yes netlify-cli login --request 'Hermes necesita autorización para desplegar el HTML de campaña La Carolina en Netlify' --json
```

Entregar al usuario la URL de autorización y luego validar con:

```bash
npx --yes netlify-cli login --check <ticket_id> --json
```

5. Desplegar directamente como sitio estático, evitando build remoto:

```bash
cd /root/.hermes/document_cache/la-carolina-meta-ads-site
npx --yes netlify-cli deploy --prod --no-build --dir . \
  --site 622f1025-99a0-4425-ab17-90ffcfc6e82d \
  --message 'Actualizar campaña La Carolina'
```

## Pitfalls

- No usar el sitio `willowy-clafoutis-e2a00e` por defecto si el objetivo es la campaña de conductores; listar sitios y escoger `campana-conductores-la-carolina`.
- Si se omite `--no-build`, Netlify puede intentar ejecutar una configuración de UI antigua como `hugo` y fallar aunque la landing sea HTML estático.
- Si `deploy` responde `Unauthorized` tras login, confirmar el Site ID correcto y que pertenece al equipo `Financiero`.
- Después del deploy, verificar la URL pública con una lectura HTTP y confirmar que aparecen las rutas de los posts aprobados.

## Entrega al usuario

Responder con el link público final y una lista breve de lo verificado. No entregar solo ZIP si el usuario pidió deploy y ya autorizó Netlify.
