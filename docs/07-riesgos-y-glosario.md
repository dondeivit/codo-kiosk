# Riesgos, decisiones y glosario

## 14. Riesgos, decisiones abiertas y glosario

**Riesgos**

| Riesgo                                   | Impacto | Mitigación                                                                                |
| ---------------------------------------- | ------- | ----------------------------------------------------------------------------------------- |
| Querer hacerlo todo y no terminar        | Alto    | Respetar el alcance de la sección 2; lo nuevo va a una lista "v2"                         |
| Aprender React, NestJS y Prisma a la vez | Medio   | Fase 1 con el mínimo de cada uno; profundizar en cada fase                                |
| El webhook de SumUp no llega a tu PC     | Medio   | Túnel HTTPS en desarrollo; endpoint `GET /orders/:id` como respaldo                       |
| Cambios en la API de SumUp               | Bajo    | Todo el código de SumUp aislado en un solo adaptador                                      |
| Usar marca ajena en un repo público      | Medio   | Nombre, logo y colores propios; mencionar a The Coffee solo como inspiración en el README |

**Decisiones abiertas**

- [ ] Nombre y marca propios del proyecto (y del café ficticio de la demo).
- [ ] ¿Cuántas horas por semana? Ajusta las fechas de la sección 11.
- [ ] NestJS o Fastify para el backend (recomendado: NestJS).
- [ ] ¿Pantalla pública de "pedidos listos" en el MVP o en v2?
- [ ] ¿Comprar un Solo físico o quedarse en sandbox + Virtual Solo?

**Ya decidido:** marca provisoria "codo." (café & doughnuts); la identidad completa se trabaja después del MVP. Sesiones en vez de JWT; PostgreSQL + Prisma con backend propio; kiosko como PWA.

**Glosario**

| Término      | Significado                                                                |
| ------------ | -------------------------------------------------------------------------- |
| Kiosko       | Pantalla táctil donde el cliente pide y paga solo                          |
| KDS          | Kitchen Display System: la pantalla del barista                            |
| Modificador  | Opción que cambia un producto (leche, tamaño, extra) y puede sumar precio  |
| Webhook      | Llamada HTTP que un servicio externo hace a tu backend para avisar algo    |
| Idempotencia | Procesar el mismo mensaje dos veces produce el mismo resultado que una vez |
| ADR          | Documento corto que registra una decisión técnica y su porqué              |
| PWA          | Web instalable que se abre a pantalla completa como una app                |

**Fuentes**

- [SumUp — Cloud API](https://developer.sumup.com/terminal-payments/cloud-api)
- [SumUp — lector Solo y países disponibles](https://developer.sumup.com/terminal-payments/readers/solo/)
- [SumUp — Virtual Solo](https://virtual-solo.sumup.com)
- [Diario Financiero — The Coffee y sus tablets de autoatención](https://www.df.cl/senal-df/el-deal/the-coffee-la-cafeteria-de-estilo-japones-que-conquista-chile)
