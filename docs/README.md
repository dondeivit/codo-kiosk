# Documentación de codo-kiosk

Kiosko de autoatención para una cafetería: el cliente pide y paga en un iPad y el pedido llega al instante a la pantalla del barista. Proyecto de aprendizaje y portafolio.

| Documento                                                          | Contenido                                                                               |
| ------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| [01 · Visión y alcance](01-vision-y-alcance.md)                    | Objetivos, criterio de éxito del MVP y qué queda fuera                                  |
| [02 · Flujos y requerimientos](02-flujos-y-requerimientos.md)      | Actores, flujos, requerimientos funcionales (RF) y no funcionales (RNF), cierre del día |
| [03 · Modelo de datos](03-modelo-de-datos.md)                      | Entidades, diagrama ER, esquema Prisma y reglas de integridad                           |
| [04 · Arquitectura, API y pagos](04-arquitectura-api-y-pagos.md)   | Integración con SumUp, stack, estructura del repo, endpoints y eventos                  |
| [05 · Diseño UI/UX](05-diseno-ux.md)                               | Pantallas K1 a K9, ciclo de vida del kiosko, navegación y detalle de producto           |
| [06 · Plan, calidad y despliegue](06-plan-calidad-y-despliegue.md) | Fases, pruebas, CI, entorno con el iPad y despliegue                                    |
| [07 · Riesgos, decisiones y glosario](07-riesgos-y-glosario.md)    | Riesgos, decisiones abiertas y glosario                                                 |

## Decisiones de arquitectura (ADR)

| ADR                                              | Decisión                                                     |
| ------------------------------------------------ | ------------------------------------------------------------ |
| [001](adr/001-pwa-no-app-nativa.md)              | El kiosko es una PWA, no una app nativa                      |
| [002](adr/002-sesiones-no-jwt.md)                | Sesiones en la base de datos en vez de JWT                   |
| [003](adr/003-postgres-prisma-backend-propio.md) | PostgreSQL + Prisma con backend propio, no Supabase completo |

## Diseño

Lienzo con todas las pantallas y el prototipo navegable: [Kiosko café — pantallas](https://claude.ai/artifact/KJppSFeQ8cbSgRUcWQ9SYH). Las capturas exportadas van en `docs/design/`.

La marca **codo.** es provisoria; la identidad completa se trabaja después del MVP.
