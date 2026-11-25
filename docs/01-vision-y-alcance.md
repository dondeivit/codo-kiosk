# Visión y alcance

## 1. Visión y objetivos

Construir un sistema de autoatención minimalista para una cafetería, inspirado en The Coffee: el cliente pide y paga en una tablet y el pedido llega al instante a la pantalla del barista. Es un proyecto de aprendizaje y de portafolio, no un producto comercial.

**Objetivos de aprendizaje**

- Dominar React con TypeScript construyendo tres interfaces reales (kiosko, barista, admin).
- Diseñar y programar un backend propio: API REST, base de datos relacional, autenticación y tiempo real.
- Integrar un proveedor de pago externo detrás de una interfaz propia, con un simulador para desarrollo.
- Recorrer el ciclo completo de un proyecto: requerimientos, diseño, implementación, pruebas, CI y despliegue.

**Objetivo de producto**

Un núcleo modular (catálogo, pedidos, pagos, cocina) que hoy sirve a una cafetería y mañana se pueda adaptar a otro rubro, como una hamburguesería, sin reescribirlo.

**Criterio de éxito del MVP**

Desde el iPad, un cliente arma un pedido con un café personalizado y un croissant, lo paga con el pago simulado y el pedido aparece en la pantalla del barista en menos de 2 segundos. Todo esto corre en una demo pública con un README que lo explica.

## 2. Alcance

El MVP replica el flujo de The Coffee para **un solo local**: pedir, personalizar, pagar y preparar. Todo lo demás queda fuera hasta que el MVP esté terminado y desplegado.

| Área     | MVP (v1)                                                                                                                        | Después (v2+)                                                                      | Fuera de alcance                     |
| -------- | ------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------ |
| Catálogo | Categorías con subcategorías, productos, íconos, modificadores (leche, tamaño, extras, niveles), producto destacado y sugeridos | Disponibilidad por horario, combos                                                 | Menú por sucursal                    |
| Pedido   | Carro, consumir aquí o llevar, nombre, nota por producto, resumen con sugeridos, número de pedido                               | Código de descuento, propina                                                       | Pedidos por app móvil del cliente    |
| Pago     | Pago simulado detrás de una interfaz `PaymentProvider`                                                                          | SumUp real (terminal o checkout)                                                   | Boleta electrónica SII, otros medios |
| Barista  | Cola de pedidos en tiempo real, cambio de estado                                                                                | Sonido al llegar un pedido, tiempos de preparación                                 | Impresora de comandas                |
| Admin    | CRUD de productos, precios, modificadores, activar/desactivar; cierre del día                                                   | Reportes por rango de fechas, stock simple, referencia a boletas (\`TaxDocument\`) | Multi-local, roles avanzados         |
| Cliente  | Pantalla "tu pedido está listo" opcional                                                                                        | Programa de fidelización                                                           | Cuentas de cliente                   |

**Modularidad desde el día 1:** cada área es un módulo del backend con su propia carpeta, rutas y tablas. Cambiar de cafetería a hamburguesería debe ser cambiar datos (catálogo y modificadores), no código.

**El kiosko es una PWA desde el MVP:** se instala en el iPad desde Safari ("Agregar a pantalla de inicio"), abre a pantalla completa con su propio ícono, mantiene la pantalla encendida y queda bloqueado con Acceso Guiado. No pasa por la App Store ni requiere un Mac.
