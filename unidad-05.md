# Desarrollo de Interfaces

## Unidad 5 · Layouts Modernos para Interfaces Web

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad de layouts. Aquí es donde el diseño se convierte en código. Vamos a dominar Flexbox, CSS Grid y posicionamiento, todo a través de Tailwind CSS. Construiréis dashboards, layouts SaaS, ecommerce y aplicaciones de chat. Esta unidad es 100% práctica. Pregunta: ¿quién ha usado Flexbox antes? ¿Y CSS Grid?

---

## Objetivos de aprendizaje

<span class="fragment">1. Construir layouts complejos con <mark>Flexbox y CSS Grid</mark> usando Tailwind</span>

<span class="fragment">2. Traducir layouts de escritorio clásicos (<mark>VBox, HBox, GridPane</mark>) a Tailwind</span>

<span class="fragment">3. Diseñar <mark>dashboards, paneles SaaS, ecommerce y chat</mark> combinando Grid + Flexbox</span>

<span class="fragment">4. Aplicar <mark>posicionamiento</mark> (relative, absolute, fixed, sticky)</span>

<span class="fragment">5. Implementar layouts <mark>responsivos mobile-first</mark> con breakpoints de Tailwind</span>

<span class="fragment">6. Utilizar técnicas avanzadas: <mark>container queries, auto-fit, clamp</mark></span>

Note:
Seis objetivos. Los 4 primeros son fundamentales (los necesitaréis siempre). Los 2 últimos son avanzados (marcan la diferencia entre un desarrollador junior y uno senior). Al final de esta unidad seréis capaces de implementar cualquier diseño de Figma como layout funcional.

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

¿Sabías que el 80% de los bugs visuales en frontend son <mark>problemas de layout</mark>?

</div>

<span class="fragment" style="font-size: 1.1rem;">Desbordamientos, elementos desalineados, scrolls inesperados...</span>

<span class="fragment" style="font-size: 1.1rem;"><mark>Flexbox + Grid</mark> resuelven el 95% de estos problemas de forma elegante</span>

<span class="fragment" style="font-size: 1.1rem;">Dominar el layout es lo que separa a un maquetador de un <mark>desarrollador de interfaces profesional</mark></span>

Note:
Los problemas de layout son los más frustrantes porque son visualmente obvios pero a menudo difíciles de depurar. Un padding incorrecto, un flex item que no se encoge, un grid que no suma 12 columnas... Esta unidad os da las herramientas para resolverlos sistemáticamente. Pregunta: ¿alguna vez habéis pasado horas intentando centrar un div?

---

## Sistemas de layout: panorama general

<div class="mermaid">
graph TB
  A[Necesito un layout] --> B{¿Bidimensional?}
  B -->|Sí: filas Y columnas| C[CSS Grid]
  B -->|No: una dirección| D[Flexbox]
  D --> E{¿Superposición?}
  E -->|Sí| F[Position: absolute/fixed]
  E -->|No| G[Flexbox puro]
  C --> H{¿Necesito salir del flujo?}
  H -->|Sí| F
  H -->|No| I[Grid puro]
</div>

Note:
Este diagrama es vuestra guía de decisión. Regla de oro: Grid para la estructura macro (página), Flexbox para los componentes micro (internos). Si hay superposición, posicionamiento. Pregunta: ¿en qué categoría cae un menú desplegable? (Posicionamiento: relative en el padre, absolute en el menú)

---

## Flexbox: conceptos fundamentales

<div class="mermaid">
graph LR
  subgraph "flex flex-row (default)"
    A1["← main axis →"] --> A2["justify-content"]
    A3["↕ cross axis ↕"] --> A4["align-items"]
  end
</div>

<span class="fragment">`justify-*` siempre actúa sobre el <mark>eje principal</mark></span>
<span class="fragment">`items-*` siempre actúa sobre el <mark>eje perpendicular</mark></span>
<span class="fragment">Invertir `flex-col` → <mark>invierte qué hace cada propiedad</mark></span>

Note:
Este es el concepto MÁS importante de Flexbox y el que más errores causa. En flex-row: justify controla horizontal, items controla vertical. En flex-col: SE INVIERTE. justify controla vertical, items controla horizontal. Memorizad esto. Pregunta: en `flex flex-col justify-center items-center`, ¿dónde se centra el contenido? (Verticalmente con justify, horizontalmente con items)

