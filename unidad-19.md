# Unidad 19: Empaquetado y Distribución de Aplicaciones

---

## Portada

### Módulo 0488 — Desarrollo de Interfaces
# Empaquetado y Distribución de Aplicaciones

**electron-builder · Instaladores · Code Signing · Auto-update · CI/CD**

Curso DAM — Unidad 19

Note:
En esta unidad aprenderéis a transformar vuestro código en un producto que los usuarios puedan instalar. Cubriremos electron-builder, formatos de instalador por plataforma, firma de código, auto-actualizaciones y pipelines CI/CD con GitHub Actions.

---

## Objetivos de Aprendizaje

1. Comprender el <mark>ciclo completo</mark>: build → empaquetado → distribución → actualización
2. Dominar <mark>electron-builder</mark> para generar instaladores nativos
3. Generar <mark>formatos por plataforma</mark>: NSIS, DMG, AppImage, DEB, RPM
4. Implementar <mark>CI/CD con GitHub Actions</mark> para build multiplataforma
5. Configurar <mark>auto-update con electron-updater</mark>
6. Comprender la <mark>firma de código</mark> (Authenticode, Apple Notarization)

Note:
El empaquetado es lo que convierte un proyecto en un producto. La diferencia con el desarrollo web es brutal: en la web, desplegar es subir archivos a un servidor. En escritorio, hay que generar instaladores de decenas de MB, firmarlos digitalmente y distribuirlos.

---

## Motivación — Del Código al Producto

<div class="mermaid">
flowchart LR
    A[💻 Código<br/>Angular + Electron] --> B[🔨 Build<br/>ng build + electron-builder]
    B --> C[📦 Instaladores<br/>.exe .dmg .AppImage]
    C --> D[🚀 Distribución<br/>GitHub Releases / Tiendas]
    D --> E[📥 Usuario<br/>Descarga e instala]
    E --> F[🔄 Auto-update<br/>Nuevas versiones]
</div>

Note:
Cada fase tiene sus desafíos. Un instalador mal configurado puede generar errores de permisos, alertas de antivirus, o simplemente una app que no arranca. La calidad del empaquetado afecta directamente a la tasa de adopción de usuarios.

---

## electron-builder — Instalación

```bash
npm install --save-dev electron-builder
```

```json
{
  "scripts": {
    "build:electron": "ng build --configuration production && electron-builder",
    "build:electron:win": "ng build --configuration production && electron-builder --win",
    "build:electron:mac": "ng build --configuration production && electron-builder --mac",
    "build:electron:linux": "ng build --configuration production && electron-builder --linux"
  }
}
```

Note:
electron-builder lee la configuración del campo `"build"` en package.json. Primero compilamos Angular (`ng build`), luego electron-builder empaqueta todo. Los flags `--win`, `--mac`, `--linux` limitan la build a una plataforma específica.

---

## Configuración Principal en package.json

```json
{
  "build": {
    "appId": "com.miempresa.miapp",
    "productName": "Mi Aplicación",
    "copyright": "Copyright © 2024 Mi Empresa S.L.",
    "directories": {
      "output": "release",
      "buildResources": "build"
    },
    "files": [
      "dist/**/*",
      "main.js",
      "preload.js",
      "node_modules/**/*",
      "!node_modules/.cache/**/*",
      "!node_modules/electron/**/*"
    ]
  }
}
```

Note:
`appId` debe ser único en formato reverse-domain. Si lo cambiáis en futuras versiones, el SO tratará la nueva versión como una app diferente. `files` usa patrones glob para controlar qué se incluye en el paquete. Excluir `devDependencies` y archivos fuente es crucial para no inflar el instalador.

---

## Tabla de Formatos por Plataforma

