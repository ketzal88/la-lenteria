# Sección Clientes — Diseño

**Fecha:** 2026-03-26
**Estado:** Aprobado

## Objetivo

Agregar una pestaña "Clientes" en el header de La Lentería que muestre todos los pacientes únicos derivados del historial de pedidos, con buscador y modal de detalle.

## Vista principal

- Nueva pestaña "Clientes" en el header junto a: Nuevo pedido / Historial / Carga IA
- Buscador por nombre o DNI (input en la parte superior)
- Lista de clientes únicos deduplicados por DNI (si existe) o por nombre exacto
- Cada fila muestra: nombre completo, DNI, teléfono, cantidad de pedidos
- Ordenados alfabéticamente por nombre
- Estado vacío cuando no hay pedidos cargados

## Modal de detalle del cliente

Se abre al hacer click en cualquier cliente de la lista.

**Encabezado del modal:**
- Nombre completo
- DNI
- Teléfono

**Cuerpo del modal:**
- Lista de todos los pedidos del cliente ordenados por fecha descendente
- Por cada pedido: número de orden, fecha, tipo (Normal/PAMI), tipo de lente, total
- Botón de reimprimir en cada pedido

**Pie del modal:**
- Botón primario "Nuevo pedido" — cierra el modal, navega a la pestaña "Nuevo pedido" y precarga: nombre, DNI, teléfono, y graduación del último pedido del cliente
- Botón secundario "Cerrar"

## Implementación técnica

- Todo dentro del mismo `index.html` (sin archivos nuevos)
- Sigue el patrón visual existente: variables CSS `--accent`, `--accent2`, `--surface`, `--border`, mismas fuentes
- La función `buscarClientes()` existente se reutiliza como base para la deduplicación
- La función `cargarDatosCliente()` existente se reutiliza para precargar datos en el formulario
- No se modifica la estructura de datos de localStorage ni Firestore
- El modal se agrega al DOM como elemento fijo con overlay, igual al patrón del login-screen

## Flujo de datos

```
localStorage/Firestore (pedidos)
  → getPedidos()
  → deduplicar por DNI o nombre
  → renderClientes() — lista en vista
  → click → abrirModalCliente(dni/nombre)
    → filtrar pedidos del cliente
    → renderModalCliente()
      → "Nuevo pedido" → cargarDatosCliente(id último pedido) + showView('nuevo')
      → "Reimprimir" → imprimirDesdeHistorial(id)
```

## Casos borde

- Cliente sin DNI: se agrupa por nombre exacto
- Cliente con múltiples DNIs distintos: aparece como clientes separados
- Sin pedidos: estado vacío con mensaje
- Buscador sin resultados: mensaje "Sin resultados"
