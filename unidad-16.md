# Unidad 16: Generación de Informes y Documentos

---

## Portada

### Módulo 0488 — Desarrollo de Interfaces
# Generación de Informes y Documentos

**PDF · CSV · Excel · pdfmake · jsPDF · PDF-LIB**

Curso DAM — Unidad 16

Note:
Bienvenidos a la unidad 16, donde aprenderemos a generar documentos profesionales desde aplicaciones Angular. Esta unidad es crucial porque toda aplicación empresarial necesita producir facturas, informes, certificados y reportes exportables. Veremos 3 librerías principales y las compararemos para elegir la más adecuada en cada caso.

---

## Objetivos de Aprendizaje

1. Comprender la necesidad de generar documentos desde apps empresariales
2. Diferenciar <mark>generación cliente vs servidor</mark> y sus criterios
3. Dominar <mark>PDFMake</mark> como herramienta principal de generación PDF
4. Conocer alternativas: <mark>jsPDF</mark> y <mark>PDF-LIB</mark>
5. Implementar exportación a <mark>CSV</mark> y <mark>Excel</mark> (SheetJS)
6. Diseñar una arquitectura de <mark>servicios en Angular</mark> con `inject()`

Note:
Al finalizar esta unidad, seréis capaces de generar facturas profesionales en PDF, exportar datos a CSV y Excel, y diseñar una arquitectura de servicios limpia y reutilizable. Veremos los 3 enfoques: cliente (PDFMake/jsPDF), servidor y PDF-LIB para manipular PDFs existentes.

---

## Motivación

- Toda app empresarial necesita <mark>documentos que salgan de la pantalla</mark>
- Una app de facturación sin PDF es <mark>prácticamente inútil</mark>
- Un ERP sin informes no aporta <mark>valor estratégico</mark>
- Los documentos deben ser: <mark>estructurados, reproducibles y portables</mark>

Note:
Pensad en cualquier aplicación real: un ecommerce envía facturas por email, un gestor académico expide certificados, un ERP genera informes de ventas. Sin la capacidad de generar documentos, estas aplicaciones no cumplen su función de negocio. Esta unidad cubre exactamente esa necesidad.

---

## Tipos de Documentos Empresariales

<div class="fragment">

- **Fiscales**: facturas, presupuestos, albaranes, tickets
- **RRHH**: nóminas, contratos, certificados, finiquitos
- **Académicos**: certificados, títulos, expedientes, actas
- **Informes**: ventas, inventario, financieros, marketing
- **Legales**: contratos, NDAs, instancias administrativas
- **Específicos**: etiquetas, carnets, diplomas, códigos QR

</div>

Note:
El abanico de documentos que una aplicación puede necesitar generar es enorme. Esto justifica dedicar una unidad entera a las técnicas y librerías de generación. Cada tipo de documento tiene requisitos específicos de formato, datos obligatorios y diseño.

---

## Flujo de Generación de un Documento

<div class="mermaid">
flowchart LR
    A[🧾 Obtención<br/>de datos] --> B[🔄 Transformación<br/>cálculos + formateo]
    B --> C[📐 Maquetación<br/>plantilla + datos]
    C --> D[🖨️ Renderizado<br/>PDF/CSV/Excel]
    D --> E[📥 Salida<br/>descarga/impresión/envío]
</div>

Note:
El flujo tiene 5 etapas. Primero obtenemos los datos de la API/BD. Luego los transformamos (cálculo de IVA, formateo de fechas). Después los insertamos en una plantilla que define la estructura visual. La librería renderiza los bytes del archivo. Finalmente se entrega al usuario. Este flujo aplica a cualquier formato de salida.

---

## Generación Cliente vs Servidor

<table>
<tr><th>Factor</th><th>Cliente (navegador)</th><th>Servidor</th></tr>
<tr><td>Rendimiento</td><td>Limitado por navegador</td><td>Alta potencia</td></tr>
<tr><td>Offline</td><td>✅ Sí (PWA)</td><td>❌ No</td></tr>
<tr><td>Seguridad</td><td>Datos en cliente</td><td>Datos en servidor</td></tr>
<tr><td>Concurrencia</td><td>Escala bien</td><td>Cuello de botella</td></tr>
<tr><td>Firma digital</td><td>❌ No</td><td>✅ Sí</td></tr>
<tr><td>PDF/A</td><td>❌ No</td><td>✅ Sí</td></tr>
</table>

