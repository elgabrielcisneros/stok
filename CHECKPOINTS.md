# CHECKPOINTS — Evaluación del estado final

> En sistemas multi-agente no se evalúa el camino, se evalúa el destino.
> Estos son los checkpoints objetivos que un juez (humano o IA) puede usar
> para decidir si el proyecto está sano.

## C1 — El arnés está completo

- [ ] Existen los archivos base: `AGENTS.md`, `feature-list.json`.
- [ ] Existen los docs: `docs/architecture.md`, `docs/conventions.md`,
      `docs/verification.md`, `docs/specs.md`.
- [ ] `pnpm jest` termina con exit code 0.

## C2 — El estado es coherente

- [ ] Como mucho una feature en `in_progress` en `feature-list.json`.
- [ ] Toda feature `done` tiene tests asociados que pasan.
- [ ] No hay basura de sesiones anteriores en `specs/`.

## C3 — El código respeta la arquitectura

- [ ] `app/`, `components/`, `database/`, `hooks/`, `store/`, `utils/`
      contienen solo los módulos previstos en `docs/architecture.md`.
- [ ] No hay dependencias externas no autorizadas en `package.json`.
- [ ] No hay `console.log()` sueltos para debug, ni TODOs sin contexto.

## C4 — La verificación es real

- [ ] `__tests__/` tiene al menos un test por módulo público.
- [ ] Los tests usan mocks solo para módulos nativos (expo-sqlite, etc.).
- [ ] `pnpm jest` muestra > 0 tests y todos verdes.
- [ ] `pnpx tsc --noEmit` no muestra errores de tipos.
- [ ] `pnpx expo lint` no muestra errores.

## C5 — La sesión se cerró bien

- [ ] No hay archivos sin trackear sospechosos (`*.tmp`, `node_modules/`
      fuera del `.gitignore`).
- [ ] La última feature trabajada está reflejada en su estado correcto
      en `feature-list.json`.

## C6 — Spec Driven Development

- [ ] Toda feature con `"sdd": true` en estado `spec_ready`, `in_progress`
      o `done` tiene su carpeta `specs/<name>/` con los archivos:
      `requirements.md`, `design.md`, `tasks.md`.
- [ ] `requirements.md` usa EARS estricto (ver `docs/specs.md`).
- [ ] Toda feature `done` con `"sdd": true` tiene todas sus tasks marcadas
      `[x]` en `tasks.md`.
- [ ] Cada `R<n>` de `requirements.md` está cubierto por al menos un test
      concreto en `__tests__/`.

## C7 — Convenciones de código

- [ ] Nombres de variables, funciones, interfaces, hooks y componentes
      están en inglés (ver `docs/conventions.md`).
- [ ] Código TypeScript/TSX, sin archivos `.js` en código fuente.
- [ ] Gestor de paquetes: pnpm (no npm/npx).

---

**Cómo usar este archivo:** el agente `reviewer` recorre cada checkbox,
marca `[x]` o `[ ]`, y rechaza el cierre de sesión si quedan boxes vacíos
en C1-C7.
