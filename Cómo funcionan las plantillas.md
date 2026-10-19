---
{}
---

# Cómo funcionan las plantillas

[[Plantillas Clipper|← Catálogo]] · [[Sistema/Clipper/Instalación y mantenimiento|Instalación y mantenimiento]]

Una plantilla de Web Clipper le dice qué datos sacar de una página y cómo escribirlos en una nota. Esta guía explica sus partes, las variables disponibles y cómo encontrar en el HTML el dato que te interesa.

## Partes de una plantilla

| Campo en Clipper | En el JSON | Para qué sirve |
|---|---|---|
| Nombre | `name` | Cómo aparece en la lista del Clipper, p. ej. «Andel · IMDb». |
| Activadores | `triggers` | Direcciones en las que se elige sola. Puede ser el principio de la URL (`https://open.spotify.com/episode/`) o una expresión regular entre barras (`/^https:\/\/(www\.)?imdb\.com\/title\//`). |
| Nombre de la nota | `noteNameFormat` | Título del archivo que se crea. |
| Carpeta y bóveda | `path`, `vault` | Aquí siempre `03_Fuentes` y `Andel`. |
| Propiedades | `properties` | El bloque de propiedades de la nota. Sus nombres (`categories`, `authors`, `status`…) no se traducen: las bases de la bóveda los usan para filtrar. |
| Contenido | `noteContentFormat` | El cuerpo de la nota: tus secciones y los datos capturados. |

Todo lo que va entre `{{ }}` se sustituye por un dato de la página.

## Variables: de dónde sale cada dato

| Tipo | Ejemplo | Qué devuelve |
|---|---|---|
| Básicas | `{{title}}`, `{{url}}`, `{{author}}`, `{{date}}`, `{{image}}`, `{{description}}` | Datos generales de la página. |
| Contenido | `{{content}}` | El texto principal de la página en Markdown. En YouTube, X, Reddit, ChatGPT o Claude usa extractores propios que ya separan publicación, hilo, comentarios o turnos. |
| Del extractor | `{{transcript}}`, `{{language}}` | Datos extra de ciertos sitios; `transcript` solo existe en YouTube. |
| Metadatos | `{{meta:property:og:image}}`, `{{meta:name:citation_title}}` | Las etiquetas `<meta>` de la cabecera. Son estables porque las usan buscadores y redes sociales. |
| Datos estructurados | `{{schema:@Movie:name}}`, `{{schema:@Product:offers.price}}` | Los datos schema.org que muchas webs publican para Google (películas, libros, productos, cursos). Es la fuente más fiable cuando existe. |
| Selector | `{{selector:#productTitle}}` | El texto de un elemento del HTML, elegido con un selector CSS. |
| Atributo | `{{selector:#landingImage?data-old-hires}}` | El valor de un atributo del elemento (lo que va tras `?`). |
| HTML | `{{selectorHtml:main h2 + div}}` | El HTML del elemento; con `\|markdown` se convierte en Markdown. |

## Filtros

Se encadenan con `|` y transforman el dato. Los más usados aquí:

- `|first`, `|join:", "`: el primer resultado o todos unidos, cuando un selector encuentra varios elementos.
- `|trim`, `|safe_name`: quitar espacios, o caracteres no válidos en un nombre de archivo.
- `|date:"YYYY-MM-DD"`: formatear una fecha.
- `|replace:"/patrón/":"sustituto"`: una expresión regular. `$1` recupera lo capturado entre paréntesis.
- `|blockquote`: convierte el texto en cita, útil para meterlo en un callout (ver más abajo).
- `|markdown`: convierte HTML en Markdown.
- `?? otra_variable`: alternativa si la primera sale vacía.

También hay lógica: `{% if … %}…{% endif %}`, `{% set x = … %}` y `{% for … %}`. Por ejemplo, IMDb elige Películas o Series según el tipo de página: `{% if meta:property:og:type contains "tv" %}`.

## Callouts (cuadros y desplegables)

Un callout es un bloque de cita con un tipo entre corchetes. Las plantillas los usan para separar el contenido capturado de tus apuntes:

```markdown
> [!quote] Sinopsis
> El texto capturado va aquí, con `>` al principio de cada línea.
```

El símbolo que va justo después del `]` decide si se puede plegar:

