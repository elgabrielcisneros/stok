
# Guía de Agentes — Tracker de Inventario (Centro de Acopio)

## Descripción

App mobile "offline-first". Diseñada para gestionar y registrar el inventario de ropa donada en un centro de acopio tras desastres naturales. Permite registrar entradas y salidas de prendas categorizadas (género, tipo, talla) de forma ultrarrápida mediante una interfaz de botones (sin uso de teclado). Funciona completamente sin internet usando una base de datos local y permite sincronización en la nube o respaldos manuales compartidos.

## Stack Tecnológico

- **Lenguaje:** JavaScript y JSX (ES6+)
- **Framework:** React Native / Expo
- **Navegación:** Expo Router
- **Manejo de estado:** Zustand (exclusivo para estado efímero de UI y triggers)
- **Base de datos Local:** SQLite (`expo-sqlite`)
- **Backend / Sincronización (Fase 2):** Supabase (`@supabase/supabase-js`)
- **Herramientas de Respaldo:** `expo-file-system`, `expo-sharing`
- **Utilidades:** `expo-crypto` (generación de UUIDs)

## Comandos Rápidos

```bash
pnpm expo start            # Iniciar el servidor de desarrollo de Expo
pnpm expo start -c         # Iniciar limpiando la caché (útil si fallan dependencias)
pnpm expo install <pkg>    # Instalar paquetes compatibles con la versión actual de Expo

```

## Estructura del Proyecto

- `app/` — Expo Router (enrutamiento basado en archivos).
- `_layout.js` — Configuración global (inicialización de SQLite).
- `(tabs)/` — Navegación principal (Dashboard, Registrar, Nube).
- `src/components/` — Componentes UI puros y reutilizables (Botones, Grid de categorías).
- `src/constants/` — Diccionarios y fuentes de verdad estáticas (ej. `categorias.js` con la estructura jerárquica de la ropa).
- `src/database/` — Capa de persistencia.
- `db.js` — Conexión con `expo-sqlite`.
- `schema.js` — Consultas de creación de tablas.
- `queries.js` — Funciones CRUD crudas.
- `store.js` — Almacén global de Zustand.
- `src/hooks/` — Hooks personalizados (ej. `useInventario.js` para conectar UI con base de datos).
- `src/utils/` — Lógica pura sin UI (generación de UUIDs, empaquetado JSON para respaldos).

## Convenciones Clave

- **Arquitectura Offline-First:** La base de datos local (SQLite) es la única fuente de la verdad para el stock. La app NUNCA debe bloquearse esperando una respuesta de red.
- **Identificadores Únicos:** Nunca usar enteros autoincrementales para IDs. Todos los registros utilizan UUIDs generados vía `expo-crypto` para evitar colisiones al fusionar bases de datos de distintos voluntarios.
- **Formularios Drill-down:** Los registros se hacen por revelación progresiva (Género -> Categoría -> Subtipo -> Talla). No usar `<TextInput>` para categorías, obligar al uso de botones basados en `src/constants/categorias.js`.
- **Manejo de Estado (Zustand):** Zustand NO guarda el inventario. Se usa exclusivamente para:

1. Estado efímero del formulario de registro paso a paso.
2. Un contador/trigger global para forzar al Dashboard a consultar SQLite nuevamente tras un `INSERT`.
3. Contadores de notificaciones (badges) para registros pendientes de sincronizar.

## Puntos a Tener en Cuenta

- **Resolución de Conflictos (Upsert):** Al importar un archivo `.json` de respaldo o bajar datos de Supabase, las consultas deben usar `INSERT OR REPLACE INTO` validando la columna `updated_at`. El dato con el timestamp más reciente siempre gana.
- **Preparación para la Nube:** Todo registro insertado localmente debe tener una columna `synced` en `0` (false) y su respectivo `updated_at`. Cuando la sincronización con Supabase sea exitosa, actualizar a `1`.
- **Optimización de UI:** En contextos de emergencia (luz solar, estrés), usar botones grandes y colores contrastantes (Verde para entrada, Rojo/Naranja para salida). Evitar modales de confirmación bloqueantes; preferir un flujo de registro directo.

## Flujo de trabajo

- Trabajamos con **Spec Driven Development**: la spec va antes que el codigo. Para una feature nueva, primero `spec.md` -> `plan.md` -> `tasks.md` en `spec/features/NNN-nombre`, y solo entonces se implementa (ver "Documentacion")
- Antes de una tarea no trivial, tu propones un plan y espera mi OK.
- Haz una tarea a la vez; al terminar dime que cambiaste, como y por que, para yo revisarlo
- Si no estas completamente seguro pregunta, o por lo menos al 80%, no inventes nada.
