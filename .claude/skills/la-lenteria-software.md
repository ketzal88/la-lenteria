# Proyecto: Software de Gestión de Pedidos — Óptica

## Contexto
App para una óptica pequeña (1 sola usuaria, PC de escritorio) para reemplazar un proceso manual:
formulario a mano → planilla Excel → hoja doblada como sobre para guardar lentes/cristales.

**Estado actual:** prototipo funcional entregado como archivo HTML standalone (`optica.html`).
Corre en el browser sin servidor, sin instalación. Datos persistidos en `localStorage`.

---

## Lo que ya existe (optica.html)

### Features implementadas
- Formulario completo de nuevo pedido
- Historial con búsqueda por nombre/DNI
- Reimprimir desde historial
- Cargar pedido histórico en formulario
- Eliminar pedido
- Cálculo automático "resta pagar" (total − seña)
- Impresión A4 en dos mitades (ficha completa + copia para sobre)
- Fecha de ingreso auto-completada con hoy

### Stack
- HTML + CSS + JS vanilla, todo en un único archivo
- Persistencia: `localStorage` con key `optica_pedidos` (array JSON)
- Fonts: DM Serif Display + DM Sans (Google Fonts, requiere internet)
- Sin dependencias npm, sin servidor, sin build step

### Estructura de datos (un pedido)
```json
{
  "id": 1718000000000,
  "ts": "2025-06-10T14:00:00.000Z",
  "nombre": "María González",
  "dni": "12345678",
  "tel": "11 1234-5678",
  "fecha": "2025-06-10",
  "entrega": "2025-06-17",
  "l": {
    "od": { "esf": "-1.50", "cil": "-0.50", "eje": "90", "adi": "" },
    "oi": { "esf": "-1.25", "cil": "",      "eje": "",   "adi": "" }
  },
  "c": {
    "od": { "esf": "+1.00", "cil": "",      "eje": "",   "adi": "+2.00" },
    "oi": { "esf": "+1.00", "cil": "",      "eje": "",   "adi": "+2.00" }
  },
  "lente": "Progresivo",
  "tratamiento": "AR + Blue",
  "obs": "Armazón traído por el cliente",
  "total": "85000",
  "senia": "40000",
  "resto": "45000"
}
```

---

## Decisiones de diseño tomadas
- **Sin servidor por ahora:** todo corre local, cero fricción para la usuaria
- **localStorage:** suficiente para el volumen de una óptica chica; no requiere setup
- **A4 doblado:** la hoja impresa tiene dos mitades idénticas separadas por línea punteada — la mitad de abajo se dobla y pega como sobre
- **Sin logo por ahora:** se mencionó que tiene logo e info de la óptica pero no era prioritario
- **Solo reimprimir desde historial:** no editar pedidos históricos (decisión explícita)

---

## Próximos pasos posibles (no implementados)

### Prioritarios
- [ ] **Nombre de la óptica configurable** — input en settings que se guarda en localStorage y aparece en el print
- [ ] **Export a CSV/Excel** — botón de backup del historial completo
- [ ] **Logo en el impreso** — upload de imagen que se guarda en localStorage como base64

### Opcionales / mejoras
- [ ] Convertir a app Electron o Tauri para que funcione como .exe sin browser
- [ ] Migrar persistencia a SQLite (si se convierte a app de escritorio)
- [ ] Filtro de historial por rango de fechas
- [ ] Estado del pedido: "pendiente / listo para retirar / entregado"
- [ ] Campos de proveedor / laboratorio al que se encarga el trabajo

---

## Para retomar en Claude Code

```bash
# El archivo de referencia es optica.html
# No hay package.json ni estructura de proyecto todavía
# Si se convierte a app:
mkdir optica-app
cd optica-app
# Opciones: Electron, Tauri, o simplemente servir el HTML con un http-server local
```

### Si se quiere convertir a Electron rápido:
```bash
npm init -y
npm install electron --save-dev
# main.js que abre el HTML como ventana, sin servidor
```

### Si se quiere agregar backend liviano (para multi-PC o backup):
- Node + Express + better-sqlite3
- O directamente Tauri (Rust) si se quiere binario nativo liviano

---

## Contacto del proyecto
- **Desarrollado para:** amiga de Gabriel (CMO de Worker), óptica pequeña, Buenos Aires
- **Prioridad:** funcional > elegante, sin fricción de instalación