| **Inicio**         | **atrás 5**               | **Siguiente 7**                     |
| ------------------ | ------------------------- | ----------------------------------- |
| [🏠](../README.md) | [⏪](./3_5_SSL_vs_TLS.md) | [⏩](./3_7_Basics_of_Subnetting.md) |

---

## **Índice**

| Temario                                                                                        |
| ---------------------------------------------------------------------------------------------- |
| [71. NAS vs SAN: ¿Cuáles son las diferencias?](#71-nas-vs-san-cuáles-son-las-diferencias)      |
| [72. Que es un NAS](#72-que-es-un-nas)                                                         |
| [73. ¿Qué es una red de área de almacenamiento?](#73-qué-es-una-red-de-área-de-almacenamiento) |

---

# **Conceptos básicos de NAS y SAN**

## **71. NAS vs SAN: ¿Cuáles son las diferencias?**

![NAS y SAN](/img/3_Networking_Knowledge/NAS_vs_SAN.jpg "NAS y SAN")

### ¿Qué es NAS (Network Attached Storage)?

- **Definición**:

  NAS es un **dispositivo de almacenamiento conectado a una red IP (Ethernet)** que permite a varios usuarios y clientes acceder a archivos de forma centralizada.
  Se comporta como un **servidor de archivos dedicado**, generalmente gestionado por un sistema operativo ligero optimizado para compartir datos.

- **Características principales**:

  - Usa **protocolos de nivel de aplicación** para compartir archivos:

    - **NFS** (Linux/Unix)
    - **SMB/CIFS** (Windows)
    - **FTP/HTTP** (otros)

  - Fácil de instalar y administrar (plug & play).
  - Ideal para pequeñas y medianas empresas o entornos domésticos.

- **Ejemplo**:

  - Una empresa compra un **Synology NAS**.
  - Los empleados guardan documentos en `\\NAS-SERVIDOR\RecursosHumanos`.
  - Todos acceden mediante **SMB** desde Windows o **NFS** desde Linux.

### ¿Qué es una SAN (Storage Area Network)?

- **Definición**:

  SAN es una **red dedicada de alta velocidad** que conecta servidores a un **pool centralizado de almacenamiento en bloque**.
  En vez de archivos, entrega discos virtuales a los servidores, como si fueran discos duros locales.

- **Características principales**:

  - Usa protocolos de **bloques de datos**:

    - **Fibre Channel (FC)** (muy común en datacenters).
    - **iSCSI** (sobre IP, más económico).
    - **FCoE** (Fibre Channel over Ethernet).

  - Ofrece **altísimo rendimiento, baja latencia y escalabilidad**.
  - Usado en **bases de datos críticas, aplicaciones financieras o virtualización masiva**.

- **Ejemplo**:

  - Un banco implementa una **SAN Fibre Channel** para su base de datos Oracle.
  - El servidor de base de datos cree que el almacenamiento proviene de discos locales, pero en realidad son LUNs (Logical Unit Numbers) expuestos desde la SAN.

### 📊 Principales diferencias entre NAS y SAN

| Característica           | NAS 🗂️ (Network Attached Storage)         | SAN 💽 (Storage Area Network)               |
| ------------------------ | ----------------------------------------- | ------------------------------------------- |
| **Tipo de acceso**       | A nivel de **archivos**                   | A nivel de **bloques**                      |
| **Protocolos**           | SMB/CIFS, NFS, FTP, HTTP                  | Fibre Channel, iSCSI, FCoE                  |
| **Red**                  | IP/Ethernet                               | Red dedicada de almacenamiento (FC o iSCSI) |
| **Complejidad**          | Fácil de implementar                      | Complejo (requiere switches, HBAs, zoning)  |
| **Costo**                | Bajo a medio                              | Medio a alto                                |
| **Escalabilidad**        | Limitada (para archivos y usuarios)       | Muy alta (para cargas críticas)             |
| **Rendimiento**          | Bueno, pero dependiente de la red IP      | Muy alto, baja latencia                     |
| **Casos de uso típicos** | Compartir documentos, backups, multimedia | Bases de datos, virtualización, ERP, SAP    |

### ✅ Elegir la solución adecuada

La elección depende de **necesidades, presupuesto y uso**:

1. **Elige NAS si…**

   - Necesitas un sistema **simple y económico**.
   - Quieres compartir archivos en red local o remota.
   - Ejemplos:

     - Oficina pequeña que comparte documentos y hace respaldos.
     - Hogar con películas y fotos centralizadas en un servidor.

2. **Elige SAN si…**

   - Necesitas **altísimo rendimiento** y **baja latencia**.
   - Tienes aplicaciones críticas: **bases de datos, virtualización, Big Data**.
   - Ejemplos:

     - Un banco que ejecuta millones de transacciones.
     - Un hospital con sistemas de imágenes médicas (radiografías, resonancias).

3. **Alternativa híbrida**:

   - Muchas empresas usan **ambos**:

     - **NAS** para archivos y colaboración.
     - **SAN** para aplicaciones críticas.

   - Algunos proveedores (Dell EMC, NetApp, HPE) ofrecen **unified storage** que combina NAS + SAN en una sola plataforma.

👉 En conclusión:

- **NAS = simple, archivos, costo bajo**.
- **SAN = complejo, bloques, máximo rendimiento**.

---

[🔼](#índice)

---

## **72. Que es un NAS**

### ¿Definición simple?

**NAS (Network Attached Storage)** es un **dispositivo de almacenamiento conectado a la red IP** que ofrece **acceso a ficheros** a múltiples usuarios y equipos mediante protocolos de red (SMB, NFS, FTP, etc.).
En vez de montar un disco en cada equipo, el NAS actúa como **servidor de archivos centralizado**.

### Componentes y arquitectura (qué hay dentro de un NAS)

- **Hardware**: CPU (ARM o x86), memoria RAM, bahías para discos (HDD/SSD), controles RAID, puertos Ethernet, a veces puertos USB y ranuras PCIe en modelos empresariales.
- **Sistema operativo/firmware**: un OS ligero tipo appliance (ej.: Synology DSM, QTS de QNAP, o firmware propietario). Ofrece GUI web para gestión.
- **Pool de almacenamiento**: discos agrupados en RAID, LVM o tecnologías propietarias (p. ej. SHR).
- **Servicios**: servidor SMB/NFS/iSCSI, servidor FTP/SFTP, servidor multimedia (Plex), backup, snapshots, control de usuarios/LDAP/AD, sincronización cloud.
- **Red**: normalmente se conecta por Ethernet (1GbE, 2.5/10GbE en modelos altos). Soporta LACP (link aggregation) en muchos modelos.

### ¿Cómo acceden los equipos a un NAS? (protocolos comunes)

- **SMB/CIFS** — Windows y muchos NAS (compartición de archivos, impresión en red).
- **NFS** — Linux/Unix; también usado para datastores en virtualización.
- **iSCSI** — acceso a **bloques** (el servidor ve un LUN como disco local). Útil cuando necesitas almacenamiento de bloque (bases de datos, VM datastores).
- **FTP / SFTP / WebDAV** — para transferencia de ficheros y accesos remotos.
- **AFP** (legacy) — antiguamente para Time Machine; hoy en día macOS usa SMB o soporte nativo para Time Machine en muchos NAS.
- **HTTP/HTTPS** — GUI de administración y servicios web.

### Casos de uso (ejemplos prácticos)

1. **Oficina pequeña — servidor de archivos**

   - Carpeta compartida `\\NAS\Compartido` accesible por todos.
   - Control de accesos por usuario/grupo (Active Directory o cuentas locales).

2. **Backup centralizado**

   - Backups automatizados de PCs/servidores al NAS.
   - Snapshots diarios para recuperación rápida de ficheros.

3. **Servidor multimedia (home)**

   - Instalas Plex o Jellyfin en el NAS y todos los dispositivos de la casa consumen películas/series.

4. **Repositorio para máquinas virtuales**

   - Exportas NFS datastores o iSCSI LUNs para VMware/Hyper-V (en empresas, con 10GbE o FC para rendimiento).

5. **Sincronización con la nube**

   - El NAS sincroniza backups o archivos con Google Drive, S3 o Dropbox usando integraciones nativas.

### Ejemplos reales: comandos para montar y usar (solo en tus sistemas)

**Montar un recurso SMB en Linux (ejemplo seguro especificando versión SMB):**

```bash
sudo mkdir -p /mnt/nas_share
sudo mount -t cifs //192.168.1.100/Docs /mnt/nas_share \
  -o username=juan,uid=1000,gid=1000,vers=3.0
```

> Nota: `vers=3.0` evita usar SMBv1 (inseguro).

**Montar NFS en Linux:**

```bash
sudo mkdir -p /mnt/nfs
sudo mount -t nfs 192.168.1.100:/export/proyectos /mnt/nfs
```

**Mapear unidad en Windows (CMD / PowerShell):**

```powershell
# PowerShell
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\192.168.1.100\Docs" -Persist -Credential (Get-Credential)

# CMD (legacy)
net use Z: \\192.168.1.100\Docs /user:juan contraseña
```

**Conectar en macOS (Finder):** `Ir -> Conectar al servidor` → `smb://192.168.1.100/Docs`
(o desde terminal: `mount_smbfs //juan@192.168.1.100/Docs /Volumes/Docs`)

**iSCSI (cliente) — flujo conceptual:**

- En el NAS configuras un **Target** (LUN).
- En el servidor Windows/Linux configuras un **Initiator** y conectas al target.
- El servidor ve el LUN como disco local (`/dev/sdX` o Disco en Windows) y lo particionas/formateas.

### NAS vs disco externo / vs SAN (breve aclaración)

- **NAS** → acceso **file-level** por red (varios clientes concurrentes). Ideal para compartir archivos.
- **Disco externo** → conectado directamente a un equipo via USB; no sirve para acceso simultáneo desde varios equipos.
- **SAN** → acceso **block-level** (iSCSI/FC); pensado para aplicaciones de alto rendimiento (bases de datos, grandes entornos virtuales).

### Ventajas y limitaciones del NAS

**Ventajas**

- Acceso centralizado y multiusuario.
- Gestión sencilla (GUI), snapshots y backups integrados.
- Soporte de protocolos múltiples (SMB, NFS, FTP, iSCSI).
- Buena relación costo/uso para PYMEs y uso doméstico.
- Funciones adicionales: Plex, Docker, sincronización cloud, vigilancia (NVR).

**Limitaciones**

- Rendimiento limitado por la red y controladora RAID; para cargas intensivas puede necesitar 10GbE o SAN.
- Riesgo si no se configura seguridad adecuada (SMB expuesto, passwords débiles).
- RAID **NO** es sustituto de backup: protege de fallo de disco, no de eliminación accidental o ransomware.

### Buenas prácticas (seguridad y operación)

- **Red**: coloca el NAS en VLAN separada para usuarios/servicios; no lo expongas directamente a Internet.
- **Acceso**: integrar con Active Directory/LDAP para control centralizado.
- **Cifrado**: cifrado de datos en reposo si el NAS lo soporta; usar TLS/HTTPS para la administración.
- **SMB**: obliga SMBv2/v3; deshabilita SMBv1. Habilita SMB signing si lo requieres.
- **Backups**: aplica regla **3-2-1** (3 copias, 2 medios, 1 offsite). Usa snapshots + backups fuera del NAS.
- **Power**: proteger NAS con UPS para evitar corrupción durante cortes.
- **Parches**: mantener firmware actualizado.
- **Monitoreo**: alertas SMART, uso de disco, logs y reportes de integridad.
- **Autenticación**: cuentas con contraseñas fuertes, 2FA para interfaces de gestión.
- **Ransomware**: conservar backups inmutables / offline y restringir permisos de escritura.

### Cómo elegir un NAS (criterios de compra)

- **Número de bahías** (2,4,8+): cuántos discos quieres instalar y crecimiento futura.
- **Soporte RAID y tipos** (RAID1,5,6,10, SHR).
- **CPU / RAM**: multimedia (transcodificación) o VMs necesitan CPU potente y más RAM.
- **Conectividad**: 1GbE vs 2.5/10GbE; puertos SFP+/RJ45 según infraestructura.
- **Funciones**: snapshots, deduplicación, integración cloud, Docker, apps.
- **Ecosistema y soporte**: facilidad de uso del OS (Synology DSM y QNAP QTS son populares).
- **Presupuesto**: modelos domésticos vs modelos empresariales (más caros pero con redundancia/soporte).

### Mini-escenario práctico (pauta rápida)

Oficina de 10 personas que quiere centralizar archivos y backups:

1. Comprar NAS 4 bahías, 2.5GbE o 1GbE con LACP.
2. Instalar 4 discos en RAID5 (o RAID10 si priorizas rendimiento).
3. Crear shares SMB: `\\oficina\nominas` y `\\oficina\proyectos`.
4. Integrar a Active Directory para permisos.
5. Habilitar snapshots diarios y backup semanal a la nube.
6. Configurar alertas por correo y un UPS.

### Conclusión

Un **NAS** es la solución ideal cuando necesitas **compartir archivos de forma centralizada, gestionable y asequible**. Escala desde hogares (mediateca, backups) hasta PYMEs (servidor de archivos, backups, pequeñas VMs). Para cargas de misión crítica y alto rendimiento, evalúa SAN o NAS con conectividad de alta velocidad (10GbE) y buenas prácticas de red/storage.

---

[🔼](#índice)

---

## **73. ¿Qué es una red de área de almacenamiento?**

### Definición corta

Una **SAN (Storage Area Network)** es una **red dedicada** que conecta servidores (hosts) con un almacenamiento centralizado a **nivel de bloques**. En una SAN el servidor ve volúmenes (LUNs) como discos locales; el sistema operativo gestiona esos bloques (particiones, sistemas de archivos o bases de datos).

### Componentes principales

- **Hosts/Servidores**: ejecutan apps (VMs, bases de datos) y usan HBAs (Fibre Channel) o NICs (iSCSI/NVMe-oF).
- **Storage array / Controladora**: el arreglo físico (discos, controladoras, cache) que presenta LUNs.
- **Switches de almacenamiento (fabric)**: switches Fibre Channel o Ethernet (para iSCSI/NVMe-oF sobre RDMA).
- **Protocolos**: Fibre Channel (FC), iSCSI, FCoE, NVMe over Fabrics (NVMe-oF).
- **Software de gestión**: administración del array (crear RAID, LUNs, snapshots, replicación).
- **Multipathing/MPIO**: software en el host para usar múltiples rutas redundantes a un LUN.

### ¿Cómo funciona (resumen conceptual)?

1. En el array creas un **LUN** (volumen de bloques) y lo **mappeas** a un host (o a un grupo de hosts).
2. En el host descubres el LUN (vía FC WWN o iSCSI IQN).
3. El host forma particiones y sistemas de archivos sobre el dispositivo de bloque (`/dev/sdX` o disco en Windows).
4. El host accede y lee/escribe bloques directamente; el array gestiona la integridad y optimización del almacenamiento.

### Protocolos SAN importantes

- **Fibre Channel (FC)**: red dedicada basada en SAN switches; baja latencia y alto rendimiento. Muy usado en datacenters tradicionales.
- **iSCSI**: encapsula SCSI sobre TCP/IP — permite SAN sobre Ethernet (con 10GbE/25/40/100GbE recomendado para rendimiento).
- **FCoE (Fibre Channel over Ethernet)**: FC frames sobre Ethernet convergente (requirements de switch especial).
- **NVMe over Fabrics (NVMe-oF)**: protocolo moderno para acceso a dispositivos NVMe a través de redes (RoCE, FC-NVMe), optimizado para baja latencia y altas IOPS.

### Conceptos clave dentro de una SAN

- **LUN (Logical Unit Number)**: volumen de bloque expuesto por el array al host.
- **Zoning (FC)**: aislamiento a nivel de switch (qué WWNs pueden verse entre sí).
- **LUN masking**: control en el array sobre qué WWN/IQN puede acceder a qué LUN.
- **Multipathing / MPIO**: múltiples rutas físicas entre host y array para tolerancia a fallos y rendimiento.
- **RAID / RAID groups**: protecciones internas del array (RAID 1/5/6/10, etc.).
- **Snapshots / Replicación**: servicios de protección y DR (sincronía/asincronía).

### Topologías típicas (ejemplos)

- **SAN FC clásico (datacenter)**:

  `Servidor (HBA WWN) --[FC]--> FC Switch --[FC]--> Storage Array`

  - Normalmente **2 fabrics** (dual fabric) por alta disponibilidad.

- **SAN iSCSI sobre 10GbE**:

  `Servidor (NIC) --[10GbE Switch (VLAN iSCSI)]--> Storage Array`

  - Se recomienda red dedicada o VLAN separada, jumbo frames y MPIO.

### Casos de uso / ejemplos reales

- **Bases de datos OLTP (Oracle, SQL Server)**: requieren IOPS altos y baja latencia → SAN (FC o NVMe-oF).
- **Virtualización a gran escala (VMware vSphere)**: datastores en SAN (VMFS sobre LUNs iSCSI/FC).
- **Sistemas ERP, transaccionales o almacenamiento compartido para clusters**.
- **Entornos que necesitan snapshots rápidos y replicación sin afectar producción**.

### Ejemplo práctico — **Conectar un LUN iSCSI desde Linux (pasos y comandos)**

> **Advertencia**: ejecuta esto únicamente en tus entornos/hosts bajo tu control.

1. Instala cliente iSCSI (Debian/Ubuntu):

```bash
sudo apt update
sudo apt install open-iscsi
```

2. Habilita y arranca el servicio:

```bash
sudo systemctl enable --now iscsid
```

3. Descubrir targets en el array (reemplaza `10.0.0.5` por la IP del target iSCSI):

```bash
sudo iscsiadm -m discovery -t sendtargets -p 10.0.0.5
# salida: iqn.2020-01.com.vendor:array.target1 10.0.0.5:3260
```

4. Iniciar sesión / login al target:

```bash
sudo iscsiadm -m node -T iqn.2020-01.com.vendor:array.target1 -p 10.0.0.5 -l
```

5. Ver nuevos dispositivos (ej.: `/dev/sdb`):

```bash
lsblk
dmesg | tail -n 20
```

6. (Opcional) Crear partición y formatear:

```bash
sudo parted /dev/sdb mklabel gpt mkpart primary 0% 100%
sudo mkfs.xfs -f /dev/sdb1       # o mkfs.ext4
sudo mkdir -p /mnt/san_lun
sudo mount /dev/sdb1 /mnt/san_lun
```

7. Multipathing (si hay múltiples rutas): instala `multipath-tools` y revisa:

```bash
sudo apt install multipath-tools
sudo systemctl enable --now multipathd
sudo multipath -ll
```

### Ejemplo práctico — **Conectar un LUN iSCSI desde Windows (pasos/síntesis)**

1. Abrir **iSCSI Initiator** (o PowerShell).
2. Agregar Target Portal (IP del array).
3. Discover Targets → Conectar (Connect).
4. En **Disk Management** inicializar disco, crear partición y formatear NTFS/ ReFS.

PowerShell mínimo:

```powershell
# Agregar portal
New-IscsiTargetPortal -TargetPortalAddress "10.0.0.5"
# Buscar targets
Get-IscsiTarget
# Conectar al target
Connect-IscsiTarget -NodeAddress "iqn.2020-01.com.vendor:array.target1"
# Revisar sesiones
Get-IscsiSession
```

### Administración típica del SAN (flujo)

1. Planificación: RAID, performance tiering, RPO/RTO.
2. Crear RAID group/volúmenes en el array.
3. Crear LUNs y snapshots según política.
4. Mapear LUN al host (por WWN/IQN).
5. Configurar zoning (si FC) y MPIO en hosts.
6. Monitorizar latencia, IOPS, throughput, health del array.
7. Backup/replicación a DR.

### Rendimiento: qué medir y optimizar

- **IOPS** (operaciones por segundo) — importante para workloads OLTP.
- **Throughput** (MB/s) — importante para backups, ingest de datos.
- **Latencia (ms)** — para bases de datos, objetivo sub-ms en FC/NVMe.
- **Block size / IO size** — usar tamaños adecuados (p. ej. DBs 8K/16K/64K alineados).
- **Queue depth** — ajustar en HBA/NIC y en host para maximizar utilización sin saturar.

### Servicios avanzados que ofrecen los arrays SAN

- **Snapshots** (instantáneos) y clones.
- **Thin provisioning** (aprovisionamiento fino).
- **Deduplicación / Compresión**.
- **Replicación síncrona / asíncrona** entre sitios.
- **Encryption at rest** (hardware).
- **Quality of Service (QoS)** para priorizar workloads.

### Seguridad y buenas prácticas

- **Red separada** o VLAN dedicada para tráfico SAN (evitar tráfico de usuario mezclado).
- **Zoning (FC)** y **LUN masking** para limitar acceso.
- **CHAP** y credenciales para iSCSI.
- **MPIO / caminos redundantes** para tolerancia.
- **Parches de firmware** en array y HBAs.
- **Monitorización y alertas** para latencia y errores SCSI.
- **Backups offline** y pruebas periódicas de recuperación.
- **Registrar y auditar** quien hizo cambios (control de cambios).

### Ventajas y desventajas (resumen)

- **Ventajas**

  - Rendimiento alto y baja latencia (FC/NVMe-oF).
  - Escalabilidad y servicios avanzados (snapshots, replicación).
  - Ideal para apps críticas (DB, virtualización a gran escala).

- **Desventajas**

  - Mayor complejidad y coste (hardware, switching, HBAs).
  - Requiere personal especializado.
  - Más planificación de red y seguridad.

### ¿Cuándo elegir SAN vs NAS? (resumen práctico)

- **Usa SAN** si necesitas: acceso a **bloques**, altísimo rendimiento, soporte a bases de datos/VMs críticas y features empresariales (replicación).
- **Usa NAS** si necesitas: compartir **archivos** (documentos, backups, multimedia), simplicidad y menor coste.

### Checklist rápido antes de desplegar una SAN

- ¿Cuál es el workload (IOPS / throughput / latencia)?
- ¿Qué RPO/RTO necesitas (replicación síncrona/asincrónica)?
- ¿Red dedicada y redundante disponible? (dual fabric recomendado)
- ¿Plan de backup y snapshots?
- ¿Seguridad: zoning, masking, CHAP, VLANs?
- ¿Política de firmware/patch y monitoreo?
- ¿Planes de escalado: más LUNs, discos, controladoras?

### Conclusión

Una **SAN** es la solución de almacenamiento corporativa cuando la aplicación exige **bloques**, altos IOPS y baja latencia con servicios empresariales (snapshots, replicación, QoS). Se compone de arrays, fabric (switches), HBAs/NICs y software de multipathing — y puede implementarse con FC, iSCSI o NVMe-oF según requisitos y presupuesto.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**               | **Siguiente 7**                     |
| ------------------ | ------------------------- | ----------------------------------- |
| [🏠](../README.md) | [⏪](./3_5_SSL_vs_TLS.md) | [⏩](./3_7_Basics_of_Subnetting.md) |
