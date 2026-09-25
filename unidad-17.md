# Unidad 17: Dashboards y Visualización de Datos

---

## Portada

### Módulo 0488 — Desarrollo de Interfaces
# Dashboards y Visualización de Datos

**Chart.js · ApexCharts · KPI Cards · Tailwind Grid · Exportación**

Curso DAM — Unidad 17

Note:
En esta unidad aprenderemos a diseñar dashboards empresariales efectivos combinando Chart.js, Angular Signals y Tailwind CSS. Un dashboard no es solo gráficos bonitos: es una herramienta de toma de decisiones que debe comunicar información de un vistazo.

---

## Objetivos de Aprendizaje

1. Comprender el <mark>propósito y tipos de dashboards</mark> empresariales
2. Dominar <mark>Chart.js</mark>: tipos de gráficos, configuración, plugins
3. Integrar gráficos en Angular con <mark>ViewChild + ngAfterViewInit</mark>
4. Crear componentes reutilizables: <mark>ChartWidget con Signals</mark>
5. Diseñar layouts con <mark>CSS Grid + Tailwind responsive</mark>
6. Exportar dashboards a <mark>CSV, Excel y PDF</mark> con gráficos incrustados

Note:
Al finalizar esta unidad seréis capaces de construir dashboards completos con KPIs, gráficos interactivos, tablas de datos y exportación. Aplicaremos los principios de Tufte y Few para diseñar dashboards que comuniquen eficazmente.

---

## Motivación

- Un dashboard <mark>no es una colección de gráficos bonitos</mark>
- Es una <mark>herramienta de apoyo a la toma de decisiones</mark>
- Un directivo no lee 50 páginas de informe en PDF
- Necesita <mark>información de un vistazo</mark> para decidir en 5 minutos
- Diferencia clave: <mark>inmediatez y accionabilidad</mark>

Note:
La diferencia entre un dashboard y un informe tradicional es que el dashboard está diseñado para ser consultado en segundos, no en horas. Es una herramienta de monitorización continua, no un documento para leer. Esto cambia radicalmente cómo diseñamos la interfaz.

---

## Principios de Diseño — Edward Tufte

<div class="fragment">

1. <mark>Maximizar data-ink ratio</mark>: la mayor parte de los píxeles deben representar datos
2. <mark>Eliminar chartjunk</mark>: cero decoración superflua (3D, gradientes, sombras)
3. <mark>Mostrar los datos</mark>, no presumir de diseño
4. <mark>Información de un vistazo</mark>: legible en menos de 5 segundos
5. <mark>Jerarquía visual clara</mark>: lo importante, grande y arriba a la izquierda
6. <mark>Sin ruido ni distracciones</mark>: menos de 6 indicadores principales

</div>

Note:
Tufte es la referencia mundial en visualización de datos. Su principio más famoso: de toda la "tinta" usada en un gráfico, la mayoría debe representar datos, no decoración. Los gráficos 3D están prohibidos: distorsionan la percepción. El dashboard debe escanearse en forma de F o Z.

---

## Anatomía de un Dashboard

```
┌─────────────────────────────────────────────┐
│  FILTROS: [Rango ▼] [Categoría ▼]          │
├──────────┬──────────┬──────────┬────────────┤
│ KPI 1 ▲  │ KPI 2 ▲  │ KPI 3 ▼  │ KPI 4 ▲   │
├──────────┴──────────┴──────────┴────────────┤
│         GRÁFICO PRINCIPAL (Líneas)          │
│              [2/3 del ancho]                │
├───────────────────────┬─────────────────────┤
│  Secundario (Barras)  │  Doughnut           │
├───────────────────────┴─────────────────────┤
│           TABLA DE DATOS                    │
└─────────────────────────────────────────────┘
```

Note:
Esta anatomía funciona para el 80% de dashboards empresariales. Los KPIs arriba dan el contexto inmediato. El gráfico principal cuenta la historia. Los secundarios añaden detalle. La tabla permite inspeccionar datos en bruto. Los filtros arriba permiten segmentar toda la información.

---

## Tipos de Dashboards

