# ADR 002 · Sesiones en la base de datos en vez de JWT

**Estado:** aceptada · noviembre 2026

## Contexto

Hay tres identidades distintas: el admin (correo y contraseña), el barista (PIN en una pantalla compartida) y los dispositivos (kiosko y cocina, sin persona detrás). Había que elegir entre JWT y sesiones guardadas en la base de datos.

## Decisión

- Admin y barista reciben un identificador aleatorio en una cookie `httpOnly`, `Secure` y `SameSite=Lax`. La sesión vive en la tabla `Session`, que guarda el hash del token.
- Los dispositivos envían un token largo y aleatorio; en la tabla `Device` se guarda solo su hash.
- Contraseñas y PIN se guardan con argon2. Máximo 5 intentos de PIN por minuto.
- Las rutas se protegen con un guard de NestJS y el decorador `@Roles()`. Socket.IO valida la misma cookie o token al conectar.

## Razones

- JWT era viable, pero revocar el acceso de un barista o de un iPad robado con sesiones es borrar una fila; con JWT obliga a armar listas negras o tokens de refresco.
- Hay un solo backend y una sola base de datos, así que no se necesita validar tokens sin consultar la base.
- Una cookie `httpOnly` no se puede leer desde JavaScript, lo que reduce el riesgo de robo del token.

## Consecuencias

- Cada petición autenticada consulta la tabla `Session`; con un solo local, el costo es despreciable.
- Si en el futuro hubiera varios servicios independientes, habría que reevaluar esta decisión.
