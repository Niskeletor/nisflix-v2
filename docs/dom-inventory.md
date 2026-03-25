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

## HOME PAGE

### Estructura HTML real

```
#indexPage  (.page.homePage.libraryPage.allLibraryPage.pageWithAbsoluteTabs.withTabs.mainAnimatedPage)
├── #homeTab.tabContent.pageTabContent.is-active
│   └── .sections.homeSectionsContainer          ← display:flex, ::before = logo NISFLIX
│       ├── .verticalSection.section0             ← "Mis contenidos"
│       ├── .verticalSection.section1             ← "Seguir viendo"
│       ├── .verticalSection.section2.hide        ← "Continuar escuchando"
│       └── .verticalSection.sectionN             ← "Reciente en X" (50+ secciones)
├── #favoritesTab.tabContent.pageTabContent
├── #customTab_0.tabContent.pageTabContent        ← "Peticiones"
├── #customTab_1.tabContent.pageTabContent        ← "Mi Lista"
└── #customTab_2.tabContent.pageTabContent        ← "Calendario"

.skinHeader  (.noHomeButtonHeader — sin botón home en home)
└── .headerTop.flex.align-items-center.flex-grow
    ├── .headerLeft
    │   └── (sin headerHomeButton — clase noHomeButtonHeader activa)
    └── .headerRight
        └── (iconos: Emby Connect, Usuarios, Cast, Búsqueda, Avatar)
    + .headerTabs.sectionTabs  ← tabs de navegación (VISIBLE en home)
        ├── button.emby-tab-button.emby-tab-button-active  ← "Inicio"
        ├── button.emby-tab-button                         ← "Favoritos"
        ├── button#customTabButton_0.emby-tab-button       ← "Peticiones"
        ├── button#customTabButton_1.emby-tab-button       ← "Mi Lista"
        └── button#customTabButton_2.emby-tab-button       ← "Calendario"

#slides-container  ← Media Bar plugin (NO usa clases estándar de Jellyfin)
  width: 1428px, height: 1102.5px, position: relative, overflow: hidden
  (DOM interno controlado 100% por el plugin Media Bar)
```

### Selectores clave — home

| Elemento | Selector CSS |
|----------|-------------|
| Página home | `#indexPage`, `.homePage` |
| Tab activo | `.tabContent.is-active`, `#homeTab` |
| Sections container | `.sections.homeSectionsContainer` |
| Logo NISFLIX (::before) | `.sections.homeSectionsContainer::before` |
| Sección individual | `.verticalSection` |
| Título de sección | `.sectionTitle`, `h2.sectionTitle` |
| Título con enlace | `.sectionTitleContainer` |
| Tabs de navegación | `.headerTabs`, `.sectionTabs` |
| Tab button | `.emby-tab-button` |
| Tab activo | `.emby-tab-button-active` |
| Tab custom (plugins) | `button[id^="customTabButton_"]` |
| Media Bar container | `#slides-container` |
| Card | `.card` |
| Card box | `.cardBox` |
| Card escalable | `.cardScalable` |
| Card imagen | `.cardImageContainer` |
| Card backdrop | `.overflowBackdropCard` |
| Botones scroll | `.emby-scrollbuttons-button` |

### Estilos computados actuales

| Elemento | Propiedad | Valor | Estado |
|----------|-----------|-------|--------|
| `#slides-container` | height | 1102.5px | Media Bar controla |
| `.card` | border-radius | 0px ⚠️ | En cardBox sí (6px) |
| `.cardBox` | border | 1px solid rgba(255,255,255,0.1) ✅ | Nuestro CSS |
| `.cardBox` | border-radius | 6px ✅ | Nuestro CSS |
| `.cardImageContainer` | border-radius | 6px ✅ | |
| `.sectionTitle` | font-family | Bebas Neue ✅ | |
| `.sectionTitle` | font-size | 26.78px | |
| `.sectionTitle` | letter-spacing | 2.67px ✅ | |
| `.sectionTitle` | margin-bottom | 29.76px | amplio |
| `.emby-tab-button` | color | blanco ✅ | |
| `.emby-tab-button` | border-bottom | 0px ⚠️ | sin indicador activo |
| `.emby-tab-button-active` | border-bottom | 0px ⚠️ | sin indicador visual |
| `.emby-scrollbuttons-button` | opacity | 0.3 ⚠️ | muy tenue |
| `.sections.homeSectionsContainer` | display | flex | ojo con layout |

