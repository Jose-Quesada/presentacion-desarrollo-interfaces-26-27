# Desarrollo de Interfaces

## Unidad 3 · Diseño de Interfaces con Figma

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad de Figma. Esta herramienta ha sustituido a Sketch y Adobe XD como el estándar de la industria. Vais a aprender a diseñar interfaces, crear sistemas de diseño y preparar handoff para desarrollo. Todo lo que diseñéis aquí lo implementaréis en Angular + Tailwind en las Unidades 4 y 5. Pregunta: ¿quién ha usado ya Figma aunque sea para abrir un archivo?

---

## Objetivos de aprendizaje

<span class="fragment">1. Navegar con soltura por el espacio de trabajo de Figma (frames, capas, componentes)</span>

<span class="fragment">2. Diseñar layouts responsivos con <mark>Auto Layout</mark> (equivalente a Flexbox)</span>

<span class="fragment">3. Crear y gestionar <mark>variables de diseño</mark> con colecciones y modos (light/dark)</span>

<span class="fragment">4. Construir <mark>componentes reutilizables</mark> con propiedades y variantes</span>

<span class="fragment">5. Exportar assets y especificaciones para <mark>handoff</mark> a desarrollo</span>

<span class="fragment">6. Utilizar el <mark>Dev Mode</mark> para inspeccionar y extraer código CSS/Tailwind</span>

Note:
Seis objetivos prácticos. Figma no es solo para diseñadores: como desarrolladores de interfaces, necesitáis entender, inspeccionar e incluso crear diseños. El puente entre Figma y el código (Auto Layout → Flexbox, Variables → Design Tokens) es una de las competencias más valiosas de este módulo.

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

¿Por qué <mark>Figma</mark> destronó a Sketch y Adobe XD en menos de 5 años?

</div>

<span class="fragment" style="font-size: 1.1rem;">Es una <mark>aplicación web</mark>: funciona en cualquier SO, archivos en la nube</span>

<span class="fragment" style="font-size: 1.1rem;"><mark>Colaboración en tiempo real</mark>: varios diseñadores y desarrolladores editando a la vez</span>

<span class="fragment" style="font-size: 1.1rem;"><mark>Dev Mode</mark> integrado: medidas, colores, código CSS/Tailwind sin ser diseñador</span>

<span class="fragment" style="font-size: 1.1rem;">Plan gratuito con todas las funcionalidades necesarias para el curso</span>

Note:
Figma es la herramienta que usan los equipos profesionales. No es casualidad: resolvió problemas que Sketch y XD no podían por su arquitectura nativa. El hecho de ser web elimina fricción: sin instalación, sin archivos desincronizados, sin "¿qué versión tienes?". Pregunta: ¿qué sistema operativo usáis? (Figma funciona igual en todos)

---

## Espacio de trabajo de Figma

<div class="mermaid">
graph TB
  subgraph "Interfaz de Figma"
    A[Toolbar<br>Herramientas de creación] 
    B[Panel Izquierdo<br>Layers · Assets · Pages]
    C[Canvas<br>Área de trabajo infinita]
    D[Panel Derecho<br>Design · Prototype · Dev Mode]
  end
</div>

| Herramienta | Atajo | Equivalente Angular |
|---|---|---|
| **Move** | V | Seleccionar elemento |
| **Frame** | F | `<div>` contenedor |
| **Text** | T | `<p>`, `<h1>`-`<h6>` |
| **Rectangle** | R | `<div>` con bg |
| **Comment** | C | Code review |

Note:
La interfaz tiene 4 zonas. La toolbar (arriba) es como la barra de herramientas de VS Code pero para diseño. El panel izquierdo es el explorador de archivos + componentes. El canvas es infinito (como un mapa mental). El panel derecho son las propiedades. Pregunta: ¿a qué corresponde un Frame en HTML? (A un `<div>` — es el contenedor fundamental)

---

## Auto Layout: Flexbox en Figma

<span class="fragment">La funcionalidad <mark>más importante</mark> para desarrolladores</span>

