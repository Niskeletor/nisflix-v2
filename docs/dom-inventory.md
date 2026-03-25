# NISFLIX DOM Inventory
> jellyfin-stg (8097) — Jellyfin 10.11.x
> Exploración: 2026-03-25

---

## LOGIN PAGE

### Estructura HTML real

```
#loginPage  (.page .standalonePage .backdropPage .mainAnimatedPage)
└── .padded-left.padded-right.padded-bottom-page.margin-auto-y
    ├── form.manualLoginForm.margin-auto-x
    │   ├── .padded-left.padded-right.flex.align-items-center.justify-content-center
    │   │   └── h1.sectionTitle              ← "Por favor, inicie sesión"
    │   ├── .inputContainer                  ← wrapper usuario
    │   │   ├── label.inputLabel.inputLabel-float.inputLabelFocused
    │   │   └── input#txtManualName.emby-input
    │   ├── .inputContainer                  ← wrapper contraseña
    │   │   ├── label.inputLabel.inputLabelUnfocused.inputLabel-float
    │   │   └── input#txtManualPassword.emby-input
    │   ├── label.checkboxContainer.emby-checkbox-label
    │   │   ├── input.chkRememberLogin.emby-checkbox  (type=checkbox)
    │   │   ├── span.checkboxLabel           ← texto "Recuérdame"
    │   │   └── span.checkboxOutline
    │   │       ├── span.material-icons.checkboxIcon.checkboxIcon-checked.check
    │   │       └── span.material-icons.checkboxIcon.checkboxIcon-unchecked
    │   └── button.raised.button-submit.block.emby-button (type=submit)
    │       └── span ← "Iniciar sesión"
    ├── .readOnlyContent
    │   ├── button.btnQuick.emby-button      ← "Usar conexión rápida"
    │   ├── button.btnForgotPassword.emby-button
    │   └── .loginDisclaimerContainer > .loginDisclaimer
    └── .visualLoginForm.hide                ← selector de usuario visual (oculto)
        └── #divUsers.itemsContainer

.skinHeader  (.skinHeader-withBackground .skinHeader-blurred .semiTransparent .noHeaderRight)
└── .flex.align-items-center.flex-grow.headerTop
    ├── .headerLeft
    └── .headerRight
```

### Selectores clave — login

| Elemento | Selector CSS |
|----------|-------------|
| Página completa | `#loginPage` |
| Fondo con imagen | `#loginPage` (background-image multicapa) |
| Formulario | `.manualLoginForm` |
| Título "Por favor..." | `.manualLoginForm h1.sectionTitle` |
| Wrapper input | `.inputContainer` |
| Input texto | `#txtManualName`, `#txtManualPassword`, `.emby-input` |
| Label flotante | `.inputLabel`, `.inputLabelFocused`, `.inputLabelUnfocused` |
| Checkbox container | `.checkboxContainer.emby-checkbox-label` |
| Checkbox visual | `.checkboxOutline` |
| Icono check (checked) | `.checkboxIcon-checked` |
| Botón submit | `.button-submit.emby-button` |
| Botón conexión rápida | `.btnQuick` |
| Botón olvidé contraseña | `.btnForgotPassword` |
| Header encima del form | `.skinHeader` |

### Estilos computados actuales (NISFLIX CSS aplicado)

| Elemento | Propiedad | Valor actual |
|----------|-----------|-------------|
| `#loginPage` | background | Multicapa: gradiente rojo + wallpapercave.com + radiales |
| `#loginPage` | font-family | Inter ✅ |
| `.inputContainer` | backdrop-filter | blur(20px) ✅ |
| `.inputContainer` | box-shadow | 3 capas (negro + rojo + blanco inset) ✅ |
| `.emby-input` | background | rgba(255,255,255,0.1) ✅ |
| `.emby-input` | border | 1px solid #e50914 (rojo al focus) ✅ |
| `.button-submit` | background | gradient 135deg rojo ✅ |
| `.button-submit` | font-weight | 700, uppercase, letter-spacing 1px ✅ |
| `.btnQuick` | background | mismo gradient que submit ⚠️ debería ser secundario |
| `h1.sectionTitle` | font-family | Bebas Neue ✅ |
| `h1.sectionTitle` | font-size | 26.78px |
| `.skinHeader` | background | rgba(20,20,20,0.95) ✅ |
| `.skinHeader` | height | 45.65px |

