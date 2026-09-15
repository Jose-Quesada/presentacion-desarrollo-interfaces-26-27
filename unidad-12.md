
<!-- .slide: data-background="#1a1a2e" -->
## Módulo 0488 · Desarrollo de Interfaces
### Unidad 12: Accesibilidad Web (WCAG)

POUR · ARIA · Angular CDK · Lighthouse · Lectores de pantalla

<small>CFGS DAM · Curso 2025/26</small>

Note:
La accesibilidad no es opcional ni un extra: es un derecho y una obligación legal. En España, el RD 1112/2018 exige nivel AA de WCAG para sitios públicos. La Ley 11/2023 lo amplía a servicios privados. Vamos a ver cómo implementarla en Angular con Tailwind. Pregunta de inicio: ¿alguien ha usado un lector de pantalla alguna vez?

---

<!-- .slide: data-background="#e8f5e9" -->
## 🎯 Objetivos de Aprendizaje

1. Identificar tipos de discapacidad y sus barreras en interfaces web
2. Aplicar los <mark>4 principios POUR</mark> de WCAG 2.1/2.2
3. Distinguir niveles de conformidad <mark>A, AA, AAA</mark> y exigencias legales
4. Dominar ARIA con la regla de oro: "no uses ARIA si HTML lo resuelve"
5. Implementar componentes accesibles con <mark>Angular CDK</mark>
6. Auditar con Lighthouse, WAVE, axe DevTools y lectores de pantalla

Note:
6 objetivos que cubren desde la teoría (POUR) hasta la práctica (herramientas). El más importante es el 4: ARIA es para cuando HTML no llega, no para sustituir HTML. Dato: el 15% de la población mundial tiene alguna discapacidad. En España, más de 4 millones. No es un nicho.

---

<!-- .slide: data-background="#e8f5e9" -->
## 🤔 Motivación: ¿Por Qué Accesibilidad?

| Razón | Impacto |
|-------|---------|
| **Ética** | La tecnología debe servir a <mark>todas</mark> las personas |
| **Legal** | RD 1112/2018, Ley 11/2023 → nivel AA obligatorio |
| **Negocio** | 15% de la población = mercado de 4M en España |
| **Calidad** | Lo accesible es usable para todos (subtítulos, contraste, teclado) |

<mark>"La accesibilidad no es una característica, es un derecho"</mark>

Note:
Tres pilares: ético (justicia social), legal (multas y sanciones) y de negocio (más usuarios). Las mejoras de accesibilidad benefician a todos: los subtítulos sirven en entornos ruidosos, el buen contraste en pantallas con reflejos, y el teclado es más rápido para usuarios avanzados. La accesibilidad NO es solo para personas con discapacidad permanente.

---

## 👥 Tipos de Discapacidad y Barreras

<div class="mermaid">
graph TD
    A[Discapacidad] --> B[Visual]
    A --> C[Auditiva]
    A --> D[Motriz]
    A --> E[Cognitiva]
    B --> B1[Lector de pantalla · sin alt → ❌]
    B --> B2[Baja visión · sin zoom → ❌]
    B --> B3[Daltonismo · solo color → ❌]
    C --> C1[Sin subtítulos en vídeo → ❌]
    D --> D1[Sin navegación por teclado → ❌]
    E --> E1[Interfaz sobrecargada · timeout → ❌]
</div>

Note:
Cada tipo de discapacidad tiene barreras específicas. Visual: sin alt en imágenes, el lector de pantalla no puede describirlas. Motriz: sin teclado, la app es inoperable para quien no usa ratón. La buena noticia: la mayoría de barreras se evitan con HTML semántico. Un `<button>` ya es accesible por teclado; un `<div onclick>` no. Pregunta: ¿qué pasaría si mañana no pudierais usar el ratón?

---

## 📜 WCAG: Web Content Accessibility Guidelines

<div class="mermaid">
graph LR
    WCAG2_0[WCAG 2.0<br/>2008] --> WCAG2_1[WCAG 2.1<br/>2018<br/>+ móvil, + cognitiva]
    WCAG2_1 --> WCAG2_2[WCAG 2.2<br/>2023<br/>+ foco, + entrada]
    WCAG2_2 --> WCAG3_0[WCAG 3.0<br/>Futuro<br/>Puntuaciones graduales]
