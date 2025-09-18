| **Inicio**         | **atrás 8**                           | **Siguiente 10**                         |
| ------------------ | ------------------------------------- | ---------------------------------------- |
| [🏠](../README.md) | [⏪](./2_8_Understand_Permissions.md) | [⏩](./2_10_Performing_CRUD_on_Files.md) |

---

## **Índice**

| Temario                                                                                  |
| ---------------------------------------------------------------------------------------- |
| [55. Instalación de software y aplicaciones](#55-instalación-de-software-y-aplicaciones) |

---

# **Instalación de software y aplicaciones**

## **55. Instalación de software y aplicaciones**

### 1) ¿Qué significa _instalar_ software?

Instalar = copiar los ficheros necesarios + registrar metadatos (dependencias, rutas, servicios, entradas de menú) + configurar permisos y (si aplica) habilitar/arrancar servicios.
Objetivo: que la aplicación sea ejecutable y mantenible (actualizable/desinstalable) por herramientas del sistema.

### 2) Formatos comunes y dónde vienen (resumen rápido)

- **Windows**

  - `.exe` (instaladores), `.msi` (Windows Installer package), portable `.exe`, Microsoft Store, `appx/msix`.

- **macOS**

  - `.dmg` (imagen de disco), `.pkg` (instalador), App Store, Homebrew (`brew`).

- **Linux**

  - Distribuciones: paquetes `.deb` (Debian/Ubuntu), `.rpm` (RHEL/Fedora), `pacman` packages (Arch).
  - Universal: `snap`, `flatpak`, `AppImage`.
  - Fuente: `./configure && make && sudo make install`.

- **Contenedores**

  - Docker images (`docker pull`) — instalación encapsulada.

- **Móvil**

  - Android: `.apk` (Play Store / sideload).
  - iOS: App Store (firmado/notarizado).

### 3) Métodos de instalación (tipos y cuándo usarlos)

1. **Gestor de paquetes de OS** (recomendado): gestiona dependencias, actualizaciones y desinstalación (`apt`, `dnf`, `pacman`, `winget`, `brew`, `choco`).
2. **Instalador nativo**: GUI installer (.msi/.pkg/.exe) cuando no hay paquete en repositorio.
3. **Contenedor**: usar Docker/Kubernetes para aislar y poder revertir fácilmente.
4. **Desde código fuente**: cuando no hay paquete; requiere compilación y gestionar dependencias manualmente.
5. **Paquete universal**: AppImage / snap / flatpak (útil en Linux para compatibilidad).
6. **Sideloading**: APK en Android (sólo si confías en la fuente).

### 4) Ejemplos prácticos por plataforma

#### Linux — Debian/Ubuntu (APT)

Actualizar lista, instalar nginx, habilitar servicio:

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable --now nginx
# comprobar
systemctl status nginx
curl -I http://localhost
```

Desinstalar y purgar:

```bash
sudo apt remove --purge -y nginx
sudo apt autoremove -y
```

Instalar desde `.deb` descargado:

```bash
wget https://ejemplo.com/app_1.2.3_amd64.deb
sudo apt install -y ./app_1.2.3_amd64.deb   # instala y resuelve deps
# o con dpkg + apt-get fix-broken:
sudo dpkg -i app_1.2.3_amd64.deb
sudo apt-get -f install
```

#### Linux — RHEL/CentOS (dnf / yum)

```bash
sudo dnf install -y httpd
sudo systemctl enable --now httpd
```

#### Linux — Arch (pacman), Snap, Flatpak, AppImage

```bash
# pacman
sudo pacman -Syu package

# snap (si el sistema lo soporta)
sudo snap install spotify

# flatpak (desde Flathub)
flatpak install flathub com.spotify.Client

# AppImage (portable)
chmod +x Spotify.AppImage
./Spotify.AppImage
```

#### Build desde fuente (ejemplo genérico)

```bash
tar xf program-1.0.tar.gz
cd program-1.0
./configure --prefix=/usr/local       # preparar (puede variar)
make -j"$(nproc)"
sudo make install
# mejor alternativa: empaquetar el build en un .deb/.rpm o usar checkinstall
```

#### Windows — MSI / EXE / Winget / Chocolatey

Instalar con `msiexec` (silencioso):

```powershell
msiexec /i "C:\downloads\app-1.2.3.msi" /qn /norestart    # /qn = quiet
# registrar logs:
msiexec /i "app.msi" /l*v "C:\temp\install.log"
```

Instalar con `winget`:

```powershell
winget install --id=Mozilla.Firefox -e
```

Chocolatey (PowerShell admin):

```powershell
choco install git -y
choco uninstall git -y
```

#### macOS — .dmg / .pkg / Homebrew

Instalar con Homebrew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install wget
brew install --cask google-chrome
```

Instalar desde `.dmg` (línea de comandos):

```bash
hdiutil attach GoogleChrome.dmg
cp -R /Volumes/Google\ Chrome/Google\ Chrome.app /Applications/
hdiutil detach /Volumes/Google\ Chrome
```

Instalar `.pkg` con `installer`:

```bash
sudo installer -pkg /path/to/Example.pkg -target /
```

#### Docker — instalación y despliegue (modo rápido)

```bash
# pull image y ejecutar
docker pull nginx:stable
docker run -d --name web -p 8080:80 nginx:stable

# rollback: parar/eliminar contenedor y lanzar otra versión
docker stop web && docker rm web
docker run -d --name web -p 8080:80 nginx:1.21
```

#### Android APK (sideload - solo si confías)

```bash
adb install app-release.apk
# desinstalar
adb uninstall com.ejemplo.app
```

### 5) Instalación silenciosa / desatendida (útil para scripts y despliegues)

- **Windows MSI**: `msiexec /i paquete.msi /qn /norestart`
- **Windows EXE**: cada vendor usa flags distintos (`/S`, `/silent`); consulta documentación.
- **Debian/Ubuntu**: `DEBIAN_FRONTEND=noninteractive sudo apt -y install paquete`
- **macOS Homebrew**: `brew install --cask --no-quarantine app` (según app).
- **Docker**: `docker run -d ...` sin interactuar.

### 6) Seguridad e integridad (verificar antes de instalar)

- **Checksums**:

```bash
sha256sum archivo.tar.gz
# comparar con el SHA256 publicado
```

- **Firmas GPG**:

```bash
gpg --verify archivo.tar.gz.sig archivo.tar.gz
```

- **Firmas de paquetes**:

  - RPM: `rpm --checksig paquete.rpm`
  - DEB: repositorios APT usan firmas apt; evita `apt-key` obsoleto y usa signed-by en sources.list.

- **Windows**: verificar firma digital (propiedades del .exe o `signtool verify`).
- **macOS**: `codesign --verify --deep --verbose=2 /Applications/App.app`

**NO** ejecutes `curl ... | sh` sin auditar el script.

### 7) Dependencias y versiones

- Los gestores resuelven dependencias (apt, dnf, pacman).
- Cuando instalas desde fuente, instala libs requeridas (`libssl-dev`, `build-essential`...).
- **Lockfiles** para reproducibilidad (npm `package-lock.json`, Python `requirements.txt`/`pipenv`/`poetry.lock`).
- En entornos prod, preferir versiones fijas / tags (no `latest` en Docker) para poder reproducir y hacer rollback.

### 8) Automatización y despliegue en empresa

- **Herramientas**: Ansible, Puppet, Chef, Salt, SCCM (Config Manager), Intune, Jamf (macOS), Chocolatey for Business, Packer (para imágenes), Pulp/Repository managers (apt/yum repos), Nexus/Artifactory.
- **Flujo típico**:

  1. Crear playbooks/recipes.
  2. Test en staging.
  3. Despliegue canario/gradual.
  4. Monitorizar y rollback si es necesario.

**Ejemplo ANSIBLE (instalar nginx en Debian):**

```yaml
- hosts: web
  become: yes
  tasks:
    - name: update apt
      apt: update_cache=yes

    - name: install nginx
      apt: name=nginx state=present

    - name: ensure nginx running
      service: name=nginx state=started enabled=yes
```

### 9) Rollback / desinstalación y snapshots

- **Rollback sencillo**: `apt remove --purge`, `dnf remove`, `choco uninstall`, `brew uninstall`, `docker rm && docker run image:oldtag`.
- **Rollback robusto**: snapshots (VM/volume), imágenes de máquina (Packer), backups de base de datos + restore.
- **Guardar instaladores** y **logs** de instalación para reproducir y diagnosticar.

### 10) Logs y troubleshooting (dónde mirar cuando falla instalación)

- **Linux**:

  - apt: `/var/log/apt/history.log`, `/var/log/dpkg.log`
  - journal/systemd: `journalctl -xe`
  - logs de make: `build.log` (redirige `make 2>&1 | tee build.log`)

- **Windows**:

  - logs MSI: usar `msiexec /i pkg.msi /l*v install.log` y ver Event Viewer (Application / System).
  - Winget/Chocolatey logs en `%LOCALAPPDATA%` o logs específicos.

- **Mac**:

  - Console.app, `/var/log/install.log`.

- **Docker**:

  - `docker logs container` o `docker inspect`.

Comandos útiles:

```bash
# ver últimas líneas de apt log
sudo tail -n 200 /var/log/apt/history.log

# ver journal
sudo journalctl -u nginx -b

# verificar dependencias de un binario
ldd /usr/local/bin/myapp
```

### 11) Buenas prácticas antes, durante y después de instalar

**Antes**

- Leer la documentación y release notes.
- Verificar checksum / firma.
- Revisar requisitos (depencias, espacio disco, puertos).
- Planear ventana/rollback.

**Durante**

- Instalar en staging primero.
- Registrar logs (instalación con salida a fichero).
- Usar cuentas con el menor privilegio necesario.

**Después**

- Ejecutar smoke tests (endpoints, proceso en ejecución).
- Configurar actualizaciones automáticas o política de parches.
- Documentar versión instalada y método (tag de Docker, release tag).

### 12) Ejemplo: script automático de instalación (Bash) — nginx + healthcheck

```bash
#!/usr/bin/env bash
set -euo pipefail

# 1 update, 2 install, 3 enable, 4 smoke test, 5 log
LOG=/var/log/auto_install_nginx.log
{
  echo "Inicio: $(date)"
  echo "Actualizar repos"
  sudo apt update

  echo "Instalar nginx"
  sudo DEBIAN_FRONTEND=noninteractive apt -y install nginx

  echo "Habilitar y arrancar"
  sudo systemctl enable --now nginx

  echo "Smoke test"
  if curl -sSf http://localhost >/dev/null; then
    echo "OK: nginx responde"
  else
    echo "ERROR: nginx no responde"
    exit 1
  fi
  echo "Fin: $(date)"
} | tee "$LOG"
```

### 13) Riesgos habituales y cómo mitigarlos

- **Instalar paquetes fuera de repositorios** → usar checksums/gpg.
- **Sobrescribir dependencias del sistema** → preferir entornos virtuales (Python venv, containers).
- **Ejecutar scripts remotos sin revisar** → inspecciona el script antes de ejecutar.
- **No testear actualizaciones** → usar staging y despliegues progresivos.

### 14) Checklist rápido (antes de pulsar _instalar_)

- ¿Fuente oficial o repositorio confiable?
- ¿Checksum/GPG verificado?
- ¿Espacio, permisos y puertos disponibles?
- ¿Rollback plan (snapshot/backup)?
- ¿Despliegue en staging probado?
- ¿Automatización (playbook/script) listo para reproducir?
- ¿Logs configurados para diagnóstico?

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**                           | **Siguiente 10**                         |
| ------------------ | ------------------------------------- | ---------------------------------------- |
| [🏠](../README.md) | [⏪](./2_8_Understand_Permissions.md) | [⏩](./2_10_Performing_CRUD_on_Files.md) |
