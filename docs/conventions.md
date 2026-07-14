# Convenciones de código

> Homogeneidad extrema. La IA predice mejor cuando el repositorio se parece
> a sí mismo en todas partes.

## Estilo JavaScript/TypeScript

- **Lenguaje:** TypeScript (ES6+) y TSX.
- **Formato:** ESLint con config Expo. 2 espacios de indentación.
- **Imports:** ordenados — librerías externas primero, luego alias `@/`,
  luego rutas relativas.
- **Strings:** comillas dobles `"..."` en JSX, comillas simples `'...'`
  en JS regular.
- **Template literals** para interpolación. Nada de concatenación con `+`.

## Nombres

| Tipo                    | Convención        | Ejemplo                        |
|-------------------------|-------------------|--------------------------------|
| Archivos componentes    | `kebab-case`      | `haptic-tab.tsx`               |
| Archivos utilidades     | `kebab-case`      | `generate-uuid.ts`             |
| Componentes React       | `PascalCase`      | `HapticTab`                    |
| Funciones / variables   | `camelCase`       | `registerEntry`               |
| Constantes              | `UPPER_SNAKE`     | `DEFAULT_CATEGORY`            |
| Hooks                   | prefijo `use`     | `useInventory`                |
| CSS-in-JS / StyleSheet  | `PascalCase`      | `styles.container`             |

## Idioma

Código, nombres de variables, funciones, interfaces, hooks, componentes y
ficheros estarán en **inglés**. Los comentarios y documentación pueden
estar en español cuando se dirijan a humanos.

## Estructura de archivo — Componentes

```tsx
import { View, Text } from 'react-native';
import { styles } from './my-component.styles';

interface MyComponentProps {
  title: string;
}

export function MyComponent({ title }: MyComponentProps) {
  return (
    <View style={styles.container}>
      <Text>{title}</Text>
    </View>
  );
}
```

## Estructura de archivo — Utilidades / Database

```typescript
import * as SQLite from 'expo-sqlite';
import { generateUUID } from '@/utils/generate-uuid';

export async function insertRecord(
  db: SQLite.SQLiteDatabase,
  data: InventoryRecord
): Promise<{ success: boolean; error?: string }> {
  // ...
}
```

## Estructura de carpetas (convenciones de nombre)

| Carpeta          | Propósito                                   | Ejemplo de uso            |
|------------------|---------------------------------------------|---------------------------|
| `app/`           | Rutas Expo Router                           | `(tabs)/index.tsx`        |
| `components/`    | UI reutilizable                             | `ui/category-button.tsx`   |
| `constants/`     | Datos estáticos / diccionarios              | `categories.ts`            |
| `database/`      | SQLite: conexión, schema, queries           | `queries.ts`              |
| `hooks/`         | Custom hooks                                | `useInventory.ts`         |
| `store/`         | Zustand (estado efímero)                    | `useStore.ts`             |
| `utils/`         | Funciones puras sin dependencia de React    | `generate-uuid.ts`        |
| `docs/`          | Documentación del proyecto                  | `architecture.md`         |
| `specs/`         | Specs de features (SDD)                     | `001-entrada/`            |

## Tests

- Los tests van en una carpeta `__tests__/` o `tests/` en la raíz.
- Un archivo de test por módulo: `__tests__/queries.test.ts`.
- Cada test debe poder ejecutarse de forma aislada con una DB temporal.
- Nombres de test descriptivos: `test_insert_record_returns_uuid`.

## Manejo de errores

```typescript
// En database/ — errores de dominio
class DatabaseError extends Error {
  constructor(message: string, code: string) {
    super(message);
    this.code = code;
    this.name = 'DatabaseError';
  }
}

// Uso
throw new DatabaseError('Tabla no encontrada', 'TABLE_NOT_FOUND');
```

El UI captura errores y muestra feedback visual (Alert, Toast, etc.).
Nunca propagar stack traces al usuario final.

## Comentarios

Por defecto **no** se escriben. Solo se permiten cuando explican un *por qué*
no obvio (p. ej. workaround documentado, invariante sutil). Los nombres deben
hacer el resto.

## Formularios (Drill-down)

Los registros se hacen por revelación progresiva:
**Género → Categoría → Subtipo → Talla**. Cada paso es un componente
que muestra botones. NO usar `<TextInput>` para categorías.
