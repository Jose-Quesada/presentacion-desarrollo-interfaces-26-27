# Desarrollo de Interfaces

## Unidad 2 · Ecosistema Profesional del Desarrollo Frontend

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
En esta unidad configuramos todo el toolchain profesional. Node.js, Angular, TypeScript, Tailwind CSS 4, Storybook, ESLint, Prettier y Git. No es teoría: al final de esta unidad cada alumno tendrá un entorno completo y funcional. Esta es la base sobre la que construiremos TODO el módulo. Pregunta inicial: ¿quién ha instalado ya Node.js en su equipo?

---

## Objetivos de aprendizaje

<span class="fragment">1. Instalar y configurar un entorno frontend profesional completo (Node.js, Angular CLI, Tailwind CSS 4, ESLint, Prettier, Storybook)</span>

<span class="fragment">2. Explicar el rol de cada herramienta en el flujo de trabajo profesional</span>

<span class="fragment">3. Crear componentes Angular con TypeScript (tipado estático, interfaces, genéricos, decoradores)</span>

<span class="fragment">4. Aplicar Tailwind CSS 4 para el estilado utility-first con `@theme`</span>

<span class="fragment">5. Documentar componentes visualmente con Storybook</span>

<span class="fragment">6. Utilizar Git y GitHub con conventional commits y PRs</span>

Note:
Son 6 objetivos ambiciosos. Los abordaremos progresivamente durante 2-3 sesiones. El objetivo 1 (configuración del entorno) lo haremos todos juntos en el aula. No paséis al objetivo 2 sin tener el 1 completado. Pregunta: ¿cuál de estos temas os genera más curiosidad?

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

¿Cómo logra <mark>Visual Studio Code</mark> funcionar exactamente igual en Windows, Linux y macOS?

</div>

<span class="fragment" style="font-size: 1.1rem;">
Está construido con tecnologías <mark>web</mark> (HTML, CSS, TypeScript) y empaquetado con <mark>Electron</mark>
</span>

<span class="fragment" style="font-size: 1.1rem;">
Lo mismo hacen <strong>Discord, Slack, Figma, Postman y Notion</strong>
</span>

<span class="fragment" style="font-size: 1.1rem;">
Todas usan el mismo stack que aprenderéis en este módulo
</span>

Note:
Este es el "por qué" del módulo. Las aplicaciones que usáis a diario están construidas con las herramientas que vamos a aprender. No son tecnologías académicas: son las herramientas de la industria. Pregunta: ¿qué otras apps de escritorio usáis que creéis que están hechas con Electron?

---

## El ecosistema en un vistazo

<div class="mermaid">
graph LR
  A[Node.js + npm] --> B[Angular CLI]
  A --> C[TypeScript]
  B --> D[Angular App]
  C --> D
  E[Tailwind CSS 4] --> D
  F[ESLint + Prettier] --> D
  G[Storybook] --> D
  H[Git + GitHub] --> D
  I[Electron] --> D
  J[Figma] -.-> D
</div>

Note:
Este diagrama muestra cómo todas las piezas encajan alrededor de Angular. Node.js es la base. TypeScript es el lenguaje. Tailwind CSS aporta los estilos. ESLint y Prettier aseguran calidad. Storybook documenta. Git versiona. Electron empaqueta. Figma alimenta el diseño. Pregunta: ¿qué pieza creéis que es prescindible? (Ninguna lo es en un entorno profesional)

---

## Bloque 1: Node.js y npm

<span class="fragment">Runtime de JavaScript <mark>fuera del navegador</mark> (motor V8 de Google)</span>

<span class="fragment"><mark>npm</mark>: mayor registro de paquetes del mundo (2M+)</span>

<span class="fragment"><code>package.json</code>: manifiesto del proyecto — dependencias, scripts, metadatos</span>

<span class="fragment"><mark>npx</mark>: ejecuta paquetes sin instalarlos globalmente</span>

<span class="fragment"><mark>SemVer</mark>: MAJOR.MINOR.PATCH — `^19.0.0` permite MINOR y PATCH</span>

