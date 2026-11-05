# PULSE · Especificación de producto

Funcionamiento completo de PULSE según el diseño final aprobado (web, iOS y landing). Describe reglas, datos, navegación, pantallas e integraciones. No la cargues entera: consulta la sección que necesite cada tarea.

## Cómo usar este documento

- **Comportamiento:** las reglas `RN-xx` (sección 2) y los módulos `M1`–`M14` (sección 5) son la referencia de lo que hace cada pantalla.
- **Aspecto:** las capturas de `../pulse-imagenes` mandan sobre cualquier descripción de este texto. Su índice está en `../pulse-imagenes/INDICE.md` (y en `indice.json`). La web está en `02-web`, iOS en `01-ios` y la landing en `03-landing`.
- **Nombres de componente:** `Main`, `WebAppInicio`, `P_Libreta`… son los nombres de las pantallas en el diseño y en el índice de capturas. En la web, cada pantalla tiene variante oscura (sufijo `Oscuro`) y, según el caso, de tablet (`Tablet`) y móvil (`Movil`).
- **Tokens visuales:** valores exactos en `docs/BRAND.md`.
- **Alcance de este repositorio:** la app web. Lo que depende de Supabase o de la app de iOS, y el running, se aplazan según `CLAUDE.md` y `docs/decisions/`. La landing vive en su propio repositorio.
- **Lo que el diseño no fija** está marcado como *por definir* y recogido en la sección 11. No se inventa: se decide y se registra en `docs/decisions/`.
- **Terminología del proyecto:** donde las capturas dicen «Vuelta a la calma», la app usa «Recuperación final».

## 1. Alcance y glosario

PULSE es una app de entrenamiento con tres tipos de sesión (intervalos, running y fuerza) que propone cada día lo que toca y guía durante la sesión. Este documento cubre la app de iPhone, la app web, la landing de lista de espera y las piezas de lanzamiento.

### Plataformas

| Plataforma  | Estado del diseño                                                 | Tecnología prevista                     | Notas                                                      |
|-------------|-------------------------------------------------------------------|-----------------------------------------|------------------------------------------------------------|
| iPhone      | Completo, claro y oscuro                                          | App nativa (SwiftUI, iOS 26)            | Apple Salud, Live Activity, Dynamic Island, widgets y Siri |
| Web         | Completo: escritorio 1440, tablet 834 y móvil 390, claro y oscuro | Angular + @dmaster/ui, backend Supabase | Misma estructura que iOS; running se inicia en el móvil    |
| Landing     | Completa: escritorio y móvil, claro y oscuro                      | Web                                     | Lista de espera, cookies y textos legales                  |
| Android     | Para más adelante                                                 | Por definir                             | Adaptación pendiente                                       |
| Apple Watch | Para más adelante                                                 | Por definir                             | Hoy el pulso llega del Watch a través de Apple Salud       |

### Glosario

| Término          | Significado                                                                                                                                               |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rutina           | Entrenamiento guardado de un tipo (intervalos, running o fuerza) con su estructura. Se crea desde cero, desde una plantilla o guardando un entreno libre. |
| Sesión           | Una ejecución de una rutina o de un entreno libre, con sus datos registrados.                                                                             |
| Entreno libre    | Sesión sin rutina previa: fuerza (añadir ejercicios sobre la marcha), intervalos libres (el usuario marca los cambios) o carrera libre (sin objetivo).    |
| Tramo            | Unidad de tiempo o distancia dentro de una sesión: trabajo, descanso, preparación, calentamiento, serie de carrera, recuperación o recuperación final.     |
| Ronda            | Repetición completa de la lista de ejercicios de una rutina de intervalos.                                                                                |
| Serie            | En fuerza, un bloque de repeticiones con un peso. En running, cada repetición de un tramo dentro de un bloque «Repetir N ×».                              |
| Lo que toca      | La rutina que la app propone hoy en Inicio.                                                                                                               |
| Objetivo semanal | Número de sesiones que el usuario quiere hacer cada semana (de lunes a domingo).                                                                          |
| Racha            | Semanas seguidas cumpliendo el objetivo semanal.                                                                                                          |
| Volumen          | En fuerza, kilos totales levantados (reps × peso sumados).                                                                                                |
| 1RM estimado     | Peso máximo estimado para una repetición de un ejercicio, calculado a partir de las series registradas.                                                   |
| Libreta / Guiada | Las dos vistas de una sesión de fuerza: tabla de todas las series o una serie cada vez.                                                                   |

## 2. Reglas de funcionamiento

Reglas de negocio que se repiten en varias pantallas. Cada una lleva un identificador para poder referenciarla.

### Tipos y propuesta diaria

**RN-01.** **Tres tipos con color fijo.** Intervalos azul `#2457ff`, Running verde `#15803d` y Fuerza naranja `#c2560a`. El color acompaña al tipo en tarjetas, sesión en vivo, final, historial, gráficos y widgets.

**RN-02.** **Lo que toca.** Inicio propone la rutina que más tiempo lleva sin hacerse y muestra el motivo («La que más tiempo lleva esperando: 5 días»). No hay días fijos: si no se hace, sigue propuesta al volver.

**RN-03.** **Cambiar la propuesta.** El botón secundario de la tarjeta permite elegir otra rutina para hoy.

**RN-04.** **Vuelta tras un parón.** Tras varios días sin entrenar se propone la rutina más corta («Llevas 12 días sin entrenar: volvemos con la más corta»). El umbral de días está *por definir*.

**RN-05.** **Hecho hoy.** Si ya se ha entrenado hoy, Inicio muestra la sesión hecha y una propuesta opcional que la complementa («Run/walk suave: complementa la fuerza de hoy»).

**RN-06.** **Plan inicial.** El onboarding crea una rutina por cada tipo elegido y propone un orden para la primera semana (en el ejemplo: Intervalos 7 × 2, Fuerza tren superior, Tabata y Fuerza tren inferior).

### Objetivo semanal y racha

**RN-07.** **Objetivo en sesiones.** Entre 2 y 7 sesiones por semana (4 en el diseño). Cuentan sesiones, no días ni minutos: dos el mismo día suman dos.

**RN-08.** **Semana de lunes a domingo.** El progreso semanal se reinicia cada lunes.

**RN-09.** **Sesiones de más.** Por encima del objetivo cuentan para el progreso, pero no adelantan la semana siguiente.

**RN-10.** **Racha.** Suma una semana por cada semana con el objetivo cumplido. Se muestra la racha actual y la mejor. Al romperse, Inicio muestra «Empieza otra racha».

### Sesiones

**RN-11.** **Qué cuenta como sesión.** Una sesión terminada, completa o con «Terminar y guardar» desde la pausa (cuenta con lo hecho hasta ese momento). «Salir sin guardar» no registra nada.

**RN-12.** **Se guarda sola.** La sesión en curso sobrevive a salir de la app y sigue en la Live Activity y la Dynamic Island.

**RN-13.** **Entreno libre a rutina.** Los tres entrenos libres ofrecen al terminar «¿Lo guardas como rutina?» con nombre editable; «Ahora no» descarta solo la rutina, no la sesión.

**RN-14.** **Patrón de intervalos libres.** Se detecta el número de tramos y los tiempos medios de trabajo y descanso y se propone una rutina redondeada (p. ej. 8 × 40 s / 20 s). «Redondear a tiempos fijos» es opcional; si se quita, se guardan los tiempos exactos. Los tramos con nombre pasan como ejercicios y el resto como «Trabajo».

**RN-15.** **La última vez.** En fuerza se precargan las repeticiones y pesos de la vez anterior (columna «Anterior» y «La última vez»).

**RN-16.** **Descanso automático.** En fuerza arranca al marcar una serie como hecha, con la duración de la rutina (1:30 en el diseño). Se puede alargar (+15 s en web), saltar («Empezar ya») o desactivar.