| Tipo | Audiencia | Horizonte | Ejemplo |
|---|---|---|---|
| <mark>Operacional</mark> | Operadores, supervisores | Minutos/horas | Call center, servidores |
| <mark>Táctico</mark> | Mandos intermedios | Semanas/meses | Ventas por región, marketing |
| <mark>Estratégico</mark> | Alta dirección | Trimestres/años | Balanced Scorecard, OKRs |

Note:
Cada tipo tiene requisitos diferentes. Un dashboard operacional necesita datos en tiempo real y alertas. Uno táctico necesita comparativas y filtros. Uno estratégico necesita pocos KPIs muy cuidados y cero ruido. Adaptad el diseño al tipo de audiencia.

---

## Chart.js — Instalación y Registro

```bash
npm install chart.js
```

```typescript
import { Chart, BarController, LineController,
  DoughnutController, BarElement, LineElement,
  PointElement, ArcElement, CategoryScale,
  LinearScale, Tooltip, Legend, Title, Filler }
  from 'chart.js';

Chart.register(BarController, LineController,
  DoughnutController, BarElement, LineElement,
  PointElement, ArcElement, CategoryScale,
  LinearScale, Tooltip, Legend, Title, Filler);
```

Note:
El registro manual (tree shaking) reduce el bundle de ~250KB a ~100-150KB. Solo registramos los controladores, elementos y plugins que realmente usamos. Para prototipado rápido, se puede usar `import Chart from 'chart.js/auto'` que registra todo.

---

## Estructura de Configuración Chart.js

```typescript
const config = {
  type: 'line' as const,
  data: {
    labels: ['Ene', 'Feb', 'Mar', 'Abr', 'May'],
    datasets: [{
      label: 'Ingresos 2024', data: [12k, 19k, 15k, 22k, 28k],
      borderColor: '#3b82f6', backgroundColor: 'rgba(59,130,246,0.1)',
      tension: 0.3, fill: true
    }]
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { position: 'bottom' } },
    scales: { y: { beginAtZero: true } }
  }
};
```

Note:
La configuración tiene 3 bloques: `type` (tipo de gráfico), `data` (labels + datasets), `options` (visualización y comportamiento). Cada dataset es una serie independiente con sus propios colores y estilos. Esto permite gráficos comparativos (2024 vs 2023 superpuestos).

---

## Guía de Selección de Tipo de Gráfico

| Pregunta | Tipo | Ejemplo |
|---|---|---|
| ¿Cómo ha evolucionado X en el tiempo? | <mark>Líneas (line)</mark> | Ingresos mensuales |
| ¿Diferencia entre categorías? | <mark>Barras (bar)</mark> | Ventas por región |
| ¿Ranking de elementos? | <mark>Barras horiz.</mark> (`indexAxis:'y'`) | Top 10 productos |
| ¿Distribución del total? | <mark>Doughnut</mark> | Ingresos por canal |
| ¿Relación 2 variables? | <mark>Scatter</mark> | Inversión vs ingresos |
| ¿Perfil multivariable? | <mark>Radar</mark> | Evaluación 360° |

Note:
Regla de oro: evolución temporal → líneas. Comparación categorías → barras. Composición → doughnut. NUNCA uséis gráfico de tarta (pie), usad doughnut (anillo). Si tenéis más de 5 categorías, reconsiderad: un gráfico de barras probablemente sea más legible que un circular.

---

## Chart.js en Angular — Ciclo de Vida

```typescript
@Component({ /* ... */ })
export class ChartWidgetComponent
    implements OnInit, OnDestroy {
  @ViewChild('chartCanvas', { static: true })
  canvasRef!: ElementRef<HTMLCanvasElement>;
  chart: Chart | null = null;

  ngOnInit(): void { this.createChart(); }

  ngOnDestroy(): void {
    this.chart?.destroy(); // ¡SIEMPRE!
  }
}
```

Note:
Tres reglas: (1) `@ViewChild` con `{ static: true }` para acceder al canvas en `ngOnInit`. (2) Crear la instancia en `ngOnInit` o `ngAfterViewInit`. (3) `chart.destroy()` en `ngOnDestroy` — si no lo hacéis, cada navegación al dashboard crea una nueva instancia sin liberar la anterior → fuga de memoria.