</div>

| Nivel | Significado | Exigencia |
|-------|-------------|-----------|
| **A** | Mínimo imprescindible | Sin esto, es inaccesible |
| **AA** | Estándar legal UE | <mark>Objetivo de esta unidad</mark> |
| **AAA** | Máximo | No exigible para sitios completos |

Note:
WCAG 2.1 es el estándar actual de referencia legal en la UE. WCAG 2.2 añade criterios sobre foco visible y entrada de datos. WCAG 3.0 cambiará el paradigma: de criterios binarios (cumple/no cumple) a puntuaciones graduales (Bronze, Silver, Gold). Nosotros apuntamos al nivel AA: es lo que la ley exige y lo que toda app profesional debe cumplir.

---

## 🧩 Los 4 Principios POUR

<div class="mermaid">
graph TD
    POUR[WCAG Principles]
    POUR --> P[Perceptible]
    POUR --> O[Operable]
    POUR --> U[Comprensible<br/>Understandable]
    POUR --> R[Robusto]
    P --> P1[Alt text · Contraste · Estructura]
    O --> O1[Teclado · Foco · Tiempo · Navegación]
    U --> U1[Legible · Predecible · Ayuda en formularios]
    R --> R1[HTML válido · Compatible con tecnologías asistivas]
</div>

<mark>Si fallas un principio, la interfaz es inaccesible para alguien</mark>

Note:
POUR son interdependientes y no negociables. Si un formulario tiene contraste perfecto (Perceptible) pero no se navega con teclado (Operable), es inaccesible para una persona con discapacidad motriz. Vamos a ver cada principio con ejemplos de código Angular + Tailwind.

---

## 👁️ POUR · Perceptible

<mark>La información debe ser percibida por todos los sentidos disponibles</mark>

| Pauta | Requisito | Ejemplo |
|-------|-----------|---------|
| 1.1 Texto alternativo | `alt` en imágenes, `aria-label` en SVGs | `<img [alt]="imagen.descripcion" />` |
| 1.3 Estructura semántica | H1-H6 sin saltos, listas reales, tablas con th | `<h1>` único por página |
| 1.4 Contraste | 4.5:1 texto normal, 3:1 texto grande | `text-gray-900` sobre `bg-white` ✅ |
| 1.4 Color | No usar solo color para transmitir info | Error: borde rojo + icono + texto + `aria-invalid` |

Note:
Perceptible significa que ningún contenido sea invisible para los sentidos. El contraste es lo más fácil de verificar y lo que más se incumple. En Tailwind: `text-gray-400` sobre `bg-gray-100` NO cumple (contraste ~1.8). `text-gray-700` sobre `bg-white` SÍ cumple (contraste ~5.7). Regla: verificad SIEMPRE con WebAIM Contrast Checker.

---

## 🖐️ POUR · Operable

<mark>Toda funcionalidad operable por teclado y con tiempo suficiente</mark>

| Pauta | Requisito | Tailwind / Angular |
|-------|-----------|--------------------|
| 2.1 Teclado | Todo operable sin ratón | Usar `<button>`, no `<div onclick>` |
| 2.1 Sin trampas | El foco entra y sale de cualquier componente | `cdkTrapFocus` en modales + Escape |
| 2.2 Tiempo | Sin timeouts estrictos, avisar inactividad | Extender sesión con un clic |
| 2.3 Convulsiones | Nada destelle >3 veces/segundo | `prefers-reduced-motion: reduce` |
| 2.4 Navegación | Skip link, títulos de página, orden de foco lógico | `sr-only focus:not-sr-only` |

Note:
La pauta 2.1 es la más violada en SPAs. Cada vez que usáis un `<div (click)>` en lugar de `<button>`, estáis rompiendo la accesibilidad. Tailwind estila botones nativos perfectamente. Skip link: primer elemento de la página, oculto hasta que recibe foco con Tab, apunta a `<main id="main-content">`. En Angular: se añade en `index.html`.

---

