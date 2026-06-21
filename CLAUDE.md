# CLAUDE.md — cami-almacen

## 1. Contexto general del ecosistema CAMI

Este repo (`cami-almacen`) es **uno de varios** que conforman el sistema CAMI de Aceros Manufacturados. Antes de tocar este repo, conviene saber dónde encaja.

CAMI es una plataforma modular para operación interna (construcción / manufactura). Cada módulo es una app web separada, mobile-first, en su propio repo de GitHub bajo `alfredoaguado-arch/`. Todos comparten un backend de autenticación común y se montan vía GitHub Pages.

**Repos del ecosistema:**

| Repo | Propósito |
|---|---|
| `cami-app2` | Hub de login + lanzador de módulos |
| `cami-ot` | Órdenes de trabajo |
| `cami-almacen` | **Este repo.** Almacén / préstamo de herramienta |
| `cami-presupuesto` | Cotizaciones / presupuestos |
| `cami-requisicion` | Requisiciones de pago |
| `cami-nomina` | Nómina quincenal |
| `cami-reportes` | Reportes fotográficos |

**Stack global:**
- Frontend: HTML/CSS/JS puro, sin frameworks ni build. Un solo `index.html` por módulo.
- Backend: Google Apps Script. Cada módulo tiene su propio script *bound* a un Google Sheet. Existe además un **Apps Script central** para auth.
- Datos: Google Sheets.
- Documentos: Google Drive (PDFs).
- Hosting: GitHub Pages hoy; migración planeada a Hostinger (`aceroscami.com`).

## 2. Qué es este módulo

`cami-almacen` es el módulo de **almacén y préstamo de herramientas**. Permite registrar entrada y salida de herramientas, controlar quién tiene qué, y mantener un inventario básico.

**Estado:** Operativo.

## 3. Patrón de autenticación

Como todos los módulos del ecosistema, este módulo:

1. Lee `sessionStorage.cami_session` (JSON con `{token, nombre, rol, apps, proyectos}`)
2. Manda `token` en cada request a su propio Apps Script
3. El Apps Script de almacén valida el token vía HTTP contra el central antes de procesar
4. Si el token expiró (4h), redirige a login

**App key requerida:** (revisar `index.html` para confirmar — probablemente `almacen`)

## 4. Convenciones técnicas

**Estilo visual:**
- Tipografía: Courier New (monoespaciada)
- Mobile-first, sin frameworks
- Diseño coherente con cami-app2

**JavaScript:**
- Sin frameworks. Vanilla JS.
- `fetch` directo al Apps Script con `Content-Type: text/plain;charset=utf-8` (para evitar preflight CORS)
- Cuerpo siempre `JSON.stringify({action, token, ...payload})`

**HTML:**
- Un solo archivo. CSS embebido en `<style>`, JS embebido en `<script>`.

## 5. Reglas de modificación

**SÍ tocar este repo cuando:**
- Cambios al flujo de préstamo / devolución de herramientas
- Ajustes al catálogo de herramientas / equipos
- Nuevas vistas o reportes de inventario

**NO tocar este repo cuando:**
- Cambios al patrón de auth global (eso es cami-app2 + central)
- Cambios al patrón de folio o metadata (eso afecta a todos los módulos)

**Antes de cualquier cambio:**
- Confirmar el plan conmigo (Alfredo) antes de generar código

## 6. Despliegue

**GitHub Pages:**
- Push a `main` despliega automáticamente
- URL: `https://alfredoaguado-arch.github.io/cami-almacen/`
- Tarda 1-2 minutos en propagar

**Backend (Apps Script):**
- Editar en el editor de Apps Script bound al sheet del módulo
- Deploy → Manage deployments → ✏️ → New version → Deploy
- La URL del endpoint NO cambia entre versiones

## 7. Migración planeada

A futuro, este módulo se mueve a Hostinger bajo `aceroscami.com/almacen/` para mantener `sessionStorage` compartido con los demás módulos en subcarpetas.

## 8. Patrón de colaboración con Claude

- Alfredo confirma plan antes de codear cualquier cambio
- Los cambios se prueban primero en local antes de hacer commit
- Siempre commit con mensaje descriptivo (`vX.Y — qué cambia`)
- Después de commit, recarga forzada en la app para validar

## 9. Nota

Este módulo es de baja prioridad operativa por ahora. La mayor parte del trabajo está enfocado en cami-nomina y futuro cami-procesos. Cambios estructurales aquí pueden esperar.
