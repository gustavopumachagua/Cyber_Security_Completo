| **Inicio**         | **atrás 4**            | **Siguiente 6**        |
| ------------------ | ---------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_4_Proxmox.md) | [⏩](./6_6_GuestOS.md) |

---

## **Índice**

| Temario                                                  |
| -------------------------------------------------------- |
| [155. ¿Qué es un hipervisor?](#155-qué-es-un-hipervisor) |

# **Hypervisor**

## **155. ¿Qué es un hipervisor?**

### 🔹 ¿Qué es un hipervisor?

Un **hipervisor** es un software, firmware o hardware que permite **ejecutar múltiples máquinas virtuales (VMs)** sobre un mismo servidor físico.
Cada VM se comporta como un computador independiente (con su propio sistema operativo, memoria, CPU y disco virtual), pero en realidad comparten los recursos del host físico.

👉 Ejemplo:

- En un servidor físico con **64 GB RAM y 16 CPUs**, un hipervisor puede levantar:

  - VM1: 16 GB RAM + 4 CPUs (Windows Server)
  - VM2: 8 GB RAM + 2 CPUs (Ubuntu Web Server)
  - VM3: 4 GB RAM + 1 CPU (Firewall pfSense)

El hipervisor gestiona que todas estas VMs convivan sin interferirse.

### 🔹 ¿Cómo funciona un hipervisor?

1. **Reserva recursos físicos** del host (CPU, RAM, disco, red).
2. **Asigna recursos virtuales** a cada VM según la configuración.
3. **Traduce las instrucciones** de las VMs hacia el hardware real mediante técnicas como:

   - Virtualización completa (KVM, VMware ESXi).
   - Paravirtualización (Xen, VirtIO drivers).

4. **Aísla las VMs**: si una VM falla o se infecta, no necesariamente afecta a las demás.

👉 Ejemplo visual:

- Una instrucción de la VM "sumar números en CPU" no va directo al procesador, pasa primero por el hipervisor, que decide cómo y cuándo darle tiempo de CPU.

### 🔹 Tipos de hipervisores

Existen dos categorías principales:

#### 1. **Hipervisor Tipo 1 (bare-metal)**

- Se instala **directamente sobre el hardware**, sin necesidad de un sistema operativo anfitrión.
- Más eficiente, usado en producción.
- Ejemplos: **VMware ESXi**, **Microsoft Hyper-V (bare-metal)**, **XenServer**, **KVM (integrado en Linux)**.

👉 Ejemplo:

Un datacenter corre ESXi en cada servidor físico, sobre el cual ejecutan cientos de VMs empresariales.

#### 2. **Hipervisor Tipo 2 (hosted)**

- Se ejecuta **sobre un sistema operativo** (Windows, Linux, macOS).
- Más fácil de usar, pero menos eficiente.
- Ejemplos: **VirtualBox**, **VMware Workstation**, **Parallels Desktop**.

👉 Ejemplo:

Un estudiante instala VirtualBox en Windows 11 para probar Linux Ubuntu como VM.

### 🔹 Contenedores vs. Máquinas Virtuales

Aunque los contenedores y las VMs permiten aislar aplicaciones, funcionan de manera distinta:

| Característica        | Máquinas Virtuales (VMs)                                 | Contenedores                                |
| --------------------- | -------------------------------------------------------- | ------------------------------------------- |
| **Virtualización**    | A nivel de hardware                                      | A nivel de sistema operativo                |
| **Sistema operativo** | Cada VM tiene su propio SO completo                      | Comparten el kernel del host                |
| **Peso**              | Pesadas (GBs)                                            | Ligeras (MBs)                               |
| **Arranque**          | Lento (segundos-minutos)                                 | Muy rápido (milisegundos)                   |
| **Casos de uso**      | Bases de datos, apps críticas, multi-SO (Windows, Linux) | Microservicios, despliegues rápidos, DevOps |

👉 Ejemplo:

- Una **VM** puede correr Windows Server para Active Directory.
- Un **contenedor** puede correr solo un microservicio Node.js sobre Linux, mucho más rápido y ligero.

### 🔹 Consideraciones de seguridad del hipervisor

El hipervisor es un **punto crítico** en la infraestructura, ya que controla todo el hardware compartido.
Aspectos clave de seguridad:

1. **Aislamiento fuerte:** que una VM comprometida no pueda escapar al host.
2. **Parcheo frecuente:** vulnerabilidades en hipervisores como ESXi o KVM pueden permitir ataques de _VM escape_.
3. **Gestión de accesos:** aplicar control de roles (RBAC), autenticación multifactor y segmentación de redes.
4. **Backups y snapshots seguros:** evitar corrupción o malware persistente.
5. **Monitoreo del tráfico entre VMs:** inspección de tráfico este-oeste (dentro del host).

👉 Ejemplo:

Un atacante explota una vulnerabilidad en VMware ESXi (_CVE-2021-21972_) y logra ejecutar código en el hipervisor → compromete todas las VMs. Por eso es vital aplicar parches de seguridad.

### 🔹 ¿Por qué elegir **Red Hat** para la virtualización?

**Red Hat Virtualization (RHV)** y su sucesor **Red Hat OpenShift Virtualization** son soluciones empresariales basadas en **KVM (Kernel-based Virtual Machine)**.

Ventajas de Red Hat:

1. **Open Source** pero con **soporte empresarial** (certificaciones, SLA).
2. **Integración con OpenShift** → puedes correr **contenedores + VMs** en el mismo clúster.
3. **Escalabilidad:** soporta grandes datacenters y clouds híbridas.
4. **Seguridad:** SELinux, aislamiento fuerte, parches rápidos.
5. **Ecosistema:** compatible con Ceph, Ansible, OpenStack.

👉 Ejemplo:

Una empresa en banca puede usar Red Hat Virtualization para correr sus VMs críticas (bases de datos, sistemas internos) mientras que OpenShift gestiona sus microservicios en contenedores, todo bajo soporte de Red Hat.

✅ **En resumen**:

- El hipervisor es la pieza central de la virtualización.
- Tipo 1 = más eficiente (producción). Tipo 2 = más simple (labs, pruebas).
- Contenedores son más ligeros que VMs, pero no reemplazan todos los casos.
- La seguridad del hipervisor es crítica: si cae el host, caen todas las VMs.
- Red Hat es elección sólida cuando se busca soporte, seguridad y ecosistema híbrido (VMs + contenedores).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**            | **Siguiente 6**        |
| ------------------ | ---------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_4_Proxmox.md) | [⏩](./6_6_GuestOS.md) |
