| **Inicio**         | **atrás 16**          | **Siguiente 18**         |
| ------------------ | --------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_16_curl.md) | [⏩](./13_18_memdump.md) |

---

## **Índice**

| Temario                                                                                                         |
| --------------------------------------------------------------------------------------------------------------- |
| [444. Cómo usar Wireshark: Tutorial completo y consejos](#444-cómo-usar-wireshark-tutorial-completo-y-consejos) |
| [445. Cómo usar Wireshark](#445-cómo-usar-wireshark)                                                            |

# **Wireshark**

## **444. Cómo usar Wireshark: Tutorial completo y consejos**

![Wireshark](/img/13_Tools_for_Incident_Response_and_Discovery/Wireshark.jpeg "Wireshark")

### 1. ¿Qué es Wireshark?

Wireshark es un **analizador de protocolos de red** (network protocol analyzer).
Permite:

- Capturar en tiempo real los **paquetes** que circulan por una interfaz de red.
- Inspeccionar cada paquete en detalle (cabeceras Ethernet, IP, TCP/UDP, HTTP, etc.).
- Filtrar, analizar y exportar la información para depuración, auditoría o investigación forense.

👉 Se usa como un **microscopio de la red**: puedes ver desde un "ping" hasta una petición HTTP, o incluso detectar tráfico sospechoso como intentos de intrusión.

### 2. ¿Cuándo se debe utilizar Wireshark?

- 🔍 **Diagnóstico de red**: identificar latencias, pérdidas de paquetes, cuellos de botella.
- 🔒 **Ciberseguridad**: detectar ataques (ej. ARP spoofing, DoS, tráfico malicioso).
- 📡 **Ingeniería inversa**: estudiar protocolos desconocidos o propietarios.
- 🖧 **Docencia y aprendizaje**: entender cómo funcionan TCP/IP, DHCP, DNS, HTTPS, etc.
- 💾 **Forense digital**: revisar capturas históricas de tráfico para investigaciones.

Ejemplo:

Si tu equipo tiene problemas de conexión a un servidor web, puedes capturar tráfico con Wireshark y ver si los **paquetes TCP se pierden** o si hay **errores DNS**.

### 3. Cómo descargar e instalar Wireshark

- **Windows**: Descargar desde [wireshark.org](https://www.wireshark.org/download.html). Incluye **Npcap** (necesario para capturar tráfico).
- **Linux**:

  ```bash
  sudo apt update && sudo apt install wireshark -y   # Debian/Ubuntu
  sudo dnf install wireshark -y                     # Fedora
  ```

  Luego agrega tu usuario al grupo `wireshark`:

  ```bash
  sudo usermod -aG wireshark $USER
  ```

- **macOS**: Instalar vía Homebrew:

  ```bash
  brew install wireshark
  ```

### 4. Paquetes de datos en Wireshark

Un **paquete** es la unidad básica de comunicación en redes.
Cuando capturas tráfico en Wireshark, verás columnas como:

- **No.** → número de paquete en la captura.
- **Time** → tiempo transcurrido desde el inicio de la captura.
- **Source** → dirección IP de origen.
- **Destination** → dirección IP de destino.
- **Protocol** → protocolo detectado (TCP, UDP, HTTP, DNS, ICMP, etc.).
- **Length** → tamaño del paquete.
- **Info** → resumen (ej. `GET /index.html HTTP/1.1`).

Ejemplo de captura HTTP:

```
No.   Time      Source          Destination     Protocol Length  Info
1     0.000000  192.168.1.10    93.184.216.34   TCP      74      51540 → 80 [SYN]
2     0.002134  93.184.216.34   192.168.1.10    TCP      74      80 → 51540 [SYN, ACK]
3     0.002567  192.168.1.10    93.184.216.34   HTTP     520     GET / HTTP/1.1
```

Aquí vemos la conexión **TCP de 3 vías** y luego la petición HTTP.

### 5. Filtros de Wireshark

Los filtros son la **clave** para analizar tráfico en Wireshark. Hay dos tipos:

#### A) Filtros de captura (antes de capturar)

Ejemplo:

- `tcp port 80` → captura solo tráfico HTTP.
- `host 192.168.1.10` → captura solo tráfico desde/hacia esa IP.
- `net 192.168.1.0/24` → captura todo el tráfico de esa red.

Se escriben en la ventana de **Capture Filter** antes de iniciar.

#### B) Filtros de visualización (Display Filters)

Se aplican **después de capturar**.

Ejemplos útiles:

- `ip.addr == 192.168.1.10` → paquetes de/para esa IP.
- `tcp.port == 443` → solo HTTPS.
- `http` → solo tráfico HTTP.
- `dns` → mostrar consultas/respuestas DNS.
- `tcp.flags.syn == 1 && tcp.flags.ack == 0` → mostrar intentos de conexión TCP.

👉 Ejemplo práctico:

Si investigas un ataque de **DNS spoofing**, puedes filtrar:

```wireshark
dns && ip.src == 192.168.1.50
```

y revisar respuestas sospechosas.

### 6. Funciones adicionales de Wireshark

Además de capturar y filtrar, Wireshark ofrece:

- 📊 **Gráficos de flujo TCP** (`Statistics > TCP Stream Graphs`) → ver rendimiento de conexiones.
- 🕵️ **Reensamblaje de streams**: seguir conversaciones completas (`Follow > TCP Stream`).
- 🔑 **Desencriptar tráfico TLS/SSL** (si tienes claves privadas).
- 💾 **Exportar datos**: guardar paquetes como `.pcap` para análisis posterior.
- 📈 **Análisis de protocolos**: ver cuántos paquetes corresponden a DNS, HTTP, etc.
- 🛠️ **Colorización de tráfico**: aplicar reglas de colores para diferenciar protocolos.

### 7. Recursos y tutoriales adicionales de Wireshark

- 📘 [Documentación oficial](https://www.wireshark.org/docs/)
- 📺 YouTube: canales como _HackerSploit_ o _NetworkChuck_ tienen tutoriales paso a paso.
- 📚 Libros: _Practical Packet Analysis_ de Chris Sanders es un clásico.
- 🔐 Para ciberseguridad: practicar con laboratorios de captura de tráfico (ej. [Malware Traffic Analysis](https://www.malware-traffic-analysis.net/)).

✅ **En resumen**: Wireshark es una herramienta esencial para entender y asegurar redes. Con filtros adecuados y práctica, puedes desde resolver problemas de red simples hasta detectar ataques complejos.

---

[🔼](#índice)

---

## **445. Cómo usar Wireshark**

### 1 — Instalación y primeros pasos rápidos

**Windows**

- Descarga desde [https://www.wireshark.org/download.html](https://www.wireshark.org/download.html) (incluye Npcap necesario para capturas).
- Ejecuta instalador (marcar Npcap y permitir instalación en modo "WinPcap compatibility" si te piden).

**Linux (Debian/Ubuntu)**

```bash
sudo apt update
sudo apt install wireshark
# permitir que el usuario capture sin sudo (cierra sesión y vuelve a entrar)
sudo usermod -aG wireshark $USER
```

**macOS**

```bash
brew install wireshark
# o descargar app bundle desde el sitio oficial
```

Al abrir Wireshark verás la lista de interfaces. Para capturar escoge la interfaz (Ethernet, Wi-Fi, loopback) y pulsa **Start**.

### 2 — Captura: opciones y buenas prácticas

#### Elegir interfaz y permisos

- En Windows: ejecuta Wireshark como Administrador si tiene problemas para capturar.
- En Linux/macOS: añadir usuario al grupo `wireshark` o usar `sudo` para capturar.

#### Captura vs visualización: filtros

- **Capture filter** (BPF — p.ej. `tcp port 80`): limita _lo que se captura_. Se pone antes de iniciar la captura.
- **Display filter** (Wireshark filter syntax — p.ej. `http.request`): filtra _lo que ves_ tras la captura; no reduce lo capturado.

**Ejemplos útiles (capture filters)**

- `tcp port 80`
- `host 192.168.1.10`
- `net 10.0.0.0/8 and not port 22`

**Ejemplos útiles (display filters)**

- `ip.addr == 192.168.1.10`
- `tcp.port == 443`
- `http && ip.dst == 93.184.216.34`
- `dns && dns.qry.name == "example.com"`

#### Ring buffers y tamaño

Para capturar mucho tiempo sin llenar disco usa dumpcap (viene con Wireshark) con rotación por tamaño:

```bash
dumpcap -i eth0 -b filesize:10000 -b files:10 -w /tmp/capture.pcap
```

Esto crea archivos de 10 MB y guarda máximo 10 archivos en bucle.

### 3 — Primeros ejemplos GUI: capturar HTTP y seguir la conversación

1. En Wireshark selecciona la interfaz (p. ej. `en0` o `Ethernet`) y en **Capture Options** pon capture filter: `tcp port 80` (opcional).
2. Pulsa **Start**.
3. En tu navegador visita `http://example.com`.
4. En Wireshark aplica display filter: `http` o `ip.addr == <tu_IP>`.

#### Follow TCP Stream

- Encuentra un paquete HTTP (protocol column = HTTP), clic derecho → **Follow** → **TCP Stream**.
- Se abrirá una ventana con la petición y la respuesta reensambladas (ideal para ver headers y cuerpo).
- Puedes cambiar la vista a **Show data as**: `Raw`/`ASCII`/`HEX`.
- Guardar: botón **Save As** para exportar el contenido (por ejemplo, guardarlo como `.html`).

#### Exportar objetos HTTP

`File → Export Objects → HTTP` → verás ficheros transferidos por HTTP (imágenes, css, js) y podrás guardarlos localmente.

### 4 — Filtros de visualización (display filters) — lista rápida y ejemplos

**Filtros básicos**

- `ip.addr == 192.168.1.10` → paquetes con esa IP origen o destino.
- `ip.src == 10.0.0.5` → paquetes con IP origen.
- `ip.dst == 8.8.8.8` → paquetes con IP destino.
- `tcp.port == 443` → paquetes con puerto TCP origen o destino 443.
- `udp.port == 53` → DNS por UDP.

**Protocolos**

- `http` — todo HTTP.
- `dns` — solicitudes/respuestas DNS.
- `arp` — ARP.
- `icmp` — ping.

**HTTP específicos**

- `http.request` — peticiones HTTP.
- `http.request.method == "GET"` — solo GET.
- `http.response.code >= 400` — respuestas con error HTTP.

**TCP diagnóstico**

- `tcp.analysis.retransmission` — retransmisiones TCP.
- `tcp.analysis.fast_retransmission` — retransmisiones rápidas.
- `tcp.analysis.duplicate_ack` — ACKs duplicados.
- `tcp.analysis.retransmission or tcp.analysis.duplicate_ack` — detectar pérdida.

**TLS / SSL**

- `tls` o `ssl` (depende de versión) — tráfico TLS.
- `tls.handshake.type == 1` — Client Hello.

**Bloques de tiempo**

- `frame.time_relative >= 10 && frame.time_relative <= 20` — paquetes entre 10 y 20 segundos desde inicio de captura.

### 5 — Analizar problemas concretos (casos prácticos)

#### A) Página lenta (latencia entre request y response)

1. Filtra por `http` y localiza la petición GET.
2. Observa columna **Time** y calcula diferencia entre request y response (o usa `Follow TCP Stream` y mira `Time` del paquete de respuesta).
3. Usa `Statistics -> HTTP -> Requests` para ver tiempos de respuesta promedio.

#### B) Pérdida / retransmisiones TCP

Filtro:

```
tcp.analysis.retransmission || tcp.analysis.fast_retransmission || tcp.analysis.out_of_order
```

- Muchas retransmisiones o out-of-order indican enlace con pérdida o problemas de buffer.

#### C) Problemas DNS (no resuelve)

Filtro: `dns`

- Comprueba si hay respuestas (dns.flags.rcode) y si TTL es 0 o respuesta NXDOMAIN.
- Si no hay respuesta, puede ser bloqueo UDP/53 o que DNS falla.

#### D) ARP spoofing / poisoning

Filtro: `arp`

- Revisa si hay múltiples respuestas ARP para la misma IP con distintas MAC.
- `arp.opcode == 2 && ether.src != <known-gateway-mac>` puede indicar spoofing.

### 6 — Estadísticas y vistas útiles

- **Statistics → Summary** — resumen de la captura (paquetes, duración, protocolos principales).
- **Statistics → Protocol Hierarchy** — desglose por protocolo (cuánto tráfico HTTP, TCP, DNS...).
- **Statistics → Conversations** — ver conversaciones entre IPs, bytes transferidos.
- **Statistics → Endpoints** — ver top talkers (IPs que más envían/reciben).
- **Statistics → IO Graphs** — trazar tráfico por segundo; puedes añadir filtros por protocolo (p. ej. `tcp.port == 80`) para ver patrones en el tiempo.
- **Analyze → Expert Information** — resumen con errores/warnings (retransmissions, malformed, suspicious).

### 7 — TLS/HTTPS: ¿cómo ver tráfico cifrado?

#### Opción A — Clave privada del servidor (solo si la tienes)

- `Preferences → Protocols → TLS → RSA keys list` (añadir IP, port, protocol, keyfile)
- Limitación: funciona solo si se usa RSA key exchange (no con perfect forward secrecy, p. ej. ECDHE).

##### Opción B — Usar **SSLKEYLOGFILE** (recomendado para pruebas con navegadores)

1. Exporta variable de entorno antes de lanzar el navegador:

   - Linux/macOS:

     ```bash
     export SSLKEYLOGFILE=~/sslkeys.log
     firefox   # o google-chrome (si está compilado con log)
     ```

   - Windows: define variable de entorno en System → Environment Variables y lanza browser.

2. En Wireshark: `Preferences → Protocols → TLS → (Pre)-Master-Secret log filename` → apunta a `~/sslkeys.log`.
3. Captura y filtra `tls` / `http` — si el navegador usó las claves, Wireshark podrá **descifrar** y mostrar HTTP/2/HTTP payloads.

> Nota: SSLKEYLOGFILE funciona con navegadores basados en NSS/BoringSSL/Chromium si están compilados con keylog enabled. No funciona para tráfico de aplicaciones que no utilicen esa variable.

### 8 — Exportar, guardar y usar línea de comandos

#### Guardar captura

`File → Save As` (.pcapng o .pcap)

#### dumpcap / tshark / tcpdump (línea de comandos)

- Capturar con dumpcap (eficiente):

```bash
dumpcap -i eth0 -w /tmp/capture.pcapng
```

- Capturar con límites y rotación:

```bash
dumpcap -i eth0 -b filesize:10000 -b files:50 -w /tmp/capture.pcapng
```

- Analizar con tshark:

```bash
tshark -r capture.pcap -Y "http.request" -T fields -e frame.number -e ip.src -e http.request.method -e http.request.uri
```

- Extraer solo paquetes DNS de un pcap:

```bash
tshark -r capture.pcap -Y "dns" -w dns_only.pcap
```

### 9 — Reconstruir archivos y objetos

**HTTP**

- `File → Export Objects → HTTP` → lista recursos descargados (puedes guardarlos).

**SMB / FTP / TFTP**

- También hay opciones `File → Export Objects → SMB` u otros protocolos soportados.

**Follow stream** → guardar payloads completos (útil para reconstruir transferencias).

### 10 — Consejos de rendimiento y manejo de capturas grandes

- Usa **capture filters** para no capturar todo.
- Usa **dumpcap** en lugar de la GUI para capturas de larga duración.
- Emplea **ring buffer** para limitar espacio en disco.
- Aumenta capture buffer en `Capture Options` si tu NIC es muy rápido (`-B` en dumpcap).
- Evita abrir un pcap enorme en la GUI si solo necesitas un subconjunto; filtra con `tshark` para extraer lo necesario y luego ábrelo.

### 11 — Atajos de trabajo y trucos útiles

- Mostrar solo paquetes que contienen una cadena: `frame contains "login"` (display filter).
- Usar **Coloring Rules** para destacar paquetes (View → Coloring Rules).
- Copiar como texto o como bytes (clic derecho → Copy → distintas opciones).
- Resolver nombres: Preferences → Name Resolution → habilita o deshabilita resolución de nombres para mejorar velocidad.

### 12 — Ejemplos prácticos — filtros listos para copiar

**Ver solo errores HTTP (4xx/5xx):**

```
http.response.code >= 400
```

**Pings (ICMP echo request):**

```
icmp.type == 8
```

**Mostrar solo retransmisiones TCP:**

```
tcp.analysis.retransmission
```

**Buscar una URL concreta:**

```
http.request.uri contains "login"
```

**DNS queries para example.com:**

```
dns.qry.name == "example.com"
```

**Buscar paquetes con payload que contenga la palabra "password":**

```
frame contains "password"
```

### 13 — Flujo de análisis ejemplo (diagnóstico real)

**Problema:** usuarios reportan que `https://miapp.example` tarda mucho en cargar.

1. Captura en el cliente o en el gateway (capture filter: `host miapp.example` o `host 10.0.0.5`).
2. Filtra por `tls` y `http` (si descifraste TLS, filtra `http`).
3. Usa **Follow TCP Stream** en una petición lenta y compara tiempos (Request time vs Response).
4. Revisa `tcp.analysis.retransmission` y `tcp.analysis.duplicate_ack` para ver pérdidas.
5. Abre **IO Graphs** para ver si hay picos de tráfico coincidentes.
6. Si DNS tarda, filtra `dns` y mira tiempos de respuesta y servidores usados.
7. Extrae evidencia y genera breve informe con capturas relevantes (Expert Info + Follow Stream).

### 14 — Recursos y aprendizaje continuo

- Documentación oficial: [https://www.wireshark.org/docs/](https://www.wireshark.org/docs/)
- Libro recomendado: _Practical Packet Analysis_ (Chris Sanders).
- Laboratorios: MalwareTrafficAnalysis, pcapr.net, sitios con pcap públicos para practicar.
- Cursos y vídeos prácticos en YouTube (buscar labs de Wireshark).

### 15 — Mini-cheat-sheet final (resumen rápido)

- Iniciar captura GUI: seleccionar interfaz → Start.
- Capture filter (BPF): `tcp port 80`, `host 1.2.3.4`.
- Display filter: `http`, `dns`, `ip.addr==192.168.1.10`, `tcp.analysis.retransmission`.
- Follow stream: clic derecho sobre paquete TCP/HTTP → Follow → TCP Stream.
- Export HTTP objects: `File → Export Objects → HTTP`.
- Decrypt TLS (dev): exportar `SSLKEYLOGFILE` en el navegador y configurarlo en **Preferences → Protocols → TLS**.
- Capturar en CLI: `dumpcap -i eth0 -w /tmp/cap.pcap -b filesize:10000 -b files:10`.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 16**          | **Siguiente 18**         |
| ------------------ | --------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_16_curl.md) | [⏩](./13_18_memdump.md) |