Note:
Node.js es el punto de partida. Sin Node.js no hay Angular CLI, no hay TypeScript, no hay nada. npm gestiona todas las dependencias. package.json es el archivo más importante del proyecto (se versiona siempre). npx evita las instalaciones globales. SemVer es crítico para entender las actualizaciones. Ejemplo: `npm install express` vs `npm install -D typescript`.

---

## dependencies vs devDependencies

| Tipo | Cuándo se usa | Ejemplos |
|---|---|---|
| <mark>dependencies</mark> | Tiempo de ejecución | Angular, RxJS |
| <mark>devDependencies</mark> | Solo desarrollo/build | TypeScript, Tailwind, ESLint |

<span class="fragment">

```json
{
  "dependencies": { "@angular/core": "^19.0.0" },
  "devDependencies": { "typescript": "~5.5.0", "tailwindcss": "^4.0.0" }
}
```

</span>

<span class="fragment">Regla: ¿lo necesita el navegador del usuario? → dependency. Si no → devDependency</span>

Note:
Error frecuente: instalar todo como dependency. ESLint, Prettier, TypeScript NUNCA llegan al navegador. Van en devDependencies. Pregunta: ¿Angular es dependency o devDependency? (Es dependency: el código de Angular se ejecuta en el navegador del usuario.)

---

## Scripts npm

```json
{
  "scripts": {
    "start": "ng serve --open",
    "build": "ng build --configuration production",
    "test": "ng test",
    "lint": "ng lint",
    "format": "prettier --write \"src/**/*.{ts,html,css}\"",
    "storybook": "storybook dev -p 6006"
  }
}
```

<span class="fragment">Unifican la interfaz de comandos para todo el equipo</span>

<span class="fragment">`npm run <script>` ejecuta el comando definido</span>

Note:
Los scripts npm son la capa de abstracción sobre las herramientas. Un desarrollador nuevo en el proyecto no necesita saber cómo funciona Angular CLI internamente: ejecuta `npm start` y listo. Esto es especialmente útil en CI/CD.

---

## Bloque 2: Angular

<span class="fragment">Framework <mark>completo</mark> (batteries included) para aplicaciones empresariales</span>

<span class="fragment">Usa <mark>TypeScript</mark> como lenguaje nativo</span>

<span class="fragment">Arquitectura basada en <mark>componentes standalone</mark> (desde Angular 17)</span>

<span class="fragment"><mark>Angular CLI</mark>: scaffolding, generación, servidor de desarrollo, build</span>

<span class="fragment"><mark>Signals</mark>: nuevo sistema de reactividad fina (alternativa a Zone.js)</span>

Note:
Angular es el framework elegido por 3 razones: es completo (no necesitas elegir librerías para routing, HTTP, formularios), usa TypeScript nativo, y es muy demandado en el sector empresarial español. Con Angular 17+, los standalone components son el estándar. Signals es el presente y futuro de la reactividad en Angular.

---

## Angular CLI: comandos esenciales

| Comando | Propósito |
|---|---|
| `ng new` | Crear proyecto |
| `ng g component` | Generar componente |
| `ng g service` | Generar servicio |
| `ng serve` | Servidor desarrollo |
| `ng build` | Build producción |
| `ng test` | Tests unitarios |

<span class="fragment">`ng new mi-app --standalone --style css --routing true`</span>

Note:
Angular CLI es la navaja suiza de Angular. `ng generate` (abreviado `ng g`) tiene esquemas para component, service, directive, pipe, guard, interceptor, interface, enum. Cada uno coloca los archivos en el directorio correcto automáticamente.

---

## Componente Standalone

```typescript
import { Component, input } from '@angular/core';

@Component({
  selector: 'app-button',
  standalone: true,
  template: `
    <button class="bg-primary text-white px-4 py-2 rounded-lg">
      <ng-content />
    </button>
  `,
})
export class ButtonComponent {
  readonly variant = input<'primary' | 'secondary'>('primary');
  readonly disabled = input(false);
}
```

<span class="fragment">Sin NgModules. Importa solo lo que necesita. Menos boilerplate.</span>

Note:
Este es el patrón que usaremos durante todo el módulo. `standalone: true`, inputs tipados con la función `input<T>()`, `<ng-content />` para proyección de contenido. Pregunta: ¿qué ventaja tiene `input()` sobre `@Input()`? (Mejor inferencia de tipos y más consistente con Signals)

