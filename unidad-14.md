
<!-- .slide: data-background="#1e293b" -->
## Módulo 0488 · Desarrollo de Interfaces
### Unidad 14: Implementación de Componentes con Tailwind CSS

Botones · Cards · Formularios · Tablas · Modales · Navbar · Dashboard

<small>CFGS DAM · Curso 2025/26</small>

Note:
Esta unidad es 100% práctica. Vamos a construir 7 tipos de componentes usando Angular + Tailwind, sin una sola línea de CSS personalizado. Al final, comparativa objetiva CSS tradicional vs Tailwind. ¿Listos para codificar?

---

<!-- .slide: data-background="#f1f5f9" -->
## 🎯 Objetivos de Aprendizaje

1. Implementar <mark>botones, cards, formularios, tablas, modales, navbars y dashboards</mark> con Tailwind
2. Construir componentes con variantes, tamaños y estados visuales completos
3. Desarrollar formularios con validación reactiva y feedback visual
4. Implementar tablas con zebra, scroll, ordenación y selección
5. Construir modales accesibles con Angular CDK + Tailwind
6. Comparar sistemáticamente <mark>CSS tradicional vs Tailwind</mark>

Note:
6 objetivos, 7 componentes. Cada componente lo veremos con código real. El objetivo 6 es la reflexión final: no se trata de ser fan de Tailwind, sino de entender cuándo y por qué elegirlo.

---

<!-- .slide: data-background="#f1f5f9" -->
## 🤔 Motivación: El Coste de la Inconsistencia

<div class="mermaid">
graph TD
    A[5 desarrolladores<br/>en el mismo proyecto] --> B[CSS Tradicional]
    A --> C[Tailwind + Componentes Angular]
    B --> B1[5 nombres para<br/>el mismo botón]
    B --> B2[3 escalas de<br/>espaciado distintas]
    B --> B3[Especificidad<br/>descontrolada]
    C --> C1[1 componente Button<br/>con 5 variantes]
    C --> C2[Escala única:<br/>p-4 = 16px siempre]
    C --> C3[Especificidad plana:<br/>sin conflictos]
</div>

Note:
En equipos, Tailwind fuerza consistencia. No hay debate sobre si el padding es 15px o 16px: `p-4` es 16px. No hay 5 formas de llamar al botón principal: hay un componente `<app-button variant="primary">`. La combinación de Tailwind + componentes Angular encapsulados es la fórmula ganadora.

---

## 🔘 Componente 1: Sistema de Botones

```typescript
@Component({
  selector: 'app-button',
  template: `
    <button
      [class]="buttonClasses()"
      [disabled]="loading() || disabled()"
      [attr.aria-disabled]="loading() || disabled()"
      [type]="type()">
      @if (loading()) {
        <svg class="animate-spin ...">...</svg>
      }
      <ng-content />
    </button>
  `
})
export class ButtonComponent {
  variant = input<'primary'|'secondary'|'outline'|'ghost'|'danger'>('primary');
  size = input<'sm'|'md'|'lg'>('md');
  loading = input(false);
  disabled = input(false);
  type = input<'button'|'submit'|'reset'>('button');

  buttonClasses = computed(() => {
    // Retorna string de clases según variant + size + estados
  });
}
```

Note:
El botón es el componente más sencillo pero ilustra el patrón: señales computadas que generan la cadena de clases. 5 variantes, 3 tamaños, estados loading/disabled. Cero CSS personalizado. El botón nativo `<button>` ya es accesible por teclado. No necesitamos divs con onclick.

---

## 🎨 Variantes del Botón

| Variante | Clases Tailwind | Uso |
|----------|----------------|-----|
| **primary** | `bg-primary-500 text-white hover:bg-primary-600` | Acción principal |
| **secondary** | `bg-gray-100 text-gray-900 hover:bg-gray-200` | Acción alternativa |
| **outline** | `border-2 border-primary-500 text-primary-500 bg-transparent` | Acción secundaria |
| **ghost** | `text-primary-500 hover:bg-primary-50` | Mínima importancia |
| **danger** | `bg-red-600 text-white hover:bg-red-700` | Acción destructiva |

Tamaños: `sm` = `px-3 py-1.5 text-sm`, `md` = `px-4 py-2 text-sm`, `lg` = `px-6 py-3 text-base`

