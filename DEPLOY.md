# Despliegue — TRANSALLENDES

Sitio estático. No necesita PHP, base de datos ni Node.js. Solo un servidor web
que sirva archivos y HTTPS.

---

## 1. Antes de publicar: sustituir los placeholders

Abre **todos** los archivos de esta carpeta y reemplaza cada `{{...}}`.
No queda ninguno válido para publicar.

| Placeholder | Qué poner | Ejemplo |
|---|---|---|
| `{{DOMINIO}}` | Dominio **sin** `https://` ni `www.` | `transallendes.cl` |
| `{{RAZON_SOCIAL}}` | Razón social exacta del proveedor | `Servicios de Carga S.A.` |
| `{{RUT}}` | RUT de la empresa | `76.123.456-7` |
| `{{REPRESENTANTE}}` | Nombre del representante legal | `Nombre Apellido` |
| `{{DOMICILIO}}` | Dirección completa de casa matriz | `Av. Ejemplo 1234, Of. 56, Santiago` |
| `{{TELEFONO}}` | Teléfono principal, formato internacional sin `+` | `5622345678` |
| `{{TELEFONO_ALT}}` | Teléfono de respaldo | `5629876543` |
| `{{CELULAR}}` | Celular de contacto comercial | `56912345678` |
| `{{WHATSAPP}}` | Mismo número de celular | `56912345678` |
| `{{WHATSAPP_NUMEROS}}` | Números que se muestran en el botón flotante | `56912345678` |
| `{{EMAIL_OPERACIONES}}` | Correo de soporte y operações | `operaciones@transallendes.cl` |
| `{{EMAIL_COTIZACIONES}}` | Correo comercial que recibe cotizaciones | `cotizaciones@transallendes.cl` |
| `{{FORM_ENDPOINT}}` | URL que recibe el formulario de cotización | `https://formulario.tudominio.cl/cotizacion` |
| `{{RECLAMACIONES_ENDPOINT}}` | URL que recibe el Libro de Reclamaciones | `https://formulario.tudominio.cl/reclamo` |
| `{{INSTAGRAM}}` | **Solo el usuario**, sin `@` ni URL | `transallendes` |
| `{{LINKEDIN}}` | **Solo el identificador**, sin URL | `transallendes` |
| `{{GA_ID}}` | ID de medición de Google Analytics 4 | `G-XXXXXXXXXX` |
| `{{FECHA_ACTUALIZACION}}` | Fecha de última revisión legal visible | `12 de marzo de 2026` |

### Verificación de que no quedó nada

```powershell
$p = 'C:\ruta\a\esta\carpeta'
Select-String -Path "$p\*.html","$p\*.xml","$p\*.txt","$p\*.webmanifest" -Pattern '\{\{'
```

Si no devuelve nada, estás listo.

### Comportamiento si algo queda sin configurar

- Sin `{{GA_ID}}` → **no se carga Google Analytics**. Es intencional, no es un error.
- Sin `{{FORM_ENDPOINT}}` → el formulario se envía por correo a
  `{{EMAIL_COTIZACIONES}}` y avisa al usuario en pantalla.
- Con `{{FORM_ENDPOINT}}` real → envío por HTTP `POST` a ese destino.

---

## 2. Opciones de despliegue

### 2.1 Apache

Sube el contenido a `public_html/` o `www/`. El archivo `.htaccess` ya included:
redirección a HTTPS, página 404, caché, compresión y cabeceras de seguridad.

Requiere los módulos `rewrite`, `deflate`, `expires`, `headers`, `mime`.

### 2.2 Nginx

`nginx.conf.example` tiene un bloque `server {}` listo. Copiarlo dentro de la
configuración del sitio, ajustar `root`, `server_name` y las rutas del
certificado, luego:

```bash
nginx -t && systemctl reload nginx
```

### 2.3 Hosting estático (Netlify, Vercel, Cloudflare Pages, GitHub Pages)

