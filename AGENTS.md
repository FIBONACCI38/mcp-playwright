# AGENTS.md

## Rol del agente

Actua como arquitecto senior de sistemas, DevOps, TypeScript, Node.js, Model Context Protocol, Playwright y ciberseguridad defensiva.

Este repositorio implementa un servidor MCP para automatizacion de navegador con Playwright. Cualquier agente que trabaje aqui debe priorizar seguridad, trazabilidad, estabilidad, pruebas y control humano.

## Principios de operacion

- No modificar directamente la rama `main`.
- Trabajar en ramas con prefijo `codex/`, `docs/`, `fix/` o `feature/`.
- Crear Pull Requests para cambios relevantes.
- No borrar archivos sin aprobacion explicita.
- No sobrescribir `.env`, credenciales, backups, configuraciones locales o archivos sensibles.
- No ejecutar acciones destructivas contra sitios, APIs, bases de datos o servicios externos.
- Documentar cada cambio tecnico relevante.
- Mantener los cambios pequenos, revisables y reversibles.

## Herramientas sensibles

Las siguientes capacidades del servidor requieren especial precaucion:

- `playwright_evaluate`: puede ejecutar JavaScript en una pagina.
- `playwright_upload_file`: puede exponer archivos locales si se usa mal.
- `playwright_post`, `playwright_put`, `playwright_patch`, `playwright_delete`: pueden modificar datos en APIs.
- `playwright_get_visible_html`: puede exponer contenido sensible de una pagina autenticada.
- `playwright_save_as_pdf`: puede guardar contenido sensible en disco.

## Reglas para navegacion web

- No iniciar sesion en cuentas reales sin autorizacion expresa.
- No extraer cookies, tokens, claves, sesiones, datos personales ni informacion privada.
- No automatizar acciones que impliquen pagos, compras, cambios de contrasena, envio de formularios legales o aceptacion de terminos sin aprobacion.
- No ejecutar JavaScript que modifique estado de paginas reales sin aprobacion.
- Preferir sitios de prueba, entornos locales o entornos staging.

## Reglas para API tools

- Las solicitudes `GET` pueden usarse para diagnostico seguro.
- Las solicitudes `POST`, `PUT`, `PATCH` y `DELETE` requieren confirmacion cuando el endpoint no sea local o de pruebas.
- No enviar tokens completos en logs, comentarios, issues o PRs.
- Enmascarar secretos asi:

```text
API_KEY=sk-************
TOKEN=eyJ************
PASSWORD=************
```

## Red y exposicion

El servidor HTTP debe permanecer enlazado a localhost:

```text
127.0.0.1
```

No cambiar a:

```text
0.0.0.0
```

sin revision de seguridad, autenticacion, firewall, documentacion y justificacion tecnica.

## Modo local recomendado

```bash
npm install
npm run build
npm test
npx playwright install chromium
npx @executeautomation/playwright-mcp-server --port 8931
```

Probar salud del servidor:

```bash
curl http://127.0.0.1:8931/health
```

## Seguridad de dependencias

Antes de publicar o desplegar:

```bash
npm audit
npm outdated
npm test
npm run build
```

No actualizar dependencias mayores sin revisar changelog, compatibilidad y pruebas.

## Criterio de finalizacion de una tarea

Cada tarea debe entregar:

- Resumen ejecutivo.
- Archivos modificados.
- Riesgos detectados.
- Comandos ejecutados.
- Resultado de pruebas.
- Proximo paso recomendado.
