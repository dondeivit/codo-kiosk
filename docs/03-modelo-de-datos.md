# Modelo de datos

## 6. Modelo de dominio

El modelo separa **qué se vende** (catálogo) de **qué se vendió** (pedidos). Por eso un pedido copia nombres y precios: si mañana sube el precio del latte, los pedidos de ayer no cambian.

**Catálogo**

| Entidad        | Campos principales                                                                                                     | Relaciones                                                                              |
| -------------- | ---------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Category       | id, parentId, name, subtitle, icon, sortOrder, active                                                                  | tiene muchos Product; puede tener una categoría padre (así se forman las subcategorías) |
| Product        | id, name, description, imageUrl, basePrice, sizeMl, temperature (caliente/frío), isFeatured, isUpsell, active, soldOut | pertenece a Category; tiene muchos ModifierGroup (vía ProductModifierGroup)             |
| ModifierGroup  | id, name ("Leche"), icon, required, minSelect, maxSelect                                                               | tiene muchas ModifierOption; se reutiliza entre productos                               |
| ModifierOption | id, name ("Avena"), priceDelta, isDefault, active                                                                      | pertenece a ModifierGroup                                                               |

**Pedidos y pagos**

| Entidad           | Campos principales                                                                                    | Relaciones                                        |
| ----------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| Order             | id, number (#042), customerName, orderType (consumir aquí / llevar), status, total, createdAt, paidAt | tiene muchos OrderItem y uno o más Payment        |
| OrderItem         | id, productId, productName, unitPrice, quantity, lineTotal, note                                      | pertenece a Order; tiene muchas OrderItemModifier |
| OrderItemModifier | id, optionId, groupName, optionName, priceDelta                                                       | pertenece a OrderItem (copia, no referencia viva) |
| Payment           | id, provider (fake / sumup), providerRef (client\_transaction\_id), amount, status, failureReason     | pertenece a Order                                 |

**Usuarios y dispositivos**

| Entidad | Campos principales                                                                  |
| ------- | ----------------------------------------------------------------------------------- |
| User    | id, email, passwordHash, pinHash, role (admin / barista), active                    |
| Device  | id, name ("Kiosko 1"), type (kiosk / kitchen), tokenHash, readerId (SumUp asociado) |

**Estados del pedido**

`PENDING_PAYMENT` → `PAID` → `PREPARING` → `READY` → `DELIVERED`. Desde `PENDING_PAYMENT` también puede ir a `PAYMENT_FAILED` (el cliente puede reintentar) o `CANCELLED`. Las transiciones viven en una sola función del dominio que rechaza cualquier salto inválido, como pasar de `PENDING_PAYMENT` a `READY`.

### 6.1 Diagrama entidad-relación

Es una sola base de datos PostgreSQL con 13 tablas; los tres grupos solo indican qué módulo del backend es dueño de cada tabla. El pedido (`Order`) es el centro: sus ítems guardan `productId` y `optionId` como referencia para reportes, pero el nombre y el precio son copias, así que borrar o cambiar el catálogo nunca altera una venta pasada.

```mermaid
erDiagram
  Category ||--o{ Category : "subcategorías"
  Category ||--o{ Product : tiene
  Product ||--o{ ProductModifierGroup : usa
  ModifierGroup ||--o{ ProductModifierGroup : "se reutiliza en"
  ModifierGroup ||--o{ ModifierOption : tiene
  Order ||--o{ OrderItem : contiene
  OrderItem ||--o{ OrderItemModifier : "copia de opciones"
  Order ||--o{ Payment : "se paga con"
  Device ||--o{ Order : crea
  User ||--o{ Session : tiene
  User ||--o{ DailyClosing : cierra

  Category { string id PK
    string parentId FK
    string name
    string subtitle
    string icon }
  Product { string id PK
    string categoryId FK
    string name
    int basePrice
    int sizeMl
    enum temperature
    bool isFeatured
    bool isUpsell
    bool soldOut }
  ModifierGroup { string id PK
    string name
    string icon
    bool required
    int minSelect
    int maxSelect }
  ModifierOption { string id PK
    string groupId FK
    string name
    int priceDelta
    bool isDefault }
  ProductModifierGroup { string productId PK
    string groupId PK
    int sortOrder }
  Order { string id PK
    date businessDate
    int number
    string customerName
    enum orderType
    enum status
    int total
    string deviceId FK }
  OrderItem { string id PK
    string orderId FK
    string productName
    int unitPrice
    int quantity
    int lineTotal
    string note }
  OrderItemModifier { string id PK
    string orderItemId FK
    string groupName
    string optionName
    int priceDelta }
  Payment { string id PK
    string orderId FK
    enum provider
    string providerRef UK
    int amount
    enum status }
  User { string id PK
    string email UK
    string passwordHash
    string pinHash
    enum role }
  Session { string id PK
    string userId FK
    datetime expiresAt }
  Device { string id PK
    string name
    enum type
    string tokenHash UK
    string readerId }
  DailyClosing { string id PK
    date businessDate UK
    int totalSales
    int paymentsTotal
    string closedById FK }
```

Dos relaciones cruzan de grupo y no se dibujaron para que el diagrama se lea mejor: `Order.deviceId` apunta al `Device` que creó el pedido, y `Device.readerId` guarda el ID del lector SumUp emparejado con ese kiosko. La relación producto–grupo de modificadores es de muchos a muchos: "Leche" se define una vez y se reutiliza en todos los cafés.

### 6.2 Creación de la base de datos

La base se define en código con Prisma, y cada cambio queda como una migración SQL versionada en el repositorio. Nadie crea tablas a mano.

1. **Levantar PostgreSQL en Docker** con un volumen para que los datos sobrevivan a reinicios:

```yaml
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_USER: cafe
      POSTGRES_PASSWORD: cafe
      POSTGRES_DB: cafe_kiosk
    ports:
      - '5432:5432'
    volumes:
      - db-data:/var/lib/postgresql/data
volumes:
  db-data:
```

2. **Configurar la conexión** en `apps/api/.env` (nunca se sube a Git; se sube un `.env.example`): `DATABASE_URL="postgresql://cafe:cafe@localhost:5432/cafe_kiosk"`.
3. **Escribir el esquema** en `apps/api/prisma/schema.prisma` (abajo). La configuración del generador y de la conexión se toma de la documentación de la versión de Prisma que instales, porque cambia entre versiones.
4. **Crear la primera migración:** `pnpm prisma migrate dev --name init`. Genera el SQL en `prisma/migrations/` y lo aplica.
5. **Cargar datos de ejemplo:** un script `prisma/seed.ts` crea el menú de la cafetería ficticia (categorías, productos, grupos Tamaño, Leche y Extras), un usuario admin y un dispositivo kiosko. Se corre con `pnpm prisma db seed`.
6. **Revisar los datos** con `pnpm prisma studio`, un panel web para ver y editar tablas.
7. **En producción** se usa `prisma migrate deploy`, que solo aplica migraciones existentes; `migrate dev` es exclusivo de desarrollo.

**Esquema Prisma (modelos)**

```prisma
enum OrderStatus { PENDING_PAYMENT PAYMENT_FAILED PAID PREPARING READY DELIVERED CANCELLED }
enum OrderType { DINE_IN TAKEAWAY }
enum Temperature { HOT COLD }
enum PaymentStatus { PENDING SUCCEEDED FAILED }
enum PaymentProvider { FAKE SUMUP }
enum Role { ADMIN BARISTA }
enum DeviceType { KIOSK KITCHEN }

model Category {
  id        String     @id @default(uuid())
  parentId  String?    // null = categoría; con valor = subcategoría
  parent    Category?  @relation("Subcategories", fields: [parentId], references: [id])
  children  Category[] @relation("Subcategories")
  name      String
  subtitle  String?    // bajada corta: "Sin esencia ni azúcar añadido"
  icon      String?    // nombre del ícono de línea
  sortOrder Int        @default(0)
  active    Boolean    @default(true)
  products  Product[]  // los productos cuelgan de la subcategoría

  @@index([parentId])
}

model Product {
  id             String                 @id @default(uuid())
  categoryId     String
  category       Category               @relation(fields: [categoryId], references: [id])
  name           String
  description    String?
  imageUrl       String?
  basePrice      Int
  sizeMl         Int?
  temperature    Temperature?
  isFeatured     Boolean                @default(false) // aparece en la pantalla de reposo
  isUpsell       Boolean                @default(false) // aparece como sugerido antes de pagar
  active         Boolean                @default(true)
  soldOut        Boolean                @default(false)
  modifierGroups ProductModifierGroup[]

  @@index([categoryId])
}

model ModifierGroup {
  id        String                 @id @default(uuid())
  name      String
  icon      String?
  required  Boolean                @default(false)
  minSelect Int                    @default(0)
  maxSelect Int                    @default(1)
  options   ModifierOption[]
  products  ProductModifierGroup[]
}

model ModifierOption {
  id         String        @id @default(uuid())
  groupId    String
  group      ModifierGroup @relation(fields: [groupId], references: [id])
  name       String
  priceDelta Int           @default(0)
  isDefault  Boolean       @default(false)
  active     Boolean       @default(true)

  @@index([groupId])
}

model ProductModifierGroup {
  productId String
  groupId   String
  sortOrder Int           @default(0)
  product   Product       @relation(fields: [productId], references: [id])
  group     ModifierGroup @relation(fields: [groupId], references: [id])

  @@id([productId, groupId])
}

model Order {
  id           String      @id @default(uuid())
  businessDate DateTime    @db.Date
  number       Int
  customerName String
  orderType    OrderType
  status       OrderStatus @default(PENDING_PAYMENT)
  total        Int
  deviceId     String?
  device       Device?     @relation(fields: [deviceId], references: [id])
  createdAt    DateTime    @default(now())
  paidAt       DateTime?
  items        OrderItem[]
  payments     Payment[]

  @@unique([businessDate, number])
  @@index([status])
}

model OrderItem {
  id          String              @id @default(uuid())
  orderId     String
  order       Order               @relation(fields: [orderId], references: [id], onDelete: Cascade)
  productId   String
  productName String
  unitPrice   Int
  quantity    Int
  lineTotal   Int
  note        String?             // "bien caliente", máximo 80 caracteres
  modifiers   OrderItemModifier[]

  @@index([orderId])
}

model OrderItemModifier {
  id          String    @id @default(uuid())
  orderItemId String
  orderItem   OrderItem @relation(fields: [orderItemId], references: [id], onDelete: Cascade)
  optionId    String
  groupName   String
  optionName  String
  priceDelta  Int
}

model Payment {
  id            String          @id @default(uuid())
  orderId       String
  order         Order           @relation(fields: [orderId], references: [id])
  provider      PaymentProvider
  providerRef   String?         @unique
  amount        Int
  status        PaymentStatus   @default(PENDING)
  failureReason String?
  createdAt     DateTime        @default(now())
  updatedAt     DateTime        @updatedAt

  @@index([orderId])
}

model User {
  id           String         @id @default(uuid())
  email        String         @unique
  passwordHash String
  pinHash      String?
  role         Role
  active       Boolean        @default(true)
  sessions     Session[]
  closings     DailyClosing[]
}

model Session {
  id        String   @id // hash SHA-256 del token que va en la cookie
  userId    String
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  expiresAt DateTime
  createdAt DateTime @default(now())

  @@index([userId])
}

model Device {
  id         String     @id @default(uuid())
  name       String
  type       DeviceType
  tokenHash  String     @unique
  readerId   String?
  lastSeenAt DateTime?
  revokedAt  DateTime?
  createdAt  DateTime   @default(now())
  orders     Order[]
}

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
```

**Reglas de integridad**

| Regla                                                         | Dónde se aplica                                               | Por qué                                                                                                                |
| ------------------------------------------------------------- | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Montos como `Int` en pesos                                    | Esquema                                                       | El CLP no usa decimales; los números con coma flotante redondean mal                                                   |
| `@@unique([businessDate, number])`                            | Esquema                                                       | No puede haber dos pedidos #042 el mismo día. El número se calcula dentro de una transacción, y si choca, se reintenta |
| `providerRef` único                                           | Esquema                                                       | Si SumUp envía el mismo webhook dos veces, el segundo no crea otro pago (idempotencia)                                 |
| Los productos no se borran, se desactivan                     | Código (servicio de catálogo)                                 | Los reportes siguen pudiendo agrupar ventas por `productId`                                                            |
| Borrado en cascada solo dentro de un pedido                   | Esquema                                                       | Borrar un pedido de prueba borra sus ítems; nunca se borra catálogo en cascada                                         |
| Índices en claves foráneas y en `Order.status`                | Esquema                                                       | La cola del barista filtra por estado en cada carga                                                                    |
| `basePrice >= 0`, `quantity > 0`                              | Restricción `CHECK` agregada a mano en el SQL de la migración | Prisma no expresa `CHECK`; la base debe rechazar datos imposibles aunque el código falle                               |
| Mínimo y máximo de opciones por grupo, transiciones de estado | Código de dominio en `packages/shared`, con tests             | Son reglas de negocio que dependen de varias filas                                                                     |