```html
<app-button variant="primary" size="lg">Guardar cliente</app-button>
<app-button variant="danger" size="sm" (click)="delete()">Eliminar</app-button>
<app-button variant="outline" [loading]="true">Cargando...</app-button>
```

Note:
5 variantes × 3 tamaños = 15 combinaciones, todas desde un solo componente. La clave es la señal computada `buttonClasses()` que concatena las clases según los inputs. El estado loading muestra un spinner SVG animado con `animate-spin`. El botón se deshabilita automáticamente durante el loading. Los botones de solo icono llevan `aria-label` para accesibilidad.

---

## 🃏 Componente 2: Sistema de Tarjetas (Cards)

| Tipo | Layout | Uso |
|------|--------|-----|
| **Básica** | `bg-white rounded-xl shadow-sm border p-6` | Contenedor genérico |
| **Con header/body/footer** | Slots con `ng-content select` | Contenido estructurado |
| **Horizontal** | `flex flex-col md:flex-row` | Listados compactos |
| **Estadística** | `flex flex-col gap-2` con valor grande + tendencia | Dashboard widgets |
| **Con imagen** | Imagen `w-full h-48 object-cover rounded-t-xl` | Catálogo, productos |

```html
<app-card>
  <h3 class="text-lg font-semibold">Título</h3>
  <p class="text-gray-600">Contenido de la tarjeta</p>
</app-card>
```

Note:
La card de estadística es la estrella del dashboard: valor en `text-3xl font-bold`, título en `text-sm text-gray-500`, tendencia en verde/rojo con `inline-flex items-center gap-1`. La card con imagen usa `object-cover` para mantener la proporción. La card horizontal cambia de `flex-col` en móvil a `flex-row` en desktop.

---

## 📝 Componente 3: Sistema de Formularios

<mark>5 estados visuales por campo: normal, hover, focus, error, disabled</mark>

```html
<!-- Input con label flotante y validación -->
<div class="relative">
  <input
    [formControl]="control"
    [class]="inputClasses()"
    class="peer w-full px-3 pt-5 pb-2 border-2 rounded-lg
           border-gray-300 focus:border-primary-500 focus:ring-2
           focus:ring-primary-200 outline-none transition"
    [class.border-red-500]="control.invalid && control.touched"
    [class.focus:border-red-500]="control.invalid && control.touched"
    [attr.aria-describedby]="errorId"
    [attr.aria-invalid]="control.invalid && control.touched" />
  <label class="absolute left-3 transition-all duration-200
                peer-focus:top-1 peer-focus:text-xs peer-focus:text-primary-500
                peer-[:not(:placeholder-shown)]:top-1
                peer-[:not(:placeholder-shown)]:text-xs">
    {{ label() }}
  </label>
</div>
@if (control.invalid && control.touched) {
  <p [id]="errorId" class="text-red-500 text-sm mt-1">
    {{ errorMessage() }}
  </p>
}
```

Note:
El label flotante usa `peer-*` para reaccionar al estado del input. Cuando el input tiene foco o valor, el label sube. La validación reactiva de Angular proporciona `touched`, `dirty`, `invalid`. Los estilos condicionales aplican borde rojo y mensaje de error. `aria-describedby` asocia el error al campo. `aria-invalid` marca el campo como inválido para lectores de pantalla.

---

## 🔘 Componentes de Formulario: Checkbox y Toggle

```html
<!-- Checkbox personalizado -->
<label class="inline-flex items-center gap-2 cursor-pointer">
  <input type="checkbox" class="sr-only peer" [formControl]="control" />
  <span class="w-5 h-5 rounded border-2 border-gray-300
               peer-checked:bg-primary-500 peer-checked:border-primary-500
               peer-focus-visible:ring-2 peer-focus-visible:ring-primary-200
               flex items-center justify-center transition">
    <!-- SVG checkmark visible solo cuando checked -->
    <svg class="w-3 h-3 text-white hidden peer-checked:block">...</svg>
  </span>
  {{ label() }}
</label>

<!-- Toggle Switch -->
<label class="inline-flex items-center gap-2 cursor-pointer">
  <input type="checkbox" class="sr-only peer" [formControl]="control" />
  <span class="w-11 h-6 rounded-full bg-gray-300
               peer-checked:bg-primary-500 transition-colors relative">
    <span class="absolute w-5 h-5 rounded-full bg-white shadow
                 top-0.5 left-0.5 peer-checked:translate-x-5
                 transition-transform duration-200"></span>
  </span>
</label>
```

