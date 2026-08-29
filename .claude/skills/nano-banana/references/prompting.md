# Guía de prompting — familia Nano Banana

Adaptada de la guía oficial del modelo. Aplica igual en Higgsfield y en ElevenLabs: cambia la
mecánica de la llamada, no cómo piensa el modelo.

Índice: [Cómo piensa](#cómo-piensa) · [Estructura del prompt](#estructura-del-prompt) ·
[Texto dentro de la imagen](#texto-dentro-de-la-imagen) · [Referencias](#usar-referencias) ·
[Edición](#edición) · [Composición](#composición) · [Estilo](#transferencia-de-estilo) ·
[Aspect ratio](#rarezas-de-aspect-ratio) · [Refinar](#refinar) · [Qué evitar](#qué-evitar)

## Cómo piensa

- Entiende lenguaje natural en profundidad: **un párrafo narrativo siempre rinde más que tags
  sueltos**.
- Edita con referencias: agrega, quita o modifica elementos de una imagen dada.
- Compone varias imágenes en una escena nueva, o transfiere el estilo de una a otra.
- Mantiene un personaje consistente si lo describes en detalle una vez y lo referencias
  después dentro de la misma sesión.
- Para escenas complejas, funciona partir el pedido en pasos ordenados.

## Estructura del prompt

Empieza con un verbo claro cuando ayude: genera, edita, transforma, reestiliza, compón,
localiza, visualiza. Describe lo que **debe estar**, no solo lo que hay que sacar.

Sé concreto en lo que importa: sujeto · acción o pose · entorno · composición y encuadre ·
iluminación · sensación de cámara o lente · texturas y materiales · gama de color y estilo ·
tratamiento del texto si lleva palabras.

**Patrón receta**: `[sujeto] + [acción] + [contexto] + [composición] + [estilo]`

**Patrón oración**:
> Un [estilo] [tipo de plano] de [sujeto], [acción o expresión], en [entorno]. La escena está
> iluminada por [luz], creando una atmósfera [ánimo]. Captada con [cámara/lente], destacando
> [texturas y detalles].

Ejemplo:
> Genera un smartwatch premium sobre un pedestal de piedra esculpida. El reloj en tres cuartos
> hacia la cámara, pantalla encendida con un dashboard de fitness minimalista. Escena: set de
> estudio de lujo, fondo grafito apagado. Composición: plano hero cerrado, centrado, con aire
> arriba. Luz: estudio suave con un rim light sutil. Estilo: campaña comercial de alta gama.
> Materiales: titanio cepillado, cristal pulido, piedra mate.

## Texto dentro de la imagen

Lo más importante para las piezas de ALTHIA. Cuanto más pesa la tipografía en el encargo, más
explícito tiene que ser el prompt. No describas las palabras: describe cómo deben verse.

- Cita el texto exacto entre comillas.
- Especifica la jerarquía línea por línea.
- Nombra la fuente o el estilo tipográfico cuando importe.
- Especifica color, tamaño relativo, peso y estilo (bold, itálica, condensada, MAYÚSCULAS).
- Especifica posición, alineación y espaciado.
- Menciona efectos que importen: contorno, sombra, glow, relieve, foil metálico, neón.
- Si hay que localizar a otro idioma, dilo explícitamente.

Plantilla:
> Renderiza este texto exactamente:
> - Línea superior: "[TEXTO]" en [tipografía], [color], [tamaño relativo], [peso/estilo]
> - Línea media: "[TEXTO]" en [tipografía], [color], [tamaño relativo], [peso/estilo]
> - Línea inferior: "[TEXTO]" en [tipografía], [color], [tamaño relativo], [peso/estilo]
>
> Layout: [posición, alineación, espaciado, jerarquía]
> Efectos: [contorno, glow, sombra, acabado metálico…]
> Fondo/escena: [entorno visual]

Localización:
> Genera el mismo póster, pero localiza el texto al inglés preservando la misma jerarquía,
> colores, énfasis y sensación general de diseño.

## Usar referencias

No digas solo "usa estas referencias": explica qué aporta cada una.

> Usando las referencias adjuntas:
> - Referencia 1 aporta [qué aporta]
> - Referencia 2 aporta [qué aporta]
>
> Crea [descripción de la imagen nueva] preservando [elementos específicos] mientras
> [cambios específicos].

## Edición

Sé explícito en las dos mitades: qué cambia y qué se mantiene idéntico.

> Edita la imagen entregada.
> Cambia: [exactamente qué cambiar]
> Mantén: [todo lo demás — sé explícito]
> Integra el cambio de forma imperceptible con la imagen original.

Ejemplo:
> Edita la imagen entregada. Cambia la chaqueta de cuero negro a tweed azul marino. Mantén el
> rostro de la modelo, la pose, el ángulo de cámara, el encuadre, el fondo y la iluminación
> exactamente iguales.

## Composición

Al agregar o combinar elementos, pide verosimilitud física.

> Agrega el frasco de perfume adjunto a la escena de tocador existente. Iguala la perspectiva,
> la escala, la dirección de la luz, los reflejos y la suavidad de las sombras para que parezca
> fotografiado naturalmente en la escena. Deja el resto de la imagen sin cambios.

## Transferencia de estilo

Preserva el contenido salvo que se pidan cambios estructurales.

> Recrea el contenido exacto de la foto callejera entregada en estilo de pintura al óleo
> expresiva. Mantén intactos el trazado de la calle, la posición de las personas, las formas
> de los edificios y la perspectiva. Cambia solo el render: pinceladas gruesas, textura de
> impasto, contraste de color luminoso.

## Rarezas de aspect ratio

- Pedir la proporción solo por texto es poco confiable. Si el conector tiene parámetro
  `aspect_ratio` (Higgsfield), úsalo; si no (ElevenLabs), adjunta una referencia con las
  dimensiones correctas.
- Al editar, el modelo tiende a perder la proporción: agrega explícitamente **"No cambies la
  proporción de la imagen de entrada"**.
- Si subes varias imágenes con proporciones distintas, el modelo adopta la de la última.

## Refinar

Cambios chicos y controlados rinden más que reescribir la escena:

- "Mantén todo lo demás idéntico, pero haz la luz más cálida, tipo golden hour."
- "Mismo sujeto y encuadre, pero cambia el fondo a acero cepillado."
- "Preserva el producto exactamente; rediseña solo el entorno."
- "Mantén la jerarquía y el layout del texto, pero localiza al inglés."

## Qué evitar

- Instrucciones vagas como "hazlo más bonito" o "que quede cool".
- Listas de keywords desconectadas; el spam tipo "8k, masterpiece, trending" no hace nada.
- Abuso de frases negativas.
- Mencionar referencias sin decir para qué sirve cada una.
- Pedir edición sin decir qué debe quedar igual.
- Pedir texto en la imagen sin citar la redacción exacta.
