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

## Nombrado automático (OCR local)

La app lee la zona MRZ del reverso de cada cédula con Tesseract.js (100% en el
navegador) y nombra cada PDF como `Cédula_Apellido_Nombre`. Si el OCR no logra
leer un reverso, ese archivo usa el nombre genérico `cedula_NN` y puede editarse
manualmente antes de descargar. El nombre del ZIP también es editable.

Nota de privacidad: la imagen nunca sale del dispositivo. En el primer uso se
descarga una única vez el modelo genérico de idioma (~2 MB) desde un CDN; ese
modelo no contiene ni recibe ningún dato personal.

## Endurecimiento opcional (recomendado a futuro)

- **Vendorizar las librerías**: descargar las librerías (`pdf.min.js`,
  `pdf.worker.min.js`, `pdf-lib.min.js`, `jszip.min.js`, `tesseract.min.js` y el
  modelo `eng.traineddata`) a una carpeta `/vendor` del repositorio y actualizar
  las rutas en `index.html`. Con eso la app funciona sin internet y se elimina
  la dependencia de los CDN.

## Limitaciones conocidas

- La detección asume tarjetas apoyadas horizontalmente (sin rotaciones fuertes).
- Escaneos muy oscuros, con fondo no blanco o de muy baja resolución pueden requerir
  ajustar los umbrales de detección en `detectCards()` dentro de `index.html`.