Note:
Ambos ocultan el input nativo con `sr-only` (accesible para lectores, invisible visualmente) y estilizan un `<span>` que refleja el estado. El checkbox usa `peer-checked:bg-primary-500` para cambiar el fondo. El toggle usa `peer-checked:translate-x-5` para deslizar el círculo. El `<label>` envuelve todo, haciendo que el clic en cualquier parte active el input.

---

## 📊 Componente 4: Tablas de Datos

```html
<div class="overflow-x-auto rounded-lg border border-gray-200 shadow-sm">
  <table class="w-full text-sm text-left">
    <thead>
      <tr class="bg-gray-50 border-b border-gray-200">
        <th class="px-4 py-3 font-medium text-gray-500 uppercase
                   tracking-wider text-xs">Nombre</th>
        <th class="px-4 py-3 font-medium text-gray-500 uppercase
                   tracking-wider text-xs hidden md:table-cell">Email</th>
        <th class="px-4 py-3 font-medium text-gray-500 uppercase
                   tracking-wider text-xs">Estado</th>
        <th class="px-4 py-3 font-medium text-gray-500 uppercase
                   tracking-wider text-xs">Acciones</th>
      </tr>
    </thead>
    <tbody>
      @for (item of data(); track item.id) {
        <tr class="even:bg-gray-50 hover:bg-primary-50 transition-colors">
          <td class="px-4 py-3 text-gray-700">{{ item.name }}</td>
          <td class="px-4 py-3 text-gray-700 hidden md:table-cell">{{ item.email }}</td>
          <td class="px-4 py-3">
            <span class="px-2 py-1 text-xs rounded-full"
                  [class.bg-emerald-50.text-emerald-700]="item.active"
                  [class.bg-gray-100.text-gray-600]="!item.active">
              {{ item.active ? 'Activo' : 'Inactivo' }}
            </span>
          </td>
          <td class="px-4 py-3">
            <app-button variant="ghost" size="sm">Editar</app-button>
          </td>
        </tr>
      }
    </tbody>
  </table>
</div>
```

Note:
Características: scroll horizontal con `overflow-x-auto`, filas alternas con `even:bg-gray-50`, hover con `hover:bg-primary-50`, columnas ocultas progresivamente con `hidden md:table-cell`, badges de estado con clases condicionales, cabeceras sticky opcionales con `sticky top-0`. Todo sin CSS personalizado. La tabla semántica usa `<thead>`, `<tbody>`, `<th>` para accesibilidad.

---

## 🪟 Componente 5: Modal Accesible

```typescript
// Modal con Focus Trap, Escape, role dialog, animaciones
@Component({
  selector: 'app-modal',
  template: `
    @if (isOpen()) {
      <div class="fixed inset-0 z-50 flex items-center 
                  justify-center p-4"
           (click)="closeOnOverlay() && close()">
        <!-- Overlay -->
        <div class="fixed inset-0 bg-black/50 transition-opacity"
             @fadeIn></div>
        <!-- Contenido -->
        <div @scaleIn cdkTrapFocus [cdkTrapFocusAutoCapture]="true"
             class="relative bg-white rounded-2xl shadow-2xl 
                    w-full max-w-lg p-6 max-h-[90vh] overflow-y-auto"
             role="dialog" aria-modal="true"
             [attr.aria-labelledby]="titleId">
          <h2 [id]="titleId" class="text-xl font-semibold">{{ title() }}</h2>
          <button (click)="close()" aria-label="Cerrar diálogo"
                  class="absolute top-4 right-4 p-1 rounded-full
                         hover:bg-gray-100">✕</button>
          <ng-content />
        </div>
      </div>
    }
  `,
  host: { '(document:keydown.escape)': 'close()' }
})
```

Note:
Modal profesional con: overlay semitransparente, cierre con Escape, trampa de foco con `cdkTrapFocus`, retorno de foco al cerrar, role `dialog` + `aria-modal`, `aria-labelledby` al título, animaciones de entrada/salida con `@angular/animations`, y configuración `closeOnOverlay`. En móvil, puede ser fullscreen con `rounded-none` usando variantes responsive.