**RN-17.** **Máquina ocupada.** «Hacerlo luego» mueve el ejercicio al final de la sesión. Si no, se sustituye por otro del mismo grupo muscular, filtrable por material, con alcance «Solo hoy» o «Siempre en la rutina».

**RN-18.** **Avisos de ritmo.** En running, si el ritmo se sale del margen del tramo (± 10 s/km en el diseño) se avisa con voz y vibración.

**RN-19.** **Sin ubicación.** Se puede correr «solo con el tiempo», sin distancia, ritmo, avisos de ritmo ni ruta.

**RN-20.** **Esfuerzo percibido.** Al final se elige Suave, Bien, Dura o Al límite; se usa para ajustar lo que se propone. El ajuste concreto está *por definir*.

**RN-21.** **Récords.** Se detectan por ejercicio y se marcan en la libreta, el final, el detalle y la ficha. El 1RM estimado se muestra en la ficha y en Progreso; la fórmula está *por definir*.

**RN-22.** **Comparación con la última vez.** El final compara con la sesión anterior de la misma rutina (tres métricas según el tipo).

### Cuenta, datos y preferencias

**RN-23.** **Cuenta opcional.** Sin cuenta los datos se quedan en el dispositivo; con cuenta se sincronizan entre dispositivos. La cuenta se puede crear más tarde desde Perfil.

**RN-24.** **Acceso.** Apple, Google o email y contraseña. Contraseña con requisitos visibles: 8 o más caracteres, letra y número, y mayúscula, con medidor de seguridad. Con Apple o Google la contraseña se gestiona en ese proveedor.

**RN-25.** **Recuperación.** Enlace por email que caduca en 1 hora; reenvío tras una cuenta atrás; opción de usar otro email.

**RN-26.** **Tema.** Automático (sigue el sistema), claro u oscuro. La sesión en vivo de intervalos, running y fuerza guiada va siempre a todo color; la libreta de fuerza sigue el tema; bienvenida y acceso siguen el sistema.

**RN-27.** **Unidades.** kg o lb.

**RN-28.** **Notificaciones.** Cuatro tipos opcionales: recordatorio de lo que toca (uno al día, a la hora elegida), resumen semanal (cada lunes), rachas y objetivo (cuando falta una sesión) y récords nuevos. Nunca publicidad.

**RN-29.** **Exportar e importar.** Exportar JSON (copia completa: sesiones, rutinas, récords y ajustes; importable) o CSV (sesiones). Importar añade las sesiones que no existan y no borra nada.

**RN-30.** **Eliminar cuenta.** Borra historial, sesiones, rutinas, récords y la cuenta. Lo que ya está en Apple Salud se queda en el iPhone. Requiere escribir ELIMINAR y confirmar en una alerta. Ofrece descargar una copia antes.

**RN-31.** **Ejercicios propios.** Se crean con nombre, grupo muscular y material, y se pueden usar en cualquier rutina.

**RN-32.** **Web y running.** Las sesiones de running se hacen con el GPS del móvil: desde la web se envía un aviso al iPhone y la rutina queda lista en su Inicio.

**RN-33.** **Web sin conexión.** Se puede seguir entrenando; se guarda en el dispositivo y se sincroniza al volver la conexión.

## 3. Modelo de datos

Entidades y campos que aparecen en las pantallas. Es una propuesta derivada del diseño: los nombres son orientativos y el esquema final se decide al desarrollar.

### Usuario y preferencias

| Campo                                               | Valores                                                   | Dónde se usa                                                                |
|-----------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------|
| nombre, email                                       | texto                                                     | Perfil, saludo de Inicio, correos                                           |
| metodoAcceso                                        | apple · google · email · sin cuenta                       | Acceso, Cambiar contraseña                                                  |
| objetivoSemanal                                     | entero 2–7                                                | Onboarding, Inicio, Final, Perfil                                           |
| unidades                                            | kg · lb                                                   | Fuerza, Progreso                                                            |
| tema                                                | automatico · claro · oscuro                               | Perfil › Apariencia                                                         |
| iconoApp (iOS)                                      | cobalto · noche · claro · tres_tipos                      | Perfil › Icono de la app                                                    |
| idioma                                              | es (por defecto)                                          | Perfil                                                                      |
| sesion.sonidos / voz / hapticos / pantallaEncendida | booleanos                                                 | Perfil › Durante la sesión (en web, atajos de teclado en lugar de hápticos) |
| notificaciones                                      | recordatorio (+ hora) · resumenSemanal · rachas · records | Perfil › Notificaciones                                                     |
| permisos                                            | salud · ubicacion · notificaciones (estado del sistema)   | Pantallas de permiso                                                        |
| sincronizadoEn                                      | fecha y hora                                              | Cabecera de Perfil («Sincronizado hace un momento»)                         |

### Rutina

| Campo            | Valores                               | Notas                                               |
|------------------|---------------------------------------|-----------------------------------------------------|
| tipo             | intervalos · running · fuerza         | Fija el color y el editor                           |
| nombre           | texto                                 | Editable en el editor y al guardar un entreno libre |
| origen           | en blanco · plantilla · entreno libre | «Hoy, desde un entreno libre»                       |
| duracionEstimada | minutos                               | «≈ 45 min», «11:40»                                 |
| ultimaVez        | referencia a sesión                   | «Hace 5 días · 2 de 2 rondas», «Sin probar todavía» |
| estructura       | según tipo (abajo)                    |                                                     |

| Tipo       | Estructura                                                                                                                                                                                                                                                     |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Intervalos | modo (libre · guiado con ejercicios) · trabajo (s) · descanso (s) · rondas · preparación por ronda (s) · ejercicios\[\] · calentamiento · recuperación · voz y sonidos                                                                                         |
| Running    | bloques\[\]: calentamiento (tiempo, intensidad) · repetir {n, tramos\[\]} · tramo {medida: distancia o tiempo, valor, ritmoObjetivo (min/km), avisoDesvio (± s/km)} · recuperación {caminar, trotar o parado; tiempo} · recuperación final (tiempo, intensidad) |
| Fuerza     | ejercicios\[\]: {ejercicio, carga (con carga · peso corporal · lastre), medida (reps · tiempo), series\[\] {reps, peso}, descanso (s)} · descansoAutomatico (booleano)                                                                                         |

### Ejercicio

| Campo          | Valores                                                                                           |
|----------------|---------------------------------------------------------------------------------------------------|
| nombre, media  | texto · foto o vídeo                                                                              |
| grupo          | Pecho · Espalda · Piernas · Hombros · Brazos · Core · Cuerpo entero (180 ejercicios en el diseño) |
| material       | Barra · Mancuernas · Polea · Máquina · Sin material · Anillas · Paralelas… («Barra y banco»)      |
| musculos       | lista («pecho, tríceps, hombro»)                                                                  |
| pasos, consejo | «Cómo se hace» en pasos numerados y aviso de seguridad; consejo corto en intervalos («Cae suave») |
| alternativas   | ejercicios con los mismos músculos («Si el banco está ocupado»)                                   |
| propio         | booleano (creado por el usuario)                                                                  |

### Sesión y registros

| Entidad            | Campos                                                                                                                                                                                                           |
|--------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sesión             | id · rutina (vacía si es libre) · tipo · inicio · fin · duración sin pausas · estado (completa · terminada antes) · esfuerzo (suave · bien · dura · al límite) · fcMedia · fcMax · kcalActivas · guardadaEnSalud |
| Serie (fuerza)     | ejercicio · número · reps · peso · hecha · esRécord · nota (web: «Añadir una nota…»)                                                                                                                             |
| Tramo (intervalos) | tipo (trabajo · descanso · preparación) · duración · nombre/ejercicio                                                                                                                                            |
| Carrera (running)  | distancia · ritmo por serie y medio · ruta GPS · vueltas\[\] {distancia, ritmo} · kilómetros\[\]                                                                                                                 |
| Récord             | ejercicio · peso × reps · fecha · sesión                                                                                                                                                                         |
| Semana (derivado)  | lunes de inicio · sesiones · objetivo · cumplida                                                                                                                                                                 |