<span class="fragment">Convierte un frame en un contenedor flexible (como `display: flex`)</span>

<span class="fragment">Los hijos se organizan automáticamente según dirección, gap, padding y alineación</span>

<span class="fragment">Al cambiar el contenido, el contenedor se <mark>redimensiona automáticamente</mark></span>

<span class="fragment">Atajo: <mark>Shift + A</mark> (aplicar/quitar Auto Layout)</span>

Note:
Auto Layout ES Flexbox. Cada propiedad tiene una equivalencia directa con CSS/Tailwind. Si entendéis Auto Layout, entendéis cómo piensa el diseñador y podréis traducir sus layouts a código casi sin esfuerzo. Pregunta: ¿qué pasa si no usas Auto Layout y posicionas con X/Y? (Es como maquetar con position: absolute — se rompe al cambiar el contenido)

---

## Auto Layout ↔ Tailwind: tabla de equivalencias

| Figma | CSS | Tailwind |
|---|---|---|
| Direction: Horizontal | `flex-direction: row` | `flex flex-row` |
| Direction: Vertical | `flex-direction: column` | `flex flex-col` |
| Gap | `gap: Npx` | `gap-{n}` |
| Padding | `padding: Npx` | `p-{n}`, `px-{n}`, `py-{n}` |
| Align Horizontal | `justify-content` | `justify-start/center/end/between` |
| Align Vertical | `align-items` | `items-start/center/end/stretch` |
| Hug contents | `width: fit-content` | `w-fit` |
| Fill container | `flex: 1` | `flex-1` |
| Wrap | `flex-wrap: wrap` | `flex-wrap` |

Note:
Esta tabla es vuestra chuleta de traducción Figma → Tailwind. Imprimidla o tenedla a mano. Cada vez que veáis Auto Layout en Figma, pensad en la clase Tailwind correspondiente. Con práctica, esta traducción se vuelve automática. Pregunta: si veis un frame con Auto Layout horizontal, gap 16px, padding 24px, ¿qué Tailwind usaríais? (flex gap-4 p-6)

---

## Auto Layout anidado: ejemplo Card

<div class="mermaid">
graph TB
  A["Card Frame<br>Auto Layout vertical<br>padding: 16px, gap: 12px"]
  A --> B["Header Frame<br>Auto Layout horizontal<br>gap: 8px, align: center"]
  B --> B1["Avatar<br>40x40, rounded-full"]
  B --> B2["Text Frame<br>Auto Layout vertical"]
  A --> C["Content Frame<br>Texto + Imagen"]
  A --> D["Actions Frame<br>Auto Layout horizontal<br>gap: 24px, space-between"]
</div>

<span class="fragment">Cada nivel de Auto Layout = un `<div>` con clases flex en Tailwind</span>

Note:
Este es el patrón mental que debéis desarrollar: ver Auto Layout anidado en Figma y visualizar inmediatamente el HTML + Tailwind. La Card exterior es `flex flex-col p-4 gap-3`. El Header es `flex items-center gap-2`. Las Actions son `flex justify-between gap-6`. Pregunta: ¿cuántos divs con flex necesitaríais para implementar esta card?

---

## Variables en Figma: Design Tokens nativos

<span class="fragment">Valores <mark>reutilizables y centralizados</mark> para colores, tamaños, tipografías</span>

<span class="fragment">Tipos: <mark>Color</mark>, Number, String, Boolean</span>

<span class="fragment"><mark>Colecciones</mark>: agrupación por categoría (Colors, Typography, Spacing)</span>

<span class="fragment"><mark>Modos</mark>: mismo nombre de variable, diferentes valores (Light/Dark)</span>

<span class="fragment">Vinculación: una variable puede <mark>referenciar</mark> a otra</span>

Note:
Las variables de Figma son exactamente lo mismo que las CSS Custom Properties y los Design Tokens. Introducidas en 2023, sustituyeron a los "Styles" antiguos que no soportaban modos ni vinculación. Pregunta: ¿qué ventaja tiene que `color-text-secondary` referencie a `slate-500`? (Si cambias slate-500, todo el texto secundario se actualiza automáticamente)

