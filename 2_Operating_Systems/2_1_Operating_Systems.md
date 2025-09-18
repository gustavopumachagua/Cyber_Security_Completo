| **Inicio**         | **Siguiente 2**        |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏩](./2_2_Windows.md) |

---

## **Índice**

| Temario                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------ |
| [32. ¿Qué es un sistema operativo?](#32-qué-es-un-sistema-operativo)                                                     |
| [33. 8 tipos diferentes de sistemas operativos con ejemplos](#33-8-tipos-diferentes-de-sistemas-operativos-con-ejemplos) |
| [34. ¿Qué es un sistema operativo lo más rápido posible?](#34-qué-es-un-sistema-operativo-lo-más-rápido-posible)         |

---

# **Sistemas operativos**

## **32. ¿Qué es un sistema operativo?**

![Sistemas operativos](/img/1_Fundamental_IT_Skills/Sistemas_operativos.jpg "Sistemas operativos")

### 1. Funciones principales del sistema operativo (qué hace)

- **Gestión de procesos**: crear, planificar (schedule), pausar, reanudar y terminar procesos y hilos.

  _Ejemplo:_ cuando abres Chrome, el SO crea procesos/hilos para cada pestaña y los ejecuta en la CPU.

- **Gestión de memoria**: asigna memoria (RAM) a procesos, gestiona memoria virtual (swap/paginación) y protecciones.

  _Ejemplo:_ si abres muchas apps, el SO mueve páginas de memoria al disco (swap) para liberar RAM.

- **Gestión de almacenamiento y sistemas de archivos**: organiza archivos y carpetas, controla acceso y persistencia en discos.

  _Ejemplo:_ NTFS (Windows) o ext4 (Linux) guardan tus documentos y permisos.

- **Gestión de dispositivos (drivers)**: controla impresoras, tarjetas de red, pantallas, discos mediante controladores.

  _Ejemplo:_ el driver de la tarjeta gráfica gestiona la aceleración 3D.

- **Interfaz de usuario**: proporciona CLI (terminal) y/o GUI (escritorio) para que el usuario interactúe.

  _Ejemplo:_ PowerShell / Terminal (CLI) y Windows Desktop o GNOME (GUI).

- **Seguridad y control de acceso**: autentica usuarios, aplica permisos, políticas y aislamiento.

  _Ejemplo:_ cuentas de usuario, contraseñas, ACLs, SELinux en Linux.

- **Servicios y utilidades**: logging, timers, sincronización entre procesos, networking, impresión, gestión de energía.

### 2. Componentes internos (qué hay “dentro” de un SO)

- **Núcleo o kernel**: corazón del SO; maneja CPU, memoria, I/O y seguridad.
- **Shell / interfaz**: traduce comandos del usuario (bash, PowerShell).
- **Gestores (sub-sistemas)**: scheduler (planificador), gestor de memoria, gestor de archivos, gestor de red.
- **Drivers**: módulos que traduce llamadas del SO a instrucciones del hardware.
- **Bibliotecas y APIs**: funciones que las aplicaciones usan para pedir servicios al SO (por ejemplo, POSIX, Win32).

### 3. Kernel: tipos y diferencias (breve)

- **Monolítico**: muchas funcionalidades dentro del kernel (ej.: Linux clásico). Rápido pero más complejo.
- **Microkernel**: kernel mínimo; servicios (sistema de archivos, red) corren en espacio de usuario (ej.: QNX, Minix). Más modular y seguro, pero con overhead de comunicación.
- **Híbrido**: mezcla de ambos (ej.: Windows NT).

### 4. Procesos, hilos y concurrencia (con ejemplos)

- **Proceso**: instancia en ejecución de un programa (espacio de direcciones propio).
- **Hilo (thread)**: unidad de ejecución dentro de un proceso; comparte memoria con otros hilos del mismo proceso.
- **Context switch**: el SO guarda/restaura el estado de la CPU para cambiar entre procesos/hilos.
- **Sincronización**: semáforos, mutexes, variables de condición para coordinar acceso a recursos compartidos.

  _Ejemplo práctico:_ un servidor web usa múltiples hilos para atender conexiones simultáneas; un mutex protege el acceso a un contador compartido.

### 5. Planificación (scheduling) — cómo se reparte la CPU

- **Round Robin**: cada proceso recibe un “quantum” de tiempo en orden circular. Bueno para interactividad.
- **Prioridad**: procesos con mayor prioridad se ejecutan primero; puede causar _starvation_ si no se gestiona.
- **FIFO / SJF / Multilevel queues**: variantes usadas según objetivos (throughput, latencia, fairness).

  _Ejemplo:_ en un desktop, el SO da más prioridad a la interfaz para que las ventanas respondan con rapidez.

### 6. Memoria: paginación, virtualización y protección

- **Memoria virtual**: permite que cada proceso piense que tiene un espacio de direcciones continuo; el SO y MMU traducen direcciones virtual → físicas.
- **Paginación**: divide memoria en páginas; mueve páginas entre RAM y disco (swap).
- **Protección**: el SO evita que un proceso acceda a la memoria de otro (aislamiento).

  _Ejemplo:_ si un proceso intenta leer memoria ajena, el SO genera una excepción (segfault) y lo termina.

### 7. Sistemas de archivos y organización de datos

- **Estructura**: árboles de directorios, inodos/metadatos, journaling para recuperar fallos.
- **Permisos**: POSIX (rwx) en Unix/Linux; ACLs en Windows.
- **Ejemplos de FS:** FAT/NTFS (Windows), ext4/Btrfs/XFS (Linux), APFS (macOS).

  _Ejemplo práctico:_ `ls -la` muestra permisos; `chmod` los cambia en Linux.

### 8. Dispositivos, controladores e interrupciones

- **Drivers**: traducen llamadas del SO a operaciones de hardware. Pueden ser módulos cargables.
- **Interrupciones (IRQ)**: señales del hardware que interrumpen la CPU para atención inmediata (por ejemplo, tecla presionada).

  _Ejemplo:_ al imprimir, el driver gestiona la cola y la impresora notifica con una interrupción cuando acaba una página.

### 9. Llamadas al sistema (system calls) y APIs

- Las aplicaciones usan **system calls** para pedir servicios (crear archivos, leer teclado, crear procesos). Ej.: `open()`, `read()`, `write()` en POSIX; `CreateFile` en Win32.
- El SO valida argumentos, gestiona recursos y devuelve resultados o errores.

  _Ejemplo en C (muy simple):_

```c
int fd = open("archivo.txt", O_RDONLY);
ssize_t n = read(fd, buf, sizeof(buf));
close(fd);
```

### 10. Interfaz de usuario: CLI vs GUI

- **CLI (Command Line Interface)**: potente, scriptable — ejemplo: bash, PowerShell.
- **GUI (Graphical User Interface)**: amigable y visual — ejemplo: Windows Desktop, macOS Finder, GNOME.

  _Ejemplo práctico:_ automatizar backups con un script en la terminal vs usar una app con botones.

### 11. Tipos de sistemas operativos y ejemplos concretos

- **Desktop / Laptop:** Windows 10/11, macOS, varias distribuciones Linux (Ubuntu, Fedora).
- **Servidores:** Windows Server, Linux (CentOS/Alma/RHEL, Ubuntu Server), BSD.
- **Móvil:** Android (Linux-based), iOS.
- **Sistemas en tiempo real (RTOS):** FreeRTOS, VxWorks, QNX — usados en automóviles, robótica, industria.
- **Empotrados / IoT:** sistemas ligeros o personalizados (Zephyr, embedded Linux).
- **Virtualización / hypervisors:** VMware ESXi, Hyper-V, KVM (permiten crear máquinas virtuales).
- **Contenedores:** Docker + kernel Linux — los contenedores comparten kernel pero aíslan procesos con namespaces/cgroups.

### 12. Boot: cómo arranca una máquina (resumen)

1. **POST / Firmware (BIOS/UEFI)** inicializa hardware.
2. **Bootloader** (GRUB, Windows Boot Manager) carga el kernel del SO.
3. **Kernel** inicializa subsistemas y drivers básicos.
4. **Init/systemd** arranca servicios y da paso a la sesión de usuario.

   _Ejemplo:_ en Linux moderno systemd lanza daemons como `networkd`, `sshd`, `cron`.

### 13. Virtualización y contenedores (por qué importan)

- **Máquinas virtuales (VMs)**: emulan hardware completo; cada VM tiene su propio SO. Aisladas y pesadas.
- **Contenedores**: comparten kernel del host; más ligeros y rápidos para despliegue de apps.

  _Ejemplo práctico:_ despliegas microservicios en contenedores Docker y orquestas con Kubernetes.

### 14. Seguridad: modelos y mecanismos

- **Autenticación y autorización**: usuarios, grupos, permisos.
- **Sandboxing / aislamiento**: apps confinadas (AppArmor, SELinux, sandboxing de browsers).
- **Actualizaciones y parches**: mantener kernel y drivers al día es crítico.
- **Logs y auditoría**: analizar eventos para detectar intrusiones (Event Viewer, syslog).

  _Ejemplo:_ forzar MFA, aplicar parches críticos y usar SELinux para limitar daños.

### 15. Comandos y herramientas útiles (ejemplos prácticos)

- **Linux/macOS:** `ls`, `ps aux`, `top`/`htop`, `free -h`, `df -h`, `journalctl`, `systemctl status`.
- **Windows:** Task Manager, `tasklist`, `Get-Process` (PowerShell), `msinfo32`, Event Viewer.

  _Ejemplo rápido:_ `ps aux | grep apache` — ver procesos Apache en Linux.

### 16. Problemas comunes y troubleshooting rápido

- **Arranque lento / no arranca:** revisar logs del bootloader, modo recovery, reparar filesystem.
- **Alta carga CPU:** `top` o Task Manager para identificar procesos; revisar cargas o bucles infinitos.
- **Falta de memoria:** revisar `swap`, cerrar aplicaciones, aumentar RAM.
- **Problemas de drivers:** reinstalar drivers, revisar compatibilidad y actualizaciones.

  _Tip práctico:_ siempre revisa logs (syslog, journalctl, Event Viewer) para diagnosticar.

### 17. ¿Cómo elegir un sistema operativo?

Considera: propósito (desktop vs servidor vs embebido), compatibilidad de hardware, seguridad, soporte, ecosistema de software y coste/licenciamiento.

_Ejemplos:_

- Si eres programador y quieres control, Linux (Ubuntu) es flexible.
- Si necesitas compatibilidad de software comercial (MS Office nativo), Windows o macOS.
- Para dispositivos de IoT con tiempo real, un RTOS apropiado.

### 18. Resumen rápido (cheat-sheet)

- SO = software que gestiona hardware y provee servicios a aplicaciones.
- Funciones clave: procesos, memoria, archivos, dispositivos, seguridad.
- Ejemplos comunes: Windows, macOS, Linux, Android, iOS, FreeRTOS.
- Conceptos clave: kernel, proceso/hilo, scheduling, memoria virtual, system calls, drivers, boot.

---

[🔼](#índice)

---

## **33. 8 tipos diferentes de sistemas operativos con ejemplos**

### 1) Sistemas operativos de **escritorio** (Desktop OS)

**Qué son:** pensados para PCs de usuario final con interfaz gráfica, multitarea y soporte amplio de hardware/periféricos.

**Ejemplos reales:** **Windows 10/11**, **macOS** (Ventura/Monterey),

**Ubuntu Desktop**.

**Características clave:** GUI rica, soporte multimedia, drivers para todo tipo de hardware, gestión de usuarios.

**Caso práctico:** tu laptop en la oficina ejecuta Office, navegador y herramientas de diseño; el SO gestiona ventanas, impresoras y audio.

**Pros/Contras:** muy amigables y con gran ecosistema de aplicaciones; menos adecuados para sistemas embebidos o ultraespecíficos.

### 2) Sistemas operativos de **servidor** (Server OS)

**Qué son:** optimizados para estabilidad, servicios de red, concurrencia y administración remota.

**Ejemplos reales:** **Ubuntu Server**, **Red Hat Enterprise Linux (RHEL)**, **Windows Server**.

**Características clave:** soporte para daemons/servicios (HTTP, DB), gestión remota, tolerancia a fallos, herramientas de monitoreo.

**Caso práctico:** un servidor web con Nginx + PostgreSQL en Ubuntu Server en la nube, gestionado por SSH.

**Pros/Contras:** excelente para cargas continuas y administración centralizada; requieren configuración y hardening para seguridad.

### 3) Sistemas operativos **móviles**

**Qué son:** diseñados para smartphones/tablets: energía eficiente, gestión de sensores, interfaces táctiles y modelos de permisos de apps.

**Ejemplos reales:** **Android**, **iOS**.

**Características clave:** administración de ciclo de vida de apps (suspensión/resume), gestión energética, ecosistemas de apps y tiendas.

**Caso práctico:** tu teléfono usa iOS/Android para llamadas, GPS y apps; el SO limita procesos en background para ahorrar batería.

**Pros/Contras:** muy pulidos para UX táctil y ecosistemas móviles; más restrictivos en control de hardware y sistema que un desktop.

### 4) Sistemas operativos **en tiempo real (RTOS)**

**Qué son:** garantizan respuestas **deterministas** (deadlines estrictos) — usados donde el tiempo de respuesta es crítico.

**Ejemplos reales:** **FreeRTOS**, **VxWorks**, **QNX**, **Zephyr**.

**Características clave:** latencia mínima y predecible, planificación en prioridades, footprint muy pequeño.

**Caso práctico:** controlador de frenos ABS en un coche o control de un brazo robótico industrial: el SO debe responder dentro de milisegundos.

**Pros/Contras:** ideal para sistemas críticos; no están pensados para multitarea general ni interfaces ricas.

### 5) Sistemas operativos **embebidos / IoT**

**Qué son:** adaptados a hardware limitado (memoria/CPU) y tareas concretas; pueden ser RTOS o Linux embebido.

**Ejemplos reales:** **Embedded Linux** (Yocto, Buildroot), **Zephyr**, **RIOT**, **TinyOS**.

**Características clave:** consumo reducido, soporte para drivers particulares, arranque rápido, adaptabilidad.

**Caso práctico:** un sensor ambiental con conectividad LoRa que recoge datos y los envía al gateway; corre un sistema embebido que gestiona sensores y comunicaciones.

**Pros/Contras:** altamente personalizables y ligeros; requieren conocimiento de hardware y a veces carecen de ecosistema de apps general.

### 6) Sistemas operativos **mainframe / de gran escala**

**Qué son:** diseñados para alto throughput, transaccionalidad, disponibilidad y escalabilidad masiva en centros de datos corporativos.

**Ejemplos reales:** **IBM z/OS**, **z/VM**.

**Características clave:** gestión avanzada de I/O, seguridad fuerte, virtualización a gran escala, alta fiabilidad (99.999%).

**Caso práctico:** bancos y compañías aéreas usan z/OS para procesar millones de transacciones críticas por día.

**Pros/Contras:** extremadamente robustos y seguros; caros y con curva de aprendizaje especializada.

### 7) Sistemas **distribuidos / de clúster** (Distributed OS / Orchestration)

**Qué son:** no siempre un "SO monolítico" como tal, sino infra que presenta recursos de muchos nodos como un sistema coherente; hoy se hace con orquestadores.

**Ejemplos/tecnologías:** **Plan 9 / Amoeba** (investigación en OS distribuidos), y en práctica moderna **Kubernetes** (orquestador de contenedores) o **Hadoop YARN** (gestor de recursos).

**Características clave:** abstracción de recursos distribuidos, tolerancia a fallos, scheduling entre nodos, gestión de datos distribuida.

**Caso práctico:** desplegar un servicio microservicios con réplicas en varios nodos usando Kubernetes para balanceo, autoscaling y despliegues sin downtime.

**Pros/Contras:** permiten escalar horizontalmente; la complejidad operativa y el debugging distribuido aumentan mucho.

### 8) **Hypervisors / sistemas de virtualización** (Type 1 y Type 2)

**Qué son:** plataformas que crean y gestionan máquinas virtuales (VMs). Pueden actuar como “SO” que gestiona hardware para múltiples invitados.

**Ejemplos reales:** **Type 1 (bare-metal):** **VMware ESXi**, **Microsoft Hyper-V**, **Xen**, **KVM**. **Type 2 (hosted):** **VirtualBox**, **VMware Workstation**.

**Características clave:** aislamiento total entre VMs, snapshots, migración en caliente (vMotion), overcommit de recursos.

**Caso práctico:** en un datacenter, ESXi ejecuta múltiples VMs (Linux/Windows) para servicios; permite mover VMs entre servidores sin interrupción.

**Pros/Contras:** excelente para consolidación y aislamiento; overhead (Type 2 mayor), y gestión de recursos compartidos añade complejidad.

### Resumen rápido (comparación en 1 línea)

- **Desktop:** Windows/macOS/Ubuntu — experiencia de usuario y aplicaciones.
- **Server:** Ubuntu Server/RHEL/Windows Server — servicios y alta disponibilidad.
- **Mobile:** Android/iOS — táctil, batería y sensores.
- **RTOS:** FreeRTOS/VxWorks — respuestas deterministas.
- **Embedded/IoT:** Embedded Linux/Zephyr — dispositivos limitados.
- **Mainframe:** z/OS — transacciones masivas y fiabilidad.
- **Distribuido/Clúster:** Kubernetes/Plan 9 — recursos en muchos nodos.
- **Hypervisor:** ESXi/Hyper-V/VirtualBox — virtualización y aislamiento.

---

[🔼](#índice)

---

## **34. ¿Qué es un sistema operativo lo más rápido posible?**

Un **sistema operativo (SO)** es el software que actúa de intermediario entre el **hardware** (CPU, memoria, disco, dispositivos) y las **aplicaciones**, gestionando recursos, seguridad y proporcionando servicios básicos para que los programas funcionen.

Puntos clave — qué hace y ejemplo práctico en 1 línea cada uno:

- **Gestión de procesos:** crea/planifica/protege programas en ejecución.

  _Ejemplo:_ al abrir tu navegador, el SO crea procesos y les da tiempo de CPU para que carguen páginas.

- **Gestión de memoria:** asigna RAM y usa memoria virtual/swap.

  _Ejemplo:_ si abres muchas pestañas, el SO mueve páginas inactivas al disco para liberar RAM.

- **Sistema de archivos / almacenamiento:** organiza archivos y carpetas en disco.

  _Ejemplo:_ guardar `documento.docx` en `C:\Users\TuUsuario\Documents`.

- **Controladores (drivers) y gestión I/O:** comunica con impresoras, discos y redes.

  _Ejemplo:_ al imprimir, el driver traduce datos a la impresora y el SO gestiona la cola.

- **Interfaz (CLI/GUI):** permite al usuario interactuar (terminal o escritorio).

  _Ejemplo:_ escribir `ls` en Linux o hacer doble clic en un icono en Windows.

- **Redes y comunicación:** pila TCP/IP y servicios de red.

  _Ejemplo:_ el SO enruta paquetes para que tu app acceda a una página web.

- **Seguridad y permisos:** controla usuarios, acceso a archivos y aislamiento.

  _Ejemplo:_ un usuario sin privilegios no puede instalar drivers del sistema.

- **Servicios y utilidades:** logging, timers, planificación de tareas, actualización.

  _Ejemplo:_ `cron` en Linux o el Programador de tareas en Windows ejecuta backups programados.

Tipos de kernel (muy breve):

- **Monolítico** (p. ej. Linux): muchas funciones en el kernel — rápido.
- **Microkernel** (p. ej. QNX): kernel pequeño + servicios en espacio usuario — más modular.
- **Híbrido** (p. ej. Windows NT): mezcla de ambos.

¿Qué pasa al arrancar?

BIOS/UEFI → bootloader (GRUB/Windows Boot Manager) → carga del kernel → init/systemd → servicios → sesión de usuario.

Ejemplos concretos de uso real:

- **Desktop:** Windows/macOS gestionan Office, multimedia y periféricos.
- **Servidor:** Ubuntu Server o RHEL ejecutan bases de datos y servidores web.
- **Móvil:** Android/iOS manejan llamadas, sensores y apps.
- **RTOS/embebido:** FreeRTOS controla un sensor o un controlador de freno en un coche con respuesta determinista.

¿Por qué importa?

Sin SO, cada aplicación tendría que manejar hardware y concurrencia: sería inmanejable. El SO hace posible usar múltiples apps, aprovechar recursos y mantener seguridad.

Cheat-sheet rápido (comandos útiles):

- Linux: `ps aux` (procesos), `top` (uso CPU/RAM), `df -h` (disk).
- Windows (PowerShell): `Get-Process`, `Get-EventLog -LogName System`, `Get-PSDrive`.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**        |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏩](./2_2_Windows.md) |