---

## 🧭 Componente 6: Barras de Navegación

<div class="mermaid">
graph TD
    A[Navbar Superior fijo] --> B[Logo + Navegación + Avatar]
    C[Sidebar Colapsable] --> D[Expandida: w-64<br/>icono + texto]
    C --> E[Colapsada: w-16<br/>solo iconos]
    C --> F[Móvil: Drawer con overlay]
    G[Tabs / Pestañas] --> H[Barra horizontal<br/>borde inferior activo]
</div>

Note:
La sidebar es el componente más complejo en responsive. En móvil: drawer oculto que se abre con overlay y botón hamburguesa. En tablet: colapsada a iconos (w-16). En desktop: expandida con texto (w-64). La transición se anima con `transition-all duration-300`. El contenido principal ajusta su margen: `ml-0 md:ml-16 lg:ml-64`.

---

## 📐 Navbar y Tabs: Código

```html
<!-- Navbar horizontal responsive -->
<nav class="fixed top-0 inset-x-0 z-40 h-16 bg-white
            border-b border-gray-200 flex items-center
            justify-between px-4 md:px-6">
  <div class="flex items-center gap-4">
    <button class="p-2 rounded-lg hover:bg-gray-100 md:hidden"
            (click)="toggleSidebar()">☰</button>
    <span class="text-xl font-bold text-gray-900">App</span>
  </div>
  <div class="hidden md:flex items-center gap-1">
    <a class="px-3 py-2 rounded-lg text-sm font-medium
              text-gray-600 hover:text-gray-900 hover:bg-gray-100">
      Dashboard
    </a>
  </div>
  <div class="flex items-center gap-3">
    <div class="w-8 h-8 rounded-full bg-primary-500 text-white
                flex items-center justify-center text-sm font-medium">
      JD
    </div>
  </div>
</nav>

<!-- Tabs -->
<div class="flex border-b border-gray-200">
  @for (tab of tabs(); track tab.id) {
    <button class="px-4 py-2 text-sm font-medium border-b-2
                   transition-colors"
            [class.text-primary-600]="tab.active"
            [class.border-primary-600]="tab.active"
            [class.border-transparent]="!tab.active"
            [class.text-gray-500]="!tab.active"
            (click)="selectTab(tab.id)">
      {{ tab.label }}
    </button>
  }
</div>
```

Note:
Navbar: botón hamburguesa solo en móvil (`md:hidden`), navegación central oculta en móvil (`hidden md:flex`), avatar siempre visible. Tabs: estado activo con `text-primary-600` y `border-primary-600`, inactivo con `text-gray-500` y `border-transparent`. Navegación entre tabs con teclado usando `FocusKeyManager` de Angular CDK.

---

## 📊 Componente 7: Dashboard Layout

```html
<div class="min-h-screen bg-gray-50">
  <!-- Navbar fijo -->
  <app-navbar class="fixed top-0 inset-x-0 z-40 h-16" />

  <!-- Sidebar -->
  <app-sidebar class="hidden md:block fixed left-0 top-16 bottom-0
                      w-16 lg:w-64 z-30 bg-gray-900 text-white
                      transition-all duration-300" />

  <!-- Contenido principal -->
  <main class="pt-16 pb-16 md:pb-0 md:ml-16 lg:ml-64
               p-4 md:p-6 lg:p-8 transition-all duration-300">
    <!-- Widgets: grid responsive -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3
                xl:grid-cols-4 gap-4 md:gap-6">
      <app-stats-widget title="Ingresos" value="47.250 €"
                        trend="+12.5%" />
      <app-stats-widget title="Clientes" value="1.284"
                        trend="+5.2%" />
      <app-stats-widget title="Pedidos" value="342"
                        trend="-2.1%" />
      <app-stats-widget title="Tickets" value="18"
                        trend="+0.0%" />
    </div>

    <!-- Tabla -->
    <app-data-table [data]="recentOrders()" class="mt-6" />
  </main>
</div>
```

Note:
El dashboard integra todos los componentes anteriores. Layout: navbar fijo arriba, sidebar fija izquierda, contenido principal con márgenes dinámicos según el estado de la sidebar. Los widgets usan grid responsive: 1→2→3→4 columnas. La tabla reutiliza el componente `DataTable`. Padding responsive: `p-4 md:p-6 lg:p-8`. Transiciones suaves en sidebar y márgenes.