## 🧠 POUR · Comprensible

<mark>La información y el manejo de la interfaz deben ser entendibles</mark>

- 3.1 **Idioma declarado**: `<html lang="es">` en `index.html`
- 3.2 **Comportamiento predecible**: un botón se activa con clic/Enter, no con doble clic
- 3.3 **Etiquetas en formularios**: `<label [for]="id">` asociado al `id` del input
- 3.3 **Errores con sugerencias**: `aria-describedby` vincula campo con mensaje de error
- 3.3 **Revisar antes de enviar**: formularios legales/financieros con paso de confirmación

Note:
El idioma en `<html lang="es">` es crítico: los lectores de pantalla usan la pronunciación correcta según el idioma. Si no está declarado, leen en inglés por defecto. Los formularios son donde más se falla: cada campo necesita label asociado, error vinculado con `aria-describedby`, e indicación de campos obligatorios. En Angular Reactive Forms: `[formControlName]` + label con `[for]` + error con `@if`.

---

## 🏗️ POUR · Robusto

<mark>Contenido compatible con tecnologías asistivas actuales y futuras</mark>

- 4.1 **HTML válido**: sin IDs duplicados, etiquetas anidadas correctamente
- 4.1 **Roles y estados expuestos**: cada elemento interactivo comunica qué es y su estado
- Los elementos nativos ya lo hacen: `<button>` tiene rol="button" y estados automáticos
- Componentes personalizados necesitan ARIA: `[attr.aria-expanded]="isOpen()"`

```html
<!-- ✅ Robusto: todo expuesto programáticamente -->
<button [disabled]="loading()" [attr.aria-busy]="loading()">
  @if (loading()) { <app-spinner /> }
  Guardar
</button>
```

Note:
Robusto significa que el código es interpretable por cualquier agente de usuario futuro. HTML válido es la base: usad el W3C Validator. En Angular, cuidado con IDs duplicados al reutilizar componentes. Solución: generar IDs únicos con `incrementalId` o usar `aria-labelledby` en lugar de `for` cuando el label está dentro del componente.

---

## 🏷️ ARIA: Accessible Rich Internet Applications

<div class="mermaid">
graph LR
    A{¿Existe elemento<br/>HTML nativo?}
    A -->|Sí| B[✅ Usa HTML nativo<br/>NO uses ARIA]
    A -->|No| C[⚠️ Usa ARIA:<br/>role + propiedades<br/>+ gestión de foco]
    C --> D[Implementa<br/>comportamiento<br/>de teclado manual]
</div>

<mark>"No uses ARIA si HTML ya lo resuelve" — Regla de Oro</mark>

Note:
Antes de `role="button"` en un `<div>`, preguntad: ¿puedo usar `<button>`? La respuesta casi siempre es sí. Tailwind permite estilizar `<button>` con cualquier apariencia. ARIA no añade interactividad: si creas un `div role="button"`, tienes que implementar manualmente Enter, Space, foco visible y prevención de eventos. Un mal uso de ARIA puede empeorar la accesibilidad.

---

## 📋 ARIA: Roles y Propiedades Clave

| Categoría | Atributo | Uso |
|-----------|----------|-----|
| **Roles** | `dialog`, `alert`, `tab`, `tabpanel` | Qué es el elemento |
| **Nombre** | `aria-label`, `aria-labelledby` | Cómo se llama para el lector |
| **Descripción** | `aria-describedby` | Vincula con texto explicativo o error |
| **Estado** | `aria-expanded`, `aria-selected`, `aria-checked` | Estado actual del elemento |
| **Vivo** | `aria-live="polite"`, `role="alert"` | Anuncia cambios dinámicos |
| **Ocultar** | `aria-hidden="true"` | Oculta del lector de pantalla (decorativo) |

Note:
Estos 6 tipos de atributos cubren el 95% de usos de ARIA. `aria-label` es para elementos sin texto visible (botón con solo icono). `aria-describedby` vincula el campo con su mensaje de error. `aria-live` anuncia cambios asíncronos: "Se han cargado 153 registros". Angular CDK tiene `LiveAnnouncer` que abstrae esto.

---