---

## Flexbox: propiedades del contenedor

```html
<!-- Dirección y wrap -->
<div class="flex flex-row flex-wrap gap-4">

<!-- Alineación en eje principal (justify) -->
<div class="flex justify-between">  <!-- Espacio entre items -->
<div class="flex justify-center">   <!-- Centrados -->

<!-- Alineación en eje perpendicular (items) -->
<div class="flex items-center">     <!-- ¡Centrado vertical! -->
<div class="flex items-baseline">   <!-- Alinear texto -->

<!-- Gap: espacio entre hijos (no márgenes) -->
<div class="flex gap-4 gap-x-6 gap-y-2">
```

<span class="fragment"><mark>`gap`</mark> es una de las mejores adiciones a CSS. Antes: márgenes y hacks.</span>

Note:
`gap` resolvió el problema histórico de espaciar elementos flex: antes tenías que poner márgenes y quitar el margen del primer/último elemento. Ahora es una propiedad del contenedor. `items-center` es el "santo grial": centrar verticalmente, imposible sin hacks hasta Flexbox. Pregunta: ¿diferencia entre justify-between y justify-around? (Between: sin espacio en extremos. Around: mitad de espacio en extremos)

---

## Flexbox: propiedades de los items

| Clase | CSS | Comportamiento |
|---|---|---|
| `flex-1` | `flex: 1 1 0%` | Crece, encoge, base 0 |
| `flex-auto` | `flex: 1 1 auto` | Crece, encoge, base auto |
| `flex-initial` | `flex: 0 1 auto` | No crece, puede encogerse |
| `flex-none` | `flex: none` | No crece ni encoge |
| `flex-shrink-0` | `flex-shrink: 0` | No se encoge nunca |
| `grow` / `grow-0` | `flex-grow: 1/0` | Solo controla grow |

<span class="fragment">`flex-1` = "ocupa el resto del espacio disponible"</span>

Note:
`flex-1` es la clase más usada en layouts. `flex-shrink-0` es crítica para sidebars y elementos que no deben encogerse. `flex-none` para elementos de tamaño fijo que no deben deformarse. Pregunta: ¿qué diferencia práctica hay entre flex-1 y flex-auto? (flex-1 reparte el espacio ignorando el tamaño del contenido; flex-auto considera el contenido para el reparto)

---

## Layout: Sidebar + Contenido

```html
<div class="flex h-screen bg-gray-50">
  <!-- Sidebar fijo -->
  <aside class="w-64 flex-shrink-0 bg-gray-900 text-white flex flex-col">
    <div class="p-4 text-xl font-bold border-b">Mi App</div>
    <nav class="flex-1 overflow-y-auto p-4">
      <!-- Enlaces de navegación -->
    </nav>
  </aside>
  <!-- Contenido flexible -->
  <main class="flex-1 flex flex-col min-w-0">
    <header class="flex items-center justify-between px-6 py-4 bg-white border-b">
      <h1 class="text-xl font-semibold">Dashboard</h1>
    </header>
    <div class="flex-1 overflow-y-auto p-6">
      <!-- Contenido scrolleable -->
    </div>
  </main>
</div>
```

Note:
Este es el layout más común en aplicaciones web. Elementos clave: `h-screen` (altura = viewport), `flex-shrink-0` (sidebar nunca se encoge), `flex-1` (contenido ocupa el resto), `min-w-0` (CRÍTICO: permite que el contenido se encoja y el overflow funcione), `overflow-y-auto` (scroll independiente). Pregunta: ¿qué pasa si olvidáis `min-w-0`? (El contenido no se encoge, causa desbordamiento horizontal)

---

## Técnica de auto-márgenes

```html
<div class="flex gap-4">
  <span>Logo</span>
  <span>Home</span>
  <span>Products</span>
  <!-- ml-auto empuja este elemento a la derecha -->
  <span class="ml-auto">Login</span>
</div>
```

<span class="fragment">Los márgenes `auto` en el eje principal <mark>consumen todo el espacio disponible</mark></span>

<span class="fragment">Alternativa a `justify-between` cuando solo quieres empujar <mark>un elemento</mark></span>

Note:
Esta técnica es menos conocida pero muy elegante. `ml-auto` en un flex item lo empuja a la derecha sin afectar al resto de elementos. Es preferible a `justify-between` cuando no quieres separar TODOS los elementos, solo el último. Pregunta: ¿cómo empujarías un elemento al fondo en una columna flex? (`mt-auto`)

