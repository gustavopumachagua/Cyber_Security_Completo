| **Inicio**         | **atrás 29**            | **Siguiente 31**                 |
| ------------------ | ----------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./13_29_syslog.md) | [⏩](./13_31_Packet_Captures.md) |

---

## **Índice**

| Temario                                      |
| -------------------------------------------- |
| [469. Cisco NetFlow](#469-cisco-netflow)     |
| [470. ¿Qué es NetFlow?](#470-qué-es-netflow) |

# **NetFlow**

## **469. Cisco NetFlow**

![NetFlow](/img/13_Tools_for_Incident_Response_and_Discovery/NetFlow.jpg "NetFlow")

### 1) ¿Qué es NetFlow (en pocas palabras)?

**NetFlow** es un protocolo de Cisco (luego estandarizado en variantes) para **capturar información agregada sobre flujos de tráfico** que atraviesan un router o switch.

Un _flujo_ normalmente se define por una **tupla clave** (p. ej. source IP, destination IP, source port, destination port, protocolo, interfaz) y por cada flujo el router mantiene contadores (bytes, paquetes) y marcas de tiempo: cuando comienza y cuando termina. Es una forma eficiente de conocer **quién habla con quién, cuánto y cuándo**, sin almacenar paquetes completos.

### 2) Para qué sirve NetFlow (casos de uso)

- **Contabilidad / billing / capacity planning**: quién consume ancho de banda.
- **Detección de anomalías / seguridad**: exfiltración de datos, DDoS, escaneos, comunicación con C2.
- **Resolución de problemas de red**: identificar “top talkers” y rutas problemáticas.
- **Clasificación de aplicaciones** (combinado con NBAR o DPI).
- **Reporting y forense**: reconstruir actividad histórica sin tener pcap completo.

### 3) Versiones importantes: v5, v9 e IPFIX

- **NetFlow v5**: formato fijo muy común para IPv4 (no extensible). Ideal para tráfico “clásico”.
- **NetFlow v9**: formato basado en _templates_ (plantillas). Es **extensible** y permitió soportar campos como IPv6, BGP next-hop, MPLS, etc.
- **IPFIX (RFC 7011)**: estandarización IETF de la idea de NetFlow v9 (más interoperabilidad). Muchos coleccionadores entienden IPFIX y v9.

### 4) Conceptos clave

- **Flujo (flow)**: unidad lógica de tráfico (generalmente 5-tuple).
- **Flow cache**: memoria local del router donde se guardan entradas de flujo hasta su expiración.
- **Active / Inactive timeout**:

  - _Active_ (p. ej. 60 s) → forzar envíos periódicos de flujos largos.
  - _Inactive_ (p. ej. 15 s) → si no hay paquetes nuevos, se exporta y borra del cache.

- **Exporter**: componente que envía registros NetFlow hacia un **collector** (IP/puerto).
- **Collector**: servidor que recibe, almacena y procesa flujos (nfdump, nfsen, SiLK, ntopng, Elastiflow+ELK, Splunk App for NetFlow, Cisco Stealthwatch, etc.).
- **Sampling**: tomar 1 de N paquetes para reducir carga; mejora escalabilidad a costa de precisión para flows pequeños.

### 5) Campos típicos en un registro NetFlow

Campos comunes (pueden variar por versión/template):

- srcAddr, dstAddr (IPv4/IPv6)
- srcPort, dstPort
- protocol (TCP/UDP/ICMP)
- packets, bytes
- startTime, endTime (o sysUptime first/last)
- inputInterfaceIndex, outputInterfaceIndex
- tcpFlags, tos (DSCP)
- srcAS, dstAS (BGP ASN)
- nextHop, vlan, mplsLabel (si se exportan)

Ejemplo de registro interpretado:

```
Start: 2025-09-16 10:00:00, End: 2025-09-16 10:01:00,
Src=192.0.2.10:12345 → Dst=198.51.100.5:80, Proto=TCP,
Packets=12, Bytes=8400, IfIn=2, IfOut=5, TCPflags=0x18
```

(8400 bytes ≡ 67,200 bits; sobre 60 s → \~1,120 bits/s promedio).

### 6) NetFlow clásico (ejemplo de configuración v5 en IOS)

**Configuración mínima** (sintaxis clásica, IOS legacy):

```text
! En el router Cisco
enable
conf t

! Definir el destino del export (collector) y versión
ip flow-export destination 10.0.0.10 2055
ip flow-export version 5
ip flow-export source GigabitEthernet0/0   ! opción para fijar IP origen de export
ip flow-cache timeout active 60
ip flow-cache timeout inactive 15

! Activar NetFlow en interfaces
interface GigabitEthernet0/1
 ip flow ingress
 ip flow egress
!
end
write memory
```

Ver estado:

```text
show ip cache flow          ! ver entradas NetFlow en cache
show ip flow export         ! ver destino, versiones y counters de export
show ip flow interface      ! ver interfaces con NetFlow
```

### 7) Flexible NetFlow (FNF) — recomendado en plataformas modernas

Flexible NetFlow (FNF) permite definir **flow record**, **flow exporter** y **flow monitor** con precisión (qué campos capturar, cómo agregarlos).

**Ejemplo de FNF (IOS XE / IOS con FNF):**

```text
conf t
! 1) Definir los campos que queremos (record)
flow record R-IPv4
 match ipv4 source address
 match ipv4 destination address
 match transport source-port
 match transport destination-port
 match transport protocol
 collect counter packets
 collect counter bytes
 collect timestamp absolute first
 collect timestamp absolute last

! 2) Definir el exporter (collector)
flow exporter EXP-1
 destination 10.0.0.10
 transport udp 2055
 source GigabitEthernet0/0
 template data timeout 60

! 3) Crear monitor y asociarlo al record/exporter
flow monitor FM-1
 record R-IPv4
 exporter EXP-1
 cache timeout active 60
 cache timeout inactive 15

! 4) Aplicar en interfaces
interface GigabitEthernet0/1
 ip flow monitor FM-1 input
 ip flow monitor FM-1 output

end
write memory
```

Comandos para ver:

```text
show flow monitor FM-1 cache
show flow monitor FM-1 statistics
show flow exporter EXP-1
```

### 8) Sampling (muestreo) — cuándo usarlo y cómo afecta

- En enlaces de alta velocidad, activar NetFlow sin muestreo puede saturar CPU/memoria del router.
- **Sampling** (p. ej. 1:1000) reduce carga: se exporta información sobre 1 paquete de cada 1000; los collectors pueden estimar volúmenes.
- Precisión baja para flows cortos; buena para top-talkers/ tendencias.

En plataformas Cisco se configura un _sampler_ o _ip flow-sampling_ — la sintaxis varía por IOS. Si vas a usar sampling, documenta la tasa y asegúrate de que el collector la conozca (muchas herramientas aceptan el factor de muestreo).

### 9) Qué ver en el collector y ejemplos de análisis

Ejemplos de análisis con **nfdump/nfsen** o herramientas SIEM:

- **Top talkers** (por bytes): ver las IPs que más bytes enviaron/recibieron.
- **Top flows por puerto**: identificar aplicaciones que consumen ancho de banda (p. ej. dst port 443).
- **Anomalías**: un host que de repente envía mucho tráfico a un único host externo → posible exfiltración.
- **Escaneos**: muchos flows cortos desde una IP a muchos puertos/destinos → escaneo/port scan.

Ejemplo de regla simple de seguridad (pseudológica):

```
Si (host_src.bytes_outbound en 10 min) > 1 GB y dst IP outside_corp, entonces ALERT: posible exfiltration
```

### 10) NetFlow y seguridad: detecciones típicas

- **Exfiltración**: grandes volúmenes hacia destinos atípicos.
- **Beaconing / C2**: pequeños flujos periódicos a un servidor externo.
- **DDoS**: miles de flows con mismo dst (amplificación); o un único flow enorme desde/para muchos hosts.
- **Lateral movement**: aumento de conexiones SMB/RPC entre hosts internos.
- **Port scans**: un src que genera muchos flows a distintos dst ports en poco tiempo.

Combina NetFlow con host telemetry (EDR) y logs para reducir falsos positivos.

### 11) Buenas prácticas de despliegue

- **Coleccionar en puntos estratégicos**: frontera Internet (edge), enlaces entre datacenters, switches de agregación.
- **Time sync (NTP)**: exportadores y collectors deben estar sincronizados.
- **Escoger versión correcta**: v5 suficiente para IPv4 simple; v9/IPFIX para IPv6, MPLS, ASNs, campos extendidos.
- **Dimensionar collectors**: flujo continuo de datos puede ser grande; usar discos rápidos y retención acorde a tus necesidades.
- **Habilitar sampling en equipos con alto throughput**.
- **Protección y confiabilidad**: usar TCP/TLS (si soportado) para export seguro en entornos sensibles (o asegurar red interna).
- **Mapear interfaces**: los índices de interfaz exportados deben mapearse en el collector con nombres legibles (mapping de ifIndex).
- **Rotación/retención**: políticas claras de retención de flows vs storage.

### 12) Comandos útiles (resumen rápido)

- **Clásico NetFlow (IOS legacy)**:

  - `show ip cache flow`
  - `show ip flow export`
  - `show ip flow interface`

- **Flexible NetFlow**:

  - `show flow monitor <name> cache`
  - `show flow monitor <name> statistics`
  - `show flow exporter`
  - `show flow record`

- **Otras**: `show logging`, `show processes cpu` (para ver impacto), `show platform hardware throughput` (según plataforma).

### 13) Ejemplo práctico: interpretar un flujo y calcular throughput

Registro (ejemplo):

```
Start=2025-09-16 10:00:00   End=2025-09-16 10:01:00
Src=10.10.10.5:52345  ->  Dst=93.184.216.34:443  Proto=TCP
Packets=12  Bytes=8400
```

- Duración = 60 s
- Bits = 8400 bytes × 8 = 67,200 bits
- Throughput ≈ 67,200 / 60 = **1,120 bits/s** (≈ 1.12 kbps) — promedio del flujo.

(Eso ayuda a identificar si un flujo es “grande” o trivial.)

### 14) Coleccionadores / herramientas populares

- **Open source**: nfdump/nfsen, SiLK, ntopng, Elastiflow (Elasticsearch + Logstash + Kibana + Packetbeat).
- **Comerciales / enterprise**: Cisco Stealthwatch, Plixer Scrutinizer, SolarWinds NetFlow, Splunk (con apps), Flowmon.
- **Integración SIEM**: importar flujos o métricas agregadas (top talkers, etc.) para correlación con otros eventos.

### 15) Problemas comunes y resolución

- **No se reciben exports**: revisar conectividad UDP/TCP al puerto correcto, `show ip flow export` para counters de errores.
- **Plantillas v9 no entendidas**: collector debe soportar v9/IPFIX; templates expiradas pueden causar records no parseables.
- **Alta carga CPU**: habilitar sampling, mover export a routers con ASIC/Hardware NetFlow si es posible, o reducir campos exportados.
- **Inconsistencia de timestamps**: sincronizar NTP.
- **IfIndex mismatch**: asegurar que el collector tenga mapping ifIndex→ifName (puede obtenerse via SNMP).

### 16) Recomendaciones finales (prácticas)

- Empieza por habilitar NetFlow **en un borde** y estudiar datos 1–2 semanas antes de expandir.
- Usa **Flexible NetFlow** para capturar campos que te interesen (por ejemplo bytes por dirección MAC, VLAN, ASNs).
- **Documenta** la tasa de muestreo, puntos de export y retención para auditoría.
- Combina NetFlow con **logs y telemetría** (firewall logs, DNS logs, EDR) para investigar incidentes con más contexto.

---

[🔼](#índice)

---

## **470. ¿Qué es NetFlow?**

**NetFlow** es una tecnología/protocolo (original de Cisco, con versiones estandarizadas) que **resume y exporta información sobre “flujos” de tráfico** que atraviesan un router o switch.

Un _flujo_ agrupa paquetes que comparten atributos (p. ej. misma IP origen/destino, mismos puertos, mismo protocolo). En lugar de guardar paquetes completos, NetFlow exporta registros por flujo con contadores (bytes/paquetes) y marcas de tiempo: quién habló con quién, cuánto y cuándo.

### ¿Por qué usar NetFlow?

- Obtener visibilidad de **quién ocupa el ancho de banda** (top talkers).
- **Detectar anomalías de seguridad**: exfiltración, beaconing/C2, escaneos, DDoS.
- **Resolución de problemas de red** y dimensionamiento (capacity planning).
- **Forense**: reconstruir actividad de red sin necesidad de pcap completo.

### Conceptos clave

- **Flujo (flow):** normalmente definido por la 5-tupla: `srcIP, dstIP, srcPort, dstPort, protocol` (+ otros campos opcionales).
- **Flow cache:** tabla en el router que acumula entradas de flujo hasta que expiran.
- **Exporter:** componente en el equipo que envía registros NetFlow a un collector.
- **Collector:** servidor que recibe, almacena y procesa los registros (ej. nfdump/nfsen, SiLK, ntopng, Elastiflow+ELK, Splunk apps).
- **Active / Inactive timeout:** parámetros que controlan cuándo se exportan flujos (por ejemplo activo 60s, inactivo 15s).
- **Sampling (muestreo):** tomar 1 de N paquetes para reducir carga; útil en enlaces de alta velocidad.

### Versiones y estandarización

- **NetFlow v5:** formato fijo, muy común para IPv4.
- **NetFlow v9:** formato basado en _templates_ (extensible: IPv6, BGP, MPLS, etc.).
- **IPFIX (RFC 7011):** estandarización IETF que deriva de v9; ampliamente soportado.

### Campos típicos de un registro NetFlow (ejemplo)

Un registro puede contener (según versión/template):

- `srcAddr`, `dstAddr` (IP origen/destino)
- `srcPort`, `dstPort`
- `protocol` (TCP/UDP/ICMP)
- `packets`, `bytes`
- `startTime`, `endTime`
- `inputInterface`, `outputInterface`
- `tcpFlags`, `tos/dscp`
- `nextHop`, `srcAS`, `dstAS`, `vlan`, `mplsLabel` (si están habilitados)

**Ejemplo de registro (legible):**

```
Start=2025-09-16 10:00:00  End=2025-09-16 10:01:00
Src=10.10.10.5:52345 -> Dst=93.184.216.34:443  Proto=TCP
Packets=12  Bytes=8400
```

### Ejemplo de cálculo de throughput (práctico)

Registro anterior → `Bytes = 8.400` ; duración = `60 s`
Bits transferidos = 8.400 × 8 = 67.200 bits
Throughput promedio = 67.200 / 60 ≈ **1.120 bits/s** (\~1.12 kb/s).

Ejemplo de alerta por exfiltración:
Si un host supera **1 GB en 10 minutos** hacia una IP externa → alarma.
1 GB = 1.073.741.824 bytes → bits = ×8 = 8.589.934.592 bits → /600 s ≈ **14.316.557 bps (\~14.3 Mbps)**

### Ejemplos de configuración (Cisco)

#### NetFlow clásico (v5) — sintaxis mínima

```text
ip flow-export destination 10.0.0.10 2055
ip flow-export version 5
ip flow-export source GigabitEthernet0/0
ip flow-cache timeout active 60
interface GigabitEthernet0/1
 ip flow ingress
 ip flow egress
```

Comandos útiles:

```
show ip cache flow
show ip flow export
show ip flow interface
```

#### Flexible NetFlow (FNF) — esquema moderno

```text
flow record R-IPv4
 match ipv4 source address
 match ipv4 destination address
 match transport source-port
 match transport destination-port
 collect counter packets
 collect counter bytes
 collect timestamp absolute first
 collect timestamp absolute last

flow exporter EXP-1
 destination 10.0.0.10
 transport udp 2055
 source GigabitEthernet0/0
 template data timeout 60

flow monitor FM-1
 record R-IPv4
 exporter EXP-1
 cache timeout active 60

interface GigabitEthernet0/1
 ip flow monitor FM-1 input
 ip flow monitor FM-1 output
```

Comandos FNF:

```
show flow monitor FM-1 cache
show flow exporter EXP-1
show flow monitor FM-1 statistics
```

### Detección y seguridad con NetFlow

**Patrones típicos que indican problemas:**

- **Exfiltración:** volumen inusitado hacia IP externa atípica.
- **Beaconing / C2:** flujos pequeños y periódicos a un mismo host externo.
- **Port scans:** muchas conexiones cortas desde una IP a distintos puertos/destinos.
- **DDoS:** multitud de flows hacia un único destino o muchos flows desde múltiples fuentes.

**Ejemplo de regla conceptual:**

```
Si host_src.bytes_outbound_en_10min > 1GB y dst outside_corp → ALERT: posible exfiltration
```

Combina NetFlow con logs (DNS, proxy, EDR) para validar y reducir falsos positivos.

### Dónde y cómo desplegar (mejores prácticas)

- **Puntos recomendados:** borde Internet (edge), enlaces de peering, agregación de data center, enlaces críticos entre zonas.
- **Usar Flexible NetFlow** si necesitas campos avanzados (IPv6, ASN, MPLS, VLAN).
- **Habilitar sampling** en enlaces de alto throughput.
- **Sincronizar relojes (NTP)** exporter ↔ collector.
- **Dimensionar collectors** (IOPS, CPU, disco) según tasa de export.
- **Proteger transporte:** si es sensible, usar export seguro (IPSec/TCP/TLS) o red interna dedicada.

### Herramientas/collectors populares

- Open source: **nfdump/nfsen**, **SiLK**, **ntopng**, **Elastiflow (ELK)**.
- Comerciales/enterprise: **Cisco Stealthwatch**, **Plixer Scrutinizer**, **Flowmon**, **Splunk** (apps NetFlow).

### Limitaciones y consideraciones

- **No reemplaza pcap**: NetFlow da metadatos por flujo, pero no paquetes ni payload.
- **Precision vs carga**: sin sampling tienes más precisión pero más carga. Con sampling puedes perder flows pequeños.
- **Temas de plantillas v9/IPFIX**: collectors deben comprender templates; errores de templates pueden romper parsing.
- **IfIndex mapping**: los índices de interfaz deben mapearse (SNMP) para nombres legibles en el collector.

### Checklist rápido para empezar con NetFlow

1. Habilita NetFlow en un borde y recolecta datos 1–2 semanas.
2. Decide versión: **v5** para IPv4 simple, **v9/IPFIX** para campos extendidos.
3. Configura timeouts y (si procede) sampling.
4. Monta collector (nfdump/Elastiflow/SiLK) y construye dashboards: top talkers, top ports, flows por host.
5. Define reglas de seguridad (exfiltración, beaconing, scans, DDoS).
6. Documenta retención y dimensionamiento.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 29**            | **Siguiente 31**                 |
| ------------------ | ----------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./13_29_syslog.md) | [⏩](./13_31_Packet_Captures.md) |
