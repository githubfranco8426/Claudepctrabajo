---
name: nano-banana
description: Genera y edita imágenes con los modelos Nano Banana (Gemini Image de Google) a través de los MCP de Higgsfield o ElevenLabs. Úsala siempre que Franco pida crear, generar, editar, retocar, ampliar o rehacer una imagen — piezas gráficas de Instagram para ALTHIA MED SPA, fotos de portada, fondos, mockups, banners, ilustraciones, flyers — y también cuando diga "nano banana", "nano-banana", "genera una imagen", "hazme una foto", "necesito una pieza para el post", "cámbiale X a esta imagen" o mencione un formato como 1080x1080, 4:5 o story. Aplícala aunque no nombre el modelo: es el camino por defecto para cualquier imagen en este repo.
---

# Nano Banana

Nano Banana es la familia de modelos de imagen de Google (Gemini Image). Destaca en tres
cosas que importan para el trabajo de ALTHIA: entiende párrafos narrativos en vez de listas
de keywords, **renderiza texto legible dentro de la imagen** (títulos, botones, teléfonos) y
edita una imagen existente conservando lo que no le pides cambiar.

## Antes de generar: esto cuesta plata

Cada generación consume créditos de la cuenta de Franco. Dos reglas que evitan cobros dobles:

- **Nunca repitas una llamada para "reintentar"** — una segunda llamada es una segunda
  generación cobrada, no un reintento. Si algo falla, revisa el estado del job antes de
  volver a pedir.
- Ante un encargo grande (varias variantes, 4K, una serie de piezas), **presupuesta primero**:
  `get_cost: true` en Higgsfield, `estimate_only: true` en ElevenLabs. Ninguno de los dos
  genera nada, solo devuelven el costo.

Si el encargo es ambiguo (¿qué formato? ¿con texto o sin texto?), pregunta antes de gastar.
Una pregunta cuesta cero; una tanda de 4 imágenes equivocadas no.

## Qué conector usar

**Higgsfield (`mcp__Higgsfield__generate_image`) es el camino por defecto.** Controla el
`aspect_ratio` y la resolución como parámetros reales, y eso importa: Nano Banana obedece mal
las proporciones cuando se las pides solo por texto en el prompt. Para una pieza de Instagram
que tiene que salir 1080×1080 exacto, el parámetro gana al prompt.

**ElevenLabs (`mcp__ElevenLabs__creative_generate_image` / `creative_edit_image`)** cuando
convenga el canvas editable: devuelve una `url` de flow que Franco puede abrir y seguir
iterando a mano, y encadena bien con video o voz. Ahí el aspect ratio se controla dando una
imagen de referencia con las dimensiones correctas.

Los IDs de modelo, parámetros y proporciones de cada conector están en
`references/modelos.md`. Léelo antes de la primera llamada de la sesión — los IDs difieren
entre conectores (`nano_banana_pro` vs `gemini-3-pro-image`) y equivocarse es un error de
validación seguro.

## Qué modelo elegir

| Encargo | Modelo (Higgsfield) |
| --- | --- |
| Pieza con texto, diagrama, infografía, cualquier cosa que lleve palabras | `nano_banana_pro`, `resolution: "2k"` |
| Foto o fondo sin texto, borrador rápido, explorar ideas | `nano_banana_2` o `nano_banana` |
| Editar una imagen que ya existe | `nano_banana_pro` con la referencia en `medias` |

Para retoque quirúrgico de una zona concreta, `nano_banana_2` acepta `is_inpaint: true` con
una máscara en `medias` (rol `mask`).

## Cómo escribir el prompt

Un párrafo narrativo siempre gana a una lista de tags. El keyword spam ("8k, masterpiece,
trending") no hace nada. Describe **qué hay**, no solo qué quieres evitar.

Cubre: sujeto · acción · entorno · encuadre · luz · materiales · estilo de color.

> Un [estilo] [tipo de plano] de [sujeto], [acción o expresión], en [entorno]. Iluminado por
> [luz], con una atmósfera [ánimo]. Captado con [cámara/lente], destacando [texturas].

**Cuando la pieza lleva texto** — que es casi siempre en este repo — el prompt tiene que ser
mucho más explícito. Cita el texto exacto entre comillas, línea por línea, con su jerarquía:

> Renderiza este texto exactamente:
> - Línea superior: "Althia Med Spa" en sans-serif geométrica fina, blanco, pequeña.
> - Título: "Kinesiología respiratoria infantil" en sans-serif bold, azul profundo, grande.
> - Botón: "Agenda tu hora" en mayúsculas, blanco sobre píldora azul.
>
> Layout: [posición, alineación, espaciado]
> Efectos: [sombra, contorno, brillo]

Si el texto sale mal escrito o mal cortado, la corrección casi nunca es "genera de nuevo":
es citar el texto de forma más explícita y describir la tipografía.

La guía completa de prompting — plantillas de referencias, edición, composición, transferencia
de estilo, localización a otro idioma, y las rarezas de aspect ratio — está en
`references/prompting.md`. Consúltala cuando el encargo se salga de un texto-a-imagen simple.

## Editar una imagen existente

Editar exige separar explícitamente lo que cambia de lo que se queda igual; si no lo dices, el
modelo se siente libre de rehacer el resto.

> Edita la imagen entregada.
> Cambia: [exactamente qué]
> Mantén: [todo lo demás — sé explícito: rostro, pose, encuadre, fondo, luz]
> No cambies la proporción de la imagen de entrada.

Mecánica según conector:

- **Higgsfield**: sube el archivo con `media_upload_widget` (archivo local de Franco) o
  `media_import_url` (URL web), y pasa el `media_id` que devuelve en `medias` con rol
  `image_references`. En `medias[].value` va el id, nunca una URL.
- **ElevenLabs**: pon el archivo en el flow primero — `creative_attach_reference_file` para
  una URL https, o `creative_create_asset_upload` → PUT → `creative_finalize_asset_upload`
  para un archivo local — y pasa el `node_id` resultante como `connect_from` de
  `creative_edit_image`. Sin `connect_from` la llamada falla. Ojo: el default de esa
  herramienta para edición es `gpt-image-2`; si Franco pidió Nano Banana, pasa
  `gemini-3-pro-image` explícitamente.

Nunca le pidas a Franco que adjunte un archivo al chat: las herramientas remotas no leen
adjuntos, se sube por las rutas de arriba.

## Formatos de ALTHIA MED SPA

Las publicaciones viven en `publicaciones/*.md`, con el texto exacto de la pieza en una tabla
y el caption debajo. Cuando el encargo sea "la imagen de este post", lee ese archivo y saca de
ahí el texto — no lo reescribas ni lo parafrasees, es copy aprobado.

| Formato | `aspect_ratio` |
| --- | --- |
| Post cuadrado (1080×1080) | `1:1` |
| Post vertical (1080×1350) | `4:5` |
| Story / reel | `9:16` |

Línea visual de la marca: salud profesional y cálida, luz suave y natural, azules y blancos,
espacio en blanco generoso, nada de estridencia ni de estética de stock corporativo.

Dos límites del rubro salud que conviene respetar sin que haga falta discutirlos: no generes
falsos antes/después clínicos ni testimonios de pacientes inventados, y no pongas en la pieza
promesas de resultados que el copy aprobado no hace.

## Después de generar

Reporta el resultado con lo que Franco necesita para seguir: la URL de la imagen o del flow,
el modelo usado, el formato y — si presupuestaste — el costo. Si generaste varias variantes,
dile cuántas hay y déjalo elegir antes de iterar.