---

## Jerarquía de variables

<div class="mermaid">
graph TB
  A["Primitivas<br>slate-500: #64748B<br>blue-600: #2563EB"] --> B["Semánticas<br>color-text-secondary → slate-500<br>color-action-primary → blue-600"]
  B --> C["Componente<br>Button usa color-action-primary<br>Input usa color-text-secondary"]
</div>

<span class="fragment">Cambiar una primitiva → se actualizan TODAS las semánticas y componentes</span>

Note:
Esta jerarquía de 3 niveles es el estándar profesional. Las primitivas son la paleta cruda. Las semánticas añaden significado (esto es "texto secundario", no "gris medio"). Los componentes consumen semánticas. Si mañana el diseñador cambia el gris, solo toca una variable. Pregunta: ¿en qué nivel pondríais `color-bg-primary`? (Semánticas, porque tiene significado funcional)

---

## Componentes en Figma

<span class="fragment">Elemento reutilizable con un <mark>master</mark> (principal) e <mark>instancias</mark> (copias vinculadas)</span>

<span class="fragment">Crear: seleccionar elementos → <mark>Ctrl+Alt+K</mark></span>

<span class="fragment">Modificar el master → <mark>todas las instancias se actualizan</mark></span>

<span class="fragment">Equivalente a un <mark>componente Angular</mark> con inputs</span>

<span class="fragment">Propiedades: texto editable, boolean (mostrar/ocultar), instance swap</span>

Note:
Los componentes de Figma son el equivalente a los componentes de Angular. El master es la clase del componente. Las instancias son `<app-button variant="primary">` en el template. Las propiedades de Figma serán los @Input() en Angular. Pregunta: ¿qué pasa si haces "Detach instance"? (Rompes el vínculo con el master — debería evitarse en el flujo normal)

---

## Variantes (Component Sets)

<span class="fragment">Agrupación de componentes relacionados que comparten propiedades</span>

<span class="fragment">Crear: seleccionar variantes → <mark>"Combine as variants"</mark></span>

<span class="fragment">Ejemplo Button: <mark>Variant</mark> × <mark>Size</mark> × <mark>State</mark> × <mark>Icon</mark></span>

<span class="fragment">4 variantes × 3 tamaños × 5 estados × 2 iconos = <mark>120 combinaciones</mark></span>

<span class="fragment">En Angular: cada propiedad → un <mark>`@Input()`</mark> con tipos literales</span>

Note:
Los Component Sets modelan lo que en código llamamos "variantes". Un Button con variant (primary/secondary/outline/ghost), size (sm/md/lg), state (default/hover/focus/disabled/loading) e icon (none/left/right). En Angular, cada dimensión es un input tipado: `variant = input<'primary'|'secondary'|'outline'|'ghost'>('primary')`.

---

## Traducción Component Set → Angular

```typescript
@Component({
  selector: 'app-input',
  standalone: true,
})
export class InputComponent {
  readonly variant = input<'default' | 'filled' | 'outline'>('default');
  readonly size = input<'sm' | 'md' | 'lg'>('md');
  readonly state = input<'default' | 'error' | 'success'>('default');
  readonly disabled = input(false);
  readonly leadingIcon = input<string | undefined>(undefined);
}
```

<span class="fragment">Cada propiedad de variante en Figma → un <mark>input tipado</mark> en Angular</span>

Note:
Esta es la traducción directa. Las variantes visuales de Figma (colores, bordes, tamaños) se implementan con clases condicionales de Tailwind en el template, basadas en los inputs. Pregunta: ¿qué tipo usarías para `size`? (Un union type de strings literales: 'sm' | 'md' | 'lg')

---

## Design Tokens en Figma

<div class="mermaid">
graph LR
  A["Figma<br>Variables"] -->|"Design Tokens plugin"| B["JSON<br>tokens.json"]
  B -->|"Style Dictionary"| C["CSS<br>Custom Properties"]
  B -->|"Style Dictionary"| D["Tailwind<br>@theme"]
  B -->|"Style Dictionary"| E["TypeScript<br>const tokens"]
