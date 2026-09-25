
<!-- .slide: data-background="#0c4a6e" -->
## Módulo 0488 · Desarrollo de Interfaces
### Unidad 8: Responsive Design en Aplicaciones Angular

Mobile First · Breakpoints Tailwind · BreakpointObserver · Dashboard Responsive

<small>CFGS DAM · Curso 2025/26</small>

Note:
Cerramos el bloque de diseño y estilos. Vamos a unir todo lo aprendido (UX, accesibilidad, Tailwind, componentes) y hacer que funcione en cualquier dispositivo. Del móvil al monitor 4K, pasando por tablet y ventanas de Electron. Pregunta: ¿quién ha probado su app en un móvil real?

---

<!-- .slide: data-background="#f0fdf4" -->
## 🎯 Objetivos de Aprendizaje

1. Aplicar <mark>Mobile First</mark> en aplicaciones de gestión empresarial
2. Dominar breakpoints de Tailwind: `sm`, `md`, `lg`, `xl`, `2xl`
3. Implementar estrategias responsive para <mark>cada tipo de componente</mark>
4. Utilizar `BreakpointObserver` de Angular CDK con Signals
5. Construir un dashboard completamente responsive (3 rangos)
6. Comprender responsive design en aplicaciones <mark>Electron</mark>

Note:
6 objetivos. Los 3 primeros son de estilos (Tailwind). El 4 es de lógica TypeScript. El 5 es el proyecto integrador. El 6 cubre el caso especial de aplicaciones de escritorio con Electron, donde la ventana puede redimensionarse a cualquier tamaño.

---

<!-- .slide: data-background="#f0fdf4" -->
## 🤔 Mobile First: Diseñar desde la Restricción

<div class="mermaid">
graph LR
    A[Desktop-First<br/>1920px → 375px] --> B[❌ Recortar<br/>Quitar elementos<br/>Información perdida]
    C[Mobile-First<br/>375px → 1920px] --> D[✅ Añadir<br/>Enriquecer layout<br/>Información complementaria]
</div>

<mark>Empezar por el móvil obliga a priorizar: ¿qué es esencial?</mark>

Note:
Mobile First NO es "diseñar solo para móviles". Es empezar por lo más restrictivo. Cuando diseñas en 1920px todo cabe, y luego al reducirlo tienes que amputar. Si empiezas en 375px, solo pones lo esencial y luego añades. El resultado es una interfaz más clara en todos los tamaños. Ejemplo real: el comercial que visita clientes con tablet, el encargado de almacén con móvil, el administrativo con monitor de 24". Todos usan la misma app.

---

## 📏 Breakpoints de Tailwind

<div class="mermaid">
graph LR
    subgraph Móvil
        A[0 - 639px<br/>sin prefijo]
    end
    subgraph "sm (640px)"
        B[640 - 767px<br/>sm:]
    end
    subgraph "md (768px)"
        C[768 - 1023px<br/>md:]
    end
    subgraph "lg (1024px)"
        D[1024 - 1279px<br/>lg:]
    end
    subgraph "xl (1280px)"
        E[1280 - 1535px<br/>xl:]
    end
    subgraph "2xl (1536px)"
        F[1536px+<br/>2xl:]
    end
</div>

<mark>Mobile-first: clases base = móvil, prefijos añaden hacia arriba</mark>

Note:
Estos breakpoints no representan dispositivos concretos (iPhone, iPad), sino rangos de espacio disponible. Diseñamos para "pantallas < 640px", no para "iPhone 15". Así el diseño funciona en cualquier dispositivo presente y futuro. En apps de gestión, el rango más usado es lg-xl (1024-1536px). Los breakpoints se pueden personalizar en @theme.

---

## 📐 Grid Responsive: De 1 a 4 Columnas

```html
<!-- La línea más importante del responsive design -->
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 md:gap-6">
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 1</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 2</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 3</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 4</div>
</div>
```

