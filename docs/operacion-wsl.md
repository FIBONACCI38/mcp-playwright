# Operacion local en Windows + WSL

## Objetivo

Esta guia explica como operar el servidor MCP Playwright en un entorno local Windows con WSL Ubuntu, manteniendo seguridad, estabilidad y trazabilidad.

## Entorno recomendado

- Windows 10/11.
- WSL2 con Ubuntu.
- Node.js LTS.
- npm actualizado.
- Git instalado.
- Playwright con Chromium instalado.

## 1. Preparar carpeta de trabajo

```bash
mkdir -p ~/codex-repos
cd ~/codex-repos
```

## 2. Clonar repositorio

```bash
git clone https://github.com/FIBONACCI38/mcp-playwright.git
cd mcp-playwright
```

## 3. Revisar estado inicial

```bash
pwd
ls -la
git status
git branch
```

## 4. Instalar dependencias

```bash
npm install
```

## 5. Instalar navegador Chromium

```bash
npx playwright install chromium
```

Opcionalmente, instalar todos los navegadores:

```bash
npx playwright install
```

## 6. Compilar

```bash
npm run build
```

## 7. Ejecutar pruebas

```bash
npm test
```

Con cobertura:

```bash
npm run test:coverage
```

## 8. Ejecutar servidor MCP en modo HTTP

```bash
npx @executeautomation/playwright-mcp-server --port 8931
```

El servidor debe escuchar solamente en:

```text
127.0.0.1:8931
```

## 9. Probar health check

En otra terminal:

```bash
curl http://127.0.0.1:8931/health
```

Respuesta esperada aproximada:

```json
{
  "status": "ok",
  "version": "1.0.11",
  "activeSessions": 0
}
```

Nota: si `package.json` indica otra version, conviene sincronizar la version del codigo.

## 10. Configuracion MCP para cliente HTTP

```json
{
  "mcpServers": {
    "playwright": {
      "url": "http://localhost:8931/mcp",
      "type": "http"
    }
  }
}
```

## 11. Configuracion MCP por stdio

Para clientes que usan stdio:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"]
    }
  }
}
```

## 12. Trabajo seguro con Codex

Crear una rama antes de modificar:

```bash
git checkout -b codex/nombre-de-la-tarea
```

Ver cambios:

```bash
git status
git diff
```

Agregar cambios:

```bash
git add AGENTS.md docs/seguridad.md docs/operacion-wsl.md
```

Commit:

```bash
git commit -m "docs: add Codex operation and security guides"
```

Subir rama:

```bash
git push origin codex/nombre-de-la-tarea
```

## 13. Diagnostico rapido

```bash
node --version
npm --version
git --version
npm run build
npm test
curl http://127.0.0.1:8931/health
```

## 14. Problemas comunes

### Puerto ocupado

Ver procesos usando el puerto:

```bash
ss -ltnp | grep 8931
```

No matar procesos sin confirmar su funcion.

### Playwright no encuentra navegador

Ejecutar:

```bash
npx playwright install chromium
```

### Error de permisos npm

Evitar instalar globalmente con `sudo` salvo que sea estrictamente necesario. Preferir `npx` o Node instalado mediante `nvm`.

### WSL sin acceso correcto a red

Probar:

```bash
ping -c 3 registry.npmjs.org
npm ping
```

Si falla, revisar DNS de WSL antes de reinstalar paquetes.

## 15. Recomendaciones para produccion

Este servidor esta pensado principalmente para uso local. Antes de exponerlo remotamente:

- Agregar autenticacion.
- Mantenerlo detras de firewall.
- No enlazar a `0.0.0.0` sin control.
- Usar logs sanitizados.
- Definir allowlist de dominios si se automatizan sitios.
- Ejecutar pruebas.
- Crear backups si se integra con datos reales.

## 16. Comandos de mantenimiento

```bash
npm audit
npm outdated
npm run build
npm test
```

## 17. Cierre seguro

Para cerrar el servidor, usar `Ctrl + C` en la terminal donde se esta ejecutando.

Luego verificar que el puerto quedo libre:

```bash
ss -ltnp | grep 8931
```
