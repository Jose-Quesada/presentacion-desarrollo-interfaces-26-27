
<!-- .slide: data-background="#0f172a" -->
## Módulo 0488 · Desarrollo de Interfaces
### Unidad 13: Tailwind CSS 4 en Desarrollo de Interfaces

Filosofía Utility-First · @theme · Vite · Clases Clave · Técnicas Avanzadas

<small>CFGS DAM · Curso 2025/26</small>

Note:
Tailwind CSS 4 es un salto cualitativo. Abandona el archivo JS de configuración y adopta CSS nativo con @theme. Vamos a ver por qué el debate "CSS vs Tailwind" está mal planteado: Tailwind ES CSS. Cada clase como `flex` o `p-4` compila directamente a propiedades CSS. No hay magia, no hay runtime. Pregunta: ¿quién ha usado Tailwind antes? ¿CSS tradicional?

---

<!-- .slide: data-background="#f8fafc" -->
## 🎯 Objetivos de Aprendizaje

1. Comprender la filosofía <mark>utility-first</mark> y cuándo aporta ventajas reales
2. Configurar Tailwind 4 en Angular con Vite y `@theme`
3. Organizar CSS en proyectos Angular: globales, plantillas, `@apply` moderado
4. Dominar clases clave: layout, espaciado, colores, tipografía, estados, responsive
5. Aplicar técnicas avanzadas: valores arbitrarios, `peer`, `group`, `has-*`
6. Utilizar <mark>Tailwind CSS IntelliSense</mark> para productividad

Note:
6 objetivos progresivos: del "por qué" al "cómo hacerlo bien". El objetivo 3 es crítico: mucha gente abusa de @apply y recrea los problemas del CSS tradicional dentro de Tailwind. La regla: Tailwind en HTML para todo, @apply como excepción muy justificada.

---

<!-- .slide: data-background="#f8fafc" -->
## 🤔 Motivación: CSS Tradicional vs Tailwind

<div class="mermaid">
graph TD
    CSS[CSS Tradicional en equipos] --> P1[Guerras de naming<br/>.btn-primary vs .button-main]
    CSS --> P2[Especificidad descontrolada<br/>.main .card .header .title]
    CSS --> P3[CSS muerto<br/>clases que nadie se atreve a borrar]
    CSS --> P4[Inconsistencia<br/>padding: 15px vs 16px]
    TW[Tailwind] --> S1[Sin nombres: clases en HTML]
    TW --> S2[Especificidad plana: 0-1-0]
    TW --> S3[JIT genera solo CSS usado]
    TW --> S4[Escala predefinida: p-4 = 16px siempre]
</div>

Note:
Estos 4 problemas son reales en equipos de 5+ personas. Tailwind los resuelve de raíz eliminando la capa de nombres. El JIT (Just-In-Time) escanea tus plantillas y solo genera el CSS que usas. En un proyecto Angular típico, el CSS generado rara vez supera los 15-20 KB comprimidos. El sistema de diseño viene embebido: no necesitas acordar manualmente qué tono de azul usar.

---

## 🔑 Premisa Fundamental

<mark>Tailwind NO reemplaza saber CSS. Todo lo contrario.</mark>

| Clase Tailwind | CSS generado |
|----------------|-------------|
| `flex` | `display: flex` |
| `items-center` | `align-items: center` |
| `justify-between` | `justify-content: space-between` |
| `gap-4` | `gap: 1rem` |
| `p-4` | `padding: 1rem` |
| `text-lg` | `font-size: 1.125rem; line-height: 1.75rem` |

<mark>Si no sabes qué hace `justify-between`, no sabrás usar Tailwind</mark>

Note:
Esta transparencia es la clave. Cada clase de Tailwind es una traducción directa de CSS. No hay abstracción mágica. Si no entiendes flexbox, no entenderás por qué `items-center` no funciona en un contenedor sin `flex`. Estudia CSS primero, Tailwind después. Tailwind acelera a quien ya sabe CSS; confunde a quien no.

---

## 📦 Instalación de Tailwind 4 en Angular

```bash
# Paso 1: Instalar paquetes
npm install tailwindcss @tailwindcss/vite

# Paso 2: Configurar vite.config.ts
```
```typescript
import tailwindcss from '@tailwindcss/vite';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [tailwindcss()]
});
```