| Breakpoint | Columnas | Ancho aprox | Caso de uso |
|------------|----------|-------------|-------------|
| (base) | 1 col | < 640px | Móvil vertical |
| `sm:` | 2 cols | 640px+ | Móvil horizontal / tablet pequeña |
| `lg:` | 3 cols | 1024px+ | Tablet horizontal / portátil |
| `xl:` | 4 cols | 1280px+ | Desktop / monitor |

Note:
Esta línea de Tailwind es la navaja suiza del responsive. El gap también puede variar: `gap-4 md:gap-6`. Los widgets individuales no cambian de diseño, solo su disposición en el grid. Esto mantiene la consistencia visual. La clase base (sin prefijo) define el diseño móvil; los prefijos añaden columnas hacia arriba.

---

## 🧭 Estrategia Responsive: Sidebar

<div class="mermaid">
graph TD
    A[Sidebar] --> B[Móvil<br/>< 768px]
    A --> C[Tablet<br/>768px - 1023px]
    A --> D[Desktop<br/>≥ 1024px]
    B --> B1[Oculta. Drawer con overlay<br/>Botón hamburguesa<br/>fixed, translate-x]
    C --> C1[Colapsada: w-16<br/>Solo iconos<br/>Tooltip al hover]
    D --> D1[Expandida: w-64<br/>Icono + texto<br/>Submenús expandibles]
</div>

Note:
Tres comportamientos, un solo componente. En móvil: `fixed inset-y-0 left-0 z-40 w-64 -translate-x-full transition-transform`. Se abre añadiendo `translate-x-0` vía binding condicional. Overlay: `fixed inset-0 bg-black/50 md:hidden`. En tablet: `md:relative md:translate-x-0 md:w-16`. En desktop: `lg:w-64`. Contenido principal: `ml-0 md:ml-16 lg:ml-64`.

---

## 🧭 Sidebar: Código de la Transición

```html
<!-- Sidebar: un componente, tres comportamientos -->
<aside class="fixed inset-y-0 left-0 z-40
              w-64 bg-gray-900 text-white
              transition-transform duration-300
              -translate-x-full
              md:relative md:translate-x-0
              md:w-16 lg:w-64"
       [class.translate-x-0]="isMobileOpen()">
  <!-- Items de navegación -->
  <nav class="flex flex-col gap-1 p-2">
    <a class="flex items-center gap-3 px-3 py-2 rounded-lg
              md:justify-center lg:justify-start
              hover:bg-gray-800 transition-colors">
      <svg class="w-5 h-5 shrink-0">...</svg>
      <span class="hidden lg:block">Dashboard</span>
    </a>
  </nav>
</aside>

<!-- Overlay solo en móvil -->
@if (isMobileOpen()) {
  <div class="fixed inset-0 bg-black/50 z-30 md:hidden"
       (click)="closeSidebar()"></div>
}
```

Note:
El truco está en la combinación de clases base + responsive + binding condicional. La sidebar siempre tiene `-translate-x-full` (oculta fuera de pantalla) pero `md:translate-x-0` la muestra en tablet+. En móvil, `translate-x-0` se añade condicionalmente cuando `isMobileOpen()` es true. Los textos se ocultan con `hidden lg:block`. Los iconos se centran con `md:justify-center lg:justify-start`.

---

## 📊 Estrategia Responsive: Tablas de Datos

<mark>Dos técnicas complementarias: scroll horizontal + ocultación progresiva</mark>

```html
<div class="overflow-x-auto rounded-lg border border-gray-200">
  <table class="min-w-[800px] w-full text-sm">
    <thead>
      <tr>
        <th>Nombre</th>
        <th class="hidden md:table-cell">Email</th>
        <th class="hidden lg:table-cell">Fecha</th>
        <th class="hidden xl:table-cell">Departamento</th>
        <th>Estado</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>...</tbody>
  </table>
</div>
```