---

## Angular Signals: reactividad moderna

```typescript
import { signal, computed, effect } from '@angular/core';

const count = signal(0);
const double = computed(() => count() * 2);

effect(() => {
  console.log(`Count: ${count()}, Double: ${double()}`);
});

count.set(5);    // Establecer valor
count.update(n => n + 1);  // Actualizar basado en anterior
```

<span class="fragment">Solo se re-renderizan los componentes que <mark>realmente cambiaron</mark></span>

Note:
Signals es el cambio más importante en Angular desde su creación. Sustituye a Zone.js (que parcheaba todas las APIs asíncronas). Con Signals, la reactividad es explícita y fina. computed() deriva valores. effect() ejecuta efectos secundarios. Pregunta: ¿qué diferencia hay entre set() y update()? (set establece valor absoluto, update recibe el valor anterior)

---

## Bloque 3: TypeScript

<span class="fragment">Superset tipado de JavaScript (Microsoft, Anders Hejlsberg)</span>

<span class="fragment">Compilación `.ts` → `.js` mediante `tsc`</span>

<span class="fragment">El navegador <mark>nunca ejecuta TypeScript</mark> directamente</span>

<span class="fragment">Detección temprana de errores, autocompletado, refactorización segura</span>

Note:
TypeScript es JavaScript con superpoderes. Todo código JS válido es TS válido. Los tipos se eliminan en compilación (no hay overhead en runtime). La gran ventaja: el editor te dice que has metido un string donde se esperaba un número ANTES de que lo descubras en producción.

---

## Sistema de tipos de TypeScript

```typescript
// Primitivos
let name: string = "María";
let age: number = 25;
let active: boolean = true;

// Arrays y tuplas
let numbers: number[] = [1, 2, 3];
let tuple: [string, number] = ["hola", 42];

// Enums
enum TaskStatus {
  Pending = "PENDING",
  InProgress = "IN_PROGRESS",
  Done = "DONE"
}

// Nunca uses `any`. Usa `unknown` si es necesario.
```

Note:
`any` desactiva el tipado: es el botón nuclear. Si no sabes el tipo, usa `unknown` (obliga a type narrowing). Los enums son muy útiles para estados finitos. Pregunta: ¿por qué `unknown` es mejor que `any`? (Te obliga a verificar el tipo antes de usarlo)

---

## Interfaces y genéricos

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age?: number;
  readonly createdAt: Date;
}

function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

interface ApiResponse<T> {
  data: T;
  message: string;
}

type UserResponse = ApiResponse<User[]>;
```

<span class="fragment">Los genéricos <mark>preservan el tipado</mark> a través de las funciones</span>

Note:
Las interfaces son contratos. Definen la forma que debe tener un objeto. Los genéricos permiten escribir código reutilizable sin perder el tipado. `ApiResponse<T>` es un patrón muy común en APIs. Pregunta: ¿qué significa `age?`? (Propiedad opcional, puede ser undefined)

---

## Decoradores en Angular

```typescript
@Component({
  selector: 'app-button',
  standalone: true,
  template: `<button><ng-content /></button>`
})
class ButtonComponent {
  @Input() variant: 'primary' | 'secondary' = 'primary';
  @Output() clicked = new EventEmitter<void>();
}
```

<span class="fragment">`@Component`, `@Input`, `@Output`, `@Injectable` — los usamos constantemente</span>

<span class="fragment">Un decorador es una función que modifica una clase/método/propiedad</span>

Note:
Los decoradores son una característica experimental de TypeScript (Stage 3 TC39). Angular los usa intensivamente. Como desarrollador de interfaces, los usarás constantemente pero raramente escribirás decoradores propios. Pregunta: ¿qué hace `@Injectable()`? (Marca una clase como inyectable mediante el sistema de DI de Angular)

---

## Bloque 4: Tailwind CSS 4

<span class="fragment">Filosofía <mark>utility-first</mark>: clases atómicas en el HTML</span>

<span class="fragment">CSS tradicional → nombras clases. Tailwind → compones utilidades</span>

<span class="fragment">Novedades v4: configuración <mark>100% CSS</mark> con `@theme`</span>

<span class="fragment">Plugin Vite nativo: builds hasta <mark>10x más rápidos</mark></span>

<span class="fragment">Bundle CSS mínimo: solo las clases que usas (~5-10 KB gzip)</span>

Note:
Tailwind es un cambio de paradigma. En lugar de escribir CSS con nombres de clase semánticos, aplicas clases utilitarias directamente en el HTML. La versión 4 elimina tailwind.config.js: todo se configura en CSS. Pregunta: ¿cuál es la principal crítica a Tailwind? (Que "ensucia el HTML" — pero en la práctica, la productividad y consistencia lo compensan)

---

## Tailwind tradicional vs Tailwind 4

**Tailwind 3 (JS config):**
```js
// tailwind.config.js
module.exports = {
  theme: { extend: { colors: { primary: '#3b82f6' } } }
}
```

**Tailwind 4 (CSS config):**
```css
@import "tailwindcss";