## ⌨️ Código: Modal Accesible con Angular CDK

```typescript
@Component({
  selector: 'app-modal',
  template: `
    @if (isOpen()) {
      <div class="fixed inset-0 z-50 bg-black/50"
           (click)="closeOnOverlay && close()">
        <div cdkTrapFocus [cdkTrapFocusAutoCapture]="true"
             class="bg-white rounded-2xl p-6 max-w-lg mx-auto mt-20"
             role="dialog" aria-modal="true"
             [attr.aria-labelledby]="titleId">
          <h2 [id]="titleId">{{ title() }}</h2>
          <ng-content />
          <button (click)="close()" aria-label="Cerrar diálogo">✕</button>
        </div>
      </div>
    }
  `,
  host: { '(document:keydown.escape)': 'close()' }
})
```

Note:
Este modal implementa: trampa de foco con `cdkTrapFocus`, cierre con Escape mediante `@HostListener`, retorno de foco al elemento que lo abrió (no mostrado por brevedad), overlay clickeable, roles ARIA (`dialog`, `aria-modal`, `aria-labelledby`), y botón de cierre con `aria-label`. Sin una línea de CSS personalizado: todo Tailwind.

---

## 📊 Tabla Comparativa: ❌ Incorrecto vs ✅ Correcto

| ❌ Incorrecto | ✅ Correcto |
|--------------|-------------|
| `<div onclick="save()">Guardar</div>` | `<button (click)="save()">Guardar</button>` |
| `<img src="graph.png" />` (sin alt) | `<img alt="Ventas 2024: enero 15k, feb 18k" />` |
| `<input placeholder="Email" />` (sin label) | `<label for="email">Email</label><input id="email" />` |
| `outline: none` (foco invisible) | `focus-visible:ring-2 ring-primary-500 ring-offset-2` |
| Error sin asociar: `<span>Error</span>` | `<span [id]="errorId" role="alert">...</span>` + `aria-describedby` |
| `<div *ngFor>` como tabla | `<table><thead><tr><th scope="col">Nombre</th>...</table>` |

Note:
Estos 6 pares cubren los errores más comunes. El primero es el más dañino: un div onclick no es focusable, no se activa con teclado, no tiene rol de botón. El navegador da todo esto gratis con `<button>`. La tabla: usar divs en lugar de table es inaccesible porque el lector de pantalla no puede navegar por celdas ni anunciar encabezados.

---

## 🛠️ Herramientas de Evaluación

| Herramienta | Tipo | ¿Qué detecta? |
|-------------|------|---------------|
| **Lighthouse** | Automática (Chrome) | ~40% de criterios WCAG. Puntuación 0-100 |
| **WAVE** | Extensión navegador | Análisis visual con iconos sobre la página |
| **axe DevTools** | Extensión + npm | Violaciones WCAG con gravedad y solución |
| **axe-core** | npm library | Integrable en tests Jasmine/Jest |
| **NVDA / VoiceOver** | Lector de pantalla | <mark>Prueba de fuego</mark>: ¿funciona sin ver? |
| **WebAIM Contrast** | Web | Relación de contraste texto/fondo |

Note:
Ninguna herramienta automática cubre el 100%. Lighthouse + WAVE + axe cubren quizá el 50% de criterios. El resto requiere prueba manual: navegar con teclado, usar lector de pantalla, verificar que el orden de foco es lógico. Una puntuación de 100 en Lighthouse no garantiza accesibilidad completa. Probar con NVDA o VoiceOver 10 minutos al mes es la mejor inversión.

---

## 🎯 Gestión del Foco con Angular CDK

```typescript
import { FocusKeyManager } from '@angular/cdk/a11y';

@Component({ ... })
export class AccessibleListComponent implements AfterViewInit {
  @ViewChildren('listItem') items!: QueryList<HTMLElement>;
  private keyManager!: FocusKeyManager<HTMLElement>;

  ngAfterViewInit() {
    this.keyManager = new FocusKeyManager(this.items)
      .withWrap(true)          // Wrap-around: último → primero
      .withTypeAhead(300);     // Tipado anticipatorio
  }

  onKeydown(event: KeyboardEvent) {
    if (event.key === 'ArrowDown') { this.keyManager.setNextItemActive(); }
    if (event.key === 'ArrowUp') { this.keyManager.setPreviousItemActive(); }
    if (event.key === 'Enter') { this.keyManager.activeItem?.click(); }
  }
}
```

