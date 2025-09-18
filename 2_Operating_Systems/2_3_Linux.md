| **Inicio**         | **atrás 2**            | **Siguiente 4**      |
| ------------------ | ---------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./2_2_Windows.md) | [⏩](./2_4_MacOS.md) |

---

## **Índice**

| Temario                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------- |
| [38. Hoja de ruta de Linux](#38-hoja-de-ruta-de-linux)                                                                 |
| [39. Linux from scratch](#39-linux-from-scratch)                                                                       |
| [40. Hoja de trucos de comandos de Linux](#40-hoja-de-trucos-de-comandos-de-linux)                                     |
| [41. Linux en 100 comandos](#41-linux-en-100-comandos)                                                                 |
| [42. Introducción a Linux](#42-introducción-a-linux)                                                                   |
| [43. Explora las publicaciones más importantes sobre Linux](#43-explora-las-publicaciones-más-importantes-sobre-linux) |

---

# **Linux**

## **38. Hoja de ruta de Linux**

### 1) Objetivo de la hoja de ruta

Que seas capaz de **usar Linux con confianza**, administrar servidores, automatizar tareas, desplegar contenedores/cluster, asegurar sistemas y —si te interesa— tocar temas avanzados como kernel o sistemas embebidos. Al final tendrás proyectos reales en tu portafolio.

### 2) Rutas / roles (elige una o varias)

- **Administrator / Sysadmin** — instalar/gestionar servicios, usuarios, backups, seguridad.
- **DevOps / Cloud engineer** — contenedores, CI/CD, orquestación (Docker, Kubernetes), infra como código.
- **Developer** — usar Linux como entorno de desarrollo (toolchains, WSL, depuración).
- **Security / Pentesting** — hardening, forense, detección (Kali, herramientas forense).
- **Kernel / Embedded** — compilación del kernel, drivers, RTOS, Raspberry Pi.

### 3) Recomendación de distribuciones según propósito

- **Principiante / Desktop:** Ubuntu / Linux Mint / Fedora Workstation.
- **Servidores / Empresas:** Debian, CentOS Stream / AlmaLinux / Rocky Linux, RHEL.
- **Últimas tecnologías / bleeding edge:** Fedora.
- **Aprendizaje profundo del sistema:** Arch Linux (más exigente).
- **IoT / SBC:** Raspberry Pi OS.
- **Seguridad:** Kali Linux (solo para testing ético).

### 4) Niveles de la hoja de ruta — temas clave con ejemplos y ejercicios

#### Nivel **Básico** (Fundamentos — 0 a 2 semanas)

**Objetivos:** Familiarizarte con la terminal, filesystem, permisos y comandos esenciales.

- Temas y comandos:

  - Navegación: `ls`, `cd`, `pwd`, `tree` (si está instalado).
  - Manipular archivos: `cp`, `mv`, `rm`, `mkdir`, `rmdir`, `touch`.
  - Ver contenido: `cat`, `less`, `head`, `tail`.
  - Buscar: `find`, `locate`, `which`.
  - Texto y filtros: `grep`, `sort`, `uniq`, `wc`.
  - Permisos: `chmod`, `chown`, `chgrp`.
  - Procesos: `ps aux`, `top`, `kill`, `killall`.

- Mini-ejercicio: crea una jerarquía de carpetas para un proyecto, añade archivos y usa `find` + `grep` para localizar líneas específicas.
- Ejemplo:

```bash
mkdir -p ~/proyectos/miweb/{src,logs,backup}
echo "Hola Mundo" > ~/proyectos/miweb/src/index.html
grep -R "Hola" ~/proyectos
```

#### Nivel **Intermedio** (Administración y scripting — 1–3 meses)

**Objetivos:** Administrar usuarios, servicios, paquetería, redes y escribir scripts básicos.

- Temas:

  - Gestión de paquetes: `apt` (Debian/Ubuntu), `yum/dnf` (RHEL/Fedora), `pacman` (Arch).
  - Usuarios y grupos: `useradd`, `usermod`, `passwd`, `groupadd`.
  - Servicios y systemd: `systemctl start/stop/status/enable/disable`.
  - Logs: `journalctl -u nombre-servicio`, `/var/log/*`.
  - Redes: `ip addr`, `ip route`, `ss`/`netstat`, `nmcli`.
  - Firewall: `ufw` (fácil), `iptables`/`nftables` (avanzado).
  - Bash scripting: variables, condicionales, loops, funciones, `awk`, `sed`.

- Ejercicios prácticos:

  1. Instala Apache/Nginx y comparte un sitio:

     ```bash
     sudo apt update
     sudo apt install -y nginx
     sudo systemctl enable --now nginx
     echo "<h1>Mi sitio en Linux</h1>" | sudo tee /var/www/html/index.html
     ```

  2. Script de backup diario (compress and rotate):

     ```bash
     #!/usr/bin/env bash
     SRC="/home/tuusuario/proyectos"
     DEST="/home/tuusuario/backups"
     mkdir -p "$DEST"
     tar -czf "$DEST/proyectos_$(date +%F).tar.gz" -C "$SRC" .
     ```

  3. Crear systemd unit file simple (`/etc/systemd/system/mi-backup.service`):

     ```ini
     [Unit]
     Description=Backup diario

     [Service]
     Type=oneshot
     ExecStart=/home/tuusuario/scripts/backup.sh

     [Install]
     WantedBy=multi-user.target
     ```

  4. Habilitar cron/timers para automatizarlo.

#### Nivel **Avanzado** (Redes, seguridad, contenedores — 3–6 meses)

**Objetivos:** Automatizar infra, desplegar contenedores, asegurar sistemas, monitorizar.

- Temas:

  - **Containers:** Docker (build/run), imágenes, Docker Compose.
  - **Orquestación:** Kubernetes básico (minikube / kind / kubeadm).
  - **Infraestructura como código:** Ansible (playbooks), Terraform (infra cloud).
  - **Seguridad:** SSH hardening, fail2ban, SELinux/AppArmor, políticas de usuario, hardening CIS.
  - **Monitorización:** Prometheus + Grafana, `top/htop`, `iostat`, `vmstat`, `netstat`.
  - **Backups y recuperación:** rsync, snapshots LVM, BTRFS/ZFS features.

- Ejercicios prácticos:

  - Dockerfile mínimo:

    ```dockerfile
    FROM python:3.11-slim
    WORKDIR /app
    COPY requirements.txt .
    RUN pip install -r requirements.txt
    COPY . .
    CMD ["python", "app.py"]
    ```

  - Desplegar una app con Docker Compose (web + db).
  - Crear un playbook Ansible para instalar y configurar Nginx en 3 servidores.
  - Montar un cluster Kubernetes local con `kind` y desplegar una app.

#### Nivel **Experto** (Kernel, performance, scale — 6–12+ meses)

**Objetivos:** Optimizar, depurar problemas complejos, desarrollar módulos del kernel y arquitecturas a gran escala.

- Temas:

  - Compilar kernel y módulos (`make menuconfig`, `make`, `make modules_install`).
  - Tracing/Profiling: `perf`, `strace`, `bpftrace`.
  - Desarrollo de drivers simples (módulo kernel “Hello world”).
  - Scale out: Ingress, service meshes (Istio), autoscaling, logging centralizado (ELK/EFK).
  - Hardening avanzado: seccomp, namespaces, cgroups, grsecurity (si aplica).

- Ejercicios prácticos:

  - Escribir y compilar un módulo kernel simple (`hello.c`) y cargarlo con `insmod`:

    ```c
    // hello.c
    #include <linux/init.h>
    #include <linux/module.h>
    MODULE_LICENSE("GPL");
    static int __init hello_init(void) {
      printk(KERN_INFO "hola_kernel: cargado\n");
      return 0;
    }
    static void __exit hello_exit(void) {
      printk(KERN_INFO "hola_kernel: removido\n");
    }
    module_init(hello_init);
    module_exit(hello_exit);
    ```

    Compilar con un Makefile y cargar: `sudo insmod hello.ko`, ver `dmesg`.

  - Usar `perf record`/`perf report` para encontrar hotspots en una app.

### 5) Planes de estudio sugeridos (horas/semana) — elige el que quieras

#### Crash: **30 días** (2–3 horas/día) — objetivo: competencia práctica básica

- Semana 1: terminal, comandos, filesystem, permisos, usuarios.
- Semana 2: paquetes, instalar servicios (nginx), systemd, logs.
- Semana 3: scripting bash, cron, backups básicos.
- Semana 4: Docker básico, git, proyecto final (sitio en nginx con Docker).

#### Intensivo: **3 meses** (8–10 horas/semana) — objetivo: administrador competente

- Mes 1: Nivel básico + intermedio completo (paquetes, users, services, scripting).
- Mes 2: Networking, firewall, SSH, backups, introducción a Docker.
- Mes 3: Ansible básico, minikube, monitorización simple, proyecto final (deploy contenedores y monitoreo).

#### Profesional: **6 meses** (5–8 horas/semana) — objetivo: DevOps / Sysadmin sólido

- Sigue el plan 3 meses + despliegue Kubernetes, Terraform, logging, seguridad aplicada, pruebas de recuperación, preparar para certificación.

#### Experto: **12 meses+** — objetivo: SRE / Kernel dev / Arquitecto

- Todo lo anterior + kernel dev, profiling, scale (Istio), optimización, proyecto de contribución open source.

### 6) Proyectos prácticos (ordenados por complejidad) — añade a tu portafolio

1. **Setup LAMP simple** en VM (Linux + Apache + MySQL + PHP). Documenta pasos y backup.
2. **Script de backup + restore** automatizado con pruebas.
3. **Hardening**: aplicar checklist CIS y medir con Lynis (o equivalente).
4. **Infra con Ansible:** playbook que deja un servidor listo con Nginx, firewall, usuario admin.
5. **Docker + Compose app**: web + db + reverse proxy + certificados auto-signed.
6. **Kubernetes**: desplegar una app con Ingress, autoscaling y monitoreo (Prometheus + Grafana).
7. **Pipeline CI/CD**: GitHub Actions / GitLab CI que construye imagen y despliega a cluster.
8. **Módulo kernel**: “hello world” + un módulo que exponga un parámetro sysfs.
9. **Proyecto de seguridad**: establecer logging centralizado y detectar una intrusión simulada (Sysmon-Linux o auditd).
10. **Contribuir a OSS**: arreglar un bug pequeño en un repo de Linux o en un paquete.

### 7) Herramientas y comandos imprescindibles (referencia rápida)

- Navegación/archivo: `ls, cd, cp, mv, rm, find, du, df`
- Procesos: `ps aux, top, htop, nice, renice, kill`
- Networking: `ip addr, ip route, ss, netstat (legacy), ping, traceroute`
- Systemd & logs: `systemctl`, `journalctl -u servicio`, `journalctl -b`
- Discos: `lsblk, fdisk, blkid, mount, umount, mount -o`
- Paquetes: `apt`, `dnf`, `yum`, `pacman`
- Texto: `grep, sed, awk, cut, tr, sort`
- Compresión: `tar, gzip, bzip2, zip`
- SSH: `ssh`, `scp`, `rsync -avz`
- Contenedores: `docker build/run`, `docker-compose`, `kubectl`
- Perfilado: `strace`, `ltrace`, `perf`, `top`, `iotop`
- Seguridad: `iptables`, `nft`, `ufw`, `fail2ban`, `selinux`/`getenforce`

### 8) Recursos recomendados (libros y cursos — títulos para buscar)

- **Libro**: _The Linux Command Line_ (William Shotts) — excelente para principiantes.
- **Libro**: _How Linux Works_ — entender internals.
- **Cursos**: Linux Foundation (LFCS/LFS), cursos de administración en plataformas MOOC.
- **Práctica**: documentación oficial de la distro que uses, manpages (`man comando`) y foros/StackOverflow.
- **Certificaciones (opciones):** LPIC-1, CompTIA Linux+, LFCS, RHCSA (RHEL) — útiles para roles corporativos.

### 9) Buenas prácticas durante el aprendizaje

- **Practica en un laboratorio**: usa VMs (VirtualBox / VMware), WSL2 o Raspberry Pi.
- **Documenta todo**: pasos, errores y soluciones — forma tu wiki personal (Markdown).
- **Automatiza temprano**: escribe scripts y playbooks en vez de repetir comandos manuales.
- **Backups y snapshots**: antes de experimentar con kernel o particiones.
- **Control de versiones**: guarda tus scripts y config en Git.
- **Seguridad desde el día 1**: usa SSH keys, deshabilita login root por contraseña, aplica actualizaciones.

### 10) Cómo evaluar tu progreso (checklist de hitos)

- Puedo: navegar archivos, modificar permisos y resolver problemas simples. ✅
- Puedo: instalar/configurar un servicio y exponerlo por firewall. ✅
- Puedo: escribir scripts bash y automatizar tareas con cron/ systemd timers. ✅
- Puedo: construir una imagen Docker y desplegarla. ✅
- Puedo: crear playbook Ansible para aprovisionar servidores. ✅
- Puedo: debuggear un problema de CPU o I/O con `perf`/`iotop`. ✅
- Puedo: compilar un kernel básico y cargar un módulo. ✅

Si puedes marcar al menos 5/7 → ya eres un profesional operativo.

### 11) Plantillas útiles (ejemplos rápidos que puedes copiar)

**Vagrantfile** (levanta una VM Ubuntu para laboratorio):

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.10"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end
end
```

**Ansible playbook simple** (instalar nginx):

```yaml
- hosts: web
  become: yes
  tasks:
    - name: ensure nginx is installed
      apt:
        name: nginx
        state: present
        update_cache: yes
    - name: ensure nginx running
      service:
        name: nginx
        state: started
        enabled: yes
```

**systemd service** (ejemplo ya mostrado arriba) y **Dockerfile** también incluidos en secciones previas.

### 12) Errores comunes y cómo evitarlos

- **No usar un entorno de pruebas** → siempre rompe algo fatalmente.
- **Editar configs sin backup** → crea copias antes.
- **Falta de automatización** → repetir tareas humanos = errores e inconsistencia.
- **Ignorar logs** → `journalctl` y `/var/log` son el primer lugar a revisar.
- **Compartir claves privadas** → usa ssh-agent, vaults o secret managers.

---

[🔼](#índice)

---

## **39. Linux from scratch**

**Linux From Scratch (LFS)** es un proyecto y un libro que te guía para construir **tu propio sistema Linux desde el código fuente**, paso a paso, sobre un Linux anfitrión ya instalado. El objetivo no es “tener otra distro”, sino **aprender cómo se arma Linux por dentro** (toolchain, libc, kernel, arranque, ficheros, usuarios, etc.) y obtener un sistema **mínimo, compacto y a tu medida**. El libro oficial se puede leer gratis en línea.

### 1) ¿Qué vas a construir exactamente?

Un sistema base que incluye:

- **Toolchain** (binutils + gcc) aislado para compilar el resto.
- **Librería C (glibc)**, **núcleo Linux** y un conjunto mínimo de utilidades (bash, coreutils, util-linux, etc.).
- **Gestión de arranque** (GRUB), **fstab**, **usuarios/grupos** y **red básica**.
  Después puedes continuar con **BLFS** (Beyond Linux From Scratch) para añadir **red avanzada, entorno gráfico (X11/Wayland), escritorio (KDE/GNOME/Xfce), navegadores, sonido, impresión, etc.**

### 2) Requisitos realistas (antes de empezar)

- **Un Linux anfitrión** (Ubuntu, Debian, Fedora, etc.) actualizado, con compiladores y herramientas (`build-essential`, `bison`, `gawk`, `texinfo`, etc.).
- **Conexión estable** y **espacio en disco**: \~20–40 GB para ir cómodos (fuentes, compilaciones, kernel).
- **Tiempo y paciencia**: compilará _todo_ (usa `-j$(nproc)` para paralelizar).
- **Manual a mano**: sigue el libro al pie de la letra (versión estable o “development”).

### 3) Vista general del **proceso LFS** (mapa mental)

1. **Preparación del disco y el punto de montaje**
2. **Descarga de fuentes y parches**
3. **Toolchain temporal** en **/tools** (compiladores “limpios” que no contaminan el host)
4. **Entrar en chroot** al sistema LFS y **construir el sistema base**
5. **Configurar sistema** (initscripts o systemd, fstab, red, reloj)
6. **Compilar kernel**, **instalar GRUB** y **reiniciar** al nuevo Linux

> La **separación por chroot** es clave: cuando entras a `chroot` tu “/” pasa a ser el árbol del LFS; y **PATH** se ajusta para dejar de usar herramientas del host y usar las del sistema que construyes.

### 4) Paso a paso (con ejemplos de comandos)

> **Nota:** Estos ejemplos son ilustrativos para que entiendas el flujo. En la práctica, copia exactamente los comandos de la versión del libro que sigas.

#### 4.1. Preparar el entorno y el árbol LFS

```bash
# En el anfitrión (con sudo)
export LFS=/mnt/lfs
sudo mkdir -pv $LFS
# Particiona tu disco/imagen (ej.: /dev/sdb) con fdisk/cfdisk y crea:
#  - raíz (ext4),  - swap
sudo mkfs.ext4 /dev/sdb1
sudo mkswap /dev/sdb2
sudo mount -v -t ext4 /dev/sdb1 $LFS
sudo swapon /dev/sdb2

# Estructura inicial
sudo mkdir -pv $LFS/{sources,tools}
sudo chmod -v a+wt $LFS/sources
```

**Descarga de fuentes y verificación** (el libro lista URLs y hashes para cada paquete): coloca todos los `.tar.xz/.gz` y parches dentro de `$LFS/sources` y **comprueba checksum**.

#### 4.2. Crear el **usuario lfs** y variables

```bash
sudo groupadd lfs
sudo useradd -s /bin/bash -g lfs -m -k /dev/null lfs
sudo chown -v lfs $LFS/{sources,tools}
sudo su - lfs

cat >> ~/.bashrc <<'EOF'
set -e
export LFS=/mnt/lfs
export LC_ALL=POSIX
umask 022
EOF
source ~/.bashrc
```

#### 4.3. **Toolchain temporal** en `/tools`

El objetivo es **construir un compilador y binutils autosuficientes** para que todo lo demás **no dependa** del sistema anfitrión (evitando “contaminación” de headers/libs del host).

Orden (simplificado): **binutils (pass 1) → gcc (pass 1) → linux-headers → glibc → binutils (pass 2) → gcc (pass 2)** y luego utilidades temporales (bash, coreutils, etc.) **instaladas en `/tools`** con `--prefix=/tools`.

Ejemplo (muy abreviado) de compilación típica:

```bash
tar xf binutils-*.tar.xz && cd binutils-*/
mkdir build && cd build
../configure --prefix=/tools --with-sysroot=$LFS --target=$(uname -m)-lfs-linux-gnu --disable-nls --disable-werror
make -j"$(nproc)"
make install
```

Repite para **GCC**, usando **mpfr/gmp/mpc** como dice el libro, y para **glibc** (construida ya usando el toolchain recién creado).

#### 4.4. **Entrar en chroot** y construir el **sistema base**

Cuando el toolchain temporal y utilidades mínimas están listas, **montas pseudo-sistemas** (proc, sysfs, dev) y entras al **chroot**:

```bash
sudo mount -v --bind /dev $LFS/dev
sudo mount -vt devpts devpts $LFS/dev/pts
sudo mount -vt proc   proc   $LFS/proc
sudo mount -vt sysfs  sysfs  $LFS/sys

sudo chroot "$LFS" /usr/bin/env -i \
  HOME=/root TERM="$TERM" PS1='(lfs-chroot) \u:\w\$ ' \
  PATH=/usr/bin:/usr/sbin \
  /bin/bash --login
```

Dentro del chroot:

- Instalas **perl, python, coreutils, sed, grep, bash, make, util-linux, e2fsprogs, shadow, iproute2**, etc., **ya a `/usr`**.
- Ajustas **/etc/passwd**, **/etc/group**, **/etc/fstab**, **zona horaria**, **locales** y **red** (por ejemplo, interfaz `eth0` o `ens33`).
- Configuras **systemd** (o **SysV** si sigues la edición System V) según el libro.

> El libro explica que **a partir de este punto ya no se usa `/tools` en el PATH**: construyes con tu **toolchain final** dentro del chroot.

#### 4.5. **Kernel Linux** y **GRUB**

Compilar el kernel (ejemplo básico):

```bash
tar xf linux-*.tar.xz && cd linux-*/
make mrproper
make menuconfig      # elige drivers y opciones
make -j"$(nproc)"
make modules_install
cp -v arch/x86/boot/bzImage /boot/vmlinuz-<tu-version>
cp -v System.map /boot/
cp -v .config /boot/config-<tu-version>
```

Instalar **GRUB**:

```bash
grub-install /dev/sdb
grub-mkconfig -o /boot/grub/grub.cfg
```

Edita `grub.cfg` si es necesario (el generador suele detectarlo). Asegura que `fstab` tenga entradas correctas:

```fstab
# /etc/fstab
/dev/sdb1   /      ext4   defaults   1 1
/dev/sdb2   none   swap   sw         0 0
```

**Sal del chroot**, desmonta y **reinicia** desde el disco LFS.

### 5) ¿Qué sigue después? **BLFS** y automatización

- **BLFS** te guía para añadir **Xorg/Wayland, drivers gráficos, KDE/GNOME/Xfce, NetworkManager, CUPS, ALSA/PulseAudio/PipeWire, navegadores, lenguajes, etc.** Es “tu distro, a tu manera”.
- **ALFS/jhalfs** automatiza el proceso extrayendo instrucciones del XML del libro; muy útil si reconstruyes a menudo o quieres validar libros nuevos.

### 6) Ejemplos de decisiones típicas (y qué implican)

- **systemd vs SysV**: systemd simplifica servicios/registro/logs, pero si prefieres lo clásico puedes usar la edición System V (BLFS tiene guías para ambos).
- **Optimización de compilación**: variables como `CFLAGS="-O2 -pipe"` y `-march=native` pueden acortar tiempos pero dificultar reproducibilidad.
- **Módulos del kernel**: compilar mínimo necesario reduce tamaño y superficie de ataque; activa solo drivers de tu hardware.
- **Locales y zona horaria**: genera únicamente las locales que uses para ahorrar espacio.
- **Red**: decide si usar `dhcpcd/NetworkManager` (BLFS) o scripts simples.

### 7) Buenas prácticas y “trucos de supervivencia”

- **Sigue el libro al pie de la letra**: pequeños desvíos rompen el toolchain. Marca cada sección como _hecha_.
- **Aísla el trabajo**: usa una **VM** o un disco dedicado.
- **Guarda logs**: `make -j$(nproc) 2>&1 | tee build.log` te salva al depurar.
- **Verifica hashes** y parches; limpia el árbol (`make clean`) si fallan compilaciones.
- **Snapshots** (si estás en VM): toma uno **antes** del chroot y otro **antes** del kernel.
- **Tiempo**: los paquetes grandes (GCC/GLIBC/KERNEL) tardan; compila con todos los núcleos (`-j`).
- **Documenta tus opciones** (config de kernel, versiones) para replicar el sistema luego.

### 8) Errores comunes (y cómo evitarlos)

1. **Olvidar exportar `$LFS`** o montar mal `/mnt/lfs` → revisa `echo $LFS` y `mount`.
2. **PATH incorrecto** (mezclando herramientas del host y `/tools`) → respeta el PATH indicado por el libro y verifica con `which gcc`.
3. **No limpiar fuentes entre paquetes** → usa árboles “limpios” por paquete (build dir separado).
4. **Config del kernel excesiva** → empieza minimal; agrega módulos si algo falta.
5. **GRUB sin el disco correcto** → confirma `/dev/sdX` y UUIDs de `fstab`.

### 9) Mini plan de trabajo (3 sesiones)

**Sesión 1 (2–4 h):** preparar discos, `$LFS`, usuario `lfs`, descargar fuentes, empezar **binutils pass 1** y **gcc pass 1**.
**Sesión 2 (3–6 h):** **linux-headers**, **glibc**, **binutils pass 2**, **gcc pass 2** y utilidades temporales en **/tools**.
**Sesión 3 (4–8 h):** **montajes + chroot**, construir **base LFS**, configurar **fstab/usuarios/red**, compilar **kernel**, instalar **GRUB**, reiniciar.

(Con hardware potente puedes hacerlo en menos tiempo; en equipos modestos tardará más.)

### 10) ¿Para quién es LFS?

- **Aprendices/estudiantes** que quieren entender Linux _de raíz_.
- **Admins/DevOps** que valoran sistemas mínimos, reproducibles y seguros.
- **Entornos embebidos/laboratorios** que requieren control total de librerías y binarios.

---

[🔼](#índice)

---

## **40. Hoja de trucos de comandos de Linux**

### 📋 Resumen rápido (comandos que recordar)

```
ls     cd    cp    mv    rm    mkdir    chmod    chown
cat    less  tail  head  grep     find    xargs    awk
ps aux top  systemctl journalctl   ip   ss   netstat
df -h  lsblk mount umount   fdisk/parted   rsync   tar
ssh scp rsync curl wget   apt/dnf/pacman   docker   kubectl
```

### 1) Navegar y gestionar archivos/carpetas

```bash
ls -la              # listar todo, long format (perms, owner, size)
cd /ruta/a/carpeta  # cambiar directorio
pwd                 # mostrar directorio actual
mkdir -p foo/bar    # crear directorio padre(s)
cp -r src/ dest/    # copiar directorio recursivo
mv archivo nuevo/   # mover/renombrar
rm archivo          # borrar archivo
rm -rf carpeta      # borrar carpeta recursiva (¡peligro!)
tree -L 2           # ver árbol (instalar tree si no está)
```

Ejemplo: crear estructura y mover archivos

```bash
mkdir -p ~/proyectos/demo/{src,logs}
mv archivo.txt ~/proyectos/demo/src/
```

### 2) Ver contenido de archivos

```bash
cat archivo.txt              # imprime todo
less archivo.txt             # ver paginado (flechas, q para salir)
head -n 20 archivo.log       # primeras 20 líneas
tail -n 50 archivo.log       # últimas 50 líneas
tail -f /var/log/syslog      # seguimiento en tiempo real
nl archivo.txt               # muestra líneas numeradas
```

### 3) Búsqueda y localización (find, locate, grep)

```bash
# Buscar archivos en el FS:
find /ruta -name '*.log' -type f

# Buscar por contenido (recursivo, case insensitive):
grep -Rni "error" /var/log

# Buscar y ejecutar comando por cada fichero:
find . -type f -name '*.tmp' -exec rm -i {} \;

# Uso seguro con xargs (espacios/linebreaks):
find . -type f -name '*.log' -print0 | xargs -0 gzip
```

Ejemplo: buscar errores de apache:

```bash
grep -Rni "segfault\|error" /var/log/apache2
```

### 4) Procesamiento de texto: cut, awk, sed, sort, uniq

```bash
cut -d: -f1 /etc/passwd          # columna 1 separada por ':'
awk -F: '{print $1, $3}' /etc/passwd  # awk: imprimir user y uid
sed -n '1,50p' archivo.txt       # mostrar líneas 1 a 50
sed -i 's/viejo/nuevo/g' file    # reemplazo en sitio (–i)
sort archivo | uniq -c | sort -nr  # contar ocurrencias y ordenar
```

Ejemplo: extraer usuarios activos:

```bash
awk -F: '$7 ~ /\/bin\/bash/ {print $1}' /etc/passwd
```

### 5) Compresión y empaquetado

```bash
tar -cvzf backup.tar.gz /ruta         # crear .tar.gz
tar -xvzf backup.tar.gz -C /dest      # extraer
zip -r archivo.zip carpeta/           # crear zip
unzip archivo.zip -d destino/         # extraer zip
gzip archivo.log                       # comprime archivo -> .gz
```

Ejemplo: crear backup de /etc:

```bash
sudo tar -cvzf /tmp/etc_backup_$(date +%F).tar.gz /etc
```

### 6) Permisos y propietarios

```bash
ls -l /ruta
chmod 755 archivo            # rwxr-xr-x (owner rwx, group r-x, others r-x)
chmod u+x script.sh         # añadir permiso ejecución al owner
chmod 644 archivo.txt       # rw-r--r--
chown usuario:grupo archivo # cambiar dueño y grupo
chown -R usuario:grupo dir/ # recursivo
# Sticky bit / setuid / setgid:
chmod 4755 programa        # setuid -> ejecuta con owner (cuidado!)
chmod 2775 dir/            # setgid en directorio -> archivos heredan grupo
chmod +t dir/              # sticky bit en /tmp-like (evita borrar otros files)
```

Explicación numérica: 7=rwx, 6=rw-, 5=r-x, 4=r--.

### 7) Gestión de procesos y recursos

```bash
ps aux | grep nombre       # ver procesos
top                       # monitor en tiempo real
htop                      # top mejorado (instalar htop)
ps aux --sort=-%mem | head -n 10   # top 10 por memoria
kill PID                  # enviar SIGTERM
kill -9 PID               # SIGKILL (forzar)
pkill nombre_proceso      # matar por nombre
killall nombre_proceso    # matar todos con ese nombre
nice -n 10 comando        # lanzar con menor prioridad
renice -n 5 -p 1234       # cambiar prioridad proceso en ejecución
```

Ejemplo: encontrar procesos que abren sockets:

```bash
ss -tulpn | grep LISTEN
```

### 8) Networking (IP, conexiones, diagnóstico)

```bash
ip addr show              # ver interfaces y IPs
ip link set eth0 up/down  # activar/desactivar interfaz
ip route show             # tabla de rutas
ping 8.8.8.8              # probar conectividad
traceroute google.com     # ruta hasta host
ss -tulpen                # sockets escuchando (tcp/udp)
netstat -tulpen           # (legacy) lista puertos
curl -I https://example.com   # ver headers HTTP
curl -fsSL https://get.thing | sh  # descargar y ejecutar (peligro!)
wget url                  # descargar
nmcli device status       # NetworkManager CLI
```

Ejemplo: ver procesos con puertos abiertos:

```bash
ss -tulpen | grep 443
```

### 9) Administrar servicios (systemd)

```bash
systemctl status nginx.service
systemctl start nginx.service
systemctl stop nginx.service
systemctl restart nginx.service
systemctl enable nginx.service   # arranque automático
systemctl disable name.service
systemctl daemon-reload          # después de añadir unit
systemctl list-units --type=service
journalctl -u nginx.service -f  # ver logs de la unidad en tiempo real
journalctl -b -p err             # ver errores desde el último boot
```

Ejemplo: ver logs del kernel:

```bash
journalctl -k --since "2 hours ago"
```

### 10) Logs y diagnóstico

```bash
# Logs en syslog / var/log:
sudo tail -f /var/log/syslog
sudo tail -n 200 /var/log/auth.log   # intentos de login
# Con Syslog + journalctl (systemd):
journalctl -xe                      # eventos recientes/errores
journalctl --since "2025-08-01" --until "2025-08-02"
```

### 11) Gestión de paquetes (distros populares)

**Debian/Ubuntu (apt):**

```bash
sudo apt update
sudo apt upgrade
sudo apt install paquete
sudo apt remove paquete
sudo apt purge paquete
apt-cache search palabra
```

**Fedora/RHEL (dnf/yum):**

```bash
sudo dnf install paquete
sudo dnf update
sudo dnf list installed
```

**Arch (pacman):**

```bash
sudo pacman -Syu
sudo pacman -S paquete
pacman -Qs palabra
```

Ejemplo (instalar nginx en Debian):

```bash
sudo apt update && sudo apt install -y nginx
```

### 12) Discos, particiones y volúmenes

```bash
lsblk                # ver dispositivos y particiones
blkid                # mostrar UUIDs
fdisk /dev/sdb       # particionar (mbr) - interactivo
parted /dev/sdb      # particionado scriptable / gpt
mkfs.ext4 /dev/sdb1  # formatear
mount /dev/sdb1 /mnt # montar
umount /mnt
df -h                # uso espacio por filesystem
du -sh carpeta/      # tamaño carpeta (resumen)
resize2fs /dev/sdb1  # redimensionar fs ext4 (tras resize de partición)
cryptsetup luksFormat /dev/sdb1  # cifrado LUKS
lsblk -f             # ver FS y labels
```

Ejemplo: clonar disco con dd (peligro, puede sobrescribir)

```bash
sudo dd if=/dev/sda of=/dev/sdb bs=64K conv=noerror,sync status=progress
```

### 13) Backup/Rsync

```bash
# Rsync local -> remoto (preservar permisos, comprimir, mostrar progreso)
rsync -avz --delete /ruta/origen/ user@host:/ruta/destino/
# Backup incremental:
rsync -av --link-dest=/backups/day-1 /src /backups/day-2
```

Ejemplo: guardar /home a servidor remoto:

```bash
rsync -avz --progress /home/backup/ backup@backup.example.com:/backups/home/
```

### 14) SSH, SCP y tunelización

```bash
ssh user@host
ssh -i ~/.ssh/id_rsa user@host
scp archivo user@host:/ruta/destino
ssh -L 8080:localhost:80 user@remote   # tunel local
ssh -R 2222:localhost:22 user@remote   # tunel reverse
# Copiar claves públicas:
ssh-copy-id user@host
```

Ejemplo: acceso sin contraseña:

```bash
ssh-keygen -t ed25519
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host
```

### 15) Usuarios y grupos

```bash
sudo useradd -m -s /bin/bash juan      # crear usuario y home
sudo passwd juan                       # asignar contraseña
sudo usermod -aG sudo juan             # añadir a grupo sudo
getent passwd usuario                  # info usuario
id juan                                # uid/gids del usuario
sudo deluser juan                      # eliminar usuario
```

Sudoers: editar con `visudo` para evitar errores de sintaxis.

### 16) Cron y systemd timers (programar tareas)

**Crontab (usuario):**

```bash
crontab -e
# Ejemplo: backup diario a 2:00
0 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

**systemd timer example (más moderno):**

- `/etc/systemd/system/mi-job.service`

```ini
[Unit]
Description=Mi job diario

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

- `/etc/systemd/system/mi-job.timer`

```ini
[Unit]
Description=Timer diario

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now mi-job.timer
```

### 17) Bash scripting esencial (plantilla y ejemplos)

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

log() { echo "[$(date +'%F %T')] $*"; }

# variables
SRC="/home/tuusuario/proyectos"
DEST="/backup/proyectos"

# función
backup() {
  tar -czf "$DEST/proyectos_$(date +%F).tar.gz" -C "$SRC" .
}

trap 'log "Interrumpido"; exit 1' INT TERM

log "Iniciando backup"
backup
log "Backup finalizado"
```

Consejos: `set -euo pipefail` ayuda a robustez; siempre validar entradas y permisos.

### 18) One-liners útiles (prácticos)

```bash
# Encontrar archivos >100MB
find / -type f -size +100M -exec ls -lh {} \; | awk '{print $5,$9}'

# Contar archivos por extensión
find . -type f -name "*.*" | sed 's|.*\.||' | sort | uniq -c | sort -nr

# Servir carpeta actual via HTTP (práctico para compartir)
python3 -m http.server 8000

# Remover archivos vacíos
find . -type f -empty -delete

# Ver top I/O consumers
iotop -o -b -n 10

# Mostrar uso de disco por carpeta ordenado
du -sh * 2>/dev/null | sort -hr | head -n 30
```

### 19) Seguridad básica / hardening rápido

- Mantén `sudo` en lugar de login root directo; usa `visudo`.
- Habilita UFW (fácil) y limita acceso RDP/SSH:

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw enable
```

- Deshabilita SSH password auth, usa keys:

```bash
# en /etc/ssh/sshd_config
PasswordAuthentication no
PermitRootLogin no
```

- Actualiza regularmente (`apt upgrade` / `dnf upgrade`) y revisa CVEs si eres administrador.

### 20) Comandos para recuperar/diagnosticar (primer auxilio)

```bash
# Ver espacio si disco lleno
df -h
du -sh /var/log/* | sort -hr | head

# Reiniciar servicio
sudo systemctl restart networking

# Montar manualmente un disco externo
sudo mount /dev/sdb1 /mnt

# Reiniciar en modo recovery/maintenance (systemd)
sudo systemctl rescue
```

### 21) Consejos, trucos y buenas prácticas

- `man comando` o `comando --help` antes de usar; `apropos keyword`.
- Usa `tee` para guardar y ver salida: `comando | tee logfile.txt`.
- Haz backups antes de operaciones destructivas y usa snapshots si es posible.
- Añade alias útiles en `~/.bashrc`:

```bash
alias ll='ls -alF'
alias gs='git status'
```

- Versiona tus configuraciones (dotfiles) con Git.

### 22) Hoja rápida imprimible (mini-resumen)

- **Ver disco:** `df -h`, `lsblk`, `du -sh *`
- **Red:** `ip addr`, `ss -tulpen`, `curl -I`
- **Procesos:** `ps aux | grep`, `top`, `kill`
- **Systemd/Logs:** `systemctl status`, `journalctl -u name -f`
- **Buscar:** `find`, `grep -Rni`
- **Texto:** `awk`, `sed`, `cut`, `sort`, `uniq -c`
- **Empaquetar:** `tar -cvzf`, `rsync -avz`
- **SSH:** `ssh`, `scp`, `ssh-keygen`, `ssh-copy-id`

---

[🔼](#índice)

---

## **41. Linux en 100 comandos**

1. **`ls`** — listar archivos y directorios.

```bash
ls -la /etc
```

2. **`cd`** — cambiar directorio.

```bash
cd ~/proyectos && pwd
```

3. **`pwd`** — mostrar directorio actual.

```bash
pwd
```

4. **`tree`** — mostrar árbol de directorios (si está instalado).

```bash
tree -L 2 ~/proyectos
```

5. **`stat`** — ver metadatos de un archivo.

```bash
stat /etc/passwd
```

6. **`file`** — detectar tipo de archivo.

```bash
file /bin/bash
```

7. **`cp`** — copiar archivos/directorios.

```bash
cp -r ~/proyecto /backup/proyecto
```

8. **`mv`** — mover o renombrar.

```bash
mv informe_v1.docx informe_final.docx
```

9. **`rm`** — borrar archivos o carpetas. (¡peligro!)

```bash
rm -i archivo_antiguo.txt
```

10. **`mkdir`** — crear directorios.

```bash
mkdir -p ~/proyectos/clienteX/{src,docs,logs}
```

11. **`rmdir`** — eliminar directorios vacíos.

```bash
rmdir carpeta_vacia
```

12. **`touch`** — crear archivo vacío o actualizar timestamp.

```bash
touch nueva_nota.txt
```

13. **`ln`** — crear enlaces (hard o soft con `-s`).

```bash
ln -s /var/www/proyecto ~/proyecto_link
```

14. **`chown`** — cambiar propietario/grupo.

```bash
sudo chown -R guss:devs /srv/app
```

15. **`chmod`** — cambiar permisos.

```bash
chmod 750 /usr/local/bin/mi_script.sh
```

16. **`chattr`** — atributos extendidos (ej. inmovilizar archivo).

```bash
sudo chattr +i /etc/archivo_importante
```

17. **`cat`** — concatenar y mostrar archivos.

```bash
cat ~/.bashrc
```

18. **`less`** — paginar archivos grandes.

```bash
less /var/log/syslog
```

19. **`more`** — paginar (más básico).

```bash
more /var/log/messages
```

20. **`head`** — primeras líneas de un archivo.

```bash
head -n 20 /var/log/auth.log
```

21. **`tail`** — últimas líneas; útil con `-f` para seguir en tiempo real.

```bash
tail -n 100 -f /var/log/nginx/access.log
```

22. **`nl`** — numerar líneas.

```bash
nl README.md | sed -n '1,50p'
```

23. **`watch`** — ejecutar un comando periódicamente.

```bash
watch -n 2 "date; uptime"
```

24. **`grep`** — buscar texto dentro de archivos.

```bash
grep -Rni "error" /var/log
```

25. **`find`** — buscar archivos por nombre/fecha/tamaño/permiso.

```bash
find /var/www -type f -name "*.log" -mtime -7
```

26. **`locate`** — búsqueda rápida indexada (db: `updatedb`).

```bash
locate nginx.conf
```

27. **`which`** — localizar ejecutables en PATH.

```bash
which python3
```

28. **`whereis`** — localizar binarios/sources/man.

```bash
whereis ssh
```

29. **`awk`** — procesamiento de texto y columnas.

```bash
awk -F: '{print $1, $3}' /etc/passwd | head
```

30. **`sed`** — stream editor (reemplazos y transformaciones).

```bash
sed -n '1,50p' archivo.txt
sed -i 's/VIEJO/NUEVO/g' archivo.txt
```

31. **`cut`** — extraer columnas.

```bash
cut -d: -f1 /etc/passwd
```

32. **`tr`** — transformar caracteres.

```bash
echo "hola|mundo" | tr '|' ' '
```

33. **`sort`** — ordenar líneas.

```bash
sort nombres.txt | head
```

34. **`uniq`** — quitar duplicados (combinado con `sort`).

```bash
sort ips.txt | uniq -c | sort -nr | head
```

35. **`wc`** — contar líneas/palabras/caracteres.

```bash
wc -l /var/log/syslog
```

36. **`paste`** — pegar columnas lado a lado.

```bash
paste file1.txt file2.txt
```

37. **`join`** — unir archivos por clave (columnas).

```bash
join -t: users.txt shells.txt
```

38. **`xargs`** — construir y ejecutar líneas de comando desde stdin.

```bash
find . -name "*.tmp" -print0 | xargs -0 rm -v
```

39. **`tar`** — empaquetar/extraer tarballs.

```bash
tar -cvzf backup_$(date +%F).tar.gz /etc
tar -xvzf backup_2025-08-29.tar.gz -C /tmp/restore
```

40. **`gzip`** — comprimir (crea `.gz`).

```bash
gzip archivo.log
```

41. **`gunzip`** — descomprimir `.gz`.

```bash
gunzip archivo.log.gz
```

42. **`bzip2`** — comprimir con bzip2.

```bash
bzip2 archivo.txt
```

43. **`xz`** — compresión xz (`.xz`).

```bash
xz -z archivo.tar
```

44. **`unzip`** — extraer ZIP.

```bash
unzip paquete.zip -d /tmp/paquete
```

45. **`zip`** — crear archivo ZIP.

```bash
zip -r proyecto.zip proyecto/
```

46. **`uname`** — información del kernel/sistema.

```bash
uname -a
```

47. **`hostname`** — ver o cambiar nombre del host.

```bash
hostnamectl set-hostname servidor1
hostname
```

48. **`uptime`** — tiempo de actividad y carga.

```bash
uptime
```

49. **`top`** — monitor de procesos en tiempo real.

```bash
top
```

50. **`htop`** — top mejorado (si está instalado).

```bash
htop
```

51. **`ps`** — listar procesos.

```bash
ps aux | grep nginx
```

52. **`vmstat`** — estadísticas de memoria/CPU/io.

```bash
vmstat 2 5
```

53. **`free`** — memoria RAM/Swap libre/ocupada.

```bash
free -h
```

54. **`lscpu`** — información CPU.

```bash
lscpu
```

55. **`lsblk`** — listar bloques/discos y particiones.

```bash
lsblk -f
```

56. **`df`** — uso del espacio en filesystems.

```bash
df -hT /
```

57. **`du`** — uso de espacio por carpeta.

```bash
du -sh /var/log/* | sort -hr | head
```

58. **`fdisk`** — particionado (MBR interactivo).

```bash
sudo fdisk -l /dev/sdb
```

59. **`blkid`** — mostrar UUIDs y tipos de FS.

```bash
sudo blkid /dev/sda1
```

60. **`lsmod`** — módulos cargados del kernel.

```bash
lsmod | grep vfat
```

61. **`modprobe`** — cargar/descargar módulos.

```bash
sudo modprobe vfat
sudo modprobe -r vfat
```

62. **`ip`** — comando moderno para interfaces/rutas.

```bash
ip addr show
ip route add 10.0.0.0/24 via 192.168.1.1
```

63. **`ifconfig`** — (legacy) mostrar interfaces; aún útil en algunas distros.

```bash
/sbin/ifconfig -a
```

64. **`ss`** — ver sockets/puertos abiertos (reemplaza `netstat`).

```bash
ss -tulpen
```

65. **`netstat`** — (legacy) estadísticas de red.

```bash
netstat -tulpn | grep LISTEN
```

66. **`ping`** — comprobar conectividad ICMP.

```bash
ping -c 4 8.8.8.8
```

67. **`traceroute`** — trazar ruta a host.

```bash
traceroute google.com
```

68. **`curl`** — transferencia HTTP/FTP, testing APIs.

```bash
curl -I https://example.com
curl -fsSL https://get.docker.com | sh
```

69. **`wget`** — descargar archivos desde la web.

```bash
wget https://example.com/archivo.iso
```

70. **`scp`** — copiar archivos vía SSH.

```bash
scp archivo user@server:/home/user/
```

71. **`sftp`** — FTP sobre SSH (interactivo).

```bash
sftp user@server
```

72. **`rsync`** — sincronización eficiente (local/remote).

```bash
rsync -avz --progress /home/user/ backup@nas:/backups/user/
```

73. **`ssh`** — acceso remoto seguro.

```bash
ssh -i ~/.ssh/id_ed25519 user@server.example.com
```

74. **`nc` (netcat)** — conexiones TCP/UDP, debugging de puertos.

```bash
nc -l 8080           # servidor simple
nc -vz host 22       # scan puerto 22
```

75. **`dig`** — consultas DNS.

```bash
dig +short example.com
```

76. **`nmcli`** — NetworkManager CLI (gestión de conexiones).

```bash
nmcli device status
nmcli con up id "WIFI-OFICINA"
```

77. **`mount`** — montar dispositivos/FS.

```bash
sudo mount /dev/sdb1 /mnt/usb
```

78. **`umount`** — desmontar.

```bash
sudo umount /mnt/usb
```

79. **`apt`** — gestor de paquetes Debian/Ubuntu (básico).

```bash
sudo apt update && sudo apt install -y nginx
```

80. **`dnf`** — gestor de paquetes Fedora/RHEL modernos.

```bash
sudo dnf install -y @development-tools
```

81. **`yum`** — gestor RHEL/CentOS (legacy en algunas versiones).

```bash
sudo yum update
```

82. **`pacman`** — gestor de Arch Linux.

```bash
sudo pacman -Syu
```

83. **`snap`** — instalar paquetes snap (Canonical).

```bash
sudo snap install code --classic
```

84. **`flatpak`** — gestionar apps Flatpak.

```bash
flatpak install flathub org.gimp.GIMP
```

85. **`kill`** — enviar señal a proceso (default SIGTERM).

```bash
kill 12345
```

86. **`pkill`** — matar procesos por nombre/pattern.

```bash
pkill -f "python3 myapp.py"
```

87. **`killall`** — matar todos procesos con nombre.

```bash
killall nginx
```

88. **`nice`** — lanzar proceso con prioridad ajustada.

```bash
nice -n 10 tar -czf bigbackup.tar.gz /var/www
```

89. **`renice`** — cambiar prioridad de proceso en ejecución.

```bash
sudo renice -n 5 -p 54321
```

90. **`bg`** — devolver job al background.

```bash
./longtask.sh &    # lanzar background
jobs
bg %1
```

91. **`fg`** — traer job al foreground.

```bash
fg %1
```

92. **`jobs`** — listado de jobs de la shell.

```bash
jobs -l
```

93. **`systemctl`** — gestionar servicios/systemd.

```bash
sudo systemctl restart nginx.service
sudo systemctl enable --now sshd
```

94. **`service`** — administrar servicios en sistemas SysV/init (compatibilidad).

```bash
sudo service apache2 restart
```

95. **`journalctl`** — ver logs de systemd/journald.

```bash
sudo journalctl -u nginx.service --since "2 hours ago" -f
```

96. **`useradd`** — crear usuario.

```bash
sudo useradd -m -s /bin/bash juan
sudo passwd juan
```

97. **`usermod`** — modificar usuario (grupos, shell).

```bash
sudo usermod -aG sudo juan
```

98. **`passwd`** — cambiar contraseña de usuario.

```bash
sudo passwd juan
passwd             # cambiar tu propia contraseña
```

99. **`su`** — cambiar a otro usuario (root por defecto).

```bash
su -     # pide contraseña de root
su - juan
```

100. **`sudo`** — ejecutar comando con privilegios de otro usuario (por defecto root).

```bash
sudo apt update && sudo apt upgrade
sudo -u postgres psql -c '\l'
```

### Consejos rápidos y buenas prácticas

- Usa `man comando` o `comando --help` para aprender opciones: `man rsync`.
- Evita `rm -rf` sin comprobar la ruta; usa `-i` para interacción.
- Prefiere `ss` sobre `netstat`, `ip` sobre `ifconfig` en sistemas modernos.
- Automatiza con `cron` o `systemd timers` y versiona tus scripts con `git`.
- Prueba comandos potencialmente destructivos en un entorno aislado (VM/containers).

---

[🔼](#índice)

---

## **42. Introducción a Linux**

### 1) ¿Qué es Linux? (en pocas palabras)

**Linux** es el **núcleo (kernel)** —el software que gestiona CPU, memoria, dispositivos y procesos— creado por Linus Torvalds en 1991. Hoy en día hablamos de “Linux” para referirnos al sistema operativo completo formado por el kernel + utilidades (muchas provistas por GNU) + servicios + entorno gráfico: eso es una **distribución (distro)** (p. ej. Ubuntu, Debian, Fedora).

**Analogía simple:** el kernel es el motor de un coche; la distro es el coche completo (motor + carrocería + asientos + tablero).

### 2) Kernel vs GNU vs Distro

- **Kernel (Linux):** manejo bajo nivel del hardware.
- **GNU:** suite de utilidades (bash, coreutils, gcc, etc.) que hacen usable el sistema.
- **Distribución:** paquete integrado (kernel + GNU + instalador + repositorios + gestor de paquetes + escritorio). Ejemplos: **Ubuntu** (fácil para principiantes), **Debian** (estabilidad), **Fedora** (tecnologías nuevas), **Arch** (enfoque minimalista), **CentOS/Alma/Rocky** (servidor empresarial).

### 3) Estructura básica del sistema de archivos (FHS) — carpetas importantes

- `/` — raíz del sistema.
- `/bin` — comandos esenciales (ej.: `ls`, `cp`).
- `/sbin` — comandos del sistema (ej.: `fdisk`, `ifconfig`).
- `/usr` — binarios y librerías de usuario; `/usr/bin`, `/usr/lib`.
- `/etc` — configuración del sistema (archivos estáticos).
- `/var` — datos variables (logs, colas, bases). Ej.: `/var/log`.
- `/home` — directorios de usuarios (`/home/tuusuario`).
- `/root` — home del root (administrador).
- `/dev` — archivos de dispositivos (discos, tty).
- `/proc` y `/sys` — pseudo-filesystems con información del kernel y procesos.

Ejemplo: `cat /etc/os-release` te dirá la distro/version.

### 4) Primeros comandos esenciales (con ejemplos)

Abrir terminal y probar:

```bash
# Información básica
uname -a                     # kernel y arquitectura
cat /etc/os-release          # distribución y versión

# Navegar y listar
pwd                          # mostrar ruta actual
ls -la /etc                  # listar con permisos
cd /home/tuusuario           # cambiar directorio
mkdir -p proyectos/demo      # crear estructura

# Ver contenidos
cat /etc/hosts
less /var/log/syslog         # páginas, q para salir
tail -n 50 /var/log/syslog   # últimas 50 líneas

# Buscar
grep -Rni "error" /var/log
find / -name "*.conf" 2>/dev/null
```

### 5) Gestión de usuarios y permisos (concepto + ejemplos)

Linux es multiusuario. Tres tipos de permiso: lectura (r), escritura (w), ejecución (x) — aplicados a owner, group, others.

```bash
# Crear usuario y asignar grupo
sudo useradd -m -s /bin/bash maria
sudo passwd maria
sudo usermod -aG sudo maria   # añadir a grupo sudo (Debian/Ubuntu)

# Ver permisos
ls -l archivo.txt    # e.g. -rwxr-x--- 1 guss devs 1234 fecha archivo.txt

# Cambiar propietario y permisos
sudo chown guss:devs archivo.txt
chmod 750 archivo.txt   # owner=rwx, group=r-x, others=---
```

Numérico: `7`=rwx, `5`=r-x, `4`=r--.

### 6) Gestor de paquetes (instalar software)

Cada familia de distros tiene su gestor:

**Debian/Ubuntu (apt):**

```bash
sudo apt update
sudo apt install -y nginx
sudo apt remove nginx
```

**Fedora (dnf):**

```bash
sudo dnf install nginx
```

**Arch (pacman):**

```bash
sudo pacman -Syu
sudo pacman -S nginx
```

Ejemplo: instalar `htop` y correrlo:

```bash
sudo apt install -y htop
htop
```

### 7) Gestión de servicios (systemd)

La mayoría de distros modernas usan **systemd**:

```bash
# Controlar servicio
sudo systemctl start nginx
sudo systemctl enable nginx    # arrancar al boot
sudo systemctl status nginx
sudo systemctl stop nginx
journalctl -u nginx -f         # ver logs en tiempo real
```

### 8) Procesos y recursos

```bash
ps aux | grep myapp
top                # monitor interactivo
kill PID           # terminar proceso
nice -n 10 comando  # bajar prioridad al ejecutar
free -h            # memoria usada/libre
df -h              # espacio en disco
```

### 9) Redes básicas

```bash
ip addr show            # ver interfaces y IPs
ip route show           # tabla de rutas
ss -tulpen              # sockets escuchando
ping 8.8.8.8
curl -I https://example.com
sudo ufw allow 22/tcp   # firewall simple (ufw)
sudo ufw enable
```

### 10) Archivos, compresión y backups

```bash
tar -cvzf backup.tar.gz /etc
tar -xvzf backup.tar.gz -C /tmp
rsync -avz /home/ user@backup:/backups/
```

Ejemplo de script simple de backup:

```bash
#!/usr/bin/env bash
SRC="/home/tuusuario"
DEST="/backups/$(date +%F)"
mkdir -p "$DEST"
rsync -av --delete "$SRC" "$DEST"
```

### 11) Scripting básico en Bash (ejemplo)

Plantilla robusta:

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

log(){ echo "[$(date +'%F %T')] $*"; }

SRC="/home/tuusuario/proyecto"
DEST="/tmp/backup"

log "Iniciando backup"
tar -czf "${DEST}/proyecto_$(date +%F).tar.gz" -C "$SRC" .
log "Backup completado"
```

`set -euo pipefail` ayuda a evitar errores silenciosos.

### 12) Boot: cómo arranca Linux (resumen)

1. **Firmware** (BIOS/UEFI) inicializa hardware.
2. **Bootloader** (GRUB) carga kernel e initramfs.
3. **Kernel** se inicializa y monta root.
4. **Init** (systemd) arranca servicios y targets (multi-user.target, graphical.target).

### 13) Seguridad básica

- Usa **sudo** en lugar de root directo.
- Habilita **UFW** o configura `iptables`/`nftables`.
- Mantén sistema actualizado: `sudo apt upgrade`.
- Usa **SSH keys** en vez de contraseñas: `ssh-keygen` y `ssh-copy-id`.
- Activa cifrado de disco (LUKS) si manejas datos sensibles.

### 14) Entornos de desarrollo y contenedores

- **WSL2** en Windows permite ejecutar Linux en Windows.
- **Docker** para contenerizar apps:

```bash
# Dockerfile simple
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python","app.py"]
```

- **Kubernetes** para orquestación a escala.

### 15) Troubleshooting rápido (comandos útiles)

- `journalctl -xe` — eventos recientes y errores.
- `dmesg | tail` — mensajes del kernel.
- `systemctl status <servicio>` — estado y logs.
- `ss -tulpn` — puertos en escucha.
- `tail -f /var/log/syslog` — seguir logs.

### 16) Ruta práctica de aprendizaje (30/90/180 días)

- **30 días (fundamentos):** terminal, filesystem, permisos, apt/pacman, servicios básicos, scripting pequeño.
- **90 días (administrador):** systemd, redes, firewall, backups, usuarios, automatización con cron/Ansible, Docker básico.
- **180 días (avanzado):** Kubernetes, security hardening, kernel basics, performance tuning, monitoreo (Prometheus/Grafana).

### 17) Proyectos prácticos (para aprender)

1. Monta una VM e instala Ubuntu Server; configura SSH + firewall.
2. Despliega un sitio estático con Nginx y crea backup automático con rsync.
3. Automatiza instalación/configuración con un playbook Ansible.
4. Crea una imagen Docker de tu app y publícala a Docker Hub.
5. Monitoriza logs con Prometheus + Grafana (básico).

### 18) Errores comunes y cómo evitarlos

- Ejecutar `rm -rf` sin verificar — siempre revisa la ruta.
- Editar configs sin backup — copia `/etc/archivo.conf` antes (`cp file file.bak`).
- Ignorar logs — `journalctl` y `/var/log` son tus mejores amigos.
- No usar entornos de prueba: prueba en VM/containers antes de producción.

### 19) Hoja de trucos rápida (cheat-sheet)

- Navegación: `ls`, `cd`, `pwd`, `tree`
- Texto: `cat`, `less`, `head`, `tail`, `grep`, `awk`, `sed`
- Sistema: `uname -a`, `top/htop`, `ps aux`, `free -h`, `df -h`, `lsblk`
- Red: `ip addr`, `ss -tulpen`, `ping`, `curl`
- Servicios: `systemctl start/enable/status`, `journalctl -u <servicio> -f`
- Paquetes: `sudo apt update && sudo apt install -y <pkg>`
- Seguridad: `sudo ufw enable`, `ssh-keygen`, `ssh-copy-id`

### 20) Recursos recomendados para seguir aprendiendo

- **Libros:** _The Linux Command Line_ (William Shotts), _How Linux Works_.
- **Cursos:** Linux Foundation (LFCS), cursos en plataformas como Coursera/Pluralsight/EDX.
- **Práctica:** crea VMs con VirtualBox, usa WSL2 o Raspberry Pi.
- **Foros & docs:** manpages (`man comando`), Stack Overflow, documentación oficial de la distro.

---

[🔼](#índice)

---

## **43. Explora las publicaciones más importantes sobre Linux**

### Resumen rápido (qué leer según objetivo)

- **Desarrollo del kernel / seguridad crítica:** `kernel.org`, listas de correo del kernel (LKML / linux-cve-announce) y **LWN** para análisis profundo.
- **Benchmarks / hardware / rendimiento:** **Phoronix**.
- **Noticias del ecosistema / proyectos y coordinación:** **Linux Foundation / Linux Foundation Blog**.
- **Lanzamientos y comparativa de distros:** **DistroWatch** + blogs oficiales de cada distro (Ubuntu, Fedora, Red Hat, SUSE…).
- **Tutoriales y how-tos:** **HowtoForge**, _It’s FOSS_, **OMG!Ubuntu/OMG!Linux**.
- **Cobertura general y periodismo técnico:** _Ars Technica_, _Tom’s Hardware_, _The Register_, _The Hacker News_ para seguridad/pruebas.
- **Comunidad / debates / señales tempranas:** Reddit (`r/linux`), Hacker News, listas de correo y foros de cada proyecto.

A continuación lo explico por categorías con ejemplos concretos y pasos para seguirlos.

### 1) Fuentes “canónicas” (qué son y por qué leerlas)

#### Kernel.org — **la fuente oficial del kernel**

Qué ofrece: anuncios de versiones (mainline/stable/LTS), tarballs, changelogs y archivos del kernel. Si quieres saber _qué versión_ salió hoy o cuándo parchearon un bug crítico: aquí es donde se anuncia.

**Ejemplo:** la página de releases muestra la versión _stable_ y los tarballs; ideal para scripts de monitorización o para verificar que tu distro ya empaquetó el patch.

#### LWN.net — análisis profundo y contexto técnico

Qué ofrece: piezas largas, seguimientos de debates del LKML, entrevistas, explicaciones técnicas y contexto (no sólo “qué”, sino “por qué importa”). LWN es de pago en parte, pero su cobertura técnica es insustituible.

**Ejemplo:** cuando cambia la gestión de CVE del kernel o se hace un cambio importante en la política del proyecto, LWN suele publicar el análisis con implicaciones para administradores y mantenedores.

#### Mailing lists del kernel / lore.kernel.org / LKML

Qué ofrece: el flujo directo de desarrollo — parches, discusiones y anuncios tempranos. No es “lectura fácil” pero es la **fuente primaria** (si quieres el primer aviso de un parche o CVE). Usa lore.kernel.org o lkml.org para leer archivos.

**Ejemplo:** la lista `linux-cve-announce` publica asignaciones CVE y parches upstream; suscribirse es esencial si gestionas kernels.

#### Phoronix — benchmarks y cobertura hardware/Linux

Qué ofrece: pruebas de rendimiento, comparativas de kernels, soporte hardware y noticias sobre controladores/firmware; muy útil para comparar drivers o rendimiento entre kernels/distros.

**Ejemplo:** artículos tipo “qué cambia en X driver del kernel y cómo impacta en el rendimiento” o benchmarks antes/después de una actualización.

#### Linux Foundation / Linux Foundation Blog / Linux.com

Qué ofrece: anuncios de proyectos, conferencias (Open Source Summit), iniciativas (LF training, proyectos como CNCF-related), y artículos oficiales de coordinación. Útil para roadmap y noticias de gobernanza.

### 2) Distro-centric y comerciales (notas de lanzamiento y guías)

Para entender cambios aplicables a los usuarios/empresas debes leer los blogs oficiales de las distribuciones o de los vendors:

- **Ubuntu / Canonical** — notas de lanzamiento, imágenes cloud, guías de migración.
- **Fedora / Fedora Magazine** — cambios de mesa de trabajo, módulos y novedades rápidas.
- **Red Hat (RHEL) / Red Hat Blog / Red Hat Developer** — enfoque enterprise, backports, EOL y soporte; lectura obligada para clientes empresariales.
- **SUSE / SUSE Blog & News** — noticias corporativas, soporte empresarial, embedded/edge.
- **DistroWatch** — resumen de lanzamientos/posición de popularidad y enlaces a anuncios oficiales (útil para comparar distros rápidamente).

**Ejemplo de uso real:** si trabajas en infra, lees primero el blog del vendor (RHEL/SUSE/Ubuntu) para saber qué parches aplican a tu versión y después LWN/kernel.org para entender la raíz técnica del parche.

### 3) Medios técnicos y tutoriales (usuario y administrador)

- **OMG!Ubuntu / OMG!Linux / It’s FOSS / HowtoForge** — noticias y tutoriales prácticos fáciles de seguir. Útiles para guías paso a paso, review de apps y trucos de escritorio.

- **Ars Technica / Tom’s Hardware / The Hacker News** — cobertura técnica de incidentes de seguridad, investigaciones y reportajes largos.

**Ejemplo:** tras publicarse una vulnerabilidad (CVE), _The Hacker News_ o _Ars_ suelen ofrecer contexto y mitigaciones orientadas a administradores, mientras que _HowtoForge_ o _It’s FOSS_ publican instrucciones prácticas para aplicar el parche a una distro concreta.

### 4) Comunidad y señales tempranas (por qué seguirlas)

- **Reddit `r/linux`** — noticias rápidas, discusiones de usuarios y señales de problemas reportados por la comunidad (drivers que fallan, problemas de empaquetado).
- **Hacker News** — a menudo enlaza artículos técnicos y trae discusión de desarrolladores/ingenieros.
- **Listas de correo de proyectos** (por ejemplo LKML, listas de cada subsistema) — para cambios de diseño y parches tempranos.

**Ejemplo:** un fallo que aparece masivamente en Reddit puede ser la primera señal de un problema que luego se traduce en CVE y parche en kernel.org; monitorizar la comunidad te da ventaja para crear tickets internos y pruebas.

### 5) Seguridad: feeds y triage (cómo reaccionar)

Fuentes clave: `linux-cve-announce` (lista de kernel CNA), LKML/lore.kernel.org, LWN para análisis y The Hacker News/OSS-sec para explotación en estado real.

**Workflow de ejemplo para un CVE de kernel**

1. **Detección:** suscríbete a `linux-cve-announce` / orquesta NVD/CVEs.
2. **Confirmación:** verifica release/patch en `kernel.org` y busca análisis en LWN.
3. **Impacto:** revisar notas del vendor (RHEL/Ubuntu/SUSE) para ver si tu versión está afectada y si hay backport.
4. **Triage + desplegar:** probar parche en lab, planificar despliegue (WSUS/patch management/Ansible).

### 6) Cómo seguir todo sin volverte loco (herramientas + ejemplo de setup)

Recomendación práctica (mínimo viable):

1. **Un lector RSS** (Feedly, newsboat) con estas fuentes: LWN, kernel.org (releases), linux-cve-announce (si tiene feed), Phoronix, Linux Foundation blog, DistroWatch, y los blogs de tus distros. (muchas de estas páginas tienen RSS).
2. **Suscripción a mailing lists** clave (LKML, linux-cve-announce, listas de subsistemas relevantes). Usa lore.kernel.org para archivar/leer.
3. **Canales de comunidad moderados**: `r/linux` + HN para señales tempranas.
4. **Automatizar alertas**: un pequeño CRON/GitHub Action que revise `kernel.org` releases o el RSS de LWN y te mande un email/Slack cuando aparezca X tipo de artículo (por ejemplo "CVE" o "security"). Ejemplo genérico (pseudo-script):

```bash
# ejemplo: chequea feed (pseudo)
curl -s https://www.kernel.org/feeds/kdist.xml | xmllint --xpath '//item[1]/title/text()' -
# si cambia, enviar notificación (mail / webhook)
```

(Para producción usa un lector RSS serio o integración SIEM para seguridad.)

### 7) Lista “curada” (arranca aquí — 12 publicaciones imprescindibles)

1. **kernel.org** — releases y listas del kernel.
2. **LWN.net** — análisis técnico y seguimiento del LKML.
3. **Phoronix** — benchmarks y hardware/Linux.
4. **Linux Foundation Blog / Linux.com** — proyectos y coordinación.
5. **linux-cve-announce** (lista/archivo) — avisos CVE del kernel.
6. **DistroWatch** — lanzamientos y ranking de distros.
7. **Ubuntu Blog** — noticias y LTS notes (si usas Ubuntu).
8. **Fedora Magazine / Community Blog** — novedades Fedora.
9. **Red Hat Blog / Red Hat Developer** — enterprise & backports.
10. **SUSE Blog / News** — SUSE & Rancher news.
11. **HowtoForge / It’s FOSS / OMG!Ubuntu / OMG!Linux** — tutoriales y how-tos.
12. **Ars Technica / Tom’s Hardware / The Hacker News** — investigación, seguridad y cobertura amplia.

### 8) Consejos para validar información (evitar noticias falsas / clickbait)

- **Primera comprobación:** ¿La noticia tiene enlace al parche o al commit upstream (kernel.org)? Si no, desconfía.
- **Cruza fuentes:** si lo cuenta un blog de usuario, comprueba LWN/Ars/Phoronix o las listas del kernel.
- **Para CVEs:** confirma en `linux-cve-announce` / NVD y en notas de los vendors (RHEL/Ubuntu/SUSE).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**            | **Siguiente 4**      |
| ------------------ | ---------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./2_2_Windows.md) | [⏩](./2_4_MacOS.md) |
