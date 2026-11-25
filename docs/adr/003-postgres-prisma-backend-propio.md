# ADR 003 · PostgreSQL + Prisma con backend propio, no Supabase completo

**Estado:** aceptada · noviembre 2026

## Contexto

Supabase ofrece PostgreSQL con una API REST generada automáticamente, autenticación y tiempo real. La alternativa es escribir un backend propio en NestJS que use PostgreSQL a través de Prisma.

## Decisión

- Backend propio en NestJS, con PostgreSQL y Prisma.
- En desarrollo, PostgreSQL corre en Docker.
- En la demo pública, Supabase o Neon se usan solo como hosting de PostgreSQL, conectados con `DATABASE_URL`.

## Razones

- El objetivo del proyecto es aprender a construir un backend: diseñar endpoints, validar datos, manejar sesiones y emitir eventos. Supabase completo genera justamente esas partes.
- El login de Supabase choca con la decisión de usar sesiones propias (ADR 002).
- Usar el proveedor solo como base de datos evita depender de él: cambiar de proveedor es cambiar una variable de entorno.

## Consecuencias

- Hay más código que escribir y mantener que con Supabase completo.
- Las migraciones quedan versionadas en el repositorio con Prisma.
- El proyecto muestra en el portafolio un backend completo hecho a mano.