Note:
FocusKeyManager es ideal para listas, tabs, menús y dropdowns. `withWrap(true)` hace que al llegar al final, pase al principio. `withTypeAhead(300)` permite escribir letras para saltar a un elemento (como en los selects nativos). Se integra con `@ViewChildren` y eventos `keydown`.

---

## 📢 Live Regions: Anunciar Cambios Dinámicos

```typescript
import { LiveAnnouncer } from '@angular/cdk/a11y';

@Component({ ... })
export class DataTableComponent {
  private announcer = inject(LiveAnnouncer);

  onDataLoaded(count: number) {
    this.announcer.announce(
      `Se han cargado ${count} registros`,
      'polite'  // Espera a que termine la frase actual
    );
  }

  onError(message: string) {
    this.announcer.announce(message, 'assertive'); // Interrumpe
  }
}
```

- `polite`: no interrumpe. Para cargas de datos, resultados de búsqueda
- `assertive`: interrumpe inmediatamente. Para errores críticos, timeouts
- Alternativa manual: `<div aria-live="polite" class="sr-only">...</div>`

Note:
LiveAnnouncer abstrae las live regions. Sin él, un usuario de lector de pantalla no se entera de que los datos se han cargado porque está enfocado en otra parte. `polite` para la mayoría de casos, `assertive` solo para alertas urgentes (errores de sesión, desconexión).

---

## 🔧 Angular ESLint para Accesibilidad

```json
// .eslintrc.json
{
  "overrides": [{
    "files": ["*.html"],
    "extends": ["plugin:@angular-eslint/template/recommended"],
    "rules": {
      "@angular-eslint/template/accessibility-alt-text": "error",
      "@angular-eslint/template/accessibility-label-has-associated-control": "error",
      "@angular-eslint/template/accessibility-valid-aria": "error",
      "@angular-eslint/template/accessibility-role-supports-aria-attr": "error",
      "@angular-eslint/template/accessibility-table-scope": "error",
      "@angular-eslint/template/no-positive-tabindex": "error"
    }
  }]
}
```

<mark>Configurar como error (no warning) → CI rechaza código inaccesible</mark>

Note:
Estas reglas detectan problemas en tiempo de compilación. `alt-text`: imágenes sin alt. `label-has-associated-control`: inputs sin label. `valid-aria`: atributos ARIA inválidos. `table-scope`: th sin scope. `no-positive-tabindex`: tabindex > 0 (mala práctica). Si las ponéis como error, el commit no pasa si introducís una regresión de accesibilidad.

---

## ✅ Checklist de Accesibilidad (10 Puntos)

1. **HTML válido**: pasar por W3C Validator
2. **Estructura H1-H6**: un solo `<h1>`, sin saltos de nivel
3. **Teclado completo**: todo operable con Tab, Enter, Escape, flechas
4. **Foco visible**: `focus-visible:ring-2` en todo elemento interactivo
5. **Textos alternativos**: `alt` en imágenes, `aria-label` en iconos SVG
6. **Contraste 4.5:1**: verificado con WebAIM para todas las combinaciones
7. **Formularios**: label + `aria-describedby` + `aria-invalid` + asterisco obligatorios
8. **Skip link**: `<a href="#main">Saltar al contenido</a>` al inicio
9. **Lighthouse 100**: corregir todas las incidencias detectables
10. **Lector de pantalla**: probar funcionalidad principal sin mirar la pantalla

Note:
Esta checklist debe aplicarse antes de dar por terminada cualquier funcionalidad. Los 4 primeros puntos son mecánicos (5 minutos). El punto 10 es el más revelador: cuando navegas sin ver, experimentas la aplicación como una persona ciega. Dedica 10 minutos al mes. Si algo no se anuncia correctamente, no es accesible.

---

## 📱 Accesibilidad en Angular: Skip Link y Título

