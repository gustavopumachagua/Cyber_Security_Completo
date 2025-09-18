| **Inicio**         | **atrás 12**                  | **Siguiente 14**         |
| ------------------ | ----------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./7_12_Port_Scanners.md) | [⏩](./7_14_Kerberos.md) |

---

## **Índice**

| Temario                                                                              |
| ------------------------------------------------------------------------------------ |
| [184. ¿Qué es un analizador de protocolos?](#184-qué-es-un-analizador-de-protocolos) |
| [185. Analizadores de protocolo](#185-analizadores-de-protocolo)                     |

# **Analizadores de protocolo**

## **184. ¿Qué es un analizador de protocolos?**

![Analizadores de protocolo](/img/7_Troubleshooting_Tools/Analizadores_de_protocolo.jpg "Analizadores de protocolo")

### 🔹 1. ¿Qué es un analizador de protocolos?

Un **analizador de protocolos** (también llamado _sniffer_ o _packet analyzer_) es una herramienta de **software o hardware** que se utiliza para **capturar, decodificar, analizar y mostrar** los datos que viajan por una red de computadoras.

En otras palabras, permite ver **qué está pasando dentro del tráfico de red**:

- Qué protocolos se están usando (HTTP, DNS, TCP, UDP, etc.).
- Qué dispositivos se comunican.
- Qué datos se transmiten (si no están cifrados).
- Cómo es el rendimiento (latencia, pérdida de paquetes, jitter).

👉 Ejemplo práctico: con **Wireshark**, puedes ver que tu PC está haciendo una consulta DNS al servidor 8.8.8.8, qué nombre pidió y la respuesta que recibió.

### 🔹 2. ¿Cuáles son los diferentes tipos de analizadores de protocolo?

Existen varias categorías según **implementación** y **uso**:

#### ✅ Según el tipo de implementación:

1. **Software**: instalados en un sistema (ej. Wireshark, tcpdump).
2. **Hardware**: dispositivos físicos dedicados para análisis profundo de red (usados en entornos empresariales, ej. Tektronix, Keysight).

#### ✅ Según la función:

- **Analizadores generales de red**: capturan y muestran cualquier tráfico.
- **Analizadores específicos de aplicación/protocolo**: diseñados para VoIP, SIP, RTP, HTTP, SMTP, etc.
- **Analizadores de seguridad (IDS/IPS)**: integran funciones de detección de intrusos (ej. Snort, Suricata).

#### ✅ Según el entorno:

- **Cableados** (Ethernet).
- **Inalámbricos** (WiFi, Bluetooth, 4G/5G).

### 🔹 3. ¿Cómo funciona un analizador de protocolos?

El funcionamiento sigue 3 pasos básicos:

1. **Captura de tráfico**:

   - El analizador pone la **tarjeta de red en modo promiscuo** (para leer todos los paquetes que pasan por la interfaz).
   - En redes WiFi, puede entrar en **modo monitor** para capturar tráfico inalámbrico.

2. **Decodificación**:

   - Divide el paquete en **capas OSI** (Ethernet → IP → TCP/UDP → aplicación).
   - Reconoce el protocolo y muestra los datos de cada encabezado.

3. **Análisis y visualización**:

   - Filtra y organiza la información (ej. “muéstrame solo tráfico HTTP”).
   - Identifica errores, retransmisiones, anomalías o intentos de ataque.

👉 Ejemplo práctico con `tcpdump`:

```bash
sudo tcpdump -i eth0 port 80
```

Esto captura y muestra tráfico HTTP en la interfaz `eth0`.

### 🔹 4. Beneficios de los analizadores de protocolos para las operaciones de red

- **Monitoreo en tiempo real**: visibilidad total del tráfico.
- **Diagnóstico de fallas**: detectar problemas de conectividad, latencia o paquetes perdidos.
- **Optimización de red**: medir rendimiento, balancear carga.
- **Seguridad**: identificar tráfico sospechoso, ataques (ej. ARP spoofing, DoS, escaneos).
- **Cumplimiento**: verificar que las comunicaciones siguen políticas corporativas (ej. uso de HTTPS).
- **Auditoría forense**: investigar incidentes de ciberseguridad.

👉 Ejemplo: un administrador detecta que la red está lenta. Con Wireshark descubre que hay **muchas retransmisiones TCP**, lo que indica pérdida de paquetes en un switch defectuoso.

### 🔹 5. Caso de uso: Analizadores de protocolos para tráfico de voz, vídeo y datos inalámbricos

#### 📞 **Voz (VoIP)**

- Protocolos analizados: SIP, RTP.
- Se mide **jitter, latencia, pérdida de paquetes** → afectan la calidad de llamadas.
- Ejemplo: verificar por qué una llamada en Zoom suena entrecortada.

#### 🎥 **Vídeo**

- Protocolos: RTSP, RTP, MPEG-TS.
- Permiten verificar buffering, cortes, calidad de streaming.
- Ejemplo: analizar retrasos en videoconferencias o IPTV.

#### 📡 **Datos inalámbricos**

- Protocolos: 802.11 (WiFi), LTE, 5G.
- Sirve para detectar interferencias, ataques (deauth en WiFi), saturación de canal.
- Ejemplo: un sniffer en modo monitor detecta que hay un ataque de desautenticación en una red WiFi corporativa.

### 🔹 6. ¿Cuáles son los mejores analizadores de protocolo?

#### 🔸 **De software (los más usados)**

1. **Wireshark** → el más popular, interfaz gráfica, multiplataforma, gratuito.
2. **tcpdump** → basado en consola, muy ligero, ideal para servidores Linux.
3. **Tshark** → versión CLI de Wireshark.
4. **Microsoft Message Analyzer** (descatalogado, pero usado en entornos Windows).
5. **Ettercap** → sniffing + ataques MITM.

#### 🔸 **De seguridad**

- **Snort** → IDS/IPS con funciones de captura.
- **Suricata** → análisis profundo y detección avanzada.

#### 🔸 **De hardware / empresariales**

- **Keysight (ex-Ixia)**.
- **Tektronix**.
- **Savvius Omnipeek**.

### ✅ Resumen final

Un **analizador de protocolos** es una herramienta esencial en redes y ciberseguridad porque permite **ver lo que realmente circula por la red**. Puede ser software o hardware, y se usa para **diagnóstico, optimización, seguridad y forense**. En tráfico de **voz, vídeo y datos inalámbricos**, ayuda a medir calidad, detectar ataques o resolver problemas de rendimiento.

Las herramientas más populares son **Wireshark** (para análisis visual) y **tcpdump** (para CLI en servidores).

---

[🔼](#índice)

---

## **185. Analizadores de protocolo**

Un **analizador de protocolos** (packet analyzer / protocol analyzer / network sniffer) es una herramienta —software o hardware— que **captura, decodifica, reensambla y muestra** el tráfico que circula por una red para que un humano o un sistema lo analice. Sirve para troubleshooting, monitoreo de rendimiento, seguridad, forense y desarrollo de protocolos.

A continuación tienes todo lo necesario: qué hacen, cómo funcionan, tipos, ejemplos de herramientas y muchos **comandos y ejemplos** listos para usar.

### 1 — ¿Qué hace exactamente un analizador de protocolos?

- **Captura** paquetes desde una interfaz de red (modo promiscuo o monitor).
- **Decodifica** capas OSI (Ethernet → IP → TCP/UDP → aplicación).
- **Reensambla** flujos (p. ej. reensamblado TCP para ver una página HTTP completa).
- **Filtra y presenta** la información (filtros de captura BPF y filtros de visualización).
- **Genera estadísticas** (conversaciones, throughput, latencia, jitter).
- **Exporta/guarda** en formatos pcap/pcapng para análisis posterior.

### 2 — Tipos de analizadores de protocolo

- **Software de escritorio GUI**: Wireshark (análisis visual, reensamblado, estadísticas).
- **CLI / servidores**: tcpdump, tshark (Wireshark en terminal), Zeek (antes Bro) para logs y análisis a gran escala.
- **Sondas/IDS**: Suricata, Snort (detección y correlación de firmas en tiempo real).
- **Herramientas especializadas**: NetworkMiner (forense), Omnipeek, Ixia/Tektronix (hardware empresarial).
- **Librerías / scripting**: Scapy (Python) para construir/parsear paquetes y automatizar tests.

### 3 — Cómo funciona (paso a paso técnico)

1. **Captura**: la NIC se pone en _promiscuo_ (Ethernet) o _monitor_ (Wi-Fi) y usa libpcap/Npcap para recibir paquetes.
2. **Filtrado (opcional)**: se aplica un _capture filter_ BPF para reducir lo almacenado (ej. `tcp port 80`).
3. **Almacenamiento**: paquetes se guardan en memoria o fichero `.pcap/.pcapng`.
4. **Decodificación**: cada paquete pasa por _dissectors_ (decodificadores) que interpretan encabezados y payloads por protocolo.
5. **Reensamblado**: reconstrucción de streams (TCP, RTP) para ver la conversación completa.
6. **Análisis**: display filters, estadísticas, gráficos, extracción de objetos (HTTP, archivos).
7. **Exportación / correlación**: generación de logs (Zeek), alertas (Suricata) y exportación a SIEMs.

### 4 — Captura vs Visualización: filtros BPF vs display filters

- **Capture filter (BPF)**: reduce lo que se guarda; se aplica en el kernel/libpcap. Ejemplo (tcpdump/Wireshark capture box):

  ```text
  tcpdump -i eth0 'tcp and port 443 and host 192.168.1.10' -s 0 -w capture.pcap
  ```

- **Display filter (Wireshark/tshark)**: filtra lo que ves en la GUI/CLI después de haber capturado:

  ```text
  ip.addr == 192.168.1.10 && tcp.port == 443
  ```

Consejo: usa BPF para reducir almacenamiento y filtro de visualización para análisis profundo.

### 5 — Herramientas principales y para qué se usan

- **Wireshark** — análisis GUI, reensamblado, follow TCP stream, estadísticas, exportar objetos HTTP.
- **tcpdump** — captura rápida en CLI, ideal en servidores.
- **tshark** — versión CLI de Wireshark, ideal para scripting.
- **Zeek** — convierte pcap en logs ricos por protocolo (http.log, dns.log, conn.log). Muy buena para análisis forense/proactivo.
- **Suricata / Snort** — IDS/IPS en línea, detectan firmas e incluyen motor de captura.
- **Scapy** — construir/parsear/inyectar paquetes (pruebas).
- **NetworkMiner** — extracción de artefactos (archivos, credenciales) de pcaps para forense.
- **Hardware (TAPs, Ixia)** — captura a alta velocidad en entornos críticos.

### 6 — Ejemplos prácticos (comandos y uso real)

#### 6.1 Capturar tráfico HTTP a un archivo (tcpdump)

```bash
sudo tcpdump -i eth0 'tcp port 80' -s 0 -w http_traffic.pcap
# -s 0 = snaplen completo; -w = escribir pcap
```

#### 6.2 Abrir pcap en Wireshark y seguir un flujo HTTP

1. `File → Open` → `http_traffic.pcap`
2. Click derecho en un paquete HTTP → **Follow → TCP Stream** → verás la petición y la respuesta reensambladas.

#### 6.3 Mostrar solo solicitudes POST en Wireshark (display filter)

```
http.request.method == "POST"
```

#### 6.4 Extraer Hosts HTTP con tshark

```bash
tshark -r http_traffic.pcap -Y "http.request" -T fields -e ip.src -e http.host -e http.request.uri
```

#### 6.5 Capturar tráfico HTTPS (solo metadatos) y ver SNI

```bash
# captura TLS, luego filtras por SNI (Server Name, si no está cifrado en TLS1.3)
sudo tcpdump -i eth0 'tcp port 443' -s 0 -w tls_capture.pcap
# En Wireshark display filter: tls.handshake.extensions_server_name
```

> Nota: el contenido HTTPS está cifrado; SNI y los encabezados del handshake TLS pueden mostrar el nombre de host (salvo Encrypted Client Hello).

#### 6.6 Detectar retransmisiones TCP y calcular pérdida (Wireshark)

- En Wireshark: `Statistics → TCP Stream Graphs → Time-Sequence (Stevens)` o `Statistics → Summary` y usa el botón **Expert Information** para ver retransmisiones.

#### 6.7 Captura en modo monitor (Wi-Fi) y ver radiotap

```bash
sudo ip link set wlan0 down
sudo iw dev wlan0 set monitor control
sudo ip link set wlan0 up
sudo tcpdump -i wlan0 -s 0 -w wifi.pcap
# En Wireshark verás encabezado radiotap y 802.11 frames
```

Para descifrar WPA2/WPA3 necesitas capturar el 4-way handshake y la clave pre-compartida.

#### 6.8 Usar Zeek para generar logs a partir de un pcap

```bash
zeek -r capture.pcap
# Produce conn.log, http.log, dns.log, etc., útiles para ingest en SIEM o análisis forense.
```

#### 6.9 Extraer archivos HTTP con NetworkMiner / Wireshark

- Wireshark: `File → Export Objects → HTTP`
- NetworkMiner abre el pcap y muestra archivos extraídos, credenciales, sesiones.

#### 6.10 Analizar VoIP (RTP) — medir jitter y pérdida

- Wireshark: `Telephony → RTP → RTP Streams` → selecciona stream → **Analyze** → ver jitter, packet loss, MOS estimate.
- Para capturar SIP + RTP filtra `sip || rtp`.

### 7 — Reensamblado y dissectors

Analizadores modernos incluyen **dissectors** (decodificadores) para cientos de protocolos: HTTP, DNS, SMB, TLS, RTP, DHCP, MQTT, etc. Si un protocolo es desconocido se puede:

- Añadir un _plugin_ / dissector (Wireshark admite Lua y C).
- Usar Scapy para parseo/creación manual.

### 8 — Limitaciones y retos

- **Cifrado**: contenido TLS/SSH/VPN no es visible sin claves; sólo se ven metadatos (puertos, IP, tamaños, timing).
- **Captura en switches**: por defecto un switch no envía todo el tráfico a un puerto; para ver todo necesitas **SPAN/mirror** o TAP físico.
- **Alta velocidad**: en 10/40/100 Gbps necesitas hardware especializado (TAPs, NICs con DPDK) para no perder paquetes.
- **Privacidad y legalidad**: los pcaps contienen PII; custodiar/eliminar adecuadamente y siempre actuar con autorización.

### 9 — Buenas prácticas de captura / análisis

- Configura **snaplen** (`-s 0`) si necesitas payload completo; si no, limita para ahorrar espacio.
- **Sincroniza relojes** (NTP) para correlacionar eventos entre sistemas.
- Usa **capture filters** para reducir datos; usa display filters para análisis.
- **Rotación/compresión**: `tcpdump -C` y `-G` o `| gzip` para capturas largas.
- Protege los pcaps (encripta en reposo) y aplica retención apropiada por privacidad.
- Documenta: quién, qué, cuándo y por qué capturaste tráfico.

### 10 — Casos de uso concretos (resumidos)

- **Troubleshooting**: descubrir retransmisiones, latencia, NAT issues.
- **Seguridad / Forense**: identificar C2, exfiltración, indicadores de compromiso (IOC).
- **IMS/VoIP**: medir jitter/pérdida y arreglar calidad de llamadas.
- **Wireless**: detectar interferencias, ataques de deauth, analizar roaming.
- **Desarrollo de protocolos**: depurar implementaciones TCP/HTTP/IoT.

### 11 — Ejemplo completo: detectar credenciales HTTP y extraer objetos

1. Captura:

```bash
sudo tcpdump -i eth0 -s 0 -w session.pcap 'tcp port 80'
```

2. Abrir en Wireshark → Display filter:

```
http.request.method == "POST"
```

3. Follow → TCP stream para ver form-data (posibles credenciales en texto).
4. Exportar objetos HTTP: `File → Export Objects → HTTP` → guardar archivos transferidos.

_(Recuerda: hacer esto sólo con autorización y en entornos controlados.)_

### 12 — Recomendaciones por herramienta / caso de uso

- **Wireshark**: análisis profundo interactivo (ingeniería, forense).
- **tcpdump/tshark**: capturas en servidores, scripting.
- **Zeek**: generación automática de logs por protocolo para análisis a gran escala.
- **Suricata/Snort**: detección en línea de amenazas.
- **Scapy**: pruebas/prototipado y manipulación de paquetes.
- **Hardware TAPs / SPAN**: capturar todo en redes con switches.

### Conclusión

Los analizadores de protocolo son la ventana técnica más directa a lo que “está ocurriendo en la red”. Saber **capturar bien**, **filtrar inteligentemente**, **reensamblar** y **usar las herramientas adecuadas** (Wireshark para análisis interactivo, Zeek para logs, Suricata para detección) te da capacidad para resolver problemas de redes, investigar incidentes y garantizar calidad/seguridad.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 12**                  | **Siguiente 14**         |
| ------------------ | ----------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./7_12_Port_Scanners.md) | [⏩](./7_14_Kerberos.md) |