## 4. Navegación

Cómo se llega a cada pantalla. En iOS hay una barra de tres pestañas; en web, una barra lateral en escritorio y tablet y una barra de pestañas en móvil.

### Estructura

| Nivel           | iOS                                                                                                               | Web                                                                                                   |
|-----------------|-------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Raíz            | Pestañas Inicio · Entrenar · Progreso                                                                             | Barra lateral Inicio · Entrenar · Progreso y botón «Nuevo entrenamiento»; en móvil, barra de pestañas |
| Perfil          | Avatar arriba a la derecha → Perfil (se cierra con Listo)                                                         | Tarjeta «Perfil y ajustes» abajo en la barra lateral                                                  |
| Segmentos       | Entrenar: Mis rutinas · Ejercicios. Progreso: Resumen · Historial                                                 | Igual                                                                                                 |
| Hojas y modales | Vista rápida, menú contextual, Nuevo entrenamiento, Añadir a una rutina, Pausa, Máquina ocupada, Añadir ejercicio | Panel lateral de detalle, modales de Nuevo entrenamiento, Añadir, Empezar en el móvil y Cookies       |
| Sesión en vivo  | Pantalla completa, fuera de las pestañas                                                                          | Pantalla completa, con atajos de teclado                                                              |

### Recorridos principales

| Desde                   | Acción                                  | Lleva a                                                                                             |
|-------------------------|-----------------------------------------|-----------------------------------------------------------------------------------------------------|
| Bienvenida              | Empezar · Ya tengo cuenta               | Onboarding · Iniciar sesión                                                                         |
| Onboarding              | Continuar en cada paso (o Saltar)       | Tipos → Objetivo → Plan → Guarda tu plan → Salud y avisos → Inicio «Tu plan está listo»             |
| Inicio                  | Empezar · Ver rutina · botón cambiar    | Sesión del tipo · Vista rápida · elegir otra rutina                                                 |
| Inicio (en curso)       | Volver a la sesión · Pausar             | Sesión en vivo · hoja de pausa                                                                      |
| Inicio (hecho hoy)      | Ver resumen                             | Resumen de la sesión de hoy                                                                         |
| Mis rutinas             | Tocar tarjeta · mantener pulsado        | Vista rápida (Empezar, Editar, Duplicar, Eliminar) · menú contextual (+ Compartir)                  |
| Entrenar                | Botón +                                 | Nuevo entrenamiento: entreno libre de fuerza, intervalos o running · Crear rutina · Desde plantilla |
| Asistente               | Continuar · Probar sin guardar          | Editor del tipo · sesión sin guardar la rutina                                                      |
| Editor                  | Guardar · Guardar y empezar             | Mis rutinas (rutina recién guardada) · sesión                                                       |
| Ejercicios              | Grupo · ejercicio · Añadir a una rutina | Cuadrícula del grupo · ficha · hoja de rutinas → aviso «añadido»                                    |
| Sesión                  | Pausa                                   | Reanudar · Reiniciar paso/tramo · Terminar y guardar → Final · Salir sin guardar                    |
| Sesión                  | Terminar (fin natural o botón)          | Final de sesión → Listo (Inicio) · Ver el detalle                                                   |
| Historial               | Tocar día · Ver detalle                 | Sesiones del día · Detalle (Repetir rutina, Eliminar sesión)                                        |
| Running sin ubicación   | Empezar                                 | Pantalla de permiso → Abrir Ajustes · Correr solo con el tiempo                                     |
| Web · rutina de running | Empezar en el móvil                     | Modal «se hace con el móvil» → Avisar en mi iPhone · Ahora no                                       |

## 5. Módulos de la app

Cada módulo resume sus pantallas (por nombre de componente), sus elementos, acciones, estados y las reglas que aplica. Sus capturas se localizan por el nombre de componente en `../pulse-imagenes/INDICE.md`.

### M1 · Bienvenida y onboarding

- **Objetivo:** Preparar un plan con lo que le apetece entrenar al usuario y, opcionalmente, guardar su cuenta y activar permisos.
- **Pantallas:** Bienvenida · 1 Qué te apetece entrenar · 2 Objetivo semanal · 3 Tu plan (carga) · 4 Guarda tu plan · 4b Crear cuenta con email · 5 Salud y avisos.
- **Elementos:**
  - Indicador de pasos y «Saltar» en cada paso.
  - Tipos: tarjetas multiselección (Intervalos «Cronómetro guiado», Running «Con GPS», Fuerza «Series y pesos») y contador «N elegidos».
  - Objetivo: valor con − y +, texto de ayuda y resumen de tipos.
  - Cuenta: Apple, Google, email, «Ahora no», «¿Ya tienes cuenta? Inicia sesión», aviso legal.
  - Permisos: tarjeta de Apple Salud (ejemplo de pulso) y de avisos (ejemplos), «Empezar a entrenar».
- **Reglas:** RN-06, RN-07, RN-08, RN-23, RN-26. La ubicación no se pide aquí, sino en la primera carrera.
- **Salida:** Inicio en estado «plan recién creado».

### M2 · Cuenta y acceso

- **Pantallas:** Iniciar sesión · contraseña incorrecta · Olvidé mi contraseña (pedir enlace, enlace enviado, nueva contraseña, cambiada) · Hola de nuevo (sincronizando). Crear cuenta con email está en el onboarding (iOS) y en su propia pantalla (web).
- **Elementos:**
  - Login: Continuar con Apple/Google, email, contraseña con «Mostrar», «¿Olvidaste tu contraseña?», «¿No tienes cuenta? Empieza aquí».
  - Error en línea bajo el campo.
  - Recuperar: pasos Email · Enlace · Nueva; «Abrir Mail», «Reenviar en 0:45», «Usar otro email».
  - Nueva contraseña: medidor y «Coinciden»; «Guardar y entrar».
  - Hola de nuevo: contadores de rutinas, sesiones y racha que se llenan y lista de rutinas mientras sincroniza.
- **Reglas:** RN-23, RN-24, RN-25. Bienvenida y acceso siguen el tema del sistema.

### M3 · Inicio

- **Objetivo:** Decir qué toca hoy y cómo va la semana.
- **Estados:**
  - **Habitual**: «Lo que toca hoy».
  - **Plan recién creado**: «Tu primera sesión» + «Tu primera semana».
  - **Hecho hoy**: resumen + «¿Te apetece algo más?».
  - **En curso**: progreso, Pausar, Volver a la sesión, «Se guarda sola».
  - **Tras un parón**: «Para volver» con la rutina más corta.
- **Elementos:**
  - Fecha y saludo; avatar → Perfil.
  - Tarjeta principal en el color del tipo: etiqueta de estado, «Ver rutina», nombre, motivo, gráfico de la estructura, duración y bloques, Empezar y botón secundario (cambiar rutina, elegir otra, hacer otra sesión o pausar).
  - «Esta semana»: N de objetivo, segmentos por sesión con tipo y día, racha.
  - «La última vez»: fecha, rutina, resultado y duración.
- **Reglas:** RN-02 a RN-05, RN-07 a RN-10, RN-12.

### M4 · Mis rutinas y vista rápida

- **Elementos:**
  - Segmentos Mis rutinas · Ejercicios; botón + (iOS) o «+ Nueva rutina» y búsqueda ⌘K (web).
  - Filtros Todas · Intervalos · Running · Fuerza.
  - Tarjeta: tipo en su color, «Toca hoy» si procede, nombre, duración, gráfico de estructura y última vez; botón de empezar.
