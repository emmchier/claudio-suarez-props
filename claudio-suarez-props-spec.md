# Claudio Suárez Propiedades — Especificaciones del proyecto

> **Archivo de contexto maestro.** Claude debe leer este archivo al inicio de cada sesión de trabajo
> sobre este proyecto, antes de escribir código o investigar documentación.
> Última actualización: 2026-11-17 10:05 (ART) · Versión: **0.3** (listo para retomar)

---

# ▶ RETOMAR ACÁ — Estado al cierre de la sesión del 17/11 (mañana)

> **Cómo retomar este proyecto.** Decí **"Claudio"** y nada más. Dos vías para que Claude recupere
> este contexto, en orden de preferencia:
> 1. **Desde GitHub** (recomendado): el repo es `https://github.com/emmchier/claudio-suarez-props`.
>    Claude lo adjunta a la sesión con la herramienta de repos de GitHub, lo clona y lee
>    `claudio-suarez-props-spec.md`. No hace falta que el usuario adjunte nada.
> 2. **Adjuntando el archivo**: `claudio-suarez-props-spec.md`, que vive en
>    `~/Documents/proyectos/claudio-suarez-props/` en la Mac del usuario.
>
> Una sesión nueva arranca sin memoria y con el workspace vacío: **sin una de esas dos vías no hay
> contexto**. Claude lee este bloque primero y sigue desde "Plan de la próxima sesión".


**Cómo se retoma:** el usuario abre una sesión nueva, **adjunta este archivo** y dice "sigamos
con el artefacto de Claudio Props". Una sesión nueva arranca con el workspace vacío: sin este
archivo adjunto no hay contexto. Claude lee este bloque primero y sigue desde "Plan de la próxima
sesión".

### Dónde quedamos
Diagnóstico cerrado y arquitectura decidida (§1–§3). Estado real del sitio verificado por API
(§0). CMS existente auditado: se mantiene y se refina (§4). **No se escribió código todavía.**

### Decisión de orden de trabajo
Se construye **la ficha de detalle dinámica primero** (es la pieza de mayor riesgo técnico:
item page + binding + Data API + galería + mapa). Después listados, después Home.
Colección piloto: **`alquileres`**.

### Tareas pendientes del usuario (hacer en el editor, antes o durante la sesión)
- [ ] **T1.** Agregar los campos de §4.2 a `alquileres` (prompt listo para Aria más abajo).
- [ ] **T2.** Confirmar si ya existe la **item page dinámica** de `alquileres`
      (*Site Structure → Pages → Wix CMS*). Si no existe: *Add Page → Collection page →
      elegir `Alquileres` → Item page*.
- [ ] **T3.** (opcional, gratis ahora) Renombrar rutas: `/alquileres-de-propiedades` → `/alquileres`,
      `/ventas-de-propiedades` → `/ventas`, `/desarrollos-inmobiliarios` → `/desarrollos`.
- [ ] **T4.** Renombrar el sitio a "Claudio Suárez Propiedades".

### Prompt listo para pegarle a Aria (T1)
```
Agregá estos campos a la colección "alquileres" del CMS, respetando exactamente estas keys y tipos:
- esNueva (Boolean)
- barrio (Text)
- localidad (Text)
- lat (Number)
- lng (Number)
- codigo (Text, con índice único)
- expensas (Number)
- aptoCredito (Boolean)
- antiguedad (Number)
- superficieTerreno (Number)
- planos (Media Gallery)
- videoUrl (Text)
- tourUrl (Text)
- seoTitle (Text)
- seoDescription (Text)
No borres ni renombres ningún campo existente. No toques las otras colecciones.
```
(Los cambios de tipo de `servicios` y `caracteristicas` a ARRAY_STRING quedan para después:
hay data dummy cargada y conviene hacerlo con criterio, no automático.)

### Plan de la próxima sesión (en orden)
1. **Verificar por API** que los campos de T1 están creados y que la item page existe
   (endpoints en §9.3; `siteId` en §0). No pedir capturas si se puede leer por API.
2. **Claude entrega `PropertyHero`**: `component.tsx` + `<nombre>.extension.ts` + CSS, como
   archivos de este proyecto, con la ruta destino exacta
   (`src/extensions/site/components/property-hero/`) y el paso a paso de:
   scaffold con Wix Extension Builder → pegar código → build + regenerar manifest →
   colocar el ERC en la item page → bindear props al Data Source `Alquileres (item page)`.
   Props a exponer: `nombre`, `direccion`, `barrio`, `precio`, `moneda`, `estado`, `esNueva`,
   `imagenPrincipal`.
3. **Validar el binding de punta a punta** con un ítem dummy antes de seguir. Este paso es el
   que prueba que toda la arquitectura funciona.
4. Si 3 sale bien, seguir en la misma sesión con **`PropertyGallery`** y **`PropertySpecs`**
   (binding directo, sin Data API todavía).
5. Recién después, **`SimilarProperties`** (primer componente que usa Data API en código) y
   **`PropertyMap`** (necesita `lat`/`lng` cargados en el ítem de prueba).
6. Actualizar §11 y este bloque al cerrar la sesión.

### Fase 1.5 — antes de que Claudio cargue la primera propiedad real
Construir el **uploader de imágenes con compresión en el browser** (§13). No es opcional: es lo
que mantiene el sitio en el plan Basic. Incluye la dashboard page con el uploader, el HTTP
endpoint que genera la upload URL con permisos elevados, y el panel de auditoría de archivos
pesados.

### Criterio de "listo" de la fase actual
La item page de `alquileres` renderiza la ficha completa con ERCs propios, leyendo el ítem real
del CMS, en desktop y mobile, con la data dummy que ya está cargada.

---

## 0. Estado actual del sitio (verificado por API, sin abrir el canvas)