---

## 📋 Select Personalizado Accesible

```typescript
@Component({ selector: 'app-select' })
export class SelectComponent {
  options = input<{label: string; value: string}[]>([]);
  isOpen = signal(false);
  selectedValue = model<string>('');

  toggle() { this.isOpen.update(v => !v); }

  select(value: string) {
    this.selectedValue.set(value);
    this.isOpen.set(false);
  }
}
```

```html
<div class="relative">
  <button (click)="toggle()"
          class="w-full px-3 py-2 border-2 border-gray-300
                 rounded-lg text-left focus:border-primary-500"
          [attr.aria-expanded]="isOpen()">
    {{ selectedValue() || 'Seleccionar...' }}
  </button>
  @if (isOpen()) {
    <ul class="absolute z-10 w-full mt-1 bg-white border
               rounded-lg shadow-lg max-h-60 overflow-y-auto">
      @for (opt of options(); track opt.value) {
        <li (click)="select(opt.value)"
            class="px-4 py-2 hover:bg-gray-100 cursor-pointer"
            [class.bg-primary-50]="selectedValue() === opt.value">
          {{ opt.label }}
        </li>
      }
    </ul>
  }
</div>
```

Note:
Select personalizado porque el `<select>` nativo tiene estilado limitado. El botón actúa como trigger con `aria-expanded`. Las opciones son una lista desplegable. La opción seleccionada tiene `bg-primary-50`. Navegación por teclado con FocusKeyManager (no mostrado por brevedad). Todo Tailwind, cero CSS personalizado.

---

## 🔐 Formulario de Login Completo

```html
<div class="min-h-screen flex items-center justify-center
            bg-gray-100 p-4">
  <form [formGroup]="loginForm" (ngSubmit)="onSubmit()"
        class="bg-white rounded-2xl shadow-xl p-8 w-full max-w-md">
    <h1 class="text-2xl font-bold text-gray-900 mb-6">Iniciar sesión</h1>
    <app-input label="Email" type="email"
               formControlName="email" class="mb-4" />
    <app-input label="Contraseña" type="password"
               formControlName="password" class="mb-4" />
    <div class="flex items-center justify-between mb-6">
      <app-checkbox formControlName="remember">Recordarme</app-checkbox>
      <a class="text-sm text-primary-600 hover:underline">
        ¿Olvidaste tu contraseña?
      </a>
    </div>
    <app-button variant="primary" type="submit"
                class="w-full" [loading]="isSubmitting()">
      Iniciar sesión
    </app-button>
    <p class="text-center text-sm text-gray-500 mt-4">
      ¿No tienes cuenta? <a class="text-primary-600">Regístrate</a>
    </p>
  </form>
</div>
```

Note:
El formulario de login integra todos los subcomponentes: InputText, Checkbox, Button con loading. El `FormGroup` tiene validadores: email required + pattern, password required + minLength(8). El botón se deshabilita mientras el formulario es inválido o está enviando (`[disabled]="loginForm.invalid || isSubmitting()"`). Toast de éxito + redirección al dashboard.

---

## 📊 Tabla con Ordenación Visual

```typescript
sortColumn = signal<string>('');
sortDirection = signal<'asc' | 'desc'>('asc');

toggleSort(column: string) {
  if (this.sortColumn() === column) {
    this.sortDirection.update(d => d === 'asc' ? 'desc' : 'asc');
  } else {
    this.sortColumn.set(column);
    this.sortDirection.set('asc');
  }
  // Reordenar datos...
}
```

```html
<th class="px-4 py-3 cursor-pointer select-none"
    (click)="toggleSort('name')">
  <span class="inline-flex items-center gap-1">
    Nombre
    @if (sortColumn() === 'name') {
      <span>{{ sortDirection() === 'asc' ? '↑' : '↓' }}</span>
    } @else {
      <span class="text-gray-300">⇅</span>
    }
  </span>
</th>
```

Note:
Ordenación con señales: `sortColumn` almacena qué columna está ordenada, `sortDirection` la dirección. El icono cambia: flecha doble gris (sin ordenar), flecha arriba azul (asc), flecha abajo azul (desc). `cursor-pointer select-none` en el th indica que es clickeable pero no seleccionable. La lógica de ordenación puede delegarse a un pipe puro.