Note:
La decisión arquitectónica depende de varios factores. En este módulo nos centramos en el enfoque cliente con Angular, pero debéis conocer ambos. Si el documento requiere firma digital o cumplimiento PDF/A, el enfoque servidor es obligatorio. Para dashboards y descargas bajo demanda, el cliente es ideal.

---

## Criterios de Decisión

<div class="fragment">

| Situación | Recomendación |
|---|---|
| < 50 páginas, alta concurrencia | <mark>Cliente</mark> (PDFMake) |
| > 100 páginas, procesamiento lote | <mark>Servidor</mark> |
| Datos confidenciales (bancarios, médicos) | <mark>Servidor</mark> |
| Funcionamiento offline necesario | <mark>Cliente</mark> |
| Previsualización interactiva | <mark>Cliente</mark> |
| Requiere firma digital | <mark>Servidor</mark> (obligatorio) |

</div>

Note:
Como regla general: documentos pequeños con muchos usuarios → cliente. Documentos grandes, confidenciales o con firma digital → servidor. En el módulo, todo lo haremos en cliente con Angular + PDFMake.

---

## Comparativa de Librerías JavaScript

| Característica | PDFMake | jsPDF + AutoTable | PDF-LIB |
|---|---|---|---|
| Paradigma | Declarativo | Imperativo | Imperativo |
| Facilidad | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| Tablas | Nativas | Plugin | Manual |
| PDFs existentes | ❌ | ❌ | ✅ Excelente |
| Paginación | Automática | AutoTable | Manual |
| Bundle | ~300 KB | ~250 KB | ~150 KB |
| Licencia | MIT | MIT | MIT |

Note:
Cada librería ocupa un nicho diferente. PDFMake es la más productiva para crear documentos desde cero con enfoque declarativo. jsPDF da control total de posicionamiento (x,y). PDF-LIB es para manipular PDFs existentes (rellenar formularios, fusionar documentos). Elegid la herramienta adecuada según el caso de uso.

---

## PDFMake — Instalación y Configuración

```bash
npm install pdfmake
```

```typescript
import pdfMake from 'pdfmake/build/pdfmake';
import pdfFonts from 'pdfmake/build/vfs_fonts';

// Configurar UNA SOLA VEZ en el constructor del servicio
@Injectable({ providedIn: 'root' })
export class PdfService {
  constructor() {
    pdfMake.vfs = pdfFonts.vfs;
  }
}
```

Note:
La instalación es trivial. Lo crítico es configurar `pdfMake.vfs` una sola vez. El lugar ideal es el constructor de un servicio `providedIn: 'root'`, que es singleton y se instancia una vez en todo el ciclo de vida de la app. Sin esto, PDFMake lanza errores de "fuente no encontrada".

---

## Estructura de un Documento PDFMake

```typescript
const docDefinition = {
  pageSize: 'A4',
  pageMargins: [40, 60, 40, 60],
  header: { text: 'Cabecera', style: 'header' },
  footer: (currentPage, pageCount) =>
    `Página ${currentPage} de ${pageCount}`,
  content: [ /* elementos */ ],
  styles: { /* estilos con nombre */ },
  images: { logo: 'data:image/png;base64,...' }
};
```

Note:
La definición del documento es un objeto JS con campos bien definidos. `content` es el más importante: un array de elementos (texto, tablas, imágenes, columnas). `styles` permite definir estilos con nombre reutilizables. `header` y `footer` pueden ser funciones para paginación dinámica.

---

## Elementos de Contenido en PDFMake

<div class="fragment">