| Columna | Móvil | Tablet (md) | Desktop (lg) | XL |
|---------|-------|-------------|--------------|-----|
| Nombre | ✅ | ✅ | ✅ | ✅ |
| Email | ❌ | ✅ | ✅ | ✅ |
| Fecha | ❌ | ❌ | ✅ | ✅ |
| Depto | ❌ | ❌ | ❌ | ✅ |

Note:
El `min-w-[800px]` fuerza el scroll en pantallas más estrechas. Las columnas menos esenciales se ocultan con `hidden md:table-cell` (visible desde tablet), `hidden lg:table-cell` (visible desde desktop). La combinación de ambas técnicas garantiza que la tabla sea usable en todos los tamaños. Importante: los datos ocultos visualmente siguen en el DOM para lectores de pantalla (a menos que se marquen con `aria-hidden`).

---

## 📝 Estrategia Responsive: Formularios

```html
<form [formGroup]="form" class="flex flex-col gap-4">
  <!-- Sección: Datos Fiscales -->
  <fieldset class="border rounded-lg p-4">
    <legend class="text-lg font-semibold px-2">Datos Fiscales</legend>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <app-input label="Razón Social" formControlName="name" />
      <app-input label="CIF" formControlName="cif" />
      <app-input label="Dirección" formControlName="address"
                 class="md:col-span-2" />
    </div>
  </fieldset>

  <!-- Sección: Contacto -->
  <fieldset class="border rounded-lg p-4">
    <legend class="text-lg font-semibold px-2">Contacto</legend>
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
      <app-input label="Teléfono" formControlName="phone" />
      <app-input label="Email" formControlName="email" />
    </div>
  </fieldset>

  <app-button variant="primary" type="submit"
              [loading]="isSubmitting()">Guardar cliente</app-button>
</form>
```

Note:
Formulario con secciones agrupadas en fieldsets. Cada sección usa grid responsive: 1 columna en móvil, 2 en desktop. Campos que ocupan ancho completo usan `md:col-span-2`. Las etiquetas siempre visibles, no placeholders. Validación reactiva en tiempo real con mensajes de error junto al campo. En móvil, los campos se apilan verticalmente; en desktop, se distribuyen en columnas.

---

## 🪟 Estrategia Responsive: Modales

```html
<!-- Modal: fullscreen en móvil, centrado en desktop -->
@if (isOpen()) {
  <div class="fixed inset-0 z-50 flex items-end md:items-center
              justify-center"
       (click)="closeOnOverlay() && close()">
    <div class="fixed inset-0 bg-black/50"></div>
    <div class="relative bg-white shadow-2xl overflow-y-auto
                w-full rounded-t-2xl md:rounded-2xl
                md:max-w-lg md:mx-4
                max-h-[90vh]"
         cdkTrapFocus role="dialog" aria-modal="true">
      <!-- Contenido del modal -->
    </div>
  </div>
}
```

| Propiedad | Móvil | Desktop |
|-----------|-------|---------|
| Posición | `items-end` (anclado abajo) | `md:items-center` (centrado) |
| Ancho | `w-full` (toda la pantalla) | `md:max-w-lg` (limitado) |
| Bordes | `rounded-t-2xl` (solo arriba) | `md:rounded-2xl` (todos) |

Note:
El modal es uno de los componentes que más cambia entre móvil y desktop. En móvil, anclado abajo (como una action sheet nativa) o fullscreen. En desktop, centrado con ancho máximo. Las variantes responsive de Tailwind permiten ambas cosas en un solo componente. La accesibilidad (cdkTrapFocus, Escape, aria) es idéntica en ambos casos.

---

## 🔍 BreakpointObserver de Angular CDK

<mark>Tailwind para lo visual. BreakpointObserver para lógica de negocio</mark>

