# Desarrollo de Interfaces

## Unidad 15 · Del Diseño a la Implementación

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad final. Aquí converge todo lo aprendido: Figma (U3), Layouts (U4), Angular + Tailwind + Storybook (U2). Vamos a recorrer el flujo profesional completo: inspeccionar un diseño en Figma, extraer design tokens, configurar Tailwind @theme, organizar componentes con Atomic Design, implementarlos, documentarlos y construir pantallas completas. Esta unidad es el 40% de la evaluación del módulo. Pregunta: ¿quién tiene ya su entorno de la Unidad 2 funcionando?

---

## Objetivos de aprendizaje

<span class="fragment">1. Analizar diseños Figma con <mark>Dev Mode</mark> y extraer especificaciones sistemáticamente</span>

<span class="fragment">2. Extraer y documentar un sistema de <mark>Design Tokens</mark> completo</span>

<span class="fragment">3. Configurar <mark>Tailwind @theme</mark> con tokens semánticos y modo oscuro</span>

<span class="fragment">4. Organizar componentes con <mark>Atomic Design</mark> (átomos, moléculas, organismos)</span>

<span class="fragment">5. Implementar <mark>8+ componentes</mark> Angular + Tailwind con todos sus estados</span>

<span class="fragment">6. Documentar en Storybook y construir <mark>pantallas completas</mark></span>

Note:
Seis objetivos, 8 fases. Esta unidad es la más larga y la que más peso tiene. Cada fase se construye sobre la anterior. Si os saltáis la FASE 2 (extraer bien los tokens), arrastraréis errores durante toda la implementación. Dediquemos tiempo a hacerlo bien desde el principio.

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

¿Cuánto tiempo se pierde cuando el diseño y el código <mark>no coinciden</mark>?

</div>

<span class="fragment" style="font-size: 1.1rem;">El desarrollador implementa "de memoria" → 15 discrepancias visuales</span>
<span class="fragment" style="font-size: 1.1rem;">El diseñador enumera diferencias → el desarrollador corrige → iterar</span>
<span class="fragment" style="font-size: 1.1rem;"><mark>Solución:</mark> flujo sistemático Figma → Tokens → Tailwind → Componentes → Storybook</span>

Note:
Este es el problema que resuelve esta unidad. Sin un proceso sistemático, el handoff diseño-desarrollo es caótico. Con nuestro flujo de 8 fases, cada paso es trazable, verificable y automatizable. El resultado: el código refleja fielmente el diseño. Pregunta: ¿habéis sufrido alguna vez el "esto no se parece al diseño"?

---

## El flujo completo en 8 fases

<div class="mermaid">
graph LR
  A["F1: Inspección<br>Figma Dev Mode"] --> B["F2: Extracción<br>Design Tokens"]
  B --> C["F3: Configuración<br>Tailwind @theme"]
  C --> D["F4: Organización<br>Atomic Design"]
  D --> E["F5: Implementación<br>Componentes"]
  E --> F["F6: Assets<br>Iconos, imágenes"]
  F --> G["F7: Pantallas<br>Composición"]
  G --> H["F8: Testing<br>Calidad"]
</div>

Note:
Estas 8 fases son el esqueleto de la unidad. Las recorreremos en orden. Cada fase produce artefactos que alimentan la siguiente. Si una fase está mal, el error se propaga. Por eso insistimos en hacer cada fase con rigor. Pregunta: ¿qué fase creéis que es la más crítica? (La F2: si los tokens son incorrectos, todo lo demás será incorrecto)

---

## FASE 1: Inspección sistemática de diseños

<span class="fragment">1. <mark>Visión general</mark>: recorrer el diseño completo, entender el propósito</span>

<span class="fragment">2. <mark>Identificar patrones</mark>: elementos recurrentes → futuros componentes</span>

<span class="fragment">3. <mark>Descomposición por capas</mark>: de fuera hacia dentro, identificar sistemas de layout</span>

