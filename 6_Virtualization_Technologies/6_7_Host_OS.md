| **Inicio**         | **atrás 6**            |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./6_6_GuestOS.md) |

---

## **Índice**

| Temario                                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------- |
| [158. Definición del sistema operativo host](#158-definición-del-sistema-operativo-host)                                                 |
| [159. Sistema operativo anfitrión versus sistema operativo invitado](#159-sistema-operativo-anfitrión-versus-sistema-operativo-invitado) |

# **Host OS**

## **158. Definición del sistema operativo host**

### 🔹 Definición del sistema operativo host

El **sistema operativo host (anfitrión)** es el **sistema operativo principal instalado directamente en el hardware físico** de un computador o servidor.

Es el encargado de administrar **todos los recursos físicos reales** (CPU, memoria RAM, disco duro, tarjeta de red, etc.) y de proveer la base para ejecutar un **hipervisor** o software de virtualización (como VirtualBox, VMware Workstation, Hyper-V).

👉 Ejemplo:

- Tu laptop tiene **Windows 11** instalado → ese es el **host**.
- Desde Windows instalas VirtualBox → y dentro corres Ubuntu como máquina virtual (Ubuntu sería el **invitado**).

### 🔹 Qué hace un sistema operativo host

El SO host cumple varias funciones clave:

1. **Administra el hardware físico**

   - Controla CPU, memoria, disco, GPU, dispositivos USB, etc.
   - Ejemplo: si tu host tiene 16 GB de RAM, decide cuánto asignar al sistema mismo y cuánto puede ceder a las máquinas virtuales.

2. **Ejecuta el hipervisor o software de virtualización**

   - El host actúa como plataforma para instalar VirtualBox, VMware o Hyper-V.
   - Estos, a su vez, crean las máquinas virtuales donde correrán los SO invitados.

3. **Asigna recursos a los invitados**

   - El host “presta” parte de sus recursos a cada invitado.
   - Ejemplo: de 8 núcleos de CPU, puedes asignar 2 a Ubuntu y 4 a Windows Server (invitados).

4. **Gestiona la seguridad y aislamiento**

   - El host controla que las VMs no accedan directamente al hardware físico sin permisos.
   - Esto mantiene al sistema principal seguro frente a posibles fallos o malware en los invitados.

5. **Facilita la conectividad de red**

   - El host crea redes virtuales para que los invitados puedan comunicarse entre ellos o salir a internet.
   - Ejemplo: desde tu host Windows, configuras VirtualBox para que Ubuntu (invitado) navegue por la red como si fuera otro equipo conectado al router.

6. **Proporciona herramientas de integración**

   - Copiar/pegar entre host e invitado, carpetas compartidas, dispositivos USB redirigidos, etc.

✅ **Resumen rápido:**

- El **SO host** es el que corre directamente en el hardware físico.
- Su trabajo es **administrar los recursos reales** y permitir que se ejecuten máquinas virtuales con sus respectivos **SO invitados**.

---

[🔼](#índice)

---

## **159. Sistema operativo anfitrión versus sistema operativo invitado**

### 🔹 ¿Qué es el sistema operativo host?

El **sistema operativo host (anfitrión)** es el que está instalado **directamente en el hardware físico** del equipo o servidor.

Es la base sobre la cual se instalan los programas de virtualización (**hipervisores de tipo 2** como VirtualBox, VMware Workstation o Hyper-V).

👉 Ejemplo:

- Tienes una **PC con Windows 11** → ese es el **host**.
- En ese Windows instalas VirtualBox para correr otras máquinas virtuales.

### 🔹 ¿Qué es un sistema operativo invitado?

El **sistema operativo invitado (Guest OS)** es el que se instala y ejecuta **dentro de una máquina virtual (VM)**, usando los recursos que le asigna el host a través del hipervisor.

👉 Ejemplo:

- Tu host es **Windows 11**.
- Creas una máquina virtual en VirtualBox y dentro instalas **Ubuntu 22.04** → Ubuntu es el **invitado**.

### 🔹 Ventajas de un sistema operativo invitado

Un invitado tiene múltiples beneficios:

1. **Aislamiento**

   - Si el invitado falla o se infecta con malware, el host no se ve afectado.
   - Ejemplo: pruebas un virus en un Ubuntu invitado, pero tu Windows host sigue intacto.

2. **Flexibilidad**

   - Puedes ejecutar varios sistemas operativos en un solo equipo físico.
   - Ejemplo: usar Windows para trabajo diario y Linux invitado para programación o ciberseguridad.

3. **Entorno de pruebas seguro**

   - Perfecto para estudiantes, desarrolladores y sysadmins que necesitan probar software sin arriesgar el host.

4. **Snapshots (instantáneas)**

   - Puedes congelar un estado del invitado y restaurarlo cuando quieras.
   - Ejemplo: tomas un snapshot antes de instalar un programa; si falla, restauras la VM y todo vuelve al estado anterior.

5. **Movilidad y portabilidad**

   - Puedes exportar la VM y ejecutarla en otro host.
   - Ejemplo: llevar tu Ubuntu configurado en una memoria USB e importarlo en otra computadora.

### 🔹 Cómo hacer copias de seguridad y recuperar sistemas operativos virtuales

Existen varias formas de proteger un **sistema operativo invitado**:

#### 1. **Snapshots (instantáneas)**

- Guardan el estado exacto de la VM en un momento dado.
- Restaurar es tan simple como un clic.
- Ejemplo: antes de actualizar Ubuntu invitado, haces un snapshot; si algo falla, vuelves atrás.

#### 2. **Exportar/Importar la VM**

- La mayoría de hipervisores permiten **exportar una VM** a un archivo `.ova` o `.ovf`.
- Luego puedes **importarla en otro host**.
- Ejemplo: exportas una VM con Windows Server desde tu PC y la importas en otra máquina en la oficina.

#### 3. **Copiar manualmente los archivos de la VM**

- Una VM es básicamente un conjunto de archivos en el disco (ej: `.vdi` en VirtualBox, `.vmdk` en VMware).
- Copiando esos archivos a un disco externo ya tienes una copia de seguridad.

#### 4. **Backup con software especializado**

- Herramientas como **Veeam Backup**, **Nakivo** o incluso soluciones nativas en Proxmox/VMware ESXi.
- Ejemplo: programar un backup automático cada noche de todas las VMs de un servidor.

#### 5. **Restauración**

- Si la VM falla, puedes restaurar desde snapshot, importar el archivo exportado o usar el backup programado.

✅ **Resumen Final:**

- El **host** es el sistema que corre directamente en el hardware.
- El **invitado** es el SO que corre dentro de una máquina virtual.
- Los invitados tienen ventajas como aislamiento, flexibilidad, pruebas seguras y snapshots.
- Para respaldarlos puedes usar snapshots, exportar/ importar, copiar archivos o usar software de backup.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**            |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./6_6_GuestOS.md) |