**Sitio de trabajo**
- Nombre en Wix: **`My Site 1`** (renombrar a *Claudio Suárez Propiedades*)
- `siteId`: **`a04f6b57-a30c-4290-9894-41601414daf7`**
- URL draft: `https://emmchierchie.wixstudio.com/my-site-1`
- Editor: **Studio Two** · Estado: **Draft** · Plan: **Free** · Velo: disabled
- Creado 13/11/2026 · Idioma `es` / región `AR` / TZ `America/Argentina/Buenos_Aires` / moneda `ARS`
- Apps instaladas: Promote SEO, **Wix Forms**, Wix Invoices (V2), Wix Payments, Site Members
- Cuenta: queda en la cuenta del usuario durante el desarrollo y también después
  (Claudio entra solo como colaborador al dashboard).

**Colecciones existentes (3, creadas por Aria desde el Excel)**
`alquileres` · `ventas` · `desarrollos` — **schema idéntico (espejo)**, 27 campos cada una.
Permisos ya correctos: `read: ANYONE`, `insert/update/remove: ADMIN`.
Plugin `EDITABLE_PAGE_LINK` activo y campo `_slug` (tipo SLUG) presente en las tres → ya están
preparadas para páginas dinámicas de ítem.

Campos actuales (keys reales, respetar estos nombres):
`nombre` TEXT · `_slug` SLUG · `descripcion` RICH_TEXT · `precio` NUMBER · `moneda` TEXT ·
`direccion` TEXT · `tipoPropiedad` TEXT · `dormitorios` NUMBER · `banos` NUMBER ·
`ambientes` NUMBER · `superficieCubierta` NUMBER · `superficieTotal` NUMBER ·
`tieneCochera` BOOLEAN · `caracteristicas` RICH_TEXT · `servicios` TEXT · `documentacion` TEXT ·
`estado` TEXT · `disponibilidad` BOOLEAN · `publicado` BOOLEAN · `destacada` BOOLEAN ·
`fechaAlta` DATE · `imagenPrincipal` MEDIA_IMAGE · `galeriaImagenes` MEDIA_GALLERY
(+ sistema: `_id`, `_createdDate`, `_updatedDate`, `_owner`)

**Colecciones de apps presentes:** `Locations/Locations`, `Members/*`,
`WixForms/0cf6fe79-...` = "Formulario de consulta inmobiliaria", `WixForms/9f34ab89-...` = Subscriptions.

**Páginas estáticas existentes (8)**
`/home` · `/alquileres-de-propiedades` · `/ventas-de-propiedades` · `/desarrollos-inmobiliarios` ·
`/nosotros` · `/contacto` · `/privacy-policy` · `/accessibility-statement`
Sitio **no publicado** todavía.
Las item pages dinámicas no aparecen en este listado (son otra categoría en Site Structure →
Pages → Wix CMS): **pendiente de confirmar a ojo**.

**Veredicto: NO borrar y empezar de cero.** El CMS está bien formado, con permisos correctos,
slug y page link listos, y ya tiene la data del Excel. Se refina (§4) y se reconstruyen las
páginas con ERCs, que es lo que íbamos a hacer igual. Rehacer todo solo costaría re-importar
el Excel.

---

## 1. Contexto

- **Cliente:** Claudio Suárez (cuñado del usuario). Inmobiliaria **Claudio Suárez Propiedades**.
- **Producto:** sitio web público + dashboard administrativo propio.
- **Plataforma:** **Wix Studio Two** (no Wix Studio, no Headless). Fundamento en §3.
- **Premisa de diseño:** usar **Editor React Components (ERC)** como unidad de construcción para
  tener dominio total del diseño sin las limitaciones de los componentes nativos.
- **Premisa de trabajo:** economía de tokens. El usuario ejecuta a mano todo lo que sea
  "clickear en el editor"; Claude escribe código, arquitectura y revisiones. Ver §9.

### Alcance en dos fases
1. **Sitio público (read-only):** Home, Nosotros, Contacto, TyC, Privacidad + listados
   (Alquileres, Ventas, Desarrollos) + página de detalle dinámica de cada ítem.
2. **Dashboard administrativo** para Claudio: crear, editar, eliminar, publicar/despublicar,
   marcar como nueva, marcar disponible/no disponible, en las 3 colecciones.
   **Además:** editar los datos de contacto y textos del sitio público (§4.4).

---

## 2. Stack y restricciones de Studio Two (verificado en docs, nov-2026)

### Lo que usamos
| Capa | Herramienta |
|---|---|
| UI del sitio | **ERC** (React + TypeScript, manifest-driven) |
| Estado compartido en página | **Data Sources** (context providers propios) |
| Utilidades compartidas | **Function libraries** |
| Datos | **Wix CMS** + **Data API** (`@wix/data`, preinstalado) |
| Binding sin código | **CMS Data Sources** (uno por colección + uno `(item page)`) |
| Backend | **HTTP endpoints** (solo los llama nuestro frontend), **backend events**, **service plugins** |
| Dashboard | **Dashboard pages / modals / plugins** (React) + **Dashboard SDK** + **WDS** |
| Entorno | Tab **Code** = VS Code en el navegador, npm, terminal, Aria, Wix Extension Builder |
| Contexto en runtime | Site SDKs: `@wix/site-service-router`, `-url`, `-pages`, `-lightbox`, `-locale`, `-site-theme`, `-device-info`, `-rendering-context`, `-head-appender` |

### Lo que NO existe en este release (restricciones duras)
- **Custom Elements**, `$w`, page code, Velo, datasets, data hooks, forms con código.
- **Custom CSS global** (todavía no soportado).
- **Web modules**, **HTTP functions públicas**, **routers propios**, **scheduled jobs**,
  **triggered emails**, custom actions de automations.
- **Sin integración Git**, sin IDE local, sin Wix IDE, sin Velo packages, sin testing funcional.
- No se pueden reemplazar las páginas por defecto de apps de negocio.
- Backend con límites de cómputo fijos, sin autoescalado.
- **Export to code (beta): un sitio con páginas dinámicas NO se puede exportar.**
  → El repo de GitHub se mantiene **a mano** (fuente de verdad local, copy/paste al tab Code).

### Límites del modelo de datos que condicionan el diseño
- Un CMS Data Source **solo muestra** datos: no agrega, no actualiza, no borra, no hace queries
  custom ni agregaciones.