```typescript
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { toSignal } from '@angular/core/rxjs-interop';
import { map } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class LayoutService {
  private bo = inject(BreakpointObserver);

  readonly isMobile = toSignal(
    this.bo.observe(['(max-width: 767px)']).pipe(map(s => s.matches)),
    { initialValue: true }
  );

  readonly isTablet = toSignal(
    this.bo.observe([Breakpoints.Medium]).pipe(map(s => s.matches)),
    { initialValue: false }
  );

  readonly isDesktop = toSignal(
    this.bo.observe([Breakpoints.Large, Breakpoints.XLarge])
      .pipe(map(s => s.matches)),
    { initialValue: false }
  );
}
```

Note:
`BreakpointObserver` trabaja con media queries CSS. En Angular 17+, `toSignal` convierte el observable en una señal reactiva. Esto se integra limpiamente con el modelo de señales. ¿Cuándo usar BreakpointObserver? Cuando necesitas lógica TypeScript: ¿inicializo el editor drag-and-drop? (solo desktop), ¿cargo datos completos o resumidos? (móvil = resumidos), ¿cambio el comportamiento de navegación? (móvil = push, desktop = tabs).

---

## 🔀 Sidebar Inteligente con BreakpointObserver

```typescript
@Component({ ... })
export class SidebarComponent {
  private layout = inject(LayoutService);

  manualOpen = signal(false);
  autoOpen = signal(false);

  readonly isOpen = computed(() => {
    if (this.layout.isMobile()) return this.manualOpen();
    return this.layout.isDesktop();
  });

  readonly sidebarState = computed(() => {
    if (this.layout.isMobile()) return 'drawer';
    if (this.layout.isTablet()) return 'collapsed';
    return 'expanded';
  });

  // Al cruzar breakpoints, ajustar automáticamente
  constructor() {
    effect(() => {
      if (this.layout.isDesktop()) this.manualOpen.set(true);
      if (this.layout.isMobile()) this.manualOpen.set(false);
    });
  }
}
```

Note:
La sidebar inteligente: en móvil se abre/cierra manualmente. Al cruzar a tablet, se colapsa automáticamente. Al cruzar a desktop, se expande automáticamente. Si redimensionas de desktop a móvil, se cierra automáticamente. Los `effect()` reaccionan a cambios en las señales del LayoutService y ajustan el estado. La señal computada `sidebarState` devuelve 'drawer', 'collapsed' o 'expanded'.

---

## 🖥️ Responsive en Electron

<mark>La misma app Angular funciona en navegador y en ventana nativa</mark>

```javascript
// main.js (proceso principal de Electron)
const { BrowserWindow } = require('electron');

const win = new BrowserWindow({
  width: 1280,
  height: 800,
  minWidth: 800,    // Tamaño mínimo: la UI deja de ser funcional
  minHeight: 600,
  webPreferences: {
    nodeIntegration: false,
    contextIsolation: true
  }
});

win.loadURL('http://localhost:4200'); // Misma app Angular
```

- Sin código responsive específico de Electron: Tailwind y BreakpointObserver funcionan igual
- Definir `minWidth`/`minHeight` para evitar ventanas demasiado pequeñas
- En pantallas 4K: centrar contenido con `max-w-screen-2xl mx-auto`
- La ventana puede redimensionarse libremente → la app debe responder

Note:
Electron ejecuta tu app Angular en un Chromium embebido. Todo el código responsive (Tailwind, BreakpointObserver) funciona exactamente igual que en el navegador. La diferencia principal: defines tamaños mínimos de ventana desde el proceso principal. En pantallas muy grandes (4K), puedes centrar el contenido con `max-w-screen-2xl mx-auto` para evitar que un dashboard de 4 widgets se vea ridículo.

---

## 🏗️ Proyecto: Dashboard Completamente Responsive

<div class="mermaid">
graph TD
    subgraph Móvil
        M1[Navbar sup. + hamburguesa]
        M2[Sidebar: drawer overlay]
        M3[Widgets: 1 columna]
        M4[Tabla: scroll horiz. + 3 cols]
        M5[Navbar inferior: 4 iconos]
    end
    subgraph Tablet
        T1[Sidebar colapsada: w-16]
        T2[Widgets: 2 columnas]
        T3[Tabla: scroll + 5 cols]
        T4[Sin barra inferior]
    end
    subgraph Desktop
        D1[Sidebar expandida: w-64]
        D2[Widgets: 3-4 columnas]
        D3[Tabla: todas las cols]
        D4[Navegación completa]
    end
