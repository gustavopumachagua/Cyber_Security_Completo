| **Inicio**         | **atrás 3**          | **Siguiente 5**                               |
| ------------------ | -------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_3_Linux.md) | [⏩](./2_5_Installation_and_Configuration.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [44. MacO](#44-maco)                                                             |
| [45. Tutorial de Mac para principiantes](#45-tutorial-de-mac-para-principiantes) |

---

# **MacOS**

## **44. MacO**

### 1) ¿Qué es macOS? (resumen rápido)

**macOS** es el sistema operativo de escritorio de Apple (Mac). Técnicamente es una **distribución** formada por: el **kernel XNU** (parte de Darwin), utilidades heredadas de BSD, frameworks Apple (Cocoa/AppKit, SwiftUI), y la capa gráfica/experiencia de usuario (Aqua). macOS integra características propias (Finder, Dock, Spotlight, Time Machine, Gatekeeper) y estrecha integración con el ecosistema Apple (iCloud, Handoff, AirDrop).

### 2) Historia y líneas principales (muy breve)

- **Darwin** = kernel XNU + capa BSD — la base libre/abierta.
- **macOS** (antes OS X / Mac OS X) combina Darwin con frameworks Apple y una interface GUI propietaria.
- Apple mantiene compatibilidad y añade capas (code signing, notarization, sandboxing) para seguridad y App Store.

### 3) Arquitectura general (componentes importantes)

- **Kernel (XNU)** — gestión de procesos, memoria, drivers, IPC.
- **Launchd** — init/system manager (arranque y gestión de servicios).
- **Userland (BSD + CoreUtils)** — utilidades Unix.
- **Frameworks Cocoa/AppKit / SwiftUI** — APIs para apps gráficas.
- **Window Server / Quartz / CoreGraphics** — renderizado de pantallas y ventanas.
- **Security layers:** System Integrity Protection (SIP), Gatekeeper, notarization, TCC (privacy), FileVault (disk encryption).
- **APFS (Apple File System)** — sistema de archivos moderno con snapshots, clones y cifrado nativo.
- **Servicios de usuario:** Finder, Dock, Spotlight, Notification Center, System Settings.

### 4) Experiencia de usuario (GUI) — funciones clave

- **Finder**: navegador de archivos (equivalente a Explorer).
- **Dock**: acceso rápido a apps/ventanas.
- **Spotlight** (`Cmd + Space`): búsqueda universal (archivos, apps, web, calculadora).
- **Mission Control / Escritorios**: gestión de múltiples escritorios y ventanas.
- **Continuity**: Handoff, Universal Clipboard, AirDrop, Sidecar (iPad como segunda pantalla).
- **Time Machine**: copias automáticas y restauración por versiones.
  Ejemplo práctico (GUI): para compartir un archivo por AirDrop — abre Finder → selecciona archivo → clic derecho → Share → AirDrop → elige el Mac/iPhone destino.

### 5) Sistema de archivos y almacenamiento (APFS y Time Machine)

- **APFS**: diseñado para SSD, snapshots (instantáneas) y clones (copias rápidas). Soporta cifrado por volumen o por archivo.
- **Time Machine**: en versiones recientes funciona con discos APFS; crea snapshots locales y backups incrementales.
  Comando útil:

```bash
# Ver volúmenes APFS
diskutil apfs list

# Crear snapshot (ejemplo administrativo)
sudo tmutil localsnapshot
# Ver snapshots de Time Machine
tmutil listlocalsnapshots /
```

### 6) Seguridad y privacidad (capas principales)

- **System Integrity Protection (SIP):** protege archivos del sistema y procesos firmados; se gestiona desde el modo Recovery (desactivarlo es inseguro).
- **Gatekeeper:** controla qué apps se permiten ejecutar — Apps notarizadas/firmadas vs apps descargadas de Internet.
- **Notarization & Code Signing:** los desarrolladores firman sus apps y las envían a Apple para notarización, lo que reduce warnings de seguridad al instalar.
- **FileVault:** cifrado de disco completo (XTS-AES 128).
- **TCC (Transparency, Consent, Control):** permisos de privacidad (micrófono, cámara, accesos a Documentos/Escritorio).
- **Sandboxing & entitlements:** para apps del App Store (limitación de recursos y APIs).
  Comandos de comprobación:

```bash
# Estado de Gatekeeper
spctl --status

# Ver firma de una app
codesign -dv --verbose=4 /Applications/Visual\ Studio\ Code.app

# Ver FileVault (estado)
fdesetup status
```

### 7) Administración básica (Terminal + GUI) — tareas frecuentes

#### Usuarios y cuentas

```bash
# Ver usuarios locales
dscl . list /Users

# Crear usuario (ejemplo)
sudo sysadminctl -addUser juan -fullName "Juan Perez" -password 'Pass1234' -home /Users/juan

# Añadir a grupo admin
sudo dseditgroup -o edit -a juan -t user admin
```

#### Servicios y arranque (launchd)

- Los _agents_ y _daemons_ se manejan con `launchctl` y con archivos plist en `/Library/LaunchDaemons` o `~/Library/LaunchAgents`.

```bash
# Listar jobs de launchd
launchctl list

# Cargar/descargar un daemon plist
sudo launchctl bootout system /Library/LaunchDaemons/com.example.daemon.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/com.example.daemon.plist
```

#### Gestión de discos y volúmenes

```bash
# Listar discos y particiones
diskutil list

# Formatear un disco a APFS (¡peligro, borra datos!)
sudo diskutil eraseDisk APFS MyDisk disk2
```

#### Actualizaciones y paquetes

- **Software Update** (CLI):

```bash
# Buscar e instalar actualizaciones del sistema
softwareupdate --list
sudo softwareupdate -i -a
```

- **Homebrew** (gestor de paquetes popular en macOS):

```bash
# instalar Homebrew (resumido)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# instalar paquetes
brew install wget htop
```

### 8) Desarrollo en macOS (Xcode, runtimes, notarization)

- **Xcode**: IDE oficial (compiladores clang/llvm, simuladores iOS, Interface Builder).
- **Swift / Objective-C / Cocoa / SwiftUI**: frameworks para desarrollar apps nativas.
- **Catalyst**: portar iPad apps a macOS.
- **Notarización**: flujo típico — `codesign` → `xcrun notarytool submit` → `xcrun stapler staple` (firmar, enviar para notarizar, insertar ticket).
  Ejemplo (firma básica):

```bash
# Firmar una app
codesign --deep --force --verify --verbose --sign "Developer ID Application: Tu Nombre (ID)" /path/to/MyApp.app

# Verificar signature
codesign --verify --deep --strict --verbose=2 /path/to/MyApp.app
spctl --assess --type execute --verbose /path/to/MyApp.app
```

> Nota: notarization requiere cuenta Apple Developer y suele usarse para distribución fuera del App Store.

### 9) Compatibilidad Apple Silicon & Rosetta 2

- **Apple Silicon (M1/M2/…):** arquitectura ARM (aarch64).
- **Rosetta 2:** traductor binario para ejecutar apps x86_64 en Apple Silicon; permite incompatibilidades temporales hasta portar apps a arquitectura Universal.
  Comando para identificar arquitectura de un binario:

```bash
lipo -info /Applications/SomeApp.app/Contents/MacOS/SomeApp
# o
file /Applications/SomeApp.app/Contents/MacOS/SomeApp
```

### 10) Terminal & utilidades Unix (comandos prácticos)

macOS incluye utilidades BSD y muchas herramientas GNU se instalan via Homebrew. Ejemplos útiles:

```bash
# Información del hardware
system_profiler SPHardwareDataType

# Estado de la red
ifconfig
netstat -rn

# Spotlight index (buscar/forzar reindex)
mdfind "nombre archivo"
sudo mdutil -E /   # reindexar

# Time Machine (gestión)
tmutil listlocalsnapshots /
tmutil status

# Reiniciar el servicio de escritorio (dock/finder) — útil para aplicar cambios UI
killall Dock
killall Finder
```

### 11) Debugging y logs

- **Console.app** (GUI) para logs del sistema y aplicaciones.
- **Terminal**:

```bash
# Ver logs del sistema (con journal-like)
log show --predicate 'process == "kernel"' --last 1h
# Seguir logs en tiempo real
log stream --predicate 'eventMessage contains "error"'
```

- Para problemas gráficos o de apps: revisar `~/Library/Logs/` y `/var/log/`.

### 12) Backup y recuperación

- **Time Machine**: configuración GUI en System Settings → Time Machine.
- **Restauración desde Internet Recovery** (Cmd+R / Option+Cmd+R on boot) para reinstalar macOS.
- **Snapshots** (APFS) permiten rollback local.

### 13) Seguridad práctica — checklist rápido (para admins/usuarios)

1. **Activa FileVault** para cifrado completo.
2. **Habilita SIP** y evita desactivarlo salvo necesidad.
3. **Usa cuentas no-admin** para actividades diarias.
4. **Activa Gatekeeper** (permite App Store + Identified Developers).
5. **Mantén macOS y Xcode actualizados** (parches de seguridad).
6. **Usa notificaciones de privacidad (TCC)** y revisa qué apps tienen acceso a cámara/micrófono/archivos.
7. **Configura backups** (Time Machine + copia externa inmutable si es crítico).

### 14) Casos prácticos — comandos + escenarios

#### A) Comprobar si una app está firmada y qué autoridad la firmó

```bash
codesign -dv --verbose=4 /Applications/Google\ Chrome.app
spctl --assess --verbose /Applications/Google\ Chrome.app
```

#### B) Forzar reindexar Spotlight (cuando no encuentra archivos)

```bash
sudo mdutil -E /       # borra índice y fuerza reindexado
sudo mdutil -i on /    # activar index (si está desactivado)
```

#### C) Activar FileVault desde Terminal

```bash
sudo fdesetup enable
# te pedirá autorización y te mostrará la clave de recuperación si procede
```

#### D) Ver hardware y modelo (útil para inventario)

```bash
system_profiler SPHardwareDataType
# o en una sola línea:
system_profiler SPHardwareDataType | awk -F: '/Model Identifier|Processor Name|Memory/ {print}'
```

#### E) Instalar paquetes útiles con Homebrew

```bash
brew install --cask visual-studio-code
brew install git htop wget
```

### 15) Buenas prácticas para desarrolladores macOS

- **Firmar y notarizar** builds para evitar problemas con Gatekeeper.
- **Probar en Apple Silicon y x86** (si soportas ambas).
- **Usar entitlements mínimos** (privilegios por necesidad).
- **Automatizar CI** para build, signing y notarization (ej. GitHub Actions + notarytool).
- **Proveer actualización (Sparkle, in-app updates)** para distribuciones fuera del App Store.

### 16) Limitaciones y consideraciones

- macOS es **cerrado/proprietario** en su capa GUI y frameworks — integraciones profundas requieren Apple Developer program.
- Apple controla la **firma/notarización**: para distribución a gran escala fuera de la App Store necesitas gestionar certificados y perfiles.
- Cambios en políticas Apple (Gatekeeper, notarization, entitlements) pueden afectar flujos de distribución — revisa documentación Apple Developer regularmente.

### 17) Recursos para profundizar

- **Apple Developer Documentation** (API, notarization, security guidelines).
- **man pages** y `xcrun` para herramientas de línea de comando.
- Blogs y foros: Stack Overflow, Apple Developer Forums, r/macadmins (Slack/Reddit), Hacker News para novedades.

---

[🔼](#índice)

---

## **45. Tutorial de Mac para principiantes**

### 1) Primer arranque y configuración inicial

Cuando enciendes un Mac por primera vez verás el asistente de configuración. Pasos clave:

1. **Selecciona idioma y zona horaria.**
2. **Conéctate a Wi-Fi.**
3. **Inicia sesión con Apple ID (o crea uno):** usar Apple ID te permite iCloud, App Store, sincronización de contraseñas (Keychain) y Find My.

   - Crear Apple ID: ` menu > System Settings > Sign in` → _Create Apple ID_.

4. **Configura Touch ID / Face ID (si tu Mac lo soporta)** y crea un **PIN** o contraseña fuerte.
5. **Activa FileVault** (te lo solicitará o puedes activar luego): cifra tu disco.
6. **Elige sincronización con iCloud** (Documentos, Escritorio, Fotos).

**Ejemplo práctico:** Si tienes un iPhone, al iniciar sesión con el mismo Apple ID verás tus contactos, calendario y fotos empezar a sincronizarse automáticamente.

### 2) Orientación por la interfaz: escritorio, Dock y Finder

- **Barra de menús (arriba):** menú , reloj, iconos de estado (Wi-Fi, Bluetooth, Spotlight).
- **Dock (abajo por defecto):** accesos rápidos a apps; arrastra apps para anclarlas.
- **Finder:** el "Explorador" de macOS para archivos. Atajos útiles:

  - `Cmd + N` → nueva ventana Finder.
  - `Cmd + Shift + N` → nueva carpeta.
  - `Cmd + Delete` → mover a la papelera.
  - `Space` → Quick Look (vista previa sin abrir).

**Consejo:** usa **Stacks** (clic derecho en escritorio → Use Stacks) para organizar archivos por tipo automáticamente.

### 3) Spotlight y búsqueda rápida

- Abre Spotlight con `Cmd + Space`.
- Escribe el nombre de un archivo, app, conversión o cálculo rápido.
  **Ejemplo:** escribe `resume.pdf` o `72 USD in EUR` (Spotlight hace conversiones y cálculos).

### 4) Configuración esencial en System Settings (antes System Preferences)

Ruta: ` > System Settings`
Ajustes recomendados para empezar:

- **General > Software Update:** activa actualizaciones automáticas.
- **Privacy & Security:** revisa permisos de cámara/micrófono y Gatekeeper (permite App Store + apps identificadas).
- **Users & Groups:** crea una cuenta estándar para uso diario y deja la cuenta admin sólo para instalar/configurar.
- **Displays:** ajusta resolución y escalado.
- **Trackpad:** configura gestos (deslizar entre apps, Mission Control).

### 5) iCloud y Apple ID — sincronización

- `System Settings > [tu nombre]` para ver iCloud.
- **iCloud Drive** puede sincronizar Escritorio y Documentos entre Macs.
  **Ejemplo:** activa _Desktop & Documents Folders_ para acceder a esos archivos desde tu iPad o iPhone.

**Gestionar almacenamiento:** `System Settings > [tu nombre] > iCloud > Manage` para ver qué ocupa espacio y comprar más si es necesario.

### 6) Apps básicas y App Store

- **Safari** (navegador), **Mail**, **Calendar**, **Notes**, **Messages**, **FaceTime**, **Photos**.
- Abre App Store para instalar apps: `App Store` → busca → `Get` / `Buy`.
  **Consejo:** muchas apps populares también están en Homebrew (ver sección “terminal”).

### 7) AirDrop, Handoff y Continuity (integración Apple)

- **AirDrop**: compartir archivos con iPhone/Mac cercanos.

  - Finder → Share → AirDrop → selecciona dispositivo.

- **Handoff** permite continuar en Mac lo que empezaste en iPhone (p. ej. Mail o Safari).
- **Universal Clipboard**: copia en iPhone → pega en Mac (mismo Apple ID y Wi-Fi/Bluetooth activados).

**Ejemplo práctico AirDrop:** en iPhone elige la foto → Share → AirDrop → selecciona tu Mac y acepta en la notificación.

### 8) Time Machine — copias de seguridad

- Conecta un disco externo o usa un NAS compatible.
- `System Settings > General > Time Machine` → `Select Backup Disk` → elige disco.
- Time Machine hace backups incrementales automáticos y permite “retroceder” a versiones anteriores de archivos.

**Ejemplo:** si borras por error `documento.docx`, abre la carpeta en Finder → `Enter Time Machine` (en menu) y restaura la versión anterior.

### 9) Seguridad y privacidad importantes

- **FileVault:** cifra disco completo. Activar: `System Settings > Privacy & Security > FileVault`. Guarda la clave de recuperación.
- **Gatekeeper:** deja activado para evitar apps no firmadas. `spctl --status` (Terminal) muestra estado.
- **SIP (System Integrity Protection):** protege archivos del sistema; no lo desactives salvo que sepas qué haces.
- **Revisa permisos de apps:** `System Settings > Privacy & Security` → accesos (Camera, Microphone, Files and Folders).

### 10) Mantenimiento básico y actualizaciones

- **Actualizar macOS:** ` > System Settings > General > Software Update`.
- **Reiniciar periódicamente** (soluciona muchos problemas).
- **Liberar espacio:** `Apple menu > About This Mac > Storage > Manage` para sugerencias (almacenamiento optimizado, vaciar basura).

### 11) Atajos de teclado y gestos útiles (rápido)

- `Cmd + Space` — Spotlight.
- `Cmd + Tab` — cambiar apps.
- `Cmd + Q` — salir app.
- `Cmd + W` — cerrar ventana.
- `Cmd + ,` — abrir preferencias de la app.
- `Cmd + Shift + 3` — captura pantalla completa; `Cmd + Shift + 4` — seleccionar área.
- Trackpad: deslizar con tres dedos = Mission Control (configurable).

### 12) Finder avanzado: etiquetas, vistas y compartir

- **Etiquetas (tags):** clic derecho → Tags → añadir color/etiqueta. Útil para filtrar.
- **Compartir carpeta:** clic derecho sobre carpeta → `Get Info` → `Shared folder` (en entornos con File Sharing activado).
- **Conectar a servidor SMB/AFP:** Finder → Go → Connect to Server (`Cmd + K`) → `smb://server-name/share`.

### 13) Instalar apps y utilidades con Homebrew (Terminal)

Homebrew facilita instalar herramientas Unix y apps:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
# luego instalar paquetes:
brew install --cask google-chrome visual-studio-code
brew install htop wget git
```

**Ejemplo:** `brew install --cask google-chrome` instala Chrome.

### 14) Primeros pasos con Terminal (seguridad y comandos básicos)

Abre Terminal (`Applications > Utilities > Terminal`).

Comandos seguros para comenzar:

```bash
# ver información de sistema
uname -a
system_profiler SPHardwareDataType

# listar archivos
ls -la ~

# abrir carpeta en Finder desde Terminal
open ~/Documents
```

No ejecutes comandos que no entiendas ni pegues `curl ... | sh` sin revisar.

### 15) Imprimir, escanear y compartir impresora

- `System Settings > Printers & Scanners` → `+` para añadir impresora (AirPrint o IP).
- Compartir impresora vía `Share this printer` en las opciones de la impresora.

### 16) Problemas comunes y soluciones rápidas

- **Mac lento:** reinicia, revisa Activity Monitor (`Cmd + Space` → Activity Monitor), cierra procesos que consumen CPU.
- **Wi-Fi no conecta:** apagar/encender Wi-Fi, reiniciar router, `System Settings > Network` → eliminar y volver a añadir la red.
- **Impresora no imprime:** elimina e instala nuevamente; revisa drivers del fabricante.
- **No arranca/Kernel panic:** reinicia en **Safe Mode** (mantén pulsada la tecla **Shift** al arrancar) y revisa logs en Console.app.

### 17) Migrar desde Windows o mover datos

- Usa **Migration Assistant** (`Applications > Utilities > Migration Assistant`) para transferir usuarios, documentos y apps desde Windows o otro Mac por red o disco duro.

### 18) Consejos de productividad

- Usa **Spaces** (multiple desktops) para separar trabajo / ocio.
- Configura **Hot Corners** (`System Settings > Desktop & Dock > Hot Corners`) para mostrar escritorio o activar Mission Control con un movimiento de ratón.
- Activa **Do Not Disturb / Focus** para evitar interrupciones mientras trabajas.

### 19) Accesibilidad (si la necesitas)

- `System Settings > Accessibility` contiene opciones para VoiceOver, aumentar contrastes, dictado, control por voz y lecturas de pantalla.

### 20) Qué aprender después (siguiente nivel)

- **Automatización:** Shortcuts app (atajos) y AppleScript.
- **Desarrollo:** Xcode, Homebrew, Terminal + Git.
- **Administración avanzada:** perfiles de configuración (MDM), Apple Business Manager / Apple School Manager.
- **Seguridad avanzada:** gestión de certificados, notarización y herramientas EDR para Mac.

### 21) Mini-cheat-sheet imprimible (resumen rápido)

- Spotlight: `Cmd + Space`
- Cambiar apps: `Cmd + Tab`
- Captura pantalla: `Cmd + Shift + 3/4`
- Abrir Terminal: `Cmd + Space` → type `Terminal`
- Time Machine: `System Settings > General > Time Machine`
- FileVault: `Privacy & Security > FileVault`
- Homebrew install: comando en sección 13

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**          | **Siguiente 5**                               |
| ------------------ | -------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_3_Linux.md) | [⏩](./2_5_Installation_and_Configuration.md) |