---

## CSS Grid: conceptos fundamentales

<span class="fragment">Sistema de layout <mark>bidimensional</mark>: controlas filas Y columnas simultáneamente</span>

<span class="fragment">`display: grid` en el contenedor → hijos directos son <mark>grid items</mark></span>

<span class="fragment">Columnas con `grid-template-columns` (tamaños fijos, `fr`, `repeat()`, `minmax()`)</span>

<span class="fragment">La unidad <mark>`fr`</mark> (fracción): distribuye el espacio disponible proporcionalmente</span>

<span class="fragment">Items pueden posicionarse en celdas específicas con `grid-column` y `grid-row`</span>

Note:
CSS Grid (2017) fue un salto generacional. Antes, hacer un layout de 3 columnas con sidebar requería floats, clearfixes o frameworks CSS completos como Bootstrap. Ahora es una línea de CSS. La unidad `fr` es mágica: `1fr 2fr` significa "una parte para el primer track, dos para el segundo". Pregunta: ¿qué hace `grid-cols-12` en Tailwind? (Define 12 columnas de igual tamaño)

---

## Grid en Tailwind: definición de columnas

```html
<!-- 3 columnas iguales -->
<div class="grid grid-cols-3 gap-4">

<!-- 12 columnas (sistema clásico) -->
<div class="grid grid-cols-12 gap-4">
  <div class="col-span-8">Main (8/12)</div>
  <div class="col-span-4">Sidebar (4/12)</div>
</div>

<!-- Columnas de diferente tamaño (valores arbitrarios) -->
<div class="grid grid-cols-[250px_1fr_200px] gap-4">
  <div>Sidebar (250px fijo)</div>
  <div>Contenido (1fr = resto)</div>
  <div>Panel lateral (200px fijo)</div>
</div>
```

<span class="fragment">`grid-cols-[250px_1fr_200px]` es una de las features <mark>más potentes</mark> de Tailwind</span>

Note:
La sintaxis de valores arbitrarios permite definir tracks con unidades mixtas. 250px fijos para sidebar, 1fr para contenido (resto del espacio), 200px fijos para panel. Esto es puro CSS Grid expresado como clase Tailwind. Pregunta: ¿cuántas columnas por defecto tiene `grid-cols-3`? (3 columnas de igual tamaño = 1fr cada una)

---

## Grid: posicionamiento de items

```html
<div class="grid grid-cols-4 gap-4">
  <div class="col-span-2">Ocupa 2 columnas</div>
  <div>Col 3</div>
  <div>Col 4</div>

  <div class="col-start-2 col-span-3">
    Empieza en col 2, ocupa 3
  </div>

  <div class="col-span-full">Full width</div>
  <div class="row-span-2">2 filas de alto</div>
</div>
```

<span class="fragment">`col-span-{1-12}`, `row-span-{1-6}`, `col-span-full`, `row-span-full`</span>

Note:
Los items pueden colocarse en posiciones específicas del grid. `col-span-full` es muy útil para elementos que deben ocupar todo el ancho. `col-start-{n}` y `col-end-{n}` permiten control preciso. Pregunta: si tengo `grid-cols-4` y pongo `col-span-2` + `col-span-3`, ¿qué pasa? (La suma es 5 > 4, el segundo elemento salta a la siguiente fila)

---

## Dashboard con Grid: estructura

<div class="mermaid">
graph TB
  subgraph "grid grid-cols-12 gap-6"
    A["col-span-3<br>KPI 1"] 
    B["col-span-3<br>KPI 2"]
    C["col-span-3<br>KPI 3"]
    D["col-span-3<br>KPI 4"]
    E["col-span-8<br>Main Chart"]
    F["col-span-4<br>Activity"]
    G["col-span-full<br>Data Table"]
  end
</div>

<span class="fragment">Grid maneja la estructura bidimensional; Flexbox maneja los layouts internos</span>

Note:
Este es el patrón de dashboard más común. 12 columnas de grid. KPI cards: 3 columnas cada una (suman 12). Chart principal: 8 columnas (2/3 del ancho). Panel de actividad: 4 columnas (1/3). Tabla: full width. En móvil, todo col-span-full. Pregunta: ¿por qué no usar Flexbox para esto? (Con Flexbox, las tarjetas de diferentes alturas no se alinearían en filas perfectas)