---

## 📊 Tabla con Selección de Filas y Acciones Masivas

```typescript
selectedIds = signal<Set<number>>(new Set());
allSelected = computed(() =>
  this.selectedIds().size === this.data().length
);

toggleAll() {
  if (this.allSelected()) {
    this.selectedIds.set(new Set());
  } else {
    this.selectedIds.set(new Set(this.data().map(d => d.id)));
  }
}

toggleRow(id: number) {
  this.selectedIds.update(ids => {
    const newIds = new Set(ids);
    newIds.has(id) ? newIds.delete(id) : newIds.add(id);
    return newIds;
  });
}
```

```html
<th class="px-4 py-3 w-10">
  <app-checkbox [checked]="allSelected()"
                (change)="toggleAll()" />
</th>

@if (selectedIds().size > 0) {
  <div class="bg-primary-50 px-4 py-2 flex items-center gap-3">
    <span class="text-sm">{{ selectedIds().size }} seleccionados</span>
    <app-button variant="danger" size="sm">Eliminar</app-button>
    <app-button variant="secondary" size="sm">Exportar</app-button>
  </div>
}
```

Note:
La selección usa `Set<number>` para IDs. `allSelected()` es una señal computada que compara el tamaño del Set con el total. `toggleAll()` selecciona/deselecciona todo. La barra de acciones masivas aparece condicionalmente con `@if`. Las filas seleccionadas tienen `bg-primary-50`. Patrón completo: checkbox maestro + checkbox por fila + barra de acciones.

---

## 🎬 Demo: Construcción de un Dashboard Widget

Construimos en vivo un <mark>KPI Widget con tendencia</mark>:

1. Creamos el componente `StatsWidget` standalone
2. Inputs: `title`, `value`, `trend`, `icon`
3. Template con Tailwind: card + valor grande + indicador tendencia
4. Color condicional: `trend > 0 ? 'text-emerald-600' : 'text-red-600'`
5. Resultado: 0 líneas de CSS, ~25 líneas de HTML, funcional

```typescript
@Component({
  selector: 'app-stats-widget',
  template: `
    <article class="bg-white rounded-xl shadow-sm border
                    border-gray-200 p-4 md:p-6">
      <div class="flex items-start justify-between mb-4">
        <p class="text-sm text-gray-500">{{ title() }}</p>
        @if (icon()) { <app-icon [name]="icon()!" /> }
      </div>
      <p class="text-3xl font-bold text-gray-900">{{ value() }}</p>
      @if (trend(); as t) {
        <span [class]="trendClasses()">↑ {{ t }}%</span>
      }
    </article>
  `
})
export class StatsWidgetComponent {
  title = input.required<string>();
  value = input.required<string>();
  trend = input<number>();
  icon = input<string>();
  trendClasses = computed(() =>
    (this.trend() ?? 0) >= 0
      ? 'text-emerald-600 bg-emerald-50 ...'
      : 'text-red-600 bg-red-50 ...'
  );
}
```

Note:
Demo en vivo de 10 minutos. El componente acepta 4 inputs. La señal computada `trendClasses()` devuelve clases verdes o rojas según la tendencia. El icono es opcional. La card es responsive: `p-4 md:p-6`. Este patrón se repite en todos los widgets del dashboard. Cero CSS personalizado, 100% Tailwind.

---

## 🧪 Tests de Componentes con Tailwind

```typescript
describe('ButtonComponent', () => {
  it('should apply primary variant classes', () => {
    const fixture = TestBed.configureTestingModule({...})
                       .createComponent(ButtonComponent);
    fixture.componentRef.setInput('variant', 'primary');
    fixture.detectChanges();

    const btn = fixture.nativeElement.querySelector('button');
    expect(btn.classList).toContain('bg-primary-500');
    expect(btn.classList).toContain('text-white');
    expect(btn.classList).not.toContain('bg-gray-100');
  });

  it('should disable button when loading', () => {
    fixture.componentRef.setInput('loading', true);
    fixture.detectChanges();
    const btn = fixture.nativeElement.querySelector('button');
    expect(btn.disabled).toBeTrue();
    expect(btn.querySelector('svg.animate-spin')).toBeTruthy();
  });

  it('should apply size classes correctly', () => {
    fixture.componentRef.setInput('size', 'lg');
    fixture.detectChanges();
    const btn = fixture.nativeElement.querySelector('button');
    expect(btn.classList).toContain('px-6');
    expect(btn.classList).toContain('py-3');
    expect(btn.classList).toContain('text-base');
  });
});
```

