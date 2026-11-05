# PULSE · Marca y tokens

Valores exactos del diseño final de la web (Claude Design). Los nombres de token son una propuesta: al implementarlos, adáptalos a las convenciones de `@dmaster/ui` sin cambiar los valores. Si una pantalla necesita un valor que no está aquí, se toma de su captura en `../pulse-imagenes` y se añade a este archivo.

## Colores por tipo

Cada tipo conserva su color en todo su recorrido: tarjetas, sesión en vivo, final, historial, gráficos y avisos.

| Token | Intervalos | Running | Fuerza |
|---|---|---|---|
| Sólido (botones, cabeceras, sesión en vivo, final) | `#2457ff` | `#15803d` | `#c2560a` |
| Gradiente de tarjeta destacada y botón primario | `linear-gradient(150deg, #3a6bff 0%, #2457ff 42%, #1a3fcc 100%)` | `linear-gradient(150deg, #178a43 0%, #15803d 45%, #0f5f2d 100%)` | `linear-gradient(150deg, #c2560a 0%, #a84b06 50%, #8a3d00 100%)` |
| Gráficos y puntos · claro | `#2457ff` | `#15803d` | `#e0730a` |
| Gráficos y puntos · oscuro | `#5b80ff` | `#34c46e` | `#f2902f` |
| RGB para sombras y tintes | `36, 87, 255` | `21, 128, 61` | `194, 86, 10` |

Tokens extra que el diseño define para intervalos y fuerza:

| Token | Claro | Oscuro |
|---|---|---|
| Acento de intervalos (enlaces, iconos activos) | `#2457ff` | `#8aa4ff` |
| Tinte de intervalos (fondo de icono, nav activa) | `#eef1ff` | `rgba(91, 128, 255, .16)` |
| Sombra de intervalos | `0 40px 80px -40px rgba(36, 87, 255, .7)` | `0 40px 90px -40px rgba(36, 87, 255, .95)` |
| Texto de fuerza | `#a34a00` | `#f6a85a` |
| Tinte de fuerza | `#fdf0e3` | `rgba(242, 144, 47, .16)` |
| Fondo de tarjeta de fuerza | `linear-gradient(160deg, #fff4e8, #ffffff 60%)` | `linear-gradient(160deg, rgba(242, 144, 47, .16), rgba(255, 255, 255, .03) 60%)` |
| Borde de tarjeta de fuerza | `rgba(194, 86, 10, .14)` | `rgba(242, 144, 47, .26)` |
| Sombra de fuerza | `0 30px 60px -32px rgba(194, 86, 10, .7)` | `0 30px 70px -34px rgba(224, 115, 10, .9)` |

Otros colores con significado:

| Uso | Claro | Oscuro |
|---|---|---|
| Fase de preparación (intervalos) | `#ffb020` | `#ffb020` |
| Récord · fondo | `#fdf0c8` | `rgba(255, 196, 64, .16)` |
| Récord · texto | `#6b4a00` | `#ffd27a` |
| Tendencia positiva | `#116632` | `#5fd68d` |

## Superficies y texto

| Token | Claro | Oscuro |
|---|---|---|
| `bg` fondo de pantalla | `#f4f6f9` | `#06080e` |
| `side` barra lateral | `#ffffff` | `#090c14` |
| `ink` texto principal | `#0e1726` | `#ffffff` |
| `ink-inverse` texto sobre `ink` | `#ffffff` | `#06080e` |
| `sub` texto secundario | `#5a6678` | `rgba(255, 255, 255, .66)` |
| `faint` texto terciario | `#a3acbb` | `rgba(255, 255, 255, .4)` |
| `card` tarjeta | `#ffffff` | `rgba(255, 255, 255, .05)` |
| `card-border` | `rgba(14, 23, 38, .06)` | `rgba(255, 255, 255, .09)` |
| `card-shadow` | `0 1px 2px rgba(14, 23, 38, .04), 0 18px 40px -28px rgba(14, 23, 38, .3)` | `none` |
| `line` separadores | `rgba(14, 23, 38, .07)` | `rgba(255, 255, 255, .08)` |
| `pill` fondo de píldora | `#f4f6f9` | `rgba(255, 255, 255, .07)` |
| `segment-track` | `#e9edf2` | `rgba(255, 255, 255, .08)` |
| `segment-on` | `#ffffff` | `rgba(255, 255, 255, .16)` |
| `segment-shadow` | `0 1px 4px rgba(14, 23, 38, .12)` | `none` |
| `input-border` | `#d5dae3` | `rgba(255, 255, 255, .16)` |
| `dash` bordes discontinuos | `#c9ced8` | `rgba(255, 255, 255, .22)` |
| `track` barras de progreso vacías | `#e9edf2` | `rgba(255, 255, 255, .10)` |
| `nav-active` | `#eef1ff` | `rgba(255, 255, 255, .08)` |
| `photo` hueco de foto de ejercicio | `linear-gradient(160deg, #eef1f6, #e2e7ee)` | `linear-gradient(160deg, rgba(255, 255, 255, .10), rgba(255, 255, 255, .03))` |
| `today-ring` día actual en calendario | `inset 0 0 0 1.5px #0e1726` | `inset 0 0 0 1.5px #ffffff` |

