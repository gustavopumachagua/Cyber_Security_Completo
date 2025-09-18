| **Inicio**         | **atrás 31**                     | **Siguiente 33**           |
| ------------------ | -------------------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./13_31_Packet_Captures.md) | [⏩](./13_33_MAC_based.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [473. ¿Qué es el registro de firewall y por qué es importante?](#473-qué-es-el-registro-de-firewall-y-por-qué-es-importante) |
| [474. Revisión de los registros del firewall](#474-revisión-de-los-registros-del-firewall)                                   |

# **Firewall Logs**

## **473. ¿Qué es el registro de firewall y por qué es importante?**

![Firewall Logs](/img/13_Tools_for_Incident_Response_and_Discovery/Firewall_Logs.png "Firewall Logs")

### 1) Definir Firewall – ¿Qué significa Firewall?

Un **firewall** es un sistema de seguridad (hardware, software o híbrido) que controla el tráfico de red entrante y saliente según reglas predefinidas.

Funciona como una **barrera** entre redes internas de confianza y redes externas (ej. Internet).

### 2) ¿Qué es un firewall en redes?

En redes, un firewall inspecciona **paquetes, conexiones y aplicaciones** para decidir si los permite o bloquea.

Ejemplos:

- Bloquear accesos no autorizados a un servidor interno.
- Permitir tráfico web (HTTP/HTTPS) pero bloquear Telnet o FTP inseguros.

### 3) 4 tipos principales de firewalls

1. **Packet-filtering firewall** → inspecciona cabeceras (IP, puertos, protocolo). Rápido pero básico.

   _Ejemplo_: bloquear `TCP 23` (Telnet).

2. **Stateful inspection firewall** → analiza estado de la conexión (SYN, ACK, etc.). Más seguro que solo filtrar paquetes.

   _Ejemplo_: permite tráfico de respuesta solo si hubo petición válida.

3. **Application-level gateway (proxy firewall)** → inspecciona tráfico a nivel de aplicación.

   _Ejemplo_: filtrar HTTP y detectar SQL injection en la petición.

4. **Next-Generation Firewall (NGFW)** → integra filtrado de paquetes, IDS/IPS, inspección profunda, antivirus y control de aplicaciones.

   _Ejemplo_: identificar y bloquear tráfico de una app P2P no autorizada.

### 4) ¿Qué es el registro del firewall?

El **registro del firewall (firewall log)** es el archivo o base de datos donde se almacenan **eventos generados por el firewall**, como:

- Tráfico permitido o bloqueado.
- Intentos de conexión fallidos.
- Alertas de intrusión.
- Errores de configuración.

👉 Es la **caja negra del firewall**, que permite saber qué ha pasado en la red.

### 5) Importancia de los registros del firewall

- Detectar intentos de intrusión (fuerza bruta, escaneos).
- Identificar configuraciones erróneas.
- Cumplir normativas (ISO 27001, PCI-DSS, GDPR).
- Forense digital tras un ataque.
- Monitorear uso indebido de la red (ej. un empleado usando BitTorrent en horario laboral).

### 6) ¿Cuál es el propósito de un firewall?

- **Prevenir accesos no autorizados**.
- **Filtrar tráfico malicioso** (malware, exploits).
- **Segregar redes** (ejemplo: red de invitados ≠ red corporativa).
- **Cumplimiento normativo**.
- **Monitorear tráfico** para auditoría.

### 7) ¿Cuál es la mejor manera de configurar un firewall?

- **Principio de mínimo privilegio** → solo permitir lo necesario.
- **Default deny** → bloquear todo y permitir solo reglas específicas.
- **Segmentación** → separar redes críticas (servidores, IoT, usuarios).
- **Reglas específicas > generales**.
- **Actualizar y parchear** firmware/software.

### 8) Ejemplo de un lector de registros de firewall

Ejemplo de log de firewall (formato típico):

```
Date: 2025-09-16 12:45:10
Action: BLOCK
Protocol: TCP
Source IP: 192.168.1.50
Source Port: 52345
Destination IP: 10.0.0.10
Destination Port: 3389
Reason: Unauthorized access attempt
```

👉 Explicación: El firewall **bloqueó** un intento de conexión **TCP** desde la IP `192.168.1.50` al puerto **3389 (RDP)** del servidor `10.0.0.10`.

Esto podría ser un intento de **fuerza bruta a escritorio remoto**.

### 9) Protección de la información de registro

- **Acceso restringido**: solo admins autorizados.
- **Almacenamiento seguro**: en servidores centralizados de logs (ej. SIEM).
- **Integridad**: firmar digitalmente los logs o enviarlos a syslog remoto para evitar manipulación.
- **Retención**: guardar logs según normativa (ej. 1 año en PCI-DSS).

### 10) ¿Cómo puedo acceder a los registros del firewall?

- En **Windows Firewall**: `Visor de eventos → Logs de seguridad`.
- En firewalls físicos (Cisco ASA, Fortinet, Palo Alto): vía CLI, GUI o exportando a syslog.
- En Linux con `iptables`/`ufw`: logs en `/var/log/syslog` o `/var/log/ufw.log`.

### 11) ¿Cómo se analiza un firewall?

1. **Revisión manual**: leer logs buscando anomalías (p. ej. múltiples intentos fallidos).
2. **Filtros y búsquedas**: buscar por IP, puerto, acción.
3. **Automatización**: enviar logs a **SIEM** (ej. Splunk, ELK, QRadar).
4. **Alertas**: configurar reglas para alertar en tiempo real.

### 12) Mejores prácticas de registro del firewall

- Habilitar **logging de eventos críticos** (tráfico bloqueado, intentos sospechosos).
- No registrar **todo** el tráfico (consume recursos).
- Configurar niveles de log (info, warning, critical).
- Sincronizar **NTP** en todos los dispositivos (timestamps correctos).
- Enviar logs a un **servidor central (syslog/SIEM)**.

### 13) Estrategias de seguridad de firewall

- Revisar reglas periódicamente.
- Configurar alertas por intentos anómalos.
- Segmentar red con múltiples firewalls.
- Implementar **políticas de escalamiento** ante detección de ataques.

### 14) Cortafuegos de seguridad de red

Un **firewall de seguridad de red** se coloca entre la red interna y externa. Puede ser:

- Perimetral (entre LAN ↔ Internet).
- Interno (entre segmentos críticos).
- Cloud firewall (ej. AWS Security Groups, Azure NSG).

### 15) Cómo leer los registros del firewall

Pasos:

1. Identificar **timestamp** → cuándo ocurrió.
2. Acción (ALLOW/BLOCK).
3. Protocolo (TCP, UDP, ICMP).
4. IP origen/destino y puertos.
5. Razón (ej. política, regla específica).

Ejemplo real:

```
2025-09-16 13:02:33 ALLOW UDP 10.0.0.5:53 -> 8.8.8.8:53
```

👉 Explicación: El firewall **permitió** a un host interno `10.0.0.5` consultar a `8.8.8.8` por DNS.

### 16) ¿Qué es un registro de firewall ISA?

El **ISA Server (Internet Security and Acceleration Server)** de Microsoft (obsoleto, reemplazado por TMG) generaba logs detallados de tráfico web, VPN y firewall.

Ejemplo:

```
2025-09-16 14:10:22 192.168.1.25 TCP DENY CONNECT 80 www.example.com
```

Mostraba que el firewall ISA denegó acceso HTTP al sitio.

### 17) Problemas comunes en registros del firewall

- **Exceso de falsos positivos**: tráfico legítimo bloqueado.
- **Registros incompletos**: configuración insuficiente de logging.
- **Desincronización horaria**: dificulta correlación.
- **Sobrecarga de logs**: millones de eventos sin filtrar → difícil análisis.
- **Logs no enviados a SIEM**: riesgo de pérdida de datos.

### 18) ¿Qué es la gestión de registros de firewall?

Es el conjunto de **procesos, herramientas y políticas** para:

- Centralizar logs en un SIEM (ej. Splunk, ELK, Graylog).
- Normalizar formatos.
- Detectar patrones de ataque.
- Generar reportes de cumplimiento.
- Retener logs según normativa.

👉 Es clave en **seguridad y auditorías**.

✅ **Resumen:**

Los **registros de firewall** son esenciales para **detectar ataques, resolver problemas y cumplir normativas**. Un buen manejo incluye:

- **Configurar reglas de logging adecuadas**,
- **Enviar a un servidor central seguro**,
- **Analizar y correlacionar con otras fuentes** (SIEM),
- **Aplicar buenas prácticas de seguridad y retención**.

---

[🔼](#índice)

---

## **474. Revisión de los registros del firewall**

### 1 — ¿Qué es la «revisión de registros del firewall» y por qué importa?

Revisar registros del firewall significa **inspeccionar sistemáticamente** los eventos que el firewall genera (ALLOW, DENY, NAT, VPN, cambios de política, errores) para detectar:

- intentos de intrusión,
- escaneos de puertos,
- exfiltración de datos,
- errores de configuración,
- y eventos relevantes para cumplimiento.

**Importancia:** el firewall es la primera capa de defensa perimetral; sus logs son la “caja negra” para diagnosticar incidentes, auditar accesos y probar cumplimiento.

### 2 — Requisitos y preparación (antes de revisar)

Antes de empezar la revisión diaria/forense, asegúrate de:

1. **Ingestión y normalización**

   - Los logs del firewall deben enviarse a un servidor central (syslog/SIEM).
   - Normaliza campos claves: `timestamp`, `src_ip`, `dst_ip`, `src_port`, `dst_port`, `protocol`, `action`, `rule_id`, `bytes`, `bytes_in`, `bytes_out`, `interface`, `user` (si aplica).

2. **Sincronización de tiempo (NTP)**

   - Exportadores y collectors sincronizados (±1s) para correlación.

3. **Mapeo de interfaces y zonas**

   - ifIndex → nombre de interfaz; IP internal/external zones.

4. **Retención e integridad**

   - Definir retención (ej. 90–365 días según normativa).
   - Enviar copia a SIEM remoto y firmar/hashear archivos o usar WORM.

5. **Baselines**

   - Conocer comportamiento normal (picos, top talkers, puertos esperados).

### 3 — Formato típico y ejemplos de entradas (genérico)

Un registro normal (simplificado) — campos clave:

```
2025-09-16T13:02:33Z action=BLOCK protocol=TCP src=192.168.1.50 src_port=52345 dst=10.0.0.10 dst_port=3389 rule_id=1003 bytes=0 policy=InternetToDMZ
```

Ejemplos vendor específicos (estilizados — para interpretar):

- **Cisco ASA (simplificado):**

```
%ASA-6-302013: Built inbound TCP connection 12345 for outside:192.0.2.5/52345 to inside:10.0.0.10/3389
```

- **Palo Alto (simplificado):**

```
2025/09/16 13:02:33 192.168.1.50 10.0.0.10 52345 3389 tcp drop rule_id=Allow_RDP
```

- **iptables (syslog):**

```
Sep 16 13:02:33 firewall kernel: [UFW BLOCK] IN=eth0 OUT= MAC=... SRC=192.168.1.50 DST=10.0.0.10 PROTO=TCP DPT=3389
```

### 4 — Flujo de trabajo para la revisión de logs (daily → incidente)

1. **Ingesta & verificación (mañana)**

   - ¿Están llegando logs? (`logs_received`, `no. of sources`)
   - ¿Timestamps bien?
   - KPIs: % de dispositivos que envían logs, lag máximo (minutos).

2. **Revisión diaria automática (top lists)**

   - Top 10 IPs bloqueadas.
   - Top 10 puertos bloqueados.
   - Top 10 reglas que más bloquearon.
   - Volumen de tráfico saliente inusual.

3. **Alertas en tiempo real**

   - Reglas críticas (brute-force, scan masivo, exfiltration candidate) ya deben alertar.

4. **Triage**

   - Priorizar por severidad: `blocked -> repeated -> to known-malicious` > single allow to rare service.

5. **Investigación**

   - Enriquecer con whois, GeoIP, passive DNS, reputación de IP, logs de proxy/DNS/EDR.
   - Buscar lateralidad: ¿la IP afectó otras máquinas?

6. **Contención y remediación**

   - Bloquear IP/segmento, aislar host, forzar reset de credenciales, actualizar reglas.

7. **Cierre y lecciones**

   - Documentación en ticket, POA\&M para cambios de firewall y reglas.

### 5 — Qué buscar primero (lista priorizada)

- **Picos de DENY** desde una misma IP o red.
- **Intents a servicios críticos** (RDP 3389, SSH 22, SMB 445) desde Internet.
- **Escaneos**: una IP destino múltiple puertos / una IP origen muchas puertos destino.
- **Beaconing**: pequeñas sesiones periódicas a un mismo host externo (C2).
- **Transferencias grandes** hacia IPs fuera de la empresa (posible exfil).
- **Cambios de configuración** del firewall (who, when).
- **Errores repetidos** (licencia, socket issues) que puedan indicar fallos operativos.

### 6 — Ejemplos de detecciones y consultas (Splunk + Elastic/KQL)

> **Nota:** adapta `index`, `field` y nombres según tu SIEM / esquema de ingestión.

#### A) Top IPs bloqueadas (Splunk)

```spl
index=firewall action=deny earliest=-24h
| stats count by src_ip
| sort -count
| head 20
```

#### B) Top puertos destino bloqueados (Splunk)

```spl
index=firewall action=deny earliest=-24h
| stats count by dst_port
| sort -count
```

#### C) IPs con muchos intentos a puertos distintos → posible escaneo (Splunk)

```spl
index=firewall earliest=-1h
| stats dc(dst_port) AS unique_ports, count AS attempts by src_ip
| where unique_ports > 50 AND attempts > 100
| sort -unique_ports
```

#### D) Flujos pequeños y repetidos → posible beaconing (Splunk simple)

```spl
index=firewall action=allow dst_zone=external bytes < 1000 earliest=-24h
| stats count by src_ip,dst_ip
| where count > 200
```

#### E) Tráfico saliente voluminoso (posible exfiltration)

```spl
index=firewall action=allow dest_zone=external earliest=-1h
| stats sum(bytes) AS total_bytes by src_ip, dst_ip
| where total_bytes > 1000000000
```

(> \~1 GB en 1 h como ejemplo; ajusta umbrales)

### KQL / Kibana (ejemplos conceptuales)

- Top IPs bloqueadas:

```
action: "deny" AND @timestamp:[now-24h TO now]
| top src.ip
```

- Muchos puertos:

```
action: "deny" AND @timestamp:[now-1h TO now]
| stats count() by src.ip, destination.port | where count > 100
```

(En Kibana usar visualizaciones/aggs)

### 7 — Dashboard & KPIs recomendados

- **KPIs diarios:** número de logs recibidos, % dispositivos activos, tiempo medio de ingestión.
- **Seguridad:** top 10 IPs bloqueadas, top reglas que bloquean, alertas activas, número de intentos de login bloqueados.
- **Rendimiento:** volumen total bytes in/out, top talkers (hosts).
- **Tuning:** tasa de falsos positivos por regla.

### 8 — Investigación detallada (playbook resumido)

Cuando una alerta se dispara (ej. IP X intentó conectarse 1000 veces al puerto 3389):

1. **Recolectar evidencias**

   - Extraer logs firewall (últimas 24–72h) por `src_ip` y `dst_ip`.
   - Extraer logs proxy, DNS, EDR para los mismos timestamps.

2. **Enriquecer**

   - GeoIP lookup (país de origen)
   - WHOIS, listas negras, IOC feeds
   - Ver si la `src_ip` es de cloud provider o casa usuario

3. **Contexto interno**

   - ¿El src_ip es un empleado remoto? ¿IP legítima de partner?
   - ¿Hubo fallos de autenticación correlacionados en logs de dominio?

4. **Decisión**

   - Si malicioso: bloquear en firewall/proxy, notificar IS, aislar host afectado.
   - Si benigno (falso positivo): ajustar regla, documentar por qué.

5. **Remediación**

   - Implementar bloqueo y reglas preventivas (rate limit, geo-block) si aplica.
   - Revisar y cerrar ticket con evidencia y recomendaciones.

### 9 — Reglas de alerta sugeridas (ejemplos prácticos)

- **Brute force RDP/SSH:** `count(deny OR fail_auth) from src_ip to dst_port 3389/22 > 50 in 10m` → ALTA.
- **Escaneo de puertos:** `dc(dst_port) > 100` en X minutos → MEDIA/ALTA.
- **Exfiltración:** `sum(bytes_outbound by src_ip) > threshold` → MUY ALTA.
- **Beaconing:** `many small flows to same dst_ip repeatedly` → INVESTIGATE.
- **Cambio de reglas:** `firewall_config_change` events → REVISAR por auditor.

### 10 — Tuning & reducir falsos positivos

- **Whitelist**: IPs de partners, CDNs, servicios internos confiables.
- **Rate limiting** en reglas para no alertar por picos legítimos (p. ej. backups).
- **Contexto de horario**: backups nocturnos, mantenimiento.
- **Análisis de causa raíz**: si un evento se repite por actividad legítima, ajustar la regla o crear excepción documentada.

### 11 — Protección, integridad y privacidad de logs

- **Transporte seguro**: syslog over TLS o encaminamiento por red management.
- **Acceso controlado**: RBAC en SIEM; auditoría access logs.
- **Integridad**: firmar archivos (SHA) o enviar copia a almacenamiento inmutable.
- **Redacción**: cuando compartas, anonimiza datos sensibles (PII).
- **Retención**: cumplir normativa (ej. PCI: 1 año, ISO local).

### 12 — Errores comunes en la revisión y cómo evitarlos

- **No revisar periódicamente** → problemas pasan desapercibidos.
- **Alertas mal afinadas** → fatiga por falsos positivos.
- **Logs incompletos** → reglas que no generan logs (ej. allow sin logging).

  - _Solución:_ habilita logging para reglas críticas y revisa políticas.

- **Timestamps fuera de sincronía** → fallas en correlación.

  - _Solución:_ NTP sincronizado.

### 13 — Automatización y enriquecimiento que acelera la revisión

- Integrar feeds de reputación (ThreatIntel).
- Enriquecer automáticamente con GeoIP + WHOIS + ASN + owner.
- Playbooks SOAR para acciones: bloquear IP, crear ticket, notificar equipo.
- Machine Learning para detectar beaconing o patrones anómalos (si tu SIEM lo soporta).

### 14 — Ejemplo concreto de caso (mini-caso)

**Alerta:** IP 198.51.100.45 bloqueada 4.500 veces en 6 horas intentando conectar a puerto 3389 (RDP).

**Revisión rápida:**

- `show logs` → confirma 4.500 DENY eventos `src=198.51.100.45 dst=10.0.0.23 dport=3389`.
- Enriquecimiento → GeoIP: país X; WHOIS → proveedor cloud.
- Correlación → EDR en host 10.0.0.23 no detecta procesos inusuales, pero hay muchas autenticaciones fallidas en AD logs.
- Acción → bloquear IP 198.51.100.45 en firewall, forzar password reset para la cuenta objetivo, investigar si hay credenciales comprometidas.
- Post-mortem → ajustar reglas: rate-limit para RDP desde Internet, deshabilitar RDP directo (VPN/JIT).

### 15 — Checklist rápido de revisión diaria / semanal / mensual

**Diaria**

- Verificar ingestión y lag.
- Top 10 denies y allows.
- Alertas críticas (brute force, exfil).
- Revisar cambios de configuración.

**Semanal**

- Revisar reglas con mayor bloqueo (posibles errores).
- Hunting por anomalías (beaconing, scans).

**Mensual**

- Revisar reglas firewall (limpieza, documentación).
- Revisar retención y backups de logs.
- Ejercicios de revisión de incidentes (tabletop).

### 16 — Campos mínimos que DEBE contener un log de firewall (para revisión eficaz)

- `timestamp` (ISO UTC)
- `action` (ALLOW / DENY / DROP / RESET)
- `src_ip`, `src_port`
- `dst_ip`, `dst_port`
- `protocol` (TCP/UDP/ICMP)
- `bytes` (in/out)
- `rule_id` o `policy` (referencia a la regla)
- `interface_in` / `interface_out`
- `user` / `session_id` (si aplica)
- `device_hostname` / `vendor` / `log_source`

---

[🔼](#índice)

---

| **Inicio**         | **atrás 31**                     | **Siguiente 33**           |
| ------------------ | -------------------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./13_31_Packet_Captures.md) | [⏩](./13_33_MAC_based.md) |