```html
<!-- index.html: Skip link (primer elemento de la página) -->
<a href="#main-content"
   class="sr-only focus:not-sr-only focus:absolute focus:top-4
          focus:left-4 bg-primary-500 text-white px-4 py-2
          rounded-lg z-50">
  Saltar al contenido principal
</a>

<!-- app.component.html -->
<main id="main-content" tabindex="-1">
  <router-outlet />
</main>
```

```typescript
// En cada componente de página: actualizar título
import { Title } from '@angular/platform-browser';

@Component({ ... })
export class ClientesComponent {
  constructor() { inject(Title).setTitle('Clientes - GestionApp'); }
}
```

Note:
El skip link es el primer elemento focusable. Aparece al presionar Tab, permite saltar menús de navegación. `sr-only` lo oculta visualmente pero es accesible; `focus:not-sr-only` lo muestra al recibir foco. El `tabindex="-1"` en main permite enviar el foco programáticamente. El título de página (WCAG 2.4.2) se actualiza con el servicio Title de Angular.

---

## 🧪 Tests Automatizados con axe-core

```typescript
import axe from 'axe-core';

describe('LoginComponent accessibility', () => {
  it('should have no detectable accessibility violations', async () => {
    const fixture = TestBed.createComponent(LoginComponent);
    fixture.detectChanges();

    const results = await axe.run(fixture.nativeElement);

    // Violaciones vacías = todo OK
    expect(results.violations).toEqual([]);
  });

  it('should flag missing aria-label on icon button', async () => {
    const fixture = TestBed.createComponent(LoginComponent);
    // Eliminamos aria-label deliberadamente
    const btn = fixture.nativeElement.querySelector('[aria-label]');
    btn.removeAttribute('aria-label');
    fixture.detectChanges();

    const results = await axe.run(fixture.nativeElement);
    expect(results.violations.length).toBeGreaterThan(0);
  });
});
```

<mark>Integrable en CI/CD: cada PR verifica accesibilidad automáticamente</mark>

Note:
axe-core es la librería que usan Lighthouse y axe DevTools. Se instala con `npm i -D axe-core`. Podéis integrarlo en vuestros tests de Jasmine. El primer test verifica que el componente no tiene violaciones. El segundo es un test de regresión: si alguien quita un aria-label, el test falla. Esto en CI/CD evita regresiones de accesibilidad.

---

## 🎬 Demo: Auditoría de Accesibilidad

**Ejecutamos Lighthouse en una app Angular**

1. `ng serve` → abrimos Chrome DevTools (F12)
2. Pestaña **Lighthouse** → marcar solo Accessibility → Analyze
3. Revisar incidencias una por una
4. Corregir: `lang="es"`, contrastes, alt texts, labels
5. Repetir hasta puntuación 100

Luego: **WAVE** para chequeo visual y **Tab** para recorrido con teclado

Note:
Voy a hacer una demo en vivo. Primero Lighthouse: veo puntuación 73, detecta falta de lang, contraste bajo, enlaces no descriptivos. Corrijo en caliente: añado lang al index.html, cambio text-gray-500 por text-gray-800, reescribo "Ver más" como "Ver detalle del cliente". Segunda pasada: 96. Luego WAVE encuentra lo que Lighthouse no vio: tabla sin scope, select sin label. Los corrijo también. Finalmente, recorro con Tab: el menú dropdown no se abre con teclado → añado keyboard events.

---

## 🏋️ Actividad en Clase

**Hacer accesible un formulario de alta de cliente**

| ⏱️ Tiempo | 🎯 Objetivo | 📦 Entregable |
|-----------|-------------|---------------|
| 60 min | Añadir labels, errores con aria, foco visible, validación accesible | Formulario con Lighthouse 100 |

**Pasos**:
1. Añadir `<label>` asociados a cada input (5 min)
2. Marcar campos obligatorios con `*` y `required` (5 min)
3. Implementar errores con `aria-describedby` y `aria-invalid` (15 min)
4. Añadir resumen de errores al inicio con enlaces a campos (10 min)
5. Auditar con Lighthouse hasta 100 (15 min)
6. Probar con teclado y anotar barreras (10 min)

