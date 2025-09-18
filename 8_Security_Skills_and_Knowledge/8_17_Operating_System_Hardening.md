| **Inicio**         | **atrás 16**              | **Siguiente 18**                                    |
| ------------------ | ------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_16_Honeypots.md) | [⏩](./8_18_Understand_the_Concept_of_Isolation.md) |

---

## **Índice**

| Temario                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [227. Fortalecimiento del sistema operativo: 15 prácticas recomendadas](#227-fortalecimiento-del-sistema-operativo-15-prácticas-recomendadas)                            |
| [228. Fortalecimiento del sistema operativo (SO): ventajas, desventajas e importancia](#228-fortalecimiento-del-sistema-operativo-so-ventajas-desventajas-e-importancia) |
| [229. Técnicas de endurecimiento](#229-técnicas-de-endurecimiento)                                                                                                       |

# **Endurecimiento del sistema operativo**

## **227. Fortalecimiento del sistema operativo: 15 prácticas recomendadas**

![Endurecimiento del sistema operativo](/img/8_Security_Skills_and_Knowledge/Hardening.jpg "Endurecimiento del sistema operativo")

### 1) Mantener el sistema y paquetes parcheados

**Por qué:** vulnerabilidades conocidas son el vector nº1 de intrusión.

**Cómo (Linux):**

```bash
# Debian/Ubuntu
sudo apt update && sudo apt upgrade -y

# RHEL/CentOS (dnf o yum según versión)
sudo dnf upgrade --refresh -y
```

**Cómo (Windows):** habilitar Windows Update automático o usar WSUS/Intune.

Ejemplo PowerShell (módulo PSWindowsUpdate):

```powershell
Install-Module -Name PSWindowsUpdate -Force
Get-WindowsUpdate -Install -AcceptAll -AutoReboot
```

**Verificar:** `uname -a`/`/etc/os-release` + revisar `apt list --upgradable` o en Windows: `Get-WindowsUpdateLog`.

**Tip:** automatiza parches con ventanas de mantenimiento y pruebas previas.

### 2) Instalación mínima (reduce la superficie de ataque)

**Por qué:** menos software = menos vulnerabilidades y servicios a asegurar.

**Cómo (Linux):**

- Al instalar SO, elegir la instalación mínima o servidor base.
- Remover paquetes innecesarios: `sudo apt purge package-name`.

  **Cómo (Windows):**

- No instalar roles/feature innecesarios; usar `Server Manager` o `Uninstall-WindowsFeature`.

  **Verificar:** lista de paquetes/roles instalados (`dpkg -l` / `Get-WindowsFeature | Where Installed`).

  **Tip:** usa imágenes base minimal en VMs/containers.

### 3) Deshabilitar servicios y demonios innecesarios

**Por qué:** servicios abiertos exponen puertos y vectores de ataque.

**Cómo (Linux systemd):**

```bash
# ver servicios
systemctl list-unit-files --type=service

# deshabilitar y parar
sudo systemctl disable --now telnet.service
sudo systemctl mask telnet.service    # evita arranque accidental
```

**Cómo (Windows):**

```powershell
# listar y detener servicio
Get-Service -Name "ServiceName"
Stop-Service -Name "ServiceName"
Set-Service -Name "ServiceName" -StartupType Disabled
```

**Verificar:** `ss -tunlp` / `netstat -tuln` y en Windows `Get-NetTCPConnection`.

**Tip:** documenta cada servicio deshabilitado por dependencia.

### 4) Control de cuentas, políticas de contraseñas y MFA

**Por qué:** cuentas débiles y contraseñas comprometidas facilitan brechas.

**Cómo (Linux):**

- Forzar complejidad con PAM (`/etc/pam.d/common-password`, `pam_pwquality.so`) y bloqueo de intentos (`pam_faillock`).

```bash
# ejemplo: ver aging de usuario
chage -l usuario
```

**Cómo (Windows):**

- GPO -> Account Policies: longitud mínima, bloqueo de cuenta, expiración.
- Habilitar **MFA** para acceso remoto y cuentas críticas (Azure AD/Microsoft 365, soluciones 3rd-party).

  **Verificar:** revisar `faillock`/`faillog`, en Windows GPO resultante y `net accounts`.

  **Tip:** usa autenticación de factores para admins.

### 5) Principio de menor privilegio y gestión de sudo/administradores

**Por qué:** minimiza el daño por cuenta comprometida.

**Cómo (Linux):**

- Revocar acceso root directo: **usar sudo** con `visudo`.

```text
# ejemplo en /etc/sudoers (editar con visudo)
%admins ALL=(ALL) ALL
Defaults logfile="/var/log/sudo.log"
```

- Evitar `NOPASSWD` salvo casos justificados.

  **Cómo (Windows):**

- No usar la cuenta Administrador para tareas diarias; usar cuentas estándar elevadas con UAC.
- Implementar **LAPS** (Local Administrator Password Solution) para manejar contraseñas locales.

  **Verificar:** `getent group sudo` / revisar `/var/log/sudo.log`. En Windows revisar miembros de grupos `Administrators`.

  **Tip:** audita privilegios periódicamente.

### 6) Asegurar el acceso remoto (SSH y RDP)

**Por qué:** accesos remotos son blanco principal de fuerza bruta y explotación.

**Cómo (SSH - Linux):** editar `/etc/ssh/sshd_config`:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers usuario1 usuario2
LoginGraceTime 30
PermitEmptyPasswords no
```

Reiniciar: `sudo systemctl restart sshd`

Asegurar `.ssh/authorized_keys`:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**Cómo (RDP - Windows):**

- Requerir NLA (Network Level Authentication).
- Restringir RDP por IP y usar RD Gateway / VPN.
- Deshabilitar cuentas con RDP si no necesarias.

  **Verificar:** `sshd -T | grep permitrootlogin` o `systemctl status sshd`. En Windows revisar `Get-RDSessionCollectionDeployment`.

  **Tip:** usar autenticación por llave, no passwords; considerar fail2ban o bloqueos automáticos.

### 7) Firewall y segmentación de red

**Por qué:** filtrar tráfico reduce vectores y limitas movimiento lateral.

**Cómo (Linux - ufw):**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp           # solo si usas SSH
sudo ufw enable
```

**RHEL:** `firewall-cmd --permanent --add-service=ssh && firewall-cmd --reload`

**Cómo (Windows):**

```powershell
New-NetFirewallRule -DisplayName "Allow-SSH" -Direction Inbound -LocalPort 22 -Protocol TCP -Action Allow
```

**Segmentación:** VLANs, microsegmentación y políticas de firewall entre subredes.

**Verificar:** `ufw status verbose`, `firewall-cmd --list-all`, `Get-NetFirewallRule`.

**Tip:** implementar reglas por defecto “deny” y abrir solo lo necesario.

### 8) Cifrado de disco y protección de datos en reposo

**Por qué:** protege datos si un disco es robado o extraído.

**Cómo (Linux LUKS):** (¡hacer backup antes!)

```bash
# ejemplo conceptual: en un disco nuevo /dev/sdb
sudo cryptsetup luksFormat /dev/sdb
sudo cryptsetup luksOpen /dev/sdb securedata
mkfs.ext4 /dev/mapper/securedata
```

**Cómo (Windows BitLocker):**

```powershell
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnly
```

**Verificar:** `lsblk` + `cryptsetup status <name>`; en Windows `Get-BitLockerVolume`.

**Tip:** gestionar claves con TPM y recuperación off-site en vault seguro.

### 9) Harden del kernel y parámetros de red (sysctl)

**Por qué:** mitigaciones a nivel kernel reducen vectores TCP/IP y explotación.

**Cómo (Linux):** añadir a `/etc/sysctl.conf`:

```text
# ejemplo seguro
net.ipv4.ip_forward = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.tcp_syncookies = 1
```

Aplicar: `sudo sysctl -p`

**SELinux/AppArmor:** activar y mantener en `enforcing` / perfiles estrictos:

```bash
# SELinux
getenforce
sudo setenforce 1
```

**Verificar:** `sysctl net.ipv4.tcp_syncookies`, `sestatus` o `aa-status`.

**Tip:** aplicar solo cambios probados (algunos parámetros afectan redes/containers).

### 10) Monitorización de integridad y control de cambios (FIM)

**Por qué:** detectar cambios no autorizados en archivos críticos (rootkits, backdoors).

**Herramientas:** **AIDE**, Tripwire, OSSEC/Wazuh.

**Ejemplo rápido AIDE:**

```bash
# instalar y crear base
sudo apt install aide
sudo aideinit
# después de init
sudo cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db
sudo aide --check
```

**Verificar:** revisar reportes y alertas; integrar a SIEM.

**Tip:** generar baseline después de parche y cambios planificados.

### 11) Logging, auditoría y recolección centralizada

**Por qué:** registros confiables permiten detección y respuesta rápida.

**Cómo (Linux):**

- Habilitar `auditd` y reglas para /etc, /var/log, binarios sensibles:

```bash
sudo apt install auditd
# ejemplo: vigilar /etc/passwd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

- Enviar logs a servidor central (rsyslog/syslog-ng -> SIEM).

  **Cómo (Windows):**

- Habilitar auditoría avanzada de seguridad y Windows Event Forwarding (WEF) a SIEM.

  **Verificar:** `ausearch -k passwd_changes`, `/var/log/audit/audit.log`, revisar en tu SIEM.

  **Tip:** asegurar logs (integrity and access control) y retención acorde a normativa.

### 12) Control de aplicaciones: whitelisting / AppLocker / WDAC

**Por qué:** previene ejecución de software no autorizado (gran defensa contra ransomware).

**Cómo (Windows):** AppLocker o Windows Defender Application Control (WDAC) via GPO/Intune.

**Cómo (Linux):** usar SELinux/AppArmor, o herramientas como `rpm`/`dpkg` para verificar firmas.

**Verificar:** en Windows `Get-AppLockerPolicy -Effective`. En Linux revisar perfiles AppArmor.

**Tip:** whitelist por hash o publisher; implementar en modo “audit” antes de bloquear.

### 13) Copias de seguridad regulares y plan de recuperación (DR)

**Por qué:** ataques (ransomware), fallos hardware y errores humanos requieren recuperación fiable.

**Cómo:** políticas de backup (3-2-1: 3 copias, 2 medios, 1 off-site). En Linux: `borg`, `restic`, `rsync` + cifrado. En Windows: VSS + soluciones empresariales.

**Verificar:** pruebas periódicas de restore (no sirve si no restauras).

**Tip:** backups inmutables/versionados (S3 Object Lock / snapshots) para mitigar ransomware.

### 14) Gestión de configuración, automatización y baselines (IaC)

**Por qué:** aplicar configuraciones seguras de forma reproducible y auditable.

**Cómo:** usar **Ansible / Puppet / Chef / Salt** para hardening y parches. Aplica benchmarks (CIS, STIG).

**Ejemplo Ansible (deshabilitar telnet):**

```yaml
- name: Disable telnet service
  hosts: servers
  become: yes
  tasks:
    - name: stop and disable telnet
      systemd:
        name: telnet
        state: stopped
        enabled: no
```

**Verificar:** CI/CD pipeline que valide conformidad y escaneo de drift.

**Tip:** versiona playbooks en git y haz pruebas en entorno staging.

### 15) Seguridad del firmware / arranque seguro (Secure Boot, TPM)

**Por qué:** evita que un atacante instale bootkits o altere el arranque; TPM almacena claves seguras.

**Cómo:**

- Habilitar **Secure Boot** en UEFI/BIOS.
- Configurar contraseña BIOS/UEFI y deshabilitar arranque desde USB/CD si no necesario.
- Usar TPM para BitLocker (Windows) o integrarlo con LUKS (Linux).
  **Verificar:** en Linux `mokutil --sb-state` o `dmesg | grep -i secure`, en Windows `Confirm-SecureBootUEFI`.

  **Tip:** documenta y resguarda claves de recuperación.

### Recomendaciones finales y buenas prácticas transversales

- **Prueba en laboratorio** antes de producción.
- **Documenta** cada cambio (why/how/who).
- **Backups** antes de cambios importantes (kernel, particiones, cifrado).
- **Monitoriza** y automatiza alertas (SIEM, EDR).
- **Capacita al personal**: muchas brechas vienen por errores humanos.
- **Aplica un baseline** y audita periódicamente (CIS Benchmark, scanners de vulnerabilidades).

---

[🔼](#índice)

---

## **228. Fortalecimiento del sistema operativo (SO): ventajas, desventajas e importancia**

### ⚠️ Desventajas del endurecimiento del sistema operativo

Aunque el endurecimiento (hardening) es **esencial para la seguridad**, también puede traer ciertas desventajas si no se gestiona bien:

1. **Compatibilidad**

   - Al deshabilitar servicios o protocolos, algunas aplicaciones pueden dejar de funcionar.
   - Ejemplo: desactivar SMBv1 en Windows rompe apps antiguas que aún lo usan.

2. **Mayor complejidad en la administración**

   - Configuraciones avanzadas de firewall, permisos o cifrado requieren más tiempo y conocimiento.

3. **Riesgo de bloqueos accidentales**

   - Un cambio de configuración mal hecho (ej. reglas de firewall muy restrictivas) puede dejar inaccesible el sistema.

4. **Curva de aprendizaje y costos**

   - Se necesita personal capacitado y tiempo para mantener las configuraciones seguras.

### 🔄 Diferencia entre endurecimiento y parcheo del sistema operativo

- **Endurecimiento (Hardening):**

  - Proceso **preventivo y proactivo**.
  - Consiste en **configurar el sistema de manera segura**, reduciendo la superficie de ataque.
  - Ejemplos: deshabilitar servicios innecesarios, aplicar políticas de contraseñas, habilitar cifrado, restringir accesos.

- **Parcheo:**

  - Proceso **reactivo y correctivo**.
  - Consiste en **aplicar actualizaciones de seguridad** para corregir vulnerabilidades conocidas.
  - Ejemplos: instalar parches de Windows Update, `apt upgrade` en Linux.

👉 **En resumen:** el endurecimiento asegura la configuración del sistema; el parcheo corrige errores en el software. Ambos son complementarios.

### 📋 ¿Qué es una lista de verificación de fortalecimiento del sistema?

Es un **checklist estructurado de prácticas y configuraciones** que deben aplicarse para asegurar un sistema operativo.

- Sirve como **guía paso a paso** para administradores.
- Facilita **auditorías** y comprobación de cumplimiento (ej. CIS Benchmarks, STIGs).

Ejemplo de ítems en un checklist:

- ¿Está el firewall activado y con reglas restrictivas?
- ¿Se deshabilitó el inicio de sesión como root/Administrador?
- ¿Se están aplicando parches automáticamente?
- ¿Está configurado el cifrado de disco?

### ⚙️ Programas y funciones predeterminados

- Desinstalar o deshabilitar **software y servicios innecesarios** que vienen por defecto.
- Ejemplos: Telnet, FTP, SMBv1, servicios de impresión en servidores que no lo requieren.
- Objetivo: reducir la **superficie de ataque**.

### 🔑 Acceso y autenticación

- Aplicar el **principio de menor privilegio**.
- Forzar políticas de contraseñas seguras y bloqueo tras intentos fallidos.
- Usar **MFA (autenticación multifactor)**.
- Deshabilitar cuentas predeterminadas (ej. “guest” en Windows, “root” en acceso remoto Linux).

### 🛠️ Parcheo

- Mantener el **sistema y aplicaciones actualizados** con los últimos parches de seguridad.
- Configurar parches automáticos cuando sea posible.
- Probar parches críticos en un **entorno de pruebas** antes de pasarlos a producción.

### 📝 Resumen

- El **endurecimiento del SO** reduce riesgos configurando el sistema de forma más segura.
- Sus **desventajas** principales son compatibilidad, complejidad y riesgo de errores si se aplica mal.
- **Hardening ≠ Parcheo** → uno asegura configuración, el otro corrige vulnerabilidades.
- Una **lista de verificación** organiza y estandariza las prácticas de seguridad.
- Áreas clave:

  - **Programas y funciones predeterminados:** eliminar o deshabilitar lo innecesario.
  - **Acceso y autenticación:** restringir privilegios, contraseñas seguras, MFA.
  - **Parcheo:** aplicar actualizaciones regularmente y probarlas.

---

[🔼](#índice)

---

## **229. Técnicas de endurecimiento**

Son acciones y configuraciones que reducen la **superficie de ataque** y elevan la dificultad para un atacante: eliminar/mitigar vectores de entrada, controlar quién puede hacer qué, y asegurarse de que las defensas detecten y contengan intrusiones.

### Principios guía (rápidos)

- **Menor privilegio**: dar solo lo estrictamente necesario.
- **Defensa en profundidad**: varias capas (red, host, app, identidad).
- **Denegar por defecto**: permitir solo lo imprescindible.
- **Auditar y verificar**: si no puedes medirlo, no puedes protegerlo.
- **Automatizar y versionar**: reproducibilidad y control de cambios.

### Técnicas de endurecimiento (con ejemplos)

#### 1. Instalación mínima y reducción de la superficie

**Qué:** instalar sólo los paquetes/roles necesarios.

**Ejemplo (Linux):**

```bash
# eliminar paquete innecesario
sudo apt purge telnetd vsftpd -y
```

**Ejemplo (Windows):** quitar roles con Server Manager o PowerShell:

```powershell
Uninstall-WindowsFeature -Name Print-Services
```

**Verificar:** `dpkg -l | grep telnet` / `Get-WindowsFeature | Where-Object Installed`.

#### 2. Deshabilitar servicios y demonios innecesarios

**Qué:** parar/evitar servicios no usados.

**Linux:**

```bash
sudo systemctl disable --now avahi-daemon
```

**Windows:**

```powershell
Stop-Service -Name "Spooler"
Set-Service -Name "Spooler" -StartupType Disabled
```

**Verificar:** `ss -tunlp` / `Get-Service -Name Spooler`.

#### 3. Gestión de parches y vulnerabilidades

**Qué:** mantener kernel, libraries y apps actualizados; aplicar políticas de parcheo.

**Linux (ejecutar actualizaciones):**

```bash
sudo apt update && sudo apt upgrade -y
```

**Windows:** WSUS/Intune o `Get-WindowsUpdate` (PSWindowsUpdate).

**Verificar:** herramientas de scanning (Nessus, OpenVAS) o `apt list --upgradable`.

#### 4. Control de cuentas y políticas de contraseñas

**Qué:** políticas de complejidad, bloqueo, expiración.

**Linux (PAM):** editar `/etc/pam.d/common-password` para `pam_pwquality`.

**Windows:** GPO -> Account Policies (min length, history, lockout).

**Verificar:** `chage -l usuario` / `net accounts`.

#### 5. Autenticación fuerte: llaves, certificados y MFA

**Qué:** preferir claves públicas/PKI y MFA para accesos críticos.

**SSH (Linux) ejemplo:**

- Desactivar autenticación por contraseña: `PasswordAuthentication no` en `/etc/ssh/sshd_config`.

  **MFA:** habilitar MFA en Azure AD / IdP para admins.

  **Verificar:** `sshd -T | grep passwordauthentication`.

#### 6. Principio de menor privilegio y control de elevaciones

**Qué:** evitar cuentas admin para tareas diarias; usar sudo, roles o JIT.

**Linux (sudoers):**

```
%devops ALL=(ALL) ALL
Defaults logfile="/var/log/sudo.log"
```

**Windows:** LAPS para gestionar contraseñas locales; limitar miembros del grupo Administrators.

**Verificar:** revisar `/var/log/sudo.log` y miembros del grupo.

#### 7. Endurecimiento del acceso remoto (SSH / RDP)

**SSH (Linux) ejemplos:**

- `PermitRootLogin no`
- `AllowUsers gusdev admin`
- Usar `Fail2ban` para bloquear fuerza bruta.

```bash
sudo apt install fail2ban
```

**RDP (Windows):**

- Forzar NLA, limitar por IP, usar RD Gateway o VPN.

  **Verificar:** logs `/var/log/auth.log` o Windows Event Viewer.

#### 8. Firewalls y segmentación

**Qué:** reglas “deny-by-default”, segmentación en VLANs/subredes.

**Linux (ufw):**

```bash
sudo ufw default deny incoming
sudo ufw allow from 10.0.0.0/24 to any port 22
sudo ufw enable
```

**Windows (PowerShell):**

```powershell
New-NetFirewallRule -DisplayName "Allow SSH" -Direction Inbound -LocalPort 22 -Protocol TCP -Action Allow
```

**Verificar:** `ufw status verbose` / `Get-NetFirewallRule`.

#### 9. Cifrado en reposo y en tránsito

**Cifrado disco (Linux):** LUKS

```bash
sudo cryptsetup luksFormat /dev/sdb
sudo cryptsetup open /dev/sdb securedata
```

**Windows:** BitLocker (TPM + PIN):

```powershell
Enable-BitLocker -MountPoint "C:" -UsedSpaceOnly -EncryptionMethod XtsAes256
```

**TLS:** forzar TLS 1.2/1.3, renovar certificados, usar HSTS para web.

**Verificar:** `lsblk` + `cryptsetup status` / `Get-BitLockerVolume`.

#### 10. Hardening del kernel / parámetros de red (sysctl)

**Qué:** mitigar spoofing, redirects, IP forwarding no deseado.

**Ejemplo `/etc/sysctl.conf`:**

```
net.ipv4.ip_forward = 0
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.tcp_syncookies = 1
```

Aplicar: `sudo sysctl -p`.

**Verificar:** `sysctl net.ipv4.tcp_syncookies`.

#### 11. Control de aplicaciones: AppLocker / SELinux / AppArmor

**Qué:** limitar qué aplicaciones pueden ejecutarse.

**Linux:** activar SELinux (enforcing) o perfiles AppArmor.

**Windows:** AppLocker o WDAC configurados por GPO.

**Verificar:** `sestatus` / `Get-AppLockerPolicy -Effective`.

#### 12. Integridad de ficheros (FIM)

**Qué:** detectar cambios en binarios/archivos críticos.

**Herramientas:** AIDE, Tripwire, Wazuh.

**Comandos AIDE:**

```bash
sudo apt install aide
sudo aideinit
sudo cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db
sudo aide --check
```

**Verificar:** revisar reporte `aide --check`.

#### 13. Logging y monitorización centralizada

**Qué:** enviar logs a SIEM/servidor central, almacenar pcaps críticos.

**Linux:** `auditd`, rsyslog -> SIEM.

```bash
sudo apt install auditd
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

**Windows:** Windows Event Forwarding (WEF) a SIEM.

**Verificar:** consultar SIEM y reglas de correlación.

#### 14. Protección contra malware y EDR

**Qué:** AV + EDR con capacidades de detección y respuesta (tamper protection).

**Ejemplo:** Microsoft Defender for Endpoint, SentinelOne, CrowdStrike.

**Verificar:** consola del EDR y alertas.

#### 15. Copias de seguridad y capacidad de recuperación

**Qué:** estrategia 3-2-1, backups inmutables/versionados, pruebas de restore.

**Ejemplo:** `restic` o `borg` para backups cifrados y verificados.

**Verificar:** pruebas periódicas de restauración.

#### 16. Hardening de contenedores y VMs

**Qué:** imágenes mínimas, escaneo de vulnerabilidades, runs sin root, read-only FS.

**Docker ejemplo:**

```bash
docker run --read-only --cap-drop ALL --user 1000:1000 imagen
```

**Verificar:** escanear con `trivy` o `clair`.

#### 17. Gestión de configuración e IaC (automatización)

**Qué:** aplicar playbooks que documenten exactamente la configuración segura.

**Ejemplo Ansible (deshabilitar telnet):**

```yaml
- hosts: servers
  become: yes
  tasks:
    - name: stop telnet
      systemd:
        name: telnet
        state: stopped
        enabled: no
```

**Verificar:** estado con `ansible-playbook --check` y CI.

#### 18. Harden de firmware y arranque (Secure Boot, TPM)

**Qué:** impedir bootkits usando Secure Boot y TPM para llaves de cifrado.

**Acción:** habilitar Secure Boot en UEFI, usar TPM para BitLocker/LUKS.

**Verificar:** `mokutil --sb-state` en Linux o `Confirm-SecureBootUEFI` en Windows.

### Cómo verificar y auditar el hardening

- Usar benchmarks: **CIS Benchmarks**, **DISA STIGs** y herramientas de evaluación (CIS-CAT, Lynis, OpenSCAP).
- Escaners de vulnerabilidades (Nessus, OpenVAS) y pruebas de penetración en laboratorio.
- Revisar logs y alertas en SIEM, y ejecutar comprobaciones automáticas (playbooks de compliance).

### Riesgos e impactos a valorar

- **Compatibilidad:** algunas apps antiguas pueden romperse.
- **Operaciones:** mayores tiempos de gestión y necesidad de rollback plan.
- **Rendimiento:** cifrado y controles extra pueden añadir latencia.
- **Falsa sensación de seguridad:** hardening no reemplaza monitoreo ni respuesta (assume breach).

### Buenas prácticas finales

1. Probar todo en laboratorio antes de producción.
2. Versionar configuraciones (git).
3. Automatizar (Ansible/CIS baselines).
4. Documentar y auditar periódicamente.
5. Implementar detección y respuesta (EDR + SIEM).
6. Formar al equipo (operaciones y devs) sobre cambios y excepciones.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 16**              | **Siguiente 18**                                    |
| ------------------ | ------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_16_Honeypots.md) | [⏩](./8_18_Understand_the_Concept_of_Isolation.md) |