1. <mark>`text`</mark> — Texto con estilos inline y fragmentos (spans)
2. <mark>`columns`</mark> — Layouts horizontales (datos emisor | datos receptor)
3. <mark>`table`</mark> — Tablas con headerRows, colSpan, rowSpan, layouts
4. <mark>`image`</mark> — Imágenes base64 (logo, firmas, gráficos)
5. <mark>`stack`</mark> — Agrupar elementos verticalmente con margen común
6. <mark>`qr`</mark> — Códigos QR para verificación de documentos

</div>

Note:
Estos 6 elementos cubren el 95% de las necesidades de documentos empresariales. Las tablas son el elemento más potente: soportan cabeceras repetidas, combinación de celdas, layouts predefinidos y personalizados, y manejo automático de saltos de página.

---

## Texto con Fragmentos (Inline Styling)

```typescript
{
  text: [
    'Texto normal, ',
    { text: 'negrita', bold: true },
    ', ',
    { text: 'rojo y grande', color: 'red', fontSize: 16 },
    '\n',
    { text: 'Nueva línea', italics: true }
  ]
}
```

Note:
Similar a los `<span>` en HTML. Permite mezclar estilos dentro de un mismo párrafo. Muy útil para resaltar partes de un texto sin necesidad de crear múltiples elementos separados. Los saltos de línea se controlan con `\n`.

---

## Columnas — Layout Horizontal

```typescript
{
  columns: [
    {
      width: '60%',
      text: 'Empresa S.L.\nCIF: B-12345678\nSevilla'
    },
    {
      width: '40%',
      stack: [
        { text: 'FACTURA', bold: true, fontSize: 18 },
        { text: 'N.º: F2024-0001' },
        { text: 'Fecha: 15/11/2024' }
      ],
      alignment: 'right'
    }
  ]
}
```

Note:
Las columnas son fundamentales para layouts de facturas y documentos empresariales. Los anchos se pueden definir en porcentajes, píxeles, `'*'` (auto) o `'auto'` (ajuste al contenido). Se pueden anidar columnas y stacks para layouts complejos.

---

## Tablas Avanzadas

```typescript
{
  table: {
    headerRows: 1,
    widths: ['*', 'auto', 80, 80, 80],
    body: [
      [{ text: 'Concepto', style: 'tableHeader' },
       { text: 'Cant.', style: 'tableHeader' },
       { text: 'Precio', style: 'tableHeader' },
       { text: 'IVA', style: 'tableHeader' },
       { text: 'Total', style: 'tableHeader' }],
      ['Desarrollo web', '1', '2.500 €', '21%', '2.500 €'],
      [{ text: 'TOTAL', colSpan: 4, bold: true },
       {}, {}, {}, { text: '2.500 €', bold: true }]
    ]
  }
}
```

Note:
`headerRows` indica cuántas filas son cabeceras (se repiten en cada página). `widths` admite combinación de formatos. `colSpan` fusiona celdas horizontalmente (las celdas fusionadas deben ser objetos vacíos). Los layouts predefinidos son: noBorders, headerLineOnly, lightHorizontalLines. También se pueden crear layouts personalizados.

---

## Layout de Tabla Personalizado (Efecto Cebra)

```typescript
layout: {
  hLineWidth: (i) => (i === 0 ? 1.5 : 0.5),
  vLineWidth: () => 0,
  hLineColor: () => '#e2e8f0',
  paddingLeft: () => 8, paddingRight: () => 8,
  paddingTop: () => 6, paddingBottom: () => 6,
  fillColor: (i) => i % 2 === 0 ? '#f7f7f7' : null
}
```

Note:
El layout personalizado da control total sobre cada aspecto visual de la tabla: grosor y color de líneas, padding, color de fondo alterno (efecto cebra). Las funciones reciben el índice de fila/columna y el nodo, permitiendo lógica condicional para estilos diferentes en cabecera, cuerpo y totales.

---

## Sistema de Estilos

```typescript
styles: {
  header: { fontSize: 22, bold: true, alignment: 'center' },
  companyName: { fontSize: 14, bold: true, color: '#1a365d' },
  tableHeader: {
    fontSize: 9, bold: true,
    fillColor: '#2d3748', color: '#ffffff'
  },
  totalAmount: {
    fontSize: 14, bold: true, color: '#2b6cb0',
    alignment: 'right'
  }
}
```