| Plataforma | Formato | Descripción | Cuándo usarlo |
|---|---|---|---|
| Windows | <mark>NSIS</mark> `.exe` | Instalador tradicional con asistente | Usuarios finales |
| Windows | <mark>Portable</mark> `.exe` | Sin instalación, ejecutar directamente | Entornos corporativos |
| Windows | <mark>AppX/MSIX</mark> | Microsoft Store | Distribución oficial |
| macOS | <mark>DMG</mark> | Imagen de disco (arrastrar a /Applications) | Fuera de App Store |
| macOS | <mark>PKG</mark> | Instalador de paquetes | Componentes del sistema |
| macOS | <mark>MAS</mark> | Mac App Store | Distribución oficial |
| Linux | <mark>AppImage</mark> | Portable universal | Máxima compatibilidad |
| Linux | <mark>DEB</mark> | Debian/Ubuntu/Mint | Gestor de paquetes |
| Linux | <mark>RPM</mark> | Fedora/RHEL/CentOS | Gestor de paquetes |
| Linux | <mark>Snap</mark> | Snap Store (Canonical) | 40+ distribuciones |

Note:
Cada formato tiene su uso. NSIS es el estándar para Windows. DMG es lo que los usuarios de Mac esperan (arrastrar el icono a Applications). AppImage es la opción más fácil para Linux. Ofreced múltiples formatos para maximizar el alcance.

---

## Configuración por Plataforma

```json
{
  "build": {
    "win": {
      "target": [
        { "target": "nsis", "arch": ["x64", "ia32"] },
        { "target": "portable", "arch": ["x64"] }
      ],
      "icon": "build/icon.ico",
      "publisherName": "Mi Empresa S.L."
    },
    "mac": {
      "target": [
        { "target": "dmg", "arch": ["x64", "arm64"] },
        { "target": "zip", "arch": ["x64", "arm64"] }
      ],
      "icon": "build/icon.icns",
      "category": "public.app-category.productivity",
      "hardenedRuntime": true
    },
    "linux": {
      "target": ["AppImage", "deb", "rpm"],
      "icon": "build/icons",
      "category": "Office"
    }
  }
}
```

Note:
En macOS, `arm64` genera binarios nativos para Apple Silicon (M1/M2/M3). `hardenedRuntime` es necesario para la notarización de Apple. En Linux, la carpeta `build/icons` debe contener PNGs de varios tamaños (16x16 a 512x512).

---

## Personalización del Instalador NSIS (Windows)

```json
{
  "nsis": {
    "oneClick": false,
    "perMachine": true,
    "allowToChangeInstallationDirectory": true,
    "installerIcon": "build/icon.ico",
    "uninstallerIcon": "build/icon.ico",
    "createDesktopShortcut": true,
    "createStartMenuShortcut": true,
    "shortcutName": "Mi Aplicación",
    "license": "LICENSE.txt",
    "installerLanguages": ["es_ES", "en_US"]
  }
}
```

Note:
`oneClick: false` muestra el asistente completo (elegir directorio, ver licencia). `perMachine: true` instala para todos los usuarios (requiere admin). `allowToChangeInstallationDirectory: true` deja al usuario elegir dónde instalar. Las imágenes de fondo y sidebars se configuran con `installerSidebar`.

---

## Personalización del DMG (macOS)

```json
{
  "dmg": {
    "title": "Mi Aplicación ${version}",
    "icon": "build/icon.icns",
    "iconSize": 80,
    "background": "build/dmg-background.png",
    "contents": [
      { "x": 130, "y": 220 },
      { "x": 410, "y": 220, "type": "link",
        "path": "/Applications" }
    ],
    "window": { "width": 540, "height": 380 }
  }
}
```

Note:
El fondo del DMG típicamente incluye una flecha indicando "arrastra aquí". Las posiciones (x, y) controlan dónde aparecen el icono de la app y el enlace a /Applications. `${version}` se sustituye automáticamente con la versión del package.json.

---

## Configuración de Publicación (Publish)

```json
{
  "build": {
    "publish": [
      {
        "provider": "github",
        "owner": "miempresa",
        "repo": "miapp",
        "releaseType": "draft"
      }
    ]
  }
}
```

Note:
El campo `publish` configura el servidor de actualizaciones para electron-updater. `provider: "github"` usa GitHub Releases (gratuito para repos públicos). `releaseType: "draft"` crea la release como borrador para revisarla antes de publicar. También se soportan AWS S3, Bintray, y servidores personalizados.

