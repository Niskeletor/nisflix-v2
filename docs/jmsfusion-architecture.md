# JMSFusion / MonWUI Plugin — Arquitectura Técnica
> Investigación: 2026-03-25 | Versión analizada: 2.1.0.0

## Stack

- **Backend**: C# / .NET 9.0 — sin npm, sin webpack, sin node
- **Frontend**: 30+ módulos ES modules + 23 archivos CSS embebidos en el assembly .NET
- **Target**: Jellyfin 10.10.0+

---

## Cómo inyecta el código en Jellyfin

Tres capas de fallback (de mayor a menor prioridad):

```
1. InMemoryRewriterFileProvider  ← intercepta index.html en HTTP, inyecta en memoria
2. IndexPatcher.cs               ← modifica index.html en disco
3. Middleware HTTP                ← fallback final
```

Lo que inyecta:
```html
<!-- SL-INJECT BEGIN -->
<script type="module" src="../slider/main.js"></script>
<script type="module" src="../slider/modules/player/main.js"></script>
<!-- SL-INJECT END -->
```

**En Docker** (nuestro caso): solo la capa 1 funciona (in-memory). Por eso
`Detected web root: (not found)` es normal e inofensivo.

---

## Servicio de archivos CSS/JS

Ruta: `GET /slider/{*path}`

Prioridad:
1. Si `ScriptDirectory` configurado → sirve desde disco
2. Si no → sirve desde recursos embebidos en el .dll

**Implicación importante**: podemos apuntar `ScriptDirectory` a una carpeta
en el servidor y servir nuestros propios CSS/JS sin recompilar el plugin.

---

## Sistema de variantes CSS

23 archivos CSS en `Resources/slider/src/`:

| Archivo | Uso |
|---------|-----|
| `normalslider.css` | Layout estándar (hero + contenido) |
| `fullslider.css` | Pantalla completa inmersiva |
| `peakslider.css` | Carrusel 3D con peek lateral |
| `pauseModul.css` | Pantalla de pausa |
| `detailsModal.css` | Modal de detalles |
| `studioHubs.css` | Colecciones de estudios |
| `profileChooser.css` | Selector de perfil |
| `player-dark.css` / `player-light.css` | Tema del reproductor |
| `notifications.css` (x4 variantes) | Notificaciones |

**Cambio de variante**: `html[data-css-variant=peakslider]` — atributo en `<html>`
set por JS desde config del usuario.

---

## API endpoints propios

| Endpoint | Método | Uso |
|----------|--------|-----|
| `/Plugins/JMSFusion/UserSettings` | GET | Settings por perfil (mobile/desktop) |
| `/Plugins/JMSFusion/UserSettings/Publish` | POST | Guardar settings |
| `/JMSFusion/config` | GET/POST | Configuración admin |
| `/JMSFusion/trailers/status` | GET | Estado descarga trailers |
| `/JMSFusion/trailers/run` | POST | Lanzar automatización trailers |
| `/JMSFusion/watchlist` | GET | Watchlist del usuario |
| `/JMSFusion/watchlist/items` | POST/DELETE | Añadir/quitar items |
| `/JMSFusion/watchlist/shares` | POST/DELETE | Compartir watchlist |

---

## Funcionalidades destacadas

### Trailer Automation
- Descarga trailers automáticamente vía **yt-dlp** + validación con **ffprobe**
- Fuente: TMDb API
- Hasta 8 descargas paralelas
- Crea archivos NFO con URLs de trailer

### Watchlist propio
- Sistema independiente del watchlist nativo de Jellyfin
- Límite: 500 items por usuario, 2000 shares
- Compartible entre usuarios

### CSS Loading dinámico
```javascript
// En player/main.js
loadCSS()  // Lee config (playerTheme, playerStyle) y carga link tags dinámicamente
```

### Auth (atención)
- Captura tokens via interceptación de `console.log`
- Almacena en sessionStorage + localStorage
- Soporte multi-servidor

---

## Cómo modificar sin recompilar

**Opción 1 — ScriptDirectory** (recomendada):
Configurar `ScriptDirectory` en el panel admin apuntando a una carpeta
accesible por el contenedor. El plugin servirá esos archivos en lugar
de los embebidos.

```
/home/stilgar/docker/jellyfin-clean/jmsfusion-custom/
├── main.js          ← override del main
└── src/
    └── nisflix-dark.css  ← nuestro CSS de variante
```

**Opción 2 — CSS injection via Jellyfin branding**:
Añadir CSS que sobrescriba los estilos de JMSFusion desde el campo
de CSS personalizado de Jellyfin. Funciona pero con menor especificidad.

**Opción 3 — Fork del plugin**:
Clonar el repo, modificar CSS/JS, compilar con `dotnet build`.
Más trabajo pero control total.

---

## Estado global en window

```javascript
window.__sliders_created   // número de slides creados
window.__cycle_time        // timing del slider
window.__auth_ready        // estado de autenticación
window.__booting_stages    // progreso del boot
```

Útil para debugging desde consola del navegador.

---

## Notas de compatibilidad

- Jellyfin 10.11.6 ✅ (nuestro jellyfin-clean)
- Docker ✅ (in-memory injection activo)
- Sin dependencias npm externas — todo self-contained en el .dll
