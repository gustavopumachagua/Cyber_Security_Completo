| **Inicio**         | **atrás 6**         | **Siguiente 8**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_6_NMAP.md) | [⏩](./7_8_Tracert.md) |

---

## **Índice**

| Temario                                                            |
| ------------------------------------------------------------------ |
| [171. Página de manual de tcpdump](#168-nmap-website)              |
| [172. TCP Dump: ¿Qué es y cómo se utiliza?](#169-nmap-cheat-sheet) |

# **TCPdump**

## **171. Página de manual de tcpdump**

![TCPdump](/img/7_Troubleshooting_Tools/TCPdump.webp "TCPdump")

¡Vamos al grano! `tcpdump` es la herramienta clásica de línea de comandos para **capturar y analizar tráfico de red** en sistemas tipo Unix (Linux, macOS, BSD). Captura paquetes desde una interfaz de red y permite filtrarlos con expresiones BPF (Berkeley Packet Filter). Es ideal para troubleshooting, análisis forense de red y debugging de protocolos.

> ⚠️ **Ético y legal:** sólo captures tráfico en redes/hosts tuyos o con autorización explícita. Las capturas pueden contener datos sensibles (credenciales, cookies, PII).

### 1) Conceptos básicos y cómo funciona

- `tcpdump` abre una interfaz en modo promiscuo (si se le permite) y escribe paquetes en pantalla o en un archivo pcap.
- Usa **filtros BPF** para seleccionar qué paquetes grabar (ej.: `host`, `port`, `net`, `tcp`, `udp`, combinadores `and`, `or`, `not`).
- Los archivos guardados (`-w`) son **pcap** estándar que puedes abrir con Wireshark o `tshark`.

### 2) Requisitos / permisos

- En la mayoría de sistemas necesitas privilegios de root (o capacidades `CAP_NET_RAW` / `CAP_NET_ADMIN`) para capturar en interfaces.
- Para evitar `sudo` puedes dar capacidades al binario (ejemplo en Linux):

  ```bash
  sudo setcap cap_net_raw,cap_net_admin=eip $(which tcpdump)
  ```

- Alternativa segura: usar `-Z usuario` para que tcpdump **baje privilegios** después de iniciar la captura:

  ```bash
  sudo tcpdump -i eth0 -w out.pcap -Z nobody
  ```

### 3) Comandos esenciales (rápidos)

Ver interfaces:

```bash
tcpdump -D
```

Captura mínima y ver en pantalla (interfaz eth0):

```bash
sudo tcpdump -i eth0
```

No resolver nombres (más rápido, salida numérica):

```bash
sudo tcpdump -i eth0 -n
```

Capturar N paquetes y salir:

```bash
sudo tcpdump -i eth0 -c 100
```

Capturar todo el paquete (snaplen completo) y escribir a archivo pcap:

```bash
sudo tcpdump -i eth0 -s 0 -w captura.pcap
```

Leer un pcap:

```bash
tcpdump -r captura.pcap
```

Mostrar paquete en hex + ASCII (útil para ver payload):

```bash
sudo tcpdump -A -s 0 -i eth0 'tcp port 80'
# o con -X (incluye cabecera de enlace)
```

Más información en pantalla (verbose):

```bash
sudo tcpdump -vvv -i eth0
```

### 4) Filtros BPF (ejemplos prácticos)

Filtros simples:

```bash
# Tráfico a/desde un host
sudo tcpdump -i eth0 host 192.168.1.10

# Tráfico TCP al puerto 22 (SSH)
sudo tcpdump -i eth0 tcp port 22

# Tráfico UDP al puerto 53 (DNS)
sudo tcpdump -i eth0 udp port 53

# Rango de puertos
sudo tcpdump -i eth0 portrange 1000-2000

# Red completa
sudo tcpdump -i eth0 net 10.0.0.0/8
```

Combinando condiciones:

```bash
# tráfico HTTP desde LAN interna
sudo tcpdump -i eth0 'src net 192.168.1.0/24 and dst port 80'

# todo menos ARP y VLAN
sudo tcpdump -i eth0 'not arp and not vlan'
```

Filtros avanzados (útiles y poderosos):

```bash
# Capturar sólo SYN (handshake inicial)
sudo tcpdump -i eth0 'tcp[13] & 0x02 != 0'

# Capturar sólo paquetes que contengan "GET " en el payload HTTP (ejemplo avanzado)
sudo tcpdump -i eth0 'tcp[((tcp[12] & 0xf0) >> 2):4] = 0x47455420'
# 0x47 0x45 0x54 0x20 == "GET "
```

### 5) Ejemplos de uso comunes (listas listas para pegar)

1. Mapear quién está vivo en la LAN:

```bash
sudo tcpdump -i eth0 -n icmp
# o para ver ARP (direcciones MAC)
sudo tcpdump -i eth0 -n arp
```

2. Capturar tráfico HTTP y ver contenido (texto) en tiempo real:

```bash
sudo tcpdump -i eth0 -s 0 -A 'tcp port 80'
```

3. Guardar todo el tráfico de la interfaz a archivo para análisis posterior:

```bash
sudo tcpdump -i eth0 -s 0 -w /tmp/full_capture.pcap
```

4. Capturar sólo DNS consultados a un servidor concreto (udp 53):

```bash
sudo tcpdump -i eth0 -n 'udp and dst port 53 and dst host 8.8.8.8'
```

5. Capturar durante 1 minuto y rotar archivos cada 60s (time-based rotation):

```bash
sudo tcpdump -i eth0 -s 0 -w '/tmp/dump-%Y%m%d%H%M%S.pcap' -G 60
# -w con nombres con strftime + -G cada N segundos
```

6. Rotación por tamaño (ej.: 100 MB por archivo) — ejemplo básico:

```bash
sudo tcpdump -i eth0 -s 0 -w /tmp/dump.pcap -C 100
# Genera dump.pcap, dump1.pcap, dump2.pcap... cuando llega a 100MB
```

7. Crear anillo de archivos (ring buffer) — mantén N archivos:

```bash
sudo tcpdump -i eth0 -s 0 -w '/tmp/dump-%Y%m%d%H%M%S.pcap' -G 3600 -W 24
# rota cada hora y mantiene hasta 24 archivos (combinación -G + -W)
```

8. Comprimir en vuelo (evita ocupar disco rápidamente):

```bash
sudo tcpdump -i eth0 -s 0 -w - | gzip > /tmp/capture.pcap.gz
```

### 6) Interpretación de una línea de salida (ejemplo)

Salida típica:

```
12:34:56.789012 IP 192.168.1.100.45678 > 93.184.216.34.80: Flags [S], seq 123456789, win 64240, options [mss 1460,sackOK,TS val 123456 ecr 0,nop,wscale 7], length 0
```

Interpretación:

- `12:34:56.789012` → timestamp.
- `IP` → protocolo de red (puede aparecer `ARP`, `IP6`, etc.).
- `192.168.1.100.45678` → dirección origen + puerto.
- `>` → hacia.
- `93.184.216.34.80` → dirección destino + puerto.
- `Flags [S]` → bandera TCP (S = SYN).
- `seq 123456789` → número de secuencia.
- `win 64240` → ventana TCP.
- `options [...]` → opciones TCP negociadas.
- `length 0` → longitud de la carga útil (payload) en bytes.

### 7) Herramientas complementarias para análisis

- **Wireshark** (GUI): abre `.pcap` para análisis visual, filtros más amigables, reensamblado TCP, seguimientos (Follow TCP Stream).
- **tshark** (CLI de Wireshark): análisis a modo texto / extracción masiva.

Ej.: `tshark -r captura.pcap -Y "http.request"`

- **ngrep**: filtro por expresiones de texto en payload.
- **capinfos** (de Wireshark) para metadatos de pcap.

Ejemplo: abrir pcap con wireshark:

```bash
wireshark captura.pcap
```

o usar tshark para extraer flujos HTTP:

```bash
tshark -r captura.pcap -Y http.request -T fields -e ip.src -e http.host -e http.request.uri
```

### 8) Buenas prácticas y rendimiento

- **No** captures más de lo necesario; filtra por host/puerto para reducir ruido.
- Usa `-s 0` si necesitas el payload completo; si no, ajusta `-s <bytes>` para ahorrar espacio.
- Escribe a disco (`-w`) en lugar de imprimir en pantalla para mejor rendimiento.
- Comprime o rota archivos si vas a capturar durante largos periodos.
- En sistemas con IDS/IPS, captura a baja tasa (`-G`, `-T2` para timing) para evitar afectarlos.
- Evita DNS inversas (`-n`/`-nn`) para mayor velocidad.

### 9) Riesgos y seguridad

- Capturar tráfico puede exponer credenciales y datos sensibles; protege y elimina pcaps cuando ya no sean necesarios.
- Falsos positivos: algunos filtros detectan patrones pero no garantizan vulnerabilidad; valida con pruebas adicionales autorizadas.
- Guardar pcaps en servidores seguros (encriptados) y controlar acceso.

### 10) Mini-cheat sheet (resumen rápido)

- Listar interfaces: `tcpdump -D`
- Capturar 100 paquetes en eth0: `sudo tcpdump -i eth0 -c 100 -n`
- Guardar pcap completo: `sudo tcpdump -i eth0 -s 0 -w out.pcap`
- Leer pcap: `tcpdump -r out.pcap -nn -tttt -X`
- Filtro básico: `host 1.2.3.4`, `tcp port 80`, `net 10.0.0.0/8`
- Mostrar ASCII payload: `-A` ; mostrar hex+ASCII: `-X`
- Rotar por tiempo: `-G seconds -w 'dump-%Y%m%d%H%M%S.pcap'`
- Rotar por tamaño: `-C megabytes -w basefile`
- Capturar solo SYN: `tcp[13] & 0x02 != 0`

---

[🔼](#índice)

---

## **172. TCP Dump: ¿Qué es y cómo se utiliza?**

¡Perfecto — hacemos un deep dive práctico! `tcpdump` es la navaja suiza de línea de comandos para **capturar paquetes de red** en sistemas tipo Unix (Linux, macOS, BSD). Te sirve para troubleshooting, análisis forense, rendimiento y auditoría. A continuación tienes desde conceptos hasta comandos avanzados y ejemplos listos para copiar/pegar.

### 1. Concepto rápido

- `tcpdump` captura paquetes en una interfaz de red y permite **filtrarlos** con **BPF (Berkeley Packet Filter)** para grabar sólo lo que te interesa.
- Guarda capturas en formato **pcap** (compatible con Wireshark/tshark).
- **Legal / ético:** captura solo en equipos/redes tuyas o con autorización. Las pcaps contienen datos sensibles (credenciales, PII).

### 2. Permisos y preparación

- Normalmente requiere root o capacidades para abrir sockets en modo promiscuo.

```bash
# Capturar con sudo
sudo tcpdump -i eth0
# Dar capacidades para no usar sudo (Linux)
sudo setcap cap_net_raw,cap_net_admin=eip $(which tcpdump)
```

- Para bajar privilegios después de abrir el dispositivo:

```bash
sudo tcpdump -i eth0 -w out.pcap -Z nobody
```

### 3. Comandos esenciales (base y lectura)

Ver interfaces disponibles:

```bash
tcpdump -D
```

Capturar en pantalla (interfaz `eth0`):

```bash
sudo tcpdump -i eth0
```

Capturar N paquetes y salir:

```bash
sudo tcpdump -i eth0 -c 100
```

No resolver nombres (más rápido, salida numérica):

```bash
sudo tcpdump -i eth0 -n
```

Guardar a pcap (snaplen completo) y leer después:

```bash
sudo tcpdump -i eth0 -s 0 -w captura.pcap
tcpdump -r captura.pcap
```

Mostrar payload en ASCII (útil para HTTP claro):

```bash
sudo tcpdump -i eth0 -s 0 -A 'tcp port 80'
```

Más verboso / timestamps legibles:

```bash
sudo tcpdump -vvv -tttt -i eth0
```

### 4. Filtros BPF (prácticos y comunes)

Filtros básicos:

```bash
# Host origen o destino
tcpdump -i eth0 host 192.168.1.10

# Puertos
tcpdump -i eth0 tcp port 22
tcpdump -i eth0 udp port 53

# Red completa
tcpdump -i eth0 net 10.0.0.0/8

# Rango de puertos
tcpdump -i eth0 portrange 1000-2000
```

Combinaciones:

```bash
# Tráfico HTTP desde LAN interna hacia cualquier lado
sudo tcpdump -i eth0 'src net 192.168.1.0/24 and dst port 80'

# Todo excepto ARP y VLAN
sudo tcpdump -i eth0 'not arp and not vlan'
```

Filtros avanzados (bits TCP / flags):

```bash
# Capturar sólo SYN (paquetes de inicio de conexión)
sudo tcpdump -i eth0 'tcp[13] & 0x02 != 0'

# Capturar sólo paquetes con ACK
sudo tcpdump -i eth0 'tcp[13] & 0x10 != 0'
```

Buscar texto en payload (ejemplo: "GET "):

```bash
sudo tcpdump -i eth0 -s 0 'tcp[((tcp[12] & 0xf0) >> 2):4] = 0x47455420' -A
# 0x47 0x45 0x54 0x20 == "GET "
```

> Nota: filtros BPF indexan bytes del encabezado TCP; son poderosos pero frágiles — usa con cuidado.

### 5. Rotación, ring buffer y compresión (capturas largas)

Rotar cada N segundos (formato strftime):

```bash
sudo tcpdump -i eth0 -s 0 -w '/tmp/dump-%Y%m%d%H%M%S.pcap' -G 60
```

Rotar por tamaño (MB):

```bash
sudo tcpdump -i eth0 -s 0 -w /tmp/dump.pcap -C 100
# Crea dump.pcap, dump1.pcap, dump2.pcap...
```

Ring buffer (mantener sólo N archivos):

```bash
sudo tcpdump -i eth0 -s 0 -w '/tmp/dump-%Y%m%d%H%M%S.pcap' -G 3600 -W 24
# rota cada hora, mantiene 24 archivos máximo
```

Comprimir en vuelo:

```bash
sudo tcpdump -i eth0 -s 0 -w - | gzip > /tmp/capture.pcap.gz
```

### 6. Interpretación de una línea de salida

Ejemplo:

```
12:34:56.789012 IP 192.168.1.100.45678 > 93.184.216.34.80: Flags [S], seq 123456789, win 64240, options [mss 1460,sackOK], length 0
```

Desglose:

- `12:34:56.789012` → timestamp.
- `IP` → tipo de paquete (o `ARP`, `IP6`).
- `192.168.1.100.45678` → IP origen y puerto.
- `>` → indica dirección hacia destino.
- `93.184.216.34.80` → IP destino y puerto.
- `Flags [S]` → bandera TCP (S = SYN).
- `seq` → número de secuencia.
- `win` → ventana TCP.
- `options` → opciones TCP negociadas.
- `length` → longitud del payload.

### 7. Integración con Wireshark / tshark / ngrep

- Abre `.pcap` con Wireshark para análisis visual, reensamblado TCP y “Follow TCP stream”.

```bash
wireshark captura.pcap
```

- Usar `tshark` para extracción por línea:

```bash
tshark -r captura.pcap -Y "http.request" -T fields -e ip.src -e http.host -e http.request.uri
```

- `ngrep` es útil para búsquedas por expresiones en payload en tiempo real:

```bash
sudo ngrep -d eth0 'login' tcp
```

### 8. Casos de uso reales con ejemplos

1. **Capturar todo el tráfico y analizar después:**

```bash
sudo tcpdump -i eth0 -s 0 -w /tmp/full_capture.pcap
```

2. **Capturar solo DNS hacia 8.8.8.8:**

```bash
sudo tcpdump -i eth0 -n 'udp and dst host 8.8.8.8 and dst port 53' -c 200 -w dns_to_google.pcap
```

3. **Ver HTTP en tiempo real (no recomendado en redes muy cargadas):**

```bash
sudo tcpdump -i eth0 -s 0 -A 'tcp port 80' | less
```

4. **Capturar solo SYN (mapear intentos de conexión):**

```bash
sudo tcpdump -i eth0 'tcp[13] & 0x02 != 0' -n -tttt
```

5. **Captura distribuida / con poca carga en disco:**

```bash
sudo tcpdump -i eth0 -s 0 -w - | ssh user@collector 'cat > /data/capture-$(hostname)-$(date +%s).pcap'
```

### 9. Buenas prácticas y consejos prácticos

- **Filtra para reducir ruido**: capturar todo en entornos productivos es ineficiente y riesgoso.
- **Usa `-s 0`** si necesitas payload completo; si no, limita snaplen para ahorrar espacio.
- **Escribe a disco, no a pantalla**, cuando captures mucho (`-w`).
- **Protege las pcaps** (encriptar o acceso controlado).
- **Evita DNS reversas** (`-n`/`-nn`) para velocidad.
- **Comprueba el enlace/VM**: en VMs el tráfico puede estar en otra interfaz (`br0`, `virbr0`, `enp0s3`, `any`). Usa `tcpdump -D` y `-i any` si no estás seguro.
- **Ten cuidado con tráfico cifrado**: no podrás leer HTTPS sin man-in-the-middle autorizado.

### 10. Mini cheat sheet (rápido)

```text
Listar interfaces:       tcpdump -D
Capturar 100 paquetes:  sudo tcpdump -i eth0 -c 100 -n
Guardar pcap completo:  sudo tcpdump -i eth0 -s 0 -w out.pcap
Leer pcap:              tcpdump -r out.pcap -nn -tttt -X
Mostrar ASCII payload:  sudo tcpdump -i eth0 -s 0 -A 'tcp port 80'
Rotar por tiempo:       -G seconds -w 'dump-%Y%m%d%H%M%S.pcap'
Rotar por tamaño:       -C megabytes -w basefile
Solo SYN:               'tcp[13] & 0x02 != 0'
Filtro por host:        host 1.2.3.4
Filtro por puerto:      tcp port 443
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**         | **Siguiente 8**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_6_NMAP.md) | [⏩](./7_8_Tracert.md) |