<span class="fragment">4. <mark>Inspección detallada</mark> con Dev Mode: dimensiones, colores, tipografías, bordes, sombras</span>

<span class="fragment">5. <mark>Identificar estados</mark>: ideal, loading, empty, error, edge cases</span>

<span class="fragment">6. <mark>Documentar</mark>: checklist, dudas, observaciones técnicas</span>

Note:
La inspección no es mirar el diseño 5 minutos y empezar a programar. Es un proceso estructurado de 6 pasos. El paso 5 es el más olvidado: el diseño muestra el "happy path", pero la app real necesita loading spinners, empty states, mensajes de error... Si no están en Figma, preguntad al diseñador antes de implementar. Pregunta: ¿qué pasa si implementáis estados sin consultar al diseñador? (Inconsistencia visual entre pantallas)

---

## Checklist de inspección

<span class="fragment">☐ Dimensiones del viewport (width × height)</span>
<span class="fragment">☐ Layout principal: ¿flex?, ¿grid?, ¿combinación?</span>
<span class="fragment">☐ Breakpoints responsive: ¿móvil/tablet/desktop?</span>
<span class="fragment">☐ <mark>Colores</mark>: primario, secundario, neutros, semánticos</span>
<span class="fragment">☐ <mark>Tipografías</mark>: familias, tamaños, pesos, interlineados</span>
<span class="fragment">☐ <mark>Espaciados</mark>: padding y gap en cada contenedor</span>

Note:
Este checklist deberíais completarlo ANTES de escribir una sola línea de código. Es vuestra garantía de que no se os escapa nada. Imprimidlo o tenedlo en un segundo monitor mientras trabajáis. Pregunta: ¿qué apartado del checklist suele ser el más ignorado? (Los breakpoints responsive: mucha gente solo mira la versión desktop)

---

## FASE 2: Extracción de Design Tokens

**Paleta de colores:**

<span class="fragment">• <mark>Brand</mark>: primary-50 al 950, secondary-50 al 950</span>
<span class="fragment">• <mark>Neutral</mark>: neutral-0 (blanco), neutral-50 al 950 (escala de grises)</span>
<span class="fragment">• <mark>Semánticos</mark>: success, warning, error, info (con variantes claras)</span>

**Escala tipográfica:**

<span class="fragment">• Familias (sans, heading, mono), tamaños (xs a 4xl), pesos (400 a 700)</span>
<span class="fragment">• Interlineados (tight: 1.25, normal: 1.5, relaxed: 1.625)</span>

Note:
Si el diseño usa variables de Figma, la extracción es directa: abrid el panel de variables y documentad. Si no, tenéis que inferir los tokens inspeccionando múltiples componentes y buscando patrones. La paleta de colores debe cubrir TODOS los tonos, no solo los que aparecen en el diseño actual (siempre se necesitan variantes más claras/oscuras para hover, focus, etc.). Pregunta: ¿cuántos tonos debe tener una paleta de color profesional? (10 tonos: 50, 100, 200... 900, 950)

---

## FASE 2: Tabla de Design Tokens

| Token | Valor | Uso |
|---|---|---|
| `--color-primary` | `#2563EB` | Acciones principales |
| `--color-primary-hover` | `#1D4ED8` | Hover de botones primary |
| `--color-bg-primary` | `#FFFFFF` | Fondo principal |
| `--color-bg-secondary` | `#F8FAFC` | Fondo secundario |
| `--color-text-primary` | `#0F172A` | Texto principal |
| `--color-text-secondary` | `#64748B` | Texto secundario |
| `--color-border-default` | `#E2E8F0` | Bordes por defecto |
| `--radius-lg` | `0.5rem` | Botones, inputs |
| `--radius-xl` | `0.75rem` | Tarjetas, modales |
| `--shadow-md` | `0 4px 6px -1px rgb(0 0 0/0.1)` | Elevación media |

<span class="fragment">Esta tabla es el <mark>contrato</mark> entre diseño y desarrollo</span>

