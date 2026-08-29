# Modelos y mecánica por conector

Los IDs son distintos en cada conector. Copiar el ID de una tabla a la otra da error de
validación, así que confirma en qué conector estás antes de escribir la llamada.

## Nombres comerciales → IDs

| Nombre comercial | Higgsfield | ElevenLabs |
| --- | --- | --- |
| Nano Banana | `nano_banana` | `gemini-2.5-flash-image` |
| Nano Banana 2 | `nano_banana_2` | `gemini-3.1-flash-image` |
| Nano Banana 2 Lite | `nano_banana_2_lite` | `gemini-3.1-flash-lite-image` |
| Nano Banana Pro | `nano_banana_pro` | `gemini-3-pro-image` |

Nano Banana Pro es el de mejor renderizado de texto de la familia. Nano Banana (el original,
Flash) es el más rápido y barato. Nano Banana 2 queda cerca de la calidad Pro a velocidad Flash.

---

## Higgsfield — `mcp__Higgsfield__generate_image`

Todo va dentro de `params`. Ejemplo de una pieza cuadrada con texto:

```json
{
  "params": {
    "model": "nano_banana_pro",
    "prompt": "…",
    "aspect_ratio": "1:1",
    "resolution": "2k",
    "count": 2
  }
}
```

**Parámetros por modelo**

| Modelo | `resolution` | Extras |
| --- | --- | --- |
| `nano_banana` | — | — |
| `nano_banana_2` | `1k` (def), `2k`, `4k` | `is_inpaint` (bool), acepta rol `mask` |
| `nano_banana_2_lite` | `1k` | `thinking`: `MINIMAL` / `HIGH` (def), `is_inpaint`, `mask` |
| `nano_banana_pro` | `1k`, `2k` (def), `4k` | — |

**Proporciones disponibles** (las cuatro familias): `1:1`, `3:2`, `2:3`, `4:3`, `3:4`, `4:5`,
`5:4`, `9:16`, `16:9`, `21:9`. `nano_banana_2` y `nano_banana_2_lite` además aceptan `auto`.

**Referencias**: `medias: [{ "value": "<media_id|job_id>", "role": "image_references" }]`.
El `value` es un UUID de `media_upload_widget` / `media_import_url`, o el `job_id` de una
generación anterior — nunca una URL `https://`.

**Costo y créditos**
- `get_cost: true` → devuelve el costo en créditos sin enviar ningún job.
- `use_unlim`: **omítelo**. Si Franco tiene generaciones ilimitadas de prueba que cubren el
  modelo, el servidor no envía nada y devuelve `unlim_choice`, la pregunta que hay que
  trasladarle antes de gastar. No pongas `true` por tu cuenta para "ahorrarle créditos".
- `count` (1-4) son variantes del **mismo** prompt. Para prompts distintos usa
  `generate_image_batch`, luego `jobs_wait` y una sola llamada a `show_generation_by_ids`.

**Herramientas relacionadas**: `upscale_image` (2K/4K, hay que pasarle width y height reales),
`outpaint_image` (ampliar el encuadre), `reframe` (cambiar proporción), `remove_background`.
Para editar un asset existente son mejores que volver a generar.

---

## ElevenLabs — `mcp__ElevenLabs__creative_generate_image` / `creative_edit_image`

Parámetros al nivel raíz, no dentro de `params`:

```json
{
  "prompt": "…",
  "model_id": "gemini-3-pro-image",
  "context": "para qué se pide",
  "generations_count": 2,
  "flow_id": "…"
}
```

- Devuelve de inmediato con `flow_id`, `node_id`, `session_ids` y una `url` de canvas.
  **No es el resultado final**: hay que sondear `creative_get_flow_run_status` con ese
  `flow_id` y `session_ids`, esperando `poll_after_seconds` entre llamadas, hasta que
  `all_completed` o `has_failures` sea true.
- `generations_count` viene en 4 por defecto y el costo escala con él. Bájalo si Franco no
  pidió varias opciones.
- `estimate_only: true` presupuesta sin generar.
- Sin `flow_id` se crea un flow nuevo. Si la imagen va a alimentar otra generación (video,
  lipsync), llama antes a `creative_create_flow` y reusa ese `flow_id` en todas: nodos de
  flows distintos no se pueden conectar.
- **El aspect ratio no se controla desde `creative_generate_image`** — esa herramienta no
  expone el parámetro, y el nodo arranca en **16:9 a 1K** por defecto. Pedir "formato
  cuadrado 1:1" dentro del prompt **no sirve**: verificado en vivo, devuelve 1376×768 y cobra
  igual. El modelo sí acepta el parámetro; solo hay que llegarle por el nodo:

  ```
  creative_get_model_schema(node_type="image-generation", model_id="gemini-3-pro-image")
  creative_update_node(flow_id, node_id, model_parameters={"aspect_ratio": "1:1",
                                                           "resolution": "2K"}, prompt=...)
  creative_run_flow_nodes(flow_id, node_ids=[node_id], generations_count=N)
  ```

  `aspect_ratio` acepta `auto`, `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`,
  `16:9`, `21:9`; `resolution` acepta `1K`, `2K`, `4K`. Consulta siempre el schema antes:
  los nombres varían entre modelos y un nombre inventado falla la validación.
- Por eso, para una pieza con formato exigido, **Higgsfield es la ruta de menor fricción**:
  ahí `aspect_ratio` y `resolution` van en la misma llamada que genera.
- `creative_get_flow_node_types` lista lo que este workspace puede ejecutar realmente;
  `creative_get_model_guide` trae la guía de prompting de un modelo concreto.
