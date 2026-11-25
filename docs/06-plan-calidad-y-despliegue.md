# Plan, calidad y despliegue

## 11. Plan de trabajo

Siete fases cortas, cada una con algo que funciona al final. El orden sigue el recorrido del pedido: primero el menú, después el carro, luego el pago y la cocina, y al final el admin. Así, desde la semana 3 ya tienes algo que mostrar en el iPad.

| Fase                                     | Semanas        | Entregable                                                                                                               | Control al terminar                               |
| ---------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------- |
| 0 · Preparación                          | 1              | Monorepo con pnpm, Tailwind, ESLint, Prettier, CI en GitHub Actions; documentación en `docs/`                            | El CI corre en verde en cada PR                   |
| 1 · Catálogo                             | 2 y 3          | Kiosko: reposo, categorías, carrusel por subcategoría y detalle con modificadores (K1 a K4), primero con datos de prueba | Se navega el menú completo en el iPad             |
| 2 · Carro y pedidos                      | 4 y 5          | Carro con Zustand y cálculo de precios con tests; llevar o servir, nombre y resumen con sugeridos (K5 a K7)              | Un pedido completo se arma sin errores            |
| 3 · Backend, pago simulado y tiempo real | 6 y 7          | API con NestJS y Prisma, `FakePaymentProvider`, Socket.IO, pantallas de pago y resultado (K8 y K9), pantalla del barista | Un pedido pagado llega al barista en menos de 2 s |
| 4 · Admin y autenticación                | 8 y 9          | Login, PIN, tokens de dispositivo; CRUD del menú, agotado en vivo y cierre del día                                       | El admin cambia el menú y el kiosko lo refleja    |
| 5 · Calidad y despliegue (MVP)           | 10 y 11        | Tests E2E con Playwright, demo pública, README con GIF                                                                   | Demo pública funcionando desde el iPad            |
| 6 · SumUp real                           | 12 en adelante | Sandbox y Virtual Solo, webhook con túnel HTTPS, cancelación                                                             | Pago de prueba aprobado en el sandbox             |

Las semanas suponen unas 10 horas por semana; si tienes más o menos tiempo, se ajustan. Cada fase se trabaja como un milestone de GitHub, con issues que referencian los IDs de la sección 4.

## 12. Pruebas, calidad y CI

Se prueba más donde un error cuesta plata: precios, estados del pedido y pagos. Cada push a GitHub corre lint, tipos y tests; no se mezcla a `main` con el CI en rojo.

| Nivel       | Herramienta                             | Qué cubre                              | Ejemplos                                                                                            |
| ----------- | --------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Unitario    | Vitest                                  | Reglas de dominio en `packages/shared` | Precio con leche de avena + shot extra; transición inválida rechazada; grupo obligatorio sin opción |
| Integración | Vitest + Supertest + Postgres en Docker | Endpoints con base de datos real       | Crear pedido recalcula el total; webhook repetido no duplica                                        |
| Componentes | Testing Library                         | Pantallas del kiosko aisladas          | El botón "Agregar" muestra el precio actualizado                                                    |
| E2E         | Playwright                              | Flujo completo en navegador            | Pedir en el kiosko → pagar con el pago simulado → ver el pedido en la pantalla del barista          |

**Pipeline de GitHub Actions**

1. Instalar dependencias con caché de pnpm.
2. `eslint` + `prettier --check`.
3. `tsc --noEmit` en todos los paquetes.
4. Tests unitarios y de integración (con un servicio Postgres en el job).
5. Tests E2E con Playwright contra el stack levantado.
6. Si todo pasa en `main`: despliegue automático.

**Prácticas de repositorio**

- Ramas por issue (`feat/RF-CAR-01-agregar-al-carro`), pull requests aunque trabajes solo.
- Commits convencionales (`feat:`, `fix:`, `test:`, `docs:`).
- Un ADR (Architecture Decision Record) corto por cada decisión importante, por ejemplo "por qué NestJS" o "por qué webhooks y no polling".

## 13. Entorno con el iPad, despliegue y demo

El iPad no necesita ninguna app nativa: el kiosko es una web que se abre en Safari y se instala como PWA en la pantalla de inicio, a pantalla completa.

**Desarrollo local**

1. En el PC: `docker compose up` levanta Postgres y la API; `pnpm dev` levanta los tres frontends.
2. Vite se inicia con `--host` para que el iPad lo vea en la red local (ej. `http://192.168.1.20:5173`).
3. El PC y el iPad deben estar en la misma red Wi-Fi.
4. Para depurar el iPad desde el PC: inspector web de Safari (requiere un Mac) o un panel de logs propio en la pantalla del kiosko.
5. La pantalla del barista se abre en el PC, o en un celular, en otra pestaña.

**Modo kiosko en el iPad**

- En Safari: Compartir → "Agregar a pantalla de inicio" (requiere un `manifest.json` con `display: standalone`).
- Activar **Acceso Guiado** en Ajustes → Accesibilidad, para que el iPad quede bloqueado en la app como en un local real.
- Bloquear el zoom y la selección de texto en el CSS del kiosko.

**Despliegue de la demo pública**

| Pieza                              | Dónde                                                   | Nota                                         |
| ---------------------------------- | ------------------------------------------------------- | -------------------------------------------- |
| Frontends (kiosko, barista, admin) | Vercel o Netlify                                        | Un proyecto por app o subrutas de un dominio |
| API + WebSocket                    | Railway, Render o Fly.io                                | Debe soportar conexiones persistentes        |
| PostgreSQL                         | Supabase o Neon, usados solo como hosting de PostgreSQL | Seed con un menú de ejemplo al desplegar     |
| Pagos                              | `PAYMENT_PROVIDER=fake`                                 | La demo pública nunca usa SumUp real         |

**Qué debe tener la demo:** un enlace al kiosko y otro a la pantalla del barista para abrir lado a lado, un usuario admin de demo con contraseña en el README, y un botón para reiniciar los datos. Revisa los planes gratuitos de cada proveedor al desplegar, porque cambian seguido.
