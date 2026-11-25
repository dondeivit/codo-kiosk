# ADR 001 · El kiosko es una PWA, no una app nativa

**Estado:** aceptada · noviembre 2026

## Contexto

El kiosko corre en un iPad. Las opciones eran una web en el navegador, una web instalada (PWA) o una app nativa empaquetada con Capacitor o publicada en la App Store.

## Decisión

El kiosko es una aplicación web en React instalada como PWA en la pantalla de inicio del iPad.

## Razones

- El iPad nunca habla directo con el lector SumUp: el cobro lo inicia el backend por la Cloud API. El kiosko solo muestra pantallas y recibe eventos, y eso una web lo hace bien.
- Desde iPadOS 26, todo sitio agregado a la pantalla de inicio abre como web app, sin la interfaz de Safari.
- Las web apps instaladas pueden mantener la pantalla encendida con Screen Wake Lock.
- Con Acceso Guiado, el iPad queda bloqueado en la app como en un local real.
- Una app nativa exige un Mac con Xcode y una cuenta de desarrollador pagada para que no expire a los 7 días.

## Consecuencias

- No hay que pasar por la App Store ni tener un Mac.
- Si más adelante se necesita una app nativa, la misma app de React se empaqueta con Capacitor, sin reescribirla.
- Se depende del soporte de Safari para funciones de PWA; se revisa en cada versión de iPadOS.
