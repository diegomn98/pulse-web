# Arquitectura

Estado: propuesta inicial. Se valida durante la puesta en marcha contra la guía de estilo de la versión de Angular instalada, y cualquier cambio se refleja aquí.

## Principios

- **Local-first.** Todo funciona sin conexión y sin cuenta. Supabase llegará como otro adaptador y como sincronización, sin cambiar el dominio.
- **Dominio puro.** Modelos y reglas de negocio en TypeScript sin Angular, sin DOM y sin IndexedDB, probados de forma aislada.
- **Funcionalidades aisladas.** Cada pantalla o grupo de pantallas vive en su feature; las features no se importan entre sí.
- **Una fuente por cosa.** El aspecto sale de `docs/BRAND.md` y de las capturas; el comportamiento, de `docs/PRODUCT_SPEC.md`.

## Estructura de carpetas

```
src/
  app/
    app.config.ts            providers, router y registro de repositorios
    app.routes.ts
    core/                    servicios transversales con Angular
      theme/                 tema automático, claro u oscuro
      shell/                 layout responsive: barra lateral, raíl y pestañas
      storage/               conexión a IndexedDB
      active-session/        sesión en curso compartida entre Inicio y Sesión
      clock/                 reloj inyectable para tests
    domain/                  sin Angular
      routine/               rutina de intervalos, fuerza y running; plantillas
      session/               sesión en curso, resultado, series, tramos y esfuerzo
      exercise/              ejercicio, grupo muscular y material
      planning/              lo que toca y plan inicial (RN-02 a RN-06)
      progress/              semana, objetivo, racha, récords, 1RM y comparación (RN-07 a RN-10, RN-21, RN-22)
      interval-engine/       motor de intervalos
      preferences/           preferencias del usuario
      repositories/          contratos de repositorio
    data/                    adaptadores IndexedDB de los contratos, esquema, migraciones y catálogo inicial
    shared/                  componentes de presentación propios de PULSE que @dmaster/ui no cubre
    features/
      onboarding/            M1
      home/                  M3
      routines/              M4 y la hoja «Nuevo entrenamiento» de M5
      exercises/             M6
      routine-editor/        M7
      session/               M8 y M10: intervalos, intervalos libres, fuerza guiada, libreta y fuerza libre
      session-summary/       M11
      progress/              M12
      profile/               M13
  styles/                    tokens de BRAND.md y estilos globales
e2e/                         por flujo de usuario
test-fixtures/
  shared-rules/              copia versionada de las fixtures de pulse-supabase
```

Cuando se desbloqueen, la cuenta (M2) irá en `features/account/` y el running (M9) en `features/session/` y `features/routine-editor/`.

## Capas y dependencias

| Capa | Puede importar | No puede importar |
|---|---|---|
| `domain` | Nada de la app | Angular, DOM, IndexedDB, `data`, `core`, `shared`, `features` |
| `data` | `domain`, `core/storage` | `features`, `shared` |
| `core` | `domain` | `features`, `data` (salvo el registro en `app.config.ts`) |
| `shared` | `domain` (tipos), `@dmaster/ui` | `features`, `data` |
| `features` | `domain`, `core`, `shared`, `@dmaster/ui` | `data` y otras `features` |

- Los repositorios se definen como contratos en `domain/repositories` y se inyectan mediante tokens registrados en `app.config.ts` con su adaptador de `data`. Las features nunca conocen el adaptador.
- La navegación entre features se hace con el router, nunca importando una feature desde otra.
- Cada feature tiene su servicio de estado (Signals) que orquesta dominio y repositorios. Los servicios que comparten varias features van en `core`.
- Alias: `@core`, `@domain`, `@data`, `@shared`, `@features`.

## Rutas (propuesta)

Las rutas son visibles para el usuario, así que van en español; el código, en inglés.

| Ruta | Pantalla | Módulo |
|---|---|---|
| `/bienvenida` | Onboarding (primer uso) | M1 |
| `/inicio` | Inicio | M3 |
| `/entrenar` | Mis rutinas | M4 |
| `/entrenar/ejercicios` | Ejercicios por grupo | M6 |
| `/entrenar/ejercicios/:id` | Ficha del ejercicio | M6 |
| `/rutinas/nueva` | Asistente | M7 |
| `/rutinas/:id/editar` | Editor del tipo | M7 |
| `/sesion/:id` | Sesión en vivo | M8, M10 |
| `/sesion/:id/resumen` | Final de sesión | M11 |
| `/progreso` | Resumen | M12 |
| `/progreso/historial` | Historial | M12 |
| `/progreso/sesiones/:id` | Detalle de sesión | M12 |
| `/perfil` y `/perfil/:ajuste` | Perfil y sus pantallas de ajuste | M13 |

La vista rápida de una rutina, la hoja «Nuevo entrenamiento», «Añadir a una rutina», la pausa y «Máquina ocupada» son paneles o diálogos sobre su ruta, no rutas propias.

## Layout responsive

| Vista | Ancho de diseño | Navegación |
|---|---|---|
| Escritorio | 1440 | Barra lateral completa con «Nuevo entrenamiento» y tarjeta de perfil |
| Tablet | 834 | Raíl lateral compacto |
| Móvil | 390 | Cabecera y barra de pestañas inferior |

Puntos de corte propuestos: móvil por debajo de 768 px, tablet de 768 a 1279 px y escritorio desde 1280 px. La sesión en vivo ocupa toda la pantalla en las tres vistas.

## Persistencia

- Una base IndexedDB `pulse` con almacenes para rutinas, sesiones terminadas, sesión en curso, ejercicios (catálogo y propios), preferencias y metadatos (versión de esquema).
- Esquema versionado con migraciones desde la v1. Hasta el lanzamiento no hay datos de usuarios, así que el esquema puede cambiar sin migraciones de compatibilidad; desde el lanzamiento, toda modificación lleva su migración y su test.
- API nativa de IndexedDB. Una librería de envoltorio o `fake-indexeddb` para tests requieren autorización.
- La sesión en curso se guarda con sus marcas temporales para que el motor la reconstruya tras una recarga o suspensión.
- IDs con `crypto.randomUUID()` y fechas en UTC (ISO 8601), listos para sincronizar con Supabase.

## Tema y tokens

- Los tokens de `docs/BRAND.md` se definen como propiedades CSS en `src/styles/`, integradas con el sistema de temas de `@dmaster/ui` si lo permite.
- El tema se aplica antes del primer pintado para evitar el destello de tema equivocado. «Automático» sigue `prefers-color-scheme`.
- La sesión en vivo usa siempre los colores de su tipo, sea cual sea el tema.

## Tests

- **Dominio:** cada regla `RN-xx` implementada tiene tests unitarios y ejecuta las fixtures compartidas de `pulse-supabase` (copiadas en `test-fixtures/shared-rules/`, fuera de `src/`, con `npm run sync:rules`), incluido el total de 700 s de la plantilla de intervalos.
- **Datos:** tests de integración de los adaptadores y de cada migración.
- **Features:** tests del servicio de estado de cada feature; componentes solo donde haya lógica.
- **E2E por flujo:** primer uso, empezar lo que toca, sesión de intervalos completa, intervalos libres guardados como rutina, fuerza en libreta con máquina ocupada, entreno libre de fuerza, progreso y perfil.
- **Visual:** capturas de cada pantalla a 375, 390, 834 y 1440 px en claro y oscuro; se fijan como referencia cuando Diego las aprueba.
