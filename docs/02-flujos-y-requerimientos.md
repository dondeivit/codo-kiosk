# Flujos y requerimientos

## 3. Actores y flujos

Hay tres actores humanos y uno externo. El flujo central es lineal: **elegir → personalizar → revisar → pagar → preparar → entregar**.

| Actor             | Dispositivo                       | Qué hace                                                               |
| ----------------- | --------------------------------- | ---------------------------------------------------------------------- |
| Cliente           | Kiosko (tu iPad)                  | Arma y paga su pedido sin hablar con nadie                             |
| Barista           | Pantalla de cocina (PC o tablet)  | Ve la cola de pedidos pagados y los marca como en preparación o listos |
| Administrador     | Navegador en PC                   | Mantiene el menú, precios, modificadores y disponibilidad              |
| Proveedor de pago | Terminal SumUp Solo (o simulador) | Cobra la tarjeta y avisa al backend el resultado                       |

**Flujo del cliente (kiosko)**

1. **Reposo:** el producto destacado ocupa la pantalla, con un botón "Iniciar". Arriba, una franja con el logo, "Formas de pago" e "Idioma".
2. **Categorías:** una grilla de íconos de línea, cada uno con su nombre y una bajada corta (ej. "Bebidas puristas: sin esencia ni azúcar añadido").
3. **Productos:** dentro de la categoría, un carrusel horizontal agrupado por subcategorías. Arriba, íconos para cambiar de categoría y una fila de subcategorías que se marca sola al deslizar.
4. **Detalle:** elige tamaño, leche, nivel de dulzor y extras; puede escribir una nota y cambiar la cantidad. Al tocar "Agregar" vuelve al carrusel en la misma posición y el carro se actualiza; el carro no se abre solo.
5. Repite 3 y 4 las veces que quiera. Cuando termina, toca "Finalizar".
6. **¿Consumir aquí o para llevar?:** una pantalla con dos botones.
7. **Nombre:** "Te llamaremos por tu nombre (o apodo)", con teclado en pantalla y "Ok".
8. **Resumen y pago:** pantalla dividida. A la izquierda, el pedido (editable) y productos sugeridos; a la derecha, el total y "Pagar".
9. **Pagando:** el kiosko muestra el monto, un dibujo del terminal y el estado en vivo ("Conectando con el terminal…", "Acerca tu tarjeta", "Procesando…").
10. **Resultado:** si se aprueba, "Pedido recibido, gracias" con su nombre y número, y un botón "Hacer un nuevo pedido". Si se rechaza o cancela, vuelve al resumen con el pedido intacto.
11. Tras unos segundos sin interacción, el kiosko vuelve a reposo y vacía el carro.

**Flujo del barista**

1. Inicia sesión con un PIN.
2. Ve una columna "Nuevos" que se llena sola cuando entra un pedido pagado.
3. Toca un pedido para pasarlo a "Preparando" y luego a "Listo".
4. Al marcar "Listo", llama al cliente por su nombre y el pedido sale de la cola tras entregarse.

**Flujo del administrador**

1. Inicia sesión con correo y contraseña.
2. Crea o edita categorías, productos, precios, imágenes y grupos de modificadores.
3. Marca un producto como "agotado" y desaparece del kiosko al instante.

## 4. Requerimientos funcionales

Cada requerimiento tiene un ID para referenciarlo en issues y commits (por ejemplo `feat(cart): RF-CAR-03`). Prioridad: **M** = obligatorio en el MVP, **D** = deseable, si alcanza el tiempo.

