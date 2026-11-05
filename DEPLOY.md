# Despliegue — Transportes Don Alfonso

Sitio estático para `https://transportesdonalfonso.cl/`. No requiere build,
PHP, base de datos ni Node.js en producción: solo un servidor que entregue
archivos y HTTPS.

## 1. Estructura publicable

- `index.html`: página principal, servicios, flota, FAQ, formulario y mapa.
- `transporte-carga-chile-brasil/`, `transporte-carga-chile-argentina/`,
  `transporte-carga-chile-paraguay/`, `transporte-refrigerado/` y
  `transporte-ftl-ltl/`: páginas comerciales con contenido propio.
- `privacidad.html`, `terminos.html`, `cookies.html`, `reclamaciones.html`:
  páginas legales. Tienen `noindex` intencional y no van en el sitemap.
- `404.html`: página de error.
- `assets/`: imágenes, fuentes y estilos compartidos.
- `sitemap.xml`, `robots.txt` y `manifest.webmanifest`: SEO y PWA.
- `.htaccess` y `nginx.conf.example`: configuración para servidores propios.
- `wrangler.jsonc`: configuración de Cloudflare Workers Assets.

No hay placeholders `{{...}}` pendientes en el sitio publicado.

## 2. Cloudflare Workers Assets

El proyecto publica la carpeta raíz como assets estáticos:

```bash
npx wrangler deploy
```

Para probar en local:

```bash
npx wrangler dev
```

`wrangler.jsonc` usa `"not_found_handling": "404-page"`, por lo que
`404.html` responde ante rutas inexistentes. No se necesita comando de build.

Si se publica con Cloudflare Pages en lugar de Workers, usar la raíz del
repositorio como directorio de salida y mantener `404.html` en la raíz.

## 3. Archivos que no deben publicarse

Antes de desplegar, eliminar o excluir los archivos de trabajo:

- `README.md`, `DEPLOY.md`, `fix_map.ps1`, `wrangler.jsonc`,
  `nginx.conf.example` y `.htaccess`.
- `logo_count.txt`, `logo_lines.txt`, `logo_list.txt`, `logos.txt`,
  `svc_css.txt`, `svc_func.txt`, `svc_js.txt`, `temp_services.txt`,
  `theme_css.txt` y `trans_names.txt`.

El archivo `.assetsignore` excluye esos archivos en Cloudflare Workers Assets.
En otros hostings, borrarlos antes de subir la carpeta o bloquearlos desde el
servidor.

## 4. Formularios y analytics

- El formulario principal usa `action=""`. Si no existe un endpoint real, el
  JavaScript arma un `mailto:` con los datos y avisa al usuario.
- `reclamaciones.html` usa `mailto:jmallendesa@transallen.cl` con el mismo
  respaldo por JavaScript.
- `GA_ID` está vacío: Google Analytics no se carga. Si se configura, debe
  respetar el consentimiento de cookies.
- Los datos de contacto visibles y del JSON-LD son:
  `+56 2 2929 6883`, `+56 9 8744 3471`, `+56 9 9076 2211`,
  `hugo@transallen.cl`, `jmallendesa@transallen.cl` y
  `transportesdonalfonso@gmail.com`.

## 5. Verificación antes de publicar

- [ ] No queda ningún `{{...}}` en HTML, XML, TXT ni Webmanifest.
- [ ] `https://transportesdonalfonso.cl/robots.txt` y `sitemap.xml` responden.
- [ ] Las 6 URLs del sitemap responden con 200 y canonical correcto.
- [ ] `404.html` responde ante una ruta inexistente.
- [ ] Los formularios llegan a un correo que se revisa a diario.
- [ ] Se probó el sitio en móvil, tablet y escritorio.
- [ ] Se probaron el tema claro/oscuro, el selector ES/PT y el menú móvil.
- [ ] Se revisó la consola del navegador sin errores.
- [ ] Se validó el JSON-LD y no hay enlaces internos rotos.

## 6. Datos confirmados y pendientes

El cliente confirmó el rango de temperatura de -25 °C a +25 °C, los sensores
de temperatura, el seguro de carga según póliza, las características Euro VI y
ABS, las capacidades de 26/28 toneladas, las frecuencias/tiempos de operación y
la autorización de los tres testimonios publicados.

Por decisión del cliente, no se publican razón social, RUT, domicilio ni
representante legal. El formulario mantiene el respaldo `mailto:` y Google
Analytics permanece desactivado.

Pendiente antes de publicar:

- revisión de los textos legales por un abogado, especialmente en protección
  de datos, comercio internacional, seguros y Libro de Reclamaciones;
- confirmar que los correos de contacto se revisan a diario.

## 7. Search Console

1. Verificar la propiedad del dominio.
2. Enviar `https://transportesdonalfonso.cl/sitemap.xml`.
3. Revisar `Páginas` e `Indexación` para confirmar las 6 URLs comerciales.
4. Inspeccionar la URL principal y solicitar indexación.
5. Revisar errores de datos estructurados y de experiencia móvil.
