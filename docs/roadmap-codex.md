# Roadmap tecnico para integracion con Codex

## Objetivo

Este documento registra tareas tecnicas recomendadas despues de la primera auditoria del repositorio para integracion con Codex y agentes IA.

Como los issues pueden estar deshabilitados en este repositorio, estas tareas quedan documentadas aqui para seguimiento mediante Pull Requests.

---

## 1. Sincronizar version package.json vs codigo fuente

### Resumen

Durante la auditoria inicial se detecto una posible inconsistencia de version:

- `package.json` declara version `1.0.12`.
- Algunos archivos fuente muestran `1.0.11` como version del servidor.

### Riesgo

**Bajo a medio.** No necesariamente rompe la ejecucion, pero puede generar confusion operativa, diagnosticos inconsistentes o documentacion desactualizada.

### Acciones recomendadas

1. Revisar todos los puntos donde se declara version.
2. Definir fuente unica de verdad, idealmente `package.json`.
3. Sincronizar `serverInfo.version` u otros valores hardcodeados.
4. Ejecutar build y pruebas.

### Comandos sugeridos

```bash
npm run build
npm test
```

### Criterio de aceptacion

- La version expuesta por el servidor coincide con `package.json`.
- No quedan versiones hardcodeadas inconsistentes.
- El PR documenta build y pruebas.

---

## 2. Auditar herramientas HTTP sensibles

### Resumen

El servidor MCP expone herramientas HTTP capaces de ejecutar:

- GET
- POST
- PUT
- PATCH
- DELETE

Estas capacidades son utiles, pero deben operar con limites claros cuando Codex u otros agentes IA las usen.

### Riesgo

**Alto** si se usan contra endpoints externos, productivos o autenticados sin confirmacion.

### Acciones recomendadas

1. Revisar implementacion de `playwright_post`, `playwright_put`, `playwright_patch` y `playwright_delete`.
2. Evaluar una politica de confirmacion para acciones no locales.
3. Considerar allowlist de dominios o modo seguro.
4. Sanitizar tokens y headers sensibles en logs.
5. Documentar limites de uso.

### Criterio de aceptacion

- Las herramientas sensibles estan documentadas.
- Existe una decision tecnica sobre confirmacion, allowlist o modo seguro.
- Los logs no exponen tokens completos.

---

## 3. Evaluar endurecimiento progresivo de TypeScript

### Resumen

El proyecto tiene `strict: true`, pero tambien:

```json
{
  "noImplicitAny": false,
  "strictNullChecks": false
}
```

Esto permite flexibilidad, pero puede ocultar errores de tipos, nulos y argumentos ambiguos.

### Riesgo

**Medio.** Puede permitir bugs sutiles en runtime, especialmente en handlers MCP y herramientas con argumentos dinamicos.

### Acciones recomendadas

1. Ejecutar auditoria de tipos sin cambiar configuracion inicialmente.
2. Identificar modulos con mayor uso de `any` o posibles nulos.
3. Endurecer por etapas.
4. Evitar cambios masivos en un solo PR.

### Comandos sugeridos

```bash
npm run build
npx tsc --noEmit
```

### Criterio de aceptacion

- Se identifican areas problematicas.
- Se propone una estrategia gradual.
- No se rompen builds existentes.

---

## 4. Verificar CI/CD y pruebas automatizadas

### Resumen

Conviene confirmar si el repositorio tiene workflows de GitHub Actions para build, tests y auditoria basica.

### Riesgo

**Medio.** Sin CI, cambios futuros pueden romper build o tests sin deteccion temprana.

### Acciones recomendadas

1. Revisar `.github/workflows/`.
2. Si no existe CI, agregar workflow minimo.
3. Incluir:
   - `npm ci`
   - `npm run build`
   - `npm test`
4. Evaluar `npm audit` como tarea informativa.

### Criterio de aceptacion

- Existe CI minimo o se documenta por que no se implementa.
- Build y tests se ejecutan en PRs.

---

## 5. Evaluar modo seguro para ejecucion con agentes IA

### Resumen

Para operar con Codex, Claude, Copilot u otros agentes, conviene agregar una politica de modo seguro.

### Posibles controles

- Permitir por defecto solo `GET` y herramientas de lectura.
- Requerir confirmacion para acciones mutativas.
- Bloquear dominios no permitidos.
- Limitar acceso a archivos locales.
- Registrar auditoria sin secretos.

### Criterio de aceptacion

- Existe una decision clara sobre modo seguro.
- La documentacion indica como operar con agentes IA sin exponer datos ni sistemas reales.
