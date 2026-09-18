# Desarrollo de Interfaces

## Unidad 3 · Interfaces Naturales de Usuario (NUI)

**Módulo 0488 · Desarrollo de Interfaces**
CFGS Desarrollo de Aplicaciones Multiplataforma (DAM)

Curso 2025/2026

Note:
Bienvenidos a la unidad de interfaces naturales. Vamos a salir de la GUI clásica (ratón y teclado) e interactuar con el sistema mediante capacidades «instintivas»: voz, gestos, movimiento corporal y realidad aumentada. Todo se implementa en el navegador con las APIs web modernas y se encapsula en servicios Angular. Recordad: la NUI complementa a la GUI, nunca la sustituye. Pregunta: ¿quién usa un asistente de voz a diario?

---

## Objetivos de aprendizaje

<span class="fragment">1. Definir el concepto de <mark>interfaz natural (NUI)</mark> y diferenciarla de la GUI</span>

<span class="fragment">2. Reconocer las modalidades: <mark>voz, gestos, cuerpo, táctil avanzado y RA/RV</mark></span>

<span class="fragment">3. Implementar una NUI sencilla integrando <mark>reconocimiento de voz o detección de movimiento</mark></span>

<span class="fragment">4. Aplicar criterios de usabilidad y accesibilidad: <mark>alternativa siempre disponible</mark></span>

Note:
Cuatro objetivos. El tercero es el práctico clave: vais a construir una interfaz que responda a la voz o a un gesto. El cuarto es transversal y crítico: una NUI mal diseñada excluye a usuarios con discapacidad, por eso exigimos paridad con el teclado.

---

## Alineación con el currículo

<div style="font-size: 1.3rem; text-align: left;">

Esta unidad trabaja el <mark>RA 2</mark> del módulo 0488 (RD 405/2023):

</div>

<span class="fragment" style="font-size: 1.15rem;"><mark>«Genera interfaces naturales de usuario utilizando herramientas visuales»</mark></span>

<span class="fragment" style="font-size: 1.05rem;">CE: herramientas de aprendizaje automático · reconocimiento de voz · detección del movimiento · partes del cuerpo · realidad aumentada</span>

Note:
El RA 2 es el único resultado del módulo dedicado a interfaces no gráficas. Aquí cubrimos sus seis criterios de evaluación, con énfasis en voz y cuerpo (los más factibles en el navegador).

---

## Motivación

<div style="font-size: 1.4rem; text-align: left;">

¿Por qué salir del ratón y el teclado?
</div>

<span class="fragment" style="font-size: 1.1rem;">Acortar la distancia entre <mark>intención</mark> y <mark>acción</mark></span>

<span class="fragment" style="font-size: 1.1rem;">Manos ocupadas (cocina, conducción, quirófano, industria)</span>

<span class="fragment" style="font-size: 1.1rem;">Accesibilidad: usuarios con <mark>movilidad reducida</mark></span>

<span class="fragment" style="font-size: 1.1rem;">Contextos donde el contacto no es deseable (higiene, seguridad)</span>

Note:
La NUI no es una moda: responde a contextos reales donde el ratón no llega o no conviene. Pero insisto en el matiz: es un acelerador, no una dependencia crítica.

---

## De la GUI a la NUI · Modalidades

<div class="mermaid">
graph LR
  G[GUI<br>Ratón · Teclado] --> N[NUI<br>Capacidades instintivas]
  N --> V[Voz / VUI]
  N --> GE[Gestos]
  N --> CO[Cuerpo]
  N --> HA[Háptica]
  N --> RA[Realidad Aumentada / Virtual]
</div>

Note:
La GUI usa dispositivos intermedios (puntero, teclado). La NUI usa el cuerpo y la voz directamente. Cada modalidad tiene su tecnología y sus limitaciones; las repasamos una a una.

---

## Arquitectura típica de una NUI

<div class="mermaid">
graph TB
  A[Sensor<br>Cámara · Micrófono] --> B[Preprocesado<br>Señal / Fotogramas]
  B --> C[Modelo de reconocimiento<br>ASR · Landmarks]
  C --> D[Interpretación semántica<br>Comando / Gesto]
  D --> E[Acción en la aplicación]