---

## Dashboard responsive: mobile-first

```html
<div class="grid grid-cols-12 gap-6 p-6">
  <!-- KPI Cards: 1 col móvil, 2 col tablet, 4 col desktop -->
  <div class="col-span-full sm:col-span-6 lg:col-span-3">
    <div class="bg-white rounded-xl p-6 shadow-sm">
      <p class="text-sm text-gray-500">Total Users</p>
      <p class="text-3xl font-bold mt-1">24,521</p>
      <p class="text-sm text-green-600 mt-2">↑ 12.5%</p>
    </div>
  </div>
  <!-- ... más KPI cards ... -->

  <!-- Chart: full móvil, 8/12 desktop -->
  <div class="col-span-full lg:col-span-8 bg-white rounded-xl p-6">
    <h2 class="text-lg font-semibold mb-4">Revenue</h2>
  </div>

  <!-- Activity: full móvil, 4/12 desktop -->
  <div class="col-span-full lg:col-span-4 bg-white rounded-xl p-6">
    <h2 class="text-lg font-semibold mb-4">Activity</h2>
  </div>
</div>
```

Note:
Mobile-first: las clases base (`col-span-full`) definen el layout móvil. Los prefijos `sm:` (≥640px), `lg:` (≥1024px) añaden modificaciones para pantallas más grandes. Esto es más mantenible que desktop-first. Pregunta: ¿qué prefijo usaríais para tablet en Tailwind? (md: ≥768px)

---

## Layout Ecommerce: Grid de productos

```html
<!-- Catálogo responsivo sin media queries explícitas -->
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  <!-- Product Card -->
  <div class="group bg-white rounded-xl border hover:shadow-lg transition-shadow">
    <div class="aspect-square bg-gray-100 relative overflow-hidden">
      <img src="product.jpg" class="w-full h-full object-cover
                  group-hover:scale-105 transition-transform" />
    </div>
    <div class="p-4">
      <p class="text-sm text-gray-500">Brand</p>
      <h3 class="font-medium truncate">Product Name</h3>
      <div class="flex items-center gap-2">
        <span class="font-bold">€29.99</span>
        <span class="text-sm text-gray-400 line-through">€49.99</span>
      </div>
    </div>
  </div>
</div>
```

Note:
El catálogo usa Grid para las tarjetas (columnas perfectamente alineadas). `aspect-square` mantiene la imagen cuadrada. `group-hover:scale-105` escala la imagen al hacer hover en la tarjeta (gracias a `group` en el padre). `truncate` evita que nombres largos desborden. Pregunta: ¿por qué Grid y no Flexbox wrap para las tarjetas? (Grid garantiza que todas las tarjetas tengan exactamente el mismo ancho alineado en columnas)

---

## Posicionamiento: valores de `position`

| Valor | Comportamiento | Tailwind |
|---|---|---|
| `static` | Flujo normal (por defecto) | `static` |
| `relative` | Relativo a su posición original. Crea contexto para hijos absolute | `relative` |
| `absolute` | Respecto al ancestro posicionado más cercano. Sale del flujo | `absolute` |
| `fixed` | Respecto al viewport. No se mueve al hacer scroll | `fixed` |
| `sticky` | Híbrido: relative hasta cruzar umbral, luego fixed | `sticky` |

<span class="fragment">`relative` en el padre + `absolute` en el hijo = <mark>patrón más común</mark></span>

Note:
Cada valor tiene su caso de uso. `static` es el default (casi nunca se usa explícitamente). `relative` + `absolute` es el dúo dinámico de los overlays. `fixed` para modales y headers persistentes. `sticky` para headers de sección que se pegan al hacer scroll. Pregunta: ¿qué pasa si un elemento absolute no tiene ancestro posicionado? (Se posiciona respecto al body/html)

---

## Patrones de posicionamiento

```html
<!-- Tooltip -->
<div class="relative inline-block">
  <button>Hover me</button>
  <div class="absolute bottom-full left-1/2 -translate-x-1/2 mb-2
              bg-gray-900 text-white text-sm rounded-lg px-3 py-2
              opacity-0 group-hover:opacity-100 transition-opacity">
    Tooltip text
  </div>
</div>

<!-- Modal centrado -->
<div class="fixed inset-0 z-50">
  <div class="absolute inset-0 bg-black/50 backdrop-blur-sm"></div>
  <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2
              w-full max-w-lg bg-white rounded-2xl shadow-2xl p-6">
    <h2 class="text-xl font-bold">Modal Title</h2>
  </div>
</div>
```

