| **Inicio**         | **atrás 3**         | **Siguiente 5**           |
| ------------------ | ------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./6_3_ESXi.md) | [⏩](./6_5_Hypervisor.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [152. Proxmox](#152-proxmox)                                                     |
| [153. Entorno virtual Proxmox](#153-entorno-virtual-proxmox)                     |
| [154. ¿Qué es la virtualización Proxmox?](#154-qué-es-la-virtualización-proxmox) |

# **Proxmox**

## **152. Proxmox**

### ¿Qué es **Proxmox VE**?

**Proxmox VE (Virtual Environment)** es una plataforma de virtualización de código abierto basada en Debian que integra dos tecnologías principales:

- **KVM/QEMU** para máquinas virtuales (VMs) — virtualización completa (como VMware ESXi).
- **LXC** para contenedores ligeros (CTs) — aislamiento a nivel de sistema operativo.

Proxmox agrega una **interfaz web**, **API/CLI**, clustering nativo, gestión de almacenamiento (ZFS, Ceph, LVM, NFS, etc.), backups, HA y herramientas para orquestar todo en un datacenter pequeño/mediano.

### Componentes clave (resumen)

- **pve-manager**: el servicio que corre la interfaz web y la gestión central.
- **QEMU/KVM**: motor de VMs.
- **LXC**: motor de contenedores.
- **pve-cluster / corosync**: clustering y sincronización entre nodos.
- **pve-storage**: integración de múltiples tipos de almacenamiento (local, LVM, ZFS, NFS, Ceph).
- **vzdump / Proxmox Backup Server (PBS)**: backups de VMs/CTs; PBS ofrece deduplicación y backups incrementales.
- **GUI web**: consola gráfica (https\://IP_del_nodo:8006) para administrar todo.
- **CLI / API**: `qm`, `pct`, `pvecm`, `pvesh` para automatización.

### ¿Cómo funciona (arquitectura básica)?

1. **Instalas Proxmox VE** en un servidor (recomendado con la ISO oficial).
2. Creas VMs (KVM) o CTs (LXC).
3. El host gestiona recursos (CPU, memoria, I/O) y presenta redes virtuales (bridges) y datastores.
4. Al agregar más nodos, los unes en un **cluster** (pvecm), que permite migrar VMs entre nodos, HA y gestión centralizada.
5. Para almacenamiento distribuido a escala, integras **Ceph** o **vSAN-like** (ZFS en cada nodo + sincronización).

### Ejemplos prácticos — pasos y comandos útiles

> **Nota**: muchos usuarios administran Proxmox desde la **Web GUI** (más sencillo). Abajo tienes flujos equivalentes por **CLI**.

#### 1) Instalar Proxmox

- Método recomendado: bajar la **ISO oficial de Proxmox VE**, crear USB booteable e instalar.
- Alternativa avanzada: instalar sobre Debian y añadir repositorios Proxmox (para entornos controlados).

#### 2) Crear una VM desde la Web UI

1. Subir ISO: _Datacenter → Storage → ISO Images → Upload_.
2. _Create VM_ → elegir ID/nombre → resources (CPU/RAM) → disco (local-lvm / ZFS) → network (bridge vmbr0) → finish → _Start_ → abrir consola y proceder a la instalación del OS.

#### 3) Crear una VM desde CLI (`qm`)

Ejemplo (crea VM id 100, 4 GB RAM, 2 cores, disco 32 GB, ISO como CD):

```bash
# Crear VM y asignar recursos
qm create 100 --name ubuntu22 --memory 4096 --cores 2 --net0 virtio,bridge=vmbr0

# Crear disco y asignarlo como scsi0 (32G thin)
qm set 100 --scsihw virtio-scsi-pci --scsi0 local-lvm:32

# Adjuntar ISO como CD (suponiendo que subiste iso a storage 'local')
qm set 100 --ide2 local:iso/ubuntu-22.04.iso,media=cdrom

# Configurar arranque
qm set 100 --boot c --bootdisk scsi0

# Iniciar VM
qm start 100
```

#### 4) Crear un contenedor LXC con `pct`

Ejemplo (ID 101, plantilla de Ubuntu, 2 GB RAM, rootfs 8GB, net dhcp):

```bash
pct create 101 local:vztmpl/ubuntu-22.04-standard_22.04-1_amd64.tar.gz \
  --hostname ct01 --cores 2 --memory 2048 --rootfs local-lvm:8 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp --start 1
```

`pct start 101`, `pct enter 101` para entrar dentro del contenedor.

#### 5) Networking: configuración de puente (bridge)

Ejemplo `/etc/network/interfaces` (proxmox/debian style):

```text
auto vmbr0
iface vmbr0 inet static
    address 192.168.1.10/24
    gateway 192.168.1.1
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
```

VLAN en una VM: en net0 agregar `tag=100`:

```
net0: virtio=DE:AD:BE:EF:00:01,bridge=vmbr0,tag=100
```

#### 6) ZFS como almacenamiento (ejemplo rápido)

En la instalación puedes elegir ZFS. Para crear un pool manualmente:

```bash
zpool create -f rpool mirror /dev/sda /dev/sdb
```

Proxmox detectará `rpool` y podrás agregarlo como storage para VMs/CTs.

#### 7) Backups con `vzdump`

Backup de VM 100 en modo snapshot al storage `backup` con compresión zstd:

```bash
vzdump 100 --mode snapshot --compress zstd --storage backup
```

Restore (ej. QEMU backup) con `qmrestore`:

```bash
qmrestore /var/lib/vz/dump/vzdump-qemu-100-2025_09_02-00-00.vma.zst 200 --storage local-lvm
```

#### 8) Migración en vivo (live migration)

Desde CLI (requiere shared storage o use online migration with storage migration):

```bash
qm migrate 100 nodo-destino --online
```

#### 9) Crear un cluster básico

En el **primer** nodo:

```bash
pvecm create mi-cluster
```

En nodos secundarios:

```bash
pvecm add IP_DEL_PRIMER_NODO
```

Después de esto los nodos comparten configuración; puedes administrar VMs desde cualquier nodo.

### Alta disponibilidad (HA) y Ceph

- **HA**: Proxmox utiliza `pvecm` + `corosync` para coordinar estado; al habilitar HA para una VM, si el host falla la VM se reiniciará en otro nodo disponible (requiere almacenamiento compartido o Ceph).
- **Ceph**: integración nativa (CephFS/RBD) para almacenamiento distribuido altamente disponible. Proxmox facilita la creación de clústeres Ceph.

### Proxmox Backup Server (PBS)

- Solución complementaria para backups con deduplicación, cifrado y backups incrementales.
- Flujo típico: Proxmox VE → storage backup via PBS → restores rápidas y deduplicadas.

### Seguridad y gestión

- **Autenticación**: usuarios locales, LDAP/AD, 2FA (TOTP).
- **Firewall**: PVE firewall por host y por VM/CT.
- **Actualizaciones**: usa repositorio enterprise si tienes suscripción; para community ed., usar repositorio no-subscription (o habilitar repositorios públicos).
- **SSH**: usar llaves, deshabilitar root-password sin llave, limitar acceso.

### Diferencias frente a otras soluciones (breve)

- A diferencia de VMware ESXi, Proxmox es **open source** y combina **VMs + contenedores** nativamente; ZFS y Ceph están integrados; tiene una **web UI** potente y API REST completísima. Es ideal para POCs, labs, SMB y producción con menor coste de licenciamiento.

### Buenas prácticas / recomendaciones

- Usa **ZFS** o almacenamiento redundante para producción (RAID/ZFS/CEPH).
- Separa tráfico: management, storage, vMotion en NICs/VLANs separadas.
- Haz **backups regulares** y testea restores.
- No mantener snapshots muy largos.
- Monitoriza con Prometheus/Grafana o las métricas integradas.
- Evalúa Proxmox Backup Server para deduplicación y eficiencia en backups.

---

[🔼](#índice)

---

## **153. Entorno virtual Proxmox**

### 1) ¿Qué es el _entorno virtual Proxmox_?

Proxmox VE (Virtual Environment) es una plataforma de virtualización **open-source** basada en Debian que integra:

- **KVM/QEMU** para máquinas virtuales completas (VMs).
- **LXC** para contenedores ligeros (CTs).
- Una **interfaz web** (GUI), API REST y herramientas CLI para gestionar todo (VMs, contenedores, almacenamiento, red, cluster, backups, HA, etc.).

Proxmox permite administrar nodos individuales o clústers completos, soporta múltiples backends de almacenamiento (ZFS, LVM, NFS, Ceph, iSCSI, etc.) y se integra con **Proxmox Backup Server (PBS)** para backups eficientes.

### 2) Componentes y arquitectura (resumen)

- **Nodo:** servidor físico con Proxmox VE instalado.
- **Cluster:** conjunto de nodos coordinados (pvecm / corosync) que comparten configuración y permiten migraciones/HA.
- **pve-manager:** servicio que corre la GUI/API.
- **QEMU/KVM & LXC:** motores para VMs y containers.
- **Storage:** `local`, `local-lvm`, `ZFS`, `Ceph/RBD`, `NFS`, `iSCSI`, etc.
- **Networking:** bridges (`vmbr0`), bonding, VLANs (tag), SDN opcional.
- **Backup:** `vzdump` (builtin) y PBS (deduplicación/incrementales).

### 3) Instalación rápida (pasos principales)

1. Descarga la ISO oficial de Proxmox VE.
2. Crea USB booteable (Rufus, dd).
3. Bootea el servidor e instala (opción de ZFS es típica para root).
4. Configura IP de gestión, hostname, DNS y NTP en el instalador.
5. Accede a la GUI: `https://IP_DEL_NODO:8006` y entra con `root` y la contraseña creada.

**Tip:** si vas a clúster, asegúrate de que todos los nodos tengan nombres DNS resolubles y hora sincronizada (NTP).

### 4) Crear VMs y CTs — ejemplos prácticos

#### 4.1 Crear VM (CLI con `qm`)

```bash
# Crear VM id=100, 2 cores, 4GB RAM, interfaz virtio
qm create 100 --name ubuntu22 --memory 4096 --cores 2 \
  --net0 virtio,bridge=vmbr0

# Crear disco 32GB en storage local-lvm y asignarlo (scsi)
qm set 100 --scsihw virtio-scsi-pci --scsi0 local-lvm:32

# Adjuntar ISO (subida a storage local:iso/)
qm set 100 --ide2 local:iso/ubuntu-22.04.iso,media=cdrom

# Arrancar VM
qm start 100
```

Luego abre la consola desde la GUI (`https://IP:8006`) y procede con la instalación del SO.

#### 4.2 Crear LXC container (CLI con `pct`)

```bash
# Asume que subiste plantilla a local:vztmpl/
pct create 101 local:vztmpl/ubuntu-22.04-standard_22.04-1_amd64.tar.gz \
  --hostname ct01 --cores 2 --memory 2048 --rootfs local-lvm:8 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp --start 1
```

`pct enter 101` para acceder al contenedor desde el host.

### 5) Storage: opciones y ejemplos

- **local-lvm**: rápido para VMs en un único host (thin provisioning).
- **ZFS**: snapshot, compresión, envío/replicación (muy común en Proxmox).

  ```bash
  # ejemplo: crear zpool (simple)
  zpool create -f rpool mirror /dev/sda /dev/sdb
  ```

- **Ceph**: almacenamiento distribuido, recomendado para clústers con alta disponibilidad.
- **NFS / iSCSI**: útil para storage compartido sin Ceph.
- Comprueba los storages con:

  ```bash
  pvesm status
  ```

### 6) Networking: bridges, VLANs y bonding (ejemplos)

#### /etc/network/interfaces (ejemplo básico con bridge + bond)

```text
auto lo
iface lo inet loopback

auto eno1
iface eno1 inet manual

auto eno2
iface eno2 inet manual

auto bond0
iface bond0 inet manual
    bond-slaves eno1 eno2
    bond-miimon 100
    bond-mode 802.3ad

auto vmbr0
iface vmbr0 inet static
    address 192.168.10.10/24
    gateway 192.168.10.1
    bridge-ports bond0
    bridge-stp off
    bridge-fd 0
```

#### VLAN en una VM (net0)

En la GUI o con `qm set`:

```
net0: virtio=DE:AD:BE:EF:00:01,bridge=vmbr0,tag=100
```

Eso coloca la interfaz en VLAN 100.

### 7) Clúster y migración en vivo

#### Crear clúster (nodo maestro)

```bash
pvecm create mi-cluster
```

#### Añadir nodo secundario (desde nodo secundario)

```bash
pvecm add IP_NODO_PRIMARIO
```

**Requisitos:** hostnames resolubles, NTP sincronizado, puertos corosync abiertos (por defecto 5404/5405) y buena conectividad.

#### Migrar VM en caliente

```bash
qm migrate 100 nodo-destino --online
```

(Para migración online normalmente necesitas storage compartido o usar almacenamiento migrado online).

### 8) Alta disponibilidad (HA)

- Una vez en clúster, habilitas HA (desde GUI → Datacenter → HA) para VMs que requieres que se reinicien en otros nodos si el host falla.
- **Requisito**: almacenamiento compartido (Ceph, NFS, iSCSI) o replicación/sincronización adecuada; de lo contrario la VM no será reiniciable en otro host con su disco.

### 9) Backups y restores

#### Hacer backup (vzdump)

```bash
# Backup de VM 100 con snapshot, compresión zstd al storage 'backup'
vzdump 100 --mode snapshot --compress zstd --storage backup
```

#### Restaurar VM

```bash
qmrestore /var/lib/vz/dump/vzdump-qemu-100-2025_09_02-00-00.vma.zst 200 --storage local-lvm
```

#### Proxmox Backup Server (PBS)

- Recomendado para backups incrementales y deduplicados; se integra nativamente como storage en la GUI.

### 10) Monitorización y automatización

- GUI muestra métricas, logs y tasks.
- CLI/API: `pvesh` (API shell) y `pvesm`, `qm`, `pct` para scripting.
  Ejemplo: listar nodos vía API:

  ```bash
  pvesh get /nodes
  ```

- Integra con **Prometheus + Grafana** (exporter disponibles) para monitorización avanzada.

### 11) Seguridad & buenas prácticas

- Usa **SSH keys**, deshabilita root password SSH cuando sea posible.
- Habilita **2FA** en la GUI (TOTP).
- Separa redes: management, storage, VM traffic, backup.
- Usa ZFS/RAID/Ceph para redundancia.
- Testea restores de backup periódicamente.
- Aplica actualizaciones desde repositorios oficiales (si es producción, usa repos. enterprise o ventana de mantenimiento).

### 12) Diferencias prácticas: Proxmox vs otros

- **Vs VMware ESXi:** Proxmox es open-source, combina VMs y containers, integra ZFS/Ceph fácilmente; menor coste de licencias.
- **Vs soluciones cloud:** Proxmox es para infra on-premise o colocation, ideal para POCs, SMB y datacenters privados.

### 13) Comandos “cheat-sheet” (útiles)

```bash
# VMs / containers
qm list                      # listar VMs (QEMU)
pct list                     # listar containers (LXC)

qm start 100                 # arrancar VM
qm stop 100                  # apagar VM (force)
pct start 101                # iniciar container

qm migrate 100 node2 --online
vzdump 100 --storage backup --compress zstd
qmrestore <backupfile> <newid> --storage local-lvm

pvecm create mi-cluster
pvecm add IP_DEL_PRIMARIO
pvesm status                 # estado storages
pvesh get /nodes             # API: listar nodos
```

### 14) Casos de uso típicos

- **Lab / desarrollo:** rápidos despliegues de VMs/CTs.
- **SMB / hosting:** infraestructura económica y potente.
- **Producción a escala:** con Ceph, HA y PBS, entornos tolerantes a fallos.
- **Edge / ROBO:** nodos Proxmox con ZFS para replicación eficiente.

---

[🔼](#índice)

---

## **154. ¿Qué es la virtualización Proxmox?**

### ¿Qué es la **virtualización Proxmox**?

**Resumen corto:**

La _virtualización Proxmox_ se refiere al uso de **Proxmox VE (Virtual Environment)** como plataforma para ejecutar **máquinas virtuales (KVM/QEMU)** y **contenedores (LXC)** sobre servidores físicos, gestionando almacenamiento, redes, backups, clustering y alta disponibilidad desde una única interfaz web y API. Es una solución open-source pensada para labs, entornos de producción y nubes privadas.

### 1) ¿Cómo funciona (arquitectura esencial)?

- **Host Proxmox (nodo):** sistema Debian con `pve-manager` que orquesta KVM y LXC.
- **KVM/QEMU:** virtualización completa — cada VM tiene su kernel y alto aislamiento. Requiere VT-x/AMD-V.
- **LXC:** contenedores basados en namespaces y cgroups del kernel — mucho más ligeros; comparten el kernel del host.
- **Storage backends:** `local`, `local-lvm`, **ZFS**, **Ceph/RBD**, NFS, iSCSI, etc.
- **Networking:** bridges (vmbr0), bonding, VLANs, NAT opcional; las VMs/CTs se conectan a bridges que exponen la red física.
- **Cluster:** `pvecm` + `corosync` sincronizan configuración y permiten migraciones/HA entre nodos.
- **Backups:** `vzdump` integrado; opcional Proxmox Backup Server (PBS) para backups incrementales/deduplicados.

### 2) Diferencia VM (KVM) vs Contenedor (LXC) — ¿cuándo usar cada uno?

- **VM (KVM)**

  - Aislamiento completo, kernel propio.
  - Ideal para: bases de datos, sistemas con requisitos kernel específicos, appliances, VMs Windows.
  - Sobrecarga moderada (CPU/memoria/disk).

- **Contenedor (LXC)**

  - Ligero, arranque rápido, menos uso de recursos.
  - Ideal para: microservicios, servicios Linux que no requieren kernel diferente, entornos de CI/CD.
  - Menos aislamiento (mismo kernel del host).

**Ejemplo práctico:** front-end en LXC (ligero), base de datos en VM (aislada y con tuning de kernel).

### 3) Ejemplos prácticos — comandos y flujos comunes

#### Crear una VM (KVM) con `qm`

```bash
# 1) Crear VM (ID 100)
qm create 100 --name web01 --memory 4096 --cores 2 --net0 virtio,bridge=vmbr0

# 2) Añadir disco (32GB en local-lvm)
qm set 100 --scsihw virtio-scsi-pci --scsi0 local-lvm:32

# 3) Adjuntar ISO (suponiendo que subiste ubuntu-22.04.iso a storage 'local')
qm set 100 --ide2 local:iso/ubuntu-22.04.iso,media=cdrom

# 4) Arrancar
qm start 100
```

#### Crear un contenedor LXC con `pct`

```bash
# Crear CT id 101 desde plantilla subida a local:vztmpl
pct create 101 local:vztmpl/ubuntu-22.04-standard_22.04-1_amd64.tar.gz \
  --hostname ct-web --cores 1 --memory 1024 --rootfs local-lvm:8 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp --start 1
```

#### Snapshot / Backup / Restore

```bash
# Snapshot VM
qm snapshot 100 pre-update --description "Antes de actualizar"

# Backup (vzdump)
vzdump 100 --mode snapshot --compress zstd --storage backup

# Restaurar backup en nuevo ID 200
qmrestore /var/lib/vz/dump/vzdump-qemu-100-2025_09_02.vma.zst 200 --storage local-lvm
```

#### Migración en vivo (online migrate)

```bash
# Mover VM 100 al nodo destino (requiere storage compartido o Storage migration)
qm migrate 100 nodo-destino --online
```

#### Crear ZFS pool (ejemplo rápido)

```bash
# Crear pool ZFS espejo
zpool create -f rpool mirror /dev/sda /dev/sdb
# Proxmox detectará rpool y podrás usarlo como storage
```

### 4) Networking: ejemplo de bridge en `/etc/network/interfaces`

```text
auto vmbr0
iface vmbr0 inet static
    address 192.168.10.10/24
    gateway 192.168.10.1
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
```

- Las VMs con `bridge=vmbr0` reciben IP en la LAN física si está en modo bridged.

### 5) Clúster y alta disponibilidad (breve)

- **Crear clúster (nodo maestro):**

```bash
pvecm create mi-cluster
```

- **Agregar nodo secundario (desde el secundario):**

```bash
pvecm add IP_DEL_PRIMARIO
```

- **Habilitar HA para VM** desde GUI → Datacenter → HA o con `ha-manager` en CLI. Requiere almacenamiento compartido o Ceph para reiniciar VM en otro nodo.

### 6) Storage: opciones y recomendaciones

- **local-lvm:** práctico pero no compartido entre nodos.
- **ZFS (local):** snapshots nativos, compresión, send/receive (buena para backups/replicación).
- **Ceph/RBD:** almacenamiento distribuido para clústeres con tolerancia a fallos y alta disponibilidad.
- **NFS / iSCSI:** simples de integrar como storage compartido.

**Buen consejo:** para clústeres de producción usa Ceph o un NFS/ISCSI compartido para poder migrar/recuperar VMs entre nodos.

### 7) Backups con Proxmox Backup Server (PBS)

- PBS provee backups incrementales, deduplicación y restauraciones rápidas.
- Flujo típico: Proxmox VE → tarea de backup → PBS almacena deduplicado y versionado.
- Recomendado para entornos con muchas VMs/CTs.

### 8) Buenas prácticas / recomendaciones operativas

- **Instalación:** usa ISO oficial y, si vas a producción, considera ZFS para root o LVM + Ceph para datos.
- **Red:** separa management, storage y VM traffic en NICs/VLANs.
- **Drivers:** en VMs Linux, usar `virtio` (paravirtual) para NIC y SCSI.
- **Snapshots:** útiles para checkpoints; no sustituye backups. No los mantengas mucho tiempo.
- **Backups:** automatiza con `vzdump` o PBS; prueba restores.
- **Seguridad:** 2FA en GUI, SSH keys, firewalls por VM, limitar acceso al puerto 8006.
- **Monitorización:** integra Prometheus/Grafana o usa las métricas integradas.

### 9) Casos de uso reales y ejemplos de arquitectura

- **Pequeña empresa (2 nodos):** ZFS local + NFS para backups → correr VMs de servicios internos; replicación manual con `zfs send` para DR.
- **Mediana empresa (3+ nodos):** cluster Proxmox + Ceph OSDs en cada nodo → VMs con HA, live-migrate, almacenamiento distribuido.
- **Lab / desarrollo:** un solo nodo Proxmox con LXC para microservicios y VMs para bases de datos.

**Ejemplo concreto de despliegue:**

- Nodo1, Nodo2, Nodo3 (cada uno 32GB RAM, SSDs)
- Ceph OSDs en discos de datos → pool RBD
- VMs críticas (DB) en KVM con discos sobre RBD, HA activado.
- Containers web en LXC sobre local-zfs, backups diarios en PBS.

### 10) Problemas frecuentes y soluciones rápidas

- **VM no arranca por falta de espacio:** revisa `pvesm status` y espacio de datastores.
- **Lento I/O:** usar discos NVMe/SSD, comprobar queue depth, usar `virtio` y PVSCSI.
- **Errores de migración:** comprobar versiones de Proxmox/CEPH y conectividad entre nodos (ping, ports corosync).
- **Plantillas/ISO no aparecen:** verificar storage y permisos; usar GUI → Storage → ISO Images → Upload.

### Resultado final — ¿qué te llevas?

- **Proxmox VE** es una plataforma integral para virtualización (VMs + containers) con GUI, API, clustering y soporte para almacenamiento moderno (ZFS, Ceph).
- Es **flexible** (labs → producción), **económico** (open source) y **potente** (migración en vivo, HA, backups avanzados con PBS).
- Con los comandos `qm`, `pct`, `pvecm`, `vzdump` puedes automatizar todo desde CLI; la GUI facilita la operativa diaria.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**         | **Siguiente 5**           |
| ------------------ | ------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./6_3_ESXi.md) | [⏩](./6_5_Hypervisor.md) |