Sube la carpeta tal cual. No uses `.htaccess` en esos servicios: la configuración
de cabeceras y redirecciones se hace en su panel o en un `headers` propio.

Si usas Netlify, agrega un `_redirects` o configura en `netlify.toml`:

```
/privacidad  /privacidad.html  301
/terminos    /terminos.html    301
/cookies     /cookies.html     301
```

---

## 3. Certificado HTTPS

En servidor propio con Let's Encrypt:

```bash
sudo certbot --nginx -d {{DOMINIO}} -d www.{{DOMINIO}}
```

O, en Apache, con `certbot --apache`. El certificado se renueva automáticamente
si instalaste el temporizador de `certbot`.

**No actives `Strict-Transport-Security` en `.htaccess` hasta confirmar que todo
el sitio, incluidos los subdominios, responde por HTTPS.** Está comentado a
propósito.

---

## 4. Lista de verificación antes de publicar

- [ ] No queda ningún `{{...}}` en el sitio.
- [ ] Razón social, RUT y domicilio coinciden con la empresa real.
- [ ] Los teléfonos y correos responden de verdad.
- [ ] El formulario de cotización llega a un correo que se revisa a diario.
- [ ] El Libro de Reclamaciones tiene un destino de envío real y **alguien
      responde en menos de 10 días hábiles**.
- [ ] Google Analytics 4 está configurado y **no se carga antes del consentimiento**.
- [ ] Probaste en móvil, tablet y escritorio.
- [ ] Probaste el tema claro y el oscuro.
- [ ] Probaste el mapa: aparece la puerta de consentimiento y carga al aceptar.
- [ ] La página 404 funciona.
- [ ] `https://tudominio.cl/robots.txt` y `sitemap.xml` responden bien.

---

## 5. Revisión legal pendiente

Los textos legales de `privacidad.html`, `terminos.html`, `cookies.html` y
`reclamaciones.html` son **una base de trabajo, no un documento final**. Antes
de publicar deben revisarlos un abogado, en particular:

- plazos de conservación y transferencias internacionales;
- límites de responsabilidad y cobertura del seguro de carga;
- cláusulas de jurisdicción y normativa aplicable a cada país de operación;
- adequacy del Libro de Reclamaciones con la Ley 19.496;
- textos de terceros (Google Analytics, OpenStreetMap, CARTO).

Lo mismo aplica a las **métricas, la experiencia y los testimonios** publicados
en `index.html`: deben ser verificables y estar autorizados por escrito.

---

## 6. Datos que se almacenan en el navegador

| Clave | Contenido | Para qué |
|---|---|---|
| `tl_consent` | `granted` / `denied` | No volver a preguntar por analítica |
| `tl_map` | `1` | Recordar que ya activaste el mapa |
| `tl_theme` | `light` / `dark` | Recordar el tema elegido |

No hay cookies propias de seguimiento. `tl_consent` es el mecanismo que impide
cargar Google Analytics antes de que el visitante acepte.

Para cambiar la decisión, el enlace **"Cambiar preferencias de cookies"** del pie
borra `tl_consent` y recarga la página.

---

## 7. Cómo se generó este sitio

`index.html` se generó a partir de `imagenes/landing-v6.html` mediante tres
pasadas de PowerShell en `C:\Users\garay\AppData\Local\Temp\opencode`:

| Script | Qué hace |
|---|---|
| `build-site.ps1` | Rutas de imágenes, WebP, `lazy`, datos de contacto, mapa |
| `build-site2.ps1` | Formulario, enlaces legales, SEO, JSON-LD |
| `build-site3.ps1` | `<main>`, CSS, banner de consentimiento, JS |

Para regenerar desde cero: `build-site.ps1` → `build-site2.ps1` → `build-site3.ps1`.

Las imágenes WebP se generaron con `webp3.ps1` (usa `ffmpeg`).

`landing-v6.html` es la fuente original y **no se modifica**. Los fondos de las
secciones y las imágenes usadas como `background-image` se conservaron tal cual.