Note:
Dos patrones esenciales. Tooltip: `relative` en padre, `absolute` en hijo, `group-hover:` para mostrar/ocultar. Modal: `fixed inset-0` (cubre toda la pantalla), overlay con `bg-black/50 backdrop-blur-sm`, contenido centrado con `top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2`. Esta fórmula de centrado es la más fiable. Pregunta: ¿por qué `-translate-x-1/2`? (Porque top/left 50% posiciona la esquina superior izquierda en el centro; translate compensa la mitad del ancho/alto del propio elemento)

---

## Sticky: lo mejor de relative y fixed

```html
<!-- Header que se pega al hacer scroll -->
<header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b">
  <!-- Contenido -->
</header>

<!-- Sticky headers de sección en lista -->
<div class="sticky top-16 bg-gray-50 px-4 py-2 text-sm font-semibold z-10">
  Hoy
</div>
```

<span class="fragment">Requisitos para que sticky funcione:</span>
<span class="fragment">1. Necesita `top` (o bottom/left/right) definido</span>
<span class="fragment">2. El contenedor padre debe tener altura suficiente</span>
<span class="fragment">3. Ningún ancestro con `overflow: hidden`</span>

Note:
Sticky es genial cuando funciona, pero tiene reglas estrictas. Si "no funciona", comprobad estas 3 condiciones. El `top-16` en el segundo ejemplo es para que el header de sección se pegue justo debajo del header principal (que mide 64px = 4rem). Pregunta: ¿por qué sticky necesita que el padre tenga altura? (Porque sticky es "pegajoso" dentro de su contenedor; si el contenedor no tiene scroll, no hay nada contra lo que pegarse)

---

## Grid + Flexbox: la regla de oro

> **Grid para la estructura de página (macro-layout).**  
> **Flexbox para los componentes (micro-layout).**

<span class="fragment">

```html
<!-- Grid: estructura macro -->
<div class="grid grid-cols-[250px_1fr] h-screen">
  <!-- Flexbox: contenido del sidebar -->
  <aside class="flex flex-col bg-gray-900">...</aside>

  <!-- Flexbox: estructura del main -->
  <main class="flex flex-col min-w-0">
    <!-- Flexbox: header -->
    <header class="flex items-center justify-between px-6 py-4">...</header>
    <!-- Grid: widgets del dashboard -->
    <div class="flex-1 overflow-y-auto p-6">
      <div class="grid grid-cols-12 gap-6">...</div>
    </div>
  </main>
</div>
```

</span>

Note:
Esta regla resuelve el 95% de las decisiones de layout. Grid es bidimensional, Flexbox es unidimensional. El layout anida: Grid → Flexbox → Grid → Flexbox, según el nivel. En el ejemplo: Grid para la página, Flexbox para sidebar y main, Grid para los widgets del dashboard. Pregunta: ¿en qué casos usaríais Flexbox para la estructura macro? (Layouts muy simples de una sola dimensión, como un chat con 3 paneles horizontales)

---

## Container Queries: el futuro

```html
<!-- Contenedor con container query habilitado -->
<div class="@container">
  <!-- Se adapta al ancho del CONTENEDOR, no del viewport -->
  <div class="grid grid-cols-1 @sm:grid-cols-2 @lg:grid-cols-3 @xl:grid-cols-4 gap-4">
    <div class="bg-white rounded-xl p-4 shadow">Card 1</div>
    <div class="bg-white rounded-xl p-4 shadow">Card 2</div>
    <div class="bg-white rounded-xl p-4 shadow">Card 3</div>
    <div class="bg-white rounded-xl p-4 shadow">Card 4</div>
  </div>
</div>
```

<span class="fragment">Los prefijos <mark>@sm:, @md:, @lg:</mark> consultan el tamaño del contenedor, no del viewport</span>

Note:
Esto es revolucionario para componentes reutilizables. Con media queries, un componente no sabe si está en un sidebar estrecho o en un área ancha (ambos dependen del viewport). Con container queries, el componente pregunta a su contenedor inmediato. Tailwind 4 soporta esto nativamente. Pregunta: ¿cuándo usaríais container queries en lugar de media queries? (Cuando un mismo componente debe comportarse diferente según el contexto donde se use)

