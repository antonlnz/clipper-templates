---
site: YouTube
template_version: '1.8'
template_updated: '2026-10-18'
template_file: '[[Sistema/Clipper/JSON/YouTube.json]]'
---

# YouTube

[[Plantillas Clipper|← Catálogo]]

Utiliza la extracción integrada de Web Clipper, que obtiene la transcripción sin ejecutar nada más y la guarda en un desplegable «Transcripción» plegado, con cada marca de tiempo enlazada a ese momento del vídeo. También guarda la descripción completa, con los capítulos enlazados, en una cita «Descripción de YouTube», para distinguirla de tus apuntes. La lee de los datos estructurados del vídeo y, si la página no los trae (por ejemplo, al capturar el HTML sin navegador), de los datos del reproductor. En ambos casos comprueba que son del vídeo de la URL: si capturas justo después de cambiar de vídeo, antes de que cargue, la deja vacía en lugar de copiar la del anterior. Probada con Web Clipper 1.4.0 (Safari) y 1.7.1. Guarda únicamente apuntes, texto y referencias; no descarga el vídeo. Si no aparece texto, puedes pegar la transcripción manualmente.

## Subtítulos: abre «Mostrar transcripción» antes de capturar

Web Clipper no mira qué subtítulos tienes activados en el reproductor, y la plantilla tampoco puede: ese texto no está en la página. Lo que decide el idioma es el panel «Mostrar transcripción»:

1. Activa los subtítulos que quieras guardar.
2. Abre «Mostrar transcripción», bajo la descripción del vídeo. El panel se abre en el idioma de esos subtítulos (salvo si usas la traducción automática, que el panel no ofrece).
3. Captura. Clipper copia exactamente la pista del panel y la plantilla guarda su idioma en `transcript_language`.

Si capturas con el panel cerrado, Clipper descarga una pista por su cuenta: prefiere el inglés y, si no hay, la primera de la lista. En vídeos en español con doblaje automático suele acabar en inglés. Abre también el panel cuando la pista venga recortada con frases terminadas en «…». Detalles y comprobaciones: [[Sistema/Transcripciones de YouTube]].

Este paso extra dejará de hacer falta cuando Web Clipper permita elegir el idioma de la transcripción, algo que su extractor ya admite pero la extensión no expone. La petición está abierta en GitHub: [obsidianmd/obsidian-clipper#991](https://github.com/obsidianmd/obsidian-clipper/issues/991) (relacionadas: [#957](https://github.com/obsidianmd/obsidian-clipper/issues/957) y el pull request [#978](https://github.com/obsidianmd/obsidian-clipper/pull/978)). Cuando salga una versión que lo incluya, revisa esta ficha.

## Archivo importable

[[Sistema/Clipper/JSON/YouTube.json|Abrir el JSON]]

En Web Clipper → Ajustes → Plantillas → Importar, selecciona este archivo. Si editas la versión del navegador, expórtala para actualizar esta copia.