---

## Componente ChartWidget Reutilizable

```typescript
@Component({
  selector: 'app-chart-widget', standalone: true,
  template: `<canvas #chartCanvas></canvas>`
})
export class ChartWidgetComponent implements OnInit,
    OnDestroy, OnChanges {
  @Input() type: ChartType = 'bar';
  @Input() labels: string[] = [];
  @Input() datasets: any[] = [];
  @Input() options: any = {};
  @Input() isLoading = false;

  chart: Chart | null = null;

  ngOnInit() { this.createChart(); }

  ngOnChanges(changes: SimpleChanges) {
    if (this.chart && (changes['labels'] ||
        changes['datasets'])) {
      this.chart.data.labels = this.labels;
      this.chart.data.datasets = this.datasets;
      this.chart.update('none');
    }
  }

  getBase64Image(): string | null {
    return this.chart?.toBase64Image() ?? null;
  }

  ngOnDestroy() { this.chart?.destroy(); }
}
```

Note:
Este componente encapsula toda la complejidad de Chart.js. El resto de la aplicación solo necesita pasarle `type`, `labels` y `datasets` como inputs. El método `getBase64Image()` es clave para exportar gráficos a PDF. `chart.update('none')` actualiza sin animación para cambios reactivos.

---

## Uso del ChartWidget en el Dashboard

```html
<app-chart-widget
  type="line"
  [labels]="revenueLabels()"
  [datasets]="revenueDatasets()"
  [options]="{ plugins: { legend:
    { position: 'bottom' } } }"
  [isLoading]="isLoading()"
  height="350px">
</app-chart-widget>
```

Note:
Uso declarativo desde el dashboard. Los datos vienen de Signals computadas que transforman los datos de la API al formato de Chart.js. El estado `isLoading` muestra un spinner mientras se cargan los datos. La altura se controla con CSS gracias a `maintainAspectRatio: false`.

---

## Integración con Signals

```typescript
salesData = signal<number[]>([100, 200, 150]);
salesLabels = signal<string[]>(['Ene', 'Feb', 'Mar']);

constructor() {
  effect(() => {
    const data = this.salesData();
    const labels = this.salesLabels();
    if (this.chart) {
      this.chart.data.labels = labels;
      this.chart.data.datasets[0].data = data;
      this.chart.update();
    }
  });
}
```

Note:
Los Signals + `effect()` permiten reactividad automática: cada vez que cambian los datos, el gráfico se actualiza solo. Esto es más limpio que `ngOnChanges` con múltiples inputs. El `effect` se ejecuta cuando cualquiera de las signals leídas dentro de él cambia.

---

## Componente StatCard (KPI)

```html
<div class="bg-white rounded-xl p-4 shadow-sm">
  <div class="flex items-center justify-between mb-2">
    <span class="text-sm text-gray-500">{{ label }}</span>
    <span class="w-8 h-8 text-blue-500">{{ icon }}</span>
  </div>
  <div class="text-2xl font-bold">{{ value }}</div>
  @if (trend !== undefined) {
    <div [class.text-green-600]="trend >= 0"
         [class.text-red-600]="trend < 0">
      {{ trend >= 0 ? '▲' : '▼' }} {{ abs(trend) }}%
      <span class="text-xs text-gray-400 ml-1">vs mes anterior</span>
    </div>
  }
</div>
```

Note:
Cada tarjeta KPI debe mostrar: etiqueta, valor (grande), tendencia con flecha y color (verde positivo, rojo negativo), y contexto (vs período anterior). La altura de todas las tarjetas debe ser idéntica para mantener alineación visual. Los skeleton loaders mejoran la percepción de velocidad durante la carga.

---

## Layout Dashboard con CSS Grid + Tailwind

```html
<!-- KPI Cards: 1 col móvil, 2 tablet, 4 desktop -->
<div class="grid grid-cols-1 sm:grid-cols-2
            xl:grid-cols-4 gap-4 mb-6">
  @for (kpi of kpiData(); track kpi.label) {
    <app-stat-card [label]="kpi.label"
      [value]="kpi.value" [trend]="kpi.trend" />
  }
</div>