Note:
Documentad los tokens en una tabla como esta. Cada token tiene: nombre (que usaremos en @theme), valor (extraído de Figma), y uso (dónde se aplica). Esto evita ambigüedades. Si un token no está en esta tabla, no debería usarse en el código. Pregunta: ¿por qué usar nombres semánticos (text-primary) en lugar de nombres de color (slate-900)? (Porque si mañana el texto principal cambia de slate-900 a otra cosa, solo hay que cambiar el valor del token semántico)

---

## FASE 3: Configuración de Tailwind @theme

```css
@import "tailwindcss";

@theme {
  /* Colores de marca */
  --color-primary: #2563eb;
  --color-primary-hover: #1d4ed8;
  --color-primary-light: #dbeafe;

  /* Colores semánticos de superficie */
  --color-bg-primary: #ffffff;
  --color-bg-secondary: #f8fafc;
  --color-text-primary: #0f172a;
  --color-text-secondary: #64748b;
  --color-border-default: #e2e8f0;

  /* Estados */
  --color-success: #22c55e;
  --color-error: #ef4444;
  --color-warning: #f59e0b;

  /* Tipografía */
  --font-sans: 'Inter', ui-sans-serif, system-ui;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
}
```

<span class="fragment">Cada token de la tabla → una línea en `@theme`. Sin `tailwind.config.js`.</span>

Note:
Este es el corazón de la configuración. Cada token de la FASE 2 se traduce a una custom property en @theme. Una vez definido, se usa como clase Tailwind: `bg-primary`, `text-text-secondary`, `rounded-xl`. Pregunta: ¿cómo se usa un color definido como `--color-primary` en Tailwind? (`bg-primary`, `text-primary`, `border-primary`, `ring-primary`...)

---

## FASE 3: Modo oscuro

```css
.dark {
  --color-bg-primary: #0f172a;
  --color-bg-secondary: #1e293b;
  --color-text-primary: #f8fafc;
  --color-text-secondary: #94a3b8;
  --color-border-default: #334155;

  /* Sombras más sutiles en dark mode */
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.4);
}

@layer base {
  body {
    font-family: var(--font-sans);
    color: var(--color-text-primary);
    background-color: var(--color-bg-primary);
  }
}
```

<span class="fragment">Alternancia con `<mark>.dark</mark>` en `<html>` (estrategia `class` de Tailwind)</span>

Note:
El modo oscuro se configura sobrescribiendo las variables semánticas dentro de `.dark`. Los neutros se invierten (fondo oscuro, texto claro). Las sombras se vuelven más sutiles (en dark mode, una sombra negra sobre fondo oscuro no se ve). Pregunta: ¿cómo activáis el modo oscuro en la app? (`document.documentElement.classList.toggle('dark')`)

---

## FASE 4: Atomic Design en Angular

<div class="mermaid">
graph TB
  A["Átomos<br>Button, Input, Icon, Badge<br>shared/ui/"] --> B["Moléculas<br>InputField, SearchBar<br>shared/components/"]
  B --> C["Organismos<br>Navbar, Card, Modal, Table<br>shared/ui/ o features/"]
  C --> D["Templates<br>DashboardLayout, AuthLayout<br>shared/layouts/"]
  D --> E["Pages<br>DashboardPage, LoginPage<br>features/"]
</div>

Note:
Atomic Design (Brad Frost) aplicado a Angular. Átomos: elementos indivisibles. Moléculas: combinaciones de átomos. Organismos: secciones complejas. Templates: layouts de página. Pages: instancias con contenido real. Pregunta: ¿dónde pondríais un componente DataTable? (Organismo: combina Table + Input + Badge + Button + Pagination)

---

## FASE 4: Estructura de carpetas

