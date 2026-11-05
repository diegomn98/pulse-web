# PULSE

Plataforma de entrenamiento personalizable: intervalos/HIIT, running y fuerza. PWA mobile-first, local-first, con Supabase como backend futuro.

Proyecto nuevo, construido desde cero sobre el diseño final aprobado. Repositorios hermanos: `pulse-landing` (landing de lista de espera) y `pulse-supabase` (backend común, reglas compartidas y tipos).

## Fuentes de verdad

- **Aspecto:** las capturas de `../pulse-imagenes`. La web está en `02-web` (escritorio 1440, tablet 834 y móvil 390, en claro y oscuro). Índice en `../pulse-imagenes/INDICE.md`. Los estados que solo existen en iOS (`01-ios`) se adaptan al layout de la web.
- **Comportamiento:** `docs/PRODUCT_SPEC.md`: reglas `RN-xx`, módulos `M1`–`M14`, modelo de datos, navegación, web y paridad con iOS. No la cargues entera: consulta la sección que necesite la tarea.
- **Tokens visuales:** `docs/BRAND.md`, con los valores exactos del diseño.
- **Estructura:** `docs/ARCHITECTURE.md`.
- **Reglas compartidas con iOS:** definición exacta en `../pulse-supabase/docs/RULES.md` y casos de prueba en `../pulse-supabase/rules/fixtures/`. Si difieren de `docs/PRODUCT_SPEC.md`, manda `RULES.md`.
- Orden de prioridad si se contradicen: este archivo → capturas y `docs/PRODUCT_SPEC.md` → `docs/BRAND.md`. Si encuentras una contradicción, no la resuelvas por tu cuenta: anótala en `docs/STATUS.md` y pregunta.
- No inventes textos, datos, pantallas ni estados. Los textos de UI son los de las capturas, salvo la terminología fijada en este archivo.
- Al hablar de una pantalla, cita su módulo y su componente de diseño (por ejemplo, M3 · `WebAppInicio`) para poder cruzarla con su captura.

## Autoría (obligatorio, sin excepciones)

