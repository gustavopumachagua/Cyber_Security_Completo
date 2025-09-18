| **Inicio**         | **atrás 28**                | **Siguiente 30**         |
| ------------------ | --------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_28_Event_Logs.md) | [⏩](./13_30_NetFlow.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [467. ¿Qué es syslog?](#467-qué-es-syslog)             |
| [468. CCNA gratuito Syslog](#468-ccna-gratuito-syslog) |

# **Syslog**

## **467. ¿Qué es syslog?**

![Syslog](/img/13_Tools_for_Incident_Response_and_Discovery/Syslog.png "Syslog")

**Syslog** (abreviatura de _System Logging Protocol_) es un **estándar para el envío y almacenamiento de mensajes de registro (logs)** en sistemas informáticos y de red.

- Nació en **UNIX en los años 80** para registrar eventos del sistema.
- Hoy es un **protocolo universal** soportado por sistemas operativos (Linux, BSD, macOS), dispositivos de red (routers, switches, firewalls) y muchas aplicaciones.
- Permite que todos los eventos importantes viajen hacia un **servidor central de logs** para su análisis.

👉 Ejemplo:

Un firewall Cisco envía registros de intentos de conexión sospechosos a un servidor Syslog, donde un administrador o un SIEM los analiza.

### 📌 Definición de Syslog

> Syslog es un protocolo de mensajería de registros definido en **RFC 3164 y RFC 5424**, que permite a sistemas y dispositivos enviar mensajes de log a un servidor central, usando principalmente **UDP 514** (aunque también TCP o TLS).

### 📌 ¿Cómo funciona Syslog?

1. **Generación del evento**

   El sistema operativo, aplicación o dispositivo genera un mensaje de log (ej. "Inicio de sesión fallido").

2. **Cliente Syslog**

   El agente (ej. `rsyslog` o `syslog-ng` en Linux) toma ese mensaje y lo formatea en estándar Syslog.

3. **Transmisión**

   El mensaje se envía al servidor Syslog mediante:

   - **UDP 514** → más rápido, pero sin garantía de entrega.
   - **TCP 514 o 6514 (TLS)** → garantiza entrega y cifrado.

4. **Servidor Syslog**

   Recibe y almacena todos los mensajes en archivos de log o bases de datos, normalmente en `/var/log/`.

5. **Análisis**

   Los administradores o un **SIEM** procesan estos logs para detectar incidentes, anomalías o fallas.

👉 Ejemplo práctico en Linux:

Un intento fallido de inicio de sesión SSH se envía como:

```
<34>Sep 16 12:10:01 server1 sshd[2123]: Failed password for root from 192.168.1.50 port 54012 ssh2
```

### 📌 Beneficios de Syslog

- **Centralización** → Logs de todos los equipos (servidores, routers, firewalls) en un único lugar.
- **Estandarización** → Formato común para diferentes sistemas.
- **Escalabilidad** → Miles de dispositivos pueden reportar a un solo servidor.
- **Monitoreo en tiempo real** → útil para detectar ataques inmediatamente.
- **Integración con SIEM** → herramientas como Splunk, ELK, QRadar pueden ingerir syslog.
- **Auditoría y cumplimiento** → soporte para normativas (ISO 27001, PCI-DSS, HIPAA).

### 📌 Formato y mensajes de Syslog

Un mensaje típico de Syslog incluye:

```
<PRI> TIMESTAMP HOSTNAME APP-NAME[PROCID]: MSG
```

Ejemplo:

```
<34>Sep 16 12:15:45 server1 sshd[2345]: Accepted password for user1 from 192.168.1.20 port 50512 ssh2
```

📍 **Desglose**:

- `<34>` → **PRI** (Priority: Facility + Severity).
- `Sep 16 12:15:45` → **Fecha/Hora**.
- `server1` → **Hostname**.
- `sshd[2345]` → **Proceso** y su PID.
- `Accepted password for user1...` → **Mensaje del evento**.

**Niveles de severidad (0–7):**

- 0 → Emergencia (sistema inutilizable).
- 1 → Alerta.
- 2 → Crítico.
- 3 → Error.
- 4 → Advertencia.
- 5 → Notificación.
- 6 → Informativo.
- 7 → Depuración (debug).

### 📌 Servidores Syslog

Un **servidor Syslog** centraliza los logs enviados por múltiples clientes.

Ejemplos:

- **Linux**: `rsyslog`, `syslog-ng`, `journalctl/systemd`.
- **Comerciales / SIEM**: Splunk, Graylog, QRadar, LogRhythm.
- **Nube**: AWS CloudWatch Logs, Azure Monitor, Google Cloud Logging.

👉 Ejemplo:

Un servidor `rsyslog` configurado en Linux recibe logs de todos los switches y firewalls de una empresa en `/var/log/network/`.

### 📌 Supervisión de archivos de registro de syslog

- En **Linux**, los logs se almacenan en `/var/log/`:

  - `/var/log/syslog` (eventos generales).
  - `/var/log/auth.log` (autenticaciones).
  - `/var/log/kern.log` (kernel).

- Con **tail -f /var/log/syslog** puedes ver eventos en tiempo real.
- Con SIEM o dashboards (ej. Kibana, Graylog) puedes hacer análisis avanzado, alertas y reportes.

✅ **En resumen**:

- **Syslog = estándar universal de logging** para centralizar eventos de sistemas y dispositivos.
- Funciona con cliente-servidor y se apoya en UDP/TCP/TLS.
- Proporciona **centralización, monitoreo, detección de incidentes y cumplimiento normativo**.
- Es la base de la mayoría de arquitecturas de **SIEM y monitoreo moderno**.

---

[🔼](#índice)

---

## **468. CCNA gratuito Syslog**

### 1) ¿Qué es Syslog (definición rápida)?

**Syslog** es un protocolo y estándar para enviar mensajes de registro ("logs") desde **dispositivos de red, servidores y aplicaciones** a un **servidor central**.

Objetivo: centralizar registros para monitorización, troubleshooting y auditoría.

En redes: los routers/switches Cisco, firewalls y servidores envían sus eventos (link up/down, fallos, cambios de configuración, autenticaciones, etc.) a un servidor syslog.

### 2) ¿Cómo funciona (resumen)?

1. Un dispositivo genera un evento (p. ej. interfaz bajó).
2. El agente Syslog del dispositivo compone un mensaje con timestamp y texto.
3. Se envía al **servidor syslog** (UDP 514 por defecto; TCP/TLS opcional para más fiabilidad/seguridad).
4. El servidor guarda y/o procesa (filtros, alertas, SIEM).

Ventaja clave: **centralización** de logs (un sitio para buscar evidencias).

### 3) Formato de un mensaje Syslog (Cisco típico)

Un mensaje Cisco suele verse así:

```
Sep 16 12:01:01: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to up
```

Desglose:

- `Sep 16 12:01:01:` → timestamp (importante sincronizar con NTP).
- `%LINK-3-UPDOWN:` → `FACILITY-SEVERITY-MNEMONIC`

  - FACILITY = subsistema (ej. LINK, LINEPROTO, OSPF, SYS)
  - SEVERITY = número 0..7 que indica gravedad
  - MNEMONIC = etiqueta breve del evento

- Resto = descripción readable.

**Niveles de severidad (0 → 7)**

0 Emergencia (emergencies)
1 Alerta
2 Crítico
3 Error
4 Advertencia
5 Notificación (notice)
6 Informativo
7 Depuración (debug)

Ej.: `-3-` en `%LINK-3-UPDOWN` indica **Error/Severity 3** (importante).

### 4) Comandos básicos en equipos Cisco (configuración práctica)

Configurar un router/switch Cisco para enviar logs a un servidor 192.168.1.10:

```shell
enable
configure terminal

! Añadir timestamps legibles (recomendado)
service timestamps log datetime msec localtime show-timezone

! Definir servidor syslog (por UDP por defecto)
logging host 192.168.1.10

! Establecer el nivel mínimo de gravedad que se envía al host
logging trap informational

! Registrar mensajes localmente en buffer de la RAM (útil si no hay servidor)
logging buffered 64000 informational

! Forzar que el origen de los mensajes use la IP de una interfaz
logging source-interface GigabitEthernet0/0

end
write memory
```

Explicación rápida:

- `service timestamps...` → incluye fecha/hora con milisegundos y zona horaria (clave para correlacionar eventos).
- `logging host X.X.X.X` → destino de los syslog.
- `logging trap <nivel>` → envía al host solo mensajes de ese nivel o más graves.
- `logging buffered` → guarda logs localmente en buffer (puedes verlos con `show logging`).
- `logging source-interface` → evita que el syslog use IP de interfaz incorrecta.

**Ver estado y buffer:**

```shell
show logging
```

### 5) Ejemplo de `show logging` y qué leer

Salida típica (resumida):

```
Router# show logging
Syslog logging: enabled
    Buffer logging: level informational, 120 messages logged
    Logging to 192.168.1.10
    Logging source-interfaces: GigabitEthernet0/0
    Buffer size (bytes): 64000
    Trap logging: level informational

    *Sep 16 12:01:01.123: %LINK-3-UPDOWN: Interface GigabitEthernet0/1, changed state to up
    *Sep 16 12:02:05.456: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
```

Busca: destino, nivel (trap), interface de origen y mensajes recientes en buffer.

### 6) Montar un **servidor syslog gratuito** (rsyslog en Ubuntu/Debian) — configuración mínima

#### Instalar:

```bash
sudo apt update
sudo apt install rsyslog
```

#### Habilitar receptor UDP 514 (rsyslog v8+):

Crear archivo `/etc/rsyslog.d/50-router.conf` con:

```conf
module(load="imudp")
input(type="imudp" port="514")

# Guardar logs del router 192.168.1.1 en /var/log/router1.log
if $fromhost-ip == '192.168.1.1' then /var/log/router1.log
& stop
```

o, en sintaxis más antigua (si aplica):

```conf
$ModLoad imudp
$UDPServerRun 514
```

Reiniciar:

```bash
sudo systemctl restart rsyslog
sudo tail -f /var/log/router1.log
```

Abrir firewall (si existe):

```bash
sudo ufw allow 514/udp
```

**Consejo:** para producción usa **TCP/TLS** (más seguro y fiable).

### 7) Ejemplos de mensajes y cómo interpretarlos (casos CCNA)

- **Link UP/DOWN (interfaz física)**

  ```
  %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to down
  ```

  → indica caída física; revisar cable/peer.

- **Line protocol (capa 2/3)**

  ```
  %LINEPROTO-5-UPDOWN: Line protocol on Interface Serial0/0, changed state to up
  ```

  → el protocolo de línea rindió OK (ej. vecino OSPF/PPP).

- **Configuración guardada / cambios**

  ```
  %SYS-5-CONFIG_I: Configured from console by admin
  ```

  → cambio de configuración, acción de auditoría.

- **OSPF adjacency**

  ```
  %OSPF-5-ADJCHG: Process 1, Nbr 10.1.1.2 on GigabitEthernet0/0 from FULL to DOWN
  ```

  → pérdida de vecino OSPF; impacto en rutas.

### 8) Buenas prácticas (CCNA / operaciones básicas)

- **Sincroniza la hora con NTP** (`ntp server x.x.x.x`) antes de usar logs.
- **Activar timestamps legibles** (`service timestamps log datetime msec localtime`).
- **Centraliza logs** en un servidor seguro (no sólo buffer local).
- **Usa niveles adecuados**: para monitorización normal `logging trap informational` o `warnings`; para debug solo temporalmente.
- **No abuses de `debug` en producción** (genera mucho log y puede colapsar).
- **Asegura el transporte** en producción (usar TCP/TLS si tu plataforma lo soporta).
- **Rotación y retención** en el servidor syslog (logrotate).
- **Filtrar por host** para separar logs de diferentes equipos en archivos distintos.

### 9) Mini-laboratorio gratuito (Packet Tracer / GNS3)

**Objetivo:** router → server (Syslog) y ver logs en el servidor.

En Packet Tracer:

1. Coloca un **Router** y un **Server** y conéctalos.
2. En el **Server**: clic → _Services_ → _Syslog_ → _On_. (asigna IP 192.168.1.10)
3. En el **Router** (CLI):

   ```shell
   enable
   conf t
   ip domain-name ejemplo.local
   service timestamps log datetime msec
   logging host 192.168.1.10
   logging trap informational
   end
   ```

4. Genera evento: `shutdown` y `no shutdown` en una interfaz y mira en Server → Syslog → ver mensajes.

En GNS3/real:

- Monta una VM Ubuntu con rsyslog como expliqué arriba.
- Aplica la configuración Cisco real vista en la sección de comandos.

### 10) Cheatsheet / comandos rápidos (CCNA)

Cisco CLI:

```
service timestamps log datetime msec localtime show-timezone
logging host 192.168.1.10
logging trap informational
logging buffered 64000 informational
logging source-interface GigabitEthernet0/0
show logging
clear logging
```

rsyslog minimal:

```
sudo apt install rsyslog
# /etc/rsyslog.d/50-router.conf:
module(load="imudp")
input(type="imudp" port="514")
if $fromhost-ip == '192.168.1.1' then /var/log/router1.log
& stop
sudo systemctl restart rsyslog
```

### 11) Por qué esto es relevante para CCNA

- Syslog es parte de **gestión de infraestructura**: monitoring, troubleshooting y registro de cambios — temas que un ingeniero de red debe dominar.
- Entender syslog te ayuda en **resolución de fallos**, en analizar problemas de conectividad y en demostrar buenas prácticas operativas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 28**                | **Siguiente 30**         |
| ------------------ | --------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_28_Event_Logs.md) | [⏩](./13_30_NetFlow.md) |
