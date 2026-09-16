# Paso 4: Diseña tu solución — Moda Local

Prototipo visual navegable del flujo completo descrito en los pasos 1-3 (descubrimiento de marcas de moda local, recomendaciones con IA, compra y notificación a marcas).

**Prototipo (Claude / Artifacts):** https://claude.ai/artifact/Aqio5zRt2rafku6T95Dcdy

## Pantallas incluidas

Flujo principal de la persona compradora (7 pantallas, mobile 390×844):

1. **Inicio** — búsqueda, categorías, sección "Recomendado para ti" (IA) y marcas destacadas.
2. **Buscar + preferencias** — campo de texto libre para que la IA interprete lo que la persona busca, más filtros por categoría, precio y marca.
3. **Resultados recomendados** — grilla de productos ordenados por afinidad, con etiqueta "Para ti" en los recomendados por IA.
4. **Ficha de producto** — imagen, precio, selector de talla y cantidad, guardar en favoritos o agregar al carrito.
5. **Carrito** — productos agregados, edición de cantidad, código de descuento y subtotal.
6. **Datos y pago** — datos de contacto, dirección de despacho y método de pago.
7. **Confirmación de pedido** — número de pedido, resumen de compra y aviso de correo de confirmación enviado.

Flujo alternativo y lado de la marca (2 pantallas):

8. **Favoritos** — productos guardados para retomar la compra más adelante.
9. **Panel de la marca** — notificación de pedido nuevo con datos del comprador, productos y acción "Marcar como preparado".

## Decisiones de diseño

- Paleta cálida (marfil, tinta carbón, acento terracota) y tipografía serif/sans para transmitir un espacio curado y artesanal, distinto de un marketplace genérico.
- Las recomendaciones de IA se marcan explícitamente en la interfaz (insignia "Para ti") para que la persona entienda por qué ve cada producto, en vez de mezclarlas sin distinción con el resto del catálogo.
- El flujo de "guardar para después" y el de "comprar" quedan separados como pantallas propias (Favoritos vs. Carrito → Pago → Confirmación), replicando el diagrama de proceso del paso 2.
- Se incluyó el panel de marca porque el proceso definido en el paso 2 termina con "la marca recibe una notificación del pedido para prepararlo y despacharlo": sin esa pantalla el prototipo solo mostraría la mitad del problema que se busca resolver (visibilidad y ventas para las marcas).