</div>

Note:
Vamos a construir este dashboard como proyecto integrador. Tres experiencias distintas, un solo código base. La barra inferior de navegación solo en móvil (alcance del pulgar). La sidebar se transforma gradualmente. Los widgets se reorganizan. La tabla muestra más columnas a medida que hay más espacio. Todo con Tailwind responsive + BreakpointObserver para la lógica de la sidebar.

---

## 📱 Vista Móvil (< 768px)

```html
<!-- Layout móvil -->
<div class="min-h-screen bg-gray-50 pb-16">
  <!-- Navbar superior fijo -->
  <nav class="fixed top-0 inset-x-0 z-30 h-14 bg-white
              border-b border-gray-200 flex items-center
              justify-between px-4">
    <button class="p-2 rounded-lg hover:bg-gray-100"
            (click)="toggleSidebar()">☰</button>
    <span class="text-lg font-semibold">Dashboard</span>
    <div class="w-8 h-8 rounded-full bg-primary-500">...</div>
  </nav>

  <!-- Contenido principal -->
  <main class="pt-14 px-4">
    <div class="grid grid-cols-1 gap-4">
      <app-stats-widget ... />
      <app-stats-widget ... />
      <app-stats-widget ... />
    </div>
    <app-data-table class="mt-4" [mobileColumns]="3" />
  </main>

  <!-- Barra inferior de navegación -->
  <nav class="fixed bottom-0 inset-x-0 z-30 h-14 bg-white
              border-t border-gray-200 flex items-center
              justify-around md:hidden">
    <a class="flex flex-col items-center text-xs
              text-primary-600">📊 Home</a>
    <a class="flex flex-col items-center text-xs
              text-gray-400">👥 Clientes</a>
    <a class="flex flex-col items-center text-xs
              text-gray-400">📦 Pedidos</a>
    <a class="flex flex-col items-center text-xs
              text-gray-400">⚙️ Ajustes</a>
  </nav>
</div>
```

Note:
Vista móvil: navbar superior con hamburguesa, contenido en 1 columna con padding ajustado, tabla con scroll horizontal y solo 3 columnas, y barra de navegación inferior fija (`md:hidden` la oculta en tablet+). El padding-bottom (pb-16) reserva espacio para la barra inferior. El padding-top (pt-14) para la barra superior fija.

---

## 💻 Vista Tablet (768px - 1023px) y Desktop (≥ 1024px)

```html
<!-- Añadiendo variantes md: y lg: al mismo HTML base -->
<div class="min-h-screen bg-gray-50 pb-16 md:pb-0">
  <!-- Navbar: hamburguesa oculta en tablet+ -->
  <nav class="fixed top-0 inset-x-0 z-30 h-14 md:h-16
              bg-white border-b flex items-center
              justify-between px-4 md:px-6">
    <button class="p-2 rounded-lg hover:bg-gray-100 md:hidden"
            (click)="toggleSidebar()">☰</button>
    ...
  </nav>

  <!-- Sidebar -->
  <aside class="hidden md:flex md:flex-col md:fixed md:inset-y-0
                md:left-0 md:z-40 md:w-16 lg:w-64
                bg-gray-900 text-white transition-all duration-300">
    ...
  </aside>

  <!-- Contenido con margen dinámico -->
  <main class="pt-14 md:pt-16 md:ml-16 lg:ml-64
               p-4 md:p-6 lg:p-8 transition-all duration-300">
    <!-- Grid responsive -->
    <div class="grid grid-cols-1 sm:grid-cols-2
                lg:grid-cols-3 xl:grid-cols-4 gap-4 md:gap-6">
      ...
    </div>
  </main>
</div>
```