---

## Iconos de la Aplicación

```bash
npm install --save-dev electron-icon-builder
```

```json
{
  "scripts": {
    "generate-icons": "electron-icon-builder --input=./src/assets/icon.png --output=./build/"
  }
}
```

| Plataforma | Formato | Resoluciones |
|---|---|---|
| Windows | `.ico` | 16, 32, 48, 64, 128, 256 px |
| macOS | `.icns` | Mismas resoluciones |
| Linux | `.png` | 16x16 a 512x512 |

Note:
La imagen de entrada debe ser un PNG de al menos 1024x1024 píxeles. `electron-icon-builder` genera automáticamente todos los formatos necesarios. Un icono profesional es crucial para la primera impresión del usuario.

---

## CI/CD con GitHub Actions

<div class="mermaid">
flowchart TB
    TAG["git tag v1.0.0<br/>git push --tags"] --> WF["Workflow Release"]
    WF --> M["Matrix Strategy"]
    M --> W["Windows<br/>NSIS .exe"]
    M --> O["macOS<br/>DMG"]
    M --> L["Linux<br/>AppImage"]
    W --> REL["GitHub Release<br/>con 3 instaladores"]
    O --> REL
    L --> REL
</div>

Note:
GitHub Actions permite builds paralelos en las 3 plataformas. Cada runner genera el instalador para su SO nativo. Los 3 artefactos se adjuntan a una GitHub Release. Todo se activa automáticamente al pushear un tag de versión.

---

## Workflow GitHub Actions Completo

```yaml
name: Build and Release
on:
  push:
    tags: ['v*.*.*']

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20', cache: 'npm' }
      - run: npm ci
      - run: npm run build
      - name: Build Electron
        run: npx electron-builder --linux --publish=never
        if: runner.os == 'Linux'
      - name: Build Electron macOS
        run: npx electron-builder --mac --publish=never
        if: runner.os == 'macOS'
        env:
          APPLE_ID: ${{ secrets.APPLE_ID }}
          APPLE_APP_SPECIFIC_PASSWORD: ${{ secrets.APPLE_APP_SPECIFIC_PASSWORD }}
          CSC_LINK: ${{ secrets.CSC_LINK }}
          CSC_KEY_PASSWORD: ${{ secrets.CSC_KEY_PASSWORD }}
      - name: Build Electron Windows
        run: npx electron-builder --win --publish=never
        if: runner.os == 'Windows'
      - uses: actions/upload-artifact@v4
        with: { name: release-${{ runner.os }}, path: release/* }

  create-release:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
      - uses: softprops/action-gh-release@v1
        with:
          files: release-*/*
          draft: true
          generateReleaseNotes: true
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Note:
La matrix strategy ejecuta los 3 builds en paralelo. Los secrets (`APPLE_ID`, `CSC_LINK`, etc.) se configuran en GitHub Settings. El job `create-release` espera a que los 3 builds terminen, descarga todos los artifacts y crea la release. `draft: true` la crea como borrador para revisión.

---

## Firma de Código (Code Signing)

| Plataforma | Certificado | Coste anual | Sin firma |
|---|---|---|---|
| Windows | <mark>Authenticode</mark> (OV/EV) | 200-500 € | SmartScreen bloquea la app |
| macOS | <mark>Apple Developer ID</mark> | 99 € | Gatekeeper bloquea |
| Linux | GPG (opcional) | Gratuito | Pocas restricciones |

Note:
La firma de código es una inversión necesaria. Sin ella, Windows muestra "Windows protegió su equipo" y macOS "no se puede abrir porque es de un desarrollador no identificado". Muchos usuarios abandonan la instalación al ver estos mensajes. El certificado EV (Extended Validation) en Windows acelera la reputación con SmartScreen.

---

## Firma en macOS — Entitlements

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0"><dict>
  <key>com.apple.security.cs.allow-jit</key><true/>
  <key>com.apple.security.cs.allow-unsigned-executable-memory</key><true/>
  <key>com.apple.security.files.user-selected.read-write</key><true/>
  <key>com.apple.security.network.client</key><true/>
  <key>com.apple.security.device.camera</key><true/>
</dict></plist>
```

