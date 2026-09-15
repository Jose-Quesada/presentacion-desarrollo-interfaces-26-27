# Unidad 18: Aplicaciones de Escritorio con Electron

---

## Portada

### Módulo 0488 — Desarrollo de Interfaces
# Aplicaciones de Escritorio con Electron

**Angular + Electron · Main Process · IPC · Preload · Native APIs**

Curso DAM — Unidad 18

Note:
Bienvenidos a la unidad más transformadora del módulo. Vais a aprender a convertir vuestras aplicaciones Angular en aplicaciones de escritorio nativas para Windows, macOS y Linux. Electron es la tecnología que usan VS Code, Discord, Slack, Figma, Postman y WhatsApp Desktop.

---

## Objetivos de Aprendizaje

1. Comprender la <mark>arquitectura Electron</mark>: Chromium + Node.js
2. Dominar los <mark>3 procesos</mark>: Main, Renderer, Preload
3. Implementar <mark>comunicación IPC</mark>: ipcMain ↔ ipcRenderer
4. Integrar <mark>Angular + Electron</mark> con enfoque profesional
5. Aplicar <mark>seguridad obligatoria</mark>: nodeIntegration false + contextIsolation true
6. Usar <mark>APIs nativas</mark>: fs, dialog, Menu, Tray, Notification, globalShortcut

Note:
Al finalizar esta unidad seréis capaces de crear aplicaciones de escritorio completas usando solo tecnologías web. La seguridad es el aspecto más crítico: un XSS en Electron puede comprometer todo el sistema operativo del usuario.

---

## Motivación — ¿Por qué Electron?