@theme {
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --font-sans: 'Inter', sans-serif;
}
```

<span class="fragment">Configuración en un solo archivo, más cercana a los estándares web</span>

Note:
La gran ventaja de Tailwind 4: la configuración está donde debe estar (en el CSS). Esto facilita la integración con herramientas como Figma que exportan CSS. El archivo styles.css pasa a ser el centro de control del sistema de diseño.

---

## Clases utilitarias esenciales

| Categoría | Ejemplos |
|---|---|
| **Layout** | `flex`, `grid`, `flex-col`, `grid-cols-3` |
| **Spacing** | `p-4`, `m-2`, `gap-3`, `px-6` |
| **Color** | `bg-blue-500`, `text-white`, `border-gray-200` |
| **Typography** | `text-sm`, `font-bold`, `leading-relaxed` |
| **Effects** | `shadow-lg`, `rounded-xl`, `opacity-50` |
| **Responsive** | `sm:flex-col`, `md:grid-cols-2`, `lg:text-xl` |
| **States** | `hover:bg-blue-600`, `focus:ring-2`, `dark:bg-gray-900` |

Note:
No necesitáis memorizar todas las clases. La extensión Tailwind CSS IntelliSense para VS Code las autocompleta. Pero sí necesitáis entender el sistema: prefijo sm/md/lg para responsive, hover/focus/dark para estados. Pregunta: ¿qué significa `md:grid-cols-2`? (A partir de 768px, usa 2 columnas)

---

## @apply: usar con moderación

```css
/* ⚠️ Usar solo cuando no hay componente reutilizable */
.btn-primary {
  @apply bg-primary text-white font-semibold py-2 px-4
         rounded-lg hover:bg-primary-hover transition-colors;
}
```

<span class="fragment">La recomendación oficial: <mark>prefiere componentes sobre @apply</mark></span>

<span class="fragment">En Angular, cada componente encapsula sus estilos</span>

Note:
Adam Wathan (creador de Tailwind) recomienda usar @apply con moderación. En Angular, donde tenemos componentes como unidad natural de reutilización, casi nunca necesitamos @apply. Mantened las clases en el template. Pregunta: ¿cuándo SÍ usaríais @apply? (Contenido generado por CMS, markdown, o estilos base en @layer base)

---

## Bloque 5: Figma (avance)

<span class="fragment">Herramienta de diseño <mark>basada en navegador</mark></span>

<span class="fragment">Colaboración en <mark>tiempo real</mark> (tipo Google Docs)</span>

<span class="fragment"><mark>Dev Mode</mark>: inspección de medidas, colores, código CSS/Tailwind</span>

<span class="fragment">Plugins: Tailwind CSS, Iconify, Stark, Unsplash</span>

<span class="fragment"><mark>Handoff</mark> diseño → desarrollo eficiente</span>

Note:
Solo un avance de Figma. La Unidad 3 está dedicada íntegramente a Figma. Por ahora, basta con entender su rol en el ecosistema: es la fuente de verdad del diseño. Los desarrolladores inspeccionan diseños en Figma, no reciben PNGs por email. En la Unidad 3 aprenderéis a crear componentes, variables y sistemas de diseño completos.

---

## Bloque 6: Storybook

<span class="fragment">Entorno <mark>aislado</mark> para desarrollar y documentar componentes</span>

<span class="fragment">Cada componente tiene <mark>stories</mark>: variantes y estados documentados</span>

<span class="fragment">Addons: Controls, Actions, Docs, Viewport, Accessibility, Figma</span>

<span class="fragment"><mark>Catálogo vivo</mark> del sistema de diseño (nunca queda obsoleto)</span>

<span class="fragment">Flujo: implementar → documentar → validar con diseño → iterar</span>

Note:
Storybook resuelve el problema de "tengo que navegar 5 pantallas para ver cómo se ve este botón en disabled". Cada componente se desarrolla de forma aislada con todas sus variantes documentadas. El diseñador puede validar sin saber programar. Pregunta: ¿qué addon permite verificar automáticamente la accesibilidad de un componente? (@storybook/addon-a11y)

---

## Storybook: ejemplo de story

```typescript
const meta: Meta<ButtonComponent> = {
  title: 'UI/Button',
  component: ButtonComponent,
  tags: ['autodocs'],
};