### Issues detectados en login

1. **`.btnQuick` y `.btnForgotPassword`** tienen el mismo gradient rojo que el botón
   primario — deberían ser botones secundarios (gris oscuro). Mismo selector
   `emby-button` los pilla a todos.

2. **`.inputContainer` background** es transparente — el blur se aplica pero sin
   fondo sólido detrás pierde efecto. Necesita `background: rgba(0,0,0,0.4)`.

3. **Imagen de fondo** viene de `wallpapercave.com` (externo). Mover al repo.

4. **Logo central** se inyecta vía CSS `::before` en
   `.padded-left.padded-right.flex.align-items-center.justify-content-center`
   — selector muy genérico, puede romperse en otras páginas.

5. **`h1.sectionTitle` de "Por favor, inicie sesión"** — mismo selector que
   sectionTitle del home/biblioteca. Usar `#loginPage h1.sectionTitle` para
   no contaminar otras páginas.

---

## HEADER (skinHeader)

### Estructura

```
.skinHeader
├── .headerTop.flex.align-items-center.flex-grow
│   ├── .headerLeft
│   │   ├── button.headerButton.headerSidebarToggle   ← hamburger
│   │   ├── a.headerButton.headerHomeButton           ← ir al home
│   │   ├── .headerLogo (img o background)            ← logo servidor
│   │   └── .headerSearchContainer (si activo)
│   └── .headerRight
│       ├── .headerCastButton (Chromecast)
│       ├── button.headerRemoteControlButton
│       ├── button.headerSyncButton
│       ├── button.headerNotificationsButton
│       ├── button.headerSearchButton
│       └── button.headerUserButton                   ← avatar usuario
└── .headerTabs.sectionTabs                           ← tabs (oculto en login)
```

### Selectores clave — header

| Elemento | Selector CSS |
|----------|-------------|
| Header completo | `.skinHeader` |
| Con fondo (scrolled) | `.skinHeader-withBackground` |
| Efecto blur | `.skinHeader-blurred` |
| Barra de tabs | `.headerTabs`, `.sectionTabs` |
| Botón hamburger | `.headerSidebarToggle` |
| Botón home | `.headerHomeButton` |
| Logo | `.headerLogo` |
| Lado derecho | `.headerRight` |
| Botón usuario | `.headerUserButton` |
| Botón búsqueda | `.headerSearchButton` |
| Botón notificaciones | `.headerNotificationsButton` |

### Notas

- `.skinHeader` tiene clase dinámica `.skinHeader-withBackground` cuando hay
  scroll — útil para header transparente en top + sólido al bajar
- `.noHeaderRight` aparece en login (sin menú usuario)
- Altura actual: **45.65px** — quizás algo pequeño para un look cinematográfico

---

## NOTAS GENERALES

### Fuentes confirmadas activas
- **Inter** — body, inputs, labels ✅
- **Bebas Neue** — h1, h2, h3, .sectionTitle ✅

### Variables CSS activas confirmadas
```css
--netflix-red: #e50914
--netflix-red-hover: #f40612
--netflix-dark: #141414
--netflix-text: #f2f2f2
--netflix-text-muted: #b3b3b3
```

### Imagen de fondo login — PENDIENTE migrar
- Actual: `https://wallpapercave.com/wp/wp1946560.jpg`
- Destino: `nisflix-v2/theme/assets/login-bg.jpg`
- CDN: `https://cdn.jsdelivr.net/gh/Niskeletor/nisflix-v2@main/theme/assets/login-bg.jpg`

---
*Siguiente zona: Home + Sidebar*