### Issues detectados en home

1. **Tab activo sin indicador visual** — `.emby-tab-button-active` no tiene
   `border-bottom` ni ningún marcador. El usuario no sabe qué tab está activo.
   Fix: `border-bottom: 3px solid var(--netflix-red)` al tab activo.

2. **Scroll buttons casi invisibles** — opacity: 0.3. Debería subir a 0.7
   en hover al menos.

3. **`#slides-container` height: 1102px** — el hero ocupa casi toda la pantalla
   en desktop. Puede ser correcto para Media Bar pero revisar en móvil.

4. **`.sectionTitle` margen inferior excesivo** — 29.76px entre título y cards.
   Podría reducirse a 16px para densidad visual mejor.

5. **`.sections.homeSectionsContainer` display:flex** — hay que tener cuidado
   con el `::before` (logo NISFLIX) para que no rompa el layout. Actualmente
   funciona pero es frágil.

6. **50+ secciones "Reciente en X"** — el home tiene una sección por cada
   colección. Visualmente muy repetitivo. CSS no puede reducirlas pero sí
   mejorar la diferenciación visual entre secciones.

### Tipos de cards en home

| Clase extra | Tipo |
|-------------|------|
| `.overflowBackdropCard` | Backdrop 16:9 (colecciones) |
| `.overflowPortraitCard` | Portrait (series/películas) |
| `.groupedCard` | Agrupada (seguir viendo) |
| `.card-withuserdata` | Con barra de progreso |
| `.card-hoverable` | Con hover effect |
| `data-type="CollectionFolder"` | Carpeta de colección |
| `data-type="Movie"` | Película |
| `data-type="Episode"` | Episodio |

### Overlays de plugins en cards (JS Injector)

| Selector | Plugin | Contenido |
|----------|--------|-----------|
| `.quality-overlay-container` | JS Injector | `.quality-overlay-label.resolution` (4K/1080p/720p/480p/LOW-RES) |
| `.quality-overlay-label.video-format` | JS Injector | HEVC/H264 |
| `.quality-overlay-label.audio-codec` | JS Injector | Dolby Digital+ 5.1, DTS 2.0... |
| `.language-overlay-container > img.language-flag` | JS Injector | Flags de idioma (ES, UK...) |
| `.rating-overlay-container > .rating-tag` | JS Injector | `.rating-tag-tmdb` (★ score) |
| `.rating-tag.rating-tag-critic > .rating-text` | Parody Critics | "63%", "61%"... |
| `.countIndicator.indicator` | Jellyfin std | Nº episodios (en badge azul) |
| `.itemProgressBar` | Jellyfin std | Barra de progreso de reproducción |

### Estructura interna card (portrait — "Seguir viendo")

```
.card.overflowPortraitCard.card-hoverable.groupedCard.card-withuserdata
└── .cardBox.cardBox-bottompadded
    ├── .cardScalable
    │   ├── .cardPadder.cardPadder-overflowPortrait
    │   ├── .blurhash-canvas
    │   ├── a.cardImageContainer.coveredImage.cardContent.itemAction.lazy
    │   │   ├── .cardIndicators
    │   │   │   └── .countIndicator.indicator       ← nº episodios
    │   │   ├── .language-overlay-container         ← flags
    │   │   ├── .quality-overlay-container          ← calidad video/audio
    │   │   └── .rating-overlay-container           ← ratings
    │   └── .cardOverlayContainer.itemAction
    │       ├── a.cardImageContainer
    │       ├── button.cardOverlayButton (play)
    │       └── .cardOverlayButton-br.flex
    │           ├── button (check/visto)
    │           ├── button (favorito)
    │           └── button (más opciones)
    ├── .cardText.cardTextCentered.cardText-first    ← título
    └── .cardText.cardTextCentered.cardText-secondary ← año - actualidad

.innerCardFooter.fullInnerCardFooter.innerCardFooterClear
└── .itemProgressBar                                 ← barra progreso
```

### Plugins visibles en home