<!-- Gráficos: principal 2 cols + secundario 1 col -->
<div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-6">
  <div class="lg:col-span-2 bg-white rounded-xl p-4">
    <h3>Evolución de Ingresos</h3>
    <app-chart-widget type="line" ... />
  </div>
  <div class="bg-white rounded-xl p-4">
    <h3>Por Categoría</h3>
    <app-chart-widget type="doughnut" ... />
  </div>
</div>
```

Note:
Tailwind ofrece clases responsive que se leen como: "grid de 1 columna por defecto, 2 columnas desde md (768px), y 4 columnas desde xl (1280px)". `col-span-2` hace que el gráfico principal ocupe 2 de las 3 columnas en desktop. En móvil, todo se apila verticalmente.

---

## Paleta de Colores para Gráficos

```typescript
const CHART_COLORS = {
  primary:    'rgba(37, 99, 235, 1)',   // Azul
  primaryLight: 'rgba(37, 99, 235, 0.1)',
  secondary:  'rgba(139, 92, 246, 1)',  // Púrpura
  tertiary:   'rgba(16, 185, 129, 1)',  // Verde
  quaternary: 'rgba(245, 158, 11, 1)',  // Ámbar
  quinary:    'rgba(239, 68, 68, 1)',   // Rojo
  gridLines:  'rgba(0, 0, 0, 0.05)'     // Cuadrícula
};
```

Note:
Definid una paleta corporativa centralizada. No uséis colores aleatorios en cada gráfico. Para gráficos de una sola serie, usad un solo color. Para accesibilidad: ~8% de hombres son daltónicos (rojo-verde). No dependáis solo del color para transmitir información. Usad también texturas, patrones o etiquetas.

---

## ApexCharts — La Alternativa Moderna

```bash
npm install apexcharts ng-apexcharts
```

```html
<apx-chart
  [series]="[{ name: 'Ventas', data: [113,120,95] }]"
  [chart]="{ type: 'bar', height: 350 }"
  [xaxis]="{ categories: ['Lun','Mar','Mié'] }"
  [colors]="['#3b82f6']">
</apx-chart>
```

Note:
ApexCharts ofrece ventajas sobre Chart.js: más tipos de gráficos (RadialBar, Heatmap, Treemap), animaciones más fluidas, mejor estética por defecto, modo oscuro nativo. La desventaja: bundle más pesado (~500KB vs ~250KB) y versión gratuita limitada en exportación nativa.

---

## RadialBar — Exclusivo de ApexCharts

```typescript
series: ApexNonAxisChartSeries = [76];
chart: ApexChart = { type: 'radialBar', height: 200 };
plotOptions: ApexPlotOptions = {
  radialBar: {
    startAngle: -135, endAngle: 135,
    dataLabels: {
      name: { show: true, fontSize: '14px' },
      value: { show: true, fontSize: '28px',
               fontWeight: 'bold' }
    }
  }
};
labels: string[] = ['Objetivo alcanzado'];
```

Note:
El RadialBar es perfecto para KPIs con formato de medidor/velocímetro. Muestra un porcentaje de forma visual muy impactante. Ideal para "Objetivo de ventas alcanzado", "Tasa de ocupación", "Nivel de satisfacción". Es uno de los gráficos más demandados en dashboards ejecutivos.

---

## Tabla Comparativa Chart.js vs ApexCharts

| Característica | Chart.js | ApexCharts |
|---|---|---|
| Bundle size | ~250 KB | ~500 KB |
| Tipos de gráficos | 8 | 15+ |
| Modo oscuro | Manual | <mark>Nativo</mark> |
| Animaciones | Buenas | <mark>Excelentes</mark> |
| RadialBar / Heatmap | ❌ | ✅ |
| Exportación nativa | ❌ | Solo versión de pago |
| Comunidad | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Curva aprendizaje | Baja | Media |

Note:
Para la mayoría de dashboards, Chart.js es suficiente y más ligero. Si necesitáis RadialBar, Heatmap o Treemap, id a ApexCharts. Si el tamaño del bundle es crítico (PWA, mobile-first), Chart.js con tree shaking es la mejor opción.

---

## DashboardLayoutComponent — Estado Reactivo

```typescript
@Component({ /* ... */ })
export class DashboardLayoutComponent {
  isLoading = signal(true);
  dateRange = signal<{ start: Date; end: Date }>({
    start: new Date(), end: new Date()
  });
  dashboardData = signal<any>(null);