- <mark>Un solo código base → 3 plataformas</mark> (Windows, macOS, Linux)
- Usáis <mark>tecnologías que ya domináis</mark>: HTML, CSS, TypeScript
- <mark>Ecosistema npm</mark> con cientos de miles de paquetes
- Acceso a <mark>APIs del sistema operativo</mark> imposibles desde un navegador
- Desarrollo más rápido que nativo (C++, C#, JavaFX, Qt)

Note:
Electron democratiza el desarrollo de escritorio. Antes, crear una app para 3 plataformas requería 3 equipos especializados. Ahora, un desarrollador frontend puede hacerlo solo. El coste: mayor consumo de RAM (~50-100 MB base) y tamaño de instalador (~60-80 MB). Para la mayoría de apps empresariales, el trade-off es aceptable.

---

## ¿Cómo Funciona Electron?

<div class="mermaid">
graph TB
    subgraph "Electron App"
        MP["<mark>Main Process</mark><br/>(Node.js)<br/>Acceso total al SO"]
        RP["<mark>Renderer Process</mark><br/>(Chromium)<br/>HTML + CSS + JS<br/>Angular"]
        PS["<mark>Preload Script</mark><br/>Puente seguro"]
        MP <-->|"IPC"| PS
        PS <-->|"contextBridge"| RP
    end
    MP -->|"fs, dialog, Menu..."| OS[Sistema Operativo]
    RP -->|"Interfaz de usuario"| UI[Ventana Nativa]
</div>

Note:
Electron combina Chromium (motor de renderizado de Chrome) y Node.js en un solo ejecutable. El Main Process es el "cerebro": tiene acceso total al SO pero no al DOM. El Renderer Process ejecuta Angular y la UI en una ventana nativa. El Preload Script es el puente seguro entre ambos, aplicando el principio de mínimo privilegio.

---

## Los 3 Procesos de Electron

| Proceso | ¿Cuántos? | ¿Acceso Node.js? | ¿Acceso DOM? | Función |
|---|---|---|---|---|
| <mark>Main</mark> | 1 por app | ✅ Completo | ❌ | Orquestador: ventanas, menús, SO |
| <mark>Renderer</mark> | 1 por ventana | ❌ (por seguridad) | ✅ | UI: Angular, HTML, CSS |
| <mark>Preload</mark> | 1 por ventana | ✅ Limitado | ✅ | Puente seguro: expone API mínima |

Note:
El Main Process es el punto de entrada (definido en `"main"` de package.json). Solo hay uno. Los Renderers son procesos aislados: cada BrowserWindow tiene el suyo. El Preload se ejecuta antes de cargar la web y tiene acceso a ambos mundos, pero de forma controlada mediante `contextBridge`.

---

## Comunicación IPC (Inter-Process Communication)

<div class="mermaid">
sequenceDiagram
    participant R as Renderer (Angular)
    participant P as Preload
    participant M as Main Process
    R->>P: window.electronAPI.openFile()
    P->>M: ipcRenderer.invoke('dialog:openFile')
    M->>M: dialog.showOpenDialog()
    M-->>P: Result { filePaths, canceled }
    P-->>R: return result
    R->>R: Mostrar contenido del archivo
</div>

Note:
IPC es el mecanismo de comunicación. El modelo recomendado es invoke/handle (solicitud-respuesta asíncrona), más limpio que el antiguo send/on. El renderer NUNCA llama directamente a IPC: siempre pasa por el preload, que expone solo los métodos necesarios mediante `contextBridge`.

---

## Casos Reales — Aplicaciones Electron Famosas

<div class="fragment">

| App | Categoría | Dato destacado |
|---|---|---|
| <mark>VS Code</mark> | Editor código | Cientos de extensiones, excelente rendimiento |
| <mark>Discord</mark> | Chat voz/vídeo | WebRTC + overlay en juegos |
| <mark>Slack</mark> | Mensajería | Inversión masiva en optimización RAM |
| <mark>Figma</mark> | Diseño UI | Renderizado GPU con <mark>WebGL</mark> |
| <mark>Postman</mark> | Cliente HTTP | Sin restricciones CORS |
| <mark>Obsidian</mark> | Notas Markdown | Acceso a sistema de archivos local |

</div>

Note:
VS Code demuestra que Electron puede escalar a apps enormes con excelente rendimiento. Su secreto: Web Workers para tareas pesadas, lazy loading de extensiones y renderizado virtualizado. Figma es único: usa WebGL en lugar del renderizado DOM tradicional de Chromium para un rendimiento comparable al nativo.

---

## Lecciones de los Casos Reales

1. <mark>Separación clara UI (web) ↔ lógica nativa (Node.js)</mark>
2. Actualizaciones automáticas silenciosas
3. Instalación nativa, no "abrir en navegador"
4. Funcionalidades del SO que <mark>diferencian la app de su versión web</mark>
5. Enfoque <mark>offline-first</mark>: la app funciona sin internet

Note:
Todas las apps exitosas comparten estos patrones. La diferencia con la versión web es lo que justifica Electron: menú nativo, bandeja del sistema, acceso a archivos, notificaciones nativas, atajos de teclado globales. Si tu app de escritorio no aprovecha estas capacidades, mejor quédate con la versión web.

---

## Configuración: Proyecto Angular + Electron

### Enfoque recomendado:

<div class="fragment">

1. Proyecto Angular standalone independiente (`ng new`)
2. Electron como <mark>capa que envuelve</mark> la app compilada
3. Desarrollo: `electron .` carga `http://localhost:4200`
4. Producción: `electron .` carga `dist/index.html` desde sistema de archivos

</div>

Note:
El proyecto Angular existe de forma independiente. Electron no interfiere con el desarrollo web normal. Durante el desarrollo, la ventana de Electron carga el servidor de Angular (con hot reload). En producción, carga los archivos compilados. Esto permite que la misma app funcione como SPA web y como app de escritorio.

---

## Instalación de Electron

```bash
npm install --save-dev electron
```

```bash
# Herramientas auxiliares para desarrollo
npm install --save-dev concurrently wait-on cross-env
```

Note:
Electron se instala como dependencia de desarrollo (~60 MB, contiene Chromium + Node.js para la plataforma actual). `concurrently` ejecuta ng serve y electron en paralelo. `wait-on` espera a que el servidor Angular esté listo antes de lanzar Electron. `cross-env` permite variables de entorno multiplataforma.

---

## main.js — Crear BrowserWindow

```javascript
const { app, BrowserWindow } = require('electron');
const path = require('path');
let mainWindow = null;

function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1200, height: 800,
    minWidth: 800, minHeight: 600,
    title: 'Mi App Angular + Electron',
    webPreferences: {
      nodeIntegration: false,
      contextIsolation: true,
      preload: path.join(__dirname, 'preload.js')
    }
  });

  if (process.env.NODE_ENV === 'development') {
    mainWindow.loadURL('http://localhost:4200');
    mainWindow.webContents.openDevTools();
  } else {
    mainWindow.loadFile(
      path.join(__dirname, 'dist', 'index.html'));
  }
}

app.whenReady().then(createWindow);
```

Note:
Este es el punto de entrada de la aplicación. `webPreferences` es crítico para la seguridad: `nodeIntegration: false` + `contextIsolation: true` son obligatorios. En desarrollo, carga el servidor Angular y abre DevTools. En producción, carga los archivos compilados. `app.whenReady()` espera a que Electron esté listo para crear ventanas.

---

## Eventos del Ciclo de Vida de la App

```javascript
app.whenReady().then(createWindow);

app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();  // Windows/Linux: cerrar app
  }
  // macOS: la app sigue en el Dock sin ventanas
});

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow();  // macOS: reabrir al click en Dock
  }
});
```

Note:
En macOS, las aplicaciones no se cierran al cerrar todas las ventanas (permanecen en el Dock). En Windows/Linux, sí. `activate` se emite en macOS cuando el usuario hace clic en el icono del Dock sin ventanas abiertas. Respetad las convenciones de cada plataforma.

---

## preload.js — Puente Seguro con contextBridge

```javascript
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  openFileDialog: (options) =>
    ipcRenderer.invoke('dialog:openFile', options),
  saveFileDialog: (options) =>
    ipcRenderer.invoke('dialog:saveFile', options),
  readFile: (filePath) =>
    ipcRenderer.invoke('fs:readFile', filePath),
  writeFile: (filePath, data) =>
    ipcRenderer.invoke('fs:writeFile', filePath, data),
  getAppVersion: () =>
    ipcRenderer.invoke('app:getVersion'),
  getPlatform: () =>
    ipcRenderer.invoke('app:getPlatform'),
  onMenuEvent: (channel, callback) => {
    const validChannels = ['menu:new', 'menu:open',
      'menu:save', 'menu:saveAs'];
    if (validChannels.includes(channel)) {
      ipcRenderer.on(channel,
        (event, ...args) => callback(...args));
    }
  }
});
```

Note:
`contextBridge.exposeInMainWorld` es el ÚNICO mecanismo seguro para exponer APIs al renderer. El objeto expuesto (`electronAPI`) no puede ser modificado por código del renderer. NUNCA expongáis `ipcRenderer` directamente: eso daría al renderer acceso a todos los canales IPC. Validar los canales en el preload añade una capa extra de seguridad.

---

## Manejadores IPC en Main Process

```javascript
const { ipcMain, dialog } = require('electron');
const fs = require('fs');

ipcMain.handle('dialog:openFile', async (event, options) => {
  const result = await dialog.showOpenDialog(mainWindow, {
    title: 'Abrir archivo',
    filters: [{ name: 'Documentos',
      extensions: ['txt', 'md', 'json'] }],
    properties: ['openFile'], ...options
  });
  return result;
});

ipcMain.handle('fs:readFile', async (event, filePath) => {
  try {
    const data = fs.readFileSync(filePath, 'utf-8');
    return { success: true, data };
  } catch (error) {
    return { success: false, error: error.message };
  }
});

ipcMain.handle('app:getPlatform', () => process.platform);
ipcMain.handle('app:getVersion', () => app.getVersion());
```

Note:
`ipcMain.handle` es el patrón moderno para solicitud-respuesta. Siempre devolver objetos estructurados `{ success, data/error }` para que el renderer pueda manejar errores. NUNCA confiéis en los datos recibidos del renderer: validar tipos, rutas y rangos antes de ejecutar operaciones en el sistema de archivos.

---

## ElectronService en Angular

```typescript
export interface ElectronAPI {
  openFileDialog: (options?: any) => Promise<any>;
  readFile: (path: string) =>
    Promise<{ success: boolean; data?: string; error?: string }>;
  getPlatform: () => Promise<NodeJS.Platform>;
  // ...
}

@Injectable({ providedIn: 'root' })
export class ElectronService {
  isElectron = signal(false);
  platform = signal<string>('browser');
  private electronAPI: ElectronAPI | undefined;

  constructor() {
    if (typeof window !== 'undefined' &&
        (window as any).electronAPI) {
      this.electronAPI = (window as any).electronAPI;
      this.isElectron.set(true);
      this.electronAPI!.getPlatform()
        .then(p => this.platform.set(p));
    }
  }

  async openFile(): Promise<{ filePath: string;
      content: string } | null> {
    if (!this.electronAPI) return null;
    const result = await this.electronAPI
      .openFileDialog();
    if (result.canceled || !result.filePaths.length)
      return null;
    const filePath = result.filePaths[0];
    const read = await this.electronAPI
      .readFile(filePath);
    return read.success
      ? { filePath, content: read.data! } : null;
  }
}
```

Note:
`ElectronService` abstrae la detección del entorno y proporciona una API unificada. Si la app se ejecuta en navegador (sin `electronAPI`), los métodos devuelven `null` y se puede implementar un fallback web (`<input type="file">` en lugar de diálogo nativo). Esto permite que la misma app funcione en ambos entornos.

---

## Scripts en package.json

```json
{
  "name": "mi-app-electron",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build --configuration production",
    "electron:dev": "cross-env NODE_ENV=development concurrently \"ng serve\" \"wait-on http://localhost:4200 && electron .\"",
    "electron:build": "ng build --configuration production && electron-builder",
    "electron:start": "electron ."
  }
}
```

Note:
`electron:dev` ejecuta Angular y Electron en paralelo. `wait-on` espera a que `localhost:4200` responda antes de lanzar Electron. `electron:build` compila Angular en modo producción y luego empaqueta con electron-builder (lo veremos en la unidad 19). `"main": "main.js"` le dice a Electron cuál es el punto de entrada.

---

## Seguridad en Electron — Reglas OBLIGATORIAS

<div class="fragment">

1. <mark>`nodeIntegration: false`</mark> — SIEMPRE, sin excepciones
2. <mark>`contextIsolation: true`</mark> — SIEMPRE (por defecto desde Electron 12)
3. <mark>Usar `contextBridge`</mark> en preload, nunca exponer `ipcRenderer` directamente
4. <mark>Content Security Policy (CSP)</mark> restrictiva
5. <mark>Validar TODOS los datos</mark> recibidos por IPC en el Main Process
6. <mark>No cargar contenido remoto</mark> sin sanitizar (XSS → compromiso total del SO)

</div>

Note:
Con `nodeIntegration: true`, un ataque XSS en el renderer permite ejecutar `require('child_process').exec('rm -rf /')` con los privilegios del usuario. Es un riesgo existencial para la aplicación y el usuario. La seguridad en Electron no es opcional. Revisad periódicamente el Security Checklist oficial.

---

## Content Security Policy (CSP)

```html
<meta http-equiv="Content-Security-Policy"
  content="
    default-src 'self';
    script-src 'self';
    style-src 'self' 'unsafe-inline';
    img-src 'self' data: https:;
    connect-src 'self' https:;
    font-src 'self';
  ">
```

Note:
La CSP previene ataques XSS limitando qué recursos puede cargar el renderer. `default-src 'self'` solo permite recursos del mismo origen. `'unsafe-inline'` en styles es necesario para Tailwind (que inyecta estilos inline). Si usáis fuentes de Google Fonts o CDNs, añadidlos explícitamente.

---

## APIs Nativas Clave de Electron

| API | Descripción | Ejemplo |
|---|---|---|
| <mark>`fs`</mark> | Sistema de archivos | Leer/guardar documentos |
| <mark>`dialog`</mark> | Diálogos nativos SO | Abrir/guardar archivo |
| <mark>`Menu`</mark> | Menú nativo | Archivo, Edición, Ayuda |
| <mark>`Tray`</mark> | Bandeja del sistema | Icono + menú contextual |
| <mark>`Notification`</mark> | Notificaciones nativas | Factura vencida, recordatorio |
| <mark>`globalShortcut`</mark> | Atajos globales | Ctrl+Shift+Space (incluso sin foco) |
| <mark>`clipboard`</mark> | Portapapeles | Copiar/pegar texto e imágenes |
| <mark>`shell`</mark> | Integración SO | Abrir URL, revelar archivo en explorador |

Note:
Estas APIs son lo que diferencia una app Electron de una web. `dialog` muestra los diálogos nativos del SO (no HTML simulado). `globalShortcut` funciona incluso cuando la app está en segundo plano. `shell.openExternal()` abre URLs en el navegador por defecto del sistema. Usadlas para aportar valor real al usuario.

---

## Menú Nativo Completo

```javascript
const { Menu, shell } = require('electron');

function createMenu(mainWindow) {
  const template = [
    {
      label: 'Archivo',
      submenu: [
        { label: 'Nuevo', accelerator: 'CmdOrCtrl+N',
          click: () => mainWindow.webContents
            .send('menu:new') },
        { label: 'Abrir...', accelerator: 'CmdOrCtrl+O',
          click: () => mainWindow.webContents
            .send('menu:open') },
        { type: 'separator' },
        { label: 'Guardar', accelerator: 'CmdOrCtrl+S',
          click: () => mainWindow.webContents
            .send('menu:save') },
        { role: 'quit', label: 'Salir' }
      ]
    },
    {
      label: 'Edición',
      submenu: [
        { role: 'undo' }, { role: 'redo' },
        { type: 'separator' },
        { role: 'cut' }, { role: 'copy' },
        { role: 'paste' }, { role: 'selectAll' }
      ]
    }
  ];
  const menu = Menu.buildFromTemplate(template);
  Menu.setApplicationMenu(menu);
}
```

Note:
El menú nativo se comunica con el renderer mediante `webContents.send()`. Los roles estándar (undo, redo, cut, copy, paste) los maneja Electron automáticamente. En macOS, el primer menú debe ser el de la aplicación (con About, Services, Hide, Quit). Los atajos de teclado usan `CmdOrCtrl` para funcionar en ambas plataformas.

---

## Bandeja del Sistema (Tray)

```javascript
const { Tray, nativeImage } = require('electron');

let tray = null;
function createTray() {
  const icon = nativeImage.createFromPath(
    path.join(__dirname, 'assets', 'icon.png'));
  tray = new Tray(icon.resize({ width: 16, height: 16 }));
  tray.setToolTip('Gestor de Notas');

  const contextMenu = Menu.buildFromTemplate([
    { label: 'Mostrar ventana',
      click: () => mainWindow.show() },
    { type: 'separator' },
    { label: 'Salir', click: () => app.quit() }
  ]);
  tray.setContextMenu(contextMenu);

  tray.on('click', () => mainWindow.show());
}
```

Note:
El Tray permite que la app siga ejecutándose en segundo plano (minimizada a la bandeja). Al cerrar la ventana, en lugar de salir, ocultáis la ventana y la app sigue en el Tray. El icono debe ser pequeño (16x16 o 22x22 píxeles). En macOS, usar un icono con template para que se adapte al modo oscuro.

---

## Diálogos Nativos

```javascript
// Abrir archivo
const result = await dialog.showOpenDialog(mainWindow, {
  title: 'Abrir documento',
  filters: [
    { name: 'Documentos',
      extensions: ['txt', 'md', 'json'] },
    { name: 'Todos los archivos',
      extensions: ['*'] }
  ],
  properties: ['openFile', 'multiSelections']
});

// Guardar archivo
const result = await dialog.showSaveDialog(mainWindow, {
  title: 'Guardar como',
  defaultPath: 'documento.txt',
  filters: [{ name: 'Texto',
    extensions: ['txt', 'md'] }]
});

// Mensaje
await dialog.showMessageBox(mainWindow, {
  type: 'warning',
  title: 'Cambios sin guardar',
  message: 'Tienes cambios sin guardar.',
  detail: '¿Deseas guardar antes de salir?',
  buttons: ['Guardar', 'No guardar', 'Cancelar'],
  defaultId: 0, cancelId: 2
});
```

Note:
Los diálogos nativos son del sistema operativo real, no HTML simulado. El usuario los reconoce y confía en ellos. `showMessageBox` devuelve el índice del botón pulsado. Los filtros de archivo limitan qué archivos se muestran en el diálogo. `properties: ['openFile']` permite seleccionar archivos; añadiendo `'openDirectory'` se seleccionan carpetas.

---

## Notificaciones Nativas

```javascript
const { Notification } = require('electron');

function showNotification(title, body) {
  if (Notification.isSupported()) {
    const notification = new Notification({
      title,
      body,
      icon: path.join(__dirname, 'assets/icon.png'),
      silent: false
    });
    notification.on('click', () => {
      mainWindow.show();
      mainWindow.focus();
    });
    notification.show();
  }
}
```

Note:
Las notificaciones nativas aparecen en el Centro de Actividades (Windows), Centro de Notificaciones (macOS) o el sistema de notificaciones del entorno de escritorio (Linux). Al hacer clic en la notificación, se puede restaurar y enfocar la ventana principal. Usadlas con moderación: solo para información que requiera atención inmediata.

---

## Ventanas Avanzadas (BrowserWindow)

```javascript
new BrowserWindow({
  width: 1200, height: 800,
  minWidth: 800, minHeight: 600,
  fullscreen: false,          // Pantalla completa
  alwaysOnTop: false,         // Siempre encima
  transparent: false,         // Fondo transparente
  frame: true,                // false = sin bordes (personalizado)
  titleBarStyle: 'default',   // 'hiddenInset' en macOS
  backgroundColor: '#ffffff', // Color mientras carga
  show: false,                // Mostrar cuando esté lista
  icon: path.join(__dirname, 'assets/icon.png'),
  webPreferences: {
    nodeIntegration: false,
    contextIsolation: true,
    preload: path.join(__dirname, 'preload.js'),
    sandbox: false
  }
});

mainWindow.once('ready-to-show', () => {
  mainWindow.show();  // Evita flash blanco al cargar
});
```

Note:
`show: false` + evento `ready-to-show` evita el flash blanco al abrir la app. `frame: false` permite diseñar vuestra propia barra de título (como Spotify o VS Code con título personalizado). `transparent: true` permite ventanas no rectangulares. `titleBarStyle: 'hiddenInset'` en macOS integra los botones de tráfico de luces.

---

## Demo: Angular + Electron "Gestor de Notas"

```typescript
@Component({
  selector: 'app-editor', standalone: true,
  template: `
    <div class="h-screen flex flex-col">
      <div class="flex items-center gap-2 px-4 py-2
                  bg-gray-100 border-b">
        <button (click)="newFile()"
          class="p-2 hover:bg-gray-200 rounded">📄</button>
        <button (click)="openFile()"
          class="p-2 hover:bg-gray-200 rounded">📂</button>
        <button (click)="saveFile()"
          class="p-2 hover:bg-gray-200 rounded">💾</button>
        <span class="flex-1 text-sm text-gray-500">
          {{ currentFilePath() || 'Sin título' }}</span>
        @if (isModified()) {
          <span class="text-xs text-orange-500">●</span> }
      </div>
      <textarea [(ngModel)]="content"
        (input)="onContentChange()"
        class="flex-1 p-6 resize-none font-mono"
        placeholder="Escribe tu nota aquí..."></textarea>
      <div class="flex items-center px-4 py-1
                  bg-gray-50 border-t text-xs text-gray-400">
        {{ wordCount() }} palabras | {{ platform() }}
      </div>
    </div>
  `
})
export class EditorComponent {
  private electronService = inject(ElectronService);
  content = '';
  currentFilePath = signal<string | null>(null);
  isModified = signal(false);
  platform = this.electronService.platform;
  wordCount = computed(() =>
    this.content.trim()
      ? this.content.trim().split(/\s+/).length : 0);

  async openFile() {
    const result = await this.electronService.openFile();
    if (result) {
      this.content = result.content;
      this.currentFilePath.set(result.filePath);
      this.isModified.set(false);
    }
  }

  async saveFile() {
    if (this.currentFilePath()) {
      await this.electronService.saveExistingFile(
        this.currentFilePath()!, this.content);
      this.isModified.set(false);
    } else {
      const path = await this.electronService
        .saveFile(this.content);
      if (path) {
        this.currentFilePath.set(path);
        this.isModified.set(false);
      }
    }
  }
}
```

Note:
Este componente demuestra la integración completa: el `ElectronService` abstrae toda la comunicación con Electron. Los diálogos de archivo son nativos del SO. El estado de modificación se gestiona con Signals. En navegador, `openFile()` devuelve null y se puede implementar fallback con `<input type="file">`.

---

## Diagrama de Integración Angular + Electron

<div class="mermaid">
flowchart TB
    subgraph "Desarrollo"
        NG["ng serve\nlocalhost:4200"]
        EL["electron .\nCarga URL de desarrollo"]
        NG --> EL
    end
    subgraph "Producción"
        BUILD["ng build --prod\ngenera dist/"]
        PACK["electron-builder\nempaqueta instalador"]
        BUILD --> PACK
    end
    subgraph "Ejecución"
        MAIN["Main Process\nmain.js"]
        PRELOAD["Preload\npreload.js"]
        RENDERER["Renderer\nAngular app"]
        MAIN --> PRELOAD --> RENDERER
    end
</div>

Note:
En desarrollo, Electron carga `http://localhost:4200` (hot reload incluido). En producción, carga `dist/index.html` desde el sistema de archivos. El Main Process se comunica con el Renderer a través del Preload usando IPC. Esta arquitectura permite desarrollar y probar la app en el navegador, y luego empaquetarla para escritorio.

---

## Actividad Guiada 1: Tu Primera App Angular + Electron

**⏱️ Tiempo**: 90 minutos | **👥 Agrupamiento**: Parejas

**🎯 Objetivo**: Configurar un proyecto Angular con Electron desde cero

1. `ng new demo-electron --standalone`
2. Instalar `electron`, `concurrently`, `wait-on`, `cross-env`
3. Crear `main.js` con BrowserWindow y seguridad configurada
4. Crear `preload.js` con `contextBridge` (mínimo 2 métodos)
5. Implementar manejadores IPC en `main.js`
6. Crear `ElectronService` que detecte entorno Electron
7. Modificar `AppComponent` para mostrar plataforma
8. Configurar scripts `electron:dev` en package.json
9. Ejecutar `npm run electron:dev` y verificar

Note:
Esta actividad es fundamental: sienta las bases de todo el desarrollo con Electron. El alumno debe comprender el flujo completo: main.js → preload.js → IPC → ElectronService → Componente. Se evalúa que la app funcione tanto en navegador como en ventana nativa.

---

## Actividad Guiada 2: Sistema de Archivos y Diálogos

**⏱️ Tiempo**: 60 minutos | **👥 Agrupamiento**: Individual

1. Ampliar `preload.js` con `openFileDialog`, `saveFileDialog`, `readFile`, `writeFile`
2. Ampliar `main.js` con manejadores IPC para diálogos y fs
3. Añadir métodos `openFile()` y `saveFile()` en `ElectronService`
4. Crear componente `TextEditorComponent` con textarea + botones Abrir/Guardar
5. Probad abriendo un `.txt`, modificándolo y guardándolo

Note:
Aquí el alumno experimenta la diferencia real entre una app web y una de escritorio: diálogos nativos del SO en lugar de `<input type="file">` HTML, acceso completo al sistema de archivos en lugar de la sandbox del navegador.

---

## Actividad Guiada 3: Menú Nativo y Bandeja del Sistema

**⏱️ Tiempo**: 75 minutos | **👥 Agrupamiento**: Parejas

1. Crear plantilla de menú: Archivo, Edición, Ver, Ayuda
2. Asignar atajos de teclado: `CmdOrCtrl+N`, `CmdOrCtrl+O`, `CmdOrCtrl+S`
3. Comunicar menú → renderer vía `webContents.send('menu:action')`
4. Exponer `onMenuAction(callback)` en preload
5. Suscribirse en Angular y ejecutar acciones
6. Añadir icono Tray con menú contextual (Mostrar ventana, Salir)
7. Probar atajos de teclado y bandeja

Note:
Esta actividad completa la experiencia de escritorio. El menú nativo y la bandeja del sistema son lo que los usuarios esperan de una aplicación de escritorio "de verdad". Los atajos de teclado aumentan la productividad del usuario experto.

---

## Buenas Prácticas

1. <mark>Seguridad primero</mark>: `nodeIntegration: false`, `contextIsolation: true`, `contextBridge`
2. <mark>Main Process ligero</mark>: solo orquestar; tareas pesadas → Web Workers o child_process
3. <mark>Validar datos IPC</mark>: nunca confiéis en datos del renderer sin validar
4. <mark>Diseñar para offline</mark>: almacenar localmente, cachear, sincronizar cuando haya red
5. <mark>Respetar convenciones de cada SO</mark>: menús, atajos, comportamiento al cerrar ventanas
6. <mark>Auto-actualización desde el día 1</mark>: `electron-updater` evita versiones obsoletas

Note:
La seguridad no es negociable. Un XSS en Electron es catastrófico. El Main Process debe ser ligero: si se bloquea, toda la app deja de responder. Las apps de escritorio no se actualizan solas como las webs: necesitan un mecanismo explícito de actualización.

---

## Errores Frecuentes

1. <mark>`nodeIntegration: true`</mark> — error de seguridad gravísimo en producción
2. <mark>Exponer `ipcRenderer` directamente</mark> en vez de usar `contextBridge`
3. <mark>Cargar contenido remoto sin CSP</mark> — XSS → acceso total al SO del usuario
4. <mark>No manejar `window-all-closed` en macOS</mark> — la app no se cierra correctamente
5. <mark>`fs.readFileSync` para archivos grandes</mark> — bloquea el Main Process
6. <mark>No limpiar listeners IPC</mark> — fugas de memoria y callbacks múltiples al recargar

Note:
El error 1 es el más grave: con `nodeIntegration: true`, cualquier script malicioso puede ejecutar código Node.js arbitrario. El error 6 es sutil: en desarrollo con hot reload, cada recarga crea nuevos listeners sin eliminar los anteriores, causando comportamientos impredecibles.

---

## Resumen

- <mark>Arquitectura</mark>: Main Process (Node.js) + Renderer (Chromium/Angular) + Preload (puente)
- <mark>IPC</mark>: `ipcMain.handle` / `ipcRenderer.invoke` para comunicación segura
- <mark>Seguridad</mark>: 3 reglas no negociables (nodeIntegration:false, contextIsolation:true, contextBridge)
- <mark>Integración Angular</mark>: `ElectronService` con detección de entorno
- <mark>APIs nativas</mark>: fs, dialog, Menu, Tray, Notification, globalShortcut, shell
- <mark>Casos reales</mark>: VS Code, Discord, Slack, Figma, Postman, Obsidian

Note:
Electron os permite llevar vuestras habilidades de desarrollo web al escritorio. La clave es entender la arquitectura de procesos y aplicar rigurosamente las medidas de seguridad. Con esto, podéis crear aplicaciones que compitan con las nativas tradicionales.

---

## Próximos Pasos

1. **Unidad 19**: Empaquetado y Distribución — electron-builder, instaladores, auto-update, CI/CD
2. **Unidad 20**: Proyecto Final "GesFlow" — integraréis Electron para la versión de escritorio con menú, diálogos y notificaciones

**📚 Para profundizar**:
- Electron Docs: https://www.electronjs.org/docs/
- Electron Security Checklist: https://www.electronjs.org/docs/latest/tutorial/security
- Electron API Demos (app descargable interactiva)
- Electron Fiddle (prototipado rápido): https://www.electronjs.org/fiddle

Note:
En la unidad 19 aprenderéis a empaquetar vuestra app en instaladores profesionales para Windows, macOS y Linux, con firma de código, auto-actualizaciones y CI/CD. En el proyecto final, GesFlow tendrá versión de escritorio con todas las funcionalidades nativas que hemos aprendido.

---

## Anexo: Opciones Avanzadas de BrowserWindow

```javascript
new BrowserWindow({
  // Ventanas sin bordes (personalizadas)
  frame: false,
  titleBarStyle: 'hidden',   // macOS
  // Ventanas transparentes
  transparent: true,
  // Siempre visible
  alwaysOnTop: true,
  // Modo quiosco
  kiosk: true,
  fullscreen: true,
  // Efectos visuales macOS
  vibrancy: 'ultra-dark',    // Desenfoque de fondo
  // Múltiples pantallas
  x: 0, y: 0                // Posición inicial
});
```

Note:
Estas opciones permiten crear experiencias muy pulidas. `transparent: true` + `frame: false` permite ventanas con formas personalizadas. `vibrancy` es un efecto de desenfoque tipo Frosted Glass exclusivo de macOS. `kiosk: true` bloquea la app en pantalla completa sin posibilidad de salir (para terminales de autoservicio).

---

## Anexo: Comunicación entre Múltiples Ventanas

```javascript
// Main Process — Broker central
const windows = new Map();

ipcMain.on('broadcast', (event, channel, data) => {
  windows.forEach((win, id) => {
    if (win.webContents.id !== event.sender.id) {
      win.webContents.send(channel, data);
    }
  });
});

// Preload
contextBridge.exposeInMainWorld('electronAPI', {
  broadcast: (channel, data) =>
    ipcRenderer.send('broadcast', channel, data),
  onMessage: (channel, callback) =>
    ipcRenderer.on(channel, (e, data) => callback(data))
});
```

Note:
Para aplicaciones con múltiples ventanas (editor con ventanas de detalle, inspector, etc.), el Main Process actúa como broker de mensajes. Cada ventana envía mensajes al Main, que los redistribuye a las demás. Así se mantiene el estado sincronizado entre ventanas sin acoplamiento directo.