Note:
Los estilos con nombre son la clave para mantener la coherencia visual del documento. Se definen una vez en la sección `styles` y se aplican con `style: 'nombreEstilo'` en cualquier elemento. Propiedades disponibles: fontSize, bold, italics, color, alignment, margin, lineHeight, background, decoration.

---

## Métodos de Salida

| Método | Descripción |
|---|---|
| `.download('archivo.pdf')` | Descarga directa en el navegador |
| <mark>`.open()`</mark> | Abre en nueva pestaña (vista previa) |
| `.print()` | Diálogo de impresión del navegador |
| `.getDataUrl()` | Devuelve base64 (para previsualizar en iframe) |
| `.getBlob()` | Devuelve Blob (para enviar a servidor) |
| `.getBuffer()` | Devuelve ArrayBuffer |

Note:
`download()` es el más usado, pero `open()` es muy importante para ofrecer vista previa antes de descargar. `getBlob()` permite enviar el PDF generado a un servidor o adjuntarlo en un email. En apps empresariales, se recomienda ofrecer siempre los botones "Vista previa" y "Descargar".

---

## Código QR en PDFMake

```typescript
{
  qr: 'https://miapp.com/verificar/F2024-0001',
  fit: 80,
  alignment: 'right'
},
{
  text: 'Escanee para verificar esta factura',
  style: 'footerText', alignment: 'center'
}
```

Note:
PDFMake incluye soporte nativo para códigos QR. Muy útil para facturas electrónicas con enlace de verificación, certificados con código de autenticidad, o cualquier documento que requiera trazabilidad. El tamaño se controla con `fit` (en puntos).

---

## jsPDF — Enfoque Imperativo

```typescript
import jsPDF from 'jspdf';
import 'jspdf-autotable';

const doc = new jsPDF();
doc.setFontSize(22);
doc.text('FACTURA', 105, 20, { align: 'center' });

(doc as any).autoTable({
  startY: 40,
  head: [['Concepto', 'Cant.', 'Precio']],
  body: [['Desarrollo web', '1', '2.500 €']],
  theme: 'grid'
});

doc.save('factura.pdf');
```

Note:
jsPDF usa un modelo de "lienzo" con coordenadas (x, y) explícitas. Esto da control absoluto pero requiere más código. AutoTable mitiga la complejidad para tablas. Elegid jsPDF cuando necesitéis posicionamiento píxel-perfecto o dibujo vectorial (líneas, círculos, rectángulos) con su API de dibujo.

---

## PDF-LIB — Manipular PDFs Existentes

```typescript
import { PDFDocument } from 'pdf-lib';

async function fillForm(templateUrl: string, data: any) {
  const templateBytes = await fetch(templateUrl)
    .then(res => res.arrayBuffer());
  const pdfDoc = await PDFDocument.load(templateBytes);
  const form = pdfDoc.getForm();

  form.getTextField('nombre').setText(data.name);
  form.getTextField('fecha').setText(data.date);
  form.getCheckBox('aceptado').check();

  return await pdfDoc.save();
}
```

Note:
PDF-LIB no es para crear PDFs desde cero, sino para manipular PDFs existentes. Casos de uso: rellenar formularios oficiales (modelo 303 de la AEAT), fusionar varios PDFs en uno, añadir marcas de agua, firmas o anotaciones a documentos existentes. Es la herramienta adecuada cuando partís de plantillas PDF diseñadas por terceros.

---

## Arquitectura Recomendada en Angular

```
src/app/shared/services/
  pdf/
    pdf.service.ts              ← Servicio principal
    templates/
      invoice.template.ts       ← Función pura: datos → TDocumentDefinitions
      report.template.ts
      certificate.template.ts
    interfaces/
      document-generator.interface.ts
    helpers/
      format.helper.ts          ← Formateo fechas, monedas
      image.helper.ts           ← Conversión a base64
  csv/
    csv-export.service.ts
  excel/
    excel-export.service.ts
```