---

## Layout fluido sin media queries

```html
<!-- Grid que se adapta automáticamente SIN breakpoints -->
<div class="grid grid-cols-[repeat(auto-fit,minmax(280px,1fr))] gap-6">
  <!-- Crea tantas columnas como quepan de mínimo 280px -->
  <!-- Cada columna se estira para ocupar 1fr si sobra espacio -->
  <!-- Si no caben, saltan a la siguiente fila automáticamente -->
</div>

<!-- Ancho de lectura fluido con clamp() -->
<article class="w-[clamp(320px,80vw,1200px)] mx-auto">
  <!-- Mínimo 320px, ideal 80vw, máximo 1200px -->
</article>
```

<span class="fragment">CSS moderno permite layouts responsivos <mark>sin una sola media query</mark></span>

Note:
`auto-fit` + `minmax` es pura magia CSS. "Crea tantas columnas como quepan, cada una de mínimo 280px, y si sobra espacio, estíralas proporcionalmente". Sin breakpoints, sin media queries. `clamp()` es ideal para anchos de lectura: se adapta fluidamente entre un mínimo y un máximo. Pregunta: ¿qué diferencia hay entre auto-fit y auto-fill? (auto-fit colapsa columnas vacías; auto-fill las mantiene, creando espacio vacío)

---

## Overflows y scroll

```html
<!-- Layout de página con scroll en área de contenido -->
<div class="flex flex-col h-screen">
  <header class="flex-shrink-0 h-16">Header fijo</header>
  <main class="flex-1 overflow-y-auto">Contenido scrolleable</main>
  <footer class="flex-shrink-0 h-12">Footer fijo</footer>
</div>

<!-- Tabla ancha con scroll horizontal -->
<div class="overflow-x-auto">
  <table class="min-w-[800px]">...</table>
</div>

<!-- Prevenir desbordamiento de texto -->
<p class="truncate">Texto muy largo que se truncará...</p>
<p class="break-words">URL_muy_larga_sin_espacios_que_se_parte...</p>
```

Note:
El control del overflow es de lo más olvidado y lo más importante. Sin `h-screen` y `overflow-y-auto`, el contenido empuja el footer fuera de la pantalla. `truncate` es esencial para contenido generado por usuarios. `break-words` para URLs largas que no tienen espacios. Pregunta: ¿qué hace exactamente `truncate`? (overflow: hidden + text-overflow: ellipsis + white-space: nowrap)

---

## Layout SaaS: 3 columnas (tipo Notion/Linear)

<div class="mermaid">
graph LR
  subgraph "flex h-screen"
    A["w-12<br>Workspace nav<br>flex-shrink-0"]
    B["w-60<br>Sidebar<br>flex-shrink-0"]
    C["flex-1<br>Contenido<br>min-w-0"]
  end
  C --> C1["Header"]
  C --> C2["Área principal<br>overflow-y-auto"]
</div>

<span class="fragment">Tres paneles flex, cada uno con `flex-col` para su estructura interna</span>

Note:
Este layout de 3 columnas es el estándar de las aplicaciones SaaS modernas. La primera columna (48px) son iconos de workspace. La segunda (240px) es la navegación. La tercera es el contenido. Todas con `flex-shrink-0` excepto el contenido (`flex-1 min-w-0`). Pregunta: ¿cómo haríais esto responsive en móvil? (Ocultando columnas y mostrando solo una, con navegación hacia atrás)

---

## Layout Chat: 3 paneles (tipo Slack/Discord)

```html
<div class="flex h-screen bg-gray-200">
  <!-- Servidores: 60px -->
  <nav class="w-[60px] flex-shrink-0 bg-gray-900 flex flex-col items-center py-3 gap-2">
    <div class="w-10 h-10 bg-blue-500 rounded-2xl flex items-center justify-center text-white">D</div>
  </nav>

  <!-- Canales: 240px -->
  <aside class="w-60 flex-shrink-0 bg-gray-800 flex flex-col">
    <div class="px-4 py-3 border-b border-gray-700 font-semibold text-white text-sm">Servidor</div>
    <div class="flex-1 overflow-y-auto py-2">
      <a href="#" class="flex items-center gap-1.5 px-2 py-1 rounded text-gray-300 hover:bg-gray-700">
        <span>#</span> <span class="text-sm">general</span>
      </a>
    </div>
  </aside>

  <!-- Chat: flex-1 -->
  <main class="flex-1 flex flex-col min-w-0 bg-gray-100">
    <header class="flex-shrink-0 px-4 py-3 bg-white border-b"># canal</header>
    <div class="flex-1 overflow-y-auto px-4 py-4"><!-- Mensajes --></div>
    <div class="flex-shrink-0 px-4 py-3 bg-white border-t"><!-- Input --></div>
  </main>
</div>
```

