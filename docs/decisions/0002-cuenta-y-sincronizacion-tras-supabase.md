# 0002 · Cuenta y sincronización, cuando llegue Supabase

- Estado: aceptada
- Fecha: 2026-11-05

## Contexto
El diseño incluye cuenta opcional (Apple, Google o email), recuperación de contraseña, sincronización entre dispositivos, notificaciones, conexión con Apple Salud y «Empezar en el móvil». Todo eso necesita Supabase o la app de iOS, que todavía no existen. Mostrar esas opciones sin que funcionen va contra la regla de no tener botones inoperativos.

## Decisión
La primera versión es local: funciona sin cuenta, como si el usuario hubiera elegido «Ahora no». Las piezas que dependen de Supabase o de iOS no se implementan ni se muestran hasta que existan.

## Consecuencias
- Fuera de la primera versión: el paso «Guarda tu plan» del onboarding, M2 completo (registro, inicio de sesión, recuperación, «Hola de nuevo»), cambiar contraseña, cerrar sesión, eliminar cuenta, sincronización y su aviso «Sin conexión», notificaciones, Apple Salud y «Empezar en el móvil».
- Perfil muestra solo los grupos que funcionan en local: entrenamiento, durante la sesión, apariencia e idioma, y tus datos (exportar e importar).
- Las páginas legales y el aviso de cookies llegan con la cuenta o cuando haya algo que consentir.
- El dominio y los datos ya usan UUID y UTC para que la sincronización no obligue a migrar.
