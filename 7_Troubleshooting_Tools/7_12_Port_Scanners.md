| **Inicio**         | **atrás 11**                    | **Siguiente 13**                   |
| ------------------ | ------------------------------- | ---------------------------------- |
| [🏠](../README.md) | [⏪](./7_11_Packet_Sniffers.md) | [⏩](./7_13_Protocol_Analyzers.md) |

---

## **Índice**

| Temario                                                                                              |
| ---------------------------------------------------------------------------------------------------- |
| [182. Los 5 mejores escáneres de puertos](#182-los-5-mejores-escáneres-de-puertos)                   |
| [183. Cómo usar Nmap para buscar puertos abiertos](#183-cómo-usar-nmap-para-buscar-puertos-abiertos) |

# **Port Scanners**

## **182. Los 5 mejores escáneres de puertos**

![Port Scanners](/img/7_Troubleshooting_Tools/Port_Scanners.jpeg "Port Scanners")

### 1) **Nmap** — el estándar “todo-en-uno”

**Qué es / por qué está aquí:** Nmap (Network Mapper) es la herramienta más conocida y completa: descubrimiento de hosts, escaneo de puertos (muchos modos), detección de versiones, detección de SO y un potente motor de scripting (NSE) para automatizar pruebas. Es ideal para auditorías detalladas y análisis forense de redes.

**Fortalezas:** detección de servicios/versión, scripts NSE, gran comunidad, numerosas opciones de salida (XML, grepable, etc.).
**Limitaciones:** no es el más rápido si necesitas barridos masivos del Internet público (pero es el más completo por host).

**Ejemplos**

- Escaneo rápido de puertos comunes + detección de versión:

```bash
sudo nmap -sS -sV -p 1-1000 192.168.1.0/24 -T4
```

- Escaneo “profundo” y scripts NSE:

```bash
sudo nmap -A --script "default,safe" -p 1-65535 10.0.0.5 -oA salida_nmap
```

### 2) **Masscan** — velocidad bruta para barridos a gran escala

**Qué es / por qué está aquí:** Masscan está diseñado para velocidad: puede transmitir millones de paquetes por segundo y escanear el espacio IPv4 a gran escala. Úsalo cuando necesites encontrar rápidamente puertos abiertos en grandes rangos (ej.: descubrimiento a Internet a gran escala).

**Fortalezas:** velocidad extrema, sintaxis simple de salida (se puede parsear y pasar a Nmap).
**Limitaciones:** no hace fingerprinting o enumeración avanzada — su trabajo es encontrar puertos abiertos; para investigar servicios usa Nmap luego.

**Ejemplo**

- Escaneo rápido de puertos 80 y 443 en una red:

```bash
sudo masscan 192.168.0.0/16 -p80,443 --rate 10000 -oL masscan_results.txt
```

_(Ajusta `--rate` según tu red / ancho de banda.)_

### 3) **ZMap** — escaneos “Internet-wide” y ciencia de medición de red

**Qué es / por qué está aquí:** ZMap es un escáner estateless optimizado para encuestas de Internet: muy rápido para escanear un único puerto a través de todo el espacio IPv4 (usado por investigadores y proyectos como censys/shodan-style scans).

**Fortalezas:** escaneos a nivel de investigación (puedes escanear toda la IPv4 en minutos con la infraestructura adecuada).
**Limitaciones:** está pensado para single-port/large-scale surveys; para banner grabbing y enumeración de servicios usa herramientas complementarias (ZGrab).

**Ejemplo**

- Escanear rápidamente el puerto 80 en un rango grande (requiere permisos, tuning de kernel y red):

```bash
sudo zmap -p 80 203.0.113.0/24 -o results.csv
```

(Para Internet-wide hay que preparar NIC, kernel, y filtrado apropiado.)

### 4) **RustScan** — rápido, moderno y “tubería” directa a Nmap

**Qué es / por qué está aquí:** RustScan es un escáner moderno escrito en Rust centrado en velocidad y ergonomía: detecta puertos rápidamente y puede **inyectar** los resultados directamente en Nmap para fingerprinting detallado. Es una excelente opción cuando quieres velocidad pero no perder las capacidades de Nmap.

**Fortalezas:** muy rápido en scan de puertos locales / subredes, integración automática con Nmap, configuración amigable.
**Limitaciones:** aún depende de Nmap para análisis profundo; para Internet-wide puro prefieren Masscan/ZMap.

**Ejemplo**

- Escanear y pasar puertos abiertos a Nmap automáticamente:

```bash
rustscan -a 192.168.1.50 -p 1-65535 -- -sV -A
```

(Esta sintaxis hace el scan de puertos con RustScan y ejecuta Nmap sobre los puertos encontrados.)

### 5) **Angry IP Scanner** — simple, rápido y multiplataforma (UI y CLI)

**Qué es / por qué está aquí:** Angry IP Scanner es una herramienta ligera, práctica para administradores que necesitan escaneos rápidos de LAN con interfaz gráfica (y también CLI). Es fácil de usar, portátil (Java) y extensible con plugins. Ideal para inventario de red y escaneos ad-hoc.

**Fortalezas:** facilidad de uso, multiplataforma, exporta resultados (CSV), plugins para obtener más info (NetBIOS, ping, etc.).
**Limitaciones:** no está orientada a auditoría profunda ni a escaneos masivos a escala Internet.

**Ejemplo**

- Escaneo GUI: abrir la app, escribir `192.168.1.0/24` y Start.
- CLI (ejemplo de uso básico si tienes JAR/instalación):

```bash
java -jar angryip-<version>.jar 192.168.1.0/24 -r -o output.csv
```

### Comparación rápida (¿qué elegir según tu objetivo?)

- **Auditoría detallada por host / pentest:** Nmap.
- **Barrido a gran escala / descubrir puertos abiertos en muchos hosts:** Masscan o ZMap.
- **Escaneo rápido con pipeline a Nmap (velocidad + detalle):** RustScan.
- **Inventario de red sencillo y multiplataforma:** Angry IP Scanner.

### Consejos prácticos y seguridad (importante)

- **Pide permiso por escrito** antes de escanear redes/hosts que no sean tuyos — el escaneo no autorizado puede causar que te denuncien o que bloqueen tu IP (consulta la sección legal de Nmap).
- **No escales la tasa (`--rate`) en una red productiva** sin supervisión: puedes saturar enlaces o disparar IDS.
- Flujo recomendado para un inventario grande: usa Masscan/ZMap para **encontrar** hosts/puertos abiertos → luego usa Nmap/RustScan para **enumerar** servicios y ejecutar scripts de verificación.

---

[🔼](#índice)

---

## **183. Cómo usar Nmap para buscar puertos abiertos**

Nmap (Network Mapper) es la herramienta estándar para **descubrir hosts y puertos abiertos**, identificar servicios y hacer un primer reconocimiento de una red. Abajo tienes desde los conceptos básicos hasta flujos prácticos y comandos listos para copiar/pegar. ⚠️ **Siempre** escanea solo hosts/redes que controles o para los que tengas permiso.

### 1) Requisitos y consideraciones iniciales

- **Instalación**: en Linux `sudo apt install nmap` (o `yum`, `pacman`); macOS `brew install nmap`; Windows usar el instalador oficial (requiere Npcap).
- **Privilegios**: para `-sS` (SYN scan) y `-O` (detección de SO) necesitas privilegios de root/administrador. Sin privilegios Nmap recurre a `-sT` (connect scan).
- **Impacto en la red**: escaneos agresivos o a gran escala pueden saturar enlaces o activar IDS/IPS. Ajusta `--rate` (en Masscan) o `-T` en Nmap.
- **Legal**: pide autorización por escrito si trabajas en redes ajenas.

### 2) Conceptos clave (rápido)

- **open** = puerto responde y hay servicio.
- **closed** = puerto accesible pero sin servicio.
- **filtered** = no se recibió respuesta (firewall/ACL).
- **SYN scan (`-sS`)** = rápido y común, requiere root.
- **TCP connect (`-sT`)** = usa la pila TCP del SO (sin root).
- **UDP scan (`-sU`)** = más lento e inexacto; imprescindible para servicios UDP.
- **Service/version detection (`-sV`)** = intenta identificar software y versión.
- **Scripts NSE (`--script`)** = motor para automatizar comprobaciones (desde info hasta vulnerabilidades).
- **Timing (`-T0..T5`)** = control de velocidad; `-T4` es rápido, `-T3` por defecto, `-T1/T0` sigiloso.

### 3) Comandos básicos para buscar puertos abiertos

#### Escaneo rápido (puertos “top 1000”, detección básica)

```bash
sudo nmap -sS -T4 192.168.1.10
```

- `-sS` SYN scan (necesita root).
- `-T4` velocidad (LAN).
  **Salida típica**:

```
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
443/tcp  open  https
```

#### Solo ver _puertos abiertos_ (filtrar salida)

```bash
sudo nmap --open -sS -T4 192.168.1.10
```

`--open` muestra únicamente puertos `open`.

#### Escanear **todos** los puertos TCP (1-65535)

```bash
sudo nmap -sS -p- -T4 192.168.1.10 -oA full_tcp_scan
```

- `-p-` = todos los puertos.
- `-oA` guarda resultados en `full_tcp_scan.{nmap,xml,gnmap}`.

#### Escaneo de puertos UDP (lento)

```bash
sudo nmap -sU -p 53,67,123,161 192.168.1.10 -T3 -oN udp_scan.txt
```

- `-sU` UDP scan.
- Es normal que el scan UDP tarde y tenga falsos negativos; suele combinarse con TCP scans.

#### Detección de servicios y versiones

```bash
sudo nmap -sS -sV -p 22,80,443 192.168.1.10 -oN services.txt
```

- `-sV` intenta identificar software/version.

#### Escaneo agresivo (detectar SO, traceroute, scripts por defecto)

```bash
sudo nmap -A 192.168.1.10 -oN aggressive.txt
```

- `-A` = `-sV -O --traceroute --script=default` (más ruidoso).

### 4) Escaneo en red / varios hosts

#### Descubrir hosts “vivos” en una subred (ping-scan)

```bash
nmap -sn 192.168.1.0/24 -oN hosts_up.txt
```

- `-sn` = host discovery only (no escaneo de puertos).

#### Escanear una lista de objetivos

```bash
nmap -iL targets.txt -oA scan_all_targets
```

- `targets.txt` = lista de IP/host por línea.

### 5) Flujograma práctico recomendado (reconocimiento seguro)

1. **Descubrir hosts**: `nmap -sn 10.0.0.0/24`
2. **Escaneo rápido de puertos** (top): `nmap -sS -T4 -F <host>`
3. **Escaneo completo de puertos** (si hay algo interesante): `nmap -p- -sS <host>`
4. **Detección de servicios**: `nmap -sV -sC <host>` (`-sC` = scripts default)
5. **Scripts específicos** (vuln): `nmap --script vuln -p <puertos> <host>`
6. **Validación manual**: `curl`, `openssl s_client`, `smbclient`, `ncat` para comprobar servicios.

Ejemplo conjunto:

```bash
# 1) hosts vivos
nmap -sn 192.168.10.0/24 -oN step1_hosts.txt

# 2) quick scan sobre el primero vivo
sudo nmap -sS -T4 -F 192.168.10.15 -oN step2_quick.txt

# 3) all-ports y versiones
sudo nmap -sS -p- -T4 192.168.10.15 -oA step3_allports
sudo nmap -sV -p 22,80,443 192.168.10.15 -oN step3_services.txt

# 4) scripts vulnerables (solo con permiso)
sudo nmap --script vuln -p 22,80,443 192.168.10.15 -oN step4_vulns.txt
```

### 6) Interpretación de resultados (qué significan los estados)

Ejemplo salida:

```
PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 7.6p1
80/tcp   filtered http
139/tcp  closed   netbios-ssn
```

- `open` = servicio responde → objetivo para enumeración.
- `filtered` = firewall o filtrado (no se sabe si está abierto).
- `closed` = puerto accesible pero sin servicio (se puede abrir en el futuro).
- `Service/Version` = datos recogidos por `-sV`.

Usa `--reason` para saber por qué Nmap marcó un puerto como está (respuesta recibida, ttl, tcp-reset, icmp-type, etc.):

```bash
sudo nmap --reason -sS 192.168.1.10
```

### 7) Escaneos UDP y combinados (ejemplo avanzado)

UDP es costoso: usar `--top-ports` y subir tiempo:

```bash
sudo nmap -sU --top-ports 100 -T3 192.168.1.10 -oN udp_top100.txt
# combinar TCP y UDP
sudo nmap -sS -sU -p T:1-1024,U:53,123 192.168.1.10 -oN tcp_udp_combo.txt
```

### 8) Nmap Scripting Engine (NSE) — ejemplos útiles

- **default & safe** (no intrusivos):

```bash
sudo nmap -sV --script "default,safe" -p 80,443 192.168.1.10
```

- **vulnerability checks** (solo con permiso):

```bash
sudo nmap --script vuln -p 80,443 192.168.1.10
```

- **HTTP enumeration**:

```bash
nmap -p 80 --script http-enum,http-title,http-headers 192.168.1.10
```

Siempre revisa `--script-help <script>` para ver qué hace (y su riesgo).

### 9) Tuning (timing / stealth / evitar detección)

- **Timing templates**: `-T0` (paranoid) → `-T5` (insane). Para LAN `-T4`; para redes productivas usar `-T2/-T3`.
- **Evitar ruido**: reducir `-T`, aumentar `--host-timeout`, bajar `--max-retries`.
- **Fragmentación / decoys / source-port spoofing** (evasión — _no recomendado salvo en laboratorios autorizados_):

  - `-f` fragment packets
  - `-D RND:10` decoys
  - `-S <ip>` spoof source (requiere rutas y privilegios)
    Estos métodos pueden disparar IDS y son potencialmente ilegales sin permiso.

### 10) Salidas y procesamiento

- Guardar todo: `-oA nombre_base` (guarda .nmap, .xml, .gnmap).
- Salida grepable: `-oG file.gnmap` → luego `grep "/open/" file.gnmap` para extraer hosts con puertos abiertos:

```bash
grep "/open/" scan.gnmap | awk '{print $2 " " $4}'
```

- XML (`-oX`) se usa para integrarlo con otras herramientas.

### 11) Ejemplo práctico completo (explicado paso a paso)

Objetivo: encontrar puertos abiertos en `scanme.nmap.org` (host público de prueba).

```bash
# 1) Quick discovery (top ports)
nmap -v -T4 scanme.nmap.org -oN step1_quick.txt

# 2) Show only open ports and do service detection
sudo nmap --open -sS -sV -p- -T4 scanme.nmap.org -oA step2_full

# 3) Run safe NSE scripts for HTTP/SSL
sudo nmap -p 80,443 --script "default,safe,http-title,ssl-enum-ciphers" scanme.nmap.org -oN step3_nse.txt
```

Interpreta cada archivo `.nmap`/`.gnmap` y usa `-sV` para priorizar pruebas manuales (curl, openssl s_client, etc.).

### 12) Buenas prácticas y checklist final

- Escanea con permiso y documenta el alcance.
- Empieza suave (`-sn`, `-F`) y sube el detalle si encuentras algo.
- Conserva salidas (`-oA`) y anotaciones (timestamps).
- Usa `--open` para filtrar ruido.
- Prioriza `-sV` + `--script "default,safe"` antes de `--script vuln`.
- Para producción: coordina con equipos de red (evita DoS/alarma de IDS).
- Verifica manualmente (ncat, curl, smbclient) cualquier hallazgo antes de considerarlo un hallazgo final.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**                    | **Siguiente 13**                   |
| ------------------ | ------------------------------- | ---------------------------------- |
| [🏠](../README.md) | [⏪](./7_11_Packet_Sniffers.md) | [⏩](./7_13_Protocol_Analyzers.md) |