- Diego es el único autor visible del proyecto.
- Nunca escribas referencias a Claude, Claude Code, Anthropic, IA, "asistente", "agente", "generado automáticamente" o similares en código, comentarios, commits, PRs, README, documentación, changelogs, `package.json`, metadatos, nombres de archivos o ramas.
- Commits sin trailers `Co-Authored-By`, sin "Generated with" y sin enlaces de sesión.
- Commits en inglés con Conventional Commits (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`), pequeños y centrados en un cambio.
- No hagas push, no abras PRs y no reescribas historial sin que te lo pida.

## Comentarios en el código

- Por defecto, ninguno. El código se explica con nombres claros y funciones pequeñas.
- Solo se permite un comentario si explica un porqué no evidente (una limitación del navegador, una decisión de rendimiento). Nunca para narrar qué hace el código.
- Prohibido: comentarios que mencionen fases, pasos, tareas, prompts, módulos del diseño o capturas, código comentado y TODOs sueltos. Lo pendiente va a `docs/STATUS.md`.

## Convenciones de código y estructura

- Sigue la guía de estilo oficial de Angular para la versión instalada (nombres de archivo, sufijos, un concepto por archivo). Consúltala; no la supongas.
- Cada archivo tiene un sitio claro según `docs/ARCHITECTURE.md`. Si dudas de dónde va algo, pregunta antes de crearlo.
- Los tests unitarios van junto al archivo que prueban, con el mismo nombre y extensión `.spec.ts`. No hay specs sin una unidad propia bajo prueba.
- No se testean librerías de terceros por sí solas; se testea nuestro código que las usa.
- Los E2E van en `e2e/`, organizados por flujo de usuario.
- Prohibidos en `src/`: archivos o rutas de demo, ejemplo, prueba, smoke o temporales. Lo exploratorio va fuera de `src/` y se borra al terminar.
- Archivos en kebab-case y en inglés. Prohibidos nombres genéricos como `utils.ts`, `helpers.ts`, `common.ts` o `misc.ts`: cada archivo se nombra por su responsabilidad.
- Alias de importación (`@core`, `@shared`, `@features`, `@domain`, `@data`) en lugar de rutas relativas largas. Sin barrels `index.ts` salvo que haya una API pública justificada.
- Sin código muerto, exports sin usar, `console.log` ni configuraciones que no se utilicen.
- Los nombres de los tests describen el comportamiento esperado, con un comportamiento por test.
- Antes de dar algo por terminado, revisa el diff completo como si fueras un revisor senior de un PR en una empresa exigente y corrige lo que no pasaría la revisión.

## Stack

- Angular estable más reciente, TypeScript strict, standalone components, Signals, zoneless, Router con lazy loading, SCSS.
- `@dmaster/ui` para componentes generales. Componentes propios de PULSE solo cuando la librería no cubra el caso.
- PWA con service worker. Persistencia local: IndexedDB con esquema versionado y migraciones.
- Tests: Vitest (unitarios e integración) y Playwright (E2E, accesibilidad y regresión visual).
- Supabase (PostgreSQL, Auth, Storage, Edge Functions) en fases posteriores. Nada de NestJS, Express ni otra API.
- No añadas dependencias ni recursos externos (incluida la fuente Figtree) sin autorización expresa. Para proponer una, justifícala y verifica su licencia y mantenimiento.

## @dmaster/ui

- Es la librería de Diego: repo `github.com/diegomn98/dmaster-ui`, docs `dmasterui.com`.
- Antes de usar un componente, verifica su API real en `node_modules/@dmaster/ui` (tipos `.d.ts`) o en la documentación. No inventes componentes, inputs, outputs ni tokens.
- Los tokens de `docs/BRAND.md` se aplican a través del sistema de temas de la librería si lo permite. Si detectas una carencia que encajaría en la librería, regístrala en `docs/STATUS.md` como propuesta en lugar de parchearla desde PULSE.

## Arquitectura

- La estructura, las capas y las reglas de dependencia están en `docs/ARCHITECTURE.md`. Resumen: componentes → servicios de aplicación → contratos de repositorio → adaptador IndexedDB. Supabase será otro adaptador; nunca se consulta desde componentes.
- Modelos de dominio independientes de Angular y de Supabase. Separar definición de rutina, sesión en curso, resultado histórico y preferencias. El modelo de datos de `docs/PRODUCT_SPEC.md` (sección 3) es una referencia de campos, no un esquema cerrado.
- Las reglas de negocio (`RN-xx`: lo que toca, semana de lunes a domingo, objetivo semanal, racha, récords) viven en el dominio como funciones puras con tests, nunca en componentes. Solo se implementan las que `RULES.md` marca como decididas, y sus tests ejecutan las fixtures compartidas, copiadas con `npm run sync:rules`.
- Crear interfaces solo donde haya un punto real de sustitución. Nada de abstracciones especulativas.
- IDs estables (`crypto.randomUUID()`), timestamps en UTC (ISO 8601), fechas mostradas en la zona del usuario.
- Textos de UI en español, preparados para inglés. Identificadores de código en inglés.
- Terminología: «Recuperación final», no «vuelta a la calma», aunque alguna captura diga lo segundo.

## Motor de intervalos

- TypeScript puro, sin dependencias de Angular ni del DOM, testeable en aislamiento.
- El estado se deriva de marcas temporales reconciliables, nunca de contar ticks de `setInterval`. `requestAnimationFrame` solo para renderizar.
- Soporta start, pause, resume, skip, previous, restart, stop y complete, y se recupera tras cambios de visibilidad, suspensión y recarga.
- Plantilla inicial: 7 ejercicios, 2 rondas, 30 s de actividad, 20 s de descanso, 20 s de preparación por ronda. Sin descanso tras el último ejercicio de cada ronda; la preparación de la ronda 2 actúa como transición. Total: 700 s (11:40). Esto debe ser un test.
- El relleno de color de la pantalla de sesión se calcula con el progreso que da el motor; las animaciones nunca influyen en el cálculo del tiempo.

## Diseño y motion

- Las capturas son la referencia visual. No añadas decoración, efectos, iconos ni etiquetas que no estén en ellas, y no quites los que sí están.
- Gradientes, sombras de color y desenfoque solo los definidos en `docs/BRAND.md` (gradiente y sombra de cada tipo, desenfoque en la barra de pestañas y la cabecera del móvil). Nada más de eso.
- Cada tipo conserva su color en todo su recorrido: intervalos `#2457ff`, running `#15803d` y fuerza `#c2560a`.
- Tema automático, claro u oscuro mediante design tokens. La sesión en vivo de intervalos y fuerza guiada va siempre a todo color; la libreta de fuerza sigue el tema.
- Cada animación debe comunicar progreso, confirmar una acción, explicar una transición o dirigir la atención. Si no, se elimina. Las de referencia están en `docs/BRAND.md`.
- Animar solo `transform` y `opacity` siempre que sea posible. Respetar `prefers-reduced-motion`.
- El cronómetro debe leerse a varios metros. Las fases nunca se comunican solo con color o sonido.
- Accesibilidad objetivo: WCAG 2.2 AA. Targets táctiles de 44 px como mínimo y safe areas. Si una captura choca con esto, prevalece la accesibilidad y se anota en `docs/STATUS.md`.

## Skills de diseño

- Instaladas: `impeccable`, `emil-design-eng` y `design-taste-frontend`.
- La dirección de arte ya está fijada por el diseño. Uso:
  - `design-taste-frontend` solo para resolver algo que no esté en las capturas, siempre coherente con `docs/BRAND.md`.
  - `emil-design-eng` para animaciones, microinteracciones y detalle de componentes.
  - `impeccable` para auditar y pulir pantallas construidas, comparándolas con su captura.
- Si se contradicen, prevalece el orden de «Fuentes de verdad».
- Ninguna skill autoriza a añadir dependencias (por ejemplo GSAP), descargar imágenes o recursos externos ni crear archivos que no se hayan pedido.
- No instales ni crees skills nuevas por tu cuenta. Si crees que hace falta alguna, pregunta antes explicando qué es, de dónde sale, qué hará en el proyecto y qué alternativa habría sin ella.

## Calidad antes de dar algo por terminado

1. Compila sin errores ni warnings nuevos.
2. Tests relevantes ejecutados y en verde.
3. Lint limpio.
4. Para cambios visuales: capturas con Playwright a 375, 390, 834 y 1440 px, en light y dark. Compara 390, 834 y 1440 con su captura de referencia y enumera las diferencias que queden.
5. Estados de carga, vacío y error implementados (los vacíos de las capturas, donde existan).
6. Sin botones inoperativos ni placeholders presentados como funcionalidad.
7. `docs/STATUS.md` actualizado al cerrar cada bloque.

Nunca afirmes que algo está probado si no lo has ejecutado. Si algo falla y no lo resuelves, dilo claramente.

## Alcance aplazado

- **Running:** aplazado hasta decidir cómo se hace en la web (`docs/decisions/0001-running-en-la-web-aplazado.md`). Mientras tanto no se ofrece crear, editar ni empezar rutinas de running ni carrera libre, y el onboarding ofrece solo intervalos y fuerza. El tipo existe en el dominio para no tener que migrar después.
- **Lo que depende de Supabase o de la app de iOS** no se implementa hasta que exista, y mientras tanto no se muestra (`docs/decisions/0002-cuenta-y-sincronizacion-tras-supabase.md`): el paso «Guarda tu plan» del onboarding, registro, inicio de sesión, recuperación, «Hola de nuevo», cambiar contraseña, cerrar sesión y eliminar cuenta, sincronización y su aviso «Sin conexión», notificaciones, Apple Salud y «Empezar en el móvil». La app funciona sin cuenta, como con «Ahora no».
- El aviso y las preferencias de cookies se implementan cuando haya algo que consentir. Las páginas legales llegan con la cuenta.

## Supabase y entornos

- En esta etapa no se conecta Supabase. No inventes credenciales ni variables de entorno.
- El esquema, las funciones y los tipos viven en `pulse-supabase`. Aquí nunca se crean migraciones ni funciones; cuando llegue Supabase, los tipos se copian con un script, igual que las fixtures.
- Nunca ejecutes operaciones remotas, despliegues ni cambios en producción sin autorización explícita.
- Nunca pongas claves secretas (`sb_secret_…` o la heredada `service_role`) en el frontend; solo la publicable (`sb_publishable_…`).

## Decisiones abiertas

No las resuelvas por tu cuenta. Cuando una tarea dependa de ellas, pregunta y registra la respuesta como ADR en `docs/decisions/`:

- Días sin entrenar que activan la propuesta de «vuelta tras un parón» (RN-04).
- Desempate de «lo que toca» y cómo entran las rutinas sin probar (RN-02).
- Fórmula del 1RM estimado y criterio de récord (RN-21).
- Cómo el esfuerzo percibido ajusta las propuestas (RN-20).
- Origen y contenido del catálogo de ejercicios (nombres, grupos, material, pasos y fotos).
- Resto de puntos de la sección 11 de `docs/PRODUCT_SPEC.md`.

## Documentación del proyecto

- `docs/PRODUCT_SPEC.md`: especificación de producto y del diseño final (no la modifiques salvo que se pida).
- `docs/BRAND.md`: identidad visual y tokens.
- `docs/ARCHITECTURE.md`: arquitectura, capas y reglas de dependencia.
- `docs/decisions/`: decisiones importantes en formato ADR breve.
- `docs/STATUS.md`: estado real, plan por fases, pendientes, riesgos y contradicciones detectadas. Se actualiza al cerrar cada bloque de trabajo.
- `../pulse-imagenes`: capturas del diseño e índice (fuera del repo; no se copian a `src/` ni se versionan).
- `../pulse-supabase`: backend común, reglas compartidas (`docs/RULES.md`) y sus fixtures.

## Comandos

Si alguno todavía no existe, se crea tal como se describe aquí durante la puesta en marcha.

- `npm start`: servidor de desarrollo en `http://localhost:4200`.
- `npm run build`: build de producción en `dist/pulse`.
- `npm run lint`: ESLint (angular-eslint + eslint-config-prettier) sobre `src/`, `e2e/` y `playwright.config.ts`.
- `npm run format`: Prettier sobre todo el proyecto (respeta `.prettierignore`).
- `npm test`: tests unitarios con Vitest (builder oficial `@angular/build:unit-test`), una sola ejecución.
- `npm run test:watch`: Vitest en modo watch.
- `npm run test:coverage`: Vitest con cobertura v8 en `coverage/`, sin umbrales.
- `npm run e2e`: Playwright (Chromium y WebKit a 375, 390, 834 y 1440 px) contra su propio servidor en el puerto 4300, para no chocar con `npm start`. Incluye el proyecto `pwa-chromium`, que construye la app y la sirve en el puerto 4400 para probar el service worker y el modo sin conexión.
- `npm run check`: lint + test + build. Debe pasar antes de cada commit.
- `npm run sync:rules`: copia `../pulse-supabase/rules/fixtures/` a `test-fixtures/shared-rules/`, que se versiona en este repositorio.
- Un solo test unitario: `npx ng test --watch=false --include src/app/ruta/archivo.spec.ts`.
- Un solo proyecto E2E: `npx playwright test --project=chromium-390`.