export const Primary: Story = {
  args: { variant: 'primary', size: 'md', label: 'Button' },
};

export const Disabled: Story = {
  args: { variant: 'primary', size: 'md', disabled: true },
};
```

<span class="fragment">Cada story = un estado distinto del componente</span>

Note:
Las stories se escriben en archivos `*.stories.ts`. El panel Controls de Storybook convierte los args en controles interactivos (selectores, toggles, sliders). Un stakeholder no técnico puede "jugar" con el componente sin escribir código. Pregunta: ¿para qué sirve `tags: ['autodocs']`? (Genera documentación automática con tabla de props)

---

## Flujo de trabajo con Storybook

<div class="mermaid">
sequenceDiagram
  participant Dev as Developer
  participant SB as Storybook
  participant Dis as Designer
  Dev->>SB: Implementa componente + stories
  Dev->>SB: Ejecuta Storybook local
  Dev->>Dis: Comparte URL de Storybook
  Dis->>SB: Revisa componente vs Figma
  alt Coincide
    Dis->>Dev: ✅ Validado
  else No coincide
    Dis->>Dev: ❌ Ajustes necesarios
    Dev->>SB: Corrige y re-ejecuta
  end
</div>

Note:
Este flujo reduce los ciclos de feedback de días a minutos. Sin Storybook: desarrollador implementa → sube a staging (30 min) → diseñador revisa → enumera 15 discrepancias en un Excel → desarrollador corrige (2h) → vuelve a subir... Con Storybook: iteración instantánea.

---

## Bloque 7: Electron

<span class="fragment">Empaqueta aplicaciones <mark>web</mark> como apps de <mark>escritorio</mark> nativas</span>

<span class="fragment">Arquitectura: <mark>Main Process</mark> (Node.js) + <mark>Renderer Process</mark> (Chromium)</span>

<span class="fragment">Comunicación mediante <mark>IPC</mark> (Inter-Process Communication)</span>

<span class="fragment">Seguridad: `contextIsolation: true`, `nodeIntegration: false`</span>

<span class="fragment">Casos reales: <mark>VS Code, Discord, Slack, Figma, Postman</mark></span>

Note:
Electron = Chromium + Node.js empaquetados como app nativa. El proceso principal accede al sistema operativo (archivos, menús, notificaciones). El renderer muestra la UI web. NUNCA habilitéis nodeIntegration en el renderer: es un vector de ataque grave. Pregunta: ¿qué herramienta se usa para generar instaladores de apps Electron? (electron-builder)

---

## Cuándo usar Electron

| ✅ Usar Electron | ❌ No usar Electron |
|---|---|
| Apps multiplataforma de escritorio | Videojuegos AAA |
| Equipo domina tecnologías web | Edición de vídeo profesional |
| App de negocio/productividad | App que debe consumir <10 MB RAM |
| Tamaño 100-150 MB aceptable | Máximo rendimiento gráfico nativo |

Note:
Electron NO es para todo. Si necesitas renderizado 3D en tiempo real con trazado de rayos, Electron no es tu herramienta. Pero para el 90% de aplicaciones de negocio (dashboards, herramientas de gestión, clientes de chat), Electron es perfecto. Pregunta: ¿por qué Electron produce apps de 100-150 MB? (Incluye Chromium completo)

---

## Bloque 8: Herramientas de productividad

<span class="fragment"><mark>VS Code</mark> + extensiones: Angular Language Service, Tailwind IntelliSense, ESLint, Prettier, GitLens</span>

<span class="fragment"><mark>ESLint</mark>: análisis estático de código (errores, malas prácticas)</span>

<span class="fragment"><mark>Prettier</mark>: formateo automático de código (opinionated)</span>

<span class="fragment"><mark>Husky + lint-staged</mark>: pre-commit hooks (lint + format antes del commit)</span>

<span class="fragment"><mark>GitHub Actions</mark>: CI/CD (lint, test, build automáticos en cada push)</span>

Note:
Estas herramientas no son opcionales en un entorno profesional. Son la infraestructura de calidad. ESLint atrapa bugs antes de que lleguen a producción. Prettier elimina discusiones sobre formato. Husky evita que código sucio llegue al repo. Pregunta: ¿qué hace exactamente lint-staged? (Ejecuta linters solo en los archivos que están en staging, no en todo el proyecto)

---

## Conventional Commits

```
feat: añadir componente Button con variantes
fix: corregir overflow en sidebar
docs: actualizar README con instrucciones de instalación
refactor: extraer lógica de validación a servicio
style: formatear código con prettier
test: añadir tests para AuthService
chore: actualizar dependencias
```

<span class="fragment">Estructura: `<mark>&lt;tipo&gt;</mark>: &lt;descripción&gt;`</span>

<span class="fragment">Permite generación automática de changelogs y versionado semántico</span>

Note:
Los conventional commits son un estándar de la industria. El tipo (feat, fix, docs...) permite automatizar el versionado semántico. Un commit `feat:` incrementa MINOR. Un commit con `BREAKING CHANGE:` en el body incrementa MAJOR. Pregunta: ¿qué tipo usaríais para "corregir un typo en el README"? (docs:)

---

## Bloque 9: Configuración del entorno (paso a paso)

<span class="fragment">1. Instalar Node.js LTS con <mark>nvm</mark></span>
<span class="fragment">2. `npx @angular/cli new proyecto --standalone --style css`</span>
<span class="fragment">3. `npm install -D tailwindcss @tailwindcss/vite`</span>
<span class="fragment">4. Configurar `@theme` en `styles.css`</span>
<span class="fragment">5. Instalar ESLint + Prettier + crear scripts npm</span>
<span class="fragment">6. `npx storybook@latest init`</span>

Note:
Este es el checklist que seguiremos en el Ejemplo Guiado 1. 11 pasos, unos 45 minutos. Lo haremos todos juntos en el aula. Es crítico que TODO el alumnado termine con el entorno funcionando. Quien termine antes, ayuda a quien tenga problemas. Al final todos tendréis un proyecto con Angular + Tailwind + Storybook funcionando.

---

## Demo: Configuración de Tailwind 4 en Angular

```css
/* src/styles.css */
@import "tailwindcss";