```
src/app/
├── shared/
│   ├── ui/                  # Átomos
│   │   ├── button/          # ButtonComponent
│   │   ├── input/           # InputComponent
│   │   ├── badge/           # BadgeComponent
│   │   ├── avatar/          # AvatarComponent
│   │   ├── icon/            # IconComponent
│   │   └── spinner/         # SpinnerComponent
│   ├── components/          # Moléculas
│   │   ├── input-field/     # InputFieldComponent
│   │   └── search-bar/      # SearchBarComponent
│   ├── layouts/             # Templates
│   │   ├── dashboard-layout/
│   │   └── auth-layout/
│   └── services/
├── features/                # Pages
│   ├── dashboard/
│   └── auth/
└── app.component.ts
```

Note:
Estructura escalable. En `shared/ui` van los átomos (puramente presentacionales, altamente reutilizables). En `shared/components` las moléculas (combinan átomos). En `shared/layouts` los templates (estructuras de página). En `features` las páginas completas y componentes específicos de una funcionalidad. Pregunta: ¿por qué separar shared de features? (shared es reutilizable entre features; features contiene código específico de una funcionalidad)

---

## FASE 5: Implementación iterativa (10 pasos)

<span class="fragment">1. <mark>Analizar</mark> el componente en Figma (Dev Mode)</span>
<span class="fragment">2. <mark>Crear</mark> con Angular CLI: `ng g c shared/ui/button --standalone`</span>
<span class="fragment">3. <mark>Definir inputs</mark> usando `input<T>()` y `output<T>()`</span>
<span class="fragment">4. <mark>Implementar template</mark> HTML con clases Tailwind</span>
<span class="fragment">5. <mark>Clases condicionales</mark> basadas en inputs</span>
<span class="fragment">6. <mark>Comparar visualmente</mark> con Figma (lado a lado)</span>

Note:
Estos son los primeros 6 pasos del ciclo de 10. El paso 6 es crucial: tened Figma abierto en una ventana y la app Angular en otra. Comparad constantemente. Diferencias de 1-2px son aceptables (los navegadores renderizan tipografías diferente a Figma). Pregunta: ¿qué comando de Angular CLI crea un componente standalone? (`ng g c nombre --standalone`)

---

## FASE 5: Iteración (continuación)

<span class="fragment">7. <mark>Ajustar</mark> hasta que coincida con el diseño</span>
<span class="fragment">8. Escribir <mark>stories</mark> de Storybook (todas las variantes y estados)</span>
<span class="fragment">9. Escribir <mark>tests unitarios</mark> (renderización, cambios de estado)</span>
<span class="fragment">10. <mark>Documentar</mark> en Storybook Docs (descripción, props, ejemplos)</span>

<span class="fragment">Repetir para cada componente. Átomos: 15-20 min. Moléculas: 30-45 min. Organismos: 1-2 h.</span>

Note:
Los pasos 8, 9 y 10 son los que más se procrastinan. Hacedlos como parte del flujo, no como tarea separada. Un componente no está "terminado" hasta que tiene sus stories, sus tests y su documentación. Pregunta: ¿cuántas stories debería tener como mínimo un Button component? (Al menos 4 variantes × 3 tamaños + estados disabled/loading = 15+ stories)

---

## Ejemplo: InputFieldComponent (template)

```html
<div class="flex flex-col gap-1.5">
  <label [for]="id" class="text-sm font-medium"
    [class.text-error]="!!error()">
    {{ label() }}
  </label>

  <div class="relative">
    @if (leadingIcon()) {
      <div class="absolute left-3 top-1/2 -translate-y-1/2 text-neutral-400">
        <app-icon [name]="leadingIcon()!" size="sm" />
      </div>
    }

    <input [type]="type()" [placeholder]="placeholder()"
      [disabled]="disabled()" [(ngModel)]="value"
      class="w-full px-3 py-2 text-sm rounded-lg border
        focus:outline-none focus:ring-2
        disabled:opacity-50 disabled:cursor-not-allowed"
      [class.border-error]="!!error()"
      [class.pl-10]="!!leadingIcon()" />

    @if (error()) {
      <p class="text-xs text-error mt-0.5 flex items-center gap-1">
        <app-icon name="alert-circle" size="xs" /> {{ error() }}
      </p>
    }
  </div>
</div>
```