Note:
Esta estructura separa claramente responsabilidades. Las plantillas son funciones puras (fáciles de testear). Los helpers centralizan el formateo. La interfaz `DocumentGenerator<T>` estandariza la API de todos los generadores. Cada tipo de documento es una clase que implementa esta interfaz, facilitando la extensibilidad.

---

## Interfaz DocumentGenerator

```typescript
export interface DocumentGenerator<T> {
  generate(data: T, filename?: string): void;
  preview(data: T): void;
  getBlob(data: T): Promise<Blob>;
}
```

```typescript
@Injectable({ providedIn: 'root' })
export class InvoicePdfService
  implements DocumentGenerator<InvoiceData> {
  generate(data, filename?) { /* ... download */ }
  preview(data) { /* ... open in new tab */ }
  async getBlob(data) { /* ... return Blob */ }
}
```

Note:
La interfaz estandariza el contrato de cualquier generador de documentos. Esto permite cambiar de librería (pdfmake → jsPDF) sin afectar al resto de la aplicación. También facilita el testing: podemos mockear el generador y verificar que se llama con los datos correctos.

---

## Servicio PdfService con `inject()`

```typescript
@Injectable({ providedIn: 'root' })
export class PdfService {
  private formatHelper = inject(FormatHelper);
  private imageHelper = inject(ImageHelper);

  constructor() { pdfMake.vfs = pdfFonts.vfs; }

  generateInvoice(data: InvoiceData): void {
    const docDef = buildInvoiceTemplate(data,
      this.formatHelper, this.imageHelper);
    pdfMake.createPdf(docDef)
      .download(`Factura_${data.invoiceNumber}.pdf`);
  }

  previewInvoice(data: InvoiceData): void {
    pdfMake.createPdf(
      buildInvoiceTemplate(data,
      this.formatHelper, this.imageHelper)
    ).open();
  }
}
```

Note:
Usamos la nueva API `inject()` de Angular para inyectar dependencias en lugar del constructor tradicional. Las plantillas reciben los helpers como dependencias, no los instancian ellas mismas. Esto sigue el principio de inversión de dependencias (D de SOLID).

---

## Exportación CSV

```typescript
@Injectable({ providedIn: 'root' })
export class CsvExportService {
  exportToCsv<T>(data: T[], filename: string,
    columns?: { key: keyof T; label: string }[]): void {
    const keys = columns?.map(c => c.key) ?? Object.keys(data[0]);
    const headers = columns?.map(c => c.label) ?? keys;
    const csvRows = ['\uFEFF' + headers.join(';')];
    for (const row of data) {
      csvRows.push(keys.map(k => {
        let v = String(row[k] ?? '');
        if (v.includes(';')) v = `"${v.replace(/"/g, '""')}"`;
        return v;
      }).join(';'));
    }
    this.download(new Blob([csvRows.join('\n')],
      { type: 'text/csv;charset=utf-8;' }), `${filename}.csv`);
  }
}
```

Note:
Puntos clave: (1) BOM `\uFEFF` al inicio para que Excel reconozca UTF-8. (2) Usamos `;` como separador para compatibilidad con Excel en español. (3) Escapamos comillas duplicándolas. (4) El tipo MIME correcto es `text/csv;charset=utf-8;`. Sin BOM, las tildes y eñes se muestran como caracteres extraños en Excel.

---

## Exportación Excel con SheetJS

```bash
npm install xlsx
```

```typescript
import * as XLSX from 'xlsx';

const workbook = XLSX.utils.book_new();
const worksheet = XLSX.utils.json_to_sheet(datos);
worksheet['!cols'] = [{ width: 20 }, { width: 15 }];
XLSX.utils.book_append_sheet(workbook, worksheet, 'Clientes');
const buffer = XLSX.write(workbook,
  { bookType: 'xlsx', type: 'array' });