</div>

<span class="fragment" style="font-size: 1.05rem;">La tasa de error es inherentemente mayor que en la GUI → <mark>confirmaciones y alternativas</mark></span>

Note:
Este pipeline es el mismo en voz (micrófono → ASR) y en cuerpo (cámara → landmarks). Entenderlo ayuda a diagnosticar dónde falla una interacción.

---

## Reconocimiento de voz · Web Speech API

<div style="font-size: 1.2rem; text-align: left;">

Dos capacidades: <mark>síntesis</mark> (<code>SpeechSynthesis</code>) y <mark>reconocimiento</mark> (<code>SpeechRecognition</code>)
</div>

```typescript
const SR = window.SpeechRecognition || (window as any).webkitSpeechRecognition;
const rec = new SR();
rec.lang = 'es-ES';
rec.onresult = (e: any) => {
  const texto = e.results[0][0].transcript.trim().toLowerCase();
  if (e.results[0][0].confidence > 0.6) actuar(texto);
};
```

Note:
La Web Speech API es el camino más rápido para una VUI en el navegador. Ojo al prefijo webkit en Chrome y al umbral de confianza: por debajo, no actuamos para evitar falsos positivos.

---

## VUI · Patrones de diálogo

<span class="fragment">Comandos <mark>discretos</mark> («guardar», «filtrar») vs. lenguaje natural</span>

<span class="fragment"><mark>Confirmación</mark> obligatoria para acciones destructivas</span>

<span class="fragment">Mostrar siempre la <mark>transcripción</mark> en pantalla</span>

<span class="fragment">Ruta de salida clara: «cancelar», «no» o silencio</span>

Note:
Diseñar un diálogo por voz es diseñar una conversación: qué se dice, cómo se confirma y cómo se sale. Los comandos discretos son más fiables que el lenguaje natural libre.

---

## Detección de movimiento corporal

<div style="font-size: 1.2rem; text-align: left;">

<mark>MediaPipe</mark> (landmarks) + <mark>TensorFlow.js</mark>: inferencia en el navegador, sin backend
</div>

```typescript
const res = landmarker.detectForVideo(video, performance.now());
if (res.poseLandmarks?.length) {
  const codo = res.poseLandmarks[10];
  const rodilla = res.poseLandmarks[23];
  pose.emit({ codoY: codo.y, rodillaY: rodilla.y });
}
```

Note:
MediaPipe entrega coordenadas normalizadas de articulaciones. A partir de ellas definimos reglas simples (codo por encima de la rodilla = «de pie»). Todo corre en el dispositivo.

---

## Robustez · Suavizado y histeresis

<span class="fragment"><mark>Suavizado temporal</mark>: media móvil de posiciones entre fotogramas</span>

<span class="fragment"><mark>Histeresis</mark>: umbrales distintos para activar y desactivar un gesto</span>

<span class="fragment">Exigir que el gesto se mantenga <mark>N fotogramas</mark> antes de actuar</span>

Note:
Sin esto, la interfaz dispara acciones al azar. El suavizado elimina el ruido; la histeresis evita el «parpadeo» entre dos estados. Es la diferencia entre una demo y algo usable.

---

## Realidad aumentada · WebXR + Three.js

<span class="fragment">Sesión inmersiva: <code>navigator.xr.requestSession('immersive-ar')</code></span>

<span class="fragment"><mark>Three.js</mark> como motor de renderizado, anclado al ciclo de vida Angular</span>

<span class="fragment"><mark>Hit-test</mark>: anclar contenido 3D sobre el mundo real</span>

<span class="fragment" style="font-size: 1.05rem;">Limitada por dispositivo → se plantea como <mark>ampliación</mark>, no requisito</span>

Note:
La RA es la modalidad con más restricciones de hardware (cámara compatible + soporte WebXR). Por eso la tratamos como ampliación: quien tenga el dispositivo, la integra.

---

## Integración en Angular · Servicios que emiten eventos

```typescript
@Injectable({ providedIn: 'root' })
export class VoiceCommandService {
  readonly command = new EventEmitter<string>();
  // ... onresult emite this.command.emit(texto)
}
```