Note:
Los entitlements son permisos que la app necesita (cámara, micrófono, red, archivos). Se declaran en archivos `.plist`. Sin ellos, la funcionalidad simplemente falla sin error claro. `hardenedRuntime: true` habilita el runtime reforzado de macOS. La notarización de Apple verifica que la app no contiene malware.

---

## electron-updater — Actualizaciones Automáticas

```bash
npm install electron-updater
```

```javascript
const { autoUpdater } = require('electron-updater');

autoUpdater.autoDownload = false;
autoUpdater.autoInstallOnAppQuit = true;

app.whenReady().then(() => {
  createWindow();
  autoUpdater.checkForUpdatesAndNotify();
});

autoUpdater.on('update-available', (info) => {
  mainWindow.webContents.send('update:available', info);
});

autoUpdater.on('download-progress', (progress) => {
  mainWindow.webContents.send(
    'update:download-progress', progress);
});

autoUpdater.on('update-downloaded', (info) => {
  mainWindow.webContents.send(
    'update:downloaded', info);
});

ipcMain.handle('update:download',
  () => autoUpdater.downloadUpdate());
ipcMain.handle('update:install',
  () => autoUpdater.quitAndInstall());
```

Note:
`electron-updater` compara la versión local con la última release de GitHub. Si hay una nueva, descarga la actualización en segundo plano. `autoDownload: false` da mejor UX: primero notifica al usuario y él decide cuándo descargar. La actualización se aplica al reiniciar la app.

---

## Componente Angular de Notificación de Actualización

```typescript
@Component({
  selector: 'app-update-notification',
  standalone: true,
  template: `
    @if (updateAvailable()) {
      <div class="fixed bottom-4 right-4 bg-blue-600
                  text-white p-4 rounded-lg shadow-lg z-50">
        @if (!downloading() && !downloaded()) {
          <p class="font-semibold">¡Nueva versión!</p>
          <p class="text-sm mb-3">v{{ updateVersion() }}</p>
          <button (click)="startDownload()"
            class="px-4 py-2 bg-white text-blue-600 rounded">
            Descargar ahora</button>
        }
        @if (downloading()) {
          <p>Descargando... {{ downloadProgress() }}%</p>
          <div class="w-full bg-blue-800 h-2 rounded-full">
            <div class="bg-white h-2 rounded-full"
              [style.width]="downloadProgress() + '%'"></div>
          </div>
        }
        @if (downloaded()) {
          <p>¡Actualización descargada!</p>
          <button (click)="installNow()"
            class="px-4 py-2 bg-white text-blue-600 rounded">
            Reiniciar ahora</button>
        }
      </div>
    }
  `
})
export class UpdateNotificationComponent implements OnInit {
  private electronService = inject(ElectronService);
  updateAvailable = signal(false);
  updateVersion = signal('');
  downloading = signal(false);
  downloadProgress = signal(0);
  downloaded = signal(false);

  ngOnInit() {
    this.electronService.onUpdateAvailable(info => {
      this.updateAvailable.set(true);
      this.updateVersion.set(info.version);
    });
    this.electronService.onUpdateProgress(p => {
      this.downloading.set(true);
      this.downloadProgress.set(Math.round(p.percent));
    });
    this.electronService.onUpdateDownloaded(() => {
      this.downloading.set(false);
      this.downloaded.set(true);
    });
  }
}
```

Note:
La UX de actualización debe ser respetuosa: notificar sin interrumpir, mostrar progreso, dejar posponer. Nunca forzar la descarga en segundo plano sin consentimiento. El botón "Reiniciar ahora" llama a `autoUpdater.quitAndInstall()`, que cierra la app y la reabre con la nueva versión.

---

## Estrategia de Distribución Dual: Web + Escritorio

```typescript
// environment.web.ts
export const environment = {
  production: true,
  platform: 'web'
};

// environment.electron.ts
export const environment = {
  production: true,
  platform: 'electron'
};
```

