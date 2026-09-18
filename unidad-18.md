# Desarrollo de Interfaces

## Unidad 18 · Documentación de Aplicaciones

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad de documentación. Un software que no se documenta, no se mantiene ni se adopta. Vamos a ver qué documento le toca a cada persona (usuario, administrador, desarrollador), cómo generar ayuda sensible al contexto y cómo documentar la información persistente y el código. Todo tratado «como código»: en el repositorio, revisado y publicado desde el pipeline.

---

## Objetivos de aprendizaje

<span class="fragment">1. Identificar los <mark>tipos de documentación</mark> y su destinatario</span>

<span class="fragment">2. Seleccionar <mark>herramientas específicas</mark> para generar cada tipo (HTML, PDF, Markdown)</span>

<span class="fragment">3. Documentar la <mark>estructura de la información persistente</mark> (modelo de datos, contratos de API)</span>

<span class="fragment">4. Producir el <mark>conjunto documental mínimo</mark> de una aplicación del módulo</span>

Note:
Cuatro objetivos que van de lo conceptual (qué documento para quién) a lo práctico (generar el paquete documental de vuestra aplicación). El tercero es clave y a menudo olvidado: documentar los datos, no solo la interfaz.

---

## Alineación con el currículo

<div style="font-size: 1.3rem; text-align: left;">

Esta unidad trabaja el <mark>RA 6</mark> del módulo 0488 (RD 405/2023):
</div>

<span class="fragment" style="font-size: 1.15rem;"><mark>«Documenta aplicaciones seleccionando y utilizando herramientas específicas»</mark></span>

<span class="fragment" style="font-size: 1.05rem;">CE: sistemas de generación de ayudas · formatos habituales · ayuda sensible al contexto · estructura de la información persistente · manual de usuario y guía de referencia · manuales de instalación / configuración / administración · tutoriales</span>

Note:
El RA 6 es amplio: cubre desde la ayuda in-app hasta los manuales de administración. Aquí lo aterrizamos a una aplicación real del módulo (Angular + Tailwind + Electron).

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

Documentar no es escribir más, es poner la <mark>información adecuada</mark> ante la <mark>persona adecuada</mark> en el <mark>momento adecuado</mark>.
</div>

<span class="fragment" style="font-size: 1.1rem;">El administrador necesita configuración; el usuario nuevo, una guía rápida; quien integra la API, el contrato</span>

Note:
El error clásico es un único «manual» gigante que nadie lee. La primera decisión es definir la matriz documento → destinatario → formato.

---

## Tipos de documentación y destinatarios

| Documento | Destinatario | Formato habitual |
|---|---|---|
| **Guía rápida / tutorial** | Usuario nuevo | HTML / PDF, orientado a tareas |
| **Manual de usuario** | Usuario | HTML / PDF |
| **Guía de referencia** | Usuario avanzado | HTML (con búsqueda) |
| **Ayuda sensible al contexto** | Usuario, in situ | In-app (tooltips, paneles) |
| **Instalación / configuración / administración** | Administrador · DevOps | Markdown / HTML en repositorio |
| **Contrato de API / diccionario de datos** | Desarrollador | OpenAPI, diagramas E/R |

Note:
Esta matriz es el corazón de la unidad. Cada fila es un documento con su público y su formato. No se trata de hacerlos todos siempre, sino de elegir los que aporta vuestro proyecto.

---

## Ayuda sensible al contexto

<div style="font-size: 1.2rem; text-align: left;">

Aparece <mark>donde el usuario la necesita</mark>: un icono junto a cada campo que despliega una explicación específica
</div>

```typescript
@Directive({ selector: '[appHelp]' })
export class HelpDirective {
  @Input() appHelp = '';
  @HostListener('mouseenter') show() { /* tooltip con this.appHelp */ }
  @HostListener('mouseleave') hide() { /* oculta */ }
}
```

Note:
La ayuda contextual es la más valiosa porque interrumpe menos. En Angular se implementa con una directiva reutilizable que muestra un tooltip con el texto específico de cada elemento.

---

## Sistemas y herramientas de generación

<span class="fragment">Generadores estáticos: <mark>Docusaurus</mark>, <mark>VitePress</mark>, MkDocs, Sphinx</span>

<span class="fragment">Documentación in-app: tooltips, paneles, <code>title</code>, <code>aria-describedby</code></span>

<span class="fragment">Formatos de exportación: HTML, PDF (herramientas de la unidad de informes), Markdown</span>

<span class="fragment">Integración en el repositorio y en el <mark>CI</mark> que publica la documentación</span>