<span class="fragment" style="font-size: 1.1rem;">El componente <mark>suscribe</mark> y mapea el comando a la misma acción que el botón equivalente</span>

Note:
La clave arquitectónica: encapsular cada modalidad en un servicio que emite eventos o Signals. Los componentes solo reaccionan. Así la NUI es intercambiable y testeable, y garantizamos paridad con los controles gráficos.

---

## Accesibilidad de las NUI

<span class="fragment"><mark>Transcripción visible</mark> de todo lo dicho / escuchado</span>

<span class="fragment"><mark>Paridad funcional</mark> con el teclado para cada acción</span>

<span class="fragment">Control explícito <mark>activar / desactivar</mark> cada modalidad</span>

<span class="fragment">Avisos de estado: «escuchando…», «no te he entendido, repítelo»</span>

Note:
Una NUI que no ofrece alternativa por teclado excluye a usuarios con discapacidad del habla, auditiva o motriz. La accesibilidad aquí no es opcional: es el principio de «alternativa siempre disponible».

---

## Actividad en clase

<div style="font-size: 1.2rem; text-align: left;">

**Filtro de un dashboard por voz**
</div>

<span class="fragment">1. Añade <code>VoiceCommandService</code> a un dashboard existente</span>

<span class="fragment">2. Tres comandos: «mostrar ventas», «filtrar por mes», «limpiar»</span>

<span class="fragment">3. Muestra la transcripción en una barra inferior</span>

<span class="fragment">4. Cada comando dispara el <mark>mismo método</mark> que su botón equivalente</span>

Note:
Actividad guiada: partimos de un dashboard y le añadimos control por voz con paridad total respecto a los botones. Es la forma más rápida de ver el valor de la NUI sin depender de ella.

---

## Buenas prácticas

<span class="fragment">Ofrece <mark>siempre</mark> una alternativa por teclado / ratón</span>

<span class="fragment">Feedback visible del estado del reconocimiento</span>

<span class="fragment">Comandos cortos, consistentes y confirmación en acciones destructivas</span>

<span class="fragment">Suavizado temporal + histeresis en la detección de gestos</span>

<span class="fragment">Encapsula cada modalidad en un <mark>servicio Angular</mark> que emita eventos</span>

Note:
Estas cinco reglas resumen la unidad. La primera es inquebrantable; las demás hacen la interacción fiable y depurable.

---

## Errores frecuentes

<span class="fragment">Hacer de la voz / gesto el <mark>único</mark> canal de interacción</span>

<span class="fragment">No comprobar el <mark>soporte del navegador</mark> antes de usar la API</span>

<span class="fragment">Disparar acciones con un solo fotograma o palabra, sin umbral</span>

<span class="fragment">Olvidar <code>ngOnDestroy</code> para detener reconocimiento / cámara</span>

<span class="fragment">Ignorar la <mark>transcripción visible</mark>, haciendo la interfaz opaca</span>

Note:
El más grave es el primero: convertir una alternativa en la única vía. El segundo rompe en navegadores sin soporte. Los demás degradan la experiencia o fugan recursos.

---

## Resumen · Conceptos clave

<span class="fragment">La <mark>NUI</mark> acerca la interacción a capacidades instintivas (voz, gestos, cuerpo, RA)</span>

<span class="fragment">En el navegador: <mark>Web Speech API</mark> (voz) · <mark>TensorFlow.js / MediaPipe</mark> (cuerpo) · <mark>WebXR + Three.js</mark> (RA)</span>

<span class="fragment">Se encapsula en <mark>servicios Angular</mark> que emiten eventos; los componentes reaccionan</span>

<span class="fragment">Principio rector: <mark>alternativa siempre disponible</mark> y feedback visible</span>

Note:
Cerramos: la NUI es un complemento potente de la GUI. Si recordáis una sola cosa, que sea «siempre hay alternativa por teclado».

---

## ¿Preguntas?

<div style="font-size: 1.6rem;">

Unidad 3 · Interfaces Naturales de Usuario
</div>

Note:
Espacio para dudas. Próxima unidad (4) volvemos a Figma para el diseño visual, que es la contraparte gráfica de lo visto hoy.
