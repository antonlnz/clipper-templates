---
site: Instagram
template_version: '1.4'
template_updated: '2026-09-25'
template_file: '[[Sistema/Clipper/JSON/Instagram.json]]'
---

# Instagram

[[Plantillas Clipper|← Catálogo]]

Para posts y reels de Instagram (`/p/`, `/reel/`); se activa sola. Guarda autor, fecha, la imagen de portada y el texto completo de la publicación en una cita. Los reels van a [[Categorías/Vídeos]] y los posts a [[Categorías/Artículos]].

Funciona tanto si abres el post directamente como si llegas a él navegando dentro de Instagram: en el segundo caso la página no tiene metadatos y la plantilla lee el usuario, el texto y la imagen de la propia página.

En los carruseles guarda solo la primera imagen, e indica en la nota cuántas tiene: Instagram solo carga en la página las fotos cercanas a la que estás viendo.

No guarda transcripción: Instagram no publica en la página el texto de lo que se dice en un reel, y Web Clipper no puede transcribir audio.

La imagen es un enlace a los servidores de Instagram, que caduca al cabo de unos días. Para conservarla, usa **Download attachments for current file** después de capturar.

## Archivo importable

[[Sistema/Clipper/JSON/Instagram.json|Abrir el JSON]]

En Web Clipper → Ajustes → Plantillas → Importar, selecciona este archivo. Si editas la versión del navegador, expórtala para actualizar esta copia.
