# Desarrollo de Interfaces

## Unidad 19 · Pruebas de Software

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad de pruebas. Probar no es «pulsar botones y ver si falla»: es una disciplina con estrategia, niveles y herramientas. Vamos a organizar las pruebas en pirámide, automatizar la regresión en el CI y medir también rendimiento y seguridad. El objetivo final: evidencia reproducible de que la aplicación funciona.

---

## Objetivos de aprendizaje

<span class="fragment">1. Establecer una <mark>estrategia de pruebas</mark> (niveles: unidad, integración, E2E)</span>

<span class="fragment">2. Ejecutar pruebas de <mark>integración, regresión, volumen y estrés</mark>, seguridad y uso de recursos</span>

<span class="fragment">3. Aplicar pruebas específicas de interfaces: <mark>componentes, flujos E2E y multi-navegador</mark></span>

<span class="fragment">4. <mark>Documentar</mark> la estrategia y los resultados con trazabilidad</span>

Note:
Cuatro objetivos que van de la planificación (estrategia) a la ejecución (tipos de prueba) y a la evidencia (documentación). Todo se apoya en las unidades de componentes Angular anteriores.

---

## Alineación con el currículo

<div style="font-size: 1.3rem; text-align: left;">

Esta unidad trabaja el <mark>RA 8</mark> del módulo 0488 (RD 405/2023):
</div>

<span class="fragment" style="font-size: 1.15rem;"><mark>«Evalúa el funcionamiento de aplicaciones diseñando y ejecutando pruebas»</mark></span>

<span class="fragment" style="font-size: 1.05rem;">CE: estrategia de pruebas · integración · regresión · volumen y estrés · seguridad · uso de recursos · documentación de la estrategia y resultados</span>

Note:
El RA 8 es el resultado de cierre del módulo: evalúais lo construido. Sus siete criterios se cubren a lo largo de esta unidad, con énfasis en automatización y métricas.

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

Corregir un defecto cuesta <mark>10×</mark> más en producción que en desarrollo, y <mark>100×</mark> más tras el lanzamiento.
</div>

<span class="fragment" style="font-size: 1.1rem;">Probar antes es la decisión más rentable del ciclo de vida</span>

Note:
La curva de coste del defecto justifica toda la inversión en pruebas. Detectar hoy lo que mañana sería un incidente grave.

---

## La pirámide de pruebas

<div class="mermaid">
graph TB
  E[E2E<br>Pocas · Lentas · Navegador real] --> I[Integración<br>Medias · Componentes + servicios]
  I --> U[Unidad<br>Muchas · Rápidas · Jest + Testing Library]
</div>

<span class="fragment" style="font-size: 1.05rem;">Invertirla (muchas E2E) hace la suite <mark>frágil y lenta</mark></span>

Note:
La pirámide es el principio organizativo: base ancha de unitarias baratas, menos integración arriba y pocas E2E. Si veis una «pirámide invertida», el proyecto tiene un problema de fiabilidad.

---

## Pruebas unitarias · Jest + Testing Library

```typescript
it('emite el evento click al pulsarse', async () => {
  const fixture = await render(ButtonComponent, {
    componentProperties: { (click) => void: spy }
  });
  fireEvent.click(screen.getByRole('button'));
  expect(spy).toHaveBeenCalled();
});
```

<span class="fragment" style="font-size: 1.05rem;">Probamos <mark>comportamientos observables</mark> (roles, texto), no la implementación interna</span>

Note:
Testing Library nos obliga a probar lo que ve el usuario, no los detalles internos. Eso hace las pruebas resistentes a refactorizaciones inocuas. Medimos cobertura y fijamos umbral.

---

## Pruebas de integración y end-to-end

<span class="fragment"><mark>Integración</mark>: componentes + enrutador + servicios funcionan <mark>juntos</mark></span>

```typescript
test('crea una nota y la exporta', async ({ page }) => {
  await page.goto('/');
  await page.getByRole('button', { name: 'Nueva nota' }).click();
  await page.getByLabel('Título').fill('Prueba');
  await expect(page.getByText('Prueba')).toBeVisible();
});
```

<span class="fragment" style="font-size: 1.05rem;"><mark>E2E</mark> con Playwright / Cypress en varios navegadores y tamaños (responsive)</span>

Note:
Las E2E recorren el flujo completo en un navegador real. Las corremos en distintos navegadores y anchos de pantalla para cubrir el diseño responsive de la Unidad 8.

---

## Regresión, volumen y estrés

<span class="fragment"><mark>Regresión</mark>: reejecutar la suite tras cada cambio; lo que funcionaba no se rompe (automatizado en CI)</span>

<span class="fragment"><mark>Volumen</mark>: grandes cantidades de datos (p. ej., miles de filas en una tabla)</span>

