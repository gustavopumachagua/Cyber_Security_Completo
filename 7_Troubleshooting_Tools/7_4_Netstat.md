| **Inicio**         | **atrás 3**        | **Siguiente 5**      |
| ------------------ | ------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./7_3_Dig.md) | [⏩](./7_5_Route.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [165. Comando NetStat](#165-comando-netstat)                                 |
| [166. Explicación del comando NetStat](#166-explicación-del-comando-netstat) |

# **NetStat**

## **165. Comando NetStat**

![NetStat](/img/7_Troubleshooting_Tools/NetStat.jpg "NetStat")

### ¿Qué es `netstat`?

`netstat` (network statistics) es una herramienta de línea de comandos para mostrar **conexiones de red**, **puertos abiertos**, **tablas de enrutamiento**, **estadísticas por protocolo** y **estadísticas de interfaces**. Muy útil para diagnóstico de redes y hallar procesos que consumen sockets.

> Nota: en Linux `netstat` proviene del paquete `net-tools` y está parcialmente deprecado; hoy se recomienda usar `ss` y `ip` (`iproute2`). Igual `netstat` sigue siendo muy usado y está disponible en Windows y macOS.

### Conceptos clave que verás en la salida

- **Proto / Protocol**: `tcp`, `udp`, `tcp6`, `udp6`.
- **Local Address**: IP y puerto local (ej. `0.0.0.0:22` o `127.0.0.1:8080`).
- **Foreign Address**: IP/puerto remoto con el que hay comunicación.
- **State** (TCP): `LISTEN`, `ESTABLISHED`, `SYN_SENT`, `SYN_RECV`, `FIN_WAIT1`, `TIME_WAIT`, `CLOSE_WAIT`, etc.
- **Recv-Q / Send-Q** (Linux): colas de recep/envío; valores altos indican congestión o aplicación que no lee.
- **PID/Program name** (Linux `-p` o Windows `-o`): ID del proceso que posee el socket.
- **TTL / timers** en algunas plataformas.

### Sintaxis general / parámetros útiles

#### Linux (netstat clásico)

```bash
netstat [opciones]
# combinaciones típicas:
netstat -tulpn        # mostrar TCP/UDP listening, con PID/programa (requiere sudo)
netstat -an           # todo con direcciones numéricas
netstat -s            # estadísticas por protocolo
netstat -r            # tabla de enrutamiento
netstat -i            # estadísticas por interfaz
netstat -c            # refresco continuo (en Linux)
```

#### Windows (PowerShell / CMD)

```bat
netstat [-a] [-n] [-o] [-b] [-r] [-s]
# ejemplos:
netstat -ano          # todas las conexiones con PID (numérico)
netstat -b            # muestra ejecutables (requiere cmd admin)
netstat -r            # tabla de enrutamiento (equiv. route print)
netstat -s            # estadísticas por protocolo
```

#### macOS (netstat)

```bash
netstat -an           # similar a Linux
# para PID/use lsof en macOS:
lsof -i -P -n
```

#### Alternativa moderna en Linux

```bash
ss -tulpn             # muestra sockets con PID; reemplaza netstat -tulpn
ss -s                 # resumen de sockets
```

### Ejemplos concretos y explicación

#### 1) ¿Qué está escuchando en mi máquina? (puertos abiertos)

**Linux**

```bash
sudo netstat -tulpn
# salida ejemplo:
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1234/sshd
tcp6       0      0 :::80                   :::*                    LISTEN      2345/nginx
udp        0      0 0.0.0.0:68              0.0.0.0:*                           987/dhclient
```

- `0.0.0.0:22 LISTEN` → sshd escucha en todas las IPs en el puerto 22.
- `:::80` → nginx escucha IPv6/IPv4-mapped en 80.

**Windows**

```bat
netstat -ano | findstr :3389
# muestra PID; luego obtén el proceso:
tasklist /fi "PID eq 4321"
# o en PowerShell:
Get-Process -Id 4321
```

#### 2) ¿Con qué hosts remotos estoy conectado?

**Linux**

```bash
netstat -ant | grep ESTABLISHED
# o con ss
ss -tn state established
```

Salida ejemplo:

```
tcp 0 0 192.168.1.10:56344 93.184.216.34:443 ESTABLISHED 3456/firefox
```

- Indica una conexión HTTPS establecida entre tu puerto efímero 56344 y 93.184.216.34:443 (firefox PID 3456).

#### 3) ¿Qué proceso usa el puerto 80?

**Linux**

```bash
sudo netstat -tulpn | grep :80
# alternativamente:
sudo ss -ltnp | grep :80
```

**Windows**

```bat
netstat -ano | findstr :80
# luego:
tasklist /fi "PID eq <pid>"
```

#### 4) Ver estadísticas por protocolo (tráfico, errores)

**Linux**

```bash
netstat -s
# salida: counters para TCP, UDP, ICMP, etc. — útil para detectar retransmisiones y errores.
```

**Windows**

```bat
netstat -s
```

#### 5) Tabla de enrutamiento

**Linux / macOS**

```bash
netstat -rn
# o con iproute:
ip route show
```

**Windows**

```bat
netstat -r
# o route print
```

### Interpretación de estados TCP importantes

- **LISTEN**: socket esperando conexiones entrantes.
- **SYN_SENT / SYN_RECV**: fase de establecimiento (three-way handshake).
- **ESTABLISHED**: conexión activa y usable.
- **FIN_WAIT1 / FIN_WAIT2 / TIME_WAIT**: fase de cierre; `TIME_WAIT` es normal (protección contra paquetes retrasados).
- **CLOSE_WAIT**: el peer cerró la conexión; proceso local debe cerrar su socket — si se queda mucho tiempo indica que la app no está cerrando correctamente.
- **LAST_ACK**: esperando ACK final.

Si ves muchos `TIME_WAIT` no suele ser grave; si hay muchos `CLOSE_WAIT` investigue la aplicación.

### Uso de netstat para seguridad y detección de intrusiones

- **Detectar conexiones sospechosas**: mira conexiones `ESTABLISHED` hacia IPs extrañas en puertos no comunes.
- **Puertos expuestos**: `LISTEN` en `0.0.0.0` puede indicar servicio accesible desde Internet.
- **Ver procesos que abren sockets** (PID): identifica servicios maliciosos.
- **Monitorizar cambios**: ejecutar `netstat -tulpn` periódicamente y comparar salidas en scripts.
- **Bloquear o cerrar**: si hallas proceso sospechoso, obtén PID y finaliza proceso (`kill -9 PID` en Linux, `taskkill /PID <pid> /F` en Windows), y luego revisa persistencia (cron, systemd, startup).

Ejemplo: detectar proceso extraño y matarlo (Linux)

```bash
sudo netstat -tulpn | grep ESTABLISHED
# supongamos PID 7777 es sospechoso
sudo ps -fp 7777
sudo kill 7777         # intenta cierre limpio
sudo kill -9 7777      # forzar si no responde
```

### Diagnóstico práctico de problemas comunes

#### A) No puedo conectar a mi servidor web (port 80)

1. `sudo ss -ltnp | grep :80` → ¿hay proceso en LISTEN?
2. `sudo netstat -rn` → ¿gateway correcto?
3. `sudo iptables -L -n` o `ufw status` → ¿firewall bloqueando?
4. `curl -v http://127.0.0.1:80` → ¿el servicio responde localmente?
5. Desde otro host: `telnet servidor 80` o `nc -vz servidor 80`.

#### B) Muchas conexiones TIME_WAIT

- Es normal bajo carga. Para altas conexiones cortas, usar opciones SO_REUSEADDR, ajustar kernel TCP settings (solo en casos avanzados) o usar load balancer.

#### C) Conexiones en CLOSE_WAIT persistentes

- Indica que la aplicación no está cerrando sockets; reinicia proceso o arregla bug en app.

### Comandos avanzados / combinaciones útiles

- **Monitoreo continuo (Linux)**:

```bash
watch -n 1 'ss -tupa | wc -l'     # cuenta sockets dinamicamente
watch -n 1 'ss -tn state established'
```

- **Listar conexiones por proceso y ordenar por número de conexiones**:

```bash
ss -tanp | awk '{print $6}' | sort | uniq -c | sort -nr
```

- **Scripting: comprobar si puerto está en uso y salir con código**:

```bash
#!/bin/bash
PORT=8080
if ss -ltn "( sport = :$PORT )" | grep -q LISTEN; then
  echo "Puerto $PORT en uso"; exit 1
else
  echo "Puerto libre"; exit 0
fi
```

- **Ver conexiones y resolver nombres (más legible pero más lento)**:

```bash
netstat -antp  # con -n evita resolución DNS; quitar -n para ver nombres
```

### Limitaciones y alternativas

- **Necesitas privilegios** para ver PID/Program (`sudo` o admin).
- **En Linux netstat está deprecado** -> usa `ss` y `ip`.
- **macOS**: `netstat` existe pero no siempre muestra PID; usa `lsof -i -P -n`.
- Para captura de tráfico y análisis profundo usa `tcpdump` o Wireshark.

### Ejemplos rápidos de referencia

**Comprobar puertos escuchando (Linux):**

```bash
sudo ss -tulpn
sudo netstat -tulpn
```

**Mostrar solo conexiones establecidas:**

```bash
ss -tn state established
netstat -ant | grep ESTABLISHED
```

**Ver todo con PID en Windows:**

```bat
netstat -ano
tasklist /FI "PID eq 4321"
```

**Mostrar estadísticas TCP/UDP:**

```bash
netstat -s
ss -s
```

**Ver interfaces y paquetes:**

```bash
netstat -i
```

### Buenas prácticas

- Ejecuta `netstat/ss` con `sudo` para obtener información completa.
- Usa `-n` para evitar retrasos por resolución DNS si solo te interesan IPs/puertos.
- Automatiza comprobaciones básicas (puerto en uso, escucha) con scripts y alertas.
- Complementa con `lsof`, `tcpdump`, `iptables/ufw` y herramientas SIEM para seguridad.
- Cuando migras a Linux moderno aprende `ss` y `ip` (más rápido y completo).

---

[🔼](#índice)

---

## **166. Explicación del comando NetStat**

### Explicación del comando `netstat` (detallada, con ejemplos)

**`netstat`** (network statistics) es una utilidad de línea de comandos para **inspeccionar el estado de la red** en un equipo: conexiones activas, puertos en escucha, tablas de enrutamiento, estadísticas por protocolo e incluso qué proceso (PID) tiene abierto cada socket. Es una de las primeras herramientas que usa un administrador para diagnosticar problemas de red o detectar servicios expuestos.

> Nota: en Linux moderno `netstat` proviene del paquete **net-tools** y está parcialmente deprecado en favor de `ss` / `ip` (iproute2). Aun así `netstat` sigue siendo ampliamente usada y existe en Windows y macOS.

### Sintaxis y opciones comunes

Sintaxis general (varía según SO):

```bash
netstat [opciones]
```

Opciones muy útiles (Linux / macOS / Windows):

- `-a` : mostrar todas las conexiones (escuchando y establecidas).
- `-n` : no resolver nombres (mostrar direcciones y puertos numéricos).
- `-t` : (Linux) mostrar sólo TCP.
- `-u` : (Linux) mostrar sólo UDP.
- `-l` : (Linux) mostrar sólo sockets en LISTEN (puertos que escuchan).
- `-p` : (Linux) mostrar PID/Program name (requiere `sudo`).
- `-o` : (Windows) mostrar PID con cada conexión.
- `-r` : mostrar tabla de enrutamiento (equivalente a `route`).
- `-s` : mostrar estadísticas por protocolo (TCP, UDP, ICMP, etc.).
- `-i` : estadísticas por interfaz.
- `-c` : refresco continuo (Linux).

Combinaciones frecuentes:

```bash
# Linux: ver puertos que escuchan con PID (necesita sudo)
sudo netstat -tulpn

# Alternativa para conexiones TCP activas (numéricas)
netstat -ant

# Todas las conexiones con PID en Windows
netstat -ano
```

### Qué muestra la salida y cómo interpretarla

Ejemplo típico (Linux: `sudo netstat -tulpn`):

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1234/sshd
tcp6       0      0 :::80                   :::*                    LISTEN      2345/nginx
udp        0      0 0.0.0.0:68              0.0.0.0:*                           987/dhclient
```

Columnas clave:

- **Proto**: `tcp`, `udp`, `tcp6`, `udp6`.
- **Local Address**: IP\:puerto local. `0.0.0.0:22` = escucha en todas las interfaces.
- **Foreign Address**: IP\:puerto remoto (para `LISTEN` aparece `*`).
- **State**: estado TCP (LISTEN, ESTABLISHED, TIME_WAIT, CLOSE_WAIT, etc.).
- **PID/Program name**: proceso dueño del socket (requiere privilegios).

Explicación de estados TCP más comunes:

- **LISTEN**: socket esperando conexiones entrantes.
- **ESTABLISHED**: conexión activa entre ambos extremos.
- **SYN_SENT / SYN_RECV**: fase de establecimiento (handshake).
- **FIN_WAIT1 / FIN_WAIT2 / TIME_WAIT**: fases de cierre (TIME_WAIT protege contra paquetes retrasados).
- **CLOSE_WAIT**: peer cerró; la aplicación local debe cerrar (si persiste indica bug).

En Windows `netstat -ano` mostrará columnas similares y el PID numérico; puedes mapear PID a proceso con `tasklist /FI "PID eq 1234"` o en PowerShell `Get-Process -Id 1234`.

### Ejemplos prácticos por sistema operativo

#### Linux

1. **Puertos escuchando con PID (habitual)**:

```bash
sudo netstat -tulpn
# equivalente moderno:
sudo ss -tulpn
```

2. **Ver sólo conexiones TCP establecidas**:

```bash
netstat -ant | grep ESTABLISHED
# o con ss
ss -tn state established
```

3. **Contar conexiones establecidas**:

```bash
netstat -ant | grep ESTABLISHED | wc -l
```

4. **Tabla de enrutamiento**:

```bash
netstat -rn
# o
ip route show
```

5. **Estadísticas por protocolo**:

```bash
netstat -s
```

#### Windows

1. **Ver todas las conexiones y PID**:

```bat
netstat -ano
```

2. **Encontrar qué proceso usa el puerto 3389 (RDP)**:

```bat
netstat -ano | findstr :3389
tasklist /FI "PID eq <pid>"
```

3. **Mostrar programa ejecutable (requiere privilegios)**:

```bat
netstat -b
```

#### macOS

`netstat` funciona, pero para ver PID asociado normalmente se usa `lsof`:

```bash
# ver puertos escuchando
netstat -an | grep LISTEN

# ver qué proceso escucha en puerto 80 (mac)
sudo lsof -iTCP:80 -sTCP:LISTEN -P -n
```

### Casos de uso reales y flujos de diagnóstico

#### Caso A — “Mi web no responde”

1. En el servidor: ¿hay un proceso escuchando en el puerto 80/443?

```bash
sudo ss -ltnp | grep :80
```

2. Si no aparece: el servicio (nginx/apache) no está iniciado; revisar `systemctl status nginx`.
3. Si aparece escuchando sólo en `127.0.0.1:80`, no está accesible desde LAN → cambiar bind o firewall.
4. Si está escuchando y sigue sin responder desde otro host, comprobar firewall (`iptables`, `ufw`) y la tabla de enrutamiento (`netstat -rn`).

#### Caso B — “Mi aplicación pierde conexiones” (muchos TIME_WAIT)

- `netstat -ant | grep TIME_WAIT | wc -l` → si hay demasiados, es síntoma de muchas conexiones cortas o configuraciones TCP. TIME_WAIT es normal; ajustes avanzados pueden reducir su impacto (SO_REUSEADDR, tuning kernel), pero cambiar sin entender puede causar problemas.

#### Caso C — “Detecté conexiones sospechosas”

1. `netstat -antp | grep ESTABLISHED` para listar conexiones activas con PID.
2. Mapear PID a proceso (`ps -fp <pid>`), revisar binario y ruta.
3. Si es malicioso: detener proceso, investigar persistencia (cron, systemd unit, startup) y revisar logs.

### Combinaciones útiles y filtrado (Linux)

- Mostrar sólo puertos TCP escuchando y la aplicación:

```bash
sudo netstat -tulpn | grep LISTEN
```

- Mostrar conexiones ordenadas por cantidad por IP remota:

```bash
ss -tnp | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head
```

- Ver procesos que generan tráfico UDP:

```bash
sudo ss -unp
```

### `netstat` en scripts — ejemplos

#### Bash: alerta si un puerto está en uso

```bash
#!/bin/bash
PORT=8080
if ss -ltn "( sport = :$PORT )" | grep -q LISTEN; then
  echo "Puerto $PORT en uso"
  exit 1
else
  echo "Puerto $PORT libre"
  exit 0
fi
```

#### Windows (batch): log de conexiones fallidas

```bat
@echo off
netstat -ano > %TEMP%\netstat_snapshot.txt
type %TEMP%\netstat_snapshot.txt | findstr ESTABLISHED > %TEMP%\established.txt
REM enviar por mail o analizar
```

### Netstat para seguridad y auditoría

- **Descubrir puertos expuestos**: `LISTEN` en `0.0.0.0` indica servicio accesible desde cualquier interfaz.
- **Identificar procesos sospechosos** usando PID/Program.
- **Detectar backdoors**: conexiones persistentes a IPs desconocidas.
- **Auditoría periódica**: ejecutar `netstat -tulpn` periódicamente y comparar salidas (watch + diff) para detectar cambios inesperados.

### Limitaciones y alternativas modernas

- En Linux **`netstat` está envejeciendo**; herramientas modernas:

  - `ss` — muestra sockets, más rápido y flexible.
  - `ip` (iproute2) — reemplaza muchas funcionalidades de `netstat -r`, `ifconfig`.
  - `lsof -i` — lista procesos con archivos de red (útil en macOS).

- En Windows moderno: `Get-NetTCPConnection` (PowerShell) y `Get-Process` para mapear PID.

Equivalentes rápidos:

```bash
# netstat -tulpn
sudo ss -tulpn

# netstat -ant | grep ESTABLISHED
ss -tn state established
```

### Buenas prácticas y observaciones

- **Ejecutar con privilegios** para ver PID/Program (`sudo` en Linux; CMD como administrador en Windows).
- **Usar `-n`** cuando no necesites resolución DNS (evita retardos).
- **Combinar con otras herramientas**: `tcpdump`/`wireshark` para análisis de paquetes, `ps`/`tasklist` para investigar procesos.
- **No matar procesos sin investigar**: cerrar un proceso puede afectar servicios críticos.
- **Monitoreo continuo**: integrar checks en Nagios/Zabbix/Prometheus para alertas sobre puertos abiertos o cambios.

### Resumen rápido (cheat-sheet)

- `sudo netstat -tulpn` → ver puertos en escucha con PID (Linux).
- `netstat -ant` → ver conexiones TCP numéricas.
- `netstat -ano` → en Windows, ver PID de cada conexión.
- `netstat -rn` → tabla de enrutamiento.
- `netstat -s` → estadísticas por protocolo.
- `ss -tulpn` → alternativa moderna a `netstat -tulpn`.
- `lsof -iTCP:80 -sTCP:LISTEN` → qué proceso escucha en el puerto 80 (macOS/Linux).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**        | **Siguiente 5**      |
| ------------------ | ------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./7_3_Dig.md) | [⏩](./7_5_Route.md) |
