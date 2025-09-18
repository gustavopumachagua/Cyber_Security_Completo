| **Inicio**         | **atrás 30**             | **Siguiente 32**               |
| ------------------ | ------------------------ | ------------------------------ |
| [🏠](../README.md) | [⏪](./13_30_NetFlow.md) | [⏩](./13_32_Firewall_Logs.md) |

---

## **Índice**

| Temario                                                                                                       |
| ------------------------------------------------------------------------------------------------------------- |
| [471. Captura de paquetes: qué es y qué necesita saber](#471-captura-de-paquetes-qué-es-y-qué-necesita-saber) |
| [472. Tutorial de Wireshark para principiantes](#472-tutorial-de-wireshark-para-principiantes)                |

# **Packet Captures**

## **471. Captura de paquetes: qué es y qué necesita saber**

![Packet Captures](/img/13_Tools_for_Incident_Response_and_Discovery/Packet_Captures.webp "Packet Captures")

### 🔹 ¿Qué es la captura de paquetes?

La **captura de paquetes** (packet capture o _sniffing_) es la técnica de **interceptar, registrar y analizar paquetes de datos** que circulan por una red.

Un _paquete_ es la unidad básica de transmisión en redes: contiene **cabeceras** (IP, TCP, UDP, etc.) y, opcionalmente, **datos (payload)**.
La captura permite ver **qué viaja por la red**: origen, destino, protocolo, tamaño y, si no está cifrado, el contenido.

👉 En resumen: **es como escuchar las conversaciones que viajan por el “aire” del cable o Wi-Fi**.

### 🔹 ¿Cómo funciona la captura de paquetes?

1. La tarjeta de red (NIC) se pone en **modo promiscuo** → recibe no solo los paquetes destinados al host local, sino todos los que pasan por la red.
2. Un software de captura (ej. Wireshark, tcpdump) accede a la **capa de enlace de datos** mediante una biblioteca (ej. **libpcap** en Linux, **WinPcap/Npcap** en Windows).
3. El software guarda los paquetes en **archivos de captura** (generalmente `.pcap` o `.pcapng`).
4. Luego se pueden **analizar** en tiempo real o de manera forense.

### 🔹 Cómo leer una captura de paquetes

Cada paquete tiene capas. Ejemplo simplificado:

**Ethernet (Capa 2)**

```
Destino MAC: 00:11:22:33:44:55
Origen MAC: 66:77:88:99:AA:BB
Tipo: IPv4
```

**IP (Capa 3)**

```
Origen: 192.168.1.10
Destino: 142.250.72.14 (google.com)
Protocolo: TCP
```

**TCP (Capa 4)**

```
Puerto origen: 52345
Puerto destino: 443 (HTTPS)
Flags: SYN
```

**Datos (Capa 7)**

Si no está cifrado, puede mostrar peticiones HTTP, correos, contraseñas en texto plano, etc.

En tráfico cifrado (HTTPS, SSH, TLS), solo se ven **metadatos**: quién se conecta a quién, tamaño, frecuencia.

👉 Ejemplo de línea en tcpdump:

```
12:00:01.123456 IP 192.168.1.10.52345 > 142.250.72.14.443: Flags [S], seq 123456789, win 64240, length 0
```

### 🔹 ¡Formatos, bibliotecas y filtros, Dios mío!

- **Formatos de captura:**

  - `.pcap` → estándar clásico.
  - `.pcapng` → versión extendida (soporta más metadatos).

- **Bibliotecas:**

  - **libpcap** (Linux, BSD, macOS)
  - **WinPcap/Npcap** (Windows)

- **Filtros (muy importantes):**

  Permiten capturar **solo lo que te interesa**, para no llenar el disco ni sobrecargar CPU.

  Ejemplos (tcpdump/Wireshark BPF syntax):

  - Capturar solo tráfico HTTP → `tcp port 80`
  - Capturar solo desde una IP → `host 192.168.1.5`
  - Capturar solo tráfico DNS → `udp port 53`
  - Capturar todo excepto SSH → `not port 22`

### 🔹 Herramientas de captura de paquetes

- **Wireshark** → interfaz gráfica muy potente (filtros, gráficos, decodificación de protocolos).
- **tcpdump** (Linux/macOS) → línea de comandos, ideal para servidores.
- **tshark** → versión CLI de Wireshark.
- **Ettercap** → útil para MITM y análisis avanzado.
- **Microsoft Message Analyzer** (obsoleto, reemplazado por otras herramientas).
- **Snort / Suricata** → además de capturar, permiten detección de intrusiones (IDS).

### 🔹 Casos de uso de captura de paquetes y rastreadores

- **Resolución de problemas de red:**

  Ej. detectar por qué una aplicación no conecta (puerto cerrado, handshake TCP fallido).

- **Optimización de rendimiento:**

  Ver cuellos de botella, latencia, retransmisiones.

- **Seguridad y forense digital:**

  Analizar tráfico malicioso, infecciones, exfiltración de datos.

- **Aprendizaje:**

  Entender protocolos en vivo (HTTP, DNS, ARP, TLS).

- **Ingeniería inversa de malware:**

  Capturar tráfico C2, descifrar protocolos no documentados.

### 🔹 Ventajas y desventajas de la captura de paquetes

**✅ Ventajas:**

- Alta visibilidad de la red.
- Permite diagnóstico profundo (incluso a nivel de bit).
- Útil para auditoría y forense digital.
- Compatible con casi cualquier red y protocolo.

**❌ Desventajas:**

- Alto volumen de datos: un enlace de 1 Gbps puede generar **cientos de GB/hora**.
- Requiere experiencia para interpretar correctamente.
- Puede ser **intrusivo** si se abusa (ej. capturar contraseñas en texto plano).
- Tráfico cifrado (HTTPS, VPN) limita el análisis del contenido: solo ves metadatos.
- Puede generar problemas legales si se hace sin autorización (en muchas jurisdicciones es espionaje).

### 📝 Resumen final

La **captura de paquetes** es una técnica esencial en redes y ciberseguridad que permite **ver, analizar y entender el tráfico de red** en detalle. Con herramientas como **Wireshark** o **tcpdump**, puedes diagnosticar problemas, investigar incidentes de seguridad o aprender cómo funcionan los protocolos.

Pero debes tener en cuenta que:

- Puede ser **voluminoso** y **complejo**.
- No siempre se puede ver el contenido (si está cifrado).
- Legalmente, **siempre debe hacerse con autorización**.

---

[🔼](#índice)

---

## **472. Tutorial de Wireshark para principiantes**

### 1) ¿Qué es Wireshark y para qué sirve?

**Wireshark** es el analizador de tráfico de red más usado: lee paquetes (pcap/pcapng), decodifica protocolos (Ethernet, IP, TCP, HTTP, DNS, TLS...) y permite inspeccionar cada bit de una comunicación.
Usos típicos: troubleshooting de red, forense de incidentes, aprendizaje de protocolos, depuración de aplicaciones y detección de anomalías.

### 2) Instalación (rápida)

- **Windows**: descarga el instalador de Wireshark e instala **Npcap** cuando lo pida (Npcap habilita captura).
- **macOS**: instalar con Homebrew (`brew install --cask wireshark`) o .dmg; puede pedir permisos de captura.
- **Linux**: `sudo apt install wireshark` (Debian/Ubuntu) o `sudo dnf install wireshark` (Fedora). En Linux, o bien ejecutas Wireshark como root (no recomendable) o configuras permisos (`setcap`/grupo `wireshark`).

> Nota: en servidores sin GUI usa **tshark** (CLI). En ambientes Wi-Fi, captura en modo _monitor_ (si la tarjeta lo soporta).

### 3) Primeros pasos (abrir, seleccionar interfaz y capturar)

1. Abre Wireshark → verás lista de interfaces (Ethernet, Wi-Fi, túneles).
2. Haz doble clic en la interfaz que quieras monitorear (o usa **Capture → Options** para ajustar).
3. **Capture Options**:

   - _Promiscuous mode_: recibe todos los paquetes que pasan por la NIC (útil en cables; en switches verás solo los destinados a tu MAC a menos que estés en SPAN/mirror).
   - _Monitor mode_ (Wi-Fi): necesario para ver todo el tráfico inalámbrico.
   - _Capture filter_ (BPF) — opcional: filtra **lo que se graba** (ej. `host 1.2.3.4 and tcp port 80`).
   - _Ring buffer / file size_ → configurar para capturas largas sin llenar disco.

4. Inicia captura → verás paquetes en vivo en la lista superior.

### 4) Diferencia: **Capture filter** vs **Display filter**

- **Capture filter** (BPF) se aplica _antes_ de grabar: reduce la cantidad de datos capturados. Sintaxis tipo tcpdump (ej.: `tcp port 443`).
- **Display filter** se aplica _después_ y filtra lo que ves en Wireshark (muy potente): `http.request` o `ip.addr == 192.168.1.10`.

**Ejemplos de capture filters (BPF):**

- `host 192.168.1.10` → solo tráfico hacia/desde esa IP.
- `tcp and port 80` → solo TCP puerto 80.
- `net 10.0.0.0/8` → red 10/8.

**Ejemplos de display filters (Wireshark):**

- `ip.addr == 192.168.1.10`
- `tcp.port == 443`
- `http.request.method == "GET"`
- `dns.qry.name == "example.com"`

### 5) Anatomía de Wireshark (pantalla)

- **Panel superior**: lista de paquetes (nº, hora, origen, destino, protocolo, info).
- **Panel medio**: árbol con las capas del paquete (Frame → Ethernet → IP → TCP → HTTP, etc.).
- **Panel inferior**: bytes hex + ASCII del paquete seleccionado.
- **Barra de filtros** (encima): escribe display filters aquí y presiona Enter.

Consejo: haz **right-click** en cualquier campo del árbol → _Apply as Filter_ o _Prepare as Filter_ para crear filtros rápidamente.

### 6) Primeros análisis prácticos (ejemplos paso a paso)

#### Ejemplo A — Ver un acceso HTTP/HTTPS a un sitio

1. Captura en tu interfaz mientras visitas `http://example.com` (o solo `https://google.com` para ver TLS handshake).
2. Filtra por HTTP: `http` → verás requests/responses.
3. Para HTTPS verás TLS: `tls` o `ssl`. Para ver el contenido HTTPS necesitas descifrar TLS (ver punto sobre TLS más abajo).
4. **Follow TCP Stream**: selecciona un paquete HTTP/TCP → `Right Click → Follow → TCP Stream`. Wireshark reensambla la conversación y te muestra el texto (ideal para ver headers HTTP y payload no cifrado).

#### Ejemplo B — Buscar intentos de login SSH fallidos (Linux)

- Filtra por SSH TCP: `tcp.port == 22` y busca `sshd` en `Info` o haz display filter: `ssh`.
- Para buscar reintentos desde una IP: `ip.src == 10.0.0.5 && tcp.port == 22`.

#### Ejemplo C — Detectar retransmisiones TCP

- Display filter: `tcp.analysis.retransmission`
- También `tcp.analysis.flags` da más info (duplicate ACKs, out-of-order).

### 7) Filtros útiles — **cheat sheet** (display filters)

- `ip.addr == 192.168.1.5` — IP origen o destino.
- `ip.src == 10.0.0.1` — origen exacto.
- `ip.dst == 8.8.8.8` — destino exacto.
- `tcp.port == 80` — cualquiera de los puertos (src/dst).
- `tcp.srcport == 443` / `tcp.dstport == 443` — puerto específico.
- `http.request` — paquetes que son peticiones HTTP.
- `http.request.method == "POST"` — POST requests.
- `dns` — todos los paquetes DNS.
- `dns.qry.name == "example.com"` — consultas por nombre.
- `icmp` — paquetes ICMP.
- `arp` — tráfico ARP.
- `frame.time >= "Sep 16, 2025 12:00:00"` — filtrar por tiempo (ejemplo de sintaxis local).
- `tcp.analysis.lost_segment`, `tcp.analysis.zero_window` — problemas TCP.

### 8) Estadísticas y herramientas internas

En el menú **Statistics** encontrarás herramientas poderosas:

- **Protocol Hierarchy**: ver porcentaje por protocolo (útil para ver si mucho tráfico es HTTP, DNS, TLS...).
- **Conversations**: ver pares (src↔dst) y bytes/packets por conversación.
- **Endpoints**: lista de IPs con bytes enviados/recibidos.
- **IO Graphs**: graficar tráfico (packets/sec, bytes/sec) y comparar filtros.
- **Flow Graph**: secuencia de mensajes entre hosts.
- **Expert Information**: resumen de errores/anomalías detectadas (retransmissions, malformed, etc.). Muy útil al inicio.

### 9) Guardar, exportar y compartir

- **Guardar captura**: `File → Save As` → `.pcap` o `.pcapng`.
- **Export selected packets**: select packets → `File → Export Specified Packets`.
- **Export objects**: `File → Export Objects → HTTP` permite extraer archivos transferidos por HTTP (muy útil).
- **Export to CSV**: `File → Export Packet Dissections → As CSV`.

### 10) Análisis de protocolos comunes (qué buscar y ejemplos)

#### DNS

- Display: `dns`
- Buscar queries por dominio: `dns.qry.name == "example.com"`
- Fíjate en respuestas NXDOMAIN, TTL bajos, respuestas múltiples (fast flux).

#### HTTP

- Display: `http`
- _Follow TCP Stream_ para ver headers y payload.
- Extraer objetos: `File → Export Objects → HTTP`.

#### TLS / HTTPS

- Display: `tls` ó `ssl`
- Ver **handshake**: Client Hello, Server Hello, Certificate.
- **Descifrado**: usar `SSLKEYLOGFILE` (ver abajo) para ver contenido si controlas cliente.

#### TCP

- Buscar Handshake: ver `SYN`, `SYN/ACK`, `ACK`.
- Retransmisiones: `tcp.analysis.retransmission`
- Throughput: calcular bytes/time o usar IO Graph.

#### ARP & ICMP

- ARP: problemas de ARP (gratuitous, spoofing). Filter `arp`.
- ICMP: pings, unreachable, fragmentación. Filter `icmp`.

### 11) TLS: cómo ver tráfico HTTPS (descifrado) — método práctico

Si controlas el cliente (navegador), puedes exportar las claves TLS de sesión:

1. **SSLKEYLOGFILE**: configura la variable de entorno `SSLKEYLOGFILE=/path/sslkeys.log` antes de arrancar Chrome/Firefox (estos navegadores respetan esa variable).
2. En Wireshark: `Edit → Preferences → Protocols → TLS` → **(Pre)-Master-Secret log filename** → apunta a `sslkeys.log`.
3. Abre la pcap con Wireshark (o captura en vivo) y verás HTTP/1.1 o HTTP/2 descifrado.

(Opcional: usar claves privadas RSA en configuración TLS si se usa RSA key exchange, menos frecuente hoy.)

> Importante: **no** hagas esto en tráfico que no controlas o que contenga datos de terceros sin permiso.

### 12) Herramientas de línea de comandos (tshark, dumpcap, tcpdump)

- **tcpdump** (captura rápida desde terminal):

  - Capturar 1000 paquetes: `sudo tcpdump -i eth0 -c 1000 -w captura.pcap`
  - Filtrar BPF: `sudo tcpdump -i eth0 'port 53' -w dns.pcap`

- **tshark** (Wireshark CLI):

  - Leer pcap y mostrar paquetes: `tshark -r captura.pcap`
  - Filtrar y extraer campos:

    ```bash
    tshark -r captura.pcap -Y "http.request" -T fields -e ip.src -e http.host -e http.request.uri
    ```

  - Capturar en vivo y escribir a archivo:

    ```bash
    sudo tshark -i eth0 -w captura.pcap -c 1000
    ```

- **dumpcap** es la herramienta de bajo nivel que Wireshark usa para capturar (más eficiente). En Wireshark GUI puedes configurar que use dumpcap.

### 13) Rendimiento y captura a gran escala (consejos)

- Para enlaces rápidos (1G/10G): **no** captures todo indiscriminadamente. Usa _capture filters_, _sampling_ o SPAN a un appliance de captura.
- Usa **ring buffer**: configura archivos rotativos (`Capture Options → Use multiple files`), p. ej. 10 archivos de 100 MB.
- En servidores, usa `dumpcap` con privilegios mínimos y luego analiza el pcap en otra máquina.
- Habilita _name resolving_ con cuidado: resolver nombres DNS en tiempo real puede ralentizar la UI (y generar tráfico adicional).

### 14) Seguridad, privacidad y legalidad

- **Siempre** obtén permiso para capturar tráfico (políticas internas, jurídico).
- Capturar tráfico puede captar credenciales y datos sensibles — trata los pcap como datos confidenciales.
- Redacta o protege capturas antes de compartir.
- En entornos corporativos, documenta el propósito y retención.

### 15) Práctica guiada (3 ejercicios para afianzar)

#### Ejercicio 1 — DNS + HTTP básico

1. Abre Wireshark y selecciona tu interfaz.
2. Inicia captura.
3. En tu navegador visita `http://example.com` (o cualquier web no HTTPS para ver contenido claro).
4. En Wireshark filtra: `dns` → identifica la query y respuesta (IP).
5. Filtra `http` → usa _Follow TCP Stream_ en la petición GET para ver el HTML.

**Objetivo:** entender resolución (DNS) + comunicación HTTP.

#### Ejercicio 2 — TCP handshake y retransmisiones

1. Genera una conexión TCP (por ejemplo `curl http://example.com`).
2. Filtra: `tcp` y localiza los paquetes `SYN`, `SYN,ACK`, `ACK`.
3. Introduce una mala conexión (simula latencia o desconecta la interfaz) y busca `tcp.analysis.retransmission`.

**Objetivo:** identificar handshake y retranmisiones.

#### Ejercicio 3 — Descifrar HTTPS con SSLKEYLOGFILE

1. En tu terminal exporta `SSLKEYLOGFILE=/tmp/sslkeys.log` y luego abre Chrome/Firefox desde esa terminal.
2. Visita `https://example.com` (o algún sitio que permita).
3. En Wireshark Preferences → TLS → apunta a `/tmp/sslkeys.log`.
4. Captura o abre la pcap y aplica filtro `http` o `tls` y verás contenido descifrado (si la sesión usó claves compatibles).

**Objetivo:** practicar descifrado TLS para debugging de APIs seguras en entornos controlados.

### 16) Problemas frecuentes y soluciones rápidas

- **No veo paquetes:** seleccionaste interfaz equivocada o Wireshark no tiene permisos. En Linux añade el usuario al grupo `wireshark` o usa `sudo` para tshark. En Windows, instalaste Npcap?
- **Solo ves tráfico hacia/desde tu PC (no todo el switch):** estás en un puerto de switch no mirror. Solicita SPAN/Mirror o conecta en hub/monitor port.
- **Capturas enormes:** usa capture filters y ring buffers.
- **Mucho ruido en display filters:** guarda filtros frecuentes y usa _Analyze → Display Filters_.

### 17) Recursos rápidos / cheat sheet (apuntar y usar)

- **Display filter básicos:**

  - `ip.addr == x.x.x.x`
  - `tcp.port == 80`
  - `http.request`
  - `dns.qry.name == "example.com"`
  - `tcp.analysis.retransmission`

- **BPF capture filter ejemplos:**

  - `host 10.0.0.5`
  - `tcp port 80`
  - `net 192.168.0.0/16`

- **Comandos tshark útiles:**

  - Capturar: `sudo tshark -i eth0 -w out.pcap -c 1000`
  - Extraer campos HTTP:
    `tshark -r out.pcap -Y "http.request" -T fields -e ip.src -e http.host -e http.request.uri`

### 18) ¿Y ahora qué? Siguientes pasos recomendados

- Practica con las **ejercicios** de arriba hasta sentirte cómodo.
- Lee y juega con **Protocol Hierarchy**, **Conversations** y **IO Graphs** para obtener visibilidad rápida.
- Aprende `tshark` para automatizar extracción de indicadores y reportes.
- En entornos de seguridad, integra capturas con logs (DNS, proxy, EDR) para investigar incidentes más rápido.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 30**             | **Siguiente 32**               |
| ------------------ | ------------------------ | ------------------------------ |
| [🏠](../README.md) | [⏪](./13_30_NetFlow.md) | [⏩](./13_32_Firewall_Logs.md) |