- Un **ERC no puede leer ni controlar un CMS Data Source desde código**: solo recibe lo que se le
  bindea como props en el editor. Para datos en código → Data API o Data Source propio.
- La Data API respeta los permisos de cada colección. `auth.elevate()` **solo en backend**.
- Data events: solo colecciones creadas por el usuario.
- Consistencia eventual: `{ consistentRead: true }` cuando se lee justo después de escribir.
- Para enviar un mail al crear un lead: **automation con trigger custom llamada desde un HTTP
  endpoint** (los triggered emails no existen acá).

---

## 3. Decisión de arquitectura: Studio Two puro

**Resuelto: todo en Studio Two. No Headless, no híbrido.**
- Headless daría control total del frontend, pero habría que construir y hostear el frontend,
  perder el editor y el binding visual, y crear una app privada para tener dashboard. Costo alto,
  beneficio nulo acá.
- Híbrido fragmenta SEO, deploys y mantenimiento.
- Export to code no es vía de escape: no soporta páginas dinámicas, y este sitio las necesita.

### Granularidad de los ERC
**ERCs componibles**, no un monolito por página, porque el binding al CMS es *por prop*, los
componentes se reusan entre las 3 colecciones cambiando solo el binding, y Claudio puede reordenar
secciones sin tocar código. Excepción: un ERC "de página" compuesto internamente por
subcomponentes React propios, que recibe `_id`/`_slug` + `collectionId` por props y resuelve el
resto con la Data API.

### Patrón para los listados con filtros
Un repeater con CMS Data Source lista y pagina, pero no hace buscador con filtros combinados.
Por eso:
```
[Data Source propio: PropertySearchSource]  ← query con Data API + estado de filtros
   ├── [ERC FilterBar]      (bindeado a las funciones del Data Source)
   ├── [ERC ListingGrid]    (bindeado a items / isLoading / loadMore)
   └── [ERC ResultsSummary]
```

### Patrón para la ficha de detalle (item page dinámica)
- La item page la crea Wix (`Add Page > Collection page > Item page`); adjunta el Data Source
  `Colección (item page)`, que siempre contiene el ítem actual.
- Props simples (nombre, precio, dirección, descripción, portada) → **binding directo**, cero código.
- Props con lógica (galería con lightbox, mapa, similares, formulario) → ERC que recibe
  `_id`/`_slug` por binding y consulta con la Data API.

---

## 4. Modelo de datos — refinamiento sobre lo que ya existe

Se mantienen las **3 colecciones** (`alquileres`, `ventas`, `desarrollos`) como pidió el usuario:
Claudio las administra separadas, y dan 3 prefijos de URL limpios para SEO.
**Regla de oro:** los campos compartidos conservan la misma key en las tres colecciones. Es la
condición para reusar los mismos ERC sin duplicar código.

### 4.1 Semántica de los campos de estado (definir antes de tocar nada)
Hoy hay cuatro flags que se solapan. Semántica acordada:
| Campo | Rol | Quién lo usa |
|---|---|---|
| `publicado` BOOLEAN | **gobierna la visibilidad en el sitio.** Todo query público filtra por `publicado = true` | toggle del dashboard |
| `disponibilidad` BOOLEAN | toggle rápido disponible / no disponible | toggle del dashboard |
| `estado` TEXT | etiqueta pública opcional con **valores controlados**: `disponible` · `reservado` · `cerrado` | badge en la card y la ficha |
| `destacada` BOOLEAN | aparece en el carrusel de Home | toggle del dashboard |

### 4.2 Campos a AGREGAR en `alquileres` y `ventas`
| Campo | Tipo | Por qué |
|---|---|---|
| `esNueva` | BOOLEAN | pedido explícito de Claudio (badge "Nueva") |
| `barrio` | TEXT | **crítico**: sin esto no hay filtro por zona |
| `localidad` | TEXT | **crítico**: idem |
| `lat` | NUMBER | **crítico**: sin esto no hay mapa |
| `lng` | NUMBER | idem |
| `codigo` | TEXT + **índice único** | referencia operativa de la inmobiliaria |
| `expensas` | NUMBER | alquileres / departamentos |
| `aptoCredito` | BOOLEAN | ventas |
| `antiguedad` | NUMBER | años |
| `superficieTerreno` | NUMBER | casas y terrenos |
| `planos` | MEDIA_GALLERY | |
| `videoUrl` | TEXT (URL) | |
| `tourUrl` | TEXT (URL) | tour 360 |
| `seoTitle`, `seoDescription` | TEXT | SEO por ítem |

### 4.3 Campos a MODIFICAR
- `servicios`: TEXT → **ARRAY_STRING** (tags). Hoy como texto libre no se puede filtrar.
- `caracteristicas`: RICH_TEXT → **ARRAY_STRING** (amenities filtrables). Si se quiere además un
  texto libre, que quede en `descripcion`.
- `moneda`: TEXT con valores controlados **`USD` | `ARS`** (validar en el form del dashboard).
- `tipoPropiedad`: TEXT con valores controlados: `casa` `departamento` `ph` `local` `oficina`
  `galpon` `terreno` `cochera`.
- `imagenPrincipal`: corregir el display name (hoy dice "Galería de imágenes (imagen principal)").
- Índices regulares a crear: `publicado`, `barrio`, `precio`, `tipoPropiedad`.

### 4.4 `desarrollos` — necesita schema propio
Hoy es copia exacta de las otras. Agregar: `estadoObra` TEXT (`pozo` `en-construccion`
`terminado`), `fechaEntrega` DATE, `precioDesde` NUMBER, `unidadesDisponibles` NUMBER,
`tipologias` ARRAY_STRING, `avanceObra` NUMBER (%), `financiacion` RICH_TEXT.
Reinterpretar (no borrar todavía): `dormitorios`/`banos`/`ambientes` pasan a leerse como
"desde", o se agregan `dormitoriosDesde` / `dormitoriosHasta`.