- **Acciones:**
  - Tocar → vista rápida: métricas (duración, series/ejercicios/rondas, ritmo objetivo), bloques o ejercicios con pesos, Empezar, Editar, Duplicar, Eliminar.
  - Mantener pulsado → menú: Empezar, Editar, Duplicar, Compartir, Eliminar.
- **Estados:** Lista habitual · rutina recién guardada (etiqueta «Nueva» y aviso) · sin rutinas (crear por tipo o «O entrena libre ahora»).

### M5 · Nuevo entrenamiento

- **Elementos:**
  - Entreno libre «Empieza ya, sin preparar nada»: Fuerza («Añade ejercicios sobre la marcha»), Intervalos («Tú marcas cuándo trabajas y descansas»), Running («Corre sin objetivo, con GPS»; en web «Se registra con el GPS del móvil»).
  - Con rutina: Crear rutina · Desde plantilla (Tabata, 5×5, series…).
  - iOS: atajo de Siri «Empieza una carrera libre en PULSE».
- **Salida:** Libreta vacía de fuerza · intervalos libres · carrera libre · asistente.

### M6 · Ejercicios y ficha

- **Elementos:**
  - Buscador («Buscar entre 180 ejercicios»), chips o lista de grupos con contador, «Material: Todo», «+ Crear ejercicio» (web).
  - Todos: una fila por grupo con «Ver los N». Grupo: cuadrícula con foto, nombre y material.
  - Ficha: media y «Ver cómo se hace», grupo y material, músculos, 1RM estimado con gráfico, récord, volumen y series de la última vez, pasos, alternativas, «En tus rutinas», «Añadir a una rutina».
- **Acciones:** Añadir a una rutina → hoja con «Nueva rutina con este ejercicio» y tus rutinas (marca las que ya lo tienen) → aviso de añadido.
- **Estados:** Búsqueda sin resultados: «Crear «…»» y sugerencias. RN-31.

### M7 · Asistente y editores

- **Asistente:** Tipo (Intervalos · Running · Fuerza) y punto de partida: En blanco o plantilla del tipo (p. ej. Intervalos 7 × 2, Tabata, Circuito 4 × 4). Continuar · Probar sin guardar · Cancelar.
- **Común:** Cabecera Cancelar · Editar rutina · Guardar; nombre y duración estimada en la tarjeta del tipo; «Guardar y empezar» abajo.
- **Intervalos:** Libre / Guiado con ejercicios · Trabajo · Descanso · Rondas · Preparación por ronda (− +) · Ejercicios (N elegidos) · Más ajustes (calentamiento, recuperación, voz y sonidos).
- **Fuerza:** Ejercicios plegables; el abierto muestra Carga (con carga…), Medida (Reps/Tiempo), tabla Serie · Reps · Peso, «Añadir serie» y Descanso.
- **Running:** Calentamiento · Repetir N × · Correr (Distancia/Tiempo, valor, ritmo objetivo) · Avisarme si me desvío (± 10 s/km, voz y vibración) · Caminar entre series · Recuperación final · Añadir tramo o bloque.

### M8 · Sesión de intervalos

- **Con rutina:**
  - Cabecera: rutina, «Ronda x de y · Ejercicio x de y», cerrar.
  - Fase (TRABAJO/DESCANSO) y tiempo restante con total («de 0:20»); el color llena la pantalla según avanza.
  - Ejercicio actual: foto, nombre, grupo y consejo. Abajo: siguiente fase y tiempo; controles anterior, pausa y siguiente.
- **Pausa:** Rutina, fase y tiempo restante, hecho y por delante · Reanudar · Reiniciar este paso · Terminar y guardar · Salir sin guardar.
- **Libres:**
  - Tiempo total, «TRABAJO n» / «DESCANSO n» con su tiempo y «tu anterior».
  - Nombre opcional del siguiente tramo; gráfico «Lo que llevas»; medias de trabajo y descanso.
  - Botón grande «Descansar»/«Trabajar» («Toca cuando acabes este tramo»).
  - Pausa: Reanudar, Terminar y guardar, Salir sin guardar.
- **Reglas:** RN-11, RN-12, RN-14. Siempre a todo color. Web: Espacio pausa, → siguiente.

### M9 · Sesión de running

- **Con rutina:**
  - «GPS · Serie x de y», fase (CORRER) y metros hechos «de 400 m».
  - Ruta en directo; ritmo real frente al objetivo con diferencia («7 s más lento»).
  - Total: km, tiempo y pulso. Siguiente tramo (Caminar 1:11). Controles.
- **Pausa:** Serie y metros que quedan, km hechos, tiempo en marcha y por delante · Reanudar · Reiniciar este tramo · Terminar y guardar · Salir sin guardar.
- **Carrera libre:** Distancia, km en curso con % · ruta · ritmo ahora y medio · tiempo y pulso · vuelta actual · botones Vuelta y Fin. Pausa con kilómetros (barras por ritmo) y vueltas marcadas.
- **Reglas:** RN-18, RN-19, RN-32. Siempre a todo color.

### M10 · Sesión de fuerza y entreno libre

- **Cabecera:** Selector Guiada · Libreta y Terminar; nombre, tiempo y «n de N series»; Descanso (Auto · 1:30 / Desactivado).
- **Guiada:** SERIE x DE y, foto, «Cambiar», ejercicio, «La última vez», grupo, Repeticiones y Peso con − +, «Serie hecha», siguiente serie.
- **Libreta:** Por ejercicio: nombre, grupo y progreso (3/4), tabla Serie · Anterior · Kg · Reps, check por serie, ajuste ± 2,5 kg y ± 1 rep en la fila activa, «Añadir ejercicio». Web: lista de ejercicios a la izquierda, «Añadir serie», nota, panel de descanso (+15 s, Empezar ya) y atajos.
- **Máquina ocupada:** Hoja: Hacerlo luego · alternativas del mismo grupo con filtros (Todos, Mancuernas, Polea, Sin máquina, Máquina) y su último registro · Solo hoy / Siempre en la rutina · «Cambiar por …».
- **Entreno libre:** Vacío («Empieza por tu primer ejercicio») → Añadir ejercicio (buscar, Recientes, Todo, grupos; «Usados hace poco») → libreta → final con guardar como rutina.
- **Reglas:** RN-13, RN-15, RN-16, RN-17, RN-21. La guiada va a todo color; la libreta sigue el tema.

### M11 · Final de sesión

- **Estructura:**
  - Cabecera en el color del tipo: «Sesión completada» (o «… libre completado»), nombre, fecha y horas, gráfico; Listo.
  - Cuatro métricas del tipo (abajo).
  - «Lo que has conseguido»: objetivo semanal y racha (+1).
  - «Frente a la última vez»: tres comparaciones.
  - «¿Cómo de dura ha sido?» y «Listo» · «Ver el detalle · guardado en Salud».
- **Métricas:** Intervalos duración, rondas, FC media, energía · Running duración, distancia, ritmo en series, FC media · Fuerza duración, volumen, récords, energía.
- **Libres:** Duración sin pausas y datos propios, más «¿Lo guardas como rutina?» (RN-13, RN-14).

### M12 · Progreso

- **Resumen:** Periodo «8 sem». Sesiones de las últimas 8 semanas y diferencia, gráfico apilado por tipo con detalle de la semana; Tiempo, Volumen fuerza, Distancia y Récords con tendencia; reparto por tipo; constancia (racha y mejor) con mapa por días (Minutos/Tipo); 1RM estimado de un ejercicio; récords recientes.
- **Historial:** Mes con flechas; sesiones, horas y semanas cumplidas; calendario con puntos de color; leyenda; sesiones del día seleccionado con «Ver detalle».
- **Detalle:** Fecha, tipo y estado, nombre; métricas; pulso con gráfico, media, máxima y kcal; ejercicios con volumen, series y récord; Repetir rutina; Eliminar sesión.
- **Vacíos:** Primera semana (qué irá apareciendo y primera sesión) · mes sin sesiones (Ir a Entrenar).