Note:
Recibiréis un formulario Angular con Tailwind que visualmente funciona pero es inaccesible: sin labels, sin aria, errores genéricos. Vuestro objetivo es hacerlo accesible siguiendo los 10 puntos de la checklist. Al final debéis tener Lighthouse 100 y poder completar el formulario solo con teclado.

---

## ✅ Buenas Prácticas

1. **HTML semántico primero**, ARIA solo cuando no hay alternativa nativa
2. **No eliminar el outline** sin reemplazo: `focus:outline-none focus-visible:ring-2`
3. **Verificar contraste** de cada combinación de colores con WebAIM
4. **Navegar sin ratón diariamente**: 30 segundos por funcionalidad
5. **`aria-live` para cambios asíncronos**: notificaciones, carga de datos
6. **ESLint de accesibilidad como errores**, no warnings

Note:
La práctica 4 es la más efectiva y la más fácil de adoptar. Antes de hacer commit, recorre tu componente con Tab. Si algo no se alcanza o no se activa, arréglalo. La práctica 6: configurad `@angular-eslint/template/accessibility-*` como error en vuestro eslintrc. Así el CI rechazará código inaccesible.

---

## ❌ Errores Frecuentes

| Error | Por qué ocurre | Solución |
|-------|---------------|----------|
| **`div role="button"`** | "El botón nativo no se estila" | `<button>` con Tailwind se estila perfectamente |
| **`outline: none` global** | "El borde de foco es feo" | `focus-visible:ring-2` estilizado |
| **Placeholder como label** | "Así ahorro espacio" | Label siempre visible + placeholder como ejemplo |
| **Solo color para estado** | "El borde rojo ya indica error" | Añadir icono, texto y `aria-invalid` |
| **Lighthouse 100 = accesible** | Falsa sensación de seguridad | Solo cubre ~40% de criterios WCAG |

Note:
El primero es el que más veo en código real. Los desarrolladores se crean divs con onclick porque "no saben estilar botones". Tailwind elimina esa excusa: `<button class="px-4 py-2 bg-primary-500...">` es un botón perfectamente estilado y 100% accesible. El último es peligroso: Lighthouse 100 no significa accesible, significa que pasa los checks automáticos. Falta prueba manual.

---

## 📊 Resumen

| Principio | Clave |
|-----------|-------|
| **Perceptible** | Alt text, contraste 4.5:1, estructura semántica |
| **Operable** | Todo con teclado, sin trampas de foco, skip link |
| **Comprensible** | Idioma declarado, labels, errores con sugerencias |
| **Robusto** | HTML válido, roles y estados expuestos |
| **ARIA** | Solo cuando HTML no llega. Menos es más |
| **Herramientas** | Lighthouse + WAVE + axe + prueba manual con lector |

Note:
Resumen POUR + ARIA + herramientas. La accesibilidad no se audita al final: se construye desde la primera línea de HTML. Cada vez que elegís un `<button>` sobre un `<div>`, estáis haciendo accesibilidad. Cada vez que añadís un `alt` a una imagen, estáis haciendo accesibilidad. No es un extra, es parte del oficio.

---

## 🚀 Próximos Pasos

**Unidad 13: Tailwind CSS 4 en Desarrollo de Interfaces**

- Filosofía utility-first vs CSS tradicional
- Configuración `@theme` con colores oklch
- Clases clave: layout, colores, tipografía, estados, responsive
- Técnicas avanzadas: valores arbitrarios, `peer`, `group`, `has-*`

**Para profundizar**:
- Instalar axe DevTools en vuestro navegador hoy mismo
- Probar NVDA (Windows) o VoiceOver (Mac) 10 minutos
- Leer: WAI-ARIA Authoring Practices Guide

Note:
La unidad 13 entra en Tailwind CSS 4, que usaremos para implementar componentes visualmente consistentes. Veremos cómo Tailwind ayuda con la accesibilidad (clases `sr-only`, `focus-visible:ring`, `aria-*`). Tarea: instalad axe DevTools ahora y auditad vuestra app. En 2 minutos veréis problemas que no sabíais que existían.
