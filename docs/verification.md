# Verificación — Cómo demostrar que el trabajo funciona

> Regla de oro: **el agente no dice "funciona", lo demuestra**.
> Toda feature termina con evidencia ejecutable, no con afirmaciones.

## Niveles de verificación

### Nivel 1 — Tests unitarios (obligatorio)

Toda función pública en `database/`, `hooks/` o `utils/` tiene al menos
un test en `__tests__/` que:

1. Cubre el camino feliz.
2. Cubre al menos un camino de error si la función puede fallar.

Comando:

```bash
pnpm jest --verbose
```

### Nivel 2 — Verificación en dispositivo / emulador (obligatorio para features de UI)

Las features que añaden pantallas o componentes se verifican compilando
y ejecutando la app:

```bash
pnpx expo start
# Escanear QR con Expo Go o compilar en emulador
```

El agente debe documentar:

- Que la pantalla carga sin errores.
- Que la interacción.flujo principal funciona.
- Que los datos persisten tras cerrar y reabrir la app.

### Nivel 3 — Smoke test de base de datos (obligatorio para features de DB)

Verificar que las operaciones CRUD funcionan correctamente:

```bash
# Abrir consola de Expo
pnpx expo start
# En la consola, ejecutar pruebas manuales de queries
```

Documentar que:

- Los registros se insertan correctamente.
- Los UUIDs se generan únicos.
- Las actualizaciones preservan `updated_at`.
- Las eliminaciones son lógicas (soft delete) o físicas según diseño.

### Nivel 4 — Trazabilidad de requirements (obligatorio para features con `"sdd": true`)

Cada `R<n>` de `specs/<name>/requirements.md` debe poder mapearse a al
menos un test concreto en `__tests__/`. El reviewer rechaza si falta cobertura.

El implementer documenta el mapa en `progress/impl_<name>.md`:

```markdown
## Trazabilidad
- R1 → `test_insertar_registro_devuelve_uuid`
- R2 → `test_consulta_inventario_filtra_por_categoria`
```

## Anti-patrones (no hacer)

- ❌ "He añadido el componente, debería funcionar." → falta test ejecutable.
- ❌ Test que solo verifica que el componente no lanza excepción. → tiene que
  comprobar el resultado concreto.
- ❌ Mock de la base de datos. → usa una DB temporal en memoria o archivo temporal.
- ❌ Marcar la feature como `done` sin verificar que la app compila sin errores.

## Verificación final antes de cerrar

```bash
pnpx expo lint          # debe terminar sin errores
pnpx tsc --noEmit       # debe compilar sin errores de tipos
pnpx jest               # todos los tests deben pasar
```

Si alguno de estos comandos falla, **no** marques nada como `done`. Anota
el bloqueo en `progress/current.md` con estado `blocked` en `feature_list.json`.

## Checklist de cierre

- [ ] `pnpx expo lint` pasa sin errores
- [ ] `pnpx tsc --noEmit` compila sin errores
- [ ] `pnpx jest` todos los tests pasan
- [ ] La app carga en emulador/dispositivo sin crashes
- [ ] El flujo principal de la feature funciona end-to-end
- [ ] Los datos persisten correctamente en SQLite
- [ ] Trazabilidad documentada en `progress/impl_<name>.md`