### M13 · Perfil y ajustes

- **Grupos:**
  - Cabecera: avatar, nombre, email, sincronización.
  - Entrenamiento: Objetivo semanal · Unidades kg/lb.
  - Durante la sesión: Sonidos · Voz · Hápticos (web: Atajos de teclado) · Mantener pantalla encendida.
  - Apariencia e idioma: Tema · Icono de la app (iOS) · Idioma.
  - Salud y permisos: Apple Salud · Ubicación (iOS) · Notificaciones.
  - Tus datos: Exportar copia · Importar copia.
  - Cuenta: Cambiar contraseña · Cerrar sesión · Eliminar cuenta. Versión.
- **Pantallas:** Objetivo semanal (2–7 con chips) · Apariencia (Automático, Claro, Oscuro + pantallas especiales) · Notificaciones (4 interruptores + vista previa) · Tus datos (JSON/CSV, importar) · Cambiar contraseña · Icono de la app · Cerrar sesión (hoja) · Eliminar cuenta (confirmar, alerta, hecho).
- **Reglas:** RN-07, RN-09, RN-26 a RN-30.

### M14 · Permisos y estados vacíos

- **Permisos:** Estructura común: icono, título, explicación, «Sin esto no verás» (3 puntos), «En Ajustes del iPhone» (pasos), botón «Abrir Ajustes» y salida («Correr solo con el tiempo» o «Ahora no»).
- **Vacíos:** Sin rutinas · búsqueda sin resultados · primera semana de progreso · mes sin sesiones. Cada uno con una acción para salir del vacío.

## 6. Integraciones del iPhone

| Integración                        | Comportamiento                                                                                                                                                                                                                                                                 |
|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Apple Salud                        | Las sesiones se guardan como entrenamientos. Se lee el pulso del Apple Watch (media, máxima, gráfico) y se muestra la energía activa. Se pide en el onboarding y en Perfil; si está desconectada, pantalla de permiso. Lo guardado en Salud no se borra al eliminar la cuenta. |
| Ubicación                          | «Al usar la app». Se pide en la primera carrera. Sin ella: pantalla de permiso y opción de correr solo con el tiempo.                                                                                                                                                          |
| Live Activity                      | Pantalla bloqueada: rutina, ronda, fase, ejercicio, siguiente paso, tiempo y barra de progreso.                                                                                                                                                                                |
| Dynamic Island                     | Compacta: fase y tiempo (trabajo, km o descanso). Ampliada al mantenerla pulsada: running (serie, ritmo, Pausar, Marcar vuelta) y fuerza en descanso (siguiente serie, Pausar, Empezar ya).                                                                                    |
| Widgets                            | «Esta semana» (n de objetivo y racha) y «Lo que toca» (rutina y duración), en varios tamaños.                                                                                                                                                                                  |
| Siri                               | Atajo «Empieza una carrera libre en PULSE».                                                                                                                                                                                                                                    |
| Iconos alternativos                | Cobalto (principal), Noche, Claro y Tres tipos. El cambio se ve al momento; el sistema muestra su aviso.                                                                                                                                                                       |
| Notificaciones                     | Los cuatro tipos de RN-28, con ejemplos en el onboarding y vista previa en Perfil.                                                                                                                                                                                             |
| Pantalla encendida, voz y hápticos | Preferencias de Perfil que se aplican durante la sesión.                                                                                                                                                                                                                       |

## 7. Web

La web replica la app de iOS en el navegador y se adapta a tres anchos. Esta sección recoge solo lo que cambia.

| Tema           | Especificación                                                                                                                                                                                                            |
|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tamaños        | Escritorio 1440 × 900 · Tablet 834 × 1194 · Móvil 390. Cada pantalla existe en claro y oscuro.                                                                                                                            |
| Disposición    | Escritorio: barra lateral (logo, Nuevo entrenamiento, Inicio, Entrenar, Progreso, tarjeta de perfil) y contenido en rejilla. Tablet: misma estructura compacta. Móvil: cabecera y barra de pestañas como en iOS.          |
| Inicio         | «Lo que toca hoy» grande, «Tus otras rutinas», «Esta semana», «La última vez» y «Últimas 8 semanas».                                                                                                                      |
| Entrenar       | Lista de rutinas con panel de detalle a la derecha; búsqueda con ⌘K; «+ Nueva rutina». Ejercicios con lista de grupos a la izquierda y «+ Crear ejercicio».                                                               |
| Sesión         | Pantalla completa con atajos: Espacio pausa, → siguiente. Libreta de fuerza en dos columnas con panel de descanso, notas y atajos.                                                                                        |
| Running        | No se registra en el navegador. «Empezar en el móvil» abre un modal (tu iPhone, estado de sincronización) con «Avisar en mi iPhone» y «Ahora no».                                                                         |
| Sin conexión   | Aviso «Sin conexión · Puedes seguir entrenando: lo guardamos aquí y se sincroniza al volver» con «Reintentar».                                                                                                            |
| Perfil         | Mismos grupos que iOS salvo Hápticos (pasa a Atajos de teclado), Icono de la app y Ubicación; Apple Salud aparece como «Desde tu iPhone».                                                                                 |
| Cookies        | Aviso al entrar (Configurar, Rechazar, Aceptar) y preferencias: Necesarias (siempre activas: sesión, tema y seguridad) y Analítica (opcional, anónima), con Rechazar todo y Guardar preferencias. Se cambia desde el pie. |
| Textos legales | Política de privacidad, Términos y condiciones y Política de cookies, con índice lateral. Huecos \[CORCHETES\] por completar.                                                                                             |

## 8. Landing, correos y App Store

### Landing de lista de espera

| Sección                 | Contenido                                                                                                                                                               |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cabecera                | Logo, enlaces (Los tres tipos, Cómo funciona, Ejercicios, Preguntas), cambio claro/oscuro y «Apúntate».                                                                 |
| Hero                    | «Muy pronto en iOS, Android y web» · «Entrena a tu ritmo.» · email + «Apuntarme» · «¿Dónde la usarás?» iOS / Android / Web · «Próximamente en» App Store y Google Play. |
| Los tres tipos          | Una tarjeta por tipo con tres ventajas cada una.                                                                                                                        |
| Cómo funciona           | Lo que toca sin días fijos, objetivo en sesiones y progreso claro, con ejemplo de la cola de rutinas.                                                                   |
| Ejercicios              | «Más de 180 ejercicios. Y los que tú añadas.» Foto y técnica, crear los tuyos, progreso en cada uno y alternativas.                                                     |
| Lo que incluye          | Voz guía, pantalla bloqueada, entreno libre, récords de fuerza, claro u oscuro, cuenta opcional.                                                                        |
| Plataformas y preguntas | «Llega a iOS, Android y web» y cuatro preguntas: cuándo sale, dispositivos, si hace falta cuenta y qué se hace con el email.                                            |
| Cierre y pie            | Segundo formulario; enlaces Privacidad, Términos, Cookies, Configurar cookies, Contacto; © 2026 PULSE.                                                                  |
| Estados                 | Apuntado (confirmación en la página) · aviso de cookies.                                                                                                                |

### Correos

| Correo               | Cuándo                     | Contenido                                                                                    |
|----------------------|----------------------------|----------------------------------------------------------------------------------------------|
| Verificar cuenta     | Al crear cuenta con email  | «Confirma tu email», botón Confirmar email y enlace alternativo.                             |
| Recuperar contraseña | Al pedir el enlace         | «Crea una contraseña nueva», botón y aviso de caducidad (1 hora).                            |
| Lista de espera      | Al apuntarse en la landing | «Ya estás en la lista», los tres tipos y aviso de que solo se escribirá para el lanzamiento. |

Todos llevan pie con datos del responsable (\[NOMBRE O RAZÓN SOCIAL\] · \[DIRECCIÓN\]) y enlaces legales y de baja.

### App Store

