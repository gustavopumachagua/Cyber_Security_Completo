| **Inicio**         | **atrás 21**        | **Siguiente 23**     |
| ------------------ | ------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_21_NTP.md) | [⏩](./4_23_Star.md) |

---

## **Índice**

| Temario                                                          |
| ---------------------------------------------------------------- |
| [122. ¿Qué es IPAM?](#122-qué-es-ipam)                           |
| [123. Gestión de direcciones IP](#123-gestión-de-direcciones-ip) |

# **IPAM**

## **122. ¿Qué es IPAM?**

![IPAM](/img/4_IP_Terminology/IPAM.png "IPAM")

**IPAM (IP Address Management / Gestión de Direcciones IP)** es el **conjunto de procesos y herramientas** para **planificar, asignar, registrar y auditar** direcciones **IP**, subredes, **DNS** y **DHCP** dentro de una red.

Suele formar parte de “**DDI**”: **D**NS + **D**HCP + **I**PAM.

> Objetivo: que **nunca** haya direcciones duplicadas, perdidas o sin dueño, y que sepas **quién** usa **qué IP**, **dónde** y **cuándo**.

### ⚙️ ¿Qué problemas resuelve IPAM?

- **Conflictos de IP** (dos equipos con la misma IP).
- **“Spreadsheet sprawl”**: IPs en Excel desactualizados.
- **Falta de trazabilidad**: no sabes quién tomó una IP ni cuándo.
- **Crecimiento y cambios** (IPv6, nubes, VLANs, VRF, SD-WAN).
- **Cumplimiento y auditoría** (registros, retención, cambios).

📌 **Ejemplo**: en una oficina con 120 PCs, el técnico asigna manualmente IPs. A los 3 meses aparecen choques de IP y printers caídas. Con IPAM, el pool está controlado, hay reservas y registro histórico.

### 🧩 Componentes y funciones clave

1. **Inventario centralizado**

   - Listado vivo de **subredes** (CIDR), **pools**, **IPs libres/en uso**, etiquetas, dueño, ubicación.
   - Soporta **IPv4 e IPv6**.

2. **Planificación de direcciones (Address Planning)**

   - Diseño jerárquico por **sitio / edificio / piso / VLAN / VRF**.
   - Previene solapamientos y fragmentación.

3. **Asignación y reservas**

   - **Flujo de solicitud-aprobación** (Service Desk): “necesito 5 IPs para cámaras del almacén”.
   - IPAM **reserva** y documenta: nombre del dispositivo, propósito, fecha, quién aprobó.

4. **Descubrimiento y reconciliación**

   - **Escaneo** (ICMP/ARP/SNMP/API) para detectar IPs activas y **reconciliar** con lo registrado.
   - Detecta **rogue DHCP** y dispositivos no autorizados.

5. **Integración DDI (DNS/DHCP)**

   - Alta/baja de **registros DNS** (A, PTR) y **reservas DHCP** **automáticamente** cuando asignas una IP.
   - Evita “dar IP sin DNS” o viceversa.

6. **Automatización y APIs**

   - Entrega de IPs en **DevOps/CI-CD**, **VMware**, **Kubernetes**, **nubes (AWS/Azure/GCP)** vía API/webhooks.
   - “Entrega de red” tan ágil como la de cómputo.

7. **Políticas, roles y auditoría**

   - **RBAC** (roles por equipo/red/sitio).
   - **Log** de cambios: quién, qué, cuándo.
   - Etiquetas para **activos críticos**, **PCI**, **OT**, **IoT**.

8. **Reportes y alertas**

   - **Utilización** por subred, tasas de crecimiento, **agotamiento** previsto.
   - Alertas por **conflictos**, **subred al 80%**, **nuevos dispositivos no inventariados**.

### 🗺️ Cómo se ve un plan de direccionamiento con IPAM

```
Empresa (10.0.0.0/8)
 ├─ Perú / Lima (10.10.0.0/16)
 │   ├─ Sede San Isidro (10.10.10.0/24)  VLAN 10  Usuarios
 │   ├─ Sede San Isidro (10.10.20.0/24)  VLAN 20  VoIP
 │   └─ Sede San Isidro (10.10.30.0/24)  VLAN 30  IoT/CCTV
 └─ Chile / Santiago (10.20.0.0/16)
     └─ Campus Las Condes (10.20.10.0/24) VLAN 10
```

- Cada subred con **propósito, dueño, puerta de enlace, DNS, DHCP, etiquetas**.
- IPAM impide crear otra 10.10.10.0/24 en el mismo VRF o te avisa si en otro VRF **sí** puede existir (superposición controlada).

### 🔌 IPAM vs. DHCP vs. DNS (DDI)

- **DHCP**: **asigna** dinámicamente IPs/puerta/DNS a clientes.
- **DNS**: traduce **nombres ↔ IPs**.
- **IPAM**: **planifica y registra** todo el espacio de direcciones; orquesta y automatiza **DHCP/DNS**.

📌 **Ejemplo**: creas el servidor `files-lima`

1. IPAM te da 10.10.10.25,
2. crea **A**/PTR en DNS,
3. añade **reserva DHCP** (o estática documentada),
4. deja rastro en auditoría.

### 🛠️ Casos de uso (con ejemplos)

- **Oficina mediana**: pools por VLAN; reservas para impresoras y APs; reporte de **ocupación** y **capacidad**.
- **Fábrica/OT**: segmentación y **etiquetas de riesgo**; bloqueo de asignaciones fuera de ventanas de mantenimiento.
- **Nube híbrida**: coordinar CIDR de **VPC/VNet** para evitar solapes con on-prem; aprovisionar subredes por pipeline.
- **Kubernetes**: controlar rangos **Pod/Service CIDR** por cluster; evitar colisiones con la red corporativa.
- **Multi-tenant/VRF**: permitir **CIDR superpuestos** (10.0.0.0/24 en VRF-ClienteA y VRF-ClienteB) sin conflictos.

### 🧮 Métricas que debes mirar

- **Utilización por subred** (IPs en uso/libres).
- **Tiempo a agotamiento** (proyección).
- **Tasa de crecimiento** por sitio/aplicación.
- **Conflictos detectados** y **rogue devices**.
- **Cumplimiento de naming** (DNS) y reservas.

### 🔐 Seguridad y cumplimiento

- **Menos superficie de ataque**: sabes qué IPs existen y cuáles **no deberían** existir.
- **Rastreo forense**: IP ↔ dispositivo ↔ usuario ↔ tiempo.
- **Zero Trust de red**: IPAM como fuente de verdad para políticas (NAC/Firewall).
- **Auditoría**: cambios firmados con usuario/fecha; exportes para SOX/PCI/ISO.

### 🧭 Buenas prácticas rápidas

1. **Diseña jerárquicamente** (sitio/función/VLAN/VRF).
2. **Reserva** puertas de enlace, HSRP/VRRP, servidores, impresoras, infraestructura.
3. **Etiqueta todo** (dueño, criticidad, ambiente: prod/dev/test).
4. **Automatiza** altas/bajas con **API** y CI-CD (evita trabajo manual).
5. **Escanea y reconcilia** periódicamente.
6. **Planea IPv6** (prefijos, SLAAC/DHCPv6, DNS).
7. **Integra DDI** (un solo flujo: IP + DNS + DHCP + registro).

### 🧰 Herramientas típicas

- **Empresariales**: (ej.) Infoblox, BlueCat, EfficientIP.
- **Open source**: **phpIPAM**, NetBox (CMDB + IPAM).
- **Cloud**: **AWS IPAM**, Azure IPAM (vía Azure IPAM/Address Prefixes en VNets y herramientas), GCP con su gestión de rangos.
  _(Mencionadas a modo ilustrativo; la elección depende de tus requisitos y presupuesto.)_

### 🎯 Mini-escenario de extremo a extremo

> **“Necesito 3 IPs para cámaras nuevas en Almacén Lima.”**

1. Ticket llega a IPAM → selecciona subred **10.10.30.0/24 (IoT/CCTV)**.
2. IPAM verifica capacidad, **reserva** .41-.43, crea entradas **DNS** y, si aplica, reservas **DHCP**.
3. Etiqueta: `IoT`, `CCTV`, `Lima`, `Almacén`; dueño: Seguridad Física.
4. Envía **instrucciones** al técnico (gateway, máscara, DNS).
5. Queda **auditoría** para compliance y troubleshooting futuro.

### 🧠 En una frase

**IPAM es tu “libro mayor” de direcciones IP**: evita choques, acelera provisión, integra DNS/DHCP, da trazabilidad y prepara tu red para **nube, IoT y IPv6**.

---

[🔼](#índice)

---

## **123. Gestión de direcciones IP**

### 🌐 Gestión de direcciones IP (IPAM) en Windows Server

En **Windows Server**, **IPAM** es una **característica de rol** que se puede instalar desde el **Administrador del servidor**.
Sirve para **descubrir, monitorear, auditar y administrar** de forma centralizada:

- Direcciones **IPv4** e **IPv6**.
- **Servidores DHCP** (y sus ámbitos, reservas, leases).
- **Servidores DNS** (zonas, registros).
- Políticas de direcciones.
- Auditoría de eventos de red.

📌 Ejemplo:

Si tienes **3 sedes** cada una con su propio servidor DHCP/DNS, con **IPAM** puedes ver todos los ámbitos y direcciones **en un solo panel**, evitar solapamientos y auditar quién obtuvo qué IP.

### 🆕 Novedades en IPAM

Con cada versión de **Windows Server (2012, 2016, 2019, 2022)** se agregaron mejoras:

1. **Soporte para administración multinivel**

   - Integración con **Active Directory** para delegar permisos.
   - RBAC (Control de Acceso Basado en Roles).

2. **Descubrimiento automático mejorado**

   - Encuentra servidores **DHCP, DNS y DCs** en el dominio sin tener que configurarlos uno a uno.

3. **Integración con System Center y PowerShell**

   - Automatización con **cmdlets** para aprovisionar subredes, reservas o zonas DNS.

4. **Compatibilidad con IPv6**

   - Administración completa de prefijos IPv6 y seguimiento de direcciones.

5. **Auditoría centralizada**

   - Registro de **cambios de configuración**, **concesiones DHCP**, y **consultas DNS**.

6. **Soporte para entornos híbridos** (Windows Server 2019 y 2022)

   - Integración con Azure para manejar direcciones y sincronización de DNS/DHCP.

### ⚙️ Administrar IPAM

Existen **tres formas principales** de usar IPAM:

1. **Consola gráfica (GUI)**

   - Desde el **Administrador del servidor** → Herramientas → **IPAM**.
   - Vista centralizada de **subredes, ámbitos, reservas y direcciones libres/ocupadas**.

2. **PowerShell (cmdlets IPAM)**

   - Control avanzado, scripting y automatización.
   - Útil en **entornos grandes** o cuando necesitas integración con procesos DevOps.

3. **API/Integración**

   - Algunas operaciones se pueden integrar con **System Center**, **Azure**, o scripts personalizados.

📌 Ejemplo:

Si la empresa abre una **nueva sucursal**, puedes usar IPAM para:

- Crear un **ámbito DHCP 192.168.50.0/24**.
- Reservar direcciones para impresoras y APs.
- Configurar automáticamente los registros **DNS** asociados.

### 💻 Cmdlets del servidor de administración de direcciones IP (IPAM) en PowerShell

Windows Server trae un conjunto de **cmdlets específicos** para **gestionar IPAM**.

Algunos de los más usados son:

#### 🔎 Consulta y obtención de información

- `Get-IpamAddress` → Lista direcciones IP en el inventario.
- `Get-IpamSubnet` → Obtiene subredes registradas.
- `Get-IpamBlock` → Consulta bloques de direcciones.
- `Get-IpamRange` → Lista rangos definidos.
- `Get-IpamConfiguration` → Muestra configuración de IPAM.

#### ➕ Creación / registro

- `Add-IpamAddress` → Añade una dirección IP al inventario.
- `Add-IpamSubnet` → Crea una nueva subred.
- `Add-IpamRange` → Define un rango de direcciones dentro de una subred.
- `Add-IpamBlock` → Crea un bloque de subredes para organización.

#### ✏️ Actualización

- `Set-IpamAddress` → Modifica propiedades de una IP registrada.
- `Set-IpamSubnet` → Edita configuración de una subred.

#### ❌ Eliminación

- `Remove-IpamAddress` → Elimina una IP del inventario.
- `Remove-IpamSubnet` → Borra una subred del registro.

#### 📝 Ejemplo en acción

```powershell
# Crear una nueva subred
Add-IpamSubnet -AddressFamily IPv4 -NetworkAddress "192.168.100.0" -PrefixLength 24 -Name "Sucursal-Lima"

# Agregar una IP reservada dentro de esa subred
Add-IpamAddress -IpAddress "192.168.100.10" -MacAddress "00-1A-2B-3C-4D-5E" -Name "Impresora-Lima" -Description "Reserva impresora oficina Lima"

# Consultar las IPs registradas en esa subred
Get-IpamAddress -Subnet "192.168.100.0/24"
```

### 📌 En resumen

- **IPAM en Windows Server** = herramienta integrada para administrar DHCP, DNS y direcciones IP en una sola consola.
- Sus **novedades** incluyen mejor descubrimiento, auditoría, integración con la nube y soporte completo para IPv6.
- Puedes **administrarlo** vía GUI o PowerShell.
- Los **cmdlets** de IPAM permiten automatizar todo el ciclo de vida: crear subredes, reservar IPs, eliminar registros y auditar cambios.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 21**        | **Siguiente 23**     |
| ------------------ | ------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_21_NTP.md) | [⏩](./4_23_Star.md) |