```typescript
@Injectable({ providedIn: 'root' })
export class PlatformService {
  private electronService = inject(ElectronService);
  private http = inject(HttpClient);

  async openFile(): Promise<any> {
    if (this.electronService.isElectron()) {
      return this.electronService.openFile();
    } else {
      return this.openFileViaBrowser(); // fallback web
    }
  }

  async saveData(data: any): Promise<void> {
    if (this.electronService.isElectron()) {
      await this.electronService.saveFile(JSON.stringify(data));
    } else {
      await firstValueFrom(this.http.post('/api/data', data));
    }
  }
}
```

Note:
La misma base de código Angular puede funcionar como SPA web (Vercel/Netlify) y como app de escritorio. `PlatformService` abstrae las diferencias: en Electron usa diálogos nativos, en web usa `<input type="file">`. Esto maximiza el alcance del producto.

---

## Gestión de Dependencias Nativas

| Problema | Solución |
|---|---|
| Módulo nativo compilado para Node.js, no para Electron | <mark>`electron-rebuild`</mark> o `"npmRebuild": true` |
| `sharp`, `sqlite3`, `bcrypt` requieren compilación C++ | Instalar build tools del SO en CI |
| Dependencias nativas inflan el instalador | Buscar alternativas JS puras (`bcryptjs`) |

Note:
Los módulos nativos (C/C++) deben recompilarse para la versión de Node.js de Electron. `electron-builder` con `"npmRebuild": true` lo hace automáticamente, pero requiere herramientas de compilación en el entorno de build. En CI/CD, instalad `build-essential` (Linux), Visual Studio Build Tools (Windows) o Xcode CLT (macOS).

---

## Demo: Pipeline Completo

1. **Código** → `ng build --prod` genera `dist/`
2. **empaquetado** → `electron-builder` produce `release/Mi App Setup 1.0.0.exe`
3. **GitHub Release** → CI/CD adjunta instaladores automáticamente
4. **Usuario descarga** → instala desde `.exe` / `.dmg` / `.AppImage`
5. **Auto-update** → `electron-updater` detecta `v1.0.1`, descarga, aplica al reiniciar

Note:
Este pipeline cubre el ciclo completo. Cada paso está automatizado. La clave es la configuración inicial correcta de electron-builder y el workflow de GitHub Actions. Una vez configurado, publicar una nueva versión es tan simple como `git tag v1.0.1 && git push --tags`.

---

## Actividad en Clase

### Empaquetar "Gestor de Notas" como Instalador

**⏱️ Tiempo**: 90 minutos | **👥 Agrupamiento**: Parejas

1. Parte del proyecto Angular + Electron de la unidad 18
2. Instala electron-builder
3. Crea icono 1024x1024 y genera formatos con electron-icon-builder
4. Configura campo `"build"` en package.json para tu plataforma
5. Ejecuta `npm run build:electron:TU_PLATAFORMA`
6. Prueba el instalador generado: instala, abre, desinstala

Note:
El alumno debe experimentar el proceso completo de empaquetado. Probar el instalador en una máquina limpia (o VM) es crucial: "en mi máquina funciona" es el error más peligroso en distribución de software.

---

## Actividad Propuesta: CI/CD con GitHub Actions

**⏱️ Tiempo**: 90 minutos | **👥 Agrupamiento**: Individual

1. Sube el proyecto a un repositorio GitHub
2. Crea `.github/workflows/release.yml` con matrix strategy
3. Configura secrets en GitHub (puedes omitir firma de código)
4. Crea tag: `git tag v1.0.0-beta.1 && git push origin --tags`
5. Observa la ejecución en Actions y la release creada

Note:
Actividad avanzada que enseña automatización profesional. El alumno verá los 3 jobs ejecutándose en paralelo (Linux, macOS, Windows) y los instaladores apareciendo en la release. Esto es exactamente lo que hacen las empresas en producción.

---

## Buenas Prácticas