this.download(new Blob([buffer]), 'datos.xlsx');
```

Note:
SheetJS (librería `xlsx`) es el estándar para Excel en JS. `json_to_sheet()` convierte un array de objetos en una hoja de cálculo automáticamente. Los anchos de columna se configuran con `!cols`. La versión comunitaria tiene limitaciones en estilos avanzados, pero para exportación de datos tabulares es más que suficiente.

---

## Demo: Factura Profesional Completa

```typescript
// Datos de ejemplo → Servicio PdfService → PDF profesional
const invoice = {
  invoiceNumber: 'F2024-0042',
  issueDate: new Date('2024-11-15'),
  dueDate: new Date('2024-12-15'),
  company: {
    name: 'Soluciones Tech S.L.', taxId: 'B-90123456',
    address: 'Av. de la Innovación, 42', city: 'Sevilla',
    phone: '955 123 456', logoBase64: 'data:image/png;base64,...'
  },
  client: { name: 'Comercio Local S.A.', taxId: 'A-87654321' },
  lines: [
    { description: 'Desarrollo web', quantity: 1,
      unitPrice: 2500, taxRate: 21 },
    { description: 'Consultoría UX', quantity: 2,
      unitPrice: 750, taxRate: 21 }
  ],
  bankAccount: 'ES91 2100 1234 5612 3456 7890'
};
pdfService.generateInvoice(invoice);
// → Descarga: Factura_F2024-0042.pdf
```

Note:
Este es un ejemplo realista de los datos que alimentan una factura. El servicio calcula automáticamente: base imponible, IVA desglosado, total. La plantilla incluye: logo, datos empresa/cliente en columnas, tabla de líneas con anchos calculados, totales, QR de verificación y datos bancarios.

---

## Estructura de la Factura PDF

<div class="mermaid">
flowchart TD
    A[Logo + Datos Empresa] --> B[Datos Cliente | N.º Factura + Fechas]
    B --> C[Tabla de Líneas con Cabecera Estilizada]
    C --> D[Totales: Base + IVA + Total General]
    D --> E[Datos Bancarios + QR Verificación]
    E --> F[Pie: Gracias por su confianza]
</div>

Note:
La estructura sigue el estándar de factura profesional española. La cabecera se repite en cada página. El pie muestra paginación. El QR permite verificar la autenticidad. Las líneas de factura se paginan automáticamente si ocupan más de una página.

---

## Actividad en Clase

### Generación de Factura PDF

**⏱️ Tiempo**: 45 minutos  
**🎯 Objetivo**: Crear un servicio Angular que genere una factura PDF con PDFMake  
**📦 Entregable**: Componente con botón de descarga + PDF generado con datos de ejemplo

1. Instala pdfmake: `npm install pdfmake`
2. Crea `PdfService` con `providedIn: 'root'` y configura `pdfMake.vfs`
3. Implementa `generateInvoice()` con datos mock
4. Crea componente con botón "Descargar Factura"
5. Verifica que el PDF incluye: título, tabla con 3 líneas, total calculado

Note:
Esta actividad pone en práctica los conceptos fundamentales. El alumno debe configurar correctamente pdfmake, crear el servicio, y generar un documento con estructura mínima. Se evalúa que el PDF se descargue, contenga todos los elementos y esté correctamente tipado con TypeScript.

---

## Buenas Prácticas

1. <mark>Separar lógica de negocio de presentación</mark> — plantillas como funciones puras
2. <mark>Tipado estricto con TypeScript</mark> — interfaces para todos los datos, evitar `any`
3. <mark>Carga lazy de librerías pesadas</mark> — dynamic import para pdfmake/xlsx
4. <mark>Previsualización antes de descarga</mark> — método `open()` + botón "Vista previa"
5. <mark>Nombres de archivo significativos</mark> — `Factura_F2024-0001_20241115.pdf`
6. <mark>Formateo centralizado</mark> — `Intl.NumberFormat`/`Intl.DateTimeFormat` en helpers

Note:
Estas prácticas marcan la diferencia entre un código mantenible y uno que da problemas. La separación de responsabilidades permite testear las plantillas unitariamente. La carga lazy evita inflar el bundle inicial. Los nombres de archivo descriptivos mejoran la experiencia del usuario.

---

## Errores Frecuentes

1. <mark>Olvidar `pdfMake.vfs = pdfFonts.vfs`</mark> → error "fuente no encontrada"
2. <mark>Imágenes HTTP sin convertir a base64</mark> → no se renderizan
3. <mark>Tablas que desbordan la página</mark> → usar siempre una columna con `'*'`
4. <mark>CSV sin BOM (`\uFEFF`)</mark> → tildes y eñes ilegibles en Excel
5. <mark>No escapar comillas en CSV</mark> → columnas descuadradas
6. <mark>Márgenes de celda con `margin` en vez de `layout`</mark> → no funciona en tablas

Note:
Estos errores son muy comunes en proyectos reales. El BOM es especialmente traicionero porque en el editor de código el CSV se ve bien, pero al abrirlo en Excel los caracteres aparecen corruptos. Las imágenes siempre deben precargarse y convertirse a base64 antes de usarlas en PDFMake.

---

## Caso Real 1: Asesoría Fiscal

Una asesoría en Sevilla genera <mark>facturas automáticas</mark> al cerrar cada servicio. Cada factura incluye:
- Logo de la asesoría
- Datos fiscales del cliente
- Tabla con servicios prestados
- <mark>Desglose de IVA (21% y 10%)</mark> según tipo de servicio
- Código QR de verificación en sede electrónica
- Al final de mes: <mark>Excel con todas las facturas emitidas</mark>

Note:
Caso real que ilustra la integración de PDF + Excel. La separación entre lógica de negocio (cálculo de IVA variable) y presentación (diseño del PDF) fue clave. Se implementó `getTaxRate(serviceType)` que determina el tipo según el concepto del servicio.

---

## Caso Real 2: Academia de Formación

Una academia en Granada genera <mark>diplomas y certificados</mark> al completar cursos. Cada diploma:
- Diseño con colores corporativos y bordes decorativos
- Datos del alumno, curso, horas, fecha
- <mark>Firmas digitalizadas</mark> (PNG con fondo transparente en base64)
- <mark>Código QR único</mark> para verificación pública de autenticidad
- 3 variantes de diseño: básico, avanzado, profesional

Note:
Los certificados se implementaron como plantillas reutilizables. El diseño artístico (bordes, colores) está hardcodeado en la plantilla; los datos del alumno y curso son parámetros. Las firmas se almacenan como PNG con transparencia en base64. El QR usa la función nativa de PDFMake.

---

## Resumen

- <mark>PDFMake</mark>: herramienta principal, enfoque declarativo, ideal para facturas e informes
- <mark>jsPDF</mark>: alternativa con control de coordenadas, mejor para formularios oficiales
- <mark>PDF-LIB</mark>: manipulación de PDFs existentes (fusionar, rellenar formularios)
- <mark>CSV</mark>: BOM + punto y coma + escape de comillas
- <mark>Excel (SheetJS)</mark>: `json_to_sheet()` + `write()` + Blob
- <mark>Arquitectura</mark>: servicios con `inject()`, plantillas como funciones puras, interfaz `DocumentGenerator`

Note:
Recordad: elegid la herramienta según el caso de uso. Para el 80% de documentos empresariales, PDFMake es la opción correcta. La arquitectura de servicios con separación de responsabilidades es la clave para un código mantenible a largo plazo.

---

## Próximos Pasos

1. **Unidad 17**: Dashboards y Visualización de Datos con Chart.js
2. Integrar gráficos capturados como imágenes base64 en PDFs
3. Exportar dashboards completos a PDF (KPIs + gráficos + tablas)
4. Aplicar lo aprendido en el <mark>Proyecto Final "GesFlow"</mark>

**📚 Para profundizar**:
- PDFMake Playground: http://pdfmake.org/playground.html
- SheetJS Documentación: https://docs.sheetjs.com/
- jsPDF + AutoTable: https://github.com/simonbengtsson/jsPDF-AutoTable

Note:
En la unidad 17 conectaremos la generación de PDFs con los dashboards: capturaremos gráficos Chart.js como imágenes base64 y los incrustaremos en informes PDF. Todo esto se integrará en el proyecto final GesFlow, donde implementaréis facturación completa con PDF profesional. ¡Buena suerte!