| ID/Clase | Plugin |
|----------|--------|
| `#jf-jellyflare` | JellyFlare (banner de anuncios naranja, fixed top) |
| `#slides-container` | JMSFusion MediaBar (hero slider) |
| `.slide.active > .logo-container` | JMSFusion — logo del item |
| `.slide > .featured-content` | JMSFusion — tipo media |
| `.slide > .info-container` | JMSFusion — rating/año/cert |
| `.slide > .button-container` | JMSFusion — botón Reproducir |

---

## DETAIL PAGE

### Estructura HTML real

```
.page.libraryPage.itemDetailPage.noSecondaryNavPage.selfBackdropPage.mainAnimatedPage
├── .itemBackdrop                                ← imagen backdrop (490px alto, fija)
├── .detailLogo.lazy                             ← logo del título (imagen, abs posicionado)
└── .detailPageWrapperContainer
    ├── .detailPagePrimaryContainer              ← bg: rgb(16,16,16)
    │   ├── .detailImageContainer.hide-mobile    ← poster desktop (izquierda)
    │   │   └── .card.portraitCard > .cardBox
    │   ├── .detailRibbon.padded-left.padded-right  ← bg: rgba(32,32,32,0.8), margin-top: -107px
    │   │   ├── .infoWrapper
    │   │   │   ├── .detailImageContainer.hide-desktop.hide-tv ← poster móvil
    │   │   │   ├── .nameContainer
    │   │   │   │   └── h1.itemName.infoText.parentNameLast   ← "Domina"
    │   │   │   ├── .itemMiscInfo.itemMiscInfo-primary
    │   │   │   │   ├── .mediaInfoItem                        ← año "2021 - 2023"
    │   │   │   │   ├── .mediaInfoItem.mediaInfoOfficialRating ← "ES-18"
    │   │   │   │   └── .starRatingContainer.mediaInfoItem    ← "★ 6.8"
    │   │   │   └── .itemMiscInfo.itemMiscInfo-secondary.hide
    │   │   └── .mainDetailButtons.focuscontainer-x
    │   │       ├── button.btnPlay.detailButton               ← ▶ círculo rojo 56×56px
    │   │       ├── button.btnShuffle.detailButton
    │   │       ├── button.btnPlaystate.detailButton          ← ✓ visto
    │   │       ├── button.btnUserRating.detailButton         ← ♥ rating
    │   │       ├── button.jellyseerr-report-issue            ← ⚠ (Jellyseerr plugin)
    │   │       └── button.btnMoreCommands.detailButton       ← ⋮
    │   └── .detailPagePrimaryContent.padded-right
    │       └── .detailSection
    │           ├── .detailSectionContent
    │           │   ├── p.overview.detail-clamp-text          ← sinopsis
    │           │   └── ...metadata adicional (Aired, Etiquetas)
    │           ├── .itemDetailsGroup
    │           │   ├── .detailsGroupItem.genresGroup         ← Género: Drama
    │           │   └── .detailsGroupItem.studiosGroup        ← Estudios: Sky Atlantic
    │           ├── .nextUpSection.detailVerticalSection      ← "A Continuación"
    │           └── .detailVerticalSection (último visible)   ← Reviews (plugin TMDb)
    └── .detailPageSecondaryContainer.padded-bottom-page      ← bg: rgb(16,16,16)
        └── .detailPageContent
            ├── #childrenCollapsible.hide
            ├── #additionalPartsCollapsible.hide
            ├── .moreFromSeasonSection                        ← "TEMPORADAS"
            ├── section > .jf-ieg-box                        ← IMDb Episodes Grid (plugin)
            ├── #castCollapsible                              ← "Reparto y equipo"
            ├── #guestCastCollapsible.hide
            ├── #similarCollapsible                          ← "Más como este"
            └── .jellyseerr-details-section (×2)             ← Jellyseerr (streaming providers)
```

### Selectores clave — detail page