@theme {
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --font-sans: 'Inter', ui-sans-serif, system-ui;
}

@layer base {
  body {
    @apply bg-gray-50 text-gray-900 antialiased;
  }
}
```

<span class="fragment">¡Sin `tailwind.config.js`! Todo en CSS nativo</span>

Note:
Este es el archivo styles.css completo para empezar. @import "tailwindcss" carga todo Tailwind. @theme define los tokens personalizados. @layer base define estilos globales. Verificamos que funciona creando un componente de prueba con clases Tailwind. Pregunta: ¿qué hace `antialiased`? (Suaviza las fuentes en pantalla)

---

## Actividad en clase: Configuración colaborativa del entorno

<span class="fragment">1. Verificar Node.js: `node --version` (>= 18.x LTS)</span>
<span class="fragment">2. Crear proyecto Angular standalone</span>
<span class="fragment">3. Instalar y configurar Tailwind CSS 4</span>
<span class="fragment">4. Verificar funcionamiento en `http://localhost:4200`</span>
<span class="fragment">5. Instalar ESLint + Prettier y configurar scripts</span>
<span class="fragment">6. Inicializar Git y primer commit: `feat: proyecto inicial`</span>

**Duración:** 60 minutos

Note:
Esta actividad es guiada paso a paso por el docente. Cada alumno sigue en su equipo. El docente verifica cada paso preguntando outputs específicos. Los compañeros que terminen antes ayudan a quienes encuentren problemas. Objetivo: 100% del alumnado con entorno funcional.

