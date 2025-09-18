| **Inicio**         | **atrás 10**             | **Siguiente 12**              |
| ------------------ | ------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./7_10_Iptables.md) | [⏩](./7_12_Port_Scanners.md) |

---

## **Índice**

| Temario                                                                              |
| ------------------------------------------------------------------------------------ |
| [180. Explicación del rastreo de paquetes](#180-explicación-del-rastreo-de-paquetes) |
| [181. ¿Qué es el rastreo de paquetes?](#181-qué-es-el-rastreo-de-paquetes)           |

# **Packet Sniffers**

## **180. Explicación del rastreo de paquetes**

![Packet Sniffers](/img/7_Troubleshooting_Tools/Packet_Sniffers.webp "Packet Sniffers")

### 🔹 1. ¿Qué es el rastreo de paquetes?

El **rastreo de paquetes** (packet sniffing) es el proceso de **capturar y analizar** los datos que viajan en una red.
Un **rastreador de paquetes** (packet sniffer) es un software o hardware que intercepta el tráfico que pasa por una tarjeta de red.

Ejemplos de herramientas:

- **Wireshark** → análisis visual y completo del tráfico.
- **tcpdump** → captura en terminal.
- **Ettercap** → usado para sniffing y ataques MITM.

👉 Sirve tanto para **administradores de red** (monitorear rendimiento, detectar fallos, diagnosticar) como para **hackers** (interceptar contraseñas, cookies, etc.).

### 🔹 2. ¿Es legal el rastreo de paquetes?

✅ **Sí, es legal** cuando:

- Lo haces en tu propia red.
- Lo haces con autorización explícita (ejemplo: un pentest contratado).
- Es parte de la monitorización de seguridad en una organización.

❌ **Es ilegal** cuando:

- Capturas tráfico en redes ajenas sin permiso.
- Interceptas datos personales o sensibles (contraseñas, mensajes, tarjetas de crédito).

  En muchos países esto se considera **interceptación ilegal de comunicaciones**.

### 🔹 3. ¿Cómo funciona el rastreo de paquetes?

1. El sniffer pone la **tarjeta de red** en modo _promiscuo_ o _monitor_.

   - Normal: solo lee paquetes destinados a tu máquina.
   - Promiscuo: captura **todo el tráfico** que pasa por la red.

2. El software (ej. tcpdump, Wireshark) guarda los paquetes en un archivo `.pcap`.
3. El analista puede ver:

   - Encabezados (IP origen/destino, puertos, protocolo).
   - Payload (contenido, si no está cifrado).

Ejemplo con `tcpdump`:

```bash
sudo tcpdump -i eth0 -w captura.pcap
```

👉 Esto guarda los paquetes en `captura.pcap` que luego puedes abrir en Wireshark.

### 🔹 4. ¿Qué es un ataque de rastreo de paquetes?

Un ataque de rastreo de paquetes ocurre cuando un atacante **usa un sniffer sin autorización** para:

- Capturar tráfico sensible.
- Obtener contraseñas en texto claro (ej. FTP, Telnet, HTTP sin HTTPS).
- Robar cookies de sesión.
- Mapear la red de la víctima.

En esencia: es espionaje digital del tráfico de red.

### 🔹 5. ¿Cómo funciona un ataque de rastreo de paquetes?

Generalmente en 3 pasos:

1. **Acceso a la red**: el atacante entra a la red local (WiFi sin cifrado, acceso físico o MITM).
2. **Captura de tráfico**: pone su tarjeta en modo promiscuo y empieza a guardar paquetes.
3. **Análisis**: filtra tráfico útil (ejemplo: credenciales de correo o tráfico HTTP).

Ejemplo práctico de ataque:

- En un WiFi público sin WPA2, un atacante puede abrir Wireshark y filtrar:

  ```
  http.request.method == "POST"
  ```

  👉 Puede ver contraseñas enviadas en formularios web no cifrados.

### 🔹 6. ¿Por qué los hackers utilizan rastreadores de paquetes?

- **Robar credenciales** (FTP, Telnet, HTTP, POP3 en texto plano).
- **Capturar sesiones** (cookies → session hijacking).
- **Recolectar información de red** (IPs, puertos, servicios).
- **Explotar vulnerabilidades** detectando tráfico inseguro.
- **Ataques MITM** (en combinación con ARP spoofing).

### 🔹 7. Ejemplos de ataques de rastreo de paquetes

- **Robo de contraseñas en Telnet/FTP**: estos protocolos no cifran.
- **Captura de cookies HTTP**: con Firesheep (addon de Firefox).
- **Escucha en redes WiFi abiertas**: todo el tráfico puede ser interceptado.
- **MITM con Ettercap**: el atacante se interpone y registra todo el tráfico.

Ejemplo real:

Un hacker en una cafetería con WiFi abierto ejecuta:

```bash
ettercap -T -M arp /victima/ /router/
```

👉 Redirige tráfico víctima ↔ router y captura datos sensibles.

### 🔹 8. ¿Cuál es la mejor defensa contra el rastreo de paquetes?

1. **Cifrado en tránsito**:

   - Usar **HTTPS** en vez de HTTP.
   - Usar **SSH** en vez de Telnet.
   - VPN para todo el tráfico en redes públicas.
   - WPA3 o WPA2 en WiFi (evitar redes abiertas).

2. **Segmentación de red**:

   - VLANs para separar tráfico sensible.

3. **Detección de sniffers**:

   - Comando de prueba ARP (ver si alguien responde anómalamente).
   - Herramientas IDS (Snort, Suricata).

4. **Monitorización activa**:

   - Revisar con `arp-scan`, `nmap -sS` si hay dispositivos extraños.

5. **Concienciación de usuario**:

   - Evitar ingresar credenciales en sitios sin HTTPS.
   - Usar autenticación multifactor (MFA).

✅ En resumen:

El **rastreo de paquetes** es una herramienta poderosa para la administración de redes, pero cuando se usa sin autorización se convierte en un **ataque pasivo de espionaje digital**.

Los hackers lo utilizan para **robar credenciales y sesiones**, y la mejor defensa es el **cifrado extremo a extremo (HTTPS, SSH, VPN)** y **no usar redes abiertas sin protección**.

---

[🔼](#índice)

---

## **181. ¿Qué es el rastreo de paquetes?**

**Resumen corto:** el _rastreo de paquetes_ (o _packet sniffing_) es el acto de **capturar** y **analizar** los paquetes que circulan por una red para inspeccionar sus encabezados y, si no están cifrados, su contenido. Es una técnica fundamental para administración y seguridad de redes —pero también puede usarse para espiar—, por eso su uso requiere autorización.

### 1) Qué captura un sniffer (qué es un “paquete”)

Un _paquete_ es la unidad de datos que viaja por la red. Un sniffer puede registrar:

- **Capa 2 (Ethernet/Wi-Fi):** direcciones MAC, tipo de protocolo, VLAN tag.
- **Capa 3 (IP):** IP origen/destino, TTL, fragmentación.
- **Capa 4 (TCP/UDP/ICMP):** puertos, flags TCP (SYN/ACK), números de secuencia.
- **Capa 7 (aplicación):** payload (HTTP, DNS, SMTP, etc.) si no está cifrado.

Los sniffers guardan esto en archivos `.pcap` o `.pcapng` para analizar después.

### 2) ¿Cómo funciona técnicamente? (pasos clave)

1. **Acceso físico/lógico a la red.** Debes tener visibilidad del tráfico (estar en la misma LAN, en una interfaz mirror/span, tener acceso a un TAP, o en Wi-Fi en modo monitor).
2. **Poner la NIC en modo promiscuo o monitor:**

   - _Promiscuo_ (Ethernet): la tarjeta acepta todos los frames Ethernet que vea.
   - _Monitor_ (Wi-Fi): captura tramas Wi-Fi (management/beacon/data), incluso sin estar asociada.

3. **Librería de captura (libpcap/Npcap).** Herramientas como `tcpdump` o Wireshark usan libpcap (Linux/macOS) o Npcap/WinPcap (Windows) para leer paquetes a bajo nivel.
4. **Filtros BPF (capture filters).** Antes de escribir a disco se puede especificar qué paquetes aceptar (ej. `tcp port 80`). BPF = Berkeley Packet Filter.
5. **Escritura y análisis.** Paquetes se guardan en `.pcap` y se analizan con Wireshark, tshark, Scapy, Zeek, etc.

### 3) Modos y herramientas comunes

- **Modo promiscuo** — para Ethernet; captura todo lo que llega a la interfaz.
- **Modo monitor** — para Wi-Fi; captura tramas inalámbricas (incluye management).
- **Herramientas:**

  - _Wireshark_ (GUI, análisis visual, muy completo).
  - _tcpdump_ (CLI, captura y filtros BPF).
  - _tshark_ (CLI, la versión terminal de Wireshark).
  - _ngrep_ (buscar cadenas en payload).
  - _Scapy_ (Python, crear/analizar paquetes).
  - _Zeek / Suricata_ (análisis/IDS en red).

### 4) Ejemplos prácticos (comandos seguros y educativos)

> **Advertencia legal:** solo captures tráfico en tu propia red o con autorización. Los ejemplos muestran capturas sobre _tu equipo_ o _tus hosts_.

#### Capturar en loopback (aprendizaje, no afecta a terceros)

```bash
# Captura 100 paquetes en la interfaz loopback y guarda en pcap
sudo tcpdump -i lo -c 100 -s 0 -w loopback.pcap
```

#### Capturar todo el tráfico de tu IP (evita capturar de terceros)

```bash
# Reemplaza 192.168.1.10 por tu IP
sudo tcpdump -i eth0 host 192.168.1.10 -s 0 -w myhost.pcap
```

#### Capturar solo HTTP (puerto 80) — filtro BPF (capture filter)

```bash
sudo tcpdump -i eth0 'tcp port 80' -s 0 -w http_traffic.pcap
```

#### Leer un pcap y mostrar solicitudes HTTP (tshark)

```bash
tshark -r http_traffic.pcap -Y http.request -T fields -e ip.src -e http.host -e http.request.uri
```

#### Abrir pcap en Wireshark (GUI)

```bash
wireshark http_traffic.pcap
```

En Wireshark puedes aplicar _display filters_ (ej. `http && ip.addr == 192.168.1.10`) —nota: _display filters_ son diferentes de los filtros BPF de captura.

### 5) Ejemplo de interpretación — línea típica de tcpdump

Salida:

```
12:34:56.789012 IP 192.168.1.10.45678 > 93.184.216.34.80: Flags [S], seq 123456789, win 64240, length 0
```

Explicación:

- `12:34:56.789012` → timestamp.
- `IP` → protocolo de red.
- `192.168.1.10.45678` → IP origen y puerto.
- `>` → dirección de flujo.
- `93.184.216.34.80` → IP destino y puerto (80 = HTTP).
- `Flags [S]` → SYN (inicio de conexión TCP).
- `seq 123456789` → número de secuencia.
- `win 64240` → ventana TCP.

### 6) ¿Para qué se usa legítimamente el rastreo de paquetes?

- **Diagnóstico / troubleshooting** (latencia, retransmisiones, rutas).
- **Análisis de rendimiento** (bandwidth, congestión).
- **Desarrollo de protocolos y debugging de aplicaciones**.
- **Forense de red** (investigar incidentes).
- **Monitoreo y seguridad** (Gestión de IDS/IPS, reglas de detección).
- **Auditoría y cumplimiento** (recolección de evidencia, análisis de logs).

### 7) Riesgos y usos maliciosos (qué pueden hacer los atacantes)

- **Capturar credenciales en texto plano** (FTP, Telnet, HTTP, POP3 sin TLS).
- **Robar cookies de sesión** y secuestrar sesiones (session hijacking).
- **Mapeo de red**: descubrir hosts, servicios y topología.
- **Recolectar información sensible** para ataques posteriores.
- **Combinado con MITM** (p. ej. ARP spoofing) puede alterar y reenviar tráfico.

> Nota: la mayoría de estos ataques fallan si el tráfico está cifrado (HTTPS, TLS, SSH, VPN).

### 8) ¿Cómo detectar que alguien está rastreando paquetes en tu red?

Detectar sniffers pasivos no es trivial, pero hay indicadores:

- **ARP spoofing / anomalías ARP** → herramientas: `arpwatch`, `arping`, `arp-scan`.
- **Dispositivos con interfaces en modo promiscuo** — hay técnicas para detectarlo (enviando paquetes con destino distinto y verificando respuestas), aunque no son 100% fiables y pueden generar falso positivos.
- **IDS/IPS** (Snort, Suricata) pueden detectar patrones de MITM o escaneo.
- **Monitor de tráfico / contadores**: picos inusuales en tráfico por switch port.
- **Switches gestionables**: usar _port mirroring_ y reglas de port-security para detectar puertos que pasan tráfico de muchas MACs.

### 9) Prevención: mejores defensas contra el rastreo de paquetes

1. **Cifrado de extremo a extremo / en tránsito**

   - HTTPS (HSTS), TLS para servicios, SSH para administración.
   - Usar TLS 1.2/1.3 y suites seguras.

2. **VPN en redes públicas** — cifra todo el tráfico del cliente.
3. **Wi-Fi seguro** — WPA2/WPA3 con contraseñas fuertes; evitar redes abiertas.
4. **Segmentación y aislamiento** — VLANs, private VLANs, ACLs en switches.
5. **Switches gestionables y port security** — evita que un puerto reciba tráfico de muchas MACs (previene ARP spoofing en parte).
6. **Usar autenticación multi-factor** — reduce impacto de credenciales robadas.
7. **Harden los servicios** — evitar protocolos en texto plano (Telnet, FTP).
8. **Monitorización IDS/IPS y detección de MITM** (Snort, Suricata, Zeek).
9. **Educación de usuarios** — no introducir credenciales en sitios inseguros.

### 10) Aspectos legales y éticos

- **Legal**: capturar tráfico sin autorización suele ser ilegal (interceptación de comunicaciones).
- **Ético**: en entornos corporativos requiere políticas y consentimiento; en pentesting debe haber un acuerdo por escrito.
- Siempre documenta, limita y protege las capturas (pcaps contienen PII).

### 11) Formatos y almacenamiento (.pcap / .pcapng)

- **.pcap** (libpcap) y **.pcapng** (PCAP Next Gen) son formatos binarios que contienen encabezado global + registros de paquetes con timestamp, longitud y datos.
- Abrelos con Wireshark, `tshark`, `tcpdump -r archivo.pcap` o procesalos con Python + Scapy.

### 12) Mini-cheat-sheet (comandos útiles)

```bash
# Listar interfaces
tcpdump -D

# Capturar 200 paquetes en eth0 a archivo (snaplen completo)
sudo tcpdump -i eth0 -c 200 -s 0 -w captura.pcap

# Capturar solo tráfico hacia/desde tu IP
sudo tcpdump -i eth0 host 192.168.1.10 -s 0 -w mine.pcap

# Leer pcap
tcpdump -r captura.pcap -nn -tttt

# Mostrar solo cabeceras con Wireshark:
tshark -r captura.pcap -T fields -e frame.number -e ip.src -e ip.dst -e tcp.port -E header=y

# Buscar solicitudes HTTP (display filter en Wireshark)
http.request.method == "POST"
```

### Conclusión

El rastreo de paquetes es una herramienta imprescindible para la gestión y la seguridad de redes: permite diagnosticar, auditar y responder incidentes. Al mismo tiempo, su poder lo convierte en un vector de abuso cuando se usa sin permiso. La defensa principal es **cifrar** el tráfico y aplicar controles de red (segmentación, switches gestionables, IDS/IPS) junto con políticas y monitoreo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**             | **Siguiente 12**              |
| ------------------ | ------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./7_10_Iptables.md) | [⏩](./7_12_Port_Scanners.md) |
