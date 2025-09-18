| **Inicio**         | **atrás 5**               | **Siguiente 7**        |
| ------------------ | ------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_5_Hypervisor.md) | [⏩](./6_7_Host_OS.md) |

---

## **Índice**

| Temario                                                                                  |
| ---------------------------------------------------------------------------------------- |
| [156. ¿Qué es un sistema operativo invitado?](#156-qué-es-un-sistema-operativo-invitado) |
| [157. Sistema operativo invitado](#157-sistema-operativo-invitado)                       |

# **GuestOS**

## **156. ¿Qué es un sistema operativo invitado?**

Un **sistema operativo invitado (Guest OS)** es el sistema operativo que se instala y ejecuta **dentro de una máquina virtual (VM)**.

Funciona como si estuviera en una computadora física, pero en realidad está usando los recursos virtualizados (CPU, memoria, disco, red) que el **hipervisor** le asigna.

👉 Ejemplo:

- Tienes una laptop con **Windows 11** (SO host).
- Usas VirtualBox para crear una VM.
- Dentro de esa VM instalas **Ubuntu 22.04** → este Ubuntu es el **sistema operativo invitado**.

### 🔹 ¿En qué se diferencia un sistema operativo invitado de un sistema operativo host?

| Característica | **Sistema Operativo Host**                 | **Sistema Operativo Invitado**                                     |
| -------------- | ------------------------------------------ | ------------------------------------------------------------------ |
| Dónde corre    | Directo en el hardware físico              | Dentro de una máquina virtual                                      |
| Función        | Administra el hardware real y aplicaciones | Administra solo la VM y sus apps                                   |
| Ejemplo        | Windows 11 en tu PC                        | Ubuntu dentro de VirtualBox                                        |
| Dependencia    | Independiente                              | Depende del host y del hipervisor                                  |
| Recursos       | Acceso total al hardware físico            | Recursos limitados según configuración (RAM, CPU, disco asignados) |

👉 Ejemplo práctico:

- En un servidor con **VMware ESXi**:

  - El **host** es el hipervisor ESXi.
  - Los **invitados** son VMs con **Windows Server, CentOS, Debian**, etc.

### 🔹 Virtualización y sistema operativo invitado

La **virtualización** permite ejecutar varios **sistemas operativos invitados** en un mismo servidor físico:

- El **hipervisor** crea máquinas virtuales.
- Cada VM puede tener su propio **SO invitado** (Windows, Linux, BSD, etc.).
- Los invitados funcionan como equipos independientes, aunque compartan el mismo hardware físico.

👉 Ejemplo:

Un datacenter con **Proxmox** puede tener:

- VM1 → Windows Server 2022 (para Active Directory).
- VM2 → Debian (para servidor web Apache).
- VM3 → pfSense (para firewall).

Todos son **SO invitados**, gestionados sobre el mismo host.

### 🔹 Ventajas de un sistema operativo invitado

1. **Aislamiento** → Si el invitado falla o se infecta, no afecta al host.
2. **Pruebas seguras** → Ideal para probar software nuevo, sin dañar el SO host.
3. **Compatibilidad** → Puedes ejecutar un SO distinto al host (ejemplo: Linux en Windows).
4. **Snapshots y backups** → Puedes guardar el estado del invitado y restaurarlo en minutos.
5. **Multientorno** → Ejecutar varios sistemas en paralelo en el mismo equipo físico.

👉 Ejemplo:

Un desarrollador usa su laptop con Windows (host) y levanta 2 invitados:

- Ubuntu para programar en Python.
- Kali Linux para prácticas de ciberseguridad.

### 🔹 Instalación de un sistema operativo invitado

El proceso es similar a instalar un SO en una computadora física, pero dentro de una VM:

1. **Crear una máquina virtual** en el hipervisor (VirtualBox, VMware, Proxmox).
2. **Asignar recursos**: CPU, memoria, disco, red.

   - Ejemplo: 2 CPUs, 4 GB RAM, 40 GB disco.

3. **Montar el ISO de instalación** del sistema operativo (ejemplo: Ubuntu ISO).
4. **Arrancar la VM** → el instalador detecta la máquina virtual como si fuera física.
5. **Seguir los pasos del instalador** (idioma, partición, usuario, contraseña).
6. **Instalar drivers o herramientas de integración** (ejemplo: _VMware Tools_, _Guest Additions_ en VirtualBox).

👉 Ejemplo:

En VirtualBox:

- Host = Windows 11.
- VM creada con 2 CPUs y 4 GB RAM.
- Instalación de **Ubuntu 22.04** desde ISO.
- Ahora Ubuntu corre como **invitado** y puedes abrirlo en una ventana.

✅ **En resumen**:

- El **sistema operativo invitado** es el SO dentro de una VM.
- El **host** corre directo sobre hardware, el **invitado** corre sobre recursos virtualizados.
- Permite pruebas, compatibilidad y seguridad sin comprometer el host.
- Se instala igual que en un PC físico, pero dentro de un hipervisor.

---

[🔼](#índice)

---

## **157. Sistema operativo invitado**

### 🔹 Definición de sistema operativo invitado

Un **sistema operativo invitado** es el software que se instala y ejecuta **dentro de una máquina virtual (VM)**, administrado por un **hipervisor** (como VirtualBox, VMware ESXi, Proxmox, Hyper-V).

El invitado se comporta como si estuviera corriendo en un equipo físico, pero en realidad utiliza los recursos **virtualizados** (CPU, memoria, disco, red) que el host o el hipervisor le asignan.

👉 Ejemplo:

- **Host (anfitrión):** Windows 11 en tu laptop.
- **Hipervisor:** VirtualBox.
- **Invitado (Guest OS):** Ubuntu 22.04 corriendo dentro de VirtualBox.

### 🔹 Características técnicas de un sistema operativo invitado

1. **Ejecutado en un entorno virtualizado**

   - Se ejecuta dentro de una **máquina virtual**.
   - El invitado no accede directamente al hardware físico, sino a versiones virtualizadas.
   - Ejemplo: un disco duro físico de 1 TB se reparte en discos virtuales de 100 GB para distintos invitados.

2. **Dependencia del hipervisor**

   - El invitado **no puede funcionar sin el hipervisor** (que administra los recursos y comunicación con el host).
   - Ejemplo: si cierras VirtualBox, Ubuntu (Guest OS) se apaga.

3. **Compatibilidad flexible**

   - Un mismo host puede ejecutar invitados con **SO diferentes**.
   - Ejemplo: en un host Linux puedes tener invitados Windows, BSD o incluso otro Linux distinto.

4. **Asignación de recursos configurable**

   - El administrador puede definir cuánta CPU, memoria RAM, disco y red se asigna al invitado.
   - Ejemplo: una VM con **2 CPUs, 4 GB RAM y 40 GB de disco**.

5. **Aislamiento**

   - El invitado está aislado del host: un fallo, virus o error en el invitado no debería afectar directamente al host.
   - Ejemplo: si tu VM con Kali Linux se infecta, el Windows host se mantiene seguro.

6. **Herramientas de integración**

   - Los invitados pueden instalar complementos (como **VMware Tools** o **Guest Additions** en VirtualBox) para mejorar rendimiento, compartir carpetas, copiar/pegar texto, etc.

7. **Soporte para snapshots y backups**

   - Se pueden crear “instantáneas” del invitado para restaurarlo en un estado anterior.
   - Ejemplo: antes de probar un software en Ubuntu (invitado), tomas un snapshot; si falla, lo restauras en segundos.

8. **Acceso a red virtualizada**

   - El invitado puede conectarse a internet o a una red interna simulada por el hipervisor.
   - Modos comunes: **NAT, Bridge, Red Interna, Host-Only**.
   - Ejemplo: una VM Windows Server invitada que hace de servidor DNS en una red interna con otras VMs.

✅ **Resumen rápido**:

- **Definición:** un SO invitado es el sistema operativo que corre dentro de una VM.
- **Características técnicas:** se ejecuta en un entorno virtualizado, depende del hipervisor, tiene recursos asignados, está aislado, puede usar snapshots, integraciones y configuraciones de red específicas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**               | **Siguiente 7**        |
| ------------------ | ------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./6_5_Hypervisor.md) | [⏩](./6_7_Host_OS.md) |
