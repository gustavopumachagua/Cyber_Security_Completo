| **Inicio**         | **atrás 1**           | **Siguiente 3**     |
| ------------------ | --------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./6_1_VMWare.md) | [⏩](./6_3_ESXi.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [148. VirtualBox](#148-virtualbox)                     |
| [149. Cómo usar VirtualBox](#149-cómo-usar-virtualbox) |

# **VirtualBox**

## **148. VirtualBox**

### ¿Qué es **VirtualBox**? (resumen rápido)

**VirtualBox** es un _hypervisor_ de tipo 2 (se ejecuta sobre un sistema operativo host) desarrollado por Oracle (originalmente por Innotek → Sun → Oracle). Permite crear y ejecutar **máquinas virtuales (VMs)** en Windows, macOS, Linux y Solaris; cada VM actúa como un equipo completo con CPU virtual, memoria, disco y tarjetas de red. Es gratuito (GPL) y muy usado para desarrollo, pruebas, laboratorios y entornos de usuario/desktop.

### Características clave

- Soporta muchos sistemas invitados: Windows, Linux, BSD, Solaris, macOS (limitado).
- Gestión desde GUI (**VirtualBox Manager**) y CLI (**VBoxManage**).
- **Snapshots** (puntos de restauración).
- **Shared Folders** y portapapeles compartido (host ↔ guest).
- Formatos de disco: **VDI** (nativo), VMDK, VHD, etc.
- Múltiples modos de red: **NAT**, **Bridged**, **Host-only**, **Internal**, **NAT Network**.
- **Guest Additions** para integración (drivers, mejor rendimiento, carpetas compartidas, sincronización de mouse).
- Soporta ejecución _headless_ (sin GUI) y servidor RDP (VRDP) mediante el Extension Pack.
- Exportar/importar OVAs/OVFs para mover VMs.

### Arquitectura y componentes

- **Host OS**: Windows / Linux / macOS.
- **VirtualBox core**: el servicio que crea procesos VM.
- **Guest**: sistema operativo instalado dentro de la VM.
- **VirtualBox Manager**: GUI para crear/administrar VMs.
- **VBoxManage**: herramienta de línea de comandos para todo tipo de automatización.
- **Guest Additions**: conjunto de drivers/servicios dentro del guest que mejora integración y rendimiento.
- **Extension Pack** (instalable aparte): soporte USB 2/3, VRDP, PXE para Intel, cifrado de discos, etc.

### Instalación rápida

- **Ubuntu / Debian**:

```bash
sudo apt update
sudo apt install virtualbox
# (o usar repos oficiales de Oracle para versiones más nuevas)
```

- **Windows / macOS**: descarga el instalador desde el sitio oficial de VirtualBox o usa `brew install --cask virtualbox` en macOS y sigue el instalador gráfico.
- **Extension Pack**: descarga la versión que coincida con tu VirtualBox y haz doble clic para instalar (necesario para VRDP y soporte USB avanzado).

### Uso práctico — crear una VM (GUI)

1. Abrir **VirtualBox Manager** → **New**.
2. Nombre, tipo y versión (por ejemplo: _Ubuntu 22.04 (64-bit)_).
3. Asignar memoria (p. ej. 4096 MB) y CPUs.
4. Crear disco virtual (VDI, dinámico o fijo, tamaño p. ej. 40 GB).
5. Configurar Storage → conectar ISO de instalación (ej. `ubuntu.iso`) al controlador IDE/SATA.
6. Iniciar VM → instalar OS → instalar **Guest Additions** (en el menú _Devices → Insert Guest Additions CD image..._ y ejecutar dentro del guest).

### Uso avanzado — comandos `VBoxManage` (ejemplos concretos)

#### Crear VM, crear disco, adjuntar ISO y arrancar (headless)

```bash
# 1. Crear VM y registrarla
VBoxManage createvm --name "ubuntu22" --ostype Ubuntu_64 --register

# 2. Configurar memoria y CPU y NIC en NAT
VBoxManage modifyvm "ubuntu22" --memory 4096 --cpus 2 --nic1 nat

# 3. Crear disco virtual (40 GB)
VBoxManage createmedium disk --filename "$HOME/VirtualBox VMs/ubuntu22/ubuntu22.vdi" --size 40960 --format VDI

# 4. Añadir controlador SATA y adjuntar disco
VBoxManage storagectl "ubuntu22" --name "SATA Controller" --add sata --controller IntelAhci
VBoxManage storageattach "ubuntu22" --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium "$HOME/VirtualBox VMs/ubuntu22/ubuntu22.vdi"

# 5. Adjuntar ISO como DVD (instalador)
VBoxManage storageattach "ubuntu22" --storagectl "SATA Controller" --port 1 --device 0 --type dvddrive --medium "/path/to/ubuntu-22.04.iso"

# 6. (Opcional) Configurar port forwarding NAT para SSH (host:2222 -> guest:22)
VBoxManage modifyvm "ubuntu22" --natpf1 "guestssh,tcp,,2222,,22"

# 7. Arrancar en modo headless
VBoxManage startvm "ubuntu22" --type headless
```

Luego puedes conectarte por SSH a `localhost:2222` si instalas y habilitas `sshd` en el guest:

```bash
ssh -p 2222 usuario@localhost
```

### Snapshots

```bash
# Tomar snapshot
VBoxManage snapshot "ubuntu22" take "before-update" --description "Antes del update"

# Listar snapshots
VBoxManage snapshot "ubuntu22" list

# Restaurar snapshot
VBoxManage snapshot "ubuntu22" restore "before-update"
```

#### Clonar y exportar/importar

```bash
# Clonar (full clone)
VBoxManage clonevm "ubuntu22" --name "ubuntu22-clone" --register --mode all

# Exportar a OVA
VBoxManage export "ubuntu22" --output ubuntu22.ova

# Importar OVA
VBoxManage import ubuntu22.ova
```

#### Compartir carpetas (host → guest)

```bash
# Añadir carpeta compartida y montar automount
VBoxManage sharedfolder add "ubuntu22" --name "shared" --hostpath "/home/gustavo/shared" --automount

# En el guest (Linux), después de instalar Guest Additions:
sudo mount -t vboxsf shared /mnt/shared   # si no se automonta
```

#### Activar VRDP (Remote Desktop para la VM) — requiere Extension Pack

```bash
VBoxManage modifyvm "ubuntu22" --vrde on --vrdeport 5000
# Conectar con un cliente RDP a host:5000
```

### Modos de red — explicados con ejemplos de uso

- **NAT (por defecto)**: la VM sale a Internet a través del host. Es simple y seguro. Para acceder a servicios del guest desde el host/externo, usa _port forwarding_ (ej. `--natpf1 "guestssh,tcp,,2222,,22"`).
- **NAT Network**: permite que varias VMs en la misma NAT se comuniquen entre ellas y tengan acceso a Internet; permite DHCP interno.
- **Bridged Adapter**: la VM recibe IP en la misma red física que el host (útil si quieres que la VM sea accesible desde otros equipos de la LAN como un equipo independiente).
- **Host-only Adapter**: crea una red entre host y VMs (sin acceso a Internet por defecto). Ideal para desarrollo aislado.
- **Internal Network**: red aislada entre VMs (host no participa), buena para simular redes internas.

### Guest Additions — qué y por qué

**Guest Additions** es un paquete a instalar dentro del sistema operativo invitado que aporta:

- Drivers de video y red mejorada (mejor rendimiento).
- Integración del ratón y redimensionado automático de pantalla.
- Carpetas compartidas y portapapeles compartido.
- Mejora de rendimiento gráfico y 3D (parcial).

**Instalación (Linux guest)**:

1. Desde el host: en el menú de la VM → _Devices → Insert Guest Additions CD image..._
2. Montar y ejecutar dentro del guest:

```bash
sudo apt update
sudo apt install build-essential dkms linux-headers-$(uname -r)
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
```

### Buenas prácticas y consejos de rendimiento

- Habilita **VT-x/AMD-V** en BIOS si tu CPU lo soporta; mejora rendimiento y permite nested virtualization.
- Para I/O intensivo, usar disco **fijo** (no dinámico) puede dar mejor rendimiento.
- Dedica suficientes vRAM y vCPUs, pero evita overcommit extremo en el host.
- Instala **Guest Additions** en cada guest.
- Usa **bridged** si la VM necesita ser un nodo en la LAN; usa **NAT** si solo requiere salida a Internet.
- Haz snapshots antes de cambios importantes, pero **no los uses como backups a largo plazo**.
- Si automatizas entornos reproducibles, usa **Vagrant** con proveedor VirtualBox.

### Limitaciones y diferencia frente a hypervisors tipo-1

- Al ser _tipo 2_, VirtualBox corre sobre un OS host, por lo que el rendimiento es menor que hypervisors _bare-metal_ (p. ej. VMware ESXi, Hypervisor KVM en bare metal).
- Excelente para desktop, desarrollo y testing; menos recomendado para producción a escala en datacenters.

### Casos de uso típicos

- Laboratorios y pruebas de software (crear/descartar VMs rápido).
- Ejecutar sistemas operativos múltiples en una máquina de desarrollo.
- Entornos de testing CI (con Vagrant).
- Simular redes internas y topologías con varias VMs.
- Exportar VM en OVA para compartir con colegas.

### Ejemplo final — flujo práctico (todo junto)

1. Instalas VirtualBox en tu host.
2. Creas una VM con `VBoxManage createvm` o usando GUI.
3. Asignas memoria/CPU y creas un disco virtual.
4. Adjuntas el ISO y arrancas la VM para instalar el OS.
5. Instalas **Guest Additions** en el guest.
6. Configuras red (NAT + forwarding `2222:22` para SSH) y pruebas conexión `ssh -p 2222 usuario@localhost`.
7. Tomas un snapshot antes de una update: `VBoxManage snapshot "vm" take "pre-update"`.
8. Exportas la VM a OVA para compartir: `VBoxManage export "vm" -o vm.ova`.

---

[🔼](#índice)

---

## **149. Cómo usar VirtualBox**

### 1 — Antes de empezar (requisitos)

- CPU con virtualización (VT-x / AMD-V) **habilitada en BIOS/UEFI**.
- Espacio en disco suficiente y memoria libre en el host.
- VirtualBox (versión reciente) + Extension Pack (si necesitas USB 2/3, VRDP, cifrado).
- Imagen ISO del sistema operativo que quieras instalar (ej. `ubuntu-22.04.iso`).

### 2 — Instalación (resumen)

- **Windows / macOS**: descarga el instalador de VirtualBox y ejecuta. Instala también el _Extension Pack_ que coincida con tu versión.
- **Ubuntu/Debian** (ejemplo):

```bash
sudo apt update
sudo apt install virtualbox
# O usar repos oficial de Oracle para versión más nueva
```

Si instalas desde repos, instala también `virtualbox-ext-pack` o descarga el Extension Pack desde la web oficial.

### 3 — Crear una VM (GUI - VirtualBox Manager)

1. Abre **VirtualBox Manager** → **New**.
2. Pon nombre, tipo y versión (p. ej. _Ubuntu 64-bit_).
3. Asigna memoria (RAM) y CPUs (ej. 4096 MB, 2 vCPU).
4. Crea disco virtual (VDI) — tamaño y tipo (dinámico vs fijo).
5. Configura Storage → adjunta ISO en controlador IDE/SATA.
6. Networking → elige NAT o Bridged según necesidad.
7. Start → instala OS desde la consola.
8. Instala **Guest Additions** dentro del guest (ver sección 6).

### 4 — Crear una VM desde CLI (`VBoxManage`) — script ejemplo completo

Copia y pega (Linux/macOS/Windows PowerShell funciona igual con `VBoxManage` en PATH). Cambia variables al inicio.

```bash
#!/usr/bin/env bash
VMNAME="ubuntu22"
ISO_PATH="$HOME/isos/ubuntu-22.04.iso"
VM_DIR="$HOME/VirtualBox VMs/$VMNAME"
VDI_PATH="$VM_DIR/$VMNAME.vdi"

# 1) Crear VM
VBoxManage createvm --name "$VMNAME" --ostype Ubuntu_64 --register

# 2) Recursos
VBoxManage modifyvm "$VMNAME" --cpus 2 --memory 4096 --vram 64

# 3) NAT con port-forwarding (host:2222 -> guest:22 para SSH)
VBoxManage modifyvm "$VMNAME" --nic1 nat
VBoxManage modifyvm "$VMNAME" --natpf1 "guestssh,tcp,,2222,,22"

# 4) Disco
VBoxManage createmedium disk --filename "$VDI_PATH" --size 40960 --format VDI
VBoxManage storagectl "$VMNAME" --name "SATA" --add sata --controller IntelAhci
VBoxManage storageattach "$VMNAME" --storagectl "SATA" --port 0 --device 0 --type hdd --medium "$VDI_PATH"

# 5) Adjuntar ISO al IDE o SATA (DVD)
VBoxManage storageattach "$VMNAME" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium "$ISO_PATH"

# 6) Arrancar headless
VBoxManage startvm "$VMNAME" --type headless
echo "VM $VMNAME iniciada. Si instalaste ssh en el guest, puedes conectarte: ssh -p 2222 user@localhost"
```

### 5 — Arrancar en modo headless y conectar

- Iniciar headless:
  `VBoxManage startvm "miVM" --type headless`
- Apagar con gracia:
  `VBoxManage controlvm "miVM" acpipowerbutton`
- Forzar apagado:
  `VBoxManage controlvm "miVM" poweroff`
- Conectar por SSH cuando uses NAT+forwarding (ejemplo anterior):
  `ssh -p 2222 usuario@localhost`

### 6 — Guest Additions (imprescindible para rendimiento e integración)

**Linux guest (ej. Ubuntu)**:

1. En VirtualBox: `Devices → Insert Guest Additions CD image...`
2. En el guest:

```bash
sudo apt update
sudo apt install build-essential dkms linux-headers-$(uname -r)
sudo mount /dev/cdrom /mnt
cd /mnt
sudo ./VBoxLinuxAdditions.run
# Reinicia el guest después
```

**Windows guest**: monta Guest Additions y ejecuta `VBoxWindowsAdditions.exe` como Administrador; reinicia.

### 7 — Modos de red y ejemplos prácticos

- **NAT (por defecto)**: guest sale a Internet; para exponer puertos usa port-forwarding:

  ```bash
  VBoxManage modifyvm "miVM" --natpf1 "http,tcp,,8080,,80"
  # ahora host:8080 -> guest:80
  ```

- **NAT Network**: varias VMs comparten NAT y pueden comunicarse entre sí.
  Crear NAT Network (ejemplo):

  ```bash
  VBoxManage natnetwork add --netname natnet1 --network "10.10.10.0/24" --dhcp on
  VBoxManage modifyvm "miVM" --nic1 natnetwork --nat-network1 natnet1
  ```

- **Bridged**: VM tiene IP en la misma LAN que el host. Útil para que otros equipos accedan a la VM.
- **Host-only**: red entre host y guests (útil para testing sin Internet).
- **Internal**: red solo entre VMs (host no participa).

### 8 — Carpetas compartidas (host ↔ guest)

**Agregar (host):**

```bash
VBoxManage sharedfolder add "miVM" --name "shared" --hostpath "/home/gustavo/shared" --automount
```

**Montar en Linux guest (después de Guest Additions):**

```bash
sudo mount -t vboxsf shared /mnt/shared   # si no se automonta
```

**Windows guest:** aparecerá como unidad de red o monta con:

```
net use X: \\vboxsrv\shared
```

### 9 — Snapshots, clones y export/import

- Tomar snapshot:
  `VBoxManage snapshot "miVM" take "pre-update" --description "Antes del update"`
- Listar:
  `VBoxManage snapshot "miVM" list`
- Restaurar:
  `VBoxManage snapshot "miVM" restore "pre-update"`
- Clonar (full clone):
  `VBoxManage clonevm "miVM" --name "miVM-clone" --register --mode all`
- Exportar a OVA:
  `VBoxManage export "miVM" -o miVM.ova`
- Importar OVA:
  `VBoxManage import miVM.ova`

### 10 — VRDP (acceso remoto RDP a la VM) — requiere Extension Pack

Activar VRDP y puerto:

```bash
VBoxManage modifyvm "miVM" --vrde on
VBoxManage modifyvm "miVM" --vrdeport 5000
# Conéctate con cualquier cliente RDP a host:5000
```

### 11 — Automatización con Vagrant (ejemplo rápido)

`Vagrant` facilita reproducir entornos con VirtualBox:

`Vagrantfile` mínimo:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end
end
```

Usar:

```bash
vagrant up
vagrant ssh
vagrant halt
vagrant package --output mybox.box
```

### 12 — Buenas prácticas y tuning

- Habilita VT-x/AMD-V en BIOS.
- Instala Guest Additions en **cada** guest.
- No asignes más vCPUs o RAM de las que disponga el host (evita overcommit extremo).
- Para I/O intensivo, usar discos **fijos** y/o pasar a SSD.
- Usa `vmxnet3` equivalente (en VirtualBox el driver se maneja con Guest Additions).
- Evita snapshots largos: usarlos como checkpoint temporal, no backup.
- Haz backups exportando a OVA o usando herramientas de snapshot del host.

### 13 — Problemas comunes y soluciones rápidas

- **VT-x/AMD-V no disponible** → habilitar en BIOS/UEFI (y desactivar Hyper-V en Windows si choca).
- **Kernel driver no cargado (Linux host)** → `sudo /sbin/vboxconfig` o reinstala DKMS módulos.
- **Guest Additions falla en Ubuntu con Secure Boot** → firma de módulos o deshabilitar Secure Boot (o usar `mokutil` para firmar).
- **Red bridged no obtiene IP** → selecciona la interfaz física correcta y permisos en host.
- **Performance pobre** → instala Guest Additions, asigna más RAM/vCPU si hay recursos, usa disco fijo.

### 14 — Cheatsheet / comandos útiles (rápido)

```bash
# listar VMs
VBoxManage list vms

# iniciar VM (GUI)
VBoxManage startvm "miVM"

# iniciar headless
VBoxManage startvm "miVM" --type headless

# detener VM (ACPI)
VBoxManage controlvm "miVM" acpipowerbutton

# tomar snapshot
VBoxManage snapshot "miVM" take "label"

# exportar OVA
VBoxManage export "miVM" -o miVM.ova

# añadir NAT port forwarding
VBoxManage modifyvm "miVM" --natpf1 "ssh,tcp,,2222,,22"
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**           | **Siguiente 3**     |
| ------------------ | --------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./6_1_VMWare.md) | [⏩](./6_3_ESXi.md) |