<mark>Testear las clases de Tailwind verificadas = diseño verificado</mark>

Note:
Los tests verifican que las clases correctas se aplican según los inputs. No basta con testear que el componente renderiza: hay que verificar que `variant="danger"` realmente aplica `bg-red-600`. La clase `animate-spin` en el SVG confirma que el spinner está presente durante el loading. Estos tests detectan regresiones cuando alguien modifica las clases del componente.

---

## 📊 Comparativa Resumen por Componente

| Dimensión | CSS Tradicional | Tailwind + Componentes |
|-----------|----------------|----------------------|
| **CSS Button** | ~200 líneas (7 variantes × 3 tamaños × estados) | <mark>0 líneas de CSS</mark> |
| **Ubicación** | Archivo `.css` separado del HTML | Clases en la plantilla HTML |
| **Legibilidad** | 1 clase semántica oculta los detalles | 12 clases explícitas muestran todo |
| **Mantenibilidad** | Cambio en CSS central → se propaga | Cambio en token @theme → se propaga |
| **Curva aprendizaje** | Baja para quien sabe CSS | Media (2-3 semanas con IntelliSense) |
| **Consistencia** | Depende de disciplina del equipo | <mark>Forzada</mark> por la escala predefinida |
| **CSS muerto** | Acumulación constante | JIT: solo CSS usado en producción |

Note:
No hay ganador absoluto. Para equipos de 2+ personas con Design System, Tailwind acelera y fuerza consistencia. Para proyectos pequeños con CSS muy artesanal, CSS puro puede ser más directo. Para el perfil DAM (aplicaciones de gestión en equipo), Tailwind es la opción más alineada con el mercado.

---

## 🏋️ Actividad en Clase

**Sistema de Tabla de Datos Avanzada**

| ⏱️ Tiempo | 🎯 Objetivo | 📦 Entregable |
|-----------|-------------|---------------|
| 75 min | Tabla con ordenación, filtro, paginación, selección y columnas responsive | Componente standalone + tests |

**Requisitos**:
1. Columnas configurables mediante `@Input`
2. Ordenación con indicadores visuales (flechas ↑↓)
3. Filtro por texto con debounce 300ms
4. Paginación con controles página anterior/siguiente
5. Selección con checkbox maestro + acciones masivas
6. Cabeceras sticky + scroll vertical interno + columnas responsive

Note:
Este es el componente más complejo de la unidad. La ordenación usa señales para almacenar columna/dirección. El filtro con debounce usa RxJS `debounceTime(300)`. La paginación es un slice del array. La selección usa `Set<number>`. Las cabeceras sticky con `sticky top-0 z-10 bg-gray-50`. Columnas responsive con `hidden md:table-cell`. Todo Tailwind, cero CSS.

---

## 🗂️ Sidebar Multinivel con Submenús

```html
<aside class="w-64 bg-gray-900 text-white min-h-screen py-4">
  <nav class="flex flex-col gap-1 px-3">
    @for (section of navigation(); track section.id) {
      <div>
        <button (click)="toggleSection(section.id)"
                class="flex items-center w-full gap-3 px-3 py-2
                       rounded-lg hover:bg-gray-800 transition-colors"
                [attr.aria-expanded]="isExpanded(section.id)">
          <svg class="w-5 h-5 shrink-0">...</svg>
          <span class="flex-1 text-left text-sm">{{ section.label }}</span>
          <svg class="w-4 h-4 transition-transform"
               [class.rotate-90]="isExpanded(section.id)">▶</svg>
        </button>
        @if (isExpanded(section.id)) {
          <div class="ml-4 border-l border-gray-700 pl-4 mt-1">
            @for (child of section.children; track child.id) {
              <a class="flex items-center gap-2 px-3 py-1.5 rounded-lg
                        text-sm text-gray-400 hover:text-white
                        hover:bg-gray-800 transition-colors"
                 [class.bg-gray-800.text-white]="isActive(child.id)">
                {{ child.label }}
              </a>
            }
          </div>
        }
      </div>
    }
  </nav>
</aside>
```