---

## Flujo completo: del diseño al código

<div class="mermaid">
graph LR
  A[Figma] -->|Dev Mode| B[Design Tokens]
  B -->|@theme| C[Tailwind CSS]
  C -->|clases| D[Angular Component]
  D -->|stories| E[Storybook]
  E -->|validación| A
  D -->|build| F[App desplegada]
  D -->|Electron| G[App escritorio]
</div>

Note:
Este es el flujo completo que cubre el módulo. Hoy configuramos las herramientas. En la Unidad 3 aprenderemos Figma. En la Unidad 4 dominaremos layouts. En la Unidad 5 recorreremos el flujo completo de principio a fin. Pregunta: ¿en qué fase del flujo estamos ahora mismo? (Fase 5: Configuración del proyecto)

---

## Caso real: Sistema de diseño del SAS

<span class="fragment">El <mark>Servicio Andaluz de Salud</mark> mantiene múltiples aplicaciones web</span>

<span class="fragment">ClicSalud+, app de cita previa, historia clínica digital, farmacia...</span>

<span class="fragment">Un médico usa 3+ apps diferentes en su jornada</span>

<span class="fragment">Necesitan <mark>consistencia visual</mark> entre todas las aplicaciones</span>

<span class="fragment">Stack que usarían: <mark>Figma + Angular + Tailwind + Storybook + Electron</mark></span>

Note:
Este caso ilustra por qué las herramientas que aprendemos no son fines en sí mismas. El SAS tiene un problema real: consistencia entre decenas de aplicaciones usadas por miles de sanitarios. Un sistema de diseño unificado con componentes compartidos resuelve este problema. Pregunta: ¿cómo ayuda Storybook en este contexto? (Catálogo único de componentes para todos los equipos de desarrollo)

---

## Caso real: Startup sevillana de logística

<span class="fragment">Algoritmo de optimización de rutas de reparto</span>

<span class="fragment">Dos interfaces: <mark>dashboard</mark> (escritorio) + <mark>app repartidor</mark> (móvil/PWA)</span>

<span class="fragment">Stack: Angular + Tailwind + PWA + Electron</span>

<span class="fragment">MVP en 3 meses con <mark>2 desarrolladores</mark></span>

<span class="fragment">Comparten modelos TypeScript y servicios entre ambas apps</span>

Note:
Este caso muestra la ventaja de Angular como framework único para web + escritorio + PWA. Una startup no puede permitirse 4 desarrolladores (2 iOS, 2 Android, 1 web, 1 backend). Con Angular + PWA, cubren escritorio y móvil con el mismo equipo. Electron añade funcionalidades nativas cuando son necesarias.

---

## Buenas prácticas

<span class="fragment">1. <mark>Versiona `package.json` y `package-lock.json`</mark>, NUNCA `node_modules`</span>

<span class="fragment">2. Usa <mark>`npx`</mark> en lugar de instalaciones globales</span>

<span class="fragment">3. Prefiere <mark>componentes standalone</mark> en Angular</span>

<span class="fragment">4. Prioriza <mark>`@theme`</mark> sobre `tailwind.config.js` en Tailwind 4</span>

<span class="fragment">5. No abuses de <mark>`@apply`</mark> — prefiere componentes Angular</span>

<span class="fragment">6. Automatiza calidad con <mark>pre-commit hooks</mark> (Husky + lint-staged)</span>

Note:
Estas 6 prácticas resumen las lecciones de miles de proyectos profesionales. Si solo os lleváis una cosa de esta unidad: no versionéis node_modules. Pregunta: ¿qué comando usarías en CI en lugar de `npm install`? (`npm ci` — instalación limpia y reproducible basada en package-lock.json)