</div>

<span class="fragment">Los tokens de Figma se <mark>exportan y transforman</mark> a múltiples formatos</span>

Note:
Este es el flujo de automatización de design tokens. Las variables de Figma se exportan a JSON. Style Dictionary (de Amazon) las transforma a CSS, Tailwind @theme, TypeScript, SCSS, Android XML, iOS Swift... Un cambio en Figma → ejecutar script → todos los archivos de código actualizados. Pregunta: ¿qué ventaja tiene automatizar esto? (Evita errores humanos al copiar valores manualmente)

---

## Sistema de Diseño en Figma: estructura

```
📁 Design System
├── 📄 Cover (portada, versión, autores)
├── 📄 Foundations
│   ├── 🎨 Colors (paletas con variables)
│   ├── 🔤 Typography (escala con variables)
│   ├── 📏 Spacing (escala con variables)
│   └── 🔲 Radius & Shadows
├── 📄 Components (todos los componentes)
│   ├── 🔘 Buttons  ✏️ Inputs  ☑️ Checkboxes
│   ├── 🃏 Cards  🧭 Navigation  📊 Data Display
│   └── 🪟 Overlays  📄 Feedback
├── 📄 Patterns (combinaciones frecuentes)
├── 📄 Screens (pantallas completas)
└── 📄 Prototype (interactivo)
```

Note:
Esta es la estructura profesional de un sistema de diseño en Figma. Foundations contiene los cimientos (tokens). Components tiene todos los componentes con variantes. Patterns muestra combinaciones frecuentes. Screens son las pantallas reales. Prototype conecta las pantallas. Pregunta: ¿qué diferencia hay entre Components y Patterns? (Components son piezas individuales; Patterns son combinaciones de varias piezas)

---

## Dev Mode: la herramienta del desarrollador

<span class="fragment">Activar con <mark>Shift+D</mark></span>

<span class="fragment">Información que proporciona:</span>

<span class="fragment">• <mark>Medidas</mark> exactas (width, height, distancias entre elementos)</span>

<span class="fragment">• <mark>Código CSS/Tailwind</mark> generado automáticamente</span>

<span class="fragment">• <mark>Variables</mark> con nombres y valores actuales</span>

<span class="fragment">• <mark>Assets</mark> exportables (SVG, PNG, PDF) con selector de resolución</span>

Note:
El Dev Mode es vuestra herramienta principal como desarrolladores. Transforma Figma de "herramienta de diseño" a "especificación técnica interactiva". No copiéis el código generado ciegamente (a veces es incorrecto), pero usadlo como referencia. Pregunta: ¿qué atajo activa/desactiva Dev Mode? (Shift+D)

---

## Handoff: diseñador → desarrollador

**Responsabilidades del diseñador:**

<span class="fragment">✅ Nombrar capas <mark>semánticamente</mark> (nada de "Rectangle 47")</span>

<span class="fragment">✅ Diseñar <mark>todos los estados</mark>: hover, focus, disabled, error, loading, empty</span>

<span class="fragment">✅ Añadir <mark>anotaciones</mark> sobre comportamiento interactivo</span>

<span class="fragment">✅ Mantener Figma como <mark>fuente de verdad única</mark></span>

**Responsabilidades del desarrollador:**

<span class="fragment">✅ <mark>Revisar</mark> el diseño completo antes de programar</span>

<span class="fragment">✅ <mark>Preguntar</mark>, no asumir. <mark>Inspeccionar</mark>, no adivinar</span>

Note:
El handoff es el momento crítico. Una mala comunicación aquí genera retrabajo. El diseñador debe proporcionar todo lo necesario. El desarrollador debe revisar antes de empezar y preguntar dudas. Regla de oro: si no está en Figma, no existe. Pregunta: ¿qué pasa si el diseñador no incluye el estado de error de un formulario? (El desarrollador lo improvisa, y probablemente será inconsistente con otros formularios)

---

## Plugins esenciales para desarrolladores

