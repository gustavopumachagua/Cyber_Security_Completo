| **Inicio**         | **atrás 1**                      | **Siguiente 3**      |
| ------------------ | -------------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./2_1_Operating_Systems.md) | [⏩](./2_3_Linux.md) |

---

## **Índice**

| Temario                                                                                                                                |
| -------------------------------------------------------------------------------------------------------------------------------------- |
| [35. seguridad de Windows](#35-seguridad-de-windows)                                                                                   |
| [36. Tutorial completo de Windows 11 aprender y dominar Windows 11](#36-tutorial-completo-de-windows-11-aprender-y-dominar-windows-11) |
| [37. Explora las publicaciones más importantes sobre Windows](#37-explora-las-publicaciones-más-importantes-sobre-windows)             |

---

# **Windows**

## **35. seguridad de Windows**

### 1) Panorama general — ¿qué protegemos y por qué?

Windows es objetivo frecuente (escritorio, servidores y endpoints). Las amenazas típicas: **phishing, malware/ransomware, robo de credenciales, movimiento lateral (RDP/SMB), explotación de vulnerabilidades, y malas configuraciones**. La defensa en Windows combina: **identidad**, **endpoints**, **red**, **detección/monitorización**, **hardening** y **resiliencia (backup/recuperación)**.

### 2) Principales capas y herramientas integradas (qué hacen y ejemplos)

#### a) Antivirus / Antimalware — Microsoft Defender Antivirus

- **Qué hace:** protección en tiempo real, cloud lookups, análisis on-demand y bloqueo de amenazas conocidas/behaviorales.
- **Ejemplo práctico:** comprobar estado y lanzar un escaneo rápido:

```powershell
Get-MpComputerStatus
Start-MpScan -ScanType QuickScan
```

#### b) Endpoint Detection & Response — Microsoft Defender for Endpoint (EDR)

- **Qué hace:** telemetría avanzada, detección basada en comportamiento, alertas y respuesta remota (isolation, remediation).
- **Ejemplo:** reglas ASR (Attack Surface Reduction) que bloquean macros maliciosas o procesos hijos creados por Office.

#### c) Firewall (Windows Defender Firewall)

- **Qué hace:** control de tráfico entrante/saliente por reglas.
- **Ejemplo (bloquear RDP - puerto 3389):**

```powershell
New-NetFirewallRule -DisplayName "Block RDP inbound" -Direction Inbound -Protocol TCP -LocalPort 3389 -Action Block
Get-NetFirewallRule | Where-Object Enabled -eq "True"
```

#### d) Cifrado — BitLocker

- **Qué hace:** cifrado de disco completo (protege datos en reposo).
- **Buenas prácticas:** activar con TPM, guardar claves de recuperación en Azure AD o AD.
- **Comprobar estado:**

```powershell
Get-BitLockerVolume
```

#### e) Integridad de arranque — Secure Boot + TPM

- **Qué hacen:** Secure Boot evita cargas de loaders no firmados; TPM almacena claves y permite BitLocker con confianza de hardware.
- **Comprobar (requiere privilegios):**

```powershell
Confirm-SecureBootUEFI   # devuelve True/False en sistemas UEFI
Get-Tpm                   # info del TPM si está disponible
```

#### f) Control de cuentas y elevación — UAC y cuentas locales

- **Qué hacen:** UAC separa privilegios; evitar usar cuentas admin para tareas diarias.
- **Mejora:** aplicar LAPS (Local Administrator Password Solution) para evitar contraseña local administrada por humanos.

#### g) Allowlisting / App control — AppLocker / WDAC

- **Qué hacen:** permiten políticas para que solo software autorizado pueda ejecutarse (muy efectivo contra malware/ransomware).
- **Ejemplo:** en entornos corporativos crear políticas que permitan solo ejecutables firmados.

#### h) Protección contra ransomware — Controlled Folder Access & backups

- **Qué hace:** Controlled Folder Access bloquea cambios por procesos no confiables en carpetas críticas (Documentos, Desktop).
- **Ejemplo (activar GUI):** Windows Security → Virus & threat protection → Ransomware protection → Manage Controlled folder access.

#### i) Virtualization-based security (VBS), Credential Guard, Device Guard

- **Qué hacen:** usan hipervisor para aislar credenciales y evitar robo (Credential Guard protege LSA/NTLM hashes).
- **Cuándo:** activarlo en equipos corporativos compatibles para proteger secretos de dominio.

#### j) Windows Update / Patch Management

- **Qué hace:** distribuir parches críticos. Gestión con **Windows Update for Business**, **WSUS** o **Intune**.
- **Comprobar parches instalados:**

```powershell
Get-HotFix
```

### 3) Identidad y acceso (la primera línea de defensa)

- **Azure AD / Active Directory + MFA:** usar Azure AD con MFA obligatorio reduce compromiso por phishing.
- **Windows Hello for Business / FIDO2:** autenticación sin contraseña (passkeys) más segura.
- **Privileged Access Workstations (PAW) y principio de menor privilegio:** no usar admins locales para correos/navegación.
- **Ejemplo práctico:** exigir MFA para acceso a Exchange/Office 365 y bloquear accesos desde países sin operaciones.

### 4) Reducción de superficie y hardening (configuraciones concretas)

- **Deshabilitar SMBv1:** SMBv1 es inseguro y debe desactivarse (mitiga WannaCry).

  - Ejemplo: usar políticas o PowerShell/Group Policy para remover soporte SMBv1 en clientes/servidores.

- **Bloquear RDP al público:** permitir RDP solo por VPN o acceso bastionado (Jump Box) y usar Network Level Authentication (NLA).
- **Aplicar GPOs de seguridad** (CIS Benchmarks / Microsoft Security Baselines) para: políticas de contraseñas, bloqueo de cuenta, deshabilitar funciones innecesarias, restricciones de scripts, etc.

### 5) Monitorización, Telemetría y Logging (detección temprana)

- **Event Logs (Windows Event Viewer):** Security (4624/4625 logon events), System, Application.
- **Sysmon (Sysinternals):** genera eventos ricos (process create EventID 1, network connections EventID 3) ideales para detección en SIEM.
- **Forwarding a SIEM / Microsoft Sentinel:** centraliza y correlaciona eventos para análisis.
- **Ejemplo básico:** detectar PowerShell lanzado desde Office (hijo sospechoso) con reglas en SIEM o Defender for Endpoint.

### 6) Respuesta ante incidentes y forense (pasos y artefactos)

**Flujo de respuesta rápido:**

1. **Aislar** el host (desconectar de red o usar aislamiento remoto EDR).
2. **Recolectar** artefactos volátiles: procesos, conexiones, memoria (si es posible). Comandos útiles:

```powershell
Get-Process
netstat -ano
Get-Service
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Run
wevtutil qe Security /q:"*[System[(EventID=4624 or EventID=4625)]]" /f:text
```

3. **Hacer imagen forense** del disco (bit-for-bit) y preservar cadena de custodia.
4. **Analizar** Sysmon/Event logs, prefetch, LNKs, tareas programadas, registros de PowerShell (Module logging, ScriptBlockLogging), registros de Defender.
5. **Erradicar** y **recuperar** desde backups limpios; revisar y endurecer configuración antes de reintegrar.

**Artefactos críticos:** Event IDs de logon (4624/4625), creación de procesos (Sysmon 1), conexiones de red (Sysmon 3), eventos de servicio y tareas programadas, Run keys en registro, MFT, Prefetch, objetos MBR/UEFI si sospecha bootkit.

### 7) Mitigaciones específicas contra ransomware (ejemplos)

- **Backups offline / inmutables** (air-gapped o snapshots inmutables).
- **Least privilege**: eliminar derechos admin de usuarios estándar; administrar apps con allowlisting.
- **Segmentación de red**: limitar movimiento lateral; separar backups del resto de la red.
- **Controlled Folder Access + EDR** + aplicación de ASR rules.
- **Prueba de restauración** periódica para validar recuperación.

### 8) Comprobaciones rápidas y comandos útiles (auditoría inicial)

Ejecuta estos checks en PowerShell (como administrador):

- Defender status & quick scan:

```powershell
Get-MpComputerStatus
Start-MpScan -ScanType QuickScan
```

- Firewall rules activas:

```powershell
Get-NetFirewallRule | Where-Object Enabled -eq "True"
```

- BitLocker status:

```powershell
Get-BitLockerVolume
```

- Lista de actualizaciones instaladas:

```powershell
Get-HotFix
```

- Miembros del grupo Administradores local:

```powershell
Get-LocalGroupMember -Group "Administrators"
```

- Puertos abiertos (netstat):

```powershell
netstat -ano | Select-String LISTENING
```

- Comprobar si Secure Boot / TPM están activos:

```powershell
Confirm-SecureBootUEFI
Get-Tpm
```

### 9) Buenas prácticas operativas (prioridad práctica)

1. **Forzar MFA** para todos los accesos remotos/servicios en la nube.
2. **Aplicar parches** con cadencia regular; automatizar con WSUS/Intune/Windows Update for Business.
3. **Eliminar/administrar cuentas locales** y usar LAPS para credenciales locales.
4. **Implantar EDR** (Defender for Endpoint) y habilitar telemetría completa.
5. **Allowlisting** (AppLocker/WDAC) en equipos críticos.
6. **Backups inmutables y pruebas de recuperación** regulares.
7. **Segmentación y mínimo acceso** para servicios y administración remota.
8. **Harden baseline**: importar CIS / Microsoft Security Baselines via GPO/Intune.
9. **Registrar y enviar logs** a SIEM (Sysmon + Event Forwarding).
10. **Formación a usuarios**: phishing, gestión de contraseñas y uso de MFA.

### 10) Políticas y controles a implementar vía GPO/Intune (ejemplos)

- **Account lockout policy:** bloquear tras 5 intentos fallidos.
- **Password policy:** longitud mínima 12+ y complejidad.
- **Disable SMBv1** en clientes y servidores.
- **Configure Windows Defender ASR rules**: bloquear ejecución desde Office, bloquear procesos ofuscados.
- **Habilitar Audit Policy & Advanced Audit Policy** para eventos de log-on, privilegios, object access.

### 11) Herramientas recomendadas (para defender y responder)

- **Sysinternals Suite (ProcMon, Autoruns, Sysmon)**
- **PowerShell (remoting & scripting para auditoría)**
- **Microsoft Defender for Endpoint / Defender Antivirus**
- **Microsoft Intune / Autopilot** (gestión y despliegue)
- **LAPS** para passwords locales
- **Microsoft Sentinel / SIEM** para correlación de logs
- **Herramientas de forense**: FTK, EnCase, or open-source: Autopsy, Volatility (memoria)

### 12) Checklist priorizado — lo que haría primero en una organización pequeña/mediana

1. Habilitar MFA en todas las cuentas críticas + revisar privilegios.
2. Asegurar Windows Update / parches automáticos.
3. Instalar y configurar Defender + activar EDR si es posible.
4. Habilitar BitLocker y guardar claves en Azure AD/AD.
5. Desactivar SMBv1; restringir RDP.
6. Implementar backups inmutables y probar restauraciones.
7. Desplegar Sysmon y enviar logs a SIEM/central.
8. Aplicar GPO/Intune con Security Baseline + App control en equipamiento crítico.
9. Establecer playbook de respuesta a incidentes (aislamiento, recolección, restauración).
10. Formación de usuarios sobre phishing y contraseñas.

### 13) Ejemplo práctico: cómo activar BitLocker (pasos seguros)

1. Comprobar TPM: _Settings → Update & Security → Device security_ o `Get-Tpm`.
2. Habilitar BitLocker desde: _Control Panel → BitLocker Drive Encryption → Turn on BitLocker_.
3. Guardar clave de recuperación en **Azure AD** (dispositivo unido) o imprimirla/guardarla offline.
4. Iniciar cifrado y verificar con `Get-BitLockerVolume`.

> Consejo: en empresas automatizar con Intune/Endpoint Configuration Manager para desplegar políticas y asegurar respaldo de claves.

### 14) Recomendación final — enfoque en defensa en profundidad

No hay una sola medida que garantice seguridad. Combina:

- **Identidad fuerte (MFA, SSO)**,
- **Endpoints protegidos (EDR + allowlisting + BitLocker)**,
- **Red segmentada y controlada (firewall, VPN)**,
- **Parches y políticas (GPO/Intune)**,
- **Monitorización central (Sysmon + SIEM)**,
- **Backup y pruebas de recuperación** y
- **Formación del personal**.

---

[🔼](#índice)

---

## **36. Tutorial completo de Windows 11 aprender y dominar Windows 11**

### 1) Ruta rápida de aprendizaje (qué hacer en orden)

1. **Instalación / primer arranque** — conocer requisitos y elegir cuenta.
2. **Navegar la interfaz** — Start, Taskbar, Quick Settings, Action Center, File Explorer.
3. **Productividad básica** — Snap layouts, escritorios virtuales, atajos.
4. **Seguridad y copias** — Windows Update, Defender, BitLocker, recuperación.
5. **Optimización y mantenimiento** — Task Manager, Storage Sense, drivers.
6. **Avanzado** — PowerShell, WSL2, Hyper-V, PowerToys, winget.

### 2) Instalación y primer arranque (paso a paso)

#### Requisitos mínimos (resumen)

- CPU compatible (64-bit), 4 GB RAM (recomendado 8+), 64 GB disco, TPM 2.0, UEFI con Secure Boot.
  _(si tu PC no cumple, puede haber formas de instalar, pero son menos recomendadas)._

#### Actualizar desde Windows 10

- `Settings > Windows Update > Check for updates` → si el PC es compatible aparecerá la opción **Upgrade to Windows 11**.

#### Instalación limpia con USB (pasos)

1. Descarga **Windows 11 Media Creation Tool** o la ISO desde Microsoft.
2. Crea USB booteable (herramienta Microsoft o Rufus).
3. Arranca desde USB (UEFI) → instala → particiona y elige “Custom” para limpieza.
4. En primer arranque: elige **Microsoft account** (o cuenta local), configura privacidad, Windows Hello (recomendado) y OneDrive.

### 3) Primeros ajustes recomendados (primeros 15 minutos)

- **Cuentas:** `Settings > Accounts` → decide Microsoft account (sincroniza OneDrive, contraseñas) o cuenta local.
- **Windows Update:** `Settings > Windows Update` → activar actualizaciones automáticas y poner horarios de actividad.
- **Privacidad:** `Settings > Privacy & security` → revisar permisos (ubicación, micrófono, cámara).
- **Windows Hello:** configura reconocimiento facial, PIN o huella para más seguridad.
- **OneDrive:** inicia sesión para sincronizar Documentos/Escritorio si quieres copia automática.

### 4) Interfaz y productividad (qué usar y ejemplos)

#### Escritorio, Start y Taskbar

- Start: **Pinned apps**, Recommended (archivos recientes).
- Taskbar: íconos centrados, anclaje/desanclaje.
  **Ejemplo:** ancla Teams, Edge y File Explorer para acceso rápido.

#### Quick Settings / Notification Center

- `Win + A` para **Quick Settings** (Wi-Fi, sonido, Bluetooth).
- `Win + N` abre el Centro de notificaciones.

#### Snap layouts / Snap groups (multitarea poderosa)

- Pasa el cursor sobre el botón Maximizar para ver **layouts**: 2-column, 3-column, etc.
- **Ejemplo:** en una pantalla 16:9 usa layout 3 columnas para editar doc + navegador + chat.

#### Escritorios virtuales

- `Win + Tab` → `New desktop`.
- **Ejemplo:** Desktop1 = trabajo, Desktop2 = ocio; cambia con `Ctrl + Win + ←/→`.

#### File Explorer moderno

- Panel lateral (OneDrive), **Quick Access** y pestañas (si tu build las soporta).

  **Consejo:** usa `Win + E` para abrir y `Alt + D` para ir a la barra de direcciones.

### 5) Atajos de teclado imprescindibles

- `Win` = abrir Start
- `Win + A` = Quick Settings
- `Win + N` = Notificaciones
- `Win + Tab` = Task View / Virtual desktops
- `Win + Z` = abrir Snap layouts (directo)
- `Win + V` = portapapeles histórico (activa en `Settings > Clipboard`)
- `Win + .` = Emoji & Kaomoji panel
- `Alt + Tab` = cambiar entre ventanas
- `Ctrl + Shift + Esc` = Task Manager

### 6) Configuración avanzada del sistema (Settings profundo)

#### Settings importante

`Settings > System` (Display, Sound, Storage, Power)
`Settings > Bluetooth & devices` (impresoras, stylus, pen)
`Settings > Network & internet` (Wi-Fi, VPN, proxy)
`Settings > Apps` (Apps & features, Default apps)
`Settings > Accessibility` (modo alto contraste, Narrator, magnifier)
`Settings > Privacy & security` (diagnostics, camera, microphone)

**Ejemplo:** cambiar navegador por defecto → `Settings > Apps > Default apps` → buscar “Edge” o “Chrome” y asignar.

### 7) Seguridad y privacidad (lo esencial)

#### Windows Security (Defender)

`Start > Windows Security` incluye: **Virus & threat protection**, **Account protection**, **Firewall & network protection**, **Device security**, **App & browser control**.

Comandos útiles:

```powershell
# Estado de Defender
Get-MpComputerStatus

# Escaneo rápido
Start-MpScan -ScanType QuickScan
```

#### BitLocker (cifrado disco)

- `Settings > Privacy & security > Device encryption` o **Control Panel > BitLocker**.
- Guarda clave de recuperación en tu cuenta Microsoft / Azure AD.

Comando de verificación:

```powershell
Get-BitLockerVolume
```

#### Integridad de arranque (TPM + Secure Boot)

- Asegúrate de tener **TPM 2.0** y **Secure Boot** activados en UEFI (BIOS).

#### Control de carpeta (ransomware)

- `Windows Security > Virus & threat protection > Ransomware protection` → activar **Controlled folder access** para proteger Documentos/Escritorio.

### 8) Copias, recuperación y mantenimiento

#### Copias y recuperación

- **OneDrive** para sincronizar archivos personales.
- **File History** (Settings > Backup > Back up using File History) para versiones de archivos.
- **System Restore / Restore point**: crea puntos antes de cambios grandes.
- **Reset this PC**: `Settings > System > Recovery` → Reset / Keep files / Remove everything.
- **Create a recovery drive** (USB): `Create a recovery drive` (buscar en Start).

#### Verificación y reparación

- SFC y DISM:

```powershell
# Ejecutar en PowerShell (Admin)
sfc /scannow
DISM /Online /Cleanup-Image /RestoreHealth
```

### 9) Rendimiento y mantenimiento práctico

#### Task Manager & Resource Monitor

- `Ctrl + Shift + Esc` → ficha **Performance** y **Startup** (desactivar apps que ralentizan arranque).
- `Resource Monitor` (desde Task Manager) para I/O y uso de red.

#### Storage Sense y limpieza

- `Settings > System > Storage` → activar **Storage Sense** para limpiar temporales automáticamente.
- `cleanmgr` o `Storage > Temporary files` para liberar espacio.

#### Drivers

- `Device Manager` para actualizar/retroceder drivers. Mejor usar la web oficial del fabricante para drivers GPU (NVIDIA/AMD/Intel).
- Actualización masiva con **Windows Update** o herramientas del fabricante.

### 10) Networking y recursos compartidos (ejemplos)

- **Mapear unidad de red:** `This PC > Map network drive` o PowerShell:

```powershell
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\server\share" -Persist
```

- **Compartir carpeta:** botón derecho > Properties > Sharing.
- **RDP:** `Settings > Remote Desktop` → habilitar (uso seguro: VPN, NLA y no exponer RDP a Internet).

### 11) Herramientas de productividad y personalización (PowerToys, Terminal)

#### PowerToys (instalar desde GitHub o Microsoft Store)

- **FancyZones**: layouts de ventanas avanzados
- **PowerRename**: renombrado masivo con regex
- **Shortcut Guide**: muestra atajos al mantener Win presionado

#### Windows Terminal y PowerShell

- Instala **Windows Terminal** desde Microsoft Store; configura perfiles (PowerShell, cmd, WSL).
- Comandos útiles:

```powershell
# Información del sistema
systeminfo

# Listar discos
Get-PhysicalDisk
```

#### winget (gestión de paquetes)

```powershell
# Buscar e instalar VS Code
winget search vscode
winget install --id Microsoft.VisualStudioCode -e
```

### 12) Desarrollo y entorno avanzado

#### WSL 2 (Windows Subsystem for Linux)

Instalación rápida (Admin PowerShell):

```powershell
wsl --install
# o instalar distro específica
wsl --install -d Ubuntu
```

- Permite correr Linux nativo, usar Docker Desktop con WSL2 backend y herramientas de desarrollo.

#### Hyper-V (virtualización)

Activar en PowerShell (Admin):

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

- Crea VMs para pruebas; usa **Hyper-V Manager**.

#### Windows Sandbox (si tu edición lo soporta)

- Ejecuta aplicaciones aisladas temporalmente (ideal para probar programas desconocidos).

### 13) Automatización con PowerShell y Task Scheduler (ejemplos)

- **Script de backup simple** (ejemplo guardando carpeta a un ZIP):

```powershell
$source = "C:\Users\TuUsuario\Documents\Proyecto"
$dest = "D:\Backups\Proyecto_$(Get-Date -Format yyyyMMdd).zip"
Compress-Archive -Path $source -DestinationPath $dest -Force
```

- Programar el script con **Task Scheduler**: Crear tarea → Trigger (Daily) → Action (Start a program: `powershell.exe -File C:\scripts\backup.ps1`).

### 14) Gaming en Windows 11

- **Xbox app** y Game Pass integrada.
- **Game Mode**: `Settings > Gaming > Game Mode` (optimiza recursos).
- **Auto HDR / DirectStorage** (si hardware soporta) para tiempos de carga más rápidos y mejor calidad gráfica.
- Mantén drivers GPU actualizados desde Web de NVIDIA/AMD/Intel.

### 15) Seguridad avanzada / empresa (breve)

- **Azure AD / Microsoft Entra** para identidad y SSO.
- **Intune** para MDM / políticas.
- **Windows Defender for Endpoint** para EDR y detección.
- **LAPS** para gestionar contraseñas de administrador local.

### 16) Troubleshooting común (pasos y comandos)

- **No arranca** → `Automatic Repair` desde recovery USB → `Startup Repair`.
- **Arranque lento** → Task Manager Startup, desactivar programas innecesarios.
- **Pantalla azul** → anotar código BSOD, revisar Event Viewer y actualizar drivers.
- **Red lenta** → `ping`, `tracert`, `ipconfig /all`, reiniciar adaptador:

```cmd
ipconfig /release
ipconfig /renew
ipconfig /flushdns
```

### 17) Buenas prácticas generales y checklist de seguridad

- Activar **2FA** en cuenta Microsoft y dispositivos.
- Mantener **Windows Update** y drivers al día.
- Habilitar **BitLocker** y guardar claves de recuperación.
- Usar **Defender + SmartScreen** y evitar instalar software desconocido.
- Mantener **backups** (OneDrive + backup local / imágenes de disco).
- Crear **puntos de restauración** antes de cambios críticos.

---

[🔼](#índice)

---

## **37. Explora las publicaciones más importantes sobre Windows**

### 1) ¿Qué tipos de publicaciones debes vigilar y por qué

- **Anuncios y novedades oficiales** — funciones, lanzamientos y hojas de ruta (útiles para planear upgrades y migraciones).
- **Avisos de seguridad / parches** — vulnerabilidades, CVE y guías de mitigación (crítico para operar con seguridad).
- **Documentación técnica / how-to** — guías de despliegue, políticas de grupo, APIs (útil para administrar y desarrollar).
- **Blogs y análisis IT-pro** — contexto sobre impacto empresarial, best practices y despliegue masivo.
- **Medios técnicos y prensa** — análisis independiente, investigación y cobertura de incidentes reales.

¿Por qué? porque no basta con **anunciar** que hay un parche: necesitas la **documentación**, la **guía de implementación** y la **opinión experta** para decidir cuándo y cómo desplegarlo.

### 2) Fuentes oficiales imprescindibles (qué publican y ejemplo práctico)

1. **Windows Blog (oficial)** — anuncios de producto, funcionalidades y artículos de producto/usuario. Ideal para conocer qué cambia en Windows (features, Surface, Copilot, etc.).

   _Ejemplo:_ cuando Microsoft anuncia una nueva versión o feature (p. ej. Copilot en Windows) lo verás aquí primero; útil para planear pruebas de compatibilidad.

2. **Windows Insider Blog / Programa Windows Insider** — notas de versión y builds previas (Canary/Dev/Beta). Perfecto para testers y equipos que quieran validar cambios antes del lanzamiento.

   _Ejemplo:_ ver el changelog de una build Insider para identificar un cambio que pueda romper un driver.

3. **Microsoft Security Response Center (MSRC) — blog y Security Update Guide** — avisos de seguridad, guías de mitigación y CVE. Es la fuente primaria para vulnerabilidades de Microsoft. Si gestionas parches, este es _el_ sitio que debes monitorear.

   _Ejemplo real:_ ante un CVE crítico en SharePoint, MSRC publica guías de mitigación y parches; debes revisar esa publicación y coordinar testing y despliegue.

4. **Microsoft Learn / Windows docs (Learn.Microsoft.com)** — documentación técnica, guías de despliegue, referencias de Group Policy, Windows 11/Server docs. Indispensable para pasos concretos de implementación.

   _Ejemplo:_ buscar la referencia oficial de políticas de Windows 11 (24H2) antes de crear GPOs en producción. ([Microsoft Learn][7])

5. **Windows IT Pro Blog / Microsoft Tech Community (Windows IT Pro)** — posts dirigidos a administradores empresariales: backups, Windows Autopatch, Windows for Business, casos de uso y guías prácticas.

### 3) Publicaciones y medios externos de referencia (análisis y cobertura)

- **Windows Central** — noticias/guías orientadas a usuarios y análisis de features. Útil para reseñas y “how-to” prácticas.
- **Tom’s Hardware / Ars Technica / ZDNet / The Verge** — suelen analizar rendimiento, benchmarking y discutir afirmaciones de Microsoft con pruebas independientes (útil para validar claims de marketing).
- **WSJ / medios generalistas** — cubren incidentes grandes (explotaciones activas, impacto económico) y suelen agregar contexto regulatorio y empresarial.

**Por qué leerlos:** cuando Microsoft publica un parche o una mejora de rendimiento, los medios independientes ya te dirán si la afirmación está bien fundada o si hay casos que requieren precaución. _Ejemplo:_ análisis que cuestiona comparativas de rendimiento de Microsoft te ayuda a decidir si actualizar ya o esperar más pruebas.

### 4) Dónde encontrar _actualizaciones críticas_ y cómo priorizarlas (workflow práctico)

1. **Seguridad crítica (primer nivel):** MSRC blog + Security Update Guide → identificar CVE y severity.

   - Acción: aplicar en _test lab_ → validar compatibilidad → desplegar por OUs/colecciones (Intune/WSUS/SCCM).
   - Ejemplo: ante CVE con exploit público, priorizar patch en servidores expuestos y backups.

2. **Funcionalidad y cambios del producto (segundo nivel):** Windows Blog + Windows Insider (comprueba si la feature afectará drivers/aplicaciones).

   - Acción: probar en grupos pilotos antes de deploy masivo.

3. **Políticas y despliegue (tercer nivel):** Microsoft Learn / Windows IT Pro → aplicar GPOs, Windows Autopatch, Windows Update for Business.

### 5) Cómo **seguir** estas publicaciones (rápido, sin perderte nada)

- **RSS/Feed:** muchos blogs Microsoft incluyen “Subscribe to RSS” (Windows Blog / Windows Insider lo ofrecen). Añade feeds a Feedly/NewsBlur/your-reader y crea carpetas “security”, “release”, “dev”.
- **X / Twitter y LinkedIn:** sigue cuentas oficiales (ej. @windowsinsider) y cuentas de MSRC para alertas rápidas.
- **Alertas por correo / boletines:** suscríbete al blog oficial y al correo de tu tenant (algunas publicaciones IT/Tech ofrecen newsletters).
- **Google Alerts / News:** crea alertas para “Microsoft CVE”, “Windows 11 update”, “Security Update Guide CVE-YYYY-NNNN”.
- **SIEM / integraciones empresariales:** para empresas, integrar Security Update Guide + CVE feeds a su sistema de tickets/CMDB para priorización automática.

### 6) Lectura enfocada según tu rol (qué leer y cómo actuar)

- **Administrador de TI:** Windows IT Pro Blog, Microsoft Learn Windows docs, MSRC, Windows Blog (release notes). Proceso: leer → test → parchear → documentar.
- **Ingeniero de seguridad / SOC:** MSRC, Security Update Guide, Sysinternals blog, BleepingComputer/Threat intel; subscribir feeds CVE y configurar alertas de SIEM.
- **Desarrollador / hardware dev:** Windows Developer posts (Windows Blog dev section) y Microsoft Docs para API/compatibilidad. Probar en builds Insider si cambia API.
- **Power user / entusiasta:** Windows Blog + Windows Central + Windows Insider (si quieres probar funciones antes).

### 7) Lista rápida de **publicaciones recomendadas** (prioridad al seguirlas)

1. **Windows Blog (oficial).**
2. **Windows Insider Blog / Programa Windows Insider.**
3. **Microsoft Security Response Center (MSRC) — blog y Security Update Guide.**
4. **Microsoft Learn / Windows documentation.**
5. **Windows IT Pro Blog / TechCommunity (Windows).**
6. **Windows Central / Tom’s Hardware / Ars Technica / ZDNet** — para análisis y pruebas independientes.
7. **WSJ / Reuters / The Verge** — para impacto empresarial y cobertura de incidentes mayores.

### 8) Ejemplos concretos (casos reales para entender el flujo)

- **Incidente de seguridad:** MSRC publica guía sobre una vulnerabilidad en SharePoint → medios (WSJ, BleepingComputer) cubren explotación activa → admins testean parche en lab → desplegan mediante WSUS/Intune → SOC monitoriza señales de compromiso.
- **Debate sobre rendimiento:** Microsoft publica claims de rendimiento en un blog → Tom’s Hardware y otros reproducen pruebas y publican análisis crítico → tú usas esa info para decidir si forzar o posponer actualizaciones en equipos de producción.

### 9) Tips rápidos para no ahogarte en información

- Crea **3 filtros** en tu lector: _Seguridad (MSRC + CVE feeds)_, _Releases (Windows Blog + Insider)_, _Análisis (Windows Central + Tom’s)_.
- Automatiza: **alerts → triage → lab test → deploy**. No parchear en producción sin pruebas.
- Mantén un **changelog interno**: cuándo aplicaste cada KB/patch y resultados de pruebas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                      | **Siguiente 3**      |
| ------------------ | -------------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./2_1_Operating_Systems.md) | [⏩](./2_3_Linux.md) |