### 4.5 Nueva colección `configuracion` (singleton)
Para que Claudio edite los datos del sitio público desde el dashboard. **Un solo ítem.**
Campos: `nombreComercial`, `telefono`, `whatsapp`, `email`, `direccionOficina`, `horarios`,
`instagram`, `facebook`, `mapsUrl`, `matricula`, `logo` (MEDIA_IMAGE), `textoLegal` (RICH_TEXT),
`mensajeHome` (TEXT).
Permisos: `read: ANYONE`, escritura `ADMIN`. Se consume con un **Data Source propio** global
(`SiteConfigSource`) attachado al header/footer, o por Data API dentro de los ERC que la usen.

### 4.6 Leads / formulario de consulta
Hay dos caminos y Aria ya dejó instalado el primero:
- **A (MVP, cero código):** widget de **Wix Forms** ya instalado ("Formulario de consulta
  inmobiliaria"). Guarda submissions y manda notificaciones sin que escribamos nada.
  Contra: el diseño del form no es nuestro.
- **B (diseño propio):** ERC `ContactForm` → **HTTP endpoint** que valida, inserta con
  `auth.elevate()` en una colección `consultas` (cerrada al público) y dispara una **automation
  con trigger custom** para el mail a Claudio.
**Decisión: arrancar con A, migrar a B cuando el diseño lo pida.** Registrar el cambio en §11.

---

## 5. Mapa de páginas

| Página | Ruta actual | Acción |
|---|---|---|
| Home | `/home` | reconstruir con ERCs |
| Alquileres | `/alquileres-de-propiedades` | **renombrar a `/alquileres`** antes de publicar |
| Ventas | `/ventas-de-propiedades` | **renombrar a `/ventas`** |
| Desarrollos | `/desarrollos-inmobiliarios` | **renombrar a `/desarrollos`** |
| Nosotros | `/nosotros` | ok |
| Contacto | `/contacto` | ok |
| Privacy Policy | `/privacy-policy` | traducir a `/privacidad` |
| Accessibility Statement | `/accessibility-statement` | mantener |
| **Términos y Condiciones** | — | **falta crearla** |
| Detalle alquiler | item page sobre `alquileres` | confirmar si existe |
| Detalle venta | item page sobre `ventas` | confirmar si existe |
| Detalle desarrollo | item page sobre `desarrollos` | confirmar si existe |
| 404 | — | revisar |

> Renombrar rutas **ahora** es gratis: el sitio no está publicado. Después requiere redirects 301.

---

## 6. Inventario de ERC a construir

| ERC | Props clave | Dónde |
|---|---|---|
| `PropertyCard` | nombre, precio, moneda, imagenPrincipal, barrio, ambientes, superficieTotal, badges, itemPageLink | listados, home |
| `ListingGrid` | items[], isLoading, loadMore, renderCard (template container) | listados |
| `FilterBar` | valores + funciones del Data Source | listados |
| `PropertyHero` | nombre, direccion, precio, moneda, estado, esNueva | detalle |
| `PropertyGallery` | galeriaImagenes[], imagenPrincipal | detalle |
| `PropertySpecs` | dormitorios, banos, ambientes, tieneCochera, superficie*, antiguedad | detalle |
| `PropertyMap` | lat, lng, direccion | detalle |
| `SimilarProperties` | `_id`, `collectionId`, barrio, precio (Data API) | detalle |
| `ContactForm` | propiedadId, coleccion (solo en camino B de §4.6) | detalle, contacto |
| `WhatsAppCTA` | whatsapp, mensaje precargado (de `configuracion`) | global |
| `SearchHero` | — (navega con Router API) | home |
| `SiteConfigSource` | Data Source global de `configuracion` | header/footer |

Convenciones: el `type` del manifest arranca con `companionApp.`; los selectores deben ser clases
string planas (CSS Modules las reescribe en build); props de contenedor con `DATA` /
`CONTAINER_TYPE` de `@wix/react-component-schema`.

---

## 7. Dashboard administrativo

### 7.1 Login y seguridad — **ya resuelto por Wix, no se programa**
- Acceso con **cuenta de Wix** + **Roles & Permissions** del sitio. Sin auth custom, sin Members
  Area, sin sesiones.
- Claudio se invita como **colaborador** desde *Roles & Permissions* (invitación por email,
  válida 30 días).
- Rol recomendado: **Content Collection Manager** — agrega y modifica contenido de todas las
  colecciones y **no** puede editar otras áreas del sitio. Si hace falta granularidad, **rol custom**.
- Las dashboard pages **nunca aparecen en el sitio live** y solo las ven usuarios de Wix con los
  permisos requeridos. El código corre con la **identidad del usuario logueado**, acotado por su rol.
- Activar **verificación en dos pasos** en la cuenta de Claudio.
- ⚠️ **Asientos de colaborador:** 1 gratis por defecto; 5 en sitios de una cuenta de Wix Studio.
  Este sitio está en una cuenta Studio (`emmchierchie.wixstudio.com`) → hay margen.
- Con ese rol Claudio también administra las colecciones desde la **app móvil de Wix**.

### 7.2 Qué trae resuelto sin escribir código
- El **CMS dashboard nativo** ya hace alta, edición, borrado y vista de ítems.
- Publicar / nueva / disponible / destacada = togglear los booleans ahí mismo.
- → El dashboard custom **no bloquea el lanzamiento**. Orden: lanzar con CMS nativo, después la
  capa de UX.

### 7.3 Dashboard custom (fase 2)
- **Dashboard page** React agregada con Wix Extension Builder.
- UI con **Wix Design System (WDS)** + Patterns.
- **Dashboard SDK**: `navigate()`, modales y **`openMediaManager()`** (developer preview) para
  elegir/subir fotos desde la misma pantalla.
- CRUD con la **Data API**; operaciones sensibles o masivas vía HTTP endpoint con `elevate`.
- Pantallas: tabla de propiedades con filtros + toggles rápidos, form de alta/edición con galería,
  pantalla de `configuracion` (§4.5), bandeja de consultas, tablero simple.
- Sin scheduled jobs → no hay vencimiento automático de avisos.

---

## 8. Riesgos y cosas a vigilar
1. Release inicial de Studio Two: faltan piezas (CSS global, Git, HTTP functions públicas).
   Los paneles custom de ERC están marcados **alpha** en docs.
2. Sin Git: el repo es manual; la fuente de verdad del código vive local/GitHub.
3. Export to code no sirve acá (páginas dinámicas). No planificar round-trip.
4. SEO: los ERC rendean también en servidor, pero la semántica (h1/h2, `alt`, datos estructurados)
   es responsabilidad nuestra dentro del componente.
5. Accesibilidad: al no usar componentes nativos, teclado/ARIA quedan a cargo nuestro. Usar
   `refElement` sobre elementos nativos de Wix donde convenga para no perder validación/a11y.
6. Imágenes: usar URLs de Wix Media (`wix:image://`) y el escalado de la SDK Media API.
7. Plan Free: hace falta Premium para dominio propio.
8. Las fotos de las propiedades **todavía no existen** (el Excel vino sin fotos). Definir con
   Claudio cómo las entrega y el criterio de nombres.

---

## 9. Protocolo de trabajo (economía de tokens)

**División de tareas**
- **El usuario, a mano en Studio Two:** crear/modificar colecciones y campos, crear páginas,
  agregar las dynamic pages, colocar los ERC en el canvas, hacer los bindings, publicar,
  invitar colaborador.
- **Claude, en sesión:** arquitectura, schema, código de ERCs / Data Sources / HTTP endpoints /
  dashboard pages (como archivos de este proyecto), revisiones y debugging.

**Reglas**
1. Claude lee `specs.md` al inicio y **no re-investiga** lo que ya está documentado acá.
2. El código se entrega como archivos del proyecto; el usuario los copia al tab Code. Siempre con
   la ruta destino explícita.
3. **Inspección del sitio sin canvas:** Claude puede leer colecciones, campos, páginas estáticas,
   permisos y apps por el MCP de Wix con el `siteId` de §0. Usar esa vía antes de pedir capturas.
   - Colecciones + campos: `GET https://www.wixapis.com/wix-data/v2/collections`
   - Páginas estáticas: `POST https://www.wixapis.com/ricos/v1/site-context/static-pages/list`
   - Contexto del sitio: `GetSiteContext`
4. Revisión visual: preferir capturas del canvas antes de abrir el navegador. Claude in Chrome
   sobre el editor solo cuando haga falta de verdad.
5. Aria se usa para tareas mecánicas dentro del editor (scaffolding de extensiones, regenerar
   manifests), no para decisiones de arquitectura.
6. Toda decisión nueva se registra en §11 en la misma sesión en que se toma.
7. **Al cerrar cada sesión**, Claude actualiza el bloque **RETOMAR ACÁ** del inicio (dónde
   quedamos, tareas pendientes del usuario, plan de la próxima sesión) y le reenvía el archivo
   al usuario. Ese bloque es el único punto de entrada de la sesión siguiente.
8. El código que Claude entrega se guarda en `src/` de este proyecto con la misma estructura de
   carpetas que el tab Code de Studio Two, para que el repo de GitHub refleje el sitio.

---

## 10. Decisiones pendientes
- [ ] Renombrar el sitio a "Claudio Suárez Propiedades".
- [ ] **Comprar `claudiosuarezpropiedades.com`** (disponible al 17/11) junto con el plan anual.
- [ ] Elegir plan: Basic o Standard (§12.3).
- [ ] Confirmar si ya existen las item pages dinámicas de las 3 colecciones.
- [ ] ¿Las fotos las carga Claudio o el usuario? ¿Desde dónde?
- [ ] ¿Precios en USD con cotización o texto libre?
- [ ] Orden de construcción: propuesta de Claude = **ficha de detalle dinámica primero**
      (mayor riesgo técnico), después listados, después Home.
- [ ] Revisar la data dummy: ¿se queda como data de prueba o se limpia antes de publicar?

---

## 11. Changelog de decisiones
| Fecha | Decisión |
|---|---|
| 2026-11-17 | Plataforma: Studio Two puro. Descartados Headless e híbrido. |
| 2026-11-17 | ERCs componibles en lugar de un monolito por página. |
| 2026-11-17 | Listados con Data Source propio + Data API (los CMS Data Sources no filtran lo suficiente). |
| 2026-11-17 | Login del dashboard: Roles & Permissions de Wix + rol Content Collection Manager. Sin auth custom. |
| 2026-11-17 | Lanzar con CMS nativo; dashboard custom como fase 2. |
| 2026-11-17 | Se mantiene el sitio existente (`My Site 1`, Studio Two) y su CMS: se refina, no se rehace. |
| 2026-11-17 | Se mantienen 3 colecciones separadas, con keys compartidas idénticas. |
| 2026-11-17 | El sitio queda en la cuenta del usuario; Claudio entra solo como colaborador. |
| 2026-11-17 | Leads: arrancar con el Wix Forms ya instalado (camino A), migrar a ERC + endpoint si el diseño lo pide. |
| 2026-11-17 | Agregar colección `configuracion` singleton para los datos editables por Claudio. |

---

## 12. Costos — qué paga Claudio por tener esto online

### 12.1 Qué se paga y qué no
| Concepto | Costo |
|---|---|
| Studio Two (el editor) | **$0** — no se cobra aparte |
| Los ERC, el código, el backend, el dashboard custom | **$0** — la plataforma de desarrollo está incluida desde el plan más barato |
| Acceso de Claudio al dashboard | **$0** — es un asiento de colaborador, incluidos en el plan |
| Plan del sitio (para publicarlo con dominio propio y sin marca Wix) | **sí, es el único costo recurrente** |
| Dominio | sí, aparte (o gratis el 1er año con plan anual, según extensión) |

Dato relevante: en Studio Two **los límites de cómputo del backend son fijos e iguales para todos
los sitios**, sin importar el plan (en Wix Studio clásico dependían del plan). O sea: **no hay que
subir de plan por tener código, ERCs ni dashboard custom.**

### 12.2 Planes de Wix Studio (precios de lista, USD/mes con facturación anual)
| Plan | Precio | Storage | Colaboradores | Ítems de CMS | Dominio 1er año |
|---|---|---|---|---|---|
| **Basic** | **$19** | 10 GB | 3 | 1.500 | sí |
| **Standard** | **$27** | 50 GB | 5 | 4.000 | sí |
| Plus | $34 | 120 GB | 10 | 20.000 | sí |
| Elite | $159 | ilimitado | 100 | 10.000.000 | sí |

- Con facturación **mensual** el precio sube (ej. Basic ≈ $27/mes mensual vs $19/mes anual).
- Basic **no acepta pagos online** — irrelevante acá, el sitio no vende.
- Basic incluye: 4 formularios de captación, SEO avanzado, plataforma de desarrollo,
  versionado y edición concurrente.
- Hay garantía de devolución de 14 días.

### 12.3 Recomendación
- **Arrancar en Basic ($19/mes anual ≈ $228/año).** Cubre todo lo que necesita este proyecto.
- **Pasar a Standard ($27/mes anual ≈ $324/año) solo si** las fotos pasan los 10 GB. Con el
  pipeline de imágenes de §13 eso no debería pasar nunca.
- **Los ítems de CMS no son un problema:** el límite de 1.500 cuenta solo ítems de colecciones
  propias y de apps privadas. **Los ítems de colecciones de apps de Wix no cuentan**, así que las
  consultas que entran por **Wix Forms no consumen cupo**. Los 1.500 quedan enteros para
  propiedades. ⚠️ Si alguna vez migramos los leads a una colección propia (`consultas`, camino B
  de §4.6), **esos sí empiezan a contar**: es un argumento fuerte para quedarse con Wix Forms.
- Cada ítem de CMS admite hasta 512 KB en sus campos de texto/número; los archivos de media no
  cuentan ahí (van al Media Manager).
- Se puede subir de plan en cualquier momento con crédito proporcional de lo pagado.

### 12.4 Lo que hay que sumarle en Argentina
El precio es en USD, así que al pagar con tarjeta argentina se suma:
- **IVA servicios digitales 21%** (RG 4240).
- **Percepción 30% a cuenta de Ganancias** (RG 5617/2024) — recuperable en la declaración anual.
  *El Impuesto PAIS ya no se aplica (terminó en dic-2024).*
- **Percepción provincial de IIBB**, con alícuota según la provincia.
Estimación práctica: sobre el precio de lista hay que contar **~1,5x** el valor en pesos al dólar
oficial. ⚠️ El régimen impositivo cambia seguido: confirmar con el resumen de la tarjeta o el
contador antes de darle un número cerrado a Claudio.

**Estimación en ARS al 17/11/2026** (oficial venta BNA $1.575; multiplicador 1,51 = IVA 21% +
percepción 30%; sin IIBB provincial):
| Plan | USD/año | ARS/año | ARS/mes equiv. | Neto si recupera la percepción |
|---|---|---|---|---|
| **Basic** | $228 | **≈ $542.000** | ≈ $45.000 | ≈ $435.000 |
| Standard | $324 | ≈ $770.000 | ≈ $64.000 | ≈ $617.000 |

El dólar está en máximo nominal, así que **recalcular la cotización el día de la compra**.

### 12.5 Dominio — **`claudiosuarezpropiedades.com`**
- **Verificado el 17/11/2026: `claudiosuarezpropiedades.com` está DISPONIBLE** y es un dominio
  estándar (no premium). Es el dominio elegido para el proyecto.
- Se compra directamente desde Wix junto con el plan, así queda todo en una sola factura y la
  conexión al sitio es automática.
- Los **planes anuales incluyen voucher de dominio gratis por el primer año**, que cubre
  extensiones estándar como `.com`. Desde el segundo año el dominio renueva aparte.
- El precio del dominio se ve en el checkout; no tomar números de acá.
- ⚠️ **`claudiosuarezpropiedades.com.ar` no se puede comprar por Wix** (no soporta esa extensión).
  Si en algún momento lo quieren, se registra en **NIC.ar** (en pesos, sin impuestos de exterior)
  y se conecta al sitio como dominio externo. Conectar un dominio propio requiere plan pago.
- Alternativa libre verificada el 17/11: `csuarezpropiedades.com`.
- **Comprarlo pronto**: está libre hoy, y un nombre con el apellido del cliente conviene
  asegurarlo antes de publicar.

### 12.6 ¿Se puede pagar en cuotas sin interés?
**No, con tarjeta argentina no.** Dos razones independientes:
1. **Del lado de Wix:** el plan anual se cobra **una sola vez por todo el año**. Wix no ofrece
   cuotas; ofrece *ciclos de facturación*: mensual, anual, o 2 y 3 años (cada uno se cobra de una
   vez al inicio del período).
2. **Del lado argentino:** los consumos en moneda extranjera **no se pueden financiar en cuotas**,
   ni con tarjeta de crédito internacional; se pagan en el resumen del mes.

Alternativas reales:
- **Facturación mensual** en lugar de anual: es la forma de repartir el gasto. Costo: el precio
  por mes sube (Basic ~$27 vs ~$19) **y se pierde el voucher de dominio gratis**, que solo viene
  con planes de 1 año o más. En 12 meses eso es ≈ USD 96 más, más el dominio aparte.
- Algunos bancos financian el consumo en dólares **con interés** (no es cuota sin interés):
  depende del banco, hay que preguntarle.
- Truco conocido: dividir el pago entre dos tarjetas jugando con las fechas de cierre, lo que da
  dos resúmenes distintos. No es una cuota formal.
- Pagar con una tarjeta que debite de saldo en dólares evita la percepción del 30% (no involucra
  cambio de divisas). Es decisión de Claudio y su banco, no una recomendación nuestra.

**Recomendación:** pagar el **anual de una vez**. Es más barato, incluye el dominio del primer año
y evita 12 recargos impositivos mensuales. Si el desembolso único es un problema, mensual es
viable pero sale ~40% más caro al año.

### 12.6.1 Pagar con los dólares de Mercado Pago ✅ (opción elegida a evaluar)
Claudio tiene dólares en Mercado Pago. Pagar en dólares **sí ahorra, pero no todo**:
- **Se ahorra la percepción del 30%** (RG 5617). Solo se aplica cuando el consumo en dólares se
  cancela con pesos, porque ahí hay cambio de divisas. Pagando con dólares propios no corresponde.
  Con la tarjeta de Mercado Pago se puede elegir cancelar el consumo en dólares y la propia app
  avisa que así se evitan esos impuestos.
- **NO se ahorra el IVA del 21%** de servicios digitales (RG 4240). Ese no depende del cambio de
  divisas: lo percibe el intermediario de pago local sobre el servicio del exterior. Se paga igual.
- **NO se ahorra** la percepción provincial de IIBB.

| Forma de pago | Costo del plan Basic (USD 228) |
|---|---|
| En pesos (dólar tarjeta) | ≈ **$542.000** |
| **Con dólares propios (MP)** | ≈ **$435.000** |
| Ahorro | **≈ $107.000** |

Ese ahorro es justamente la percepción del 30%. Técnicamente es recuperable en la declaración
anual, pero hay que tramitarlo (RG 3819) y depende de si Claudio declara Ganancias. Pagando en
dólares el ahorro es inmediato y sin trámite.

⚠️ **Cuidados con Mercado Pago:**
- Evitar que el consumo se **pesifique automáticamente**: hay que cancelarlo en dólares. Si se
  paga con pesos de la cuenta, MP aplica un tipo de cambio que puede quedar ~4% arriba del oficial
  y del MEP, además de volver a meter la percepción.
- Es **tarjeta prepaga**: si no hay saldo suficiente el cargo se rechaza y el upgrade no se
  completa.
- **La renovación es automática al año.** Con tarjeta prepaga sin saldo, la renovación falla y el
  sitio puede caerse. Dejar recordatorio ~15 días antes del vencimiento y saldo disponible.
- Confirmar en la app el día de la compra que la opción de pagar el consumo en dólares está
  disponible para esa tarjeta.

### 12.6.2 Rutas para pagarlo "en cuotas"
Ninguna tarjeta argentina financia consumos en dólares, así que las cuotas no vienen del banco.
Rutas reales, ordenadas por conveniencia:

**0. No pagar nada todavía.** El sitio se construye y se prueba entero en el plan Free (preview,
test site y la URL `wixstudio.com`). Solo hace falta pagar para publicarlo con dominio propio.
Eso da semanas de aire para juntar la plata. **El dominio sí conviene asegurarlo pronto.**

**1. El usuario paga el anual y Claudio le devuelve en cuotas.** Costo financiero cero, sin banco,
sin interés. El sitio ya está en la cuenta del usuario, así que es el pagador natural.
≈ $435.000 pagando con dólares de MP, y Claudio devuelve en 3–6 pagos en pesos. **Recomendada.**

**2. Facturación mensual de Wix** = la cuota nativa, sin interés.
| | Pagando con dólares (MP) | Pagando en pesos |
|---|---|---|
| Basic mensual (USD 27) | ≈ **$51.500/mes** | ≈ $64.200/mes |
| Total 12 meses | ≈ $617.500 | ≈ $770.000 |
Contra los ≈ $435.000 del anual: **el financiamiento cuesta ≈ $182.000 al año (~42% más)**, y
además **se pierde el dominio gratis** (el voucher es solo para planes de 1 año o más).
Wix permite **cambiar el ciclo de facturación** de mensual a anual, así que se puede arrancar
mensual para no frenar el lanzamiento y pasar a anual cuando haya caja.

**3. Cuotificar el consumo en el banco.** Posible en algunos bancos, pero **con interés** y
pesificando el consumo (se pierde el ahorro del 30%). Última opción.

Hay **garantía de devolución de 14 días** si algo no cierra.

### 12.7 A confirmar
- [ ] **El precio exacto y la familia de plan** se ven en el checkout del propio sitio
      (*Dashboard del sitio → Upgrade*). Los sitios construidos en el editor de Studio van a
      **planes Studio**, no a Premium del Wix Editor; conviene verificarlo ahí en un clic, porque
      Studio Two es un editor nuevo y recién liberado a pocos usuarios.
- [ ] Si Wix ofrece promoción vigente para la cuenta (suele haber descuentos de primer año).

**Resumen para decirle a Claudio:** el sitio público + el dashboard administrativo salen
**~USD 19 a 27 por mes pagando el año adelantado**, más impuestos argentinos (~1,5x en pesos).
El dominio `claudiosuarezpropiedades.com` va **gratis el primer año** con el plan anual y se
renueva aparte desde el segundo. No hay costo extra por el desarrollo a medida, ni por su acceso
al panel.

---

## 13. Pipeline de imágenes (estrategia para quedarse en Basic)

**Objetivo:** que ninguna foto se almacene en su tamaño original, para no pasar los 10 GB del plan
Basic.

### 13.1 Lo que Wix ya hace y lo que no
- **Entrega:** ya está resuelta. Wix sirve las imágenes por su servicio de imágenes y puede
  escalar y recortar al vuelo (`scaleToFit`, `scaleToFill`, `crop` de la SDK Media API) desde el
  archivo guardado. **No hay que subir varios tamaños**: se guarda un solo master optimizado y el
  sitio pide la medida que necesita en cada breakpoint.
- **Almacenamiento:** NO está resuelto. Cada archivo subido se hostea y **cuenta contra el storage
  del plan**. Si Claudio sube una foto de celular de 4 MB, ocupa 4 MB para siempre.
- Conclusión: hay que **procesar antes de subir**. No hay forma de "comprimir después".

### 13.2 Arquitectura elegida: procesamiento en el browser
No se usa backend para esto: Studio Two tiene límites de cómputo fijos y no hay garantía de
librerías nativas de imagen. El navegador de Claudio hace el trabajo, gratis.

```
[Claudio elige fotos en NUESTRA dashboard page]
        ↓
1. createImageBitmap(file, { imageOrientation: 'from-image' })   ← corrige EXIF
2. dibujar en OffscreenCanvas redimensionado (lado largo máx. 2000 px)
3. convertToBlob({ type: 'image/webp', quality: 0.82 })          ← fallback JPEG
4. si el blob > 400 KB → reintentar bajando quality hasta 0.70
        ↓
5. HTTP endpoint propio → auth.elevate(files.generateFileUploadUrl)(mimeType, {
     fileName, filePath: 'propiedades/<codigo>' })               ← devuelve uploadUrl
        ↓
6. PUT uploadUrl?filename=<nombre> con el blob y su Content-Type
        ↓
7. guardar el id/url devuelto en `imagenPrincipal` / `galeriaImagenes` del ítem
```
**El archivo original nunca sale de la máquina de Claudio.**

Detalles de implementación:
- `files.generateFileUploadUrl` requiere scope **MANAGE-MEDIAMANAGER**, por eso va por HTTP
  endpoint con `auth.elevate()` y no directo desde el browser.
- Para archivos > 10 MB o red mala existe **Generate File Resumable Upload URL**; con fotos ya
  comprimidas no debería hacer falta.
- Un archivo recién subido **no está disponible de inmediato**: hay que manejar el estado
  "file ready" antes de escribir el ítem o mostrarlo.
- Errores a manejar: `SITE_QUOTA_EXCEEDED`, `FILE_SIZE_OVER_LIMIT`, `UNSUPPORTED_FILE_FORMAT`,
  `MISMATCH_MIME_TYPE`, `ZERO_FILE_SIZE`.
- Carpetas por `codigo` de propiedad (`filePath`) para poder auditar y limpiar después.

### 13.3 Números
| | Sin pipeline | Con pipeline |
|---|---|---|
| Foto típica de celular | 3–5 MB, ~4000 px | **~200–300 KB**, 2000 px WebP |
| Propiedad con 16 fotos | ~60–80 MB | **~3–4 MB** |
| Capacidad en 10 GB (Basic) | ~130 propiedades | **~2.500 propiedades** |

Con esto el plan Basic deja de ser un riesgo: es ~20x más capacidad.

### 13.4 Consecuencia de arquitectura ⚠️
**El pipeline solo funciona si Claudio sube las fotos por NUESTRA dashboard page.** Si usa el
uploader nativo del CMS, el Media Manager o `openMediaManager()`, el original entra sin procesar.
Por lo tanto:
- El **uploader custom pasa a ser parte obligatoria del alcance**, no un "nice to have" de fase 2.
  Se reubica como **fase 1.5**: va antes de que Claudio cargue la primera propiedad real.
- Hay que **enseñarle una sola vía de carga** y no darle acceso de Media Manager si se puede
  evitar con el rol.
- **Panel de auditoría** en el dashboard: `files.listFiles` / `searchFiles` devuelven
  `sizeInBytes`, así que se puede listar todo archivo > 500 KB para detectar los que entraron por
  otra vía. Limpieza con `bulkDeleteFiles`.

### 13.5 Reglas complementarias de storage
- **Nunca subir video a Wix**: se come el storage. Los videos van a YouTube/Vimeo y se guarda la
  URL en `videoUrl` (el ERC embebe el reproductor).
- **Carga inicial masiva** (las fotos que Claudio entregue al principio): script local en Node con
  `sharp`, que convierte la carpeta entera, y después se sube con el mismo endpoint o con
  `bulkImportFile` desde URLs públicas. Esto lo corremos nosotros, no Claudio.
- Los `planos` suelen ser PDF o imágenes grandes: mismo tratamiento, lado largo 2000 px.
- Revisar el consumo en *Settings → Manage Storage* del dashboard cada tanto.

---

## 14. Fuentes
- Studio Two Coding Features — https://dev.wix.com/docs/studio-two/get-started/studio-two-coding-features
- Compare Studio Two vs Wix Editor/Studio — https://dev.wix.com/docs/studio-two/get-started/compare-extending-websites-in-studio-two-to-wix-editor-and-wix-studio
- About Editor React Components — https://dev.wix.com/docs/studio-two/code-your-site/code-model/editor-react-components/about-editor-react-components
- Component Integration Patterns — https://dev.wix.com/docs/studio-two/code-your-site/code-model/component-integration-patterns
- About CMS Data Sources — https://dev.wix.com/docs/studio-two/code-your-site/work-with-data/data-sources-and-dynamic-pages/about-cms-data-sources
- About Dynamic Pages — https://dev.wix.com/docs/studio-two/code-your-site/work-with-data/data-sources-and-dynamic-pages/about-dynamic-pages
- Connect to Data vs. the Data API — https://dev.wix.com/docs/studio-two/code-your-site/work-with-data/introduction/connect-to-data-vs-the-data-api
- About the Data API — https://dev.wix.com/docs/studio-two/code-your-site/work-with-data/data-api/about-the-data-api
- About Collections — https://dev.wix.com/docs/studio-two/code-your-site/work-with-data/introduction/about-collections
- Dashboard Pages (extensions framework) — https://dev.wix.com/docs/overview/extensions-framework/dashboard-extensions/dashboard-pages
- About Dashboard Pages (Studio Two) — https://dev.wix.com/docs/studio-two/code-your-site/build-a-custom-dashboard/dashboard-pages/about-dashboard-pages
- openMediaManager() — https://dev.wix.com/docs/sdk/host-modules/dashboard/open-media-manager
- Roles & Permissions: inviting collaborators — https://support.wix.com/en/article/inviting-people-to-contribute-to-your-site
- About Exporting Your Site to Code — https://dev.wix.com/docs/studio-two/code-your-site/export-your-site/about-exporting-your-site-to-code
- List Data Collections (REST) — https://dev.wix.com/docs/api-reference/business-solutions/cms/collection-management/data-collections/list-data-collections
- List Static Pages (REST) — https://dev.wix.com/docs/api-reference/assets/rich-content/site-context-v1/list-static-pages