| Plugin | Función |
|---|---|
| **Tailwind CSS** | Convertir selecciones a clases Tailwind |
| **Iconify** | 150+ sets de iconos SVG insertables |
| **Stark** | Auditoría de accesibilidad (contraste, daltonismo) |
| **Content Reel** | Contenido placeholder realista |
| **Unsplash** | Imágenes de stock gratuitas |
| **Design Tokens** | Exportar variables a JSON |
| **html.to.design** | Convertir páginas web a diseños Figma |

Note:
Estos plugins aceleran el flujo de trabajo. Iconify es imprescindible para iconos. Stark es obligatorio para verificar accesibilidad en tiempo de diseño (no después de implementar). Content Reel genera datos realistas, mucho mejor que "Lorem ipsum". Pregunta: ¿por qué es mejor detectar problemas de contraste en Figma que en código? (Corregir en Figma: 30 segundos. Corregir después de implementar 20 componentes: horas)

---

## Ejemplo guiado: Button Component Set

<span class="fragment">1. Crear variables de color (blue-600, slate-200, white, transparent)</span>

<span class="fragment">2. Diseñar Primary/Default con Auto Layout</span>

<span class="fragment">3. Duplicar para Secondary, Outline, Ghost</span>

<span class="fragment">4. Añadir variantes de hover, focus, disabled, loading</span>

<span class="fragment">5. Crear tamaños: Small, Medium, Large</span>

<span class="fragment">6. <mark>Combine as variants</mark> → Component Set completo</span>

Note:
Este es el "Hola Mundo" del diseño de componentes. Lo haremos juntos en el aula (50 min). El resultado: un component set profesional con 4 variantes × 3 tamaños × 5 estados = 60 combinaciones. Luego lo implementaremos en Angular + Tailwind en la Unidad 5. Pregunta: ¿qué atajo crea un componente? (Ctrl+Alt+K)

---

## Actividad en clase: Reproducir un componente existente

<span class="fragment">Se proporciona una captura de un componente real (Card de Spotify)</span>

<span class="fragment">1. Identificar jerarquía de frames y Auto Layouts</span>

<span class="fragment">2. Medir dimensiones (desde la captura o valores razonables)</span>

<span class="fragment">3. Identificar tipografías (pesos, tamaños, interlineados)</span>

<span class="fragment">4. Extraer colores con cuentagotas</span>

<span class="fragment">5. Nombrar TODAS las capas semánticamente</span>

**Duración:** 30 minutos

Note:
Actividad guiada para practicar la mecánica de Figma. El docente guía paso a paso. El objetivo no es la "belleza" sino la corrección técnica: Auto Layout correcto, capas nombradas, jerarquía clara. Pregunta: ¿por qué es importante nombrar las capas? (Para que el desarrollador entienda el diseño sin hacer ingeniería inversa)

---

## Actividad en clase: Crear variables y modos Light/Dark

<span class="fragment">1. Crear colección "Primitives/Colors"</span>

<span class="fragment">2. Rellenar paleta Slate (50-900) y Blue (50-900)</span>

<span class="fragment">3. Crear colección "Semantic/Colors" vinculando a primitivas</span>

<span class="fragment">4. Añadir modo Dark (invertir valores de Slate)</span>

<span class="fragment">5. Verificar: cambiar modo de un frame → colores se actualizan</span>

**Duración:** 25 minutos

Note:
Esta actividad enseña el sistema de variables. Lo más importante: la vinculación. Si semantic/color-bg-primary apunta a white en light y a slate-900 en dark, el cambio de modo actualiza automáticamente todos los elementos. Pregunta: ¿cómo verificáis que la vinculación es real y no una copia del valor? (Cambiad la primitiva y comprobad que la semántica también cambia)

---

## Demo: Diseñar pantalla de login

```html
<!-- Equivalencia en Tailwind de lo diseñado en Figma -->
<div class="min-h-screen flex items-center justify-center bg-bg-secondary">
  <div class="w-full max-w-md bg-bg-primary rounded-2xl shadow-lg p-8">
    <h1 class="text-2xl font-bold text-text-primary mb-2">Iniciar sesión</h1>
    <p class="text-sm text-text-secondary mb-6">Accede a tu cuenta</p>
    <!-- Email input, Password input, Button... -->
  </div>
</div>
```