| Pieza                | Especificación                                                                                                                                                                                                   |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Icono                | Cobalto, exportado a 1024 × 1024 px (iOS aplica la máscara). Tamaños de referencia 180, 120, 87, 80, 60 y 40. Alternativos: Noche, Claro, Tres tipos.                                                            |
| Pantalla de arranque | Logo sobre fondo claro y oscuro.                                                                                                                                                                                 |
| Capturas             | Cinco, con titular: «Abres la app y ya sabes lo que toca», «Voz guía en cada cambio», «Tus series y kilos, siempre a mano», «Ritmo objetivo y GPS en cada serie», «Semanas, récords y constancia de un vistazo». |

## 9. Sistema visual

Resumen. Los valores completos (claro y oscuro, gradientes, sombras, tipografía, radios y movimiento) están en `docs/BRAND.md`.

| Token            | Valor     | Uso                                       |
|------------------|-----------|-------------------------------------------|
| Intervalos       | `#2457ff` | Color del tipo y acento general (cobalto) |
| Running          | `#15803d` | Color del tipo                            |
| Fuerza           | `#c2560a` | Color del tipo                            |
| Tinta            | `#0e1726` | Texto principal en claro                  |
| Texto secundario | `#5a6678` | Ayudas y metadatos en claro               |
| Fondo claro      | `#f4f6f9` | Fondo de pantalla; tarjetas en blanco     |
| Fondo oscuro     | `#06080e` | Fondo de pantalla en oscuro               |
| Hoja oscura      | `#10141d` | Hojas y tarjetas en oscuro                |
| Acento en oscuro | `#8aa4ff` | Iconos y enlaces sobre fondo oscuro       |

| Aspecto           | Especificación                                                                                                                                                    |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tipografía        | Figtree (300–900) para títulos, cifras y botones destacados. Texto: SF Pro en iOS; en web, la pila del sistema (-apple-system, SF Pro Text, Segoe UI, system-ui). |
| Temas             | Claro y oscuro en todas las pantallas. Excepciones de RN-26.                                                                                                      |
| Tamaños de diseño | iPhone 390 × 844 pt. Web 1440, 834 y 390 px de ancho. Landing 1440 y 390.                                                                                         |
| Formas            | Tarjetas y hojas con esquinas muy redondeadas, botones tipo píldora, barra de pestañas flotante en iOS.                                                           |
| Movimiento        | Entradas suaves de hojas y tarjetas; se respeta «reducir movimiento».                                                                                             |
| Gráficos          | Siempre con el color del tipo; apilados por tipo en Progreso.                                                                                                     |

## 10. Paridad iOS y web

La web va a la par con iOS. La tabla recoge dónde difieren.

| Función                             | iOS                                             | Web                                                        |
|-------------------------------------|-------------------------------------------------|------------------------------------------------------------|
| Onboarding y acceso                 | Sí                                              | Sí                                                         |
| Navegación                          | Pestañas; Perfil desde el avatar                | Barra lateral o pestañas en móvil; Perfil desde la tarjeta |
| Mis rutinas                         | Vista rápida en hoja, menú contextual           | Panel de detalle, búsqueda ⌘K                              |
| Ejercicios por grupo y ficha        | Sí                                              | Sí, con «+ Crear ejercicio»                                |
| Editores de los tres tipos          | Sí                                              | Sí                                                         |
| Sesión de intervalos (y libres)     | Sí                                              | Sí, con atajos de teclado                                  |
| Sesión de running (y carrera libre) | Sí, con GPS                                     | Se inicia en el móvil                                      |
| Sesión de fuerza                    | Guiada y libreta                                | Guiada y libreta (dos columnas, notas, atajos)             |
| Final, Progreso, Detalle            | Sí                                              | Sí                                                         |
| Durante la sesión                   | Sonidos, voz, hápticos, pantalla                | Sonidos, voz, atajos, pantalla                             |
| Apariencia                          | Tema e icono de la app                          | Tema                                                       |
| Salud y ubicación                   | Apple Salud y ubicación                         | Apple Salud «desde tu iPhone»                              |
| Live Activity, isla, widgets, Siri  | Sí                                              | —                                                          |
| Sin conexión                        | —                                               | Aviso y sincronización al volver                           |
| Cookies y textos legales            | Términos y privacidad enlazados al crear cuenta | Aviso de cookies, preferencias y páginas legales           |

## 11. Decisiones abiertas

Puntos que el diseño no fija. Conviene decidirlos antes o durante el desarrollo.

| Tema                     | Qué falta decidir                                                                                     |
|--------------------------|-------------------------------------------------------------------------------------------------------|
| Lo que toca              | Desempates entre rutinas con la misma espera y cómo entran las rutinas nuevas («Sin probar todavía»). |
| Vuelta tras un parón     | Número de días sin entrenar que activa la propuesta de la rutina más corta (RN-04).                   |
| Esfuerzo percibido       | Cómo modifica las propuestas (RN-20).                                                                 |
| 1RM estimado y récords   | Fórmula del 1RM y criterio exacto de récord (RN-21).                                                  |
| Propuesta complementaria | Criterio de «¿Te apetece algo más?» en Inicio (RN-05).                                                |
| Compartir rutina         | Formato y destino de «Compartir».                                                                     |
| Empezar en el móvil      | Mecanismo del aviso al iPhone desde la web (RN-32).                                                   |
| Biblioteca               | Fotos o vídeos de los 180 ejercicios y textos de técnica.                                             |
| Suscripción              | Límites del plan gratuito, plan de pago y su pantalla (gratis con limitaciones y plan de pago).       |
| Android                  | Adaptación de la app.                                                                                 |
| Apple Watch              | App propia para el reloj.                                                                             |
| Textos legales           | Completar los \[CORCHETES\] de privacidad, términos, cookies y correos.                               |

## Anexo. Inventario de pantallas

Todas las pantallas finalizadas con su componente. En web, cada pantalla tiene variante clara y oscura (sufijo `Oscuro`) y, según el caso, de tablet (`Tablet`) y móvil (`Movil`). Las capturas están en `../pulse-imagenes`; su índice completo, en `../pulse-imagenes/INDICE.md`.

### iOS