  kpiData = computed(() => {
    const d = this.dashboardData();
    if (!d) return [];
    return [
      { label: 'Ingresos', value: formatEur(d.revenue),
        trend: d.revenueTrend, icon: '💰' },
      { label: 'Pedidos', value: String(d.orders),
        trend: d.ordersTrend, icon: '📦' },
      // ... más KPIs
    ];
  });

  revenueDatasets = computed(() => [{
    label: 'Ingresos', data: this.dashboardData()
      ?.monthlyRevenue?.map((m: any) => m.amount) ?? [],
    borderColor: '#3b82f6', tension: 0.3, fill: true
  }]);

  constructor() {
    effect(() => { this.loadDashboardData(); });
  }
}
```

Note:
El componente padre gestiona todo el estado con Signals. `computed()` transforma los datos de la API a los formatos que necesitan los componentes hijos (KPIs, gráficos, tabla). `effect()` recarga los datos automáticamente cuando cambia el rango de fechas. Los hijos son puramente presentacionales.

---

## Configuración de Ejes y Escalas

```typescript
options: {
  scales: {
    y: {
      beginAtZero: true,
      ticks: {
        callback: (value) =>
          `${value.toLocaleString('es-ES')} €`,
        font: { size: 11 }
      },
      grid: {
        color: 'rgba(0, 0, 0, 0.05)',
        drawBorder: false
      }
    },
    x: {
      grid: { display: false }
    }
  }
}
```

Note:
`beginAtZero: true` evita distorsionar magnitudes (un gráfico que empieza en 90 y llega a 100 exagera diferencias del 10% como si fueran del 100%). Los callbacks de ticks permiten formatear etiquetas con `Intl.NumberFormat`. Ocultar líneas de grid verticales da un diseño más limpio.

---

## Configuración de Plugins

```typescript
plugins: {
  legend: {
    position: 'bottom', align: 'center',
    labels: {
      usePointStyle: true,
      padding: 20,
      font: { size: 12 }
    }
  },
  tooltip: {
    mode: 'index', intersect: false,
    backgroundColor: 'rgba(0, 0, 0, 0.8)',
    cornerRadius: 6, padding: 12,
    callbacks: {
      label: (ctx) => ` ${ctx.dataset.label}:
        ${ctx.parsed.y.toLocaleString('es-ES')} €`
    }
  }
}
```

Note:
Los plugins controlan la experiencia interactiva. `mode: 'index'` muestra un tooltip para todas las series en la misma posición X (muy útil para gráficos comparativos). Los callbacks permiten formatear el contenido del tooltip con moneda, porcentajes, etc. `usePointStyle: true` usa círculos en la leyenda en vez de rectángulos.

---

## Modo Oscuro en Dashboards

```typescript
isDarkMode = signal(false);