**Paso 3**: Añadir al inicio de `src/styles.css`:
```css
@import "tailwindcss";
```

<mark>Tailwind 4: sin tailwind.config.js, sin content scanning manual</mark>

Note:
3 pasos y Tailwind está funcionando. En Tailwind 3 necesitabas un archivo JS de configuración con content paths. En v4, el plugin de Vite detecta automáticamente todos los archivos que procesa (plantillas Angular, TypeScript). El `@import "tailwindcss"` reemplaza las antiguas directivas `@tailwind base/components/utilities`. Más simple, más estándar.

---

## 🎨 Configuración @theme con Colores oklch

```css
@import "tailwindcss";

@theme {
  --color-primary-50: oklch(0.97 0.01 255);
  --color-primary-100: oklch(0.93 0.03 255);
  --color-primary-300: oklch(0.75 0.11 255);
  --color-primary-500: oklch(0.62 0.19 255);
  --color-primary-700: oklch(0.47 0.17 255);
  --color-primary-900: oklch(0.28 0.10 255);

  --color-success-500: oklch(0.62 0.19 145);
  --color-danger-500: oklch(0.55 0.22 25);

  --font-family-sans: 'Inter', system-ui, sans-serif;
  --radius-button: 0.5rem;
  --radius-card: 0.75rem;
}
```

Note:
oklch es un espacio de color perceptualmente uniforme. Cambios iguales en sus coordenadas producen cambios visuales iguales. Esto facilita crear escalas de color: mismo tono (cuarto parámetro), varías la luminosidad (primer parámetro). Al definir `--color-primary-500`, automáticamente tienes `bg-primary-500`, `text-primary-500`, `border-primary-500`, etc. IntelliSense lo detecta y autocompleta.

---

## 🏗️ Organización del CSS en Angular + Tailwind

<div class="mermaid">
graph TD
    A[3 niveles de CSS] --> B["1. styles.css<br/>@import tailwind + @theme<br/>< 50 líneas"]
    A --> C["2. Clases en plantillas<br/>ESTÁNDAR<br/>class='flex gap-4 p-6...'"]
    A --> D["3. @apply en .component.css<br/>EXCEPCIÓN<br/>solo si mucha repetición"]
    D --> E{¿Repites las mismas<br/>8 clases en 20 td?}
    E -->|Sí| F[.td-base { @apply px-4 py-3... }]
    E -->|No| G[❌ No uses @apply<br/>extrae un componente]
</div>

Note:
Regla de los 3 niveles. El nivel 1 es mínimo: solo @import y @theme. El nivel 2 es donde vive el 95% de tu CSS: directamente en el HTML. El nivel 3 es la excepción, no la regla. Si necesitas @apply constantemente, probablemente necesitas crear un componente Angular, no luchar contra Tailwind. El antipatrón: usar @apply para recrear .btn-primary en un archivo global.

---

## 📐 Clases de Layout Esenciales

| Categoría | Clases | Uso |
|-----------|--------|-----|
| **Flex** | `flex`, `flex-col`, `flex-row`, `flex-wrap`, `flex-1` | Contenedor flexible |
| **Alineación** | `items-center`, `justify-between`, `justify-center` | Ejes de flex/grid |
| **Grid** | `grid`, `grid-cols-3`, `col-span-2`, `gap-6` | Cuadrícula |
| **Tamaño** | `w-full`, `h-screen`, `min-h-screen`, `max-w-lg` | Ancho y alto |
| **Posición** | `relative`, `absolute`, `fixed`, `sticky`, `inset-0` | Posicionamiento |
| **Centrado** | `mx-auto`, `container` | Centrar horizontalmente |

```html
<div class="flex items-center justify-between gap-4">
  <span>Logo</span>
  <nav class="hidden md:flex gap-2">...</nav>
</div>
```

Note:
Estas 6 categorías cubren el 90% del layout en aplicaciones de gestión. `flex items-center justify-between` es probablemente la combinación más usada: crea una barra horizontal con elementos espaciados. `min-h-screen` garantiza que el contenido ocupe al menos toda la ventana, esencial en layouts de dashboard.