Note:
Este es un ejemplo real de implementación. Observad: `[class.border-error]="!!error()"` activa/desactiva clases condicionalmente (más limpio que concatenar strings). `@if` para renderizado condicional. `leadingIcon()` e `error()` son signals. Pregunta: ¿por qué usar `[class.xxx]` en lugar de `[ngClass]`? (Más simple y tipado para clases individuales)

---

## Ejemplo: InputFieldComponent (TypeScript)

```typescript
@Component({
  selector: 'app-input-field',
  standalone: true,
  imports: [CommonModule, FormsModule, IconComponent],
  templateUrl: './input-field.component.html',
})
export class InputFieldComponent {
  readonly label = input.required<string>();
  readonly type = input<'text' | 'email' | 'password' | 'number'>('text');
  readonly placeholder = input('');
  readonly error = input<string | null>(null);
  readonly disabled = input(false);
  readonly leadingIcon = input<string | undefined>(undefined);
  readonly value = model('');

  protected id = computed(() =>
    this.label().toLowerCase().replace(/\s+/g, '-')
  );
}
```

<span class="fragment">Todos los inputs con <mark>tipos literales</mark> (no `string` genérico)</span>

Note:
Usamos `input.required()` para props obligatorias. `model()` para two-way binding con el padre (`[(value)]="email"`). `computed()` para derivar el id del label (accesibilidad: el label debe estar asociado al input vía `for`/`id`). Tipos literales ('text' | 'email' | 'password' | 'number') en lugar de `string` para autocompletado y validación en compilación. Pregunta: ¿qué diferencia hay entre `input()` y `model()`? (input es de solo lectura padre→hijo; model soporta two-way binding)

---

## FASE 5: Stories de Storybook

```typescript
const meta: Meta<InputFieldComponent> = {
  title: 'Components/InputField',
  component: InputFieldComponent,
  tags: ['autodocs'],
  argTypes: {
    type: { control: 'select',
      options: ['text', 'email', 'password', 'number'] },
    error: { control: 'text' },
    disabled: { control: 'boolean' },
  },
};

export const Default: Story = {
  args: { label: 'Email', type: 'email', placeholder: 'tu@email.com' },
};

export const WithError: Story = {
  args: { label: 'Email', error: 'Email no válido' },
};

export const Disabled: Story = {
  args: { label: 'Email', disabled: true },
};

export const WithLeadingIcon: Story = {
  args: { label: 'Email', leadingIcon: 'mail' },
};
```

<span class="fragment">Cada estado significativo → su propia story</span>

Note:
Las stories cubren todos los estados: default, con error, disabled, con iconos. `argTypes` define controles interactivos en el panel de Storybook. `tags: ['autodocs']` genera documentación automática. Pregunta: ¿qué addon de Storybook permite verificar accesibilidad automáticamente? (`@storybook/addon-a11y`)

---

## FASE 6: Estrategia de iconos

**Opción recomendada: librería (Lucide)**

```bash
npm install lucide-angular
```

```typescript
import { Component, input } from '@angular/core';
import { LucideAngularModule } from 'lucide-angular';

@Component({
  selector: 'app-icon',
  standalone: true,
  imports: [LucideAngularModule],
  template: `<lucide-icon [name]="name()"
              [size]="size()" [class]="className()" />`,
})
export class IconComponent {
  readonly name = input.required<string>();
  readonly size = input<number>(20);
  readonly className = input('');
}
```

<span class="fragment">No exportes iconos uno a uno de Figma. Usa una librería optimizada.</span>

Note:
Lucide es la opción recomendada: +1000 iconos, optimizados, con componente Angular nativo. Alternativa: Heroicons (del equipo de Tailwind). Evitad exportar SVG manualmente de Figma para cada icono: es lento, propenso a errores y los SVG de Figma contienen metadatos innecesarios. Pregunta: ¿qué formato de exportación usaríais para un icono? (SVG — es vectorial, escalable y mínimo en tamaño)

---

## FASE 6: Imágenes y fuentes

