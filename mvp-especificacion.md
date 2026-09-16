# Especificación del MVP — Moda Local

Basada en los pasos 1-4 (problema, proceso, conexiones, prototipo) y en las decisiones tomadas para esta primera versión:

- Las marcas se dan de alta con **panel propio** (autogestionan su catálogo).
- Un carrito **puede mezclar productos de varias marcas**; el sistema lo divide en un sub-pedido por marca.
- **La plataforma cobra** el pago total y liquida después a cada marca (no hay split automático en v1).
- El envío queda **solo informativo** en el MVP (sin cálculo ni integración con couriers).
- El comprador necesita **cuenta para pagar** (puede explorar sin cuenta).
- Las marcas requieren **aprobación manual** de un administrador antes de publicarse.
- Hay **stock básico por talla**, que se descuenta al confirmarse el pago.
- La marca **puede cancelar** un sub-pedido ya pagado; el reembolso se coordina fuera de la plataforma en v1.

## 1. Roles

| Rol | Qué hace | Qué NO puede hacer en v1 |
|---|---|---|
| **Comprador** | Explora sin cuenta; crea cuenta para pagar; guarda favoritos; compra; ve el estado de sus pedidos | Ver pedidos o catálogos de otros; contactar directo a la marca |
| **Marca** | Se registra, espera aprobación, gestiona su catálogo (productos, precios, stock por talla), ve y gestiona solo sus propios sub-pedidos, puede cancelarlos | Ver catálogo/pedidos de otras marcas; publicar sin aprobación; procesar el cobro (lo hace la plataforma) |
| **Administrador** (interno) | Aprueba o rechaza marcas nuevas | Editar catálogos de marcas; entrar al flujo de compra; en v1 no gestiona pedidos ni reportes financieros |

## 2. Flujo

### 2.1 Alta de marca
```
Marca se registra (datos de marca + datos de liquidación)
  → Queda "pendiente de aprobación"
  → Administrador revisa la solicitud
  → Aprueba  → la marca puede cargar productos → productos visibles en el catálogo
  → Rechaza → se notifica a la marca, no queda visible
```

### 2.2 Descubrir y comprar (comprador)
```
Entra a la plataforma (sin cuenta)
  → Busca productos/marcas e indica preferencias (texto libre para la IA + filtros)
  → La IA recomienda productos y marcas
  → Revisa las opciones
      ├─ Guardar para después → requiere cuenta (si no tiene, se le pide crearla) → queda en Favoritos
      └─ Comprar
            → Elige producto, talla y cantidad → Agregar al carrito
              (el carrito puede tener productos de distintas marcas, agrupados por marca)
            → Continuar → si no tiene cuenta, debe crearla o iniciar sesión
            → Completa datos de contacto y dirección de despacho (sin cálculo de envío)
            → Elige método de pago y paga el total (un solo cobro)
            → El sistema:
                - descuenta el stock por talla de cada producto comprado
                - divide el pedido en un sub-pedido por cada marca involucrada
            → Comprador recibe correo de confirmación con el detalle completo
            → Cada marca recibe notificación solo de su propio sub-pedido
```

### 2.3 Preparación y despacho (marca)
```
Marca recibe notificación de nuevo sub-pedido
  → Puede marcarlo: "Preparando" → "Despachado"
  → o, si no puede cumplirlo: "Cancelar"
        → se notifica al comprador; el reembolso se coordina fuera de la plataforma (v1)
```

## 3. Pantallas necesarias

### Lado comprador
1. **Inicio** — búsqueda, categorías, recomendado para ti, marcas destacadas *(ya prototipada)*
2. **Buscar + preferencias (IA)** *(ya prototipada)*
3. **Resultados recomendados** *(ya prototipada)*
4. **Ficha de producto** *(ya prototipada)*
5. **Carrito** — agrupado visualmente por marca *(ajustar la prototipada)*
6. **Crear cuenta / Iniciar sesión** — nueva, aparece al guardar un favorito o al pagar sin estar logueado
7. **Checkout: datos y pago** *(ya prototipada)*
8. **Confirmación de pedido** *(ya prototipada)*
9. **Favoritos** (productos / marcas) *(ya prototipada)*
10. **Mis pedidos** — nueva; historial con el estado de cada sub-pedido (nuevo, preparando, despachado, cancelado)
11. **Mi cuenta** — nueva; datos personales y direcciones guardadas

