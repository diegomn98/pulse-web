# Estado

Proyecto recién creado. Todavía no hay código.

## Plan por fases

Cada fase se hace en bloques pequeños (una pantalla o una pieza por tarea), con su commit y con este archivo actualizado al cerrar el bloque.

### Fase 0 · Puesta en marcha
- [ ] Proyecto Angular con la configuración de `CLAUDE.md` (strict, standalone, zoneless, SCSS, router con lazy loading).
- [ ] ESLint, Prettier, Vitest y Playwright con los anchos 375, 390, 834 y 1440 y el proyecto `pwa-chromium`.
- [ ] Scripts de `CLAUDE.md` › Comandos y `npm run check` en verde.
- [ ] Alias de importación y estructura de `docs/ARCHITECTURE.md` validada contra la guía de estilo de Angular.
- [ ] Service worker de la PWA.
- [ ] `@dmaster/ui` instalada y revisada su API de temas.

### Fase 1 · Base visual
- [ ] Figtree (pendiente de autorización).
- [ ] Tokens de `docs/BRAND.md` en claro y oscuro.
- [ ] Servicio de tema: automático, claro u oscuro, sin destello al cargar.
- [ ] Shell responsive: barra lateral, raíl y pestañas, solo con destinos ya implementados.

### Fase 2 · Dominio y datos
- [ ] Modelos de rutina, sesión, ejercicio y preferencias.
- [ ] Script `sync:rules` y fixtures compartidas copiadas desde `pulse-supabase`.
- [ ] Motor de intervalos con el test de la plantilla de 700 s (fixture `interval-total`).
- [ ] Reglas decididas en `RULES.md` (objetivo, racha, volumen) con sus fixtures. Lo que toca y la semana, cuando se decidan.
- [ ] IndexedDB v1, adaptadores de repositorio y sus tests.
- [ ] Catálogo inicial de ejercicios (necesita decidir su origen).

### Fase 3 · Pantallas
- [ ] M1 Onboarding (sin el paso de cuenta y sin running).
- [ ] M3 Inicio y sus estados.
- [ ] M4 y M5 Mis rutinas, vista rápida y nuevo entrenamiento.
- [ ] M6 Ejercicios, ficha y añadir a una rutina.
- [ ] M7 Asistente y editores de intervalos y fuerza.
- [ ] M8 Sesión de intervalos e intervalos libres.
- [ ] M10 Sesión de fuerza, libreta, máquina ocupada y entreno libre.
- [ ] M11 Final de sesión.
- [ ] M12 Progreso: resumen, historial y detalle.
- [ ] M13 Perfil (sin cuenta, notificaciones ni Apple Salud), con exportar e importar.

### Fase 4 · Cierre de la versión local
- [ ] M14 Estados vacíos.
- [ ] Modo sin conexión de la PWA probado.
- [ ] Repaso de accesibilidad WCAG 2.2 AA.
- [ ] Repaso visual de todas las pantallas contra las capturas en los cuatro anchos y en los dos temas.

### Más adelante
- [ ] Supabase: cuenta, acceso, sincronización, notificaciones, páginas legales y cookies (`docs/decisions/0002-cuenta-y-sincronizacion-tras-supabase.md`).
- [ ] Running, cuando se decida (`docs/decisions/0001-running-en-la-web-aplazado.md`).
- [ ] Suscripción y pantalla de pago.

## Decisiones abiertas

- Días sin entrenar para «vuelta tras un parón» (RN-04).
- Desempate de «lo que toca» y rutinas sin probar (RN-02).
- Fórmula del 1RM estimado y criterio de récord (RN-21).
- Cómo el esfuerzo percibido ajusta las propuestas (RN-20).
- Origen y contenido del catálogo de ejercicios.
- Running en la web.

## Propuestas para @dmaster/ui

Ninguna todavía.

## Contradicciones detectadas

Ninguna todavía.

## Riesgos

- El catálogo de ejercicios (180 en el diseño, con fotos y pasos) es contenido que hay que conseguir o crear; sin él, Ejercicios y la ficha no pueden completarse.
