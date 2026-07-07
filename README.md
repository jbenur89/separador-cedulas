# Separador de Cédulas de Identidad

Herramienta interna para OTEC que procesa un PDF con escaneos de cédulas de identidad
(páginas alternadas: impares = caras frontales, pares = caras posteriores) y genera un
PDF individual por cada cédula, con ambas caras centradas en una sola página tamaño carta.

## Arquitectura de privacidad

- **Procesamiento 100% en el navegador.** El PDF nunca se sube a ningún servidor.
  GitHub Pages solo entrega el código de la aplicación; los datos jamás salen del dispositivo.
- **Sin almacenamiento.** No se usa localStorage, cookies ni caché de datos. Al cerrar
  la pestaña, todo se descarta de la memoria.
- **Content Security Policy estricta.** La página bloquea cualquier conexión de red
  distinta de la carga inicial de librerías (cdnjs.cloudflare.com). Aunque el código
  fuera alterado, no podría exfiltrar datos.
- **Este repositorio nunca debe contener PDFs con datos reales.** El `.gitignore`
  bloquea todos los archivos PDF por defecto. No lo elimines.

## Despliegue en GitHub Pages (paso a paso)

1. Crea un repositorio nuevo en GitHub (por ejemplo `separador-cedulas`).
2. Sube los archivos de esta carpeta (`index.html`, `README.md`, `.gitignore`):
   ```bash
   git init
   git add index.html README.md .gitignore
   git commit -m "Separador de cédulas: procesamiento 100% local"
   git branch -M main
   git remote add origin https://github.com/TU_USUARIO/separador-cedulas.git
   git push -u origin main
   ```
3. En GitHub: **Settings → Pages → Build and deployment**:
   - Source: *Deploy from a branch*
   - Branch: `main` / carpeta `/ (root)` → **Save**
4. En 1–2 minutos la app queda disponible en
   `https://TU_USUARIO.github.io/separador-cedulas/`

## Uso

1. Abre la página en cualquier dispositivo (funciona en escritorio y móvil).
2. Arrastra o selecciona el PDF con los escaneos.
3. Revisa las miniaturas detectadas, renombra los archivos si quieres
   (por ejemplo con el RUN del alumno) y descarga cada PDF o el ZIP completo.

## Formato de entrada esperado

- Página 1: cara(s) frontal(es) · Página 2: cara(s) posterior(es) · Página 3: frontales · Página 4: posteriores…
- Las cédulas deben mantener el mismo orden entre la página frontal y su página posterior.
- Fondo blanco de escáner con buen contraste. Soporta varias cédulas por página.

## Endurecimiento opcional (recomendado a futuro)

- **Vendorizar las librerías**: descargar `pdf.min.js`, `pdf.worker.min.js`, `pdf-lib.min.js`
  y `jszip.min.js` a una carpeta `/vendor` del repositorio y actualizar las rutas en
  `index.html`. Con eso la app funciona incluso sin internet (basta abrir `index.html`
  localmente) y se elimina la dependencia del CDN.
- **Nombrado automático por RUN**: agregar OCR local (Tesseract.js) sobre la zona MRZ
  del reverso para nombrar cada PDF con el RUN del alumno. También 100% en el navegador.

## Limitaciones conocidas

- La detección asume tarjetas apoyadas horizontalmente (sin rotaciones fuertes).
- Escaneos muy oscuros, con fondo no blanco o de muy baja resolución pueden requerir
  ajustar los umbrales de detección en `detectCards()` dentro de `index.html`.