### Lado marca
12. **Registro de marca** — nueva; datos de marca + datos de liquidación
13. **Estado de la solicitud** — nueva; pantalla simple "pendiente de aprobación" / "rechazada, motivo: …"
14. **Panel de pedidos** *(ya prototipada; agregar estado "Cancelado" y el botón de cancelar)*
15. **Catálogo: lista de productos** — nueva
16. **Crear / editar producto** — nueva; nombre, categoría, precio, fotos, tallas con stock
17. **Mi marca** — nueva; editar datos de perfil, ver estado de aprobación

### Lado administrador (interno)
18. **Solicitudes de marcas** — nueva y mínima; lista de marcas pendientes, ver detalle, aprobar/rechazar

## 4. Datos que se guardarán

| Entidad | Campos principales |
|---|---|
| **Usuario (comprador)** | id, nombre, email, teléfono, contraseña (hash), direcciones guardadas, fecha de registro |
| **Marca** | id, nombre, descripción, categoría, logo, contacto, datos de liquidación, estado (pendiente/aprobada/rechazada), fecha de registro |
| **Producto** | id, marca_id, nombre, descripción, categoría, precio, fotos, tallas [{talla, stock}], estado (activo/agotado/inactivo) |
| **Favorito** | usuario_id, tipo (producto o marca), referencia_id, fecha |
| **Carrito** | usuario_id (o sesión), ítems [{producto_id, talla, cantidad}] |
| **Pedido** (global) | id, usuario_id, fecha, dirección de despacho, contacto, método de pago, total pagado, estado general |
| **Sub-pedido** (por marca) | id, pedido_id, marca_id, ítems, subtotal, estado (nuevo/preparando/despachado/cancelado), historial de cambio de estado |
| **Liquidación** | sub_pedido_id, monto cobrado, comisión de la plataforma, monto a pagar a la marca, estado (pendiente/pagado) — el pago en sí se gestiona fuera de la plataforma en v1, pero el monto a deber queda registrado |
| **Notificación** | tipo (confirmación comprador / nuevo pedido marca / cancelación / aprobación de marca), destinatario, canal (email), fecha de envío |

## 5. Permisos

- **Comprador**: lee el catálogo público; lee y escribe solo sus propios datos, favoritos, carrito y pedidos.
- **Marca**: lee y escribe solo su propio catálogo y sus propios sub-pedidos; ve del comprador únicamente lo necesario para despachar (nombre, dirección, contacto) — no su email completo de cuenta ni su historial de compras con otras marcas.
- **Administrador**: lee y aprueba/rechaza solicitudes de marcas; no accede al catálogo, pedidos ni pagos en el alcance del MVP (eso queda para una versión posterior con backoffice completo).

## 6. Fuera de alcance del MVP

- Cálculo automático de costo de envío e integración con couriers / tracking en vivo (ya definido como etapa posterior en el paso 3).
- Pago dividido automáticamente por marca: en v1 todo el cobro pasa por la plataforma y la liquidación a cada marca se coordina manualmente.
- Reembolsos automáticos ante una cancelación (se coordinan por fuera de la plataforma).
- Reseñas o calificaciones de productos y marcas.
- Mensajería directa entre comprador y marca.
- Motor de recomendación con aprendizaje continuo sobre historial de compra; v1 recomienda en base al texto y los filtros de cada búsqueda, sin perfil de preferencias persistente.
- Multi-idioma y multi-moneda.
- Notificaciones push o SMS (solo correo electrónico).
- Backoffice de administración completo (reportes financieros, gestión de catálogos ajenos, soporte); el panel de admin de v1 solo aprueba o rechaza marcas.
- Cupones y descuentos con lógica real (el campo puede quedar visible en el carrito pero sin funcionalidad hasta v2).
- Devoluciones postventa, más allá de la cancelación antes del despacho.

## 7. Estado del prototipo

Las 18 pantallas de esta especificación ya están construidas en el prototipo: https://claude.ai/artifact/Aqio5zRt2rafku6T95Dcdy

Cambios sobre el prototipo del paso 4: el carrito ahora agrupa los productos por marca; se agregó el flujo de cuenta (crear cuenta / iniciar sesión, obligatorio antes de pagar), "Mis pedidos" y "Mi cuenta"; del lado de la marca se agregó el registro, el estado de la solicitud, el catálogo de productos con stock por talla, la edición de producto y el perfil de marca; el panel de pedidos ahora tiene el ciclo completo de estados (nuevo → preparando → despachado) y la opción de cancelar; y se agregó el panel mínimo de administrador para aprobar o rechazar marcas.
