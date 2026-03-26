# Sección Clientes — Diseño

**Fecha:** 2026-03-26
**Estado:** Aprobado

## Objetivo

Agregar una pestaña "Clientes" en el header de La Lentería que muestre todos los pacientes únicos derivados del historial de pedidos, con buscador y modal de detalle.

## Vista principal

- Nueva pestaña "Clientes" en el header junto a: Nuevo pedido / Historial / Carga IA
- Buscador por nombre o DNI — solo se muestra si hay al menos un pedido cargado
- Lista de clientes únicos deduplicados (ver lógica en sección de datos)
- Cada fila muestra: nombre completo, DNI, teléfono, cantidad de pedidos
- Ordenados alfabéticamente por nombre (case-insensitive)
- Estado vacío con mensaje cuando no hay pedidos cargados (sin buscador)
- Mensaje "Sin resultados" cuando la búsqueda no encuentra coincidencias

## Modal de detalle del cliente

Se abre al hacer click en cualquier cliente de la lista. `z-index: 500` (sobre el header a 100, bajo el toast a 999).

**Encabezado del modal:**
- Nombre completo, DNI, Teléfono

**Cuerpo del modal:**
- Lista de todos los pedidos del cliente ordenados por fecha descendente
- Por cada pedido: número de orden (mismo formato que Historial: últimos 4 dígitos del id, prefijo "P" si es PAMI), fecha, tipo (Normal/PAMI), lente (campo `lente`, más `tratamiento` si existe, separados por " · "), total
- Botón "Reimprimir" por pedido: cierra/oculta el modal → llama `imprimirDesdeHistorial(id)` → `window.print()` → al terminar la impresión restaura el modal (via `window.onafterprint`)

**Pie del modal:**
- Botón primario "Nuevo pedido" — cierra el modal, llama `cargarDatosCliente(idUltimoPedido)` pasando el `id` numérico del pedido más reciente del cliente, luego `showView('nuevo')`
- Botón secundario "Cerrar"

## Implementación técnica

- Todo dentro del mismo `index.html` (sin archivos nuevos)
- Sigue el patrón visual existente: variables CSS `--accent`, `--accent2`, `--surface`, `--border`, mismas fuentes
- **Nueva función `obtenerTodosLosClientes()`**: lee `getPedidos()`, deduplica, ordena y devuelve la lista completa de clientes. No usa `buscarClientes()` (que requiere query ≥ 2 chars y limita a 6 resultados)
- `buscarClientes()` existente se usa solo para el autocompletado del formulario (sin cambios)
- `cargarDatosCliente(id)` existente se llama con el `id` numérico del pedido más reciente del cliente
- `showView()` se extiende agregando `clientes` al mapa de tabs: `{nuevo:0, historial:1, carga:2, clientes:3}`
- No se modifica la estructura de datos de localStorage ni Firestore
- El modal usa `position:fixed; inset:0; z-index:500` con overlay oscuro

## Lógica de deduplicación de clientes

```
key = dni ? dni.trim() : nombre.trim().toLowerCase()
```

- Si el pedido tiene DNI (no vacío): la clave es el DNI (trim)
- Si no tiene DNI: la clave es el nombre en minúsculas con trim
- El nombre y teléfono que se muestran son los del pedido más reciente del grupo
- La cantidad de pedidos es el total de pedidos con esa clave

## Flujo de datos

```
getPedidos()
  → obtenerTodosLosClientes()  — deduplica y ordena
  → renderClientes(query)      — filtra por buscador, renderiza lista
  → click fila → abrirModalCliente(key)
    → filtrar pedidos por key
    → renderModalCliente()
      → "Reimprimir" → cerrar modal → imprimirDesdeHistorial(id) → window.onafterprint → restaurar modal
      → "Nuevo pedido" → cerrar modal → cargarDatosCliente(idUltimoPedido) → showView('nuevo')
      → "Cerrar" → cerrar modal
```

## Casos borde

- Cliente sin DNI: clave = nombre.trim().toLowerCase()
- Mismo nombre con distinto DNI: aparecen como clientes separados
- Sin pedidos: estado vacío, sin buscador
- Buscador sin resultados: mensaje "Sin resultados para X"
- Modal abierto al imprimir: se oculta antes del print, se restaura en onafterprint