| Elemento | Selector CSS |
|----------|-------------|
| Página | `.itemDetailPage`, `.selfBackdropPage` |
| Backdrop | `.itemBackdrop` |
| Logo título | `.detailLogo` |
| Poster desktop | `.detailImageContainer.hide-mobile` |
| Ribbon | `.detailRibbon` |
| **Título h1** | `h1.itemName.infoText` ← ⚠️ NO es `.detailPagePrimaryTitle` |
| Metadata badges | `.itemMiscInfo-primary .mediaInfoItem` |
| Badge certificación | `.mediaInfoOfficialRating` |
| Rating | `.starRatingContainer` |
| Botones acción | `.mainDetailButtons` |
| Botón play | `.btnPlay.detailButton` |
| Sinopsis | `p.overview` |
| Grupo metadatos | `.itemDetailsGroup` |
| Item metadato | `.detailsGroupItem` |
| Label / Valor | `.label` / `.content` dentro de `.detailsGroupItem` |
| Sección vertical | `.detailVerticalSection` |
| Reparto | `#castCollapsible` |
| Similares | `#similarCollapsible` |
| Temporadas | `.moreFromSeasonSection` |

### Estilos computados actuales — detail page

| Elemento | Propiedad | Valor | Estado |
|----------|-----------|-------|--------|
| `.itemBackdrop` | height | 490px | ✅ |
| `.itemBackdrop` | backgroundImage | none ⚠️ | Sin imagen backdrop |
| `.detailLogo` | position | absolute | ✅ |
| `.detailLogo` | size | 357×196px | ✅ (si existe imagen) |
| `h1.itemName` | font-family | Bebas Neue ✅ | |
| `h1.itemName` | font-size | 26.784px ⚠️ | Muy pequeño para hero |
| `.detailRibbon` | background | rgba(32,32,32,0.8) ✅ | |
| `.detailRibbon` | margin-top | -107px | Overlap backdrop |
| `.btnPlay` | background | rgb(229,9,20) ✅ | Círculo rojo |
| `.btnPlay` | size | 56×56px, border-radius 50% ✅ | |
| `.btnShuffle` | background | transparent ⚠️ | Sin styling |
| `.mediaInfoItem` (año) | background | rgba(229,9,20,0.15) ✅ | Pill rojo |
| `.mediaInfoOfficialRating` | background | rgb(128,0,0) ✅ | Rojo oscuro |
| `.starRatingContainer` | background | rgba(42,42,42,0.8) ✅ | Gris oscuro |
| `.detailPagePrimaryContainer` | background | rgb(16,16,16) ✅ | |
| `.detailPageSecondaryContainer` | background | rgb(16,16,16) ✅ | |

### Plugins en detail page

| Selector | Plugin |
|----------|--------|
| `#jellyfin-oscars-detail-badge-styles` | Jellyfin Oscars (inyecta estilos CSS) |
| `.je-people-age-container` | JellyExtras — edad en cast cards |
| `.je-people-place-banner` | JellyExtras — ciudad/país en cast cards |
| `.jf-ieg-box` | IMDb Episodes Grid |
| `.detailSection.tmdb-reviews-section` | TMDb Reviews |
| `button.jellyseerr-report-issue` | Jellyseerr |
| `.jellyseerr-details-section` | Jellyseerr (streaming availability) |

### Issues detectados en detail page

1. **`h1.itemName` font-size: 26.784px** — igual que `.sectionTitle`. Para un título hero
   debería ser 3–4rem. Selector correcto: `h1.itemName.infoText`.

2. **`.detailPagePrimaryTitle` no existe** — nuestro `detail-page.css` usa este selector
   que no se aplica a nada. El real es `h1.itemName.infoText`.

3. **`.itemBackdrop` sin backdrop image** — puede ser que Domina no tenga backdrop en la
   BD, no es bug de CSS. Revisar con otro ítem.

4. **Botones secundarios sin estilo** — `.btnShuffle`, `.btnPlaystate`, `.btnUserRating`,
   `.btnMoreCommands` son transparentes. Deberían ser ghost circular (border: 1px solid
   rgba(255,255,255,0.3), border-radius: 50%).

5. **`.detailsGroupItem` sin estilos** — las filas Género/Estudios están en texto plano
   sin separación visual. Añadir `padding: 0.4rem 0`, `border-bottom: 1px solid rgba(255,255,255,0.05)`.

6. **`.je-people-age-container` y `.je-people-place-banner`** — badges de JellyExtras
   visibles en cast cards, sin integración con la paleta NISFLIX.

---
*Siguiente zona: Sidebar + Search*