---

## Errores frecuentes

<span class="fragment">1. Confundir <mark>dependencies</mark> y <mark>devDependencies</mark></span>

<span class="fragment">2. Usar `<mark>any</mark>` cuando TypeScript se queja</span>

<span class="fragment">3. <mark>Combatir Tailwind</mark> en lugar de darle una oportunidad</span>

<span class="fragment">4. No instalar las <mark>extensiones de VS Code</mark> (Angular, Tailwind)</span>

<span class="fragment">5. Configurar ESLint/Prettier <mark>a mitad del proyecto</mark></span>

<span class="fragment">6. Ignorar Storybook "porque ya veré el componente en la app"</span>

Note:
El error #1 es el más común: instalar TypeScript, ESLint y Prettier como dependencies en lugar de devDependencies. El error #2 (`any`) es un vicio difícil de quitar. El error #3 es psicológico: la resistencia inicial a Tailwind desaparece tras 2 semanas de uso. Pregunta: ¿por qué es mala idea añadir ESLint a mitad de proyecto? (Genera cientos de warnings que abruman y desmotivan)

---

## Resumen de la unidad

<span class="fragment">✅ <mark>Node.js + npm</mark>: base del ecosistema, gestión de dependencias</span>

<span class="fragment">✅ <mark>Angular</mark>: framework completo, standalone components, Signals</span>

<span class="fragment">✅ <mark>TypeScript</mark>: tipado estático, interfaces, genéricos, decoradores</span>

<span class="fragment">✅ <mark>Tailwind CSS 4</mark>: utility-first, @theme, plugin Vite nativo</span>

<span class="fragment">✅ <mark>Figma</mark>: diseño colaborativo, Dev Mode, source of truth</span>

<span class="fragment">✅ <mark>Storybook</mark>: catálogo vivo, desarrollo aislado, validación visual</span>

Note:
Resumen de las 6 tecnologías principales. Cada una resuelve una parte del problema. Juntas forman el stack profesional completo. ¿Alguna pregunta sobre alguna de ellas antes de pasar a la práctica?

---

## Siguientes pasos

<span class="fragment">📌 <mark>Práctica obligatoria</mark>: Actividad 1 — Configurar entorno completo (entrega con capturas y README)</span>

<span class="fragment">📌 <mark>Práctica obligatoria</mark>: Actividad 2 — Implementar Badge, Avatar e Icon con Storybook</span>

<span class="fragment">📌 <mark>Unidad 3</mark>: Figma en profundidad — diseño de interfaces con Auto Layout y variables</span>

<span class="fragment">📌 <mark>Unidad 3</mark>: Creación de un sistema de diseño completo en Figma</span>

<span class="fragment">📌 <mark>Recurso</mark>: angular.dev (documentación oficial renovada con tutoriales interactivos)</span>

Note:
Dos actividades obligatorias para esta unidad. La Actividad 1 es crítica: necesitáis el entorno funcionando para TODO el resto del módulo. La Actividad 2 es vuestra primera implementación de componentes con Angular + Tailwind + Storybook. En la Unidad 3 nos sumergimos en Figma para crear el diseño que luego implementaremos.

---

## ¿Preguntas?

<div style="font-size: 1.5rem; margin-top: 2rem;">

`npm start` · `ng serve` · `npm run storybook`

<br>

**Próxima sesión:** Unidad 3 · Diseño de Interfaces con Figma

</div>

Note:
Espacio para dudas y preguntas. Recordad: el entorno debe estar funcionando ANTES de la próxima sesión. Si tenéis problemas, escribid en el foro o pasad por tutoría. La Unidad 3 asume que tenéis Angular + Tailwind + Storybook operativos.

---

## Referencias

- **Angular Docs:** https://angular.dev
- **TypeScript Handbook:** https://www.typescriptlang.org/docs
- **Tailwind CSS v4:** https://tailwindcss.com/docs/v4
- **Storybook Angular:** https://storybook.js.org/docs/angular
- **Electron Docs:** https://www.electronjs.org/docs
- **Figma Developers:** https://www.figma.com/developers