**Imágenes:**

```html
<img src="product-800w.webp"
  srcset="product-400w.webp 400w, product-800w.webp 800w"
  sizes="(max-width: 640px) 100vw, 50vw"
  alt="Product description"
  loading="lazy"
  decoding="async" />
```

<span class="fragment">WebP como formato principal. <mark>`loading="lazy"`</mark> difiere carga. `srcset` para responsive.</span>

**Fuentes:**

```css
@font-face {
  font-family: 'Inter';
  src: url('/assets/fonts/inter-var.woff2') format('woff2');
  font-weight: 300 700;
  font-display: swap; /* Evita FOIT */
}
```

<span class="fragment"><mark>`font-display: swap`</mark>: muestra texto inmediatamente, la fuente se intercambia al cargar</span>

Note:
WebP ofrece 25-35% mejor compresión que PNG/JPEG con soporte universal en 2025. `loading="lazy"` mejora el LCP (Largest Contentful Paint) difiriendo imágenes fuera del viewport. `font-display: swap` es CRÍTICO: sin él, el navegador oculta el texto hasta que la fuente se descarga (FOIT: Flash of Invisible Text). Pregunta: ¿qué formato es aún mejor que WebP? (AVIF: hasta 50% mejor compresión que JPEG, soporte creciente)

---

## FASE 7: Pantallas completas — DashboardPage

```typescript
@Component({
  selector: 'app-dashboard-page',
  standalone: true,
  template: `
    <app-dashboard-layout pageTitle="Dashboard">
      @if (loading()) {
        <div class="flex justify-center py-20"><app-spinner size="lg" /></div>
      }
      @else if (error()) {
        <div class="text-center py-20">
          <h2 class="text-lg font-semibold mb-2">Error al cargar</h2>
          <app-button variant="primary" (clicked)="loadData()">Reintentar</app-button>
        </div>
      }
      @else if (stats().length === 0) {
        <div class="text-center py-20">
          <h2 class="text-lg font-semibold mb-2">No hay datos</h2>
          <app-button variant="primary">Crear proyecto</app-button>
        </div>
      }
      @else {
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
          @for (stat of stats(); track stat.id) {
            <app-card>...</app-card>
          }
        </div>
      }
    </app-dashboard-layout>
  `,
})
export class DashboardPageComponent {
  protected loading = signal(true);
  protected error = signal<string | null>(null);
  protected stats = signal<Stat[]>([]);
}
```

<span class="fragment">Todos los estados cubiertos: <mark>loading, error, empty, ideal</mark></span>

Note:
Esta es la implementación de una pantalla real. Observad los 4 estados: loading (spinner), error (mensaje + reintentar), empty (mensaje + acción), ideal (datos). El usuario NUNCA ve una pantalla en blanco o rota. Todos los componentes (Card, Button, Spinner) son reutilizados, no implementados aquí. Pregunta: ¿por qué usar signals en lugar de variables normales? (Los cambios en signals son reactivos: la UI se actualiza automáticamente)

---

## Gestión de todos los estados

<div class="mermaid">
stateDiagram-v2
  [*] --> Loading
  Loading --> Error: API falla
  Loading --> Empty: Sin datos
  Loading --> Ideal: Datos OK
  Error --> Loading: Reintentar
  Ideal --> Loading: Recargar
  Ideal --> Empty: Datos eliminados
  Empty --> Ideal: Datos añadidos
</div>

<span class="fragment">Cada estado tiene su propia UI. El usuario siempre sabe qué está pasando.</span>

Note:
Este diagrama muestra todas las transiciones de estado posibles en una pantalla. Cada estado tiene un tratamiento visual específico. Si el diseño de Figma no incluye todos estos estados, consultad con el diseñador. Implementar estados sin diseño es una de las principales causas de inconsistencia visual. Pregunta: ¿qué diferencia hay entre el estado Empty y el estado Error? (Empty: no hay datos que mostrar, es una situación normal. Error: algo fue mal al cargar, es una situación excepcional)