Note:
Las variantes responsive se añaden al mismo HTML. `md:hidden` oculta la hamburguesa y la barra inferior. `hidden md:flex` muestra la sidebar desde tablet. `md:w-16 lg:w-64` controla el ancho. `md:ml-16 lg:ml-64` ajusta el margen del contenido. `p-4 md:p-6 lg:p-8` amplía el padding progresivamente. El resultado: una sola base de código, tres experiencias.

---

## 🔄 Navegación Adaptativa

| Dispositivo | Patrón de navegación | Tailwind |
|-------------|---------------------|----------|
| **Móvil** | Barra inferior fija (3-5 iconos) + hamburguesa | `fixed bottom-0 ... md:hidden` |
| **Tablet** | Sidebar colapsada (w-16, solo iconos) | `hidden md:flex md:w-16` |
| **Desktop** | Sidebar expandida (w-64, icono + texto) | `lg:w-64` |
| **Desktop grande** | Sidebar expandida + submenús visibles | `xl:w-72` |

<mark>La barra inferior móvil sitúa las acciones al alcance del pulgar</mark>

Note:
La navegación es lo que más cambia entre dispositivos. En móvil, la barra inferior (patrón nativo) es ergonómica para el pulgar. En tablet/desktop, la sidebar lateral aprovecha el espacio horizontal. La transición es gradual y se implementa con variantes responsive de Tailwind, no con componentes diferentes.

---

## 🖼️ Imágenes y Medios Responsive

```html
<!-- Imagen responsive con srcset -->
<img src="hero-mobile.webp"
     srcset="hero-mobile.webp 640w,
             hero-tablet.webp 1024w,
             hero-desktop.webp 1920w"
     sizes="(max-width: 640px) 100vw,
            (max-width: 1024px) 50vw,
            33vw"
     class="w-full h-48 md:h-64 lg:h-80 object-cover rounded-xl"
     loading="lazy"
     alt="Dashboard overview" />

<!-- Contenedor de vídeo con aspect-ratio -->
<div class="aspect-video rounded-xl overflow-hidden">
  <iframe class="w-full h-full" src="..." />
</div>

<!-- Galería de imágenes con grid responsive -->
<div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-1">
  <img class="w-full aspect-square object-cover" ... />
</div>
```

Note:
Imágenes: `srcset` + `sizes` para servir diferentes resoluciones según dispositivo. `loading="lazy"` para carga diferida. `object-cover` mantiene proporción. `aspect-*` garantiza ratio consistente. Galería: grid responsive con `gap-1` para mosaico compacto. SVG para iconos (vectoriales, escalan a cualquier resolución).

---

## 🏋️ Actividad en Clase

**Dashboard Completamente Responsive**

| ⏱️ Tiempo | 🎯 Objetivo | 📦 Entregable |
|-----------|-------------|---------------|
| 90 min | Dashboard funcional en 3 rangos (móvil, tablet, desktop) | Proyecto Angular con layout responsive |

**Requisitos**:
1. Sidebar con 3 comportamientos: drawer (móvil) → colapsada (tablet) → expandida (desktop)
2. Navbar superior fija con hamburguesa visible solo en móvil
3. Barra de navegación inferior en móvil con 4 iconos
4. Grid de widgets: 1 col → 2 cols → 3 cols → 4 cols
5. Tabla con scroll horizontal y columnas responsive
6. Modal fullscreen en móvil, centrado en desktop

Note:
90 minutos, objetivo ambicioso. Empezad por el layout base móvil y añadid las variantes responsive progresivamente. El orden correcto: 1) HTML base móvil, 2) Sidebar drawer, 3) Añadir variantes md: y lg: para tablet/desktop, 4) Barra inferior móvil, 5) Integrar BreakpointObserver para la lógica de la sidebar. Probad redimensionando el navegador.

---

## ✅ Buenas Prácticas