## Tema

- Tres opciones: automático (sigue `prefers-color-scheme`), claro y oscuro.
- La sesión en vivo de intervalos, running y fuerza guiada va siempre a todo color, con el sólido o el gradiente de su tipo y texto blanco, sea cual sea el tema.
- La libreta de fuerza sigue el tema.

## Tipografía

- **Figtree**, peso 800, para títulos, cifras, cronómetros y botones primarios (pesos usados: 300–900).
- **Texto:** pila del sistema: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', system-ui, sans-serif`.
- Escala observada en la web:
  - Títulos de pantalla en escritorio: 44–46 px. Títulos grandes de tarjeta: 34–38 px. Secciones: 22–28 px. Títulos de tarjeta: 17–20 px.
  - Texto: 16 px en campos, y 15, 14 y 13 px en contenido y metadatos. 12 px en etiquetas pequeñas.
  - Etiquetas superiores en mayúsculas: 11–13 px, peso 800, con espaciado de letras.
- Figtree no está todavía en el proyecto. Añadirla (autoalojada) es un recurso nuevo: pide autorización antes.

## Formas

- Botones principales tipo píldora: 54–56 px de alto y radio 27–28 px. El primario lleva el gradiente y la sombra de su tipo, con texto blanco en Figtree 18 px 800. El secundario oscuro usa `ink` de fondo y `ink-inverse` de texto.
- Campos de formulario: 54 px de alto, radio 16 px y borde de 1,5 px con `input-border`.
- Tarjetas y hojas: radio 22–28 px. Tarjetas pequeñas, chips y miniaturas: 12–14 px. Barras de gráficos: 3–6 px.
- Desenfoque: `backdrop-filter: blur(24px) saturate(160%)` solo en la barra de pestañas y la cabecera del móvil, como en las capturas.

## Movimiento

- Entrada de contenido (`rise`): de `opacity: 0; translateY(16px)` a reposo, 0,4–0,6 s, con escalonado entre elementos de una lista.
- Modales (`modal`): de `opacity: 0; translateY(20px) scale(.97)` a reposo, 0,35–0,4 s, curva `cubic-bezier(.2, .9, .1, 1)`.
- Confirmaciones (`pop`, por ejemplo el check del final de sesión): escala .5 → 1,08 → 1 en 0,6 s.
- Barras de gráficos (`grow`): `scaleY` de 0 a 1.
- Indicador en directo (`live`): opacidad 1 → .35 → 1 en bucle.
- Todo con `transform` y `opacity`. Con `prefers-reduced-motion: reduce`, duraciones y retardos a 0.

## Anchos de referencia

| Vista | Ancho | Capturas |
|---|---|---|
| Escritorio | 1440 px | `02-web/*-escritorio-*` |
| Tablet | 834 px | `02-web/*-tablet-y-movil-*` (archivos `-tablet`) |
| Móvil | 390 px | `02-web/*-tablet-y-movil-*` y `*-movil-*` (archivos `-movil`) |

## Logo e icono

- Logotipo: tres barras redondeadas alineadas abajo, con alturas del 58 %, 100 % y 76 %. La central va en el color de acento y las laterales en un tono secundario. Al lado, «pulse» en minúsculas, Figtree 800, `letter-spacing: -.035em`.
- Icono principal «Cobalto»: barras blancas sobre `#2457ff`. Alternativos: Noche, Claro y Tres tipos. Para el icono, la referencia es su captura en `01-ios/23-app-store-icono-pantalla-de-arranque-y-capturas`.