Note:
Este es el layout de chat más completo. 3 paneles con Flexbox horizontal. Cada panel con Flexbox vertical. Áreas de scroll independientes (`overflow-y-auto`). Input de mensaje con `flex-shrink-0` para que nunca se encoja. Pregunta: ¿por qué el panel de chat usa flex-col en lugar de grid? (Es un layout unidimensional: header + mensajes + input en columna)

---

## Actividad en clase: Depuración de layouts

<span class="fragment">Se proporcionan 3 layouts con <mark>errores intencionados</mark></span>

<span class="fragment">1. Abrir DevTools → panel Elements + Styles</span>

<span class="fragment">2. Activar overlays de <mark>Flexbox y Grid</mark> en el panel Layout</span>

<span class="fragment">3. Identificar el error</span>

<span class="fragment">4. Corregir con las clases Tailwind adecuadas</span>

**Errores:** falta `min-w-0`, `flex-shrink-0` mal usado, `sticky` sin `top`, `absolute` sin `relative`, `col-span` que no suma 12

**Duración:** 25 minutos

Note:
Actividad práctica de debugging. Los overlays de Flexbox y Grid en Chrome DevTools muestran líneas de colores para ejes, gaps y áreas. Son la herramienta más infrautilizada y más valiosa para depurar layouts. Pregunta: ¿dónde se activan los overlays de Grid en Chrome? (DevTools → Panel Layout →チェック "Show grid overlay")

---

## Actividad en clase: Conversión Figma → Layout

<span class="fragment">Se proporcionan 3 <mark>diseños de Figma</mark> (con Dev Mode accesible)</span>

<span class="fragment">1. Analizar estructura: ¿dónde usa Grid? ¿dónde Flexbox? ¿hay posicionamiento?</span>

<span class="fragment">2. <mark>Traducir Auto Layout → Flexbox/Grid</mark> con la tabla de equivalencias</span>

<span class="fragment">3. Escribir HTML + Tailwind correspondiente</span>

<span class="fragment">4. Comparar implementación con el diseño</span>

**Duración:** 30 minutos

Note:
Esta actividad conecta directamente con las Unidades 3 y 5. Usáis la tabla de equivalencias Figma ↔ Tailwind para traducir diseños a código. Recordad: Auto Layout horizontal = flex-row, Auto Layout vertical = flex-col, gap = gap-{n}, padding = p-{n}. Pregunta: ¿cómo traduciríais un Auto Layout con space-between? (justify-between)

---

## Buenas prácticas

<span class="fragment">1. <mark>Mobile-first</mark>: clases base para móvil, prefijos `sm:`, `md:` para ampliar</span>

<span class="fragment">2. <mark>Grid para macro, Flexbox para micro</mark>: cubre el 95% de decisiones</span>

<span class="fragment">3. <mark>Nunca olvides `min-w-0`</mark> en flex items con overflow potencial</span>

<span class="fragment">4. <mark>Usa `gap` en lugar de márgenes</mark> para espaciar en flex y grid</span>

<span class="fragment">5. <mark>Escala de espaciado consistente</mark>: solo valores de la escala Tailwind (múltiplos de 4px)</span>

<span class="fragment">6. <mark>Semántica HTML</mark>: `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`</span>

Note:
Seis prácticas que os ahorrarán horas de debugging. La #3 (`min-w-0`) es la más olvidada y la que más dolores de cabeza causa. La #6 no es solo "buena práctica": los elementos semánticos proporcionan landmarks ARIA implícitos para lectores de pantalla. Pregunta: ¿por qué no usar valores arbitrarios para espaciado? (Rompen la consistencia visual; si necesitas un valor 3 veces, defínelo como token en @theme)

---

## Errores frecuentes

<span class="fragment">1. Olvidar <mark>`flex-col`</mark> — por defecto `flex` es `flex-row`</span>

