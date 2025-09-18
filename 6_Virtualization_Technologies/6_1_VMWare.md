| **Inicio**         | **Siguiente 2**           |
| ------------------ | ------------------------- |
| [🏠](../README.md) | [⏩](./6_2_VirtualBox.md) |

---

## **Índice**

| Temario                                    |
| ------------------------------------------ |
| [146. VMWare](#146-vmware)                 |
| [147. ¿Qué es VMWare?](#147-qué-es-vmware) |

# **VMWare**

## **146. VMWare**

### ¿Qué es VMware?

**VMware** es la compañía (y la familia de tecnologías) líder en virtualización. Su producto principal —**vSphere**, la plataforma de virtualización empresarial— permite ejecutar, gestionar y mover máquinas virtuales (VMs) a escala en infraestructuras on-premises y en nubes híbridas.

### Productos / componentes principales (breve)

- **ESXi**: hypervisor _bare-metal_ (tipo 1) que se instala directamente sobre el servidor físico y ejecuta VMs. Es la capa que gestiona CPU, memoria, I/O y recursos para las VMs.
- **vCenter Server**: plataforma de gestión centralizada para múltiples hosts ESXi — desde aquí administras clústeres, DRS, HA, vMotion, permisos, etc.
- **vSphere**: conjunto (ESXi + vCenter + servicios) para virtualización empresarial.
- **vSAN**: almacenamiento definido por software que agrega discos locales de hosts ESXi para crear datastores compartidos tipo HCI. Ideal para entornos hiperconvergentes.
- **Workstation / Fusion**: hypervisores tipo 2 para escritorios (Workstation para Windows/Linux, Fusion para macOS) — pensados para desarrollo, pruebas y desktop virtualization.
- **NSX** (network virtualization), **Horizon** (VDI), herramientas de backup (ej. integraciones como Veeam) y utilidades CLI / APIs (PowerCLI, govc, ovftool), entre otros.

### Conceptos clave y cómo funcionan (con ejemplos)

#### 1. Hypervisor tipo 1 vs tipo 2

- **Tipo 1 (ESXi)**: se instala directamente en el hardware. Ejemplo: instalas ESXi en un servidor Dell/HP, accedes al **Host Client** (web) o lo registras en vCenter.
- **Tipo 2 (Workstation/Fusion)**: corren sobre un SO host (Windows/macOS) y sirven para lab, desarrollo o testing.

#### 2. Datastores y almacenamiento

- **VMFS**: sistema de archivos de VMware para datastores en block storage (SAN/iSCSI).
- **vSAN**: suma discos locales en un pool distribuido; provee snapshots, políticas de almacenamiento y escalado.

#### 3. Networking: vSwitches y vDS

- **vSS (vSphere Standard Switch)**: switch por host.
- **vDS (vSphere Distributed Switch)**: switch administrado desde vCenter y consistente en todos los hosts del clúster (útil para NSX, QoS y políticas centrales).

#### 4. Live migration / alta disponibilidad / balanceo

- **vMotion**: mueve una VM en ejecución entre hosts sin downtime (memoria y estado transmitidos).
- **DRS (Distributed Resource Scheduler)**: balancea carga entre hosts del clúster moviendo VMs con vMotion.
- **vSphere HA**: detecta fallos de host y reinicia VMs en otros hosts disponibles.

### Ejemplo práctico: crear una VM en ESXi (paso a paso)

(Asumamos que ya tienes ESXi instalado y acceso al Host Client web o a vCenter)

1. **Subir ISO al datastore**

   - En el Host Client: Storage → Datastore browser → Upload → selecciona `ubuntu-22.04.iso` (por ejemplo).

2. **Crear la VM** (Host Client / vSphere Client):

   - New VM → Asignar nombre `web01` → Guest OS (Linux → Ubuntu 64-bit) → CPU 2, Memoria 4 GB → Disco: 40 GB (thin provisioning) → Conectar CD/DVD al ISO subido → NIC: VM Network (NAT/Bridged según red).

3. **Instalar el SO**

   - Power on → abrir console → seguir instalador de Ubuntu.

4. **Instalar VMware Tools / Open VM Tools** (mejor integración, drivers paravirtualizados):

   - En Linux: `sudo apt install open-vm-tools`.

5. **Snapshot antes de cambios** (capa de protección):

   - En vSphere Client: VM → Snapshots → Take snapshot → "before-update".
   - **Advertencia**: no mantener snapshots largos — consumen espacio y degradan I/O.

6. **Operaciones útiles desde shell del host** (ejemplos):

   - Listar VMs: `vim-cmd vmsvc/getallvms`
   - Encender VM: `vim-cmd vmsvc/power.on <vmid>`
   - Crear disco virtual: `vmkfstools -c 20G -d thin /vmfs/volumes/datastore1/vm/vm.vmdk`
   - Ver NICs: `esxcli network nic list`

(Estas herramientas son las que encuentras en la documentación y en la Shell ESXi.)

### Automatización y herramientas CLI

- **PowerCLI (PowerShell)**: la herramienta oficial para automatizar vSphere desde Windows/Linux (PS). Ejemplo para crear snapshot:

```powershell
Get-VM -Name "web01" | New-Snapshot -Name "pre-update" -Description "Antes de patch"
```

- **ovftool**: importar/exportar OVF/OVA:

```bash
ovftool my-appliance.ova vi://root@esxi-host/
```

- **govc** (CLI para vSphere en Go) y **Ansible modules** también se usan mucho.

### Operaciones avanzadas / características empresariales

- **vMotion**: live migration sin downtime (uso típico: mantenimiento hardware).
- **DRS**: policy-driven load balancing y colocación inicial de VM.
- **HA**: reinicio automático de VMs en fallos de host.
- **vSAN**: creación de datastores resilientes sin storages externos — define políticas por VM (failures to tolerate, stripe).

### Backups, snapshots y clones (mejor práctica)

- **Snapshots**: útiles como checkpoints rápidos (antes de un update) — **no** usarlos como copia de seguridad a largo plazo.
- **Clonación / Templates**: conviene crear templates de VMs (gold images) y desplegar desde ellos.
- **Backups integrados**: usa soluciones compatibles (Veeam, Commvault, etc.) que integren con APIs de vSphere y permitan restore consistente.

### Networking y seguridad

- **Seguridad**: aplicar roles/permiso en vCenter, usar NSX para microsegmentación, habilitar vSphere encryption para discos sensibles.
- **Network binding**: separar trafico management, vmotion, storage, vs ANCHOR NICs y VLANs para rendimiento y seguridad.

### Rendimiento y tuning (puntos a vigilar)

- **NUMA awareness**: ajustar vCPU vs pCPU y tamaño de memoria para respetar nodos NUMA.
- **Overcommit vs reservations**: reservar CPU/Memory si la VM lo requiere (DBs suelen necesitar reservas).
- **Paravirtual drivers**: usar PV SCSI y vmxnet3 para mejor throughput.
- **Monitorizar**: vCenter Performance Charts, vRealize o Prometheus/Grafana para métricas.

### Ejemplos concretos de comandos útiles

- Listar hosts y VMs (PowerCLI):

```powershell
Connect-VIServer vcenter.mydomain
Get-VM | Select Name, PowerState, NumCpu, MemoryGB
Get-VMHost | Get-VMHostNetworkAdapter
```

- Desde ESXi shell:

```bash
vim-cmd vmsvc/getallvms
esxcli storage filesystem list
vmkfstools -i /path/source.vmdk /path/dest.vmdk -d thin
```

### Casos de uso típicos

- **Consolidación de servidores** (reduce costes HW).
- **Entornos de test / dev** (clonación rápida de entornos).
- **VDI (virtual desktop infrastructure)** con Horizon.
- **DR / migraciones** usando vMotion y SRM (Site Recovery Manager).
- **HCI** con vSAN para tiendas con escalado horizontal.

### Buenas prácticas y “gotchas”

- Mantén vCenter y ESXi actualizados (parches) pero valida compatibilidad de hardware.
- No mantengas snapshots por mucho tiempo.
- Planifica capacidad (CPU, RAM, IOPS) y evita overcommit excesivo.
- Haz pruebas antes de actualizar herramientas o migrar hardware (lab).
- Usa VMware Tools para mejor integración y drivers en guest OS.
- Revise licenciamiento y restricciones (ediciones) según la versión y uso (vSphere, vSAN, NSX, etc.).

### Recursos oficiales y lectura recomendada

- VMware vSphere product docs — overview y guías.
- VMware ESXi / VMFS docs (conceptos y operaciones).
- vSAN tech overview y deployment guide (si vas a usar HCI).

---

[🔼](#índice)

---

## **147. ¿Qué es VMWare?**

**VMware** es una compañía y una plataforma líder en virtualización que permite ejecutar múltiples **máquinas virtuales (VMs)** sobre servidores físicos. Con VMware puedes abstraer CPU, memoria, almacenamiento y red del hardware físico para crear entornos aislados, portables y gestionables a escala (desde un portátil hasta un datacenter).

### Productos clave (qué hace cada uno)

- **ESXi** — _Hypervisor_ tipo-1 (bare-metal). Se instala directamente en el servidor físico y ejecuta las VMs.
- **vCenter Server** — consola/servicio centralizado para gestionar múltiples hosts ESXi (clústeres, vMotion, HA, DRS).
- **vSphere** — la plataforma que engloba ESXi + vCenter y servicios relacionados.
- **vSAN** — almacenamiento definido por software para crear datastores distribuidos con discos locales.
- **VMware Workstation / Fusion** — hypervisores tipo-2 para desktops (desarrollo/labs).
- **NSX** — virtualización de red (microsegmentación, routing virtual).
- **Horizon** — infraestructura de escritorios virtuales (VDI).

### Conceptos fundamentales

- **Máquina virtual (VM):** un equipo virtual con vCPU, vRAM, disco virtual (VMDK), NIC virtual, etc.
- **vCPU / vRAM:** recursos virtuales asignados a la VM; mapean a CPU/RAM físicas mediante el hypervisor.
- **Datastore:** espacio de almacenamiento para VMDKs — puede ser VMFS, NFS o vSAN.
- **vMotion:** migración en vivo de una VM entre hosts sin downtime (memoria/estado transferido).
- **Snapshot:** captura del estado de una VM en un momento; útil como checkpoint (no es backup a largo plazo).
- **Template / Clone:** plantillas para desplegar VMs rápidamente y de forma consistente.
- **vSwitch / vDS:** switches virtuales que conectan VMs a redes físicas o virtuales; vDS es administrado centralmente por vCenter.

### ¿Cómo funciona (arquitectura básica)?

1. Instalación: instalas **ESXi** en un servidor físico.
2. Gestión: registras el ESXi en **vCenter** (si hay varios hosts).
3. Provisionamiento: desde vSphere Client creas VMs, defines datastores, redes y políticas.
4. Operaciones: funciones como **vMotion** (mover VMs entre hosts), **DRS** (balanceo), **HA** (alta disponibilidad) se gestionan desde vCenter.

### Ejemplos prácticos paso a paso

#### Ejemplo A — Crear una VM sencilla en ESXi (UI)

1. Sube la ISO del sistema operativo al _Datastore_ (Storage → Datastore browser → Upload).
2. New → Virtual Machine → elige nombre y guest OS (ej. Ubuntu 22.04) → asigna CPU/RAM/disco (p. ej. 2 vCPU, 4 GB RAM, 40 GB VMDK) → conectar CD/DVD a la ISO subida → Finish.
3. Power on → Open Console → instalar SO.
4. Instala **open-vm-tools** dentro del guest (mejor integración y drivers).

#### Ejemplo B — Comandos básicos en ESXi Shell

```bash
# listar VMs y sus IDs
vim-cmd vmsvc/getallvms

# encender VM (reemplaza <vmid>)
vim-cmd vmsvc/power.on <vmid>

# apagar VM (graceful)
vim-cmd vmsvc/power.shutdown <vmid>

# clonar/crear disco virtual
vmkfstools -c 20G -d thin /vmfs/volumes/datastore1/miVM/miVM.vmdk
```

#### Ejemplo C — Crear snapshot con PowerCLI (desde un admin)

```powershell
# Conectar a vCenter
Connect-VIServer -Server vcenter.miempresa.local -User admin -Password '***'

# Crear snapshot de una VM llamada web01
Get-VM -Name "web01" | New-Snapshot -Name "pre-update" -Description "Antes parche"
```

#### Ejemplo D — vMotion (UI: operaciones)

- Mover una VM en ejecución: Botón Right-click → Migrate → Change host → seleccionar host destino → Confirm.
- Requisito: almacenamiento compartido o vSphere vMotion con Storage vMotion.

### Casos de uso comunes

- **Consolidación de servidores:** reducir hardware físico al ejecutar muchas VMs en hosts potentes.
- **Entornos Dev/Test:** desplegar snapshots y clones rápidamente.
- **VDI:** escritorios virtuales centralizados con Horizon.
- **Alta disponibilidad / DR:** vSphere HA, Site Recovery Manager.
- **HCI:** vSAN para almacenamiento sin SAN externo.

### Buenas prácticas y recomendaciones

- **VMware Tools / Open VM Tools** siempre instalados en los guests.
- **No mantener snapshots por largo tiempo** (consumen espacio y degradan I/O).
- **Planea capacidad** (CPU/RAM/IOPS) y evita overcommit extremo en producción.
- **Usa paravirtual drivers** (vmxnet3, PVSCSI) para mejor rendimiento de red y disco.
- **Seguridad:** separar redes (management, vMotion, storage, VM traffic), aplicar roles en vCenter y actualizar parches.
- **Backups:** usar soluciones integradas (Veeam, Commvault) que interactúan con APIs de vSphere para backups consistentes.

### Alternativas y compatibilidades

- Alternativas populares: **KVM**, **Microsoft Hyper-V**, **VirtualBox**.
- VMware ofrece herramientas de import/export (OVF/OVA, ovftool) para mover máquinas entre plataformas.

### Cómo empezar (mínimos pasos para probar)

1. En un portátil: instala **VMware Workstation Player** (o Fusion en Mac) para crear VMs localmente.
2. Para laboratorio: descarga la ISO de **ESXi** (eval/trial) y prueba instalarla en una máquina o en nested virtualization dentro de Workstation.
3. Para gestión a escala: evalúa una instalación de **vCenter** (VM) y conecta varios hosts ESXi.

> Nota: las políticas de licenciamiento varían por producto y edición; si vas a producción revisa las opciones de licencia de VMware.

### Problemas comunes y soluciones rápidas

- **VM muy lenta** → revisar reservas/limits, NUMA, IOPS, drivers.
- **Fallo de vMotion** → sincronizar versiones de VM Hardware y compatibilidad de CPU (EVC).
- **Snapshots acumulados** → consolidar y liberar espacio.
- **Conectividad de red** → revisar vSwitch, VLANs y teaming.

### Recursos y siguientes pasos

- Documentación oficial VMware (vSphere, ESXi, vSAN).
- Práctica: monta un laboratorio con ESXi + vCenter en dos máquinas virtuales y prueba vMotion, snapshot, clonación y creación de templates.
- Aprende PowerCLI para automatizar tareas administrativas.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**           |
| ------------------ | ------------------------- |
| [🏠](../README.md) | [⏩](./6_2_VirtualBox.md) |