---

## 🎨 Clases de Color

| Uso | Clase | Significado |
|-----|-------|-------------|
| Texto | `text-gray-900` | Texto principal oscuro |
| Texto secundario | `text-gray-500` | Texto menos importante |
| Fondo | `bg-white` | Fondo blanco |
| Fondo página | `bg-gray-50` | Fondo ligeramente gris |
| Borde | `border-gray-200` | Borde sutil |
| Primario | `bg-primary-500`, `text-primary-600` | Acción principal |

<mark>Paleta con 22 colores × 11 tonos (50-950) → 242 clases de color</mark>

```
bg-{color}-{tono}   text-{color}-{tono}   border-{color}-{tono}
ring-{color}-{tono}  accent-{color}-{tono}
```

Note:
Tailwind incluye una paleta científicamente diseñada: slate, gray, zinc, neutral, stone, red, orange, amber, yellow, lime, green, emerald, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose. Cada uno con 11 tonos. Tus colores personalizados en @theme siguen el mismo patrón. La clase `bg-primary-500` existe automáticamente si definiste `--color-primary-500`.

---

## 🔤 Clases de Tipografía

| Clase | Tamaño | Uso típico |
|-------|--------|------------|
| `text-xs` | 12px / 0.75rem | Badges, labels pequeñas |
| `text-sm` | 14px / 0.875rem | Texto de tabla, secundario |
| `text-base` | 16px / 1rem | Texto cuerpo por defecto |
| `text-lg` | 18px / 1.125rem | Subtítulos |
| `text-xl` | 20px / 1.25rem | Títulos de sección |
| `text-2xl` | 24px / 1.5rem | Títulos de página |
| `text-3xl` | 30px / 1.875rem | Valores destacados (dashboard) |
| `text-4xl` | 36px / 2.25rem | Valor principal KPI |

Peso: `font-normal`, `font-medium`, `font-semibold`, `font-bold`

Utilidades: `truncate`, `line-clamp-2`, `text-center`, `leading-relaxed`

Note:
La escala tipográfica de Tailwind es mobile-first y cubre todas las necesidades. `truncate` es esencial en celdas de tabla: aplica `overflow:hidden; text-overflow:ellipsis; white-space:nowrap` en una clase. `line-clamp-2` limita a 2 líneas con puntos suspensivos, ideal para previews de texto.

---

## 🎯 Estados Interactivos

```html
<!-- Botón completo con todos los estados -->
<button class="
  bg-primary-500 text-white px-4 py-2 rounded-lg font-medium
  hover:bg-primary-600
  active:bg-primary-700
  focus-visible:ring-2 focus-visible:ring-primary-500
  focus-visible:ring-offset-2 focus-visible:outline-none
  disabled:opacity-50 disabled:cursor-not-allowed
  transition-colors duration-200
">
  Guardar cambios
</button>
```

| Variante | Gatillo | Ejemplo |
|----------|---------|---------|
| `hover:` | Ratón encima | `hover:bg-primary-600` |
| `focus:` | Elemento enfocado | `focus:ring-2` |
| `active:` | Clic presionado | `active:scale-95` |
| `disabled:` | `[disabled]` | `disabled:opacity-50` |
| `focus-visible:` | Foco por teclado | `focus-visible:ring-2` |
| `dark:` | Modo oscuro | `dark:bg-gray-800` |

Note:
Las variantes se anteponen a cualquier clase. `focus-visible` es preferible a `focus` porque solo muestra el anillo cuando navegas con teclado, no al hacer clic. `transition-colors duration-200` suaviza los cambios de color. La variante `dark:` permite modo oscuro: defines `bg-white dark:bg-gray-900` y Tailwind genera ambas versiones.

---

## 📱 Clases Responsive

```html
<!-- Mobile-first: 1 col → 2 col (md) → 4 col (xl) -->
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 1</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 2</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 3</div>
  <div class="bg-white rounded-xl p-4 shadow-sm">Widget 4</div>
</div>
```

| Breakpoint | Ancho mínimo | Dispositivo típico |
|------------|-------------|-------------------|
| (base) | 0px | Móvil |
| `sm:` | 640px | Móvil grande / tablet pequeña |
| `md:` | 768px | Tablet |
| `lg:` | 1024px | Portátil / desktop |
| `xl:` | 1280px | Desktop estándar |
| `2xl:` | 1536px | Desktop grande |