<span class="fragment">2. `absolute` sin <mark>`relative`</mark> en el ancestro — se posiciona respecto al body</span>

<span class="fragment">3. `col-span-{n}` que no suma el número de columnas → <mark>desbordamiento</mark></span>

<span class="fragment">4. No manejar el <mark>overflow</mark>: contenido que se sale del viewport</span>

<span class="fragment">5. Usar <mark>`sticky`</mark> sin `top` o con ancestro `overflow: hidden`</span>

<span class="fragment">6. Abusar de <mark>valores arbitrarios</mark>: `w-[327px]`, `p-[7px]`</span>

Note:
El error #1 es el más común entre principiantes: `flex` solo (sin `flex-col`) pone los elementos en fila cuando querías columna. El #3 es tramposo: si tienes grid-cols-4 y pones col-span-3 + col-span-2, la suma es 5 y el segundo salta a la siguiente fila. Pregunta: ¿cómo depuráis un sticky que "no funciona"? (Comprobad: ¿tiene top definido? ¿algún ancestro tiene overflow:hidden? ¿el padre tiene altura suficiente?)

---

## Resumen de la unidad

<span class="fragment">✅ <mark>Flexbox</mark>: layout unidimensional, ejes main/cross, gap, auto-márgenes</span>

<span class="fragment">✅ <mark>CSS Grid</mark>: layout bidimensional, fr, repeat, minmax, span, start/end</span>

<span class="fragment">✅ <mark>Posicionamiento</mark>: relative/absolute (overlays), fixed (modales), sticky (headers)</span>

<span class="fragment">✅ <mark>Regla de oro</mark>: Grid para estructura, Flexbox para componentes</span>

<span class="fragment">✅ <mark>Técnicas avanzadas</mark>: container queries, auto-fit, clamp, overflow control</span>

<span class="fragment">✅ <mark>4 layouts completos</mark>: Dashboard, SaaS, Ecommerce, Chat</span>

Note:
Resumen de las 6 competencias. Ahora tenéis las herramientas para implementar cualquier diseño de Figma como layout funcional. En la Unidad 5 usaremos estos layouts como base para construir aplicaciones completas con componentes Angular. Pregunta: ¿cuál de los 4 layouts os ha parecido más útil?

---

## Próximos pasos

<span class="fragment">📌 <mark>Actividad 1</mark>: Practicar Flexbox con layouts unidimensionales (navbar, lista, footer)</span>

<span class="fragment">📌 <mark>Actividad 2</mark>: Construir Dashboard con Grid de 12 columnas y componentes Angular</span>

<span class="fragment">📌 <mark>Actividad 3</mark>: Posicionamiento avanzado (tooltip, dropdown, modal, sticky, toast)</span>

<span class="fragment">📌 <mark>Actividad 4</mark>: App de chat completa con 3 paneles y Signals</span>

<span class="fragment">📌 <mark>Unidad 5</mark>: Del diseño Figma a la implementación Angular completa</span>

Note:
Cuatro actividades obligatorias. La Actividad 4 (chat completo) es la más completa: integra layouts, componentes y Signals. La Unidad 5 es la culminación del módulo: tomaremos diseños de Figma y los implementaremos completamente con todo lo aprendido.

---

## ¿Preguntas?

<div style="font-size: 1.5rem; margin-top: 2rem;">

`flex flex-col` · `grid grid-cols-12` · `relative` + `absolute` · `min-w-0`

<br>

**Próxima sesión:** Unidad 5 · Del Diseño a la Implementación

</div>

Note:
Espacio para dudas. Recordad: min-w-0, gap en lugar de márgenes, mobile-first, Grid para macro y Flexbox para micro. Con estas 4 reglas resolveréis el 95% de los problemas de layout.

---

## Referencias

- **Tailwind Flexbox:** https://tailwindcss.com/docs/flex
- **Tailwind Grid:** https://tailwindcss.com/docs/grid-template-columns
- **Tailwind Position:** https://tailwindcss.com/docs/position
- **CSS-Tricks Flexbox Guide:** https://css-tricks.com/snippets/css/a-guide-to-flexbox
- **CSS-Tricks Grid Guide:** https://css-tricks.com/snippets/css/complete-guide-grid
- **Flexbox Froggy:** https://flexboxfroggy.com
- **Grid Garden:** https://cssgridgarden.com