Note:
La tendencia actual es tratar la documentación como un sitio estático versionado que se despliega automáticamente. Docusaurus y VitePress son los más usados con proyectos de frontend.

---

## Documentar la información persistente

<span class="fragment">Diagramas del <mark>modelo de datos</mark> (entidad-relación) y esquemas</span>

<span class="fragment"><mark>OpenAPI / Swagger</mark> como fuente única de verdad de los contratos</span>

```yaml
components:
  schemas:
    Nota:
      type: object
      required: [id, titulo]
      properties:
        id:     { type: string, format: uuid }
        titulo: { type: string, maxLength: 120 }
```

Note:
Documentar «la estructura de la información persistente» (criterio oficial) significa dejar constancia clara del modelo: entidades, relaciones, campos, tipos y restricciones. El OpenAPI evita divergencias entre frontend y backend.

---

## Documentación de código y componentes

<span class="fragment">Comentar el <mark>«por qué»</mark>, no el «qué». Convenciones (TSDoc / JSDoc)</span>

<span class="fragment"><mark>Storybook</mark> como documentación viva de componentes (Unidad 14)</span>

<span class="fragment">Changelogs, <code>CONTRIBUTING.md</code> y política de versiones</span>

<span class="fragment">Decisiones de diseño documentadas (<mark>ADR</mark>)</span>

Note:
La documentación de componentes en Storybook es ejecutable: muestra cada variante y estado. El resto (changelog, ADR) ayuda a quien mantiene el código a entender las decisiones pasadas.

---

## Actividad en clase

<div style="font-size: 1.2rem; text-align: left;">

**Paquete documental de la aplicación del módulo**
</div>

<span class="fragment">1. Elabora la <mark>matriz documento → destinatario</mark> y justifica cada formato</span>

<span class="fragment">2. Implementa <mark>ayuda contextual accesible</mark> en tres pantallas (foco + ARIA)</span>

<span class="fragment">3. Documenta el modelo de datos con un diagrama E/R y un diccionario de campos</span>

Note:
Actividad integradora: aplicáis los tres bloques a vuestra propia aplicación. El resultado es un conjunto documental coherente y entregable.

---

## Buenas prácticas

<span class="fragment">Trata la documentación como <mark>código</mark>: en el repositorio, revisada y actualizada con cada cambio</span>

<span class="fragment">Un documento, un destinatario, un formato; evita duplicar contenido</span>

<span class="fragment">Documenta el <mark>«por qué»</mark> en el código y las decisiones de diseño (ADR)</span>

<span class="fragment">Haz la ayuda contextual <mark>accesible por teclado</mark> y coherente con la guía de referencia</span>

<span class="fragment">Usa <mark>OpenAPI</mark> como fuente única de verdad para los contratos</span>

Note:
La regla de oro: si la documentación no se actualiza con el código, es peor que no tenerla (miente). Por eso vive en el repositorio y se revisa en pull request.

---

## Errores frecuentes

<span class="fragment">Documentar solo al final, cuando la aplicación ya cambió</span>

<span class="fragment">Ayuda contextual <mark>no accesible</mark> por teclado ni asociada con ARIA</span>

<span class="fragment">Duplicar información entre manual y ayuda in-app sin un único origen</span>

<span class="fragment">Omitir la documentación de <mark>instalación / configuración / administración</mark></span>

<span class="fragment">Comentar el «qué» en lugar del «por qué», aportando poco valor</span>

Note:
El más dañino es documentar al final: nace desactualizada. Y omitir los manuales de administración bloquea el despliegue (se conecta con el RA 7).

---

## Resumen · Conceptos clave

<span class="fragment">Elige el <mark>documento adecuado</mark> para cada destinatario y momento</span>

<span class="fragment">Ayuda general + <mark>sensible al contexto</mark>, manual de usuario, guía de referencia, manuales de instalación / configuración / administración y tutoriales</span>

<span class="fragment">Documenta también la <mark>información persistente</mark> (modelo de datos, diccionario, OpenAPI) y el código</span>

<span class="fragment">Trátala como <mark>código</mark>: en el repositorio, revisada y publicada desde el pipeline</span>

Note:
Cerramos: documentar es una competencia profesional, no un trámite. Un buen paquete documental multiplica la adopción y mantenibilidad de vuestra aplicación.

---

## ¿Preguntas?

<div style="font-size: 1.6rem;">

Unidad 18 · Documentación de Aplicaciones
</div>

Note:
Espacio para dudas. La próxima unidad (19) aborda las pruebas, la otra cara de la calidad junto a la documentación.