Note:
Mobile-first significa que las clases sin prefijo son para móvil y los prefijos añaden estilos hacia arriba. `grid-cols-1 md:grid-cols-2` = 1 columna en móvil, 2 desde tablet. NUNCA escribas `lg:grid-prefix` seguido de clase sin prefijo que la anule. El orden correcto es: clase base primero, luego variantes responsive.

---

## ⚔️ Caso Práctico: Navbar CSS Tradicional vs Tailwind

<div style="display: flex; gap: 1rem; font-size: 0.5em;">

<div style="flex: 1;">

**CSS Tradicional** (~25 líneas)
```css
.navbar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 1rem;
  height: 4rem;
  background: white;
  border-bottom: 1px solid #e5e7eb;
}
.navbar-logo { font-weight: 700; font-size: 1.25rem; }
.navbar-links { display: none; gap: 0.5rem; }
.navbar-link {
  padding: 0.5rem 0.75rem;
  color: #4b5563;
  border-radius: 0.5rem;
}
.navbar-link:hover { background: #f3f4f6; }
@media (min-width: 768px) { .navbar-links { display: flex; } }
```

</div>
<div style="flex: 1;">

**Tailwind** (~10 líneas HTML)
```html
<nav class="flex items-center
  justify-between px-4 h-16
  bg-white border-b
  border-gray-200">
  <span class="text-xl font-bold
    text-gray-900">Logo</span>
  <div class="hidden md:flex
    items-center gap-2">
    <a class="px-3 py-2 text-sm
      text-gray-600 rounded-lg
      hover:bg-gray-100">Inicio</a>
  </div>
</nav>
```

</div>
</div>

Note:
Comparativa lado a lado. CSS tradicional: 25 líneas en un archivo separado, necesitas cambiar de archivo para entender el estilo. Tailwind: las clases están en el HTML, ves todo de un vistazo. La media query está implícita en `md:flex`. Sin archivos CSS adicionales. Sin colisiones de nombres. Sin CSS muerto: si eliminas el nav del HTML, el CSS desaparece automáticamente.

---

## 🧪 Técnicas Avanzadas

### Valores Arbitrarios
```html
<div class="w-[300px] h-[calc(100vh-4rem)] bg-[#bada55] 
            grid-cols-[200px_1fr_200px]">
```

### Peer: reaccionar al estado de un hermano
```html
<input class="peer" required />
<p class="hidden peer-invalid:block text-red-500">
  Este campo es obligatorio
</p>
```

### Group: reaccionar al estado del padre
```html
<div class="group">
  <h2 class="group-hover:text-blue-500">Título</h2>
</div>
```

### Has: estilizar padre según descendiente
```html
<fieldset class="has-[:invalid]:border-red-500">
  <input required />
</fieldset>
```

Note:
Valores arbitrarios con `[]` para cuando necesitas salirte de la escala. Úsalos con moderación: si un valor se repite, promociónalo a token en @theme. `peer` y `group` evitan lógica TypeScript innecesaria: en lugar de un flag `isInvalid` que maneje clases, delegas al CSS. `has-*` es potente para formularios: el fieldset entero se marca como error si algún hijo es inválido.

---

## 🌓 Modo Oscuro con Tailwind

```typescript
// theme.service.ts
@Injectable({ providedIn: 'root' })
export class ThemeService {
  private dark = signal(false);
  readonly isDark = this.dark.asReadonly();

  constructor() {
    const stored = localStorage.getItem('theme');
    if (stored === 'dark' || (!stored &&
        window.matchMedia('(prefers-color-scheme: dark)').matches)) {
      this.setDark(true);
    }
  }

  toggle() { this.setDark(!this.dark()); }
  private setDark(value: boolean) {
    this.dark.set(value);
    document.documentElement.classList.toggle('dark', value);
    localStorage.setItem('theme', value ? 'dark' : 'light');
  }
}
```

```html
<!-- Uso en plantillas -->
<body class="bg-gray-50 dark:bg-gray-900 text-gray-900 dark:text-gray-100">
  <app-card class="bg-white dark:bg-gray-800" />
</body>
```