---

## Actividad en clase: Inspección colaborativa

<span class="fragment">Se proyecta un diseño de Figma. Trabajo en parejas.</span>

<span class="fragment">1. Completar el <mark>checklist de inspección</mark> para una pantalla asignada</span>

<span class="fragment">2. Identificar: layout, colores, tipografías, espaciados</span>

<span class="fragment">3. Identificar <mark>componentes reutilizables</mark> y sus variantes</span>

<span class="fragment">4. Identificar <mark>estados necesarios</mark> (loading, empty, error)</span>

<span class="fragment">5. Puesta en común: cada pareja expone sus hallazgos</span>

**Duración:** 25 minutos

Note:
Actividad para practicar la FASE 1. El docente asigna una pantalla diferente a cada pareja. La puesta en común es importante: diferentes personas identifican diferentes patrones. Pregunta: ¿qué es más importante identificar primero: los colores o los componentes? (Los componentes: definen la estructura. Los colores se extraen después)

---

## Actividad en clase: Code review de un componente

<span class="fragment">Se proporciona un <mark>componente con errores intencionados</mark></span>

<span class="fragment">Errores típicos:</span>
<span class="fragment">• Falta `min-w-0` → desbordamiento</span>
<span class="fragment">• Clases condicionales incorrectas</span>
<span class="fragment">• Falta estado disabled visual</span>
<span class="fragment">• Tipado `any` en lugar de tipos literales</span>

<span class="fragment">Identificar errores → proponer correcciones → comparar con Figma</span>

**Duración:** 20 minutos

Note:
Actividad para desarrollar ojo crítico. El componente tiene 3-4 errores que simulan problemas reales. La comparación con Figma es el paso final: solo cuando el componente se ve IDÉNTICO al diseño, está correcto. Pregunta: ¿cuál es el error más difícil de detectar? (Falta de min-w-0: el desbordamiento solo se ve con contenido muy largo)

---

## Buenas prácticas

<span class="fragment">1. <mark>Implementa los átomos primero</mark>. Una hora en átomos ahorra 10 horas después</span>

<span class="fragment">2. <mark>Mantén Figma abierto</mark> durante TODA la implementación. No trabajes "de memoria"</span>

<span class="fragment">3. <mark>Un componente, una responsabilidad</mark>. Lógica de negocio → servicios, no componentes</span>

<span class="fragment">4. <mark>Documenta mientras implementas</mark>. Stories y tests como parte del flujo, no como tarea aparte</span>

<span class="fragment">5. <mark>Usa tokens semánticos</mark>, no valores hardcodeados. `bg-primary`, no `bg-blue-600`</span>

<span class="fragment">6. <mark>Prioriza la accesibilidad</mark> como requisito funcional, no como "nice to have"</span>

Note:
La #1 es la más importante: unos átomos bien diseñados son la base de todo el sistema. La #5 evita el problema de "15 tonos de azul diferentes porque en algunos sitios puse #3b82f6 y en otros #4a90d9". Pregunta: ¿qué práctica de estas 6 os parece más difícil de mantener? (La #4: documentar mientras implementas requiere disciplina)

---

## Errores frecuentes

<span class="fragment">1. <mark>Empezar por las pantallas</mark> en lugar de por los componentes → duplicar código</span>

<span class="fragment">2. <mark>No definir tokens</mark> y usar colores hardcodeados → inconsistencia visual</span>

<span class="fragment">3. <mark>Ignorar estados</mark> (loading, empty, error) → pantalla rota con datos reales</span>

<span class="fragment">4. <mark>Copiar y pegar</mark> componentes en lugar de añadir variantes mediante inputs</span>

<span class="fragment">5. <mark>No tipar correctamente</mark>: `@Input() variant: string` en lugar de tipos literales</span>

<span class="fragment">6. <mark>No verificar contra Figma</mark> durante el desarrollo → 15 discrepancias al final</span>