darkModeOptions = computed(() => this.isDarkMode() ? {
  scales: {
    y: {
      ticks: { color: '#9ca3af' },
      grid: { color: 'rgba(255,255,255,0.1)' }
    },
    x: { ticks: { color: '#9ca3af' } }
  },
  plugins: {
    legend: { labels: { color: '#e5e7eb' } }
  }
} : {});
```

Note:
Chart.js no tiene modo oscuro nativo. Hay que cambiar manualmente colores de grid, labels y tooltips. Estrategia: usar `computed()` para generar opciones condicionales, y en un `effect()` actualizar `chart.options` y llamar a `chart.update()`. ApexCharts sí soporta `theme: { mode: 'dark' }` nativamente.

---

## Exportación de Gráficos a Imagen para PDF

```typescript
exportPdf(): void {
  const revenueImg = this.revenueChartWidget
    ?.getBase64Image('image/png', 1.0);
  const categoryImg = this.categoryChartWidget
    ?.getBase64Image('image/png', 1.0);

  const docDef = {
    pageSize: 'A4', pageOrientation: 'landscape',
    content: [
      { text: 'Informe Dashboard', style: 'header' },
      { text: `Período: ${formatDate(start)} -
        ${formatDate(end)}`, margin: [0, 0, 0, 20] },
      { table: { headerRows: 1,
          body: [['KPI', 'Valor', 'Tendencia'],
            ...kpiRows] } },
      revenueImg && { image: revenueImg,
        width: 500, margin: [0, 20] },
      categoryImg && { image: categoryImg,
        width: 350, margin: [0, 20] }
    ].filter(Boolean)
  };
  pdfMake.createPdf(docDef)
    .download(`dashboard-${formatDate(new Date())}.pdf`);
}
```

Note:
El método `toBase64Image()` de Chart.js devuelve el gráfico como PNG base64. Esto se puede incrustar directamente en un PDF con PDFMake. La calidad se controla con el segundo parámetro (0.0 a 1.0). Necesitamos referencias a los componentes ChartWidget para llamar a su método `getBase64Image()`.

---

## Estados del Dashboard

| Estado | Qué mostrar |
|---|---|
| <mark>Carga</mark> | Skeleton loaders para KPIs, gráficos y tabla |
| <mark>Vacío</mark> | Ilustración + "No hay datos para el período seleccionado" + botón "Cambiar filtros" |
| <mark>Error</mark> | Mensaje de error + botón "Reintentar" |
| <mark>Datos</mark> | Visualización normal con todos los widgets |
| <mark>Actualización</mark> | Indicador sutil "Última actualización hace X segundos" |

Note:
Cada widget debe manejar estos estados. Los skeleton loaders (Placeholder UI) mejoran la percepción de velocidad. El estado vacío nunca debe ser una pantalla en blanco: guiad al usuario con un mensaje claro y una acción. El estado de error debe ofrecer siempre un camino de recuperación.

---

## Demo: Dashboard Empresarial Completo

### Elementos del dashboard de ejemplo:

1. <mark>5 KPI Cards</mark>: Ingresos totales, pedidos, ticket medio, tasa cobro, clientes activos
2. <mark>Gráfico de líneas</mark>: Evolución ingresos 12 meses (2 series: 2024 vs 2023)
3. <mark>Barras horizontales</mark>: Top 5 productos por ingresos
4. <mark>Doughnut</mark>: Distribución ingresos por categoría
5. <mark>Tabla</mark>: Últimos 10 pedidos con estado (badge de color)
6. <mark>Filtro de fechas</mark>: 7d, 30d, 90d, 1 año, personalizado

Note:
Este dashboard integra todos los conceptos de la unidad. Los datos son mock pero la estructura es profesional. Los botones de exportación permiten descargar CSV y PDF con los gráficos incrustados. El filtro de fechas recarga todos los widgets automáticamente.

---

## Actividad en Clase

### Construir un Dashboard con Chart.js + Tailwind

**⏱️ Tiempo**: 60 minutos  
**🎯 Objetivo**: Crear un dashboard con 4 KPIs, 2 gráficos y una tabla de datos  
**📦 Entregable**: Componente `DashboardPage` funcional con datos mock

1. Instala Chart.js y crea el componente `ChartWidget` reutilizable
2. Crea el componente `StatCard` para KPIs (label, value, trend)
3. Implementa `DashboardLayout` con CSS Grid responsive
4. Añade gráfico de líneas (ingresos) y doughnut (categorías)
5. Implementa botones de exportación CSV y PDF

Note:
Esta actividad integra Chart.js, Tailwind Grid, Signals y componentes reutilizables. El alumno debe demostrar que sabe crear un componente ChartWidget genérico, tarjetas KPI con tendencia, y un layout responsive. La exportación a PDF requiere capturar los gráficos con `toBase64Image()`.

---

## Buenas Prácticas

1. <mark>Elegid el gráfico correcto</mark> para cada tipo de dato y pregunta
2. <mark>Empezad el eje Y desde cero</mark> en gráficos de barras (no distorsionar)
3. <mark>Máximo 5-7 colores</mark> en gráficos circulares; agrupad el resto en "Otros"
4. <mark>Contexto siempre en KPIs</mark>: comparación vs período anterior, objetivo o benchmark
5. <mark>No saturéis el dashboard</mark>: 4-6 KPIs + 2-3 gráficos + 1 tabla
6. <mark>Destruid siempre Chart.js</mark>: `chart.destroy()` en `ngOnDestroy()`

Note:
La regla de oro: si tenéis más de 5 categorías en un gráfico circular, reconsiderad. Un gráfico de barras será más legible. Las tarjetas KPI sin contexto (solo un número) no informan. La diferencia entre un dashboard profesional y uno amateur está en estos detalles.

---

## Errores Frecuentes

1. <mark>Crear Chart en ngOnInit sin { static: true }</mark> → canvas no existe aún
2. <mark>No llamar a chart.destroy()</mark> → fuga de memoria, rendimiento degradado
3. <mark>Modificar datos sin chart.update()</mark> → el gráfico no se redibuja
4. <mark>ngOnChanges no detecta objetos anidados</mark> → usar Signals con effect()
5. <mark>Gráficos inaccesibles</mark> → añadir tabla de datos alternativa (sr-only)
6. <mark>No formatear números en tooltips</mark> → `1234567.89` en vez de `1.234.567,89 €`

Note:
El error más grave es no destruir las instancias de Chart.js. Si navegáis al dashboard 10 veces, tendréis 10 instancias consumiendo memoria. El error de `ngOnChanges` con objetos anidados es sutil: solo detecta cambios de referencia, no de propiedades internas. Los Signals resuelven esto.

---

## Resumen

- <mark>Chart.js</mark>: 8 tipos de gráficos, integración vía ViewChild, tree shaking
- <mark>ApexCharts</mark>: alternativa con RadialBar, Heatmap, modo oscuro nativo
- <mark>ChartWidget</mark>: componente reutilizable que encapsula ciclo de vida Chart.js
- <mark>Signals + effect()</mark>: reactividad automática para actualizar gráficos
- <mark>Tailwind Grid</mark>: `grid-cols-1 md:grid-cols-2 xl:grid-cols-4` responsive
- <mark>Exportación</mark>: `toBase64Image()` → imagen PNG → PDFMake

Note:
Recordad: el dashboard es una herramienta de toma de decisiones, no un ejercicio de diseño gráfico. Priorizad la claridad sobre la estética. Usad el tipo de gráfico correcto para cada dato. Y siempre, siempre, destruid las instancias de Chart.js.

---

## Próximos Pasos

1. **Unidad 18**: Documentación de Aplicaciones
2. **Unidad 19**: Pruebas de Software
3. **Unidad 20**: Aplicaciones de Escritorio con Electron — integrar el dashboard en una app nativa de escritorio
4. **Unidad 22**: Proyecto Final "GesFlow" — implementaréis un dashboard completo con KPIs, gráficos y exportaciones

**📚 Para profundizar**:
- Chart.js Docs: https://www.chartjs.org/docs/
- ApexCharts Angular: https://apexcharts.com/docs/angular-charts/
- ColorBrewer2 (paletas accesibles): https://colorbrewer2.org/
- Libro: "Information Dashboard Design" — Stephen Few

Note:
Tras documentación (U18) y pruebas (U19), en la unidad 20 daremos el salto al escritorio con Electron. El dashboard que habéis construido se ejecutará como ventana nativa con menús, diálogos y notificaciones. En el proyecto final GesFlow (U22), el dashboard será una de las 7 funcionalidades principales que implementaréis.

---

## Anexo: Configuración Completa de Animaciones

```typescript
animation: {
  duration: 750,
  easing: 'easeOutQuart',
  animateRotate: true,
  animateScale: true
},
transitions: {
  show: { animations: {
    x: { from: 0 }, y: { from: 0 }
  }},
  hide: { animations: {
    x: { to: 0 }, y: { to: 0 }
  }}
}
```

Note:
Las animaciones mejoran la experiencia pero no deben ralentizar la percepción. `duration: 750` ms es un buen equilibrio. Para dashboards que se actualizan frecuentemente (operacionales), considerad `duration: 0` para actualizaciones instantáneas. Las transiciones controlan cómo aparecen/desaparecen los datasets al filtrar.