Note:
Estrategia `class`: añade/quita la clase `dark` al `<html>`. Alternativa `media`: respeta `prefers-color-scheme` del SO. La estrategia class es preferible en apps de gestión: permite al usuario elegir independientemente del SO. El servicio persiste en localStorage y respeta la preferencia inicial del sistema.

---

## 🚦 Estrategia de Migración a Tailwind

<div class="mermaid">
graph LR
    F0[Fase 0: Instalar<br/>sin romper CSS] --> F1[Fase 1: Tailwind<br/>solo en componentes nuevos]
    F1 --> F2[Fase 2: Migrar<br/>al modificar existentes]
    F2 --> F3[Fase 3: Eliminar<br/>CSS heredado vacío]
    F3 --> F4[Fase 4: Consolidar<br/>Tailwind como estándar]
</div>

| Fase | Acción | Riesgo |
|------|--------|--------|
| 0 | Instalar Tailwind, añadir `@import` al final | Ninguno |
| 1 | Nuevos componentes = Tailwind | Bajo |
| 2 | Migrar componentes al tocarlos por otras razones | Medio |
| 3 | Eliminar CSS heredado no usado | Medio. Usar Coverage en DevTools |
| 4 | Limpiar dependencias CSS antiguas, documentar | Alto si quedaba CSS necesario |

Note:
No hagáis "big bang". La migración incremental permite que el proyecto siga funcionando mientras se adopta Tailwind. En la fase 3, usad la pestaña Coverage de Chrome DevTools para identificar CSS no utilizado. Reglas de coexistencia: no mezclar Tailwind y CSS tradicional en el mismo componente, documentar componentes migrados, mantener tests visuales.

---

## ❌ Antipatrones: @apply Mal Usado

```css
/* ❌ MAL: Recrear clases semánticas en CSS global */
@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-primary-500 text-white rounded-lg
           hover:bg-primary-600 font-medium;
  }
  .card {
    @apply bg-white rounded-xl shadow-sm border p-6;
  }
}
```

```html
<!-- ✅ BIEN: Componente Angular reutilizable -->
<app-button variant="primary">Guardar</app-button>
<app-card>Título</app-card>
```

<mark>@apply destruye la ventaja principal de Tailwind: transparencia en el HTML</mark>

Note:
Este es el error más común al migrar. El desarrollador echa de menos sus clases semánticas y las recrea con @apply. El resultado: tienes que ir a otro archivo a buscar qué hace `.btn-primary`, perdiendo la inmediatez de Tailwind. Si necesitas reutilizar estilos, extrae un componente Angular. @apply solo para casos extremos (20 `<td>` con las mismas 8 clases).

---

## ⚡ Motor JIT (Just-In-Time)

<div class="mermaid">
graph LR
    A[Escribes clases<br/>en plantillas] --> B[JIT escanea<br/>todos los archivos]
    B --> C[Genera solo<br/>el CSS usado]
    C --> D[Bundle final<br/>~15-20 KB comprimido]
    E[Clases NO usadas] --> F[❌ No se incluyen]
</div>

```
Clases totales de Tailwind:   ~100,000   (varios MB)
Clases usadas en proyecto:    ~500-1500
CSS generado en producción:   ~15-20 KB comprimido
```

Note:
El JIT es la magia que hace viable Tailwind. Sin JIT, tendrías que cargar un CSS de varios MB. Con JIT, solo se genera el CSS que realmente usas. Escanea tus plantillas Angular, archivos TS (busca strings que parezcan clases de Tailwind) y genera exactamente las reglas CSS necesarias. El resultado es un CSS mínimo en producción.

---

## 🛠️ Tailwind CSS IntelliSense para VS Code

| Funcionalidad | Descripción |
|---------------|-------------|
| **Autocompletado** | Escribe `bg-` y sugiere todos los colores con previsualización |
| **Previsualización de color** | Cuadrado de color junto a cada clase de color |
| **Linting** | Subraya clases inválidas (`bg-primari-500` → sugiere `bg-primary-500`) |
| **Documentación inline** | Hover sobre clase → tooltip con CSS equivalente |
| **@theme support** | Lee tu `@theme` y autocompleta tus colores personalizados |