1. <mark>Versionado semántico estricto</mark>: MAJOR.MINOR.PATCH
2. <mark>Probar instaladores antes de publicar</mark>: siempre en VM limpia
3. <mark>Firma de código desde el primer release público</mark>
4. <mark>Canales de update separados</mark>: stable, beta, alpha
5. <mark>Ofrecer múltiples formatos</mark>: NSIS + portable, DMG, AppImage + DEB
6. <mark>Monitorizar tamaño del instalador</mark>: > 200 MB es un obstáculo

Note:
El versionado semántico no es opcional: electron-updater lo usa para decidir si actualizar. Los canales separados evitan que usuarios estables reciban betas. El tamaño del instalador importa: en países con conexiones lentas, cada MB extra reduce la tasa de descarga.

---

## Errores Frecuentes

1. <mark>No configurar `appId`</mark> o cambiarlo → duplicados, updates rotos
2. <mark>Incluir devDependencies</mark> en el paquete → instalador de cientos de MB
3. <mark>No recompilar módulos nativos</mark> → `Error: different Node.js version`
4. <mark>Rutas incorrectas en producción</mark>: `__dirname` apunta a `resources/app/`
5. <mark>No incrementar versión</mark> en package.json → electron-updater no detecta cambios
6. <mark>No probar en máquina limpia</mark> → "en mi máquina funciona"

Note:
El error 2 es muy común: sin patrones `files` correctos, el instalador incluye TypeScript, ESLint, electron-builder... todo. El error 4 es sutil: en desarrollo las rutas son relativas al proyecto, en producción están dentro del ASAR. Usad siempre `path.join(__dirname, ...)`.

---

## Resumen

- <mark>electron-builder</mark>: herramienta central, configuración en package.json (`"build"`)
- <mark>Formatos</mark>: NSIS/portable (Windows), DMG (macOS), AppImage/DEB/RPM (Linux)
- <mark>CI/CD</mark>: GitHub Actions con matrix strategy para 3 plataformas en paralelo
- <mark>Firma de código</mark>: Authenticode (Win), Apple Developer ID (Mac), GPG (Linux)
- <mark>electron-updater</mark>: auto-update desde GitHub Releases con UX respetuosa
- <mark>Distribución dual</mark>: mismo código → SPA web + app escritorio

Note:
El empaquetado es la última milla del desarrollo. Una app excelente con un instalador malo pierde usuarios. Una app mediocre con un instalador profesional gana confianza. Invertid tiempo en pulir esta fase.

---

## Próximos Pasos

1. **Unidad 20**: Proyecto Final "GesFlow" — integraréis todo: Figma, Angular, Chart.js, PDFMake, Electron y electron-builder
2. Vuestro proyecto final debe generar instaladores funcionales para al menos 1 plataforma

**📚 Para profundizar**:
- electron-builder Docs: https://www.electron.build/
- GitHub Actions Docs: https://docs.github.com/en/actions
- electron-updater: https://www.electron.build/auto-update
- Apple Notarization: https://developer.apple.com/documentation/security/notarizing_macos_software_before_distribution

Note:
En el proyecto final, aplicaréis TODO lo aprendido en el módulo. Vuestra app GesFlow debe compilarse con electron-builder y generar al menos un instalador funcional. El README debe documentar el proceso de build. ¡Es la culminación de 2 semanas de trabajo intensivo!

---

## Anexo: Costes de Distribución (2024-2025)

| Concepto | Coste |
|---|---|
| Certificado Authenticode OV (Windows) | ~200-300 €/año |
| Certificado Authenticode EV (Windows) | ~300-500 €/año |
| Apple Developer Program | <mark>99 €/año</mark> |
| Microsoft Store (particulares) | <mark>19 $ pago único</mark> |
| Snap Store (Linux) | <mark>Gratuito</mark> |
| GitHub Actions (repos públicos) | <mark>Gratuito</mark> |

Note:
Empezar sin firma de código es viable para betas privadas/alpha. Para el proyecto del módulo, no necesitáis certificados de pago. Pero en un producto comercial real, presupuestad estos costes desde el primer release. El coste total anual ronda los 400-600 € para distribuir en las 3 plataformas con firma.