1. **Empieza siempre por el diseño móvil**: clases base = móvil, prefijos = hacia arriba
2. **Usa `min-width` implícito** (variantes Tailwind), no `max-width` (desktop-first)
3. **Prueba en dispositivos reales**: el viewport redimensionado no simula touch ni teclado virtual
4. **No ocultes contenido crítico** en móvil: prioriza, no elimines
5. **Tailwind para lo visual, BreakpointObserver para lógica** de negocio
6. **Tamaño táctil mínimo 44x44px** en móvil: `p-3` en botones lo garantiza

Note:
La práctica 3 es crucial: el modo responsive de DevTools no es un dispositivo real. El teclado virtual ocupa el 40% de la pantalla en móvil. Los eventos touch son distintos a click. Probad en un teléfono real con USB debugging. La práctica 5: separación de responsabilidades. Si solo cambia la apariencia → Tailwind. Si cambia el comportamiento → BreakpointObserver.

---

## ❌ Errores Frecuentes

| Error | Por qué | Solución |
|-------|---------|----------|
| **Desktop-first** | Diseñar en 1920px y luego "adaptar" | Empezar en 375px y enriquecer |
| **Breakpoints por dispositivo** | Asumir `md` = iPad vertical | Diseñar para rango de espacio |
| **Scroll horizontal global** | Un elemento desborda y arruina todo | `overflow-x-hidden` en body, local en tablas |
| **Ignorar landscape** | Solo probar en portrait | La app debe funcionar en ambas orientaciones |
| **`display: none` sin accesibilidad** | Contenido oculto no disponible para lectores | `sr-only` si es importante |
| **No probar con teclado virtual** | Campos del final inaccesibles | Test con teclado abierto, usar `visualViewport` |

Note:
El error 1 es el más común en alumnos: vienen de diseñar en Figma a 1440px. Mobile-first requiere un cambio mental. El error 3 es sutil: una tabla con overflow-x-auto es correcto, pero si alguien pone un elemento con width fijo de 1200px, toda la página tendrá scroll horizontal. Verificad que `html, body` no tengan scroll horizontal.

---

## 🎯 BreakpointObserver: Cuándo Usarlo

| Caso | Herramienta | Por qué |
|------|------------|---------|
| Mostrar/ocultar sidebar | Tailwind `md:hidden lg:block` | Es puramente visual |
| Cambiar grid de 1 a 4 cols | Tailwind `grid-cols-1 xl:grid-cols-4` | Es layout CSS |
| Inicializar drag-and-drop | <mark>BreakpointObserver</mark> | No tiene sentido en móvil táctil |
| Cargar datos completos vs resumidos | <mark>BreakpointObserver</mark> | Optimizar rendimiento en móvil |
| Cambiar patrón de navegación | <mark>BreakpointObserver</mark> | Push en móvil, tabs en desktop |
| Ajustar tamaño de fuente | Tailwind `text-sm lg:text-base` | Es puramente visual |

<mark>Regla: Tailwind para presentación, BreakpointObserver para comportamiento</mark>

Note:
La regla de oro evita la tentación de usar BreakpointObserver para todo. Si solo cambia el CSS, Tailwind es más simple y más rápido (no pasa por el ciclo de detección de cambios de Angular). Si cambia la lógica de negocio (qué datos cargar, qué funcionalidad inicializar), BreakpointObserver es la herramienta correcta.

---

## 🖥️ Electron: Configuración de Ventana

```javascript
// main.js - Proceso principal de Electron
const { app, BrowserWindow } = require('electron');

function createWindow() {
  const win = new BrowserWindow({
    width: 1280,
    height: 800,
    minWidth: 800,
    minHeight: 600,
    webPreferences: {
      nodeIntegration: false,
      contextIsolation: true,
      preload: path.join(__dirname, 'preload.js')
    }
  });

  // Cargar app Angular (desarrollo o producción)
  if (process.env.NODE_ENV === 'development') {
    win.loadURL('http://localhost:4200');
  } else {
    win.loadFile(path.join(__dirname, 'dist/index.html'));
  }
}

app.whenReady().then(createWindow);
```