<mark>Instalad la extensión oficial: Tailwind CSS IntelliSense (bradlc)</mark>

Note:
Esta extensión transforma la experiencia. Sin ella, tienes que consultar la documentación para cada clase. Con ella, escribes `bg-` y ves todos los colores disponibles con su previsualización. Si defines `--color-brand-500` en @theme, la extensión lo detecta y te sugiere `bg-brand-500`. El linting te avisa de erratas y conflictos (dos clases que se anulan).

---

## 🎬 Demo: Dashboard Widget en 15 Minutos

**Construimos un KPI Widget sin escribir CSS**

```html
<article class="bg-white rounded-xl shadow-sm border
                border-gray-200 p-6 flex flex-col gap-4">
  <h3 class="text-sm font-medium text-gray-500 uppercase
             tracking-wide">Ingresos Mensuales</h3>
  <p class="text-4xl font-bold text-gray-900">47.250 €</p>
  <span class="inline-flex items-center gap-1 px-2 py-1
               text-sm font-medium rounded-full
               bg-emerald-50 text-emerald-600">
    ↑ 12.5%
  </span>
  <!-- Gráfico placeholder con divs -->
  <div class="flex items-end gap-1 h-24">
    <div class="w-4 bg-primary-200 rounded-t h-[40px]"></div>
    <div class="w-4 bg-primary-200 rounded-t h-[65px]"></div>
    <div class="w-4 bg-primary-500 rounded-t h-[80px]"></div>
  </div>
</article>
```

Note:
Este widget de KPI muestra: título, valor grande, tendencia con color condicional, y gráfico placeholder con barras de divs. Cero CSS personalizado. Todo con clases de Tailwind. La barra "actual" se destaca con color primario. Las alturas usan valores arbitrarios. Tiempo de implementación: ~15 minutos. Con CSS tradicional, fácilmente el doble entre escribir CSS, nombrar clases y ajustar media queries.

---

## 🏋️ Actividad en Clase

**Construir un Dashboard Completo con Tailwind**

| ⏱️ Tiempo | 🎯 Objetivo | 📦 Entregable |
|-----------|-------------|---------------|
| 60 min | Crear dashboard con 4 widgets, tabla y gráficos | Componente Angular funcional |

**Requisitos**:
1. Fila 1: 4 widgets KPI en grid responsive (`grid-cols-1 sm:2 lg:4`)
2. Fila 2: Tabla zebra con scroll horizontal y columnas responsive
3. Fila 3: Dos gráficos placeholder (barras + líneas)
4. Todo con Tailwind, <mark>cero CSS personalizado</mark>

Note:
Aplicaréis todo lo aprendido en un dashboard real. Los widgets usan grid responsive: 1 columna en móvil, 2 en tablet, 4 en desktop. La tabla usa `overflow-x-auto` para móvil y `even:bg-gray-50` para zebra. Los gráficos se construyen con divs (barras) y SVG inline (líneas), todo estilado con Tailwind. 60 minutos, objetivo ambicioso pero alcanzable.

---

## ✅ Buenas Prácticas

1. **Domina CSS antes de Tailwind**. Tailwind es CSS con otro nombre
2. **Usa los tokens de @theme**: no valores arbitrarios como `p-[13px]`
3. **No abuses de @apply**: si lo usas constantemente, extrae un componente
4. **Agrupa clases lógicamente**: layout → espaciado → tamaño → tipografía → color
5. **Usa `focus-visible` en lugar de `focus`**: accesibilidad sin sacrificar estética
6. **Aprovecha `peer` y `group`** para reducir lógica TypeScript innecesaria

Note:
La práctica 1 es la más importante. La práctica 4 mejora la legibilidad: adoptad un orden consistente de clases en el equipo. La extensión Headwind para VS Code ordena automáticamente. La práctica 5: `focus-visible:ring-2` muestra el anillo solo al navegar con teclado, no al hacer clic con ratón. Win-win para estética y accesibilidad.

---

## ❌ Errores Frecuentes