| Sección                                            | Pantalla                                | Claro                     | Oscuro                          |
|----------------------------------------------------|-----------------------------------------|---------------------------|---------------------------------|
| Onboarding                                         | Bienvenida                              | `PL_Bienvenida`           | `P_BienvenidaOscura`            |
|                                                    | 1 · Qué te apetece entrenar             | `PL_OnbTipos`             | `P_OnbTipos`                    |
|                                                    | 2 · Objetivo semanal                    | `PL_OnbObjetivo`          | `P_OnbObjetivo`                 |
|                                                    | 3 · Tu plan para empezar                | `PL_OnbPlan`              | `P_OnbPlan`                     |
|                                                    | 4 · Guarda tu plan                      | `PL_OnbCuenta`            | `P_OnbCuenta`                   |
|                                                    | 4b · Crear cuenta con email             | `PL_Registro`             | `P_Registro`                    |
|                                                    | 5 · Salud y avisos                      | `PL_OnbPermisos`          | `P_OnbPermisos`                 |
| Acceso                                             | Iniciar sesión                          | `PL_Login`                | `P_Login`                       |
|                                                    | Iniciar sesión · contraseña incorrecta  | `PL_LoginError`           | `P_LoginError`                  |
|                                                    | Olvidé mi contraseña · pedir enlace     | `PL_Recuperar`            | `P_Recuperar`                   |
|                                                    | Olvidé mi contraseña · enlace enviado   | `PL_RecuperarEnviado`     | `P_RecuperarEnviado`            |
|                                                    | Olvidé mi contraseña · nueva contraseña | `PL_RecuperarNueva`       | `P_RecuperarNueva`              |
|                                                    | Olvidé mi contraseña · cambiada         | `PL_RecuperarHecho`       | `P_RecuperarHecho`              |
|                                                    | Hola de nuevo · sincronizando           | `PL_BienvenidoDeNuevo`    | `P_BienvenidoDeNuevo`           |
| Inicio                                             | Inicio · habitual                       | `Main`                    | `InicioOscuro`                  |
|                                                    | Inicio · plan recién creado             | `InicioNuevo`             | `P_InicioNuevoOscuro`           |
|                                                    | Inicio · ya entrenó hoy                 | `InicioHoy`               | `P_InicioHoyOscuro`             |
|                                                    | Inicio · sesión en curso                | `InicioEnCurso`           | `P_InicioEnCursoOscuro`         |
|                                                    | Inicio · vuelve tras un parón           | `InicioParon`             | `P_InicioParonOscuro`           |
| Entrenar                                           | Entrenar · Mis rutinas                  | `Rutinas`                 | `P_RutinasOscuro`               |
|                                                    | Mis rutinas · mantener pulsado          | `RutinasMenu`             | `P_RutinasMenuOscuro`           |
|                                                    | Vista rápida · running                  | `VistaRapida`             | `P_VistaRapidaOscuro`           |
|                                                    | Vista rápida · fuerza                   | `VistaFuerza`             | `P_VistaFuerzaOscuro`           |
|                                                    | Vista rápida · intervalos               | `VistaIntervalos`         | `P_VistaIntervalosOscuro`       |
|                                                    | Botón + · nuevo entrenamiento           | `P_NuevoMenu`             | `P_NuevoMenuOscuro`             |
|                                                    | Entrenar · Ejercicios · por grupo       | `Ejercicios`              | `P_EjerciciosOscuro`            |
|                                                    | Entrenar · Ejercicios · Pecho           | `P_EjerciciosPecho`       | `P_EjerciciosPechoOscuro`       |
|                                                    | Mis rutinas · rutina recién guardada    | `RutinasGuardada`         | `P_RutinasGuardadaOscuro`       |
|                                                    | Ejercicios · ficha del ejercicio        | `FichaEjercicio`          | `P_FichaEjercicioOscuro`        |
|                                                    | Ficha · añadir a una rutina             | `AnadirRutina`            | `P_AnadirRutinaOscuro`          |
|                                                    | Ficha · añadido                         | `P_FichaAnadido`          | `P_FichaAnadidoOscuro`          |
| Crear y editar rutinas                             | Nueva rutina · asistente                | `NuevaRutina`             | `P_NuevaRutinaOscuro`           |
|                                                    | Editor · intervalos                     | `EditorIntervalos`        | `P_EditorIntervalosOscuro`      |
|                                                    | Editor · fuerza                         | `EditorFuerza`            | `P_EditorFuerzaOscuro`          |
|                                                    | Editor · running                        | `P_EditorRunning`         | `P_EditorRunningOscuro`         |
| Sesión · intervalos                                | Sesión en vivo · Llenado con cápsula    | `Crono`                   | —                               |
|                                                    | Sesión en vivo · hoja de pausa          | `Pausa`                   | —                               |
|                                                    | Final de sesión · intervalos            | `Final`                   | —                               |
|                                                    | Intervalos libres · trabajo             | `P_IntLibre`              | —                               |
|                                                    | Intervalos libres · descanso            | `P_IntLibreDescanso`      | —                               |
|                                                    | Intervalos libres · en pausa            | `P_IntLibrePausa`         | —                               |
|                                                    | Final · intervalos libres               | `P_FinalIntLibre`         | —                               |
| Sesión · running                                   | Running con GPS · Llenado               | `Running`                 | —                               |
|                                                    | Running · hoja de pausa                 | `RunningPausa`            | —                               |
|                                                    | Final de sesión · running               | `FinalRunning`            | —                               |
|                                                    | Carrera libre · en marcha               | `P_RunLibre`              | —                               |
|                                                    | Carrera libre · en pausa                | `P_RunLibrePausa`         | —                               |
|                                                    | Final · carrera libre                   | `P_FinalRunLibre`         | —                               |
| Sesión · fuerza                                    | Fuerza · vista guiada                   | `Fuerza`                  | —                               |
|                                                    | Fuerza · vista libreta                  | `P_Libreta`               | `P_LibretaOscuro`               |
|                                                    | Fuerza · libreta sin descansos          | `P_LibretaSinDescanso`    | `P_LibretaSinDescansoOscuro`    |
|                                                    | Fuerza · máquina ocupada                | `P_LibretaCambiar`        | `P_LibretaCambiarOscuro`        |
|                                                    | Final de sesión · fuerza                | `FinalFuerza`             | —                               |
|                                                    | Entreno libre de fuerza · vacío         | `P_LibreVacio`            | `P_LibreVacioOscuro`            |
|                                                    | Entreno libre · añadir ejercicio        | `P_LibreAnadir`           | `P_LibreAnadirOscuro`           |
|                                                    | Entreno libre · libreta                 | `LibretaLibre`            | `P_LibretaLibreOscuro`          |
|                                                    | Final · entreno libre de fuerza         | `P_FinalLibre`            | —                               |
| Final de sesión                                    | Final · intervalos                      | —                         | `P_FinalOscuro`                 |
|                                                    | Final · running                         | —                         | `P_FinalRunningOscuro`          |
|                                                    | Final · fuerza                          | —                         | `P_FinalFuerzaOscuro`           |
|                                                    | Final · intervalos libres               | —                         | `P_FinalIntLibreOscuro`         |
|                                                    | Final · carrera libre                   | —                         | `P_FinalRunLibreOscuro`         |
|                                                    | Final · entreno libre de fuerza         | —                         | `P_FinalLibreOscuro`            |
| Progreso y Perfil                                  | Progreso · Resumen                      | `Resumen`                 | `P_ResumenOscuro`               |
|                                                    | Progreso · Historial                    | `Historial`               | `P_HistorialOscuro`             |
|                                                    | Progreso · Detalle de sesión            | `Detalle`                 | `P_DetalleOscuro`               |
|                                                    | Perfil y ajustes                        | `PerfilPantalla`          | `P_PerfilOscuro`                |
|                                                    | Perfil · Apariencia                     | `Apariencia`              | `P_AparienciaOscuro`            |
| Perfil · ajustes y cuenta                          | Objetivo semanal                        | `P_AjusteObjetivo`        | `P_AjusteObjetivoOscuro`        |
|                                                    | Notificaciones                          | `P_AjusteNotificaciones`  | `P_AjusteNotificacionesOscuro`  |
|                                                    | Tus datos · exportar e importar         | `P_AjusteDatos`           | `P_AjusteDatosOscuro`           |
|                                                    | Cambiar contraseña                      | `P_AjusteContrasena`      | `P_AjusteContrasenaOscuro`      |
|                                                    | Icono de la app                         | `P_AjusteIcono`           | `P_AjusteIconoOscuro`           |
|                                                    | Cerrar sesión                           | `P_AjusteCerrar`          | `P_AjusteCerrarOscuro`          |
|                                                    | Eliminar cuenta · confirmar             | `P_EliminarCuenta`        | `P_EliminarCuentaOscuro`        |
|                                                    | Eliminar cuenta · última confirmación   | `P_EliminarAlerta`        | `P_EliminarAlertaOscuro`        |
|                                                    | Eliminar cuenta · hecho                 | `P_EliminarHecho`         | `P_EliminarHechoOscuro`         |
| Permisos y estados vacíos                          | Sin ubicación · al empezar a correr     | `P_PermisoUbicacion`      | `P_PermisoUbicacionOscuro`      |
|                                                    | Apple Salud desconectado                | `P_PermisoSalud`          | `P_PermisoSaludOscuro`          |
|                                                    | Avisos desactivados                     | `P_PermisoNotificaciones` | `P_PermisoNotificacionesOscuro` |
|                                                    | Progreso · primera semana               | `P_VacioProgreso`         | `P_VacioProgresoOscuro`         |
|                                                    | Historial · mes sin sesiones            | `P_VacioHistorial`        | `P_VacioHistorialOscuro`        |
|                                                    | Ejercicios · búsqueda sin resultados    | `P_VacioBusqueda`         | `P_VacioBusquedaOscuro`         |
|                                                    | Mis rutinas · sin rutinas               | `P_VacioRutinas`          | `P_VacioRutinasOscuro`          |
| iPhone · Live Activity, Dynamic Island y widgets   | Pantalla bloqueada · sesión en directo  | `P_LiveBloqueo`           | —                               |
|                                                    | Dynamic Island · compacta y ampliada    | `P_LiveIsla`              | —                               |
|                                                    | Pantalla de inicio · widgets            | `P_Widgets`               | —                               |
| App Store · icono, pantalla de arranque y capturas | Icono de la app                         | `TiendaIcono`             | —                               |
|                                                    | Pantalla de arranque · claro y oscuro   | `TiendaArranque`          | —                               |
|                                                    | Capturas para la App Store              | `TiendaCapturas`          | —                               |
| Componentes                                        | Componente · barra de pestañas          | `TabBar`                  | —                               |
|                                                    | Componente · perfil en barras           | `Barras`                  | —                               |
|                                                    | Componente · logo                       | `Logo`                    | —                               |

