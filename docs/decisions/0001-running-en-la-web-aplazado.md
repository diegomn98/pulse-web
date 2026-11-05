# 0001 · Running en la web, aplazado

- Estado: aceptada
- Fecha: 2026-11-05

## Contexto
El diseño delega las sesiones de running de la web al iPhone («Empezar en el móvil»), porque el GPS lo mide la app nativa. Pero la web es una PWA mobile-first y la app de iOS todavía no existe, así que hoy nadie podría correr con PULSE. La alternativa sería grabar las carreras con la geolocalización del navegador.

## Decisión
Se decide más adelante. Hasta entonces no se implementa running en la web.

## Consecuencias
- El tipo running existe en el dominio y en el esquema de datos, para no tener que migrar después.
- En la interfaz no se ofrece crear, editar ni empezar rutinas de running, ni la carrera libre. El onboarding ofrece solo intervalos y fuerza.
- «Lo que toca», el plan inicial y Progreso funcionan solo con intervalos y fuerza.
- Cuando se decida, se sustituye esta decisión por una nueva que diga cómo se hace (geolocalización del navegador o delegación al iPhone) y se desbloquean M7 · editor de running y M9.