<mark>La app Angular es exactamente la misma en navegador y en Electron</mark>

Note:
Electron envuelve tu app Angular en una ventana nativa con Chromium. La misma base de código responsive (Tailwind + BreakpointObserver) funciona en ambos entornos. `minWidth: 800` evita que el usuario haga la ventana demasiado pequeña. En desarrollo, carga desde `localhost:4200` con HMR. En producción, carga los archivos compilados.

---

## 🎯 Rendimiento Responsive en Móvil

| Técnica | Problema que resuelve | Implementación |
|---------|----------------------|-----------------|
| **Virtual scrolling** | 10.000 filas en móvil colapsan el navegador | `@angular/cdk/scrolling` |
| **Carga condicional** | No cargar módulos de desktop en móvil | `@if (isDesktop())` con lazy loading |
| **Imágenes responsive** | No servir imagen 4K a un móvil | `srcset` + `sizes` |
| **Fontes del sistema** | No descargar webfonts en conexiones lentas | `font-family: system-ui` |
| **CSS crítico inline** | Primer render rápido en 3G | Tailwind JIT ya genera CSS mínimo |

```typescript
// Virtual scrolling para tablas grandes
// En móvil cargar solo 20 items, en desktop 50
readonly pageSize = computed(() => this.layout.isMobile() ? 20 : 50);
```

Note:
El responsive no es solo visual: es también de rendimiento. Un móvil con 4G tiene menos CPU y memoria que un desktop. Virtual scrolling (CDK) solo renderiza las filas visibles. Carga condicional: no inicialices el editor drag-and-drop en móvil. Tailwind JIT ya genera CSS mínimo (~15KB), pero cuida las imágenes y fuentes.

---

## 📊 Resumen

| Tema | Clave |
|------|-------|
| **Mobile First** | Diseñar desde la restricción mejora todos los tamaños |
| **Breakpoints** | `sm:640`, `md:768`, `lg:1024`, `xl:1280`, `2xl:1536`. Mobile-first |
| **Sidebar** | Drawer overlay → colapsada w-16 → expandida w-64 |
| **Tablas** | Scroll horizontal + ocultación progresiva de columnas |
| **Grid** | `grid-cols-1 sm:2 lg:3 xl:4` en una línea |
| **BreakpointObserver** | Tailwind para visual, `toSignal(bo.observe(...))` para lógica |
| **Electron** | Mismo código responsive, definir `minWidth`/`minHeight` |

Note:
Hemos cubierto el responsive design desde los fundamentos (Mobile First) hasta la implementación concreta (Tailwind + BreakpointObserver). La clave: un solo código base que se adapta fluidamente a cualquier tamaño de pantalla. Sin media queries explícitas. Sin archivos CSS por dispositivo. Sin componentes duplicados para móvil/desktop.

---

## 🚀 Próximos Pasos

**Unidad 9: Ecosistema Frontend**

- Entorno profesional: Node.js, npm, Angular CLI, ESLint y Prettier
- TypeScript con tipado estático (interfaces, genéricos) en componentes Angular
- Tailwind CSS 4 (utility-first + `@theme`) y Figma para el handoff diseño-desarrollo
- Storybook para documentar componentes y Git/GitHub para control de versiones

**Para profundizar en responsive**:
- Instalar Responsively App (ver la app en 5 viewports a la vez)
- Implementar virtual scrolling (CDK) para tablas con 10.000+ registros en móvil
- Explorar Container Queries (más allá de media queries de viewport)

Note:
Con esta unidad cerramos el bloque de desarrollo de interfaces. Ahora tenéis las herramientas para construir aplicaciones Angular profesionales: UX (U1), accesibilidad (U2), Tailwind (U6), componentes (U7) y responsive (U8). El proyecto integrador del módulo pondrá todo esto en práctica. Pregunta final: ¿qué parte del desarrollo de interfaces os gustaría profundizar más?
