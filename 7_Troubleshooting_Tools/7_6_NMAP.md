| **Inicio**         | **atrás 5**          | **Siguiente 7**        |
| ------------------ | -------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_5_Route.md) | [⏩](./7_7_TcpDump.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [168. NMAP Website](#168-nmap-website)                                                                                       |
| [169. NMAP Cheat Sheet](#169-nmap-cheat-sheet)                                                                               |
| [170. Tutorial de Nmap para encontrar vulnerabilidades de red](#170-tutorial-de-nmap-para-encontrar-vulnerabilidades-de-red) |

# **NMAP**

## **168. NMAP Website**

![NMAP](/img/7_Troubleshooting_Tools/NMAP.png "NMAP")

Breve respuesta: **Nmap** (Network Mapper) es la herramienta de código abierto más usada para descubrir hosts, puertos y servicios en una red y para hacer auditorías de seguridad. Su web oficial (`nmap.org`) concentra descargas, documentación, el libro y el repositorio de scripts (NSE).

### ¿Qué ofrece el sitio web `nmap.org`?

En el sitio oficial encontrarás (entre otras cosas):

- Descargas oficiales para Windows/macOS/Linux y archivos fuente.
- Documentación completa: manual, capítulos (ej.: ejemplos, detección de OS, uso del NSE).
- Páginas sobre cuestiones legales y buenas prácticas para escaneos.
- Un host diseñado para practicar (`scanme.nmap.org`) que puedes usar para pruebas básicas.

### Instalación rápida (opciones comunes)

- **Linux (Debian/Ubuntu)**

  ```bash
  sudo apt update
  sudo apt install nmap
  ```

  (También puedes descargar binarios desde la web oficial si necesitas la última versión).

- **macOS**

  - Con Homebrew: `brew install nmap`
  - O usa el instalador `.dmg` desde la web oficial.

- **Windows**

  - Descarga el instalador desde la página de Nmap; en Windows también se instala Npcap (driver para captura de paquetes).

### Primeros comandos (escaneos **SEGUROS** y educativos)

> **IMPORTANTE:** sólo escanea hosts que posees o para los que tienes autorización. Para practicar usa `localhost` (127.0.0.1) o `scanme.nmap.org`.

1. **Escaneo básico de puertos (webserver = puertos 80/443)**

```bash
nmap -v -p 80,443 ejemplo.com
```

`-v` = verbose, `-p` = puertos.

2. **Detección de versiones y scripts “por defecto”**

```bash
nmap -sV -sC -p 80,443 ejemplo.com
```

`-sV` = versión del servicio; `-sC` = ejecuta los scripts NSE marcados como “default” (útiles y generalmente no intrusivos).

3. **Escaneo “agresivo” (combinado: versión, OS, traceroute, scripts)**

```bash
nmap -A ejemplo.com
```

`-A` es práctico en laboratorio, pero más ruidoso.

4. **Usar scripts NSE concretos para HTTP**

```bash
nmap --script=http-title,http-headers -p 80 ejemplo.com
```

`--script` permite elegir scripts NSE (ver más abajo sobre NSE y categorías).

5. **Guardar salida en varios formatos**

```bash
nmap -oN salida.txt -oX salida.xml -oA salida_todo ejemplo.com
```

`-oN` normal, `-oX` XML, `-oA` (guarda en normal, XML y grepable).

### Nmap Scripting Engine (NSE) — qué es y cómo usarlo

- El **NSE** permite ejecutar pequeños scripts (en Lua) que automatizan tareas: descubrimiento web, comprobación de certificados, detección de vulnerabilidades, fuerza bruta, etc. Es lo que convierte a Nmap en una herramienta muy poderosa y flexible.

- **Categorías de scripts**: `default`, `safe`, `vuln`, `intrusive`, `exploit`, `discovery`, `brute`, `dos`, etc. Las categorías permiten elegir scripts por su intención y nivel de riesgo. Por ejemplo `safe` y `default` son generalmente menos ruidosos; `intrusive` / `exploit` pueden alterar o dañar servicios y **no** deben ejecutarse sin permiso. (usa `--script "default and safe"` para ejecutar sólo los de ambas categorías).

- **Ejemplo**: enumerar directorios comunes en una web (script `http-enum`):

```bash
nmap --script=http-enum -p 80 ejemplo.com
```

(Este script intenta identificar rutas y aplicaciones web conocidas — leer la documentación del script antes de usarlo).

### Tipos de escaneo importantes (resumen)

- `-sS` — SYN scan (“stealth”), **requiere privilegios** (root/administrador).
- `-sT` — TCP connect (no requiere privilegios, usa la pila TCP del SO).
- `-sU` — UDP scan (más lento/no fiable; se usa para servicios UDP).
- `-sV` — versión del servicio.
- `-O` — detección de sistema operativo (requiere privilegios y a veces puertos abiertos).
- `-T0..T5` — plantillas de timing (T4 es “rápido”).
- `-Pn` — no hacer host discovery (trata al host como “up”).

(La lista completa y sus matices están en el manual de Nmap).

### Cómo interpretar un resultado típico (fragmento de ejemplo)

```
Nmap scan report for ejemplo.com (93.184.216.34)
Host is up (0.02s latency).
PORT    STATE  SERVICE
80/tcp  open   http
443/tcp open   https
Service Info: Server: Apache
```

- **STATE**: `open` (servicio responde), `closed` (puerto accesible pero sin servicio), `filtered` (un firewall/ACL bloquea la respuesta).
- **Service Info** y **version** (si se usó `-sV`) ayudan a identificar software y versiones; útil para inventario y priorizar parches.

### Buenas prácticas y aspectos legales / éticos

- **Nunca** escanees redes/hosts sin permiso. Nmap puede ser benigno técnicamente, pero usarlo sin autorización puede traerte problemas legales, sanciones por ISPs, o responsabilidad laboral. Lee la guía legal del proyecto Nmap y actúa con autorización escrita cuando hagas pruebas de penetración.
- Para **practicar**: usa `localhost`, máquinas de tu red privada, laboratorios intencionales (VulnHub, OWASP Juice Shop), o `scanme.nmap.org` (servidor de prueba de Nmap).
- **Evita** scripts `intrusive` o `exploit` en redes de terceros; úsalos sólo en entornos controlados con permiso.

### Recursos recomendados (rápidos)

- Web oficial (descargas + docs): nmap.org.
- Capítulo sobre NSE (Scripting Engine): guía oficial.
- Página “Legal Issues” de Nmap (leer antes de escanear en producción).
- Host de pruebas: `scanme.nmap.org`.

### Mini checklist para comenzar (rápido)

1. Instala Nmap desde la web oficial o tu gestor de paquetes.
2. Prueba: `nmap -v scanme.nmap.org`.
3. Haz un `-sV -sC` contra **tus** hosts para aprender.
4. Lee la documentación del NSE antes de ejecutar scripts: `--script-help <categoria|script>`.
5. Documenta y pide permiso por escrito antes de hacer pruebas en redes de terceros.

---

[🔼](#índice)

---

## **169. NMAP Cheat Sheet**

### 📝 Hoja de referencia de Nmap (Uso básico y avanzado)

⚠️ **Nota importante**: usa Nmap **solo en equipos/servidores tuyos o con autorización explícita**. Para practicar, puedes usar `scanme.nmap.org`.

### 🔍 Descubrimiento de hosts

```bash
nmap 192.168.1.1              # Escaneo simple a un host
nmap 192.168.1.0/24           # Escaneo a toda la red (subred /24)
nmap scanme.nmap.org          # Escaneo a un host de prueba oficial
nmap -sn 192.168.1.0/24       # Descubrimiento de hosts (ping scan, sin puertos)
```

### 🎯 Escaneos de puertos

```bash
nmap -p 22 192.168.1.1        # Escanear puerto 22
nmap -p 80,443 192.168.1.1    # Escanear puertos específicos
nmap -p 1-1000 192.168.1.1    # Escanear rango de puertos
nmap -p- 192.168.1.1          # Escanear todos los puertos (1-65535)
```

### ⚡ Tipos de escaneo

```bash
nmap -sS 192.168.1.1          # Escaneo TCP SYN (rápido, "stealth")
nmap -sT 192.168.1.1          # Escaneo TCP connect (sin privilegios)
nmap -sU 192.168.1.1          # Escaneo de puertos UDP
nmap -sA 192.168.1.1          # Escaneo de firewall (ACK scan)
nmap -sV 192.168.1.1          # Detección de versiones de servicios
nmap -O 192.168.1.1           # Detección de sistema operativo
nmap -A 192.168.1.1           # Escaneo agresivo (OS, servicios, traceroute, scripts)
```

### 🧩 Scripts NSE (Nmap Scripting Engine)

```bash
nmap --script=default 192.168.1.1    # Ejecuta scripts por defecto
nmap --script=vuln 192.168.1.1       # Scripts de búsqueda de vulnerabilidades
nmap --script=http-* 192.168.1.1     # Scripts relacionados con HTTP
nmap --script-help http-enum         # Ver ayuda de un script
```

Ejemplo:

```bash
nmap --script=http-title -p 80 ejemplo.com
```

### ⏱️ Timing y evasión

```bash
nmap -T4 192.168.1.1          # Escaneo rápido (más agresivo)
nmap -T1 192.168.1.1          # Escaneo lento (sigiloso)
nmap -f 192.168.1.1           # Fragmentar paquetes (evasión de IDS/IPS)
nmap -D RND:10 192.168.1.1    # Uso de decoys (IPs señuelo)
nmap -Pn 192.168.1.1          # No hacer descubrimiento de host (ignora ping)
```

### 📂 Salida de resultados

```bash
nmap -oN salida.txt 192.168.1.1    # Guarda en archivo normal
nmap -oX salida.xml 192.168.1.1    # Guarda en XML
nmap -oG salida.grep 192.168.1.1   # Salida grepable
nmap -oA resultados 192.168.1.1    # Guarda en todos los formatos
```

### 🌐 Escaneo de múltiples objetivos

```bash
nmap -iL hosts.txt               # Leer lista de hosts desde archivo
nmap 192.168.1.1,192.168.1.50    # Escanear hosts específicos
nmap 192.168.1.1-20              # Escanear rango de hosts
```

### 📌 Ejemplo completo

```bash
nmap -sS -sV -O -p 1-1000 -T4 -oN informe.txt scanme.nmap.org
```

👉 Escaneo SYN, detecta versiones, SO, 1-1000 puertos, rápido, y guarda salida.

### ✅ Resumen rápido

- `-sS` → SYN scan (común)
- `-sU` → UDP scan
- `-sV` → Detección de versiones
- `-O` → Detección de SO
- `-A` → Escaneo agresivo (OS, scripts, traceroute)
- `-T0..T5` → Velocidad (0 = lento, 5 = muy rápido)
- `-p` → Puertos
- `--script` → Ejecutar scripts NSE
- `-oN/-oX/-oA` → Guardar resultados

---

[🔼](#índice)

---

## **170. Tutorial de Nmap para encontrar vulnerabilidades de red**

> ⚠️ **Ético y legal**: usa Nmap **solo** en activos tuyos o con autorización explícita. Mantendremos el enfoque en **detección** (no explotación), priorizando scripts `safe`, `default` y `vuln` que no alteran servicios.

### 1) Preparativos y alcance

1. Define el **alcance** (IPs, rangos, ventanas de tiempo, exclusiones).
2. Asegúrate de tener **privilegios** (en Linux, usa `sudo` para escaneos SYN `-sS` y detección de OS `-O`).
3. Crea archivos de **lista de objetivos**:

```bash
echo -e "192.168.1.10\n192.168.1.20" > hosts.txt
```

### 2) Estrategia general (playbook)

1. **Descubrir hosts vivos** → `-sn`
2. **Descubrir puertos** → `-p`/`-p-` con `-sS`/`-sT` + `-sU` si aplica
3. **Identificar servicios** → `-sV` y **SO** → `-O`
4. **NSE focalizado** por tecnología → `--script` (`default`, `safe`, `vuln`, `http-*`, `ssl-*`, `smb-*`, etc.)
5. **Guardar evidencias** → `-oA` y generar un **informe** (riesgo/impacto/mitigación).

### 3) Descubrimiento de hosts (no intrusivo)

```bash
# Subred completa
sudo nmap -sn 192.168.1.0/24 -oA 01-hosts_up

# Lista personalizada (+ excluir algo si hace falta)
sudo nmap -sn -iL hosts.txt --exclude 192.168.1.100 -oA 01-hosts_up_sel
```

- Si ICMP está filtrado, fuerza trato como “arriba”: `-Pn` (ojo: más tráfico a cada host).

### 4) Enumeración de puertos y servicios

#### TCP (rápido y común)

```bash
# Top 1000 puertos TCP con detección de versiones
sudo nmap -sS -sV -T4 192.168.1.0/24 -oA 02-tcp_top1k

# Todos los puertos TCP (más exhaustivo)
sudo nmap -sS -sV -p- --min-rate 1000 192.168.1.0/24 -oA 02-tcp_all
```

#### UDP (servicios infra: DNS, SNMP, NTP, etc.)

```bash
# UDP top (rápido)
sudo nmap -sU --top-ports 100 192.168.1.0/24 -oA 03-udp_top

# UDP puertos específicos
sudo nmap -sU -p 53,123,161 192.168.1.0/24 -oA 03-udp_sel
```

#### Detección de SO (útil para mapping de vulnerabilidades)

```bash
sudo nmap -O --osscan-guess 192.168.1.10 -oA 04-osdetect
```

### 5) Búsqueda de vulnerabilidades con NSE (enfoque seguro)

> Tip: actualiza el índice de scripts si lo necesitas: `sudo nmap --script-updatedb`
> Ver ayuda de un script: `nmap --script-help <nombre>`

#### A) Servicios web (HTTP/HTTPS)

Detecta cabeceras, título, tecnologías, rutas típicas y fallos conocidos.

```bash
# Panorama rápido y seguro
nmap -p 80,443 -sV --script "default,safe,http-headers,http-title,http-server-header" ejemplo.com -oA web01-basico

# Enumeración de rutas y metadata
nmap -p 80 --script "http-enum,http-robots.txt" ejemplo.com -oA web02-enum

# TLS/SSL: cifrados débiles, certificados, protocolos inseguros
nmap -p 443 --script "ssl-enum-ciphers,ssl-cert,ssl-dh-params" ejemplo.com -oA web03-tls
```

Qué buscar:

- **HTTP**: server banner antiguo (Apache/IIS/nginx viejos), directorios por defecto, `X-Powered-By`, falta de `X-Frame-Options`, `Content-Security-Policy` (indicios de hardening pobre).
- **TLS**: soporte de **SSLv3/TLS1.0**, suites débiles (RC4/3DES), DH pequeño, cert caducado → **riesgo alto**.

#### B) SMB / Windows

```bash
# Detección general y dureza de SMB
sudo nmap -p 445 -sV --script "smb2-security-mode,smb2-time,smb-os-discovery" 192.168.1.0/24 -oA smb01-info

# Checks de vulnerabilidades comunes (ejemplos)
sudo nmap -p 445 --script "vuln and smb-*" 192.168.1.0/24 -oA smb02-vuln
```

Qué buscar:

- SMB signing deshabilitado, versiones antiguas, shares expuestos.
- CVEs conocidos reportados por scripts `vuln` (verifica con `--script-help` y prioriza sistemas críticos).

#### C) FTP

```bash
nmap -p 21 -sV --script "ftp-anon,ftp-syst,default,safe" 192.168.1.0/24 -oA ftp01
```

Señales:

- **Anon login** habilitado, versiones viejas, FTP claro en vez de FTPS.

#### D) SSH

```bash
nmap -p 22 -sV --script "ssh2-enum-algos,ssh-hostkey" 192.168.1.0/24 -oA ssh01
```

Señales:

- Algoritmos débiles/obsoletos, claves cortas, banners con versiones viejas.

#### E) DNS / UDP 53

```bash
sudo nmap -sU -p 53 --script "dns-recursion,dns-nsid" 192.168.1.0/24 -oA dns01
```

Señales:

- **Recursion abierta** hacia Internet → riesgo de abuso y filtrado de datos.

#### F) SNMP / UDP 161

```bash
sudo nmap -sU -p 161 --script "snmp-info,snmp-netstat,snmp-processes" 192.168.1.0/24 -oA snmp01
```

Señales:

- Comunidades por defecto (`public/private`), SNMPv1/v2 sin cifrado, exceso de información.

#### G) Correo y otros

```bash
# SMTP (Open relay y banners)
nmap -p 25 -sV --script "smtp-open-relay,smtp-commands" mail.midominio.com -oA smtp01

# Bases de datos
nmap -p 3306,5432,1433,1521 -sV --script "default,safe" 192.168.1.0/24 -oA db01
```

> 💡 Consejo: comienza con `--script "default and safe"` y añade `vuln` cuando tengas claro el impacto y la ventana de mantenimiento.

### 6) Priorización y validación de hallazgos

1. **Exposición**: ¿servicio crítico accesible desde redes no confiables?
2. **Severidad**: CVE/versión afectada, cifrados débiles, auth débil, servicios sin necesidad.
3. **Facilidad de explotación**: ¿requiere credenciales? ¿remoto/anon?
4. **Impacto**: confidencialidad/integridad/disponibilidad (CIA).

Valida:

- Corrobora banners con `-sV` y si puedes, con el propio servicio (`curl -I`, `openssl s_client`, `smbclient -L`, etc.).
- Repite escaneo focalizado para descartar **falsos positivos**.

### 7) Dos ejemplos end-to-end

#### A) Red interna (inventario → web y SMB)

```bash
# 1) Hosts vivos
sudo nmap -sn 192.168.10.0/24 -oA A1-hosts

# 2) Puertos top TCP + versiones
sudo nmap -sS -sV -T4 192.168.10.0/24 -oA A2-tcp

# 3) Foco en HTTP/HTTPS
sudo nmap -p 80,443 --script "default,safe,http-headers,http-title,ssl-enum-ciphers,ssl-cert" -iL A1-hosts.gnmap -oA A3-web

# 4) Foco en SMB
sudo nmap -p 445 --script "smb2-security-mode,smb-os-discovery,vuln and smb-*" -iL A1-hosts.gnmap -oA A4-smb
```

**Qué esperas ver**: servidores con TLS débil, certificados vencidos, SMB sin firma, sistemas Windows con versiones antiguas.

#### B) Host público autorizado (web endurecimiento)

```bash
# 1) Puertos y versiones
nmap -sS -sV -p 80,443 -T4 sitio.com -oA B1-web

# 2) Scripts orientados a web
nmap -p 80,443 --script "default,safe,http-headers,http-title,http-enum,ssl-enum-ciphers,ssl-cert" sitio.com -oA B2-web_scripts
```

**Qué esperas ver**: cabeceras de seguridad faltantes, rutas/recursos expuestos, suites TLS inseguras.

### 8) Guardar resultados y armar informe

```bash
# Guardar en todos los formatos (normal, XML, grepable)
nmap -oA resultados_prefijo <opciones> <objetivos>

# Extra: filtrar hosts con 445 abierto desde salida grepable
grep "/445/open/" resultados_prefijo.gnmap | awk '{print $2}' > smb_hosts.txt
```

En el informe final por hallazgo incluye: **evidencia** (salida Nmap), **riesgo**, **impacto**, **CVE/Referencia**, **recomendación** (desactivar servicio, parchear, endurecer TLS, cerrar puerto en firewall, mover a red interna, etc.).

### 9) Consejos de rendimiento y calidad

- Usa `-n` para evitar resolución DNS (más rápido).
- Controla el ritmo: `--min-rate 500` o `--max-rate`, y el tiempo por host `--host-timeout 30m`.
- Si hay IPS/IDS, reduce ruido: `-T2/-T1`, evita `-A` fuera de horario.
- Repite escaneos en otra ventana temporal para minimizar falsos negativos.

### 10) Plantillas rápidas (copiar/pegar)

```bash
# Descubrir y mapear rápido (LAN)
sudo nmap -sn 10.0.0.0/24 -oA hostsup
sudo nmap -sS -sV -O -p- -T4 -iL hostsup.gnmap -oA fullmap

# Web baseline (seguro)
nmap -p 80,443 -sV --script "default,safe,http-headers,http-title,ssl-enum-ciphers,ssl-cert" target -oA web_baseline

# SMB baseline
sudo nmap -p 445 -sV --script "smb2-security-mode,smb-os-discovery,vuln and smb-*" -iL hosts.txt -oA smb_baseline

# UDP esencial
sudo nmap -sU -p 53,123,161 --script "default,safe" -iL hosts.txt -oA udp_core
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**          | **Siguiente 7**        |
| ------------------ | -------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_5_Route.md) | [⏩](./7_7_TcpDump.md) |