<span class="fragment">Cada elemento del diseño Figma tiene su clase Tailwind equivalente</span>

Note:
Este es el puente entre Figma y código. Diseñamos la pantalla en Figma con Auto Layout, variables y componentes. Luego la implementamos con Tailwind usando la tabla de equivalencias. En 40 minutos tendremos una pantalla de login profesional con modo claro y oscuro.

---

## Figma ↔ Tailwind ↔ Angular: el puente completo

<div class="mermaid">
graph LR
  A["Figma<br>Color Fill"] -->|"bg-{color}-{shade}"| B["Tailwind<br>bg-primary"]
  C["Figma<br>Text"] -->|"text-*, font-*"| D["Tailwind<br>text-sm font-medium"]
  E["Figma<br>Auto Layout H"] -->|"flex flex-row"| F["Tailwind<br>flex gap-4"]
  G["Figma<br>Component"] -->|"standalone"| H["Angular<br>@Component"]
  I["Figma<br>Variant"] -->|"@Input()"| J["Angular<br>variant input()"]
</div>

Note:
Este diagrama resume la tesis central del módulo: todo lo que se diseña en Figma tiene una traducción directa a código. Color Fill → bg-primary. Auto Layout → flex. Component → @Component. Variant → @Input(). Pregunta: ¿qué es lo único que NO se traduce automáticamente? (El comportamiento interactivo: animaciones, tiempos, flujos — requiere comunicación explícita)

---

## Caso real: Design System de Shopify (Polaris)

<span class="fragment">Uno de los sistemas de diseño <mark>más completos</mark> y accesibles públicamente</span>

<span class="fragment">Organizado en Figma con múltiples archivos de librería</span>

<span class="fragment"><mark>Polaris Foundations:</mark> variables de color, tipografía, espaciado, iconografía</span>

<span class="fragment"><mark>Polaris Components:</mark> todos los componentes con variantes y estados</span>

<span class="fragment">Alineamiento riguroso <mark>Figma ↔ Storybook</mark>: cada variante en Figma = una story</span>

Note:
Shopify es un caso de estudio porque han compartido públicamente su proceso. Tienen un alignment estricto: si una variante existe en Figma pero no en Storybook, es un bug. Si existe en Storybook pero no en Figma, es deuda de diseño. Pregunta: ¿cuál es el beneficio de este alineamiento estricto? (Reduce drásticamente el tiempo de desarrollo: los equipos de producto no diseñan componentes, solo los ensamblan)

---

## Buenas prácticas

<span class="fragment">1. <mark>Auto Layout desde el primer frame</mark>. No diseñes sin él y lo añadas después</span>

<span class="fragment">2. <mark>Variables desde el principio</mark>. Añadirlas después es tedioso y propenso a errores</span>

<span class="fragment">3. <mark>Nombra las capas con significado</mark>. "Frame 47" no es un nombre</span>

<span class="fragment">4. <mark>Diseña todos los estados</mark>: ideal, vacío, error, carga, borde</span>

<span class="fragment">5. <mark>Comunica el comportamiento</mark>, no solo la apariencia</span>

<span class="fragment">6. <mark>Figma = fuente de verdad única</mark>. Si no está en Figma, no existe</span>

Note:
Estas 6 prácticas resumen la diferencia entre un diseñador amateur y uno profesional. La #4 es especialmente importante: si solo diseñas el "happy path", el desarrollador improvisará los demás estados y tendrás inconsistencia visual. Pregunta: ¿por qué es mala idea diseñar sin Auto Layout? (Es como maquetar una web con position: absolute para todo — se rompe al cambiar el contenido)

---

## Errores frecuentes

<span class="fragment">1. <mark>Posicionar con X/Y</mark> en lugar de usar Auto Layout</span>

<span class="fragment">2. Usar <mark>colores hardcodeados</mark> en lugar de variables</span>