Note:
El error #4 es una trampa de productividad: copiar y pegar un componente para hacer una variante es más rápido a corto plazo (30 segundos) pero genera deuda técnica masiva. Añadir una variante al componente existente (5 minutos) es la inversión correcta. Pregunta: ¿cómo detectáis el error #3 en una aplicación ajena? (Navegad a una página, simulad que la API falla en DevTools → Network → Offline)

---

## Resumen de la unidad

<span class="fragment">✅ <mark>F1 Inspección</mark>: Dev Mode, checklist, identificación de patrones</span>

<span class="fragment">✅ <mark>F2 Design Tokens</mark>: paleta de colores, tipografía, espaciado, sombras</span>

<span class="fragment">✅ <mark>F3 Tailwind @theme</mark>: traducción de tokens a CSS, modo oscuro</span>

<span class="fragment">✅ <mark>F4 Atomic Design</mark>: átomos, moléculas, organismos, templates, pages</span>

<span class="fragment">✅ <mark>F5 Implementación</mark>: ciclo de 10 pasos, tipos literales, clases condicionales</span>

<span class="fragment">✅ <mark>F6 Assets</mark>: iconos (Lucide), imágenes (WebP/lazy), fuentes (swap)</span>

Note:
Esta unidad resume todo el módulo. Si domináis estas 8 fases, podéis enfrentar cualquier proyecto profesional de desarrollo de interfaces. El flujo Figma → Design Tokens → Tailwind @theme → Componentes Angular → Storybook → App es el estándar de la industria en 2025. Pregunta: ¿cuál de las 8 fases creéis que necesita más práctica?

---

## Próximos pasos

<span class="fragment">📌 <mark>Actividad 1</mark>: Implementar 3 átomos (Badge, Avatar, Divider) desde Figma</span>

<span class="fragment">📌 <mark>Actividad 2</mark>: Implementar DataTable con búsqueda, filtros y paginación</span>

<span class="fragment">📌 <mark>Actividad 3</mark>: Proyecto completo: Biblioteca personal (Figma → Tokens → App)</span>

<span class="fragment">📌 <mark>Actividad 4</mark>: Sistema de temas dinámicos con persistencia</span>

<span class="fragment">📌 <mark>Actividad 5</mark>: Proyecto final: App de gestión de proyectos (Jira-like)</span>

<span class="fragment">📌 <mark>Evaluación</mark>: La Actividad 5 puede usarse como proyecto de evaluación final</span>

Note:
Cinco actividades de dificultad creciente. La Actividad 3 es el flujo completo aplicado a una app pequeña (biblioteca personal). La Actividad 5 es el proyecto final del módulo: una app de gestión de proyectos tipo Jira con 15+ componentes, diseño responsive, drag & drop y despliegue. Planificad bien el tiempo.

---

## ¿Preguntas?

<div style="font-size: 1.5rem; margin-top: 2rem;">

`@theme` · `input.required<T>()` · `signal()` · `@container`

<br>

**¡Enhorabuena!** Habéis completado el módulo de Desarrollo de Interfaces 🎯

</div>

Note:
Última sesión del módulo. Resolved todas las dudas pendientes. La Actividad 5 (proyecto final) es vuestra oportunidad de demostrar todo lo aprendido. Poned especial atención a: fidelidad al diseño Figma, tipado TypeScript sin any, cobertura de estados (loading/empty/error), y documentación en Storybook. ¡Buen trabajo!

---

## Referencias

- **Angular Docs:** https://angular.dev
- **Tailwind CSS v4:** https://tailwindcss.com/docs/v4
- **Storybook Angular:** https://storybook.js.org/docs/angular
- **Figma Dev Mode:** https://help.figma.com/hc/en-us/articles/15033890310167
- **Lucide Icons:** https://lucide.dev
- **Style Dictionary:** https://amzn.github.io/style-dictionary
- **Atomic Design (Brad Frost):** https://atomicdesign.bradfrost.com
- **axe DevTools:** https://www.deque.com/axe
- **Squoosh (image optimizer):** https://squoosh.app