| Sintaxis | Resultado |
|---|---|
| `> [!quote] Título` | Siempre abierto; no se puede plegar. |
| `> [!quote]- Título` | **Plegado por defecto.** Se abre con un clic. Para contenido largo que no quieres ver siempre. |
| `> [!quote]+ Título` | Abierto por defecto, pero se puede plegar. |

El tipo (`quote`, `info`, `note`, `tip`, `warning`…) solo cambia el color y el icono. Para meter texto de varias líneas dentro, pásalo por el filtro `|blockquote`, que añade `>` a cada línea.

Callouts plegados en las plantillas actuales:

- **YouTube:** Transcripción.
- **Publicación de X:** Respuestas.
- **Reddit** y **Hacker News:** Comentarios.
- **Anna's Archive:** Descripción y metadatos.
- **ServerPartDeals:** Especificaciones y garantía.

El resto (sinopsis, descripciones, resúmenes, características, referencias) son callouts abiertos.

## Cómo encontrar un dato en el HTML

1. **Mira primero las variables de la página.** En la ventana del Clipper, pulsa «…» (*Mostrar variables de página*). Verás todas las `meta:` y `schema:` disponibles con su valor. Si el dato está ahí, úsalo: es lo más estable.
2. **Si no está, inspecciona el elemento.** Clic derecho sobre el dato → *Inspeccionar*. Busca en el elemento o en sus padres algo que identifique el dato y no cambie:
   - Buenos: `id` (`#productTitle`), `data-testid` (`[data-testid="UserDescription"]`), `itemprop` (`[itemprop="director"]`), la dirección de un enlace (`a[href*="/genres/"]`).
   - Malos: clases generadas automáticamente como `css-1jxf684` o `x193iq5w`. Cambian sin aviso y rompen la plantilla; fue lo que le pasó a la antigua de X.
3. **Prueba el selector en la consola** del navegador (pestaña *Consola* del inspector):
   ```js
   document.querySelector('[data-testid="genresList"] a').textContent
   ```
   Si devuelve lo que buscas, ese selector vale para `{{selector:…}}`.
4. **Datos ocultos en scripts.** Algunas webs guardan información que no se ve en pantalla dentro de un `<script>` con JSON, como el `__NEXT_DATA__` de Goodreads, de donde salen el título original y la lista completa de géneros. Se leen con `{{selector:#__NEXT_DATA__}}` y un `replace` que extraiga el trozo concreto.
5. **Prueba antes de guardar.** La ventana del Clipper muestra la nota tal como quedará. Si una propiedad sale vacía, revisa el selector en la consola.

## Cosas que conviene saber

- **No uses barras invertidas en las expresiones regulares** (`\s`, `\d`, `\[`). El Clipper las elimina al procesar la plantilla y el patrón cambia sin avisar: `\s+` pasa a ser `s+` y borra las letras «s». Usa clases de caracteres: `[ \n]+` para espacios y saltos de línea, `[0-9]` para dígitos, `[.]` para un punto, `[(]` para un paréntesis. `\n` (salto de línea) sí funciona.
- **Las propiedades de tipo lista separan por comas.** Un autor como «Bok, S.» se partiría en dos; por eso las plantillas de papers lo convierten a «S. Bok».
- **Evita expresiones que recorran páginas enteras** con patrones como `[^]*?` repetidos: en páginas grandes (los datos internos de Goodreads ocupan cientos de kB) la captura puede tardar mucho. Es más rápido cortar el texto con `split` hasta quedarte con el trozo que te interesa.

- **El Clipper copia lo que hay en la página en ese momento.** Si un texto aparece recortado con «Mostrar más», despliégalo antes de capturar.
- **Sin sesión, algunas webs (X, Instagram) muestran otra estructura.** Las plantillas están pensadas para la versión con tu sesión iniciada.
- **Las copias de la bóveda no se sincronizan solas** con los navegadores. Si cambias una plantilla en el Clipper, expórtala a su JSON de `Sistema/Clipper/JSON` e impórtala en el otro navegador.

Documentación oficial: [variables](https://obsidian.md/help/web-clipper/variables) · [filtros](https://obsidian.md/help/web-clipper/filters) · [lógica](https://obsidian.md/help/web-clipper/logic) · [plantillas](https://obsidian.md/help/web-clipper/templates)
