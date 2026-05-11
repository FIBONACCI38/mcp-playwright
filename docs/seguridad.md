# Seguridad del servidor MCP Playwright

## Resumen ejecutivo

Este repositorio contiene un servidor MCP que permite a agentes de IA interactuar con navegadores reales mediante Playwright. Esto habilita automatizacion, pruebas, scraping controlado, capturas, generacion de PDF y solicitudes HTTP.

La capacidad es poderosa y debe operarse bajo reglas defensivas. El objetivo de este documento es reducir riesgos de exposicion, acciones no autorizadas, fuga de datos y uso accidental de herramientas sensibles.

## Superficie de riesgo

### Navegador

El servidor puede abrir paginas, interactuar con formularios, hacer clic, llenar campos, cargar archivos, leer HTML visible, leer texto visible y tomar capturas.

Riesgos principales:

- Exposicion de informacion en paginas autenticadas.
- Envio accidental de formularios.
- Automatizacion de acciones irreversibles.
- Captura de datos personales o informacion confidencial.
- Uso indebido de `playwright_evaluate`.

### API tools

El servidor define herramientas para solicitudes HTTP:

- GET
- POST
- PUT
- PATCH
- DELETE

Riesgos principales:

- Modificacion accidental de datos.
- Consumo de endpoints externos sin autorizacion.
- Envio de tokens o informacion sensible.
- Pruebas destructivas en entornos productivos.

### Archivos locales

Algunas herramientas pueden guardar PDFs, capturas o subir archivos a formularios.

Riesgos principales:

- Guardar informacion sensible sin control.
- Subir archivos incorrectos.
- Exponer rutas locales.
- Mezclar datos de prueba con datos reales.

## Controles recomendados

### 1. Mantener el servidor en localhost

El modo HTTP debe ejecutarse sobre:

```text
127.0.0.1
```

No usar `0.0.0.0` salvo que exista un plan de seguridad, autenticacion, firewall y monitoreo.

### 2. Separar entornos

Usar entornos diferenciados:

```text
local
staging
testing
production
```

El agente debe operar preferentemente en `local`, `testing` o `staging`.

### 3. Confirmacion para acciones sensibles

Requieren confirmacion previa:

- Enviar formularios reales.
- Ejecutar `POST`, `PUT`, `PATCH` o `DELETE` contra APIs externas.
- Subir archivos.
- Ejecutar JavaScript que modifique estado.
- Acceder a cuentas autenticadas.
- Guardar PDFs o capturas con informacion sensible.

### 4. Manejo de secretos

No publicar claves completas en logs, commits, issues o Pull Requests.

Formato de enmascaramiento:

```text
OPENAI_API_KEY=sk-************
ANTHROPIC_API_KEY=sk-ant-************
TOKEN=eyJ************
PASSWORD=************
```

Si se detecta un secreto expuesto:

1. Detener la operacion.
2. Identificar archivo y rama.
3. Revocar y rotar la credencial.
4. Revisar historial Git.
5. Confirmar `.gitignore`.
6. Crear `.env.example` sin valores reales.

### 5. Auditoria de dependencias

Ejecutar periodicamente:

```bash
npm audit
npm outdated
npm test
npm run build
```

Actualizar dependencias con PRs pequenos y pruebas.

### 6. Logging

Los logs deben evitar:

- Tokens completos.
- Cookies.
- Cabeceras de autorizacion completas.
- Datos personales.
- HTML sensible de sesiones autenticadas.

### 7. Politica de PRs

Cada PR debe incluir:

- Objetivo.
- Archivos modificados.
- Riesgos.
- Pruebas ejecutadas.
- Impacto operativo.

## Lista de verificacion antes de usar con Codex

- [ ] `AGENTS.md` existe y esta actualizado.
- [ ] El servidor se ejecuta en localhost.
- [ ] Las herramientas sensibles estan documentadas.
- [ ] No hay `.env` versionado.
- [ ] `npm run build` funciona.
- [ ] `npm test` funciona o los fallos estan documentados.
- [ ] No hay secretos visibles en README, docs o codigo.
- [ ] Las acciones contra APIs externas requieren confirmacion.

## Severidades

| Severidad | Criterio |
|---|---|
| Critico | Exposicion de secretos, datos personales o control externo no autorizado |
| Alto | Herramientas que modifican sistemas externos sin confirmacion |
| Medio | Configuracion debil, documentacion incompleta o tipos relajados |
| Bajo | Mejoras de claridad, mantenimiento o trazabilidad |
| Informativo | Datos utiles sin riesgo directo |