| ID        | Requerimiento                                                                                                                              | Prioridad |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| RF-CAT-01 | El kiosko muestra las categorías activas, ordenadas según el admin                                                                         | M         |
| RF-CAT-02 | El kiosko muestra los productos de una categoría con nombre, imagen y precio base                                                          | M         |
| RF-CAT-03 | Los productos agotados o inactivos no se muestran en el kiosko                                                                             | M         |
| RF-MOD-01 | Un producto puede tener grupos de modificadores (Tamaño, Leche, Extras)                                                                    | M         |
| RF-MOD-02 | Cada grupo define si es obligatorio y el mínimo/máximo de opciones (ej. Leche: elegir 1; Extras: 0 a 3)                                    | M         |
| RF-MOD-03 | Cada opción puede sumar un precio (ej. leche de avena +$500)                                                                               | M         |
| RF-MOD-04 | El precio del ítem se recalcula en vivo al elegir opciones                                                                                 | M         |
| RF-CAR-01 | El cliente agrega un ítem personalizado al carro                                                                                           | M         |
| RF-CAR-02 | El cliente modifica cantidad o elimina ítems del carro                                                                                     | M         |
| RF-CAR-03 | El carro muestra subtotal por ítem y total del pedido                                                                                      | M         |
| RF-CAR-04 | El carro se vacía tras 60 segundos de inactividad, con aviso previo                                                                        | D         |
| RF-PED-01 | Al confirmar, el backend crea el pedido y recalcula el total con los precios de la base de datos (nunca confía en el precio del cliente)   | M         |
| RF-PED-02 | Cada pedido recibe un número corto y legible del día (ej. #042)                                                                            | M         |
| RF-PED-03 | El pedido guarda una copia del nombre y precio de cada producto y opción al momento de la compra                                           | M         |
| RF-PAG-01 | Al pagar, el backend inicia el cobro en el proveedor de pago configurado                                                                   | M         |
| RF-PAG-02 | El kiosko muestra el estado del pago en tiempo real (esperando, aprobado, rechazado)                                                       | M         |
| RF-PAG-03 | El cliente puede cancelar mientras el terminal espera la tarjeta                                                                           | D         |
| RF-PAG-04 | Solo los pedidos con pago aprobado llegan al barista                                                                                       | M         |
| RF-BAR-01 | La pantalla del barista recibe los pedidos pagados sin recargar la página                                                                  | M         |
| RF-BAR-02 | El barista cambia el estado: Nuevo → Preparando → Listo → Entregado                                                                        | M         |
| RF-BAR-03 | Cada pedido muestra número, nombre, si es para consumir aquí o llevar (taza o vaso), ítems con sus opciones y notas, y tiempo transcurrido | M         |
| RF-BAR-04 | Suena un aviso al llegar un pedido nuevo                                                                                                   | D         |
| RF-ADM-01 | El admin crea, edita, ordena y desactiva categorías                                                                                        | M         |
| RF-ADM-02 | El admin crea y edita productos con imagen, precio y grupos de modificadores                                                               | M         |
| RF-ADM-03 | El admin marca un producto como agotado y el kiosko lo oculta al instante                                                                  | M         |
| RF-ADM-04 | El admin ve los pedidos del día con su estado y total                                                                                      | D         |
| RF-ADM-05 | El admin ve reportes por rango de fechas (semana, mes); el cierre diario está en 4.1                                                       | D         |
| RF-AUT-01 | Admin y barista requieren autenticación; el kiosko usa un token de dispositivo                                                             | M         |

**RF-PWA-01 (M):** el kiosko incluye `manifest.json` (nombre, ícono, `display: standalone`, orientación) y se instala en el iPad como web app. **RF-PWA-02 (M):** el kiosko pide Screen Wake Lock al iniciar y lo vuelve a pedir si se libera. **RF-PWA-03 (D):** un service worker guarda en caché la interfaz y las imágenes del menú para que la carga sea instantánea.

### 4.1 Cierre del día

Al terminar cada jornada, el admin ve un resumen de lo vendido, lo compara con el panel de SumUp y lo "cierra": desde ese momento el resumen queda guardado y no cambia. La boleta no se genera aquí: con pago con tarjeta, el voucher del proveedor tiene validez como boleta electrónica ([SII](https://www.sii.cl/noticias/2021/050121noti01er.htm)). El cierre es la mirada de negocio sobre esas mismas ventas.

**Qué muestra**

| Indicador                   | Cómo se calcula                                                                                                          |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Total vendido               | Suma de `Order.total` de los pedidos pagados del día (estados `PAID`, `PREPARING`, `READY`, `DELIVERED`)                 |
| Cantidad de pedidos         | Conteo de esos mismos pedidos                                                                                            |
| Ticket promedio             | Total vendido ÷ cantidad de pedidos                                                                                      |
| Pagos fallidos o cancelados | Conteo de `Payment` con estado `FAILED`; sirve para detectar problemas con el terminal                                   |
| Ventas por producto         | Unidades e ingresos por `OrderItem.productName`, de mayor a menor                                                        |
| Ventas por modificador      | Cuántas veces se eligió cada opción (ej. leche de avena) y cuánto sumó                                                   |
| Ventas por hora             | Pedidos e ingresos agrupados por hora de pago, para ver las horas punta                                                  |
| Conciliación                | Suma de `Payment.amount` con estado `SUCCEEDED` contra el total vendido; si no coinciden, se marca la diferencia en rojo |

**Reglas**

- **El día comercial es el de Chile, no el de UTC.** `businessDate` se calcula con la zona `America/Santiago` al crear el pedido. Si no, las ventas después de las 21:00 caerían en el día siguiente.
- **Se calcula sobre las copias del pedido** (`productName`, `unitPrice`), no sobre el catálogo actual: si el latte sube de precio mañana, el cierre de hoy no cambia.
- **Cerrar el día guarda una foto** en la tabla `DailyClosing`, con quién cerró y cuándo. Un día se cierra una sola vez.
- **Si llega un pago después del cierre** (por ejemplo, un webhook atrasado), el admin ve un aviso de "día cerrado con diferencias" en vez de alterar el registro guardado.

**Pantalla:** en el admin, "Cierre del día", con un selector de fecha, cuatro indicadores arriba (total, pedidos, ticket promedio, pagos fallidos), un gráfico de barras por hora, la tabla por producto, el bloque de conciliación y el botón "Cerrar día".

**API**

| Método | Ruta                                   | Para qué                              |
| ------ | -------------------------------------- | ------------------------------------- |
| GET    | `/admin/reports/daily?date=2026-11-24` | Calcular el resumen en vivo de un día |
| POST   | `/admin/closings`                      | Cerrar un día (fecha + nota opcional) |
| GET    | `/admin/closings`                      | Historial de cierres                  |
| GET    | `/admin/reports/daily.csv?date=…`      | Exportar el detalle a CSV             |

**Tabla nueva**

```prisma
model DailyClosing {
  id            String   @id @default(uuid())
  businessDate  DateTime @db.Date @unique
  ordersCount   Int
  totalSales    Int
  paymentsTotal Int
  closedById    String
  closedBy      User     @relation(fields: [closedById], references: [id])
  closedAt      DateTime @default(now())
  notes         String?
}
// En User se agrega: closings DailyClosing[]
```

**Ejemplo de consulta: ventas por producto**

```sql
SELECT oi."productName",
       SUM(oi.quantity)    AS unidades,
       SUM(oi."lineTotal") AS ingresos
FROM "OrderItem" oi
JOIN "Order" o ON o.id = oi."orderId"
WHERE o."businessDate" = $1
  AND o.status IN ('PAID', 'PREPARING', 'READY', 'DELIVERED')
GROUP BY oi."productName"
ORDER BY ingresos DESC;
```

Para ventas por hora, Prisma guarda las fechas en UTC sin zona horaria, así que se convierte en dos pasos: `date_trunc('hour', (o."paidAt" AT TIME ZONE 'UTC') AT TIME ZONE 'America/Santiago')`. Escribir estas consultas en SQL con `$queryRaw` es buen ejercicio; Prisma no cubre bien las agrupaciones por hora.

**Requerimientos del cierre**

| ID        | Requerimiento                                                                                      | Prioridad |
| --------- | -------------------------------------------------------------------------------------------------- | --------- |
| RF-CIE-01 | El admin ve el resumen de cualquier día: total, pedidos, ticket promedio y pagos fallidos          | M         |
| RF-CIE-02 | El resumen incluye ventas por producto                                                             | M         |
| RF-CIE-03 | El resumen compara el total de pedidos pagados con el total de pagos aprobados y marca diferencias | M         |
| RF-CIE-04 | El admin cierra el día y se guarda un registro inmutable con autor y fecha                         | M         |
| RF-CIE-05 | Gráfico de ventas por hora y tabla de ventas por modificador                                       | D         |
| RF-CIE-06 | Exportar el detalle del día a CSV                                                                  | D         |
| RF-CIE-07 | Aviso si llegan pagos a un día ya cerrado                                                          | D         |

### 4.2 Cierre del pedido en el kiosko

Desde "Finalizar" hasta el resultado del pago hay cinco pantallas (K5 a K9 en la sección 10). El pedido se crea en el backend recién al tocar "Pagar"; antes de eso solo existe el carro en el kiosko.

| ID        | Requerimiento                                                                                                                                                                                                 | Prioridad |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| RF-FLU-01 | "Agregar" devuelve al cliente al carrusel en la misma posición y actualiza el carro con una animación; el carro no se abre solo                                                                               | M         |
| RF-FLU-02 | "Finalizar" pregunta "¿Consumir aquí o para llevar?" en una pantalla con dos botones                                                                                                                          | M         |
| RF-FLU-03 | El kiosko pide un nombre o apodo obligatorio, de máximo 20 caracteres, con teclado en pantalla                                                                                                                | M         |
| RF-FLU-04 | El resumen muestra el pedido editable a la izquierda y el total y "Pagar" a la derecha; el tipo de consumo se puede cambiar ahí                                                                               | M         |
| RF-FLU-05 | El resumen sugiere productos marcados como sugeridos que no estén en el carro. Los simples se agregan con un toque (varias veces si se quiere); los que tienen opciones abren su detalle y vuelven al resumen | M         |
| RF-FLU-06 | La pantalla de pago muestra el monto, un dibujo del terminal y el estado en vivo: conectando, acerca tu tarjeta, procesando                                                                                   | M         |
| RF-FLU-07 | La confirmación muestra el nombre en grande, el número de pedido y "Hacer un nuevo pedido"                                                                                                                    | M         |
| RF-FLU-08 | Vaciar el pedido desde el resumen, con confirmación                                                                                                                                                           | D         |
| RF-FLU-09 | "Formas de pago" abre una ventana informativa (tarjetas aceptadas, sin efectivo)                                                                                                                              | D         |
| RF-FLU-10 | Selector de idioma español/inglés en todas las pantallas del cliente (react-i18next)                                                                                                                          | D         |
| RF-FLU-11 | Información nutricional en el detalle del producto                                                                                                                                                            | D         |
| RF-FLU-12 | Código de descuento en el resumen                                                                                                                                                                             | v2        |

**Mapeo del estado de pago a la Cloud API de SumUp:** "Conectando con el terminal…" mientras el backend crea el cobro; "Acerca tu tarjeta" cuando SumUp acepta el cobro; "Procesando…" tras unos segundos; resultado final cuando llega el webhook. Si el terminal no acepta el cobro, se muestra "El terminal no responde, avisa al personal" y se vuelve al resumen.

## 5. Requerimientos no funcionales

Son los que separan un proyecto de juguete de uno profesional: cada uno se puede verificar.

| ID     | Categoría      | Requerimiento                                                                      | Cómo se verifica                                     |
| ------ | -------------- | ---------------------------------------------------------------------------------- | ---------------------------------------------------- |
| RNF-01 | Rendimiento    | Un pedido pagado aparece en la pantalla del barista en menos de 2 s                | Test E2E con cronómetro                              |
| RNF-02 | Rendimiento    | El kiosko carga el menú en menos de 1,5 s en el iPad por Wi-Fi                     | Lighthouse / DevTools                                |
| RNF-03 | Usabilidad     | Botones táctiles de al menos 48×48 px; texto base de 18 px o más en el kiosko      | Revisión visual en el iPad                           |
| RNF-04 | Usabilidad     | Un cliente nuevo completa un pedido simple en menos de 60 s sin ayuda              | Prueba con 3 personas                                |
| RNF-05 | Usabilidad     | El kiosko funciona en orientación vertical y horizontal                            | Revisión en el iPad                                  |
| RNF-06 | Confiabilidad  | Si se corta la conexión, el kiosko lo indica y no permite pagar                    | Test manual con Wi-Fi apagado                        |
| RNF-07 | Confiabilidad  | Un pedido nunca queda marcado como pagado sin confirmación del proveedor           | Test de integración del flujo de pago                |
| RNF-08 | Confiabilidad  | Un webhook de pago repetido no duplica pedidos ni cambios de estado (idempotencia) | Test de integración                                  |
| RNF-09 | Seguridad      | Las claves de SumUp viven solo en el backend, en variables de entorno              | Revisión de código                                   |
| RNF-10 | Seguridad      | Contraseñas con hash (argon2 o bcrypt); endpoints de admin protegidos por rol      | Tests de la API                                      |
| RNF-11 | Seguridad      | Toda entrada de la API se valida con un esquema (Zod)                              | Tests de la API                                      |
| RNF-12 | Mantenibilidad | TypeScript estricto en frontend y backend; tipos compartidos entre ambos           | CI: `tsc --noEmit`                                   |
| RNF-13 | Mantenibilidad | Cobertura de tests de al menos 70 % en la lógica de dominio (precios, estados)     | CI: reporte de cobertura                             |
| RNF-14 | Modularidad    | El proveedor de pago se cambia por configuración, sin tocar el resto del código    | Correr la app con `PAYMENT_PROVIDER=fake` y `=sumup` |
| RNF-15 | Portabilidad   | Todo el sistema levanta con un solo comando (`docker compose up`)                  | README probado en una máquina limpia                 |

Montos en pesos chilenos como enteros (CLP no usa decimales); nunca números con coma flotante para dinero.

**RNF-16 · Marca intercambiable:** colores, tipografías, radios y espaciados se definen una sola vez como tokens (variables CSS en el `@theme` de Tailwind); los componentes usan nombres como `primary`, `surface` o `accent`, nunca colores sueltos. El nombre, el logo y los textos de marca viven en un archivo de configuración. Se verifica cambiando la marca provisoria ("codo.") por otra sin tocar ningún componente.