<span class="fragment"><mark>Estrés</mark>: carga sostenida para observar degradación, tiempos y estabilidad</span>

Note:
La regresión se automatiza para que corra en cada pull request. Volumen y estrés revelan problemas que solo aparecen con datos reales o picos de uso.

---

## Seguridad básica y uso de recursos

<span class="fragment"><mark>Seguridad</mark>: validación robusta de entradas, sin inyección por <code>[innerHTML]</code> no sanitizado, errores que no filtren datos sensibles</span>

<span class="fragment"><mark>Recursos</mark>: fugas de memoria (suscripciones no dadas de baja), <mark>Core Web Vitals</mark>, consumo en Electron vs. nativa</span>

Note:
Dos frentes a menudo olvidados en frontend. En seguridad, el riesgo típico es concatenar datos en HTML sin sanitizar. En recursos, la fuga clásica es no dar de baja suscripciones en ngOnDestroy.

---

## Documentar estrategia y resultados

<span class="fragment">Informe con: alcance, herramientas, casos, entorno, resultados, defectos y severidad</span>

<span class="fragment"><mark>Trazabilidad</mark>: requisito → caso de prueba → resultado</span>

<span class="fragment">Suite e informe integrados en el <mark>pipeline</mark>: cada build genera evidencia reproducible</span>

Note:
El criterio oficial exige documentar la estrategia y los resultados. Un informe sin trazabilidad no es accionable; con ella, el equipo sabe exactamente qué está cubierto y qué falta.

---

## Actividad en clase

<div style="font-size: 1.2rem; text-align: left;">

**Suite de pruebas de la aplicación del módulo**
</div>

<span class="fragment">1. Define la <mark>estrategia (pirámide)</mark> y justifica las herramientas por nivel</span>

<span class="fragment">2. Escribe pruebas unitarias para tres componentes y un servicio, con mocks</span>

<span class="fragment">3. Crea una prueba <mark>E2E</mark> del flujo principal y ejecútala en dos navegadores</span>

Note:
Actividad integradora: aplicáis la pirámide a vuestra aplicación. El entregable es una suite que corre en el CI y un informe de resultados.

---

## Buenas prácticas

<span class="fragment">Prioriza la <mark>pirámide</mark>: muchas unitarias, pocas E2E</span>

<span class="fragment">Prueba <mark>comportamientos observables</mark>, no implementaciones frágiles</span>

<span class="fragment">Automatiza la <mark>regresión en el CI</mark>; que corra en cada cambio</span>

<span class="fragment">Mide <mark>rendimiento y recursos</mark> como métricas, no solo «pasa / falla»</span>

<span class="fragment">Documenta defectos con <mark>severidad y trazabilidad</mark></span>

Note:
Estas cinco reglas mantienen la suite fiable y rápida. La más importante: automatizar la regresión para que el feedback sea inmediato en cada pull request.

---

## Errores frecuentes

<span class="fragment">Depender casi solo de <mark>E2E</mark> (suite lenta y frágil)</span>

<span class="fragment">No dar de baja suscripciones en <code>ngOnDestroy</code> → fugas de memoria</span>

<span class="fragment">Probar implementaciones internas en lugar del comportamiento</span>

<span class="fragment">Omitir pruebas de <mark>seguridad básicas</mark> (HTML no sanitizado, errores que filtran datos)</span>

<span class="fragment">No <mark>documentar resultados ni trazabilidad</mark>, dejando la prueba sin valor accionable</span>

Note:
El más común es abusar de E2E por comodidad. El más peligroso en producción es omitir seguridad básica. Y sin documentación, nadie puede actuar sobre los resultados.

---

## Resumen · Conceptos clave

<span class="fragment">Estrategia en <mark>pirámide</mark>: unitarias (Jest + Testing Library) → integración → E2E (Playwright / Cypress)</span>

<span class="fragment">Completar con <mark>regresión, volumen, estrés, seguridad y uso de recursos</mark> (memoria, Core Web Vitals, Electron)</span>

<span class="fragment">Todo se <mark>automatiza en el pipeline</mark></span>

<span class="fragment">Se <mark>documenta con trazabilidad</mark> requisito → caso → resultado</span>

Note:
Cerramos: evaluar el funcionamiento (RA 8) es lo que da confianza de que la aplicación se puede desplegar. La automatización y la métrica son lo que lo hacen sostenible.

---

## ¿Preguntas?

<div style="font-size: 1.6rem;">

Unidad 19 · Pruebas de Software
</div>

Note:
Espacio para dudas. A continuación (Unidades 20 y 21) pasamos a Electron y su distribución; el proyecto final (22) integra todo, incluida esta estrategia de pruebas.
