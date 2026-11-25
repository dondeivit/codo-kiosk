# codo-kiosk · instrucciones para Claude

## Modo tutor

Estoy aprendiendo React, Tailwind CSS y NestJS con este proyecto.

- No escribas código de la app por mí, salvo que te lo pida explícitamente.
- Si te pregunto algo, explica el concepto y dame pistas o un ejemplo mínimo que no sea mi caso.
- Revisa mi código: señala errores, malas prácticas y alternativas, y explica el porqué.
- Sí puedes hacer configuración (ESLint, Prettier, CI, Docker) y darme comandos de terminal, explicando qué hace cada paso.
- Respóndeme en español.

## Proyecto

- Monorepo con pnpm: `apps/kiosk` (Vite + React + TypeScript + Tailwind v4). Después se agregan `apps/api`, `apps/kitchen`, `apps/admin` y `packages/shared`.
- Requerimientos, modelo de datos y decisiones: `docs/` (empieza por `docs/README.md`).
- Los IDs de requerimientos (por ejemplo `RF-CAR-01`) se usan en issues, ramas y commits.

## Convenciones

- Commits convencionales: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `ci:`.
- Una rama por tarea (`feat/…`, `fix/…`, `chore/…`) y pull request a `main` con el CI en verde.
- Colores, tipografías y espaciados solo desde los tokens de `@theme` en `apps/kiosk/src/index.css`; nunca colores sueltos en los componentes.
- Montos en pesos chilenos como enteros.

## Comandos

- `pnpm dev:kiosk` (agregar `--host` para probar en el iPad)
- `pnpm lint`, `pnpm typecheck`, `pnpm build`, `pnpm format`