Note:
Sidebar con submenús expandibles. Cada sección es un botón con `aria-expanded`. El chevron rota con `rotate-90` usando clases condicionales. Los submenús tienen una línea vertical (`border-l`) y sangría (`ml-4 pl-4`). La sección activa se destaca con `bg-gray-800 text-white`. Las transiciones suavizan hover y cambios de estado.

---

## ✅ Buenas Prácticas

1. **Elementos HTML nativos** como base: `<button>`, no `<div role="button">`
2. **Señales computadas** para clases condicionales, no ternarios en template
3. **Variantes de estado de Tailwind** en lugar de lógica TypeScript: `group-hover:` en vez de `@HostListener`
4. **Componentes pequeños y enfocados**: un componente = una responsabilidad
5. **Tests de clases CSS**: verificar que `variant="danger"` aplica `bg-red-600`
6. **Guía de estilo viva** (Storybook) con todos los componentes y variantes

Note:
La práctica 3 es reveladora: `group-hover:opacity-100 opacity-0` en un tooltip elimina la necesidad de `@HostListener('mouseenter')`. Menos TypeScript = menos bugs. La práctica 5: no basta testear que el componente renderiza; verificad que las clases correctas se aplican según los inputs.

---

## ❌ Errores Frecuentes

| Error | Consecuencia |
|-------|-------------|
| **`div role="button"` en lugar de `<button>`** | 5 líneas extra, accesibilidad manual |
| **No gestionar estado loading** | Dobles clics, transacciones duplicadas |
| **Componentes para cada variante** | `ButtonPrimary`, `ButtonSecondary` → pesadilla |
| **Mezclar lógica de negocio con presentación** | Componente obtiene datos + renderiza + pagina |
| **Sin tipos TypeScript** para variantes | `variant: string` en vez de union type |
| **Ignorar estados de error en formularios** | Solo implementan normal y focus |

Note:
El error 3 es común en novatos: crear un componente para cada variante en lugar de un componente con input `variant`. La solución es un solo `ButtonComponent` con `variant: 'primary' | 'secondary' | ...`. Union types de TypeScript para que el compilador detecte valores inválidos.

---

## 📊 Resumen

| Componente | Características Clave |
|-----------|----------------------|
| **Button** | 5 variantes × 3 tamaños × estados loading/disabled. Señal computada |
| **Card** | Básica, header/footer, horizontal, estadística, con imagen |
| **Formulario** | Label flotante, 5 estados visuales, validación reactiva, aria |
| **Tabla** | Zebra, scroll, columnas responsive, ordenación, selección, paginación |
| **Modal** | Focus trap, Escape, overlay, role dialog, animaciones |
| **Navbar/Sidebar** | Responsive: drawer → colapsada → expandida. Tabs con teclado |
| **Dashboard** | Grid responsive 1→4 cols, composición de todos los componentes |

Note:
7 componentes, 0 líneas de CSS personalizado. Todos usan Tailwind en plantillas, señales computadas para clases condicionales, y Angular CDK para accesibilidad. El patrón se repite: inputs definen variantes, computed() genera clases, template aplica con `[class]`.

---

## 🚀 Próximos Pasos

**Unidad 15: Responsive Design en Aplicaciones Angular**

- Mobile First aplicado a apps de gestión
- Breakpoints de Tailwind con visualización gráfica
- Estrategia responsive para sidebar, tablas, formularios, modales
- `BreakpointObserver` de Angular CDK con Signals
- Dashboard completamente responsive (móvil + tablet + desktop)
- Responsive en Electron (aplicaciones de escritorio)

**Para profundizar**:
- Crear una librería de componentes propia con los 7 componentes
- Publicarla en un monorepo con Storybook
- Comparar vuestro código con Tailwind UI (referencia profesional)

Note:
La unidad 15 cierra el círculo: ahora que sabemos construir componentes, vamos a hacer que funcionen en cualquier dispositivo. Mobile-first desde el principio. Veremos cómo la sidebar se transforma entre móvil, tablet y desktop, y cómo usar `BreakpointObserver` cuando necesitamos lógica TypeScript condicionada por el tamaño de pantalla.
