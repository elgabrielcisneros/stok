# Arquitectura — Qué significa "hacer un buen trabajo"

> Este documento define el estándar de calidad. Los agentes revisores
> evalúan código contra este archivo. Si no está aquí, no es un requisito.

## Principios

1. **Offline-First.** La base de datos local (SQLite) es la única fuente
   de verdad. La app NUNCA debe bloquearse esperando una respuesta de red.

2. **Capas claras.** El proyecto tiene cuatro capas y solo cuatro:
   - `app/` — Navegación y pantallas (Expo Router).
   - `components/` — UI reutilizable (botones, grids, modales).
   - `database/` — Persistencia (SQLite via `expo-sqlite`).
   - `store/` — Estado efímero de UI (Zustand).
   No introducir capas adicionales (servicios, repositorios abstractos)
   hasta que haya una razón concreta documentada.

3. **UUIDs para todo.** Nunca usar enteros autoincrementales para IDs.
   Todos los registros utilizan UUIDs generados vía `expo-crypto` para
   evitar colisiones al fusionar bases de datos de distintos voluntarios.

4. **Errores explícitos.** Las funciones que pueden fallar lanzan
   excepciones nombradas o devuelven `{ error: string }`, no `null`
   silencioso.

5. **Preparación para sincronización.** Todo registro insertado localmente
   debe tener una columna `synced` en `0` (false) y su respectivo
   `updated_at`. Cuando la sincronización sea exitosa, actualizar a `1`.

## Flujo de datos

```
usuario  ─→  app/(tabs)/  (Expo Router)
              │
              ├─→  components/  (UI: botones, grids)
              │        │
              │        └─→  hooks/  (useInventario, etc.)
              │                 │
              │                 └─→  database/queries.js  (CRUD SQLite)
              │                          │
              │                          └─→  SQLite (local)
              │
              └─→  store/  (Zustand: estado efímero, triggers)
                       │
                       └─→  actualiza contador para refrescar UI
```

## Estructura de carpetas

```
stok/
├── app/                    # Expo Router (rutas)
│   ├── _layout.tsx         # Layout raíz (init DB, providers)
│   ├── (tabs)/             # Navegación principal
│   │   ├── index.tsx       # Dashboard
│   │   ├── registrar.tsx   # Formulario de registro
│   │   └── nube.tsx        # Sync / respaldos
│   └── modal.tsx           # Modales
├── components/             # UI reutilizable
│   └── ui/                 # Componentes atómicos
├── constants/              # Fuentes de verdad estáticas
│   └── categorias.js       # Estructura jerárquica de ropa
├── database/               # Capa de persistencia
│   ├── db.js               # Conexión con expo-sqlite
│   ├── schema.js           # Creación de tablas
│   └── queries.js          # Funciones CRUD
├── hooks/                  # Hooks personalizados
│   └── useInventario.js    # Conecta UI con SQLite
├── store/                  # Estado全局 (Zustand)
│   └── useStore.js         # Estado efímero, triggers, badges
├── utils/                  # Lógica pura sin UI
│   ├── generateUUID.js     # Generación de IDs
│   └── exportBackup.js     # Empaquetado JSON para respaldos
├── docs/                   # Documentación del proyecto
└── specs/                  # Specs de features (SDD)
```

## Qué NO hacer

- No usar `console.log()` para errores en producción. Usa `Alert` o
  muestra feedback visual al usuario.
- No mezclar lógica de negocio con componentes UI. La lógica va en
  `hooks/` o `database/`.
- No hacer fetch a APIs externas sin verificar conectividad primero.
  Offline-first significa que la app funciona sin red.
- No usar `<TextInput>` para categorías. Obligar al uso de botones
  basados en `constants/categorias.js`.
- No almacenar estado persistente en Zustand. Solo estado efímero
  y triggers de refresco.
- No escribir sin `INSERT OR REPLACE INTO` validando `updated_at`
  al importar datos (resolución de conflictos).

## Manejo de errores en UI

```javascript
// En hooks/ o database/
export async function registrarEntrada(datos) {
  try {
    await insertarRegistro(datos);
    return { success: true };
  } catch (error) {
    console.error('Error al registrar:', error);
    return { success: false, error: error.message };
  }
}

// En componentes/
const resultado = await registrarEntrada(datos);
if (!resultado.success) {
  Alert.alert('Error', resultado.error);
}
```
