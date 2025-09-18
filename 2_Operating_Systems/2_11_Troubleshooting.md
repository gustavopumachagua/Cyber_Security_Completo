| **Inicio**         | **atrás 10**                             | **Siguiente 12**                |
| ------------------ | ---------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_10_Performing_CRUD_on_Files.md) | [⏩](./2_12_Common_Commands.md) |

---

## **Índice**

| Temario                                                                                          |
| ------------------------------------------------------------------------------------------------ |
| [58. Resolución de problemas](#58-resolución-de-problemas)                                       |
| [59. Pasos para la solución de problemas de red](#59-pasos-para-la-solución-de-problemas-de-red) |

---

# **Solución de problemas**

## **58. Resolución de problemas**

### 1. Cómo utilizar **Masscan** para el escaneo de puertos de alta velocidad

**Masscan** es una herramienta de **escaneo de puertos ultrarrápido**, capaz de escanear toda Internet en minutos (similar a Nmap, pero mucho más veloz).

📌 Instalación en Linux (ejemplo en Ubuntu/Debian):

```bash
sudo apt install masscan
```

📌 Ejemplo de uso:

```bash
sudo masscan -p80,443 192.168.1.0/24 --rate=1000
```

- `-p80,443` → Escanea puertos HTTP (80) y HTTPS (443).
- `192.168.1.0/24` → Rango de red local.
- `--rate=1000` → Velocidad de envío de paquetes (1000 p/s).

👉 Resultado: verás qué hosts tienen esos puertos abiertos.

⚠️ Nota: Masscan **solo detecta puertos abiertos** (no dice qué servicio corre en detalle). Puedes combinarlo con **Nmap** para análisis más profundo.

### 2. Cómo usar **Netdiscover** para mapear y solucionar problemas de redes

**Netdiscover** sirve para **descubrir hosts en una red local** usando ARP requests. Es ideal cuando no conoces qué dispositivos están conectados.

📌 Instalación en Linux:

```bash
sudo apt install netdiscover
```

📌 Ejemplo de uso:

```bash
sudo netdiscover -r 192.168.1.0/24
```

- `-r` → define el rango de red.

👉 Resultado: Muestra la **IP**, **MAC** y **fabricante** del dispositivo.
Ejemplo:

```
192.168.1.1   00:1a:2b:3c:4d:5e   Cisco Systems
192.168.1.10  00:0c:29:ab:cd:ef   Dell
```

⚡ Uso práctico:

- Detectar dispositivos sospechosos conectados a tu WiFi.
- Verificar conflictos de IP en una red empresarial.

### 3. **Equipos rojos e IA: 5 formas de utilizar LLM (como ChatGPT) en pentesting**

Los **red teams** simulan ataques para evaluar la seguridad de una empresa. Con **LLMs** pueden:

1. **Automatizar generación de payloads**

   - Ej: usar IA para crear variaciones de comandos SQL Injection y evadir WAFs.

2. **Crear scripts personalizados rápidamente**

   - Ej: pedirle a un LLM un script en Python para fuzzear un formulario web.

3. **Simular ingeniería social avanzada**

   - Ej: redactar correos de phishing más creíbles y adaptados al contexto de la empresa objetivo.

4. **Explicar resultados de herramientas**

   - Ej: interpretar un escaneo Nmap y sugerir posibles exploits.

5. **Entrenar blue teams** (defensores) con escenarios realistas

   - Generar guiones de ataque y defensa para ejercicios de seguridad.

⚠️ Importante: el uso debe estar **controlado y ético**, en entornos de pruebas autorizados.

### 4. Cómo prevenir ataques **DoS** y qué hacer si ocurren

Un ataque **DoS (Denial of Service)** busca **inutilizar un servicio saturándolo**.

✅ **Prevención**:

- Usar **firewalls y rate limiting** (ej: bloquear IPs que envíen demasiadas peticiones).
- Implementar **CDN/servicios anti-DDoS** (ej: Cloudflare, Akamai).
- Mantener servidores actualizados para evitar exploits.
- Configurar **IDS/IPS** (ej: Snort, Suricata) para detectar tráfico malicioso.

📌 Ejemplo en Linux con iptables (limitar conexiones por IP):

```bash
sudo iptables -A INPUT -p tcp --dport 80 -m connlimit --connlimit-above 20 -j DROP
```

👉 Esto limita a 20 conexiones simultáneas por IP en el puerto 80.

✅ **Si ocurre un ataque**:

1. Identificar el tráfico sospechoso (`netstat`, `iftop`, `tcpdump`).
2. Mitigar con firewall o reglas temporales.
3. Escalar al proveedor de Internet (ISP).
4. Si es DDoS grande, usar un **servicio anti-DDoS externo**.

### 5. Cómo recuperarse de un ataque de **ransomware: guía completa**

El **ransomware** cifra tus archivos y pide un rescate.

✅ **Pasos de recuperación**:

1. **Aislar el sistema afectado** → desconectar de la red para evitar propagación.
2. **Identificar la variante** (ej: WannaCry, Ryuk). Herramientas como _ID Ransomware_ pueden ayudar.
3. **No pagar el rescate** (no garantiza recuperación y financia a criminales).
4. **Restaurar desde copias de seguridad** limpias.
5. Si no hay backup: buscar herramientas de descifrado gratuitas (_NoMoreRansom.org_).
6. **Reinstalar el sistema operativo** si no se puede limpiar.
7. **Revisar logs y parches** → detectar cómo entró (phishing, RDP sin seguridad, vulnerabilidades).

✅ **Prevención futura**:

- Mantener backups offline.
- Aplicar parches de seguridad.
- Usar antivirus/EDR con protección contra ransomware.
- Capacitar a usuarios sobre phishing.

📌 Resumen rápido:

- **Masscan** → escaneo rápido de puertos.
- **Netdiscover** → descubrir hosts en la red.
- **LLMs en Red Team** → automatización, payloads, phishing, scripts.
- **DoS** → prevenir con firewalls/CDN, actuar bloqueando tráfico sospechoso.
- **Ransomware** → aislar, no pagar, restaurar backups, aprender de la intrusión.

---

[🔼](#índice)

---

## **59. Pasos para la solución de problemas de red**

### 1) Metodología general (el flujo que siempre aplica)

1. **Definir el problema** — qué, quién, cuándo, alcance (un host / subnet / todos los usuarios).
2. **Recolectar información** — síntomas, logs, configuraciones, topología y capturas.
3. **Aislar el alcance** — ¿es local al equipo, VLAN, switch, router, ISP, aplicación?
4. **Hipótesis y plan de prueba** — qué puede causar el síntoma y cómo probarlo sin riesgo.
5. **Probar y ejecutar acciones correctivas** — aplicar la solución en entorno controlado (staging si es posible).
6. **Verificar** — repetir pruebas y monitoreo a corto plazo.
7. **Documentar y realizar RCA** — causas raíz, mitigaciones, acciones preventivas.

### 2) Mapa mental rápido (OSI como guía)

- **Capa 1 (Física):** cables, fibra, SFPs, PoE, LEDs, conectores.
- **Capa 2 (Enlace):** VLAN, MAC, STP, duplex/velocidad, errores CRC.
- **Capa 3 (Red):** IP, gateway, rutas, ARP, NAT.
- **Capa 4 (Transporte):** TCP/UDP (handshakes, resets, retransmisiones).
- **Capa 7 (Aplicación):** DNS, HTTP, LDAP, autenticación, proxies.

Siempre procede de abajo hacia arriba (Layer 1 → Layer 7) para evitar “parches” innecesarios.

### 3) Diagnóstico paso a paso con comandos y ejemplos

#### A) Paso 0 — preguntas iniciales (rueda rápida)

- ¿Cuál es el síntoma exacto? (p. ej. “no hay internet”, “sitio lento”, “intermitente”)
- ¿A quién afecta? (un usuario, segmento, toda la empresa)
- ¿Cuándo empezó? ¿Cambios recientes (patch, config, cableado, deploy)?
- ¿Hay alertas en monitoring (NMS, Zabbix, CloudWatch)?

#### B) Paso 1 — Comprobar lo obvio (físico y enlace)

**Chequeos físicos**

- Ver LEDs del switch/port y SFP.
- Reemplazar cable y probar otro puerto.
- En Wi-Fi: comprobar señal, SSID y canal.

**Comandos (Linux)**

```bash
ip link show               # ver interfaces y estado (UP/DOWN)
ethtool eth0               # velocidad, duplex, errores y offloads
mii-tool eth0              # (en algunos sistemas) información enlace
```

**Windows**

```powershell
Get-NetAdapter             # estado físico y velocidad
```

**Ejemplo interpretación**

```text
# ethtool eth0
Speed: 1000Mb/s
Duplex: Full
Link detected: yes
```

Si `Link detected: no` → problema físico o SFP mal.

#### C) Paso 2 — IP / configuración de red del host

**Linux**

```bash
ip addr show
ip route show
cat /etc/resolv.conf
```

**Windows**

```cmd
ipconfig /all
route print
nslookup google.com
```

**Chequeos**

- ¿Tiene IP correcta? (estática / DHCP)
- ¿Gateway está presente?
- ¿DNS configurado?
- ¿No hay IP duplicada? → `arp -a`

**Ejemplo: falta de gateway**

```bash
$ ip route
default via 10.0.0.1 dev eth0
```

Si falta `default`, no hay ruta a Internet.

#### D) Paso 3 — Comprobar conectividad básica (ping/traceroute)

1. **Ping a localhost** (stack TCP/IP local)

```bash
ping -c 3 127.0.0.1
```

2. **Ping a IP del gateway**

```bash
ping -c 3 10.0.0.1
```

3. **Ping a IP pública (8.8.8.8)** (aislar DNS)

```bash
ping -c 4 8.8.8.8
```

4. **Traceroute** para ver ruta y saltos problemáticos

- Linux: `traceroute 8.8.8.8` o `tracepath`
- Windows: `tracert 8.8.8.8`
- MTR (mejor para diagnóstico): `mtr -rw 8.8.8.8`

**Interpretación**

- Si ping a gateway falla → problema L1/L2 o config de gateway.
- Si ping a gateway ok pero a 8.8.8.8 falla → probablemente WAN/ISP.
- Si ping IP ok pero `ping google.com` falla → problema DNS.

#### E) Paso 4 — DNS y resolución de nombres

**Comandos**

```bash
nslookup ejemplo.com 8.8.8.8
dig +short ejemplo.com
```

**Qué revisar**

- ¿las consultas responden desde el DNS configurado?
- ¿las respuestas contienen IP correctas?
- ¿timed out o SERVFAIL → servidor DNS caído o ACL bloqueando?

**Ejemplo**

```bash
$ dig +short google.com @8.8.8.8
142.250.190.78
```

#### F) Paso 5 — comprobación de puertos / servicios (TCP)

**Probar puerto específico**

```bash
# Linux
nc -vz host.example.com 443
# Windows (PowerShell)
Test-NetConnection host.example.com -Port 443
# nmap simple
nmap -sT -p 22,80,443 host.example.com
```

**Ejemplo**

```text
$ nc -vz 10.0.0.10 22
Connection to 10.0.0.10 port 22 [tcp/ssh] succeeded!
```

#### G) Paso 6 — captura de paquetes (si es necesario)

**tcpdump (Linux)**

```bash
sudo tcpdump -i eth0 host 10.0.0.5 and port 53 -n -vv
# DHCP capture
sudo tcpdump -i eth0 port 67 or port 68 -n -vv
```

**Wireshark** para análisis GUI: filtrar por `ip.addr==10.0.0.5 && tcp.port==443`.

**Interpretación**

- Verificar SYN/SYN-ACK/ACK para problemas TCP handshake.
- Observar retransmisiones, RST, ICMP unreachable, MTU blackhole (fragmentos y PMTU Path MTU Discovery falla).

#### H) Paso 7 — comprobar el equipo intermedio (switches/routers/firewall)

**Switches (Cisco ejemplos)**

```text
show ip interface brief
show interfaces GigabitEthernet0/1   # mirar errors, CRC, collisions
show mac address-table dynamic interface GigabitEthernet0/1
show vlan brief
show running-config interface Gi0/1   # ver access/trunk config
```

**Routers / Firewalls**

- Ver reglas NAT/ACL, rutas, BGP/OSPF estado.
- Logs de firewall (deny entries).

**Linux firewall**

```bash
sudo iptables -L -n -v
sudo nft list ruleset
sudo firewall-cmd --list-all   # RHEL/fedora (firewalld)
```

#### I) Paso 8 — pruebas de rendimiento y latencia (throughput)

**iperf3** (cliente/servidor)

```bash
# en servidor: iperf3 -s
# en cliente:
iperf3 -c 10.0.0.20 -P 4 -t 30
```

**Interpretación**

- Latencia baja + buen throughput → capa L4 ok.
- Throughput pobre pero ping ok → posible problema MTU, duplex mismatch o QoS.

**Comprobar MTU**

```bash
ip link show eth0
ping -M do -s 1472 8.8.8.8   # probar PMTU (1472+28=1500)
```

**Comprobar errores de interfaz**

```bash
# Cisco
show interfaces Gi0/1 | include errors|CRC|input rate|output rate
# Linux
ethtool -S eth0
```

#### J) Paso 9 — problemas Wi-Fi

**Comprobaciones**

- `iwconfig` / `iw dev wlan0 link` / `iwlist wlan0 scan` (Linux)
- `nmcli device wifi list` (NetworkManager)
- `netsh wlan show interfaces` (Windows)
- `airport -I` (macOS, si disponible)
- Verificar señal (RSSI), SNR, canal y congestión (usar apps como Wi-Fi Analyzer).

**Acciones**

- Cambiar canal si interferencia.
- Mover AP o cliente.
- Actualizar driver/firmware del AP.

### 4) Casos prácticos (scenarios) — pasos y comandos concretos

#### Caso A — “No tengo Internet en mi laptop”

1. Ver LEDs y cable.
2. `ip addr show` → comprobar IP.
3. `ping 127.0.0.1` → `ping gateway` → `ping 8.8.8.8` → `ping google.com`.
4. Si DNS falla, `dig @8.8.8.8 google.com`.
5. `nslookup`/`dig` y `traceroute`.
6. Si gateway no responde, revisar switch: `show ip interface brief` y `show interface` (errors).
7. Captura `tcpdump` en laptop y en gateway si posible.

#### Caso B — “Sitio web lento para algunos usuarios”

1. Identificar patrón: misma subred, ISP, Wi-Fi?
2. `mtr -rw host` desde cliente afectado y desde uno no afectado.
3. `iperf3` entre cliente y servidor para medir throughput.
4. Captura en servidor para ver retransmisiones (`tcpdump tcp port 443`).
5. Revisar CPU/mem en servidor (`top`, `vmstat`) y limitaciones en app (DB slow queries).

#### Caso C — “IPs duplicadas / ARP conflicts”

1. `arp -a` para ver duplicados.
2. `ip addr show` en hosts implicados.
3. En switch: `show mac address-table | include <mac>` para ver puerto físico.
4. Corregir configuración (DHCP scope, static IPs) y reboot si necesario.

### 5) Herramientas recomendadas (resumen)

- **Linux:** ip, ss, iperf3, tcpdump, ethtool, mtr, traceroute, dig, nslookup, nmap.
- **Windows:** ipconfig, ping, tracert, pathping, Test-NetConnection (PowerShell), netsh, Wireshark.
- **Switch/Router vendor CLI:** show interface, show ip route, show logging.
- **GUI/packet analysis:** Wireshark.
- **Monitoring:** Zabbix, Prometheus, Grafana, NMS (SolarWinds, PRTG).

### 6) Automatizar un chequeo inicial (script básico)

Guarda como `netcheck.sh` y ejecútalo (Linux).

```bash
#!/usr/bin/env bash
set -e
IF=${1:-eth0}
GATEWAY=$(ip route show default | awk '/default/ {print $3}')
echo "Interface: $IF"
ip addr show "$IF"
echo "Default gateway: $GATEWAY"
echo "Testing local stack..."
ping -c 3 127.0.0.1
echo "Testing gateway..."
ping -c 3 "$GATEWAY"
echo "Testing public IP 8.8.8.8..."
ping -c 3 8.8.8.8
echo "Testing DNS resolution..."
dig +short google.com @8.8.8.8 || dig +short google.com
echo "Traceroute to 8.8.8.8"
traceroute -m 15 8.8.8.8 || true
echo "Top TCP connections"
ss -tupn | head -n 20
echo "Done."
```

(Ejecuta con `sudo` si quieres capturas tcpdump adicionales.)

### 7) Escalamiento: cuándo y qué información recolectar

Si no puedes resolver, escala a NOC/ISP/soporte vendor. Al escalar, lleva:

- Descripción del problema y alcance (hosts afectados).
- Timestamp del incidente (UTC) y window.
- Topología y diagrama (segmentos, dispositivos, IPs).
- Capturas (`.pcap`), logs de firewall, salida de `ip addr`, `route`, `show run` (switch/router), output de `mtr`/`traceroute`.
- Cambios recientes (deploys, patches, configuraciones).

### 8) Post-mortem / RCA (plantilla rápida)

- **Resumen del incidente** — qué, cuándo, impacto.
- **Causa raíz** — falla física, config incorrecta, bug app, saturación, ataque.
- **Línea temporal** — eventos y acciones con timestamps.
- **Medidas tomadas** — mitigación inmediata y permanente.
- **Lecciones aprendidas** — cómo prevenir (monitoring, playbooks, redundancia).
- **Acciones pendientes** — owners y fechas.

### 9) Checklist de acciones rápidas (priorizada)

1. Confirmar alcance (1 host / subnet / global).
2. Ver LEDs, cable, SFP, AP.
3. Comprobar `ip addr` y `ip route`.
4. Ping a gateway → ping a 8.8.8.8 → ping a DNS → traceroute.
5. Probar puerto con `nc`/`curl`.
6. Captura tcpdump (si aplicable).
7. Revisar errores en switch (`show interface`) y logs firewall.
8. Reiniciar interface/servicio si procede (`ip link set dev eth0 down/up`, `systemctl restart NetworkManager`).
9. Si es WAN: contactar ISP con traceroute y capturas.
10. Documentar todo y hacer RCA.

### 10) Buenas prácticas preventivas

- Monitoreo proactivo (latencia, packet loss, interface errors).
- Backups de configuraciones y control de cambios (git para configs).
- Segmentación y políticas claras (VLANs, ACLs).
- Tests de rendimiento periódicos (iperf, synthetic checks).
- Procedimientos y runbooks para incidentes y escalamiento.
- Mantener firmware/OS actualizados y plan de ventanas de mantenimiento.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**                             | **Siguiente 12**                |
| ------------------ | ---------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_10_Performing_CRUD_on_Files.md) | [⏩](./2_12_Common_Commands.md) |
