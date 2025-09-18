| **Inicio**         | **atrás 9**       | **Siguiente 11**    |
| ------------------ | ----------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_9_VM.md) | [⏩](./4_11_DNS.md) |

---

## **Índice**

| Temario                                                                                                        |
| -------------------------------------------------------------------------------------------------------------- |
| [94. Protocolo de configuración dinámica de host (DHCP)](#94-protocolo-de-configuración-dinámica-de-host-dhcp) |
| [95. ¿Qué es DHCP y cómo funciona?](#95-qué-es-dhcp-y-cómo-funciona)                                           |

# **DHCP**

## **94. Protocolo de configuración dinámica de host (DHCP)**

![DHCP](/img/4_IP_Terminology/DHCP.jpg "DHCP")

### 🔹 Introducción a DHCP

**DHCP (Dynamic Host Configuration Protocol)** es un protocolo de red que permite asignar automáticamente configuraciones de red a los dispositivos que se conectan a una red IP (computadoras, móviles, impresoras, cámaras IP, etc.).
Entre los parámetros más comunes que asigna están:

- **Dirección IP**
- **Máscara de subred**
- **Puerta de enlace predeterminada (gateway)**
- **Servidor DNS**
- Otros parámetros opcionales (tiempo de concesión, NTP, WINS, etc.)

👉 Sin DHCP, cada dispositivo tendría que configurarse manualmente, lo que es poco práctico en redes grandes.

**Ejemplo práctico:**

Imagina una oficina con 200 PCs. Configurar una IP fija en cada una tomaría horas y sería propenso a errores. Con DHCP, todas reciben sus configuraciones al encenderse y conectarse a la red.

### 🔹 ¿Por qué utilizar DHCP?

1. **Simplificación de la administración de red**

   → Ya no necesitas configurar cada dispositivo manualmente.

2. **Reducción de errores humanos**

   → Evita conflictos de IP (dos dispositivos con la misma dirección).

3. **Flexibilidad y escalabilidad**

   → Ideal para redes dinámicas (oficinas, escuelas, hoteles, WiFi público).

4. **Configuración centralizada**

   → Cambias el DNS o gateway una sola vez en el servidor DHCP y todos los clientes lo reciben al renovar su concesión.

**Ejemplo práctico:**

En una red de hotel, un huésped conecta su laptop y smartphone. Ambos reciben IP automáticamente gracias a DHCP, sin necesidad de configuración manual.

### 🔹 Beneficios del servidor DHCP

- **Automatización total:** asigna IPs y parámetros de red sin intervención manual.
- **Optimización del uso de direcciones IP:** reutiliza direcciones cuando dispositivos se desconectan.
- **Soporte para dispositivos temporales:** perfecto para redes con muchos usuarios “flotantes” (por ejemplo, aeropuertos o cafeterías).
- **Menor mantenimiento:** si cambia la infraestructura (por ejemplo, gateway nuevo), se actualiza desde un único punto.
- **Compatibilidad universal:** casi todos los sistemas operativos y dispositivos lo soportan.

**Ejemplo práctico:**

En una universidad con 3000 alumnos conectando laptops y móviles a diario, DHCP garantiza que siempre haya direcciones disponibles y que nadie tenga que configurar nada manualmente.

### 🔹 Características del servidor DHCP

1. **Asignación dinámica de direcciones IP** (por concesiones temporales).
2. **Asignación manual/reservada:** permite reservar direcciones fijas según la MAC (ej. impresora, servidor).
3. **Asignación automática:** entrega la misma IP a un dispositivo cuando vuelve a conectarse, si está disponible.
4. **Tiempo de concesión configurable:** define cuánto tiempo un cliente mantiene su IP.
5. **Escalabilidad:** soporta desde una red pequeña en un hogar hasta miles de dispositivos en una empresa.
6. **Interoperabilidad:** puede trabajar junto a DNS (para resolución de nombres) y con routers/firewalls.

**Ejemplo práctico:**

Un servidor DHCP en una oficina puede tener estas configuraciones:

- Pool de direcciones: 192.168.1.100 – 192.168.1.200
- Tiempo de concesión: 8 horas
- Gateway: 192.168.1.1
- Servidores DNS: 8.8.8.8 (Google) y 1.1.1.1 (Cloudflare)
- Reservas:

  - 192.168.1.10 para la impresora (MAC fija)
  - 192.168.1.20 para un servidor de archivos

✅ **En resumen:**

DHCP es esencial porque automatiza, simplifica y centraliza la gestión de direcciones IP. Es flexible, escalable y reduce errores, siendo la base de casi todas las redes modernas.

---

[🔼](#índice)

---

## **95. ¿Qué es DHCP y cómo funciona?**

### 1) ¿Qué es DHCP (en pocas palabras)?

**DHCP — Dynamic Host Configuration Protocol** — es el protocolo que **asigna automáticamente parámetros de red** (principalmente direcciones IP, máscara, gateway y DNS) a los dispositivos cuando se conectan a una red. El objetivo es automatizar y centralizar la configuración de red para evitar errores manuales y gestionar millones de dispositivos con facilidad.

### 2) Componentes principales

- **Cliente DHCP**: software en el host que solicita configuración (ordenadores, móviles, impresoras, cámaras).
- **Servidor DHCP**: servicio que gestiona pools de direcciones y opciones, responde con ofertas.
- **Agente retransmisor / DHCP Relay**: reenvía mensajes DHCP entre clientes y servidores que están en subredes distintas (usa `ip helper-address` en Cisco).
- **Base de datos / leases**: fichero o tabla donde el servidor guarda qué IPs están en uso y por quién.

### 3) Puertos y transporte

- DHCP usa **UDP**.

  - **Servidor escucha en puerto 67 (UDP)**.
  - **Cliente escucha en puerto 68 (UDP)**.

- Durante la asignación inicial el cliente suele no tener IP, por eso las solicitudes son broadcast.

### 4) Flujo (4 mensajes básicos — DORA)

El proceso típico de obtención de IP se llama **DORA**:

1. **DHCPDISCOVER** (broadcast) — el cliente pregunta “¿hay algún servidor DHCP?”

   - Origen: `0.0.0.0:68` → Destino: `255.255.255.255:67`

2. **DHCPOFFER** (unicast/broadcast) — el/los servidores responden con una oferta (IP disponible + opciones).
3. **DHCPREQUEST** (broadcast) — el cliente elige una oferta y pide formalmente esa IP (informa a todos los servidores cuál escogió).
4. **DHCPACK** — el servidor confirma la concesión y el cliente puede usar la IP.

   - Si hay problema, el servidor responde con `DHCPNAK` (negación).

Diagrama ASCII simple:

```
Cliente --DHCPDISCOVER--> (broadcast)
Servidor --DHCPOFFER--> (oferta: 192.168.1.120)
Cliente --DHCPREQUEST--> (solicita 192.168.1.120)
Servidor --DHCPACK-->  (ACK: 192.168.1.120 lease 86400)
```

### 5) Ciclo de vida de una concesión (lease) — renovación y tiempos

- El servidor concede una IP por un **tiempo de lease** (p. ej. 86400 s = 1 día).
- **T1 (renewal time)**: habitualmente el 50% del lease. En T1 el cliente intenta renovar directamente con el servidor que le dio la IP (unicast).
- **T2 (rebind time)**: normalmente 87.5% del lease. Si no pudo renovar en T1, en T2 intenta reenviar a cualquier servidor (broadcast) para rebind.
- Si la renovación falla hasta que el lease expira, el cliente deja de usar la IP y vuelve a iniciar DORA.

**Ejemplo numérico:** lease = 86.400 s → T1 = 43.200 s (12 h), T2 = 75.600 s (21 h).

### 6) Tipos de asignación de IP

- **Dinámica**: IPs del pool se asignan por tiempo limitado (lo más común).
- **Automática**: el servidor intenta dar siempre la misma IP al mismo cliente si está disponible (menos usado).
- **Estática / Reserva (Static DHCP / Fixed Address)**: se reserva una IP para una **MAC concreta** — útil para impresoras, servidores o cámaras.

### 7) Opciones DHCP importantes (ejemplos)

Los servidores DHCP pueden entregar muchos parámetros (opciones). Las más comunes:

- **option routers (3)** → puerta de enlace predeterminada (gateway).
- **option domain-name-servers (6)** → servidores DNS.
- **option domain-name (15)** → sufijo DNS.
- **option ntp-servers (42)** → servidor(es) NTP.
- **option 66/67 (PXE)** → TFTP/boot server y nombre de archivo para arranque en red.
- **option 82 (Relay Agent Info)** → info añadida por relay (útil para seguridad/identificación).

### 8) Ejemplos reales de configuración

#### A) **ISC DHCP (Linux) — `dhcpd.conf`** (ejemplo sencillo)

```text
subnet 192.168.1.0 netmask 255.255.255.0 {
  range 192.168.1.100 192.168.1.200;
  option routers 192.168.1.1;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
  option domain-name "miempresa.local";
  default-lease-time 3600;
  max-lease-time 86400;
}

host impresora {
  hardware ethernet 00:11:22:33:44:55;
  fixed-address 192.168.1.10;
}
```

#### B) **Cisco IOS — DHCP básico**

```text
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp pool LAN
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8 1.1.1.1
 lease 7
```

#### C) **Relay (Cisco)**

Si el servidor DHCP está en otra subred:

```text
interface Vlan10
 ip address 192.168.1.1 255.255.255.0
 ip helper-address 10.0.0.5   ! IP del servidor DHCP
```

#### D) **Windows Server (resumen)**

- Crear un **scope**: rango IP (ej. 192.168.10.100–192.168.10.200).
- Configurar **opciones de scope** (gateway, DNS, DNS suffix).
- Crear **reservas** por MAC para dispositivos que necesitan IP fija.

### 9) DHCPv6 vs DHCPv4 (diferencias clave)

- **IPv6** tiene **SLAAC (Stateless Address Autoconfiguration)** y **DHCPv6** (stateful) — ambos pueden coexistir.
- DHCPv6 usa **mensajes diferentes** y **multicast** en vez de broadcast.
- En IPv6 es común que el router anuncie (RA) prefijos y el host use SLAAC; DHCPv6 se usa para opciones adicionales o cuando se quiere estado centralizado.
- No existe exactamente “DHCP relay” en el mismo sentido; hay mecanismos distintos (`dhcpv6 relay` sí existe).

### 10) Integración con DNS y ARP

- **DDNS (Dynamic DNS)**: muchos servidores DHCP pueden actualizar registros DNS en un servidor autoritativo cuando asignan IPs (útil en entornos Windows/AD).
- **ARP**: cuando el cliente recibe la IP, usualmente envía un **gratuitous ARP** para anunciar su MAC en la subred y detectar conflictos (algunos servidores realizan probes antes de adjudicar una IP).

### 11) Seguridad: riesgos y mitigaciones

**Riesgos comunes**

- **Rogue DHCP server**: equipo malicioso entrega parámetros incorrectos (puerta de enlace falsa, DNS malicioso).
- **DHCP starvation**: atacante inunda el servidor con solicitudes creando requests con direcciones MAC falsas hasta agotar el pool.
- **Configuraciones maliciosas** (DNS/ gateway) para MITM o filtrado.

**Mitigaciones**

- **DHCP Snooping** en switches gestionables — marca puertos “trusted” (uplinks a servidores DHCP) y bloquea servidores DHCP no confiables en puertos de usuario.
- **Port security / 802.1X** — restringe MAC por puerto y obliga autenticación.
- **VLANs y segmentación** — evitar mezcla de redes.
- **Reservas** para equipos críticos y seguimiento de logs.
- **Monitorización** (IDS/SIEM) para detectar servers DHCP no autorizados.

### 12) Troubleshooting (comandos y pasos)

**Windows**

- `ipconfig /all` → ver si DHCP enabled, dirección, DNS, lease obtained/expires.
- `ipconfig /renew` → solicitar renovación.
- `ipconfig /release` → liberar IP.

**Linux**

- `ip addr show` / `ip -4 addr` → ver IPs.
- `sudo dhclient -v eth0` → forzar solicitud DHCP (cliente ISC).
- `cat /var/lib/dhcp/dhclient.leases` o `/var/lib/dhcp/dhcpd.leases` → ver leases.
- `tcpdump -n -i eth0 udp port 67 or udp port 68` → capturar DORA para ver tráfico.

**Switch / Router**

- `show ip dhcp binding` (Cisco) → ver bindings actuales (IP ↔ MAC ↔ lease).
- `show ip dhcp server statistics` → estadísticas / errores.

**Problema típico y solución rápida**

- **Cliente sin IP (169.254.x.x)** → indica que no obtuvo DHCP; revisar cable/puerto, VLAN, DHCP relay, reachability al servidor, logs del servidor.
- **Conflicto IP** → revisar si hay otra máquina con misma IP (ARP/gratuitous ARP), y examinar logs de DHCP.

### 13) Casos prácticos / escenarios de ejemplo

#### Escenario 1 — Oficina pequeña (router doméstico)

- Router con DHCP habilitado: range `192.168.0.100–192.168.0.200`.
- PCs obtienen IP, gateway = router `192.168.0.1`, DNS = router o ISP.
- Reservas para impresora/servidor por MAC.

#### Escenario 2 — Empresa con servidor central y subredes

- Un servidor ISC o Windows DHCP maneja varios scopes (VLANs).
- Switches usan **DHCP relay** para reenviar solicitudes a servidor central.
- Integración con DNS (DDNS) para que máquinas aparezcan en Active Directory.

#### Escenario 3 — Alta disponibilidad

- **DHCP failover**: dos servidores DHCP en modo _load balance_ o _hot standby_ (ISC DHCP y Windows Server soportan mecanismos para alta disponibilidad).
- Alternativa simple: _split-scope_ (50/50) entre dos servidores.

### 14) Buenas prácticas resumidas

- Usar **reservas** para dispositivos críticos (impresoras, servidores, cámaras).
- Proteger la red con **DHCP snooping** y **802.1X**.
- Documentar rangos y exclusiones; evitar superponer scopes.
- Automatizar backups de la base de leases y configuración del servidor DHCP.
- Vigilar lease usage y ajustar duración según movilidad del cliente (corto lease para redes públicas, largo para servidores).
- Integrar DHCP con **DDNS** cuando sea necesario para mantener registros de nombres actualizados.

### 15) Resumen / Conclusión

DHCP es **la columna vertebral** de la configuración de red dinámica: simplifica la vida del administrador, reduce errores humanos y permite escalar infraestructuras. Funciona mediante un sencillo pero robusto flujo de mensajes (DORA), gestiona leases con tiempos de renovación y puede integrarse con DNS y soluciones de alta disponibilidad. Como cualquier servicio exponible, requiere controles de seguridad (DHCP snooping, reservas, segmentación) para evitar abusos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**       | **Siguiente 11**    |
| ------------------ | ----------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_9_VM.md) | [⏩](./4_11_DNS.md) |