| Error | Consecuencia | Solución |
|-------|-------------|----------|
| **Usar Tailwind sin saber CSS** | Código frágil, probar clases al azar | Aprender CSS primero |
| **Abusar de valores arbitrarios** | `w-[314px] h-[127px]` en todo → CSS inline | Usar escala de Tailwind |
| **@apply masivo** | `.btn { @apply... }` → recrear CSS tradicional | Componentes Angular |
| **Olvidar el mobile-first** | `lg:grid-cols-3 grid-cols-1` → la base pisa lg | Base primero: `grid-cols-1 lg:grid-cols-3` |
| **No verificar contraste** | Colores personalizados sin verificar WCAG AA | WebAIM Contrast Checker |

Note:
El error 4 es sutil pero común. Recuerda: en CSS, la última regla gana. Si escribes `class="lg:grid-cols-3 grid-cols-1"`, la clase `grid-cols-1` (sin prefijo = se aplica SIEMPRE) pisa a `lg:grid-cols-3` porque aparece después. El orden correcto es `grid-cols-1 lg:grid-cols-3`. La clase base define el default móvil; las variantes lo sobrescriben hacia arriba.

---

## 🧩 Formularios y Accesibilidad con Tailwind

```html
<input type="text"
       class="w-full px-3 py-2 border-2 border-gray-300 rounded-lg
              focus:border-primary-500 focus:ring-2
              focus:ring-primary-200 focus:outline-none
              disabled:opacity-50 disabled:bg-gray-100
              disabled:cursor-not-allowed
              read-only:bg-gray-50
              placeholder:text-gray-400" />

<select class="w-full px-3 py-2 border-2 border-gray-300 rounded-lg
               focus:border-primary-500 focus:ring-2
               focus:ring-primary-200 focus:outline-none
               accent-primary-500">
  <option>Opción 1</option>
</select>
```

- `accent-primary-500`: color de acento en checkbox/radio nativos
- `sr-only`: oculta visualmente, accesible para lectores
- `peer-invalid:block`: muestra error cuando input hermano es inválido

Note:
Tailwind estila formularios nativos sin perder accesibilidad. `disabled:opacity-50 disabled:cursor-not-allowed` comunica visualmente que el campo no es editable. `read-only:bg-gray-50` diferencia read-only de disabled. `placeholder:text-gray-400` estila solo el placeholder. Los inputs nativos mantienen su comportamiento accesible.

---

## 📊 Resumen

| Concepto | Clave |
|----------|-------|
| **Filosofía** | Tailwind es CSS con otro nombre. Utility-first elimina guerras de naming |
| **Configuración** | `npm install` + plugin Vite + `@import "tailwindcss"` + `@theme` |
| **@theme** | Colores oklch, fuentes, espaciados. Sin archivos JS de configuración |
| **Organización** | 95% en plantillas, `@apply` solo como excepción |
| **Clases clave** | Layout, colores, tipografía, estados, responsive, dark mode |
| **Técnicas** | Valores arbitrarios `[]`, `peer`, `group`, `has-*`, JIT |

Note:
Tailwind 4 simplifica todo: sin config JS, sin content paths manuales, configuración CSS nativa con @theme. El JIT garantiza bundles mínimos. La clave del éxito: saber CSS, usar los tokens del tema, aplicar clases en plantillas, y solo recurrir a @apply cuando realmente haya repetición excesiva.

---

## 🚀 Próximos Pasos

**Unidad 14: Implementación de Componentes con Tailwind**

- Sistema completo de botones con variantes y tamaños
- Cards, formularios, tablas de datos avanzadas
- Modales accesibles con Angular CDK + Tailwind
- Barras de navegación: navbar horizontal + sidebar colapsable
- Dashboard completo y comparativa CSS tradicional vs Tailwind

**Para profundizar**:
- Instalar Tailwind CSS IntelliSense en VS Code
- Leer la documentación de `@theme` y oklch
- Practicar: migrar un componente de CSS tradicional a Tailwind

Note:
En la unidad 14 pondremos en práctica todo esto construyendo componentes reales. Veremos cómo implementar botones con 5 variantes y 3 tamaños usando exclusivamente Tailwind, sin CSS personalizado. También haremos una comparativa seria: mismo componente en CSS tradicional y en Tailwind, midiendo líneas de código, mantenibilidad y tiempo de desarrollo.
