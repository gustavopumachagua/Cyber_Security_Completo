| **Inicio**         | **atrás 2**               | **Siguiente 4**        |
| ------------------ | ------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_2_VirtualBox.md) | [⏩](./6_4_Proxmox.md) |

---

## **Índice**

| Temario                                              |
| ---------------------------------------------------- |
| [150. ¿Qué es ESXi?](#150-qué-es-esxi)               |
| [151. ¿Qué es VMWare ESXi?](#151-qué-es-vmware-esxi) |

# **ESXi**

## **150. ¿Qué es ESXi?**

**VMware ESXi** es un _hypervisor_ de tipo-1 (bare-metal) — es decir, un software que se instala **directamente** sobre el hardware físico de un servidor y permite ejecutar **máquinas virtuales (VMs)**. ESXi es la pieza central de la plataforma VMware vSphere y es el software que en producción hace posible correr múltiples servidores virtuales aislados sobre un mismo host físico.

A continuación tienes una explicación completa: arquitectura, componentes, ejemplos prácticos (instalación básica, comandos útiles, crear VMs) y buenas prácticas.

### 1. Concepto y arquitectura (alto nivel)

- **Tipo-1 / bare-metal:** ESXi se instala sobre el servidor físico (no sobre otro sistema operativo). Eso reduce la superficie de ataque y mejora rendimiento.
- **Stack básico:**

  - **VMkernel** — núcleo del hypervisor: gestión de CPU, memoria, I/O, planificación de VMs, drivers de red/almacenamiento.
  - **Host management agents** (hostd, vpxa cuando está registrado en vCenter) — gestionan la conexión con el cliente/vCenter y las operaciones de VM.
  - **DCUI (Direct Console User Interface)** — consola local para configurar IP de gestión, contraseña root, etc.
  - **vSphere Host Client** — interfaz web integrada en cada host (accedes por [https://IP-del-host/](https://IP-del-host/) y administras VMs si no tienes vCenter).
  - **vCenter Server** (opcional pero habitual) — gestiona varios hosts ESXi, habilita vMotion, DRS, HA, vSAN, etc.

### 2. ¿Para qué se usa ESXi?

- Consolidar servidores físicos en VMs.
- Ejecutar cargas de producción (bases de datos, aplicaciones, web).
- Laboratorios y pruebas con entorno muy parecido a datacenter.
- Integración con herramientas de backup/DR/monitoring.

### 3. Requisitos y compatibilidad

- **Hardware:** CPU con soporte de virtualización (Intel VT-x/AMD-V) recomendado; drivers y controladoras deben ser compatibles.
- **Comprobar HCL:** en producción se valida en la **VMware Hardware Compatibility Guide (HCL)** (marca, modelo, controladora iSCSI/RAID, NICs).
- Tamaño mínimo disco/RAM varía según versión, pero para lab basta con un servidor con 8–16 GB RAM y disco de varios decenas de GB; en producción se requiere mucho más.

### 4. Instalación (pasos generales — ejemplo práctico)

1. **Descargar ISO** de ESXi y crear un USB booteable (Rufus, dd).
2. **Boot** del servidor desde el USB.
3. **Instalar** ESXi: seleccionar disco, aceptar licencia, establecer contraseña `root`.
4. **Reiniciar** y entrar a **DCUI** (consola local) para configurar: IP management (vmk0), máscara, gateway, DNS y NTP.
5. Acceder vía **[https://IP-de-gestión/](https://IP-de-gestión/)** al **Host Client** para gestión gráfica.

> Ejemplo de configurar IP estática desde la consola remota (DCUI) o usando `esxcli` si tienes shell/SSH:

```bash
# Establecer IPv4 estática en vmk0 (ejemplo)
esxcli network ip interface ipv4 set -i vmk0 -I 192.168.1.50 -N 255.255.255.0 -t static -g 192.168.1.1
```

### 5. Gestión básica: Host Client vs vCenter

- **Host Client ([https://host-ip](https://host-ip))** — administrar un único host: crear VM, gestionar datastore, redes virtuales (vSwitch), snapshots básicos. Ideal para entornos pequeños o administración local.
- **vCenter Server** — agrega gestión centralizada: clústeres, **vMotion** (migrar VMs en caliente), **DRS**, **HA**, **vSAN**, políticas de placement, permisos y backup integrados a nivel de datacenter.

**Nota:** muchas funcionalidades avanzadas (vMotion, DRS, HA, vSAN) requieren vCenter y licencias apropiadas.

### 6. Comandos útiles en el host ESXi (ejemplos reales)

> Para ejecutar estos necesitas acceso a ESXi Shell o SSH (activa SSH sólo cuando sea necesario).

Listar VMs registradas:

```bash
vim-cmd vmsvc/getallvms
```

Encender / apagar una VM (usando su VMID):

```bash
vim-cmd vmsvc/power.on <vmid>
vim-cmd vmsvc/power.off <vmid>
```

Listar adaptadores de red físicos:

```bash
esxcli network nic list
```

Ver discos y datastores montados:

```bash
esxcli storage filesystem list
```

Crear un disco virtual (VMDK) del tamaño 20 GB, thin provision:

```bash
vmkfstools -c 20G -d thin /vmfs/volumes/datastore1/miVM/miVM.vmdk
```

Iniciar/Parar el servicio SSH:

```bash
esxcli system services start --service ssh
esxcli system services stop  --service ssh
# Listar servicios
esxcli system services list | grep ssh
```

Ver logs en tiempo real (ejemplo):

```bash
tail -f /var/log/vmkernel.log
tail -f /var/log/hostd.log
```

Hacer backup de la configuración del host (comando que lanza un job que deja archivo en /scratch/downloads o devuelve URL):

```bash
vim-cmd hostsvc/firmware/backup_config
```

### 7. Crear una máquina virtual (ejemplo práctico — Host Client)

1. Accede a `https://ESXi-IP/` con credenciales root.
2. **Storage → Datastore Browser** → sube el ISO del sistema operativo (ej. `ubuntu.iso`).
3. **Create / Register VM → Create new virtual machine**:

   - Nombre: `web01`
   - Guest OS: Linux / Ubuntu 64-bit
   - CPU: 2 vCPU, Memoria: 4096 MB
   - Disco: crear VMDK 40 GB (thin) en `datastore1`
   - Networking: conectar a `VM Network` (vSwitch por defecto)

4. Conecta la ISO al CD/DVD y **Power On**.
5. Abre la consola web del Host Client, instala el OS como en una máquina física.
6. Instala **VMware Tools / Open VM Tools** dentro del guest (mejor integración y drivers).

### 8. Almacenamiento y redes (resumen rápido)

- **Datastores:** VMFS (block storage), NFS, vSAN.
- **Redes virtuales:**

  - **vSwitch estándar (vSS)**: configuración por host.
  - **vSwitch distribuido (vDS)**: administrado desde vCenter y consistente entre hosts (recomendado en clústeres).

- **Buenas prácticas:** separar tráfico de management, vMotion, storage (iSCSI/NFS) y VM traffic en VLANs y distintas NICs físicas.

### 9. Funcionalidades avanzadas (cuando hay vCenter)

- **vMotion** — mover VMs en ejecución entre hosts (sin downtime).
- **Storage vMotion** — migrar disco virtual entre datastores en línea.
- **DRS** — equilibrar cargas de CPU/memoria entre hosts de un clúster.
- **HA** — reinicia VMs automáticamente si un host falla.
- **vSAN** — storage definido por software que usa los discos locales de los hosts del clúster para crear un datastore distribuido.

### 10. Licencias y limitaciones

- ESXi puede usarse en una edición gratuita (con registro) para entornos pequeños/lab; muchas APIs y capacidades empresariales (gestión a escala, automatización, backups integrados, vMotion) requieren licencias pagas y vCenter.
- En producción revisa la edición y features necesarios (vSphere Standard/Enterprise).

### 11. Seguridad y buenas prácticas

- **Habilita y configura NTP y DNS** correctamente (es crítico para autenticación y vCenter).
- **Desactiva SSH** cuando no lo uses; administra accesos con roles y least privilege.
- **Lockdown mode** (en entornos con vCenter) para limitar acceso directo al host.
- **Parches**: mantener ESXi actualizado y compatible con hardware.
- **Seguridad de red**: separar management network, usar firewall y restrictivas reglas de acceso al puerto management (por ejemplo, limitar a IPs de administración).
- **Secure Boot / TPM**: si el hardware lo soporta, activar Secure Boot para proteger la integridad del hypervisor.

### 12. Problemas comunes y cómo detectarlos (ejemplos)

- **Host sin acceso web (Host Client)** → revisar `hostd` con logs `/var/log/hostd.log`.
- **VM tiene problemas de IO** → revisar latencia del datastore y salud de la controladora (esxcli storage core stats).
- **vMotion falla** → comprobar versiones de ESXi, compatibilidad de CPU (EVC) y conectividad vmkernel para vMotion.
- **Almacenamiento no visible** → comprobar `esxcli storage core adapter list` y chequear multipath.

### 13. Ejemplo de flujo práctico: crear disco y arrancar VM vía CLI

1. Crear carpeta para VM en datastore:

```bash
mkdir /vmfs/volumes/datastore1/miVM
```

2. Crear disco VMDK:

```bash
vmkfstools -c 40G -d thin /vmfs/volumes/datastore1/miVM/miVM.vmdk
```

3. (Suele hacerse la creación de VM desde Host Client). Para ver VMs y encender:

```bash
vim-cmd vmsvc/getallvms
# identifica ID, luego:
vim-cmd vmsvc/power.on <vmid>
```

### 14. Resumen / cuándo usar ESXi

- Usa **ESXi** cuando necesites un hypervisor robusto, probado y eficiente para producción.
- ESXi es ideal en datacenters, entornos corporativos y para cargas que requieren alta disponibilidad y herramientas empresariales (cuando se acompaña de vCenter).
- Para labs y pruebas, ESXi en hardware pequeño (o nested ESXi sobre Workstation) es una buena forma de aprender la plataforma VMware.

---

[🔼](#índice)

---

## **151. ¿Qué es VMWare ESXi?**

### 🔹 1. ¿Qué es VMware ESXi?

- **VMware ESXi** es un **hipervisor de tipo 1** (_bare-metal_) desarrollado por **VMware**.
- Un **hipervisor** es un software que permite crear y gestionar **máquinas virtuales (VMs)**.
- Al ser de **tipo 1**, ESXi **se instala directamente sobre el hardware físico** (no sobre un sistema operativo), lo que le da:

  - **Alto rendimiento**
  - **Menos sobrecarga** (no hay un sistema operativo intermedio)
  - **Mayor seguridad y estabilidad**

👉 Piensa en ESXi como una capa que convierte un solo servidor físico en **varios servidores virtuales** que funcionan de manera independiente.

**Ejemplo:**

En un servidor físico Dell con 64 GB RAM y 8 CPU:

- Puedes instalar ESXi.
- Crear 5 VMs (un servidor web, un servidor de base de datos, un servidor de correo, etc.).
- Cada VM tendrá su propio sistema operativo como si fuera una máquina independiente.

### 🔹 2. ¿Cómo funciona VMware ESXi?

El funcionamiento se basa en varias capas:

1. **VMkernel (núcleo de ESXi):**

   - Es el corazón del hipervisor.
   - Administra **CPU, memoria, almacenamiento y red**.
   - Asigna recursos a cada máquina virtual de forma eficiente.

2. **Máquinas virtuales (VMs):**

   - Cada VM tiene su **propio sistema operativo invitado** (Windows, Linux, etc.).
   - ESXi las aísla unas de otras: si una VM falla, no afecta a las demás.

3. **Interfaces de administración:**

   - **DCUI (Direct Console User Interface):** configuración básica desde consola local.
   - **VMware Host Client (interfaz web):** gestión gráfica accediendo a `https://IP-del-ESXi`.
   - **vCenter Server (opcional):** gestión avanzada de varios hosts ESXi, alta disponibilidad, vMotion, clústeres, etc.

### 🔹 3. Principales características de VMware ESXi

- ✅ **Instalación ligera:** ocupa menos de 200 MB, se instala en minutos.
- ✅ **Alta eficiencia:** mínimo consumo de recursos.
- ✅ **Aislamiento seguro:** cada VM está aislada, evitando que un fallo afecte a las demás.
- ✅ **Compatibilidad amplia:** soporta Windows, Linux, BSD y otros sistemas operativos.
- ✅ **Gestión remota:** interfaz web integrada y compatibilidad con vCenter.
- ✅ **Snapshots:** permite capturar el estado de una VM para restaurarla en caso de error.
- ✅ **Thin provisioning:** discos virtuales que ocupan espacio solo cuando se usa.
- ✅ **Redes virtuales:** switches virtuales (vSwitch) para conectar VMs entre sí o a la red física.
- ✅ **Datastores:** almacenamiento centralizado para alojar las VMs (VMFS, NFS, iSCSI).
- ✅ **Alta disponibilidad (con vCenter):** reinicia VMs en otro host si uno falla.
- ✅ **vMotion (con vCenter):** migración en caliente de VMs entre hosts sin apagarlas.

### 🔹 4. Requisitos de VMware ESXi

Los requisitos dependen de la versión, pero en general:

#### 🖥️ Requisitos de hardware mínimos

- **CPU:** procesador de 64 bits con soporte de **Intel VT-x** o **AMD-V**.
- **Memoria RAM:** mínimo **4 GB** (recomendado 8 GB o más).
- **Almacenamiento:** al menos **1 disco de 32 GB** para la instalación.
- **Tarjeta de red:** al menos una NIC compatible (1 Gbps o 10 Gbps).
- **Compatibilidad de hardware:** verificar en la **VMware HCL** (Hardware Compatibility List).

#### 💽 Requisitos de software

- Acceso al **ISO de ESXi** (descargado desde la web de VMware).
- Cliente de gestión:

  - Navegador web (para Host Client).
  - vSphere Client o vCenter (opcional para entornos grandes).

#### 📌 Ejemplo de servidor compatible:

- **Servidor físico HP ProLiant DL380** con:

  - 2 CPUs Intel Xeon
  - 128 GB de RAM
  - 4 discos de 1 TB en RAID
  - 4 tarjetas de red de 1 Gbps
    → Este equipo puede correr decenas de VMs en paralelo con ESXi.

📌 En resumen:

- **ESXi** es un hipervisor ligero y potente que se instala en el hardware.
- Te permite consolidar varios servidores en uno solo.
- Tiene funciones de red, almacenamiento, snapshots y alta disponibilidad.
- Requiere hardware moderno con virtualización habilitada.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**               | **Siguiente 4**        |
| ------------------ | ------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_2_VirtualBox.md) | [⏩](./6_4_Proxmox.md) |