### Web

| Sección                  | Pantalla                               | Componente             | Tamaños                     |
|--------------------------|----------------------------------------|------------------------|-----------------------------|
| Acceso y primeros pasos  | Iniciar sesión                         | `WebAppAcceso`         | Escritorio · Tablet · Móvil |
|                          | Crear cuenta                           | `WebAppRegistro`       | Escritorio · Tablet · Móvil |
|                          | Recuperar contraseña                   | `WebAppRecuperar`      | Escritorio · Tablet · Móvil |
|                          | Recuperar · revisa tu correo           | `WebAppRecEnviado`     | Escritorio · Móvil          |
|                          | Recuperar · contraseña nueva           | `WebAppRecNueva`       | Escritorio · Móvil          |
|                          | Recuperar · contraseña cambiada        | `WebAppRecHecho`       | Escritorio · Móvil          |
|                          | Iniciar sesión · contraseña incorrecta | `WebAppErrorLogin`     | Escritorio · Móvil          |
|                          | Primeros pasos · qué entrenar          | `WebAppOnboarding`     | Escritorio · Tablet · Móvil |
|                          | Inicio · primera vez                   | `WebAppPrimera`        | Escritorio · Tablet · Móvil |
| Inicio y entrenar        | Inicio                                 | `WebAppInicio`         | Escritorio · Tablet · Móvil |
|                          | Entrenar · Mis rutinas                 | `WebAppRutinas`        | Escritorio · Tablet · Móvil |
|                          | Nuevo entrenamiento                    | `WebAppNuevo`          | Escritorio · Tablet · Móvil |
|                          | Entrenar · Ejercicios                  | `WebAppEjercicios`     | Escritorio · Tablet · Móvil |
|                          | Ficha del ejercicio                    | `WebAppFicha`          | Escritorio · Tablet · Móvil |
|                          | Añadir a una rutina                    | `WebAppAnadir`         | Escritorio · Tablet · Móvil |
|                          | Empezar en el móvil                    | `WebAppEnMovil`        | Escritorio · Móvil          |
|                          | Vacío · sin rutinas                    | `WebAppVacioRutinas`   | Escritorio · Móvil          |
|                          | Vacío · búsqueda sin resultados        | `WebAppVacioBusqueda`  | Escritorio · Móvil          |
|                          | Sin conexión                           | `WebAppSinConexion`    | Escritorio · Móvil          |
| Crear y editar           | Editor de rutina · fuerza              | `WebAppEditor`         | Escritorio · Tablet · Móvil |
|                          | Editor de rutina · intervalos          | `WebAppEditorInt`      | Escritorio · Tablet · Móvil |
|                          | Editor de rutina · running             | `WebAppEditorRun`      | Escritorio · Tablet · Móvil |
| Sesión                   | Sesión en vivo · intervalos            | `WebAppSesion`         | Escritorio · Tablet · Móvil |
|                          | Intervalos libres                      | `WebAppIntLibre`       | Escritorio · Tablet · Móvil |
|                          | Libreta de fuerza                      | `WebAppLibreta`        | Escritorio · Tablet · Móvil |
|                          | Sesión completada                      | `WebAppFinal`          | Escritorio · Tablet · Móvil |
|                          | Final de sesión · fuerza               | `WebAppFinalFuerza`    | Escritorio · Tablet · Móvil |
| Progreso                 | Progreso · Resumen                     | `WebAppProgreso`       | Escritorio · Tablet · Móvil |
|                          | Progreso · Historial                   | `WebAppHistorial`      | Escritorio · Tablet · Móvil |
|                          | Detalle de sesión                      | `WebAppDetalle`        | Escritorio · Tablet · Móvil |
|                          | Detalle de sesión · running            | `WebAppDetalleRun`     | Escritorio · Tablet · Móvil |
|                          | Vacío · progreso                       | `WebAppVacioProgreso`  | Escritorio · Móvil          |
|                          | Vacío · historial                      | `WebAppVacioHistorial` | Escritorio · Móvil          |
| Perfil                   | Perfil · objetivo semanal              | `WebAppPerfil`         | Escritorio · Tablet · Móvil |
|                          | Perfil · apariencia                    | `WebAppTema`           | Escritorio · Tablet · Móvil |
|                          | Perfil · notificaciones                | `WebAppNotificaciones` | Escritorio · Tablet · Móvil |
|                          | Perfil · tus datos                     | `WebAppDatos`          | Escritorio · Tablet · Móvil |
|                          | Perfil · cambiar contraseña            | `WebAppContrasena`     | Escritorio · Tablet · Móvil |
|                          | Perfil · cerrar sesión                 | `WebAppCerrar`         | Escritorio · Tablet · Móvil |
|                          | Perfil · eliminar cuenta               | `WebAppEliminar`       | Escritorio · Tablet · Móvil |
|                          | Objetivo semanal                       | `WebAppObjetivo`       | Tablet · Móvil              |
| Cookies y textos legales | Aviso de cookies                       | `WebAppCookies`        | Escritorio · Móvil          |
|                          | Preferencias de cookies                | `WebAppCookiesConfig`  | Escritorio · Móvil          |
|                          | Política de privacidad                 | `WebAppPrivacidad`     | Escritorio · Móvil          |
|                          | Términos y condiciones                 | `WebAppTerminos`       | Escritorio · Móvil          |
|                          | Política de cookies                    | `WebAppCookiesDoc`     | Escritorio · Móvil          |
| Correos                  | Correo · confirma tu email             | `CorreoVerificar`      | Único                       |
|                          | Correo · crea una contraseña nueva     | `CorreoRecuperar`      | Único                       |
|                          | Correo · ya estás en la lista          | `CorreoEspera`         | Único                       |

### Landing

| Pantalla                                         | Componente                      |
|--------------------------------------------------|---------------------------------|
| Escritorio · oscuro                              | `LandingEscritorio`             |
| Móvil · oscuro                                   | `LandingMovil`                  |
| Móvil · después de apuntarse                     | `LandingMovilApuntado`          |
| Landing · aviso de cookies · escritorio · oscuro | `LandingCookiesEscritorio`      |
| Landing · aviso de cookies · móvil · oscuro      | `LandingCookiesMovil`           |
| Escritorio · claro                               | `LandingEscritorioClaro`        |
| Móvil · claro                                    | `LandingMovilClaro`             |
| Landing · aviso de cookies · escritorio · claro  | `LandingCookiesEscritorioClaro` |
| Landing · aviso de cookies · móvil · claro       | `LandingCookiesMovilClaro`      |