<span class="fragment">3. Crear componentes <mark>demasiado específicos</mark> que no son reutilizables</span>

<span class="fragment">4. <mark>No diseñar estados</mark> (hover, focus, disabled, error, loading, empty)</span>

<span class="fragment">5. <mark>No nombrar capas</mark> y esperar que el desarrollador adivine</span>

<span class="fragment">6. Exportar <mark>PNG de iconos</mark> en lugar de SVG</span>

Note:
El error #1 es el más común entre principiantes: posicionar todo con X/Y absolutas. El #6 es un error técnico: PNG para iconos = pixelado en pantallas retina + archivos más pesados. Regla: vectorial = SVG, fotográfico = PNG/WebP. Pregunta: ¿qué problema causa el error #3? (Si cada pantalla necesita su propio componente, has fallado en la abstracción — los componentes base deben ser genéricos y configurables)

---

## Resumen de la unidad

<span class="fragment">✅ <mark>Auto Layout</mark>: Flexbox en Figma, traducción directa a Tailwind</span>

<span class="fragment">✅ <mark>Variables</mark>: Design Tokens con colecciones, modos y vinculación</span>

<span class="fragment">✅ <mark>Componentes y Variantes</mark>: master/instancias, component sets, propiedades</span>

<span class="fragment">✅ <mark>Dev Mode</mark>: inspección de medidas, colores, código CSS/Tailwind</span>

<span class="fragment">✅ <mark>Sistema de Diseño</mark>: foundations, componentes, patrones, pantallas</span>

<span class="fragment">✅ <mark>Handoff</mark>: proceso de entrega del diseño al desarrollo</span>

Note:
Resumen de las 6 competencias principales. En la Unidad 4 nos centraremos en layouts con Flexbox y Grid. En la Unidad 5 cerraremos el ciclo: tomaremos diseños de Figma y los implementaremos completamente en Angular + Tailwind + Storybook. Pregunta: ¿cuál de estas competencias os parece más útil como desarrolladores?

---

## Próximos pasos

<span class="fragment">📌 <mark>Actividad 1</mark>: Crear layouts con Auto Layout (navbar, card, lista)</span>

<span class="fragment">📌 <mark>Actividad 2</mark>: Crear component sets (Tabs, Toggle, Select)</span>

<span class="fragment">📌 <mark>Actividad 3</mark>: Sistema de Design Tokens completo con Light/Dark</span>

<span class="fragment">📌 <mark>Unidad 4</mark>: Layouts modernos con Flexbox y CSS Grid en Tailwind</span>

<span class="fragment">📌 <mark>Unidad 5</mark>: Del diseño Figma a la implementación Angular completa</span>

Note:
Tres actividades obligatorias para esta unidad. La Actividad 3 es la más importante: los tokens que defináis aquí serán los que uséis en la Unidad 5 para configurar Tailwind @theme. La Unidad 4 se centra en layouts (Flexbox y Grid con Tailwind). En la Unidad 5 juntaremos todo.

---

## ¿Preguntas?

<div style="font-size: 1.5rem; margin-top: 2rem;">

`Shift+A` · `Ctrl+Alt+K` · `Shift+D`

<br>

**Próxima sesión:** Unidad 4 · Layouts Modernos para Interfaces Web

</div>

Note:
Recordad los 3 atajos clave: Shift+A (Auto Layout), Ctrl+Alt+K (Componente), Shift+D (Dev Mode). Son los que más usaréis. La Unidad 4 aplica todo lo aprendido sobre layouts: pasaremos de diseñarlos en Figma a implementarlos con Tailwind.

---

## Referencias

- **Figma Learn:** https://help.figma.com/hc/en-us
- **Figma YouTube:** https://youtube.com/c/Figmadesign
- **Figma Best Practices:** https://www.figma.com/best-practices
- **Figma Community:** https://www.figma.com/community
- **Design Tokens W3C:** https://design-tokens.github.io/community-group/format
- **Style Dictionary:** https://amzn.github.io/style-dictionary
