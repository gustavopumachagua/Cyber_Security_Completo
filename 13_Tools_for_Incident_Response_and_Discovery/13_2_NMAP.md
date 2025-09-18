| **Inicio**         | **atrás 1**         | **Siguiente 3**      |
| ------------------ | ------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./13_1_dig.md) | [⏩](./13_3_ping.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [414. NMAP](#414-nmap)                                                                                                       |
| [415. Tutorial de Nmap para encontrar vulnerabilidades de red](#415-tutorial-de-nmap-para-encontrar-vulnerabilidades-de-red) |

# **NMAP**

## **414. NMAP**

![NMAP](/img/13_Tools_for_Incident_Response_and_Discovery/NMAP.png "NMAP")

### Qué es Nmap (breve)

Nmap (Network Mapper) es una herramienta de auditoría de red para descubrir hosts, servicios, versiones, sistemas operativos y características de una red. Es muy usada por administradores y profesionales de seguridad para inventario y hardening.

### Instalación rápida

- Debian/Ubuntu:

```bash
sudo apt update
sudo apt install nmap
```

- Fedora/RHEL:

```bash
sudo dnf install nmap
```

- macOS (Homebrew):

```bash
brew install nmap
```

### Estados de un puerto (qué significa)

- `open` — hay un servicio escuchando y respondió.
- `closed` — no hay servicio en ese puerto, pero el host respondió.
- `filtered` — un firewall o filtro impide saber si está abierto (no responde o ICMP bloqueado).
- `unfiltered` / `open|filtered` / `closed|filtered` — estados intermedios según el tipo de scan.

### Comandos y opciones esenciales (cheat sheet)

#### 1) Escaneo simple — escanear puertos más comunes (rápido)

```bash
nmap example.com
```

Por defecto hace un SYN/Connect a puertos comunes y reporta servicios.

#### 2) Escaneo de rango de puertos

- puertos específicos:

```bash
nmap -p 22,80,443 192.168.1.10
```

- rango:

```bash
nmap -p 1-65535 192.168.1.10
```

### 3) Escaneo de red (descubrimiento de hosts)

```bash
nmap -sn 192.168.1.0/24
```

`-sn` (ping scan) detecta hosts vivos sin hacer escaneo de puertos.

### 4) Escaneo SYN (rápido y sigiloso — requiere privilegios)

```bash
sudo nmap -sS 192.168.1.10
```

Necesita permisos de root en la mayoría de sistemas.

### 5) Escaneo TCP connect (no requiere privilegios)

```bash
nmap -sT 192.168.1.10
```

Usa la pila TCP del sistema (menos “sigiloso”).

### 6) Escaneo UDP

```bash
sudo nmap -sU 192.168.1.10
```

UDP es lento y puede devolver muchos `open|filtered`.

### 7) Detección de versión de servicio

```bash
sudo nmap -sV 192.168.1.10
```

Intenta identificar software y versión (ej. `OpenSSH 8.2p1`).

### 8) Detección de sistema operativo

```bash
sudo nmap -O 192.168.1.10
```

`-O` intenta identificar el OS por huellas TCP/IP.

### 9) Escaneo “agresivo” (combinado)

```bash
sudo nmap -A 192.168.1.10
```

Equivale a `-sV -O --script=default` (ruidoso, útil en laboratorio).

### 10) Usar scripts (Nmap Scripting Engine, NSE)

```bash
nmap --script=discovery 192.168.1.0/24
```

- `--script=default` ejecuta scripts considerados “seguros” por defecto.
- No ejecutes scripts de explotación en sistemas que no controlas.

### 11) Guardar salida (varios formatos)

```bash
nmap -oA salida_basica 192.168.1.10
```

Crea `salida_basica.nmap` (texto), `.xml` y `.gnmap` (útil para otras herramientas).

### 12) Escaneo por tiempo / velocidad

```bash
nmap -T4 192.168.1.0/24
```

`-T0` (muy lento) … `-T5` (muy rápido, ruidoso). Para redes locales `-T4` suele estar bien.

### 13) Escanear solo hosts vivos (combinar)

```bash
nmap -sn -PS22,80 192.168.1.0/24
```

Envía probes SYN (`-PS`) a puertos 22 y 80 para determinar hosts activos.

### 14) Escaneo de puertos y scripts específicos

```bash
nmap -p 80 --script http-title 192.168.1.10
```

Ejecuta el script `http-title` (muestra el título de la web).

### Ejemplos prácticos con interpretación

#### Ejemplo 1 — Escaneo rápido de una máquina local

```bash
nmap 192.168.1.10
```

Salida simplificada:

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
139/tcp closed netbios-ssn
```

Interpretación: SSH y HTTP están escuchando; 139 está cerrado.

#### Ejemplo 2 — Detección de versión y OS

```bash
sudo nmap -sV -O 192.168.1.10
```

Salida (simplificada):

```
22/tcp open  ssh   OpenSSH 7.9p1 Debian
80/tcp open  http  Apache httpd 2.4.38
OS: Linux 4.x
```

Interpretación: servidor Linux con Apache y OpenSSH, versiones identificadas.

#### Ejemplo 3 — Escaneo de subred para hosts vivos y servicios básicos

```bash
sudo nmap -sS -p 22,80,443 -T4 192.168.1.0/24 -oA scan_lan
```

- Resultado: lista de hosts con esos puertos abiertos, guardada en `scan_lan.*`.

### Nmap Scripting Engine (NSE) — uso responsable

- NSE permite automatizar tareas: descubrimiento, brute-force, explotación, vulnerabilidad, etc.
- Ejemplos seguros y útiles:

  - `--script=banner` — obtener banners de servicio.
  - `--script=http-title` — títulos de páginas web.
  - `--script=ssl-cert` — obtener información de certificados SSL.

- Evita ejecutar scripts de la categoría `exploit` en sistemas ajenos. Revisa la documentación de cada script antes de usarla.

### Buenas prácticas y consejos

- Prueba en un laboratorio (p. ej. máquinas virtuales) antes de escanear producción.
- Para inventario regular, programa escaneos controlados (baja intensidad, windows de mantenimiento).
- Combina Nmap con registros, Wazuh/OSSEC o un SIEM para correlacionar detecciones.
- Usa `-oA` para almacenar y comparar resultados históricos.
- Para escaneos lentos y discretos: reduce velocidad con `-T2` o `-T1`, pero **no** incluyas técnicas de evasión (decoys, fragmentación) sin autorización.
- Revisa la diferencia entre `open`, `filtered`, etc., antes de sacar conclusiones.

### Resumen rápido (cheat sheet)

- `nmap <target>` — escaneo por defecto.
- `nmap -p 1-65535 <target>` — todos los puertos.
- `nmap -sS <target>` — SYN scan (privilegios).
- `nmap -sT <target>` — TCP connect (no privilegios).
- `nmap -sU <target>` — UDP scan (privilegios).
- `nmap -sV <target>` — detección de versión.
- `nmap -O <target>` — detección de OS.
- `nmap -A <target>` — agresivo (sV + O + scripts).
- `nmap -sn <net>` — host discovery (ping scan).
- `nmap --script=<name|category>` — ejecutar NSE scripts.
- `nmap -oA salida <target>` — guardar en múltiples formatos.

---

[🔼](#índice)

---

## **415. Tutorial de Nmap para encontrar vulnerabilidades de red**

> ⚠️ **Aviso importante**: únicamente escanea y pruebas sistemas para los que tengas **permiso explícito**. Escanear redes o equipos sin autorización es ilegal. Usa un laboratorio (máquinas virtuales) o entornos de prueba (p. ej. Metasploitable, OWASP Juice Shop) para practicar.

### 1) Flujo general seguro para búsqueda de vulnerabilidades

1. **Planea**: objetivo, alcance (IPs, rangos), ventana de pruebas, autorizaciones firmadas.
2. **Reconocimiento pasivo**: recopilar información sin contactar el objetivo (whois, búsquedas públicas, Shodan).
3. **Descubrimiento**: detectar hosts vivos y puertos abiertos.
4. **Enumeración / fingerprinting**: identificar servicios, versiones, certificados, banners.
5. **Detección inicial de vulnerabilidades**: correlacionar versiones con CVEs y ejecutar scripts NO destructivos.
6. **Validación controlada (solo en laboratorio / con permiso)**: reproducir la vulnerabilidad en entorno controlado. **No** intentes explotar en producción sin autorización.
7. **Reporte**: describir riesgo, impacto, evidencia (salidas), pasos de mitigación.
8. **Remediación y re-test**: parchear/configurar y volver a escanear.

### 2) Configura tu laboratorio (recomendado)

- Usa VMs: VirtualBox / VMware / Vagrant.
- Imágenes para practicar: **Metasploitable**, **OWASP Juice Shop**, **DVWA**, **Vulnerable Web Apps**.
- Red aislada (NAT o host-only) para no impactar la red real.
- Haz snapshots antes de pruebas destructivas.

### 3) Comandos Nmap útiles por etapa (con ejemplos y explicación)

#### A. Descubrimiento de hosts vivos

Detectar hosts activos en una subred:

```bash
nmap -sn 192.168.56.0/24
```

Qué hace: envía sondas tipo ping (-sn) y lista hosts que respondieron. Rápido y no escanea puertos.

Detectar con probes a puertos (útil si ICMP bloqueado):

```bash
nmap -sn -PS22,80,443 192.168.56.0/24
```

`-PS` envía SYN a esos puertos para descubrir hosts.

#### B. Escaneo de puertos (detectar servicios)

Escaneo SYN (rápido, requiere privilegios):

```bash
sudo nmap -sS -p 1-1000 192.168.56.101
```

Explicación: `-sS` SYN scan, `-p` rango de puertos. Muestra puertos abiertos/filtrados.

Escaneo de todos los puertos (más lento):

```bash
sudo nmap -sS -p 1-65535 192.168.56.101 -T4 -oA full_scan
```

`-T4` acelera (útil en LAN), `-oA` guarda en varios formatos.

#### C. Detección de versión y servicios

Para saber qué servicio y versión corre en cada puerto:

```bash
sudo nmap -sS -sV -p 22,80,443 192.168.56.101
```

`-sV` intenta identificar la versión (ej. OpenSSH 7.4, Apache 2.4.29). Es clave para mapear CVEs.

#### D. Detección de sistema operativo (huella)

```bash
sudo nmap -O 192.168.56.101
```

`-O` intenta identificar la familia/versión del SO (basado en respuestas TCP/IP).

#### E. Uso del Nmap Scripting Engine (NSE) — **MUCHA precaución**

NSE permite ejecutar scripts para descubrimiento y chequeos. Úsalos **solo con permiso** y evita scripts de explotación en entornos ajenos.

Ejemplo seguro — scripts por defecto y no destructivos:

```bash
sudo nmap -sS -sV --script=default,safe 192.168.56.101 -oN basic_nse.txt
```

Qué hace: ejecuta scripts catalogados como “default” y “safe” (informativos) junto con escaneo de versión.

Listado de categorías NSE relevantes:

- `discovery` — información y banners.
- `auth` — autenticación relacionada.
- `safe` — scripts no destructivos.
- `vuln` — detección de vulnerabilidades (contiene scripts que pueden ser más intrusivos). **Ejecuta `vuln` solo con autorización explícita.**

Si tienes permiso para ejecutar comprobaciones de vulnerabilidades no destructivas:

```bash
sudo nmap --script=vuln -sV 192.168.56.101
```

**ADVERTENCIA**: `--script=vuln` puede ejecutar pruebas que afectan la estabilidad o desencadenan IDS; no usar en entornos de producción sin autorización. Alternativa: usa `--script=default,safe` y luego valida versiones manualmente.

#### F. Escaneo de SSL/TLS y certificados

Obtener información del certificado TLS:

```bash
nmap --script ssl-cert -p 443 192.168.56.101
```

Esto muestra al menos el emisor, sujeto y fechas (útil para detectar certificados expirados o mal configurados).

#### G. Ejemplo de comando “completo” de reconocimiento (informativo, no explotador)

```bash
sudo nmap -sS -p 1-65535 -sV -O --script=default,safe --reason -T4 -oA report_target 192.168.56.101
```

Explicación: escaneo SYN de todos los puertos, detecta versiones, OS, ejecuta scripts seguros, explica motivos (`--reason`), guarda resultados.

### 4) Interpretación de resultados y priorización

- **Servicios abiertos**: identifica software y versión.
- **TTL y banner**: los banners pueden indicar versiones vulnerables.
- **NSE output**: lee con atención — muchos scripts devuelven “FINGERPRINT” o “Vulnerable: YES/NO”. Verifica fuentes.
- **Prioriza** según: servicio expuesto a Internet, criticidad del servicio (e.g., RDP, SSH, web admin), facilidad de exploitabilidad y CVSS.
- **Falsos positivos**: son comunes. No reportes explotación sin confirmar en entorno controlado.

### 5) Correlacionar versiones con vulnerabilidades (validación no-explotadora)

1. Toma la versión reportada por `-sV` (por ejemplo: `Apache httpd 2.4.29`).
2. Busca la versión en bases fiables: NVD, CVE Details, advisories del proveedor o el `vulners` database.
3. Anota CVE IDs, descripciones, impacto y CVSS.
4. Para validación en laboratorio, reproduce la configuración y prueba solo en tu entorno.

No proporciones exploits activos ni pasos para explotarlos en sistemas que no controlas.

### 6) Herramientas complementarias (para flujo defensivo)

- **OpenVAS / Greenbone**: escaneo de vulnerabilidades más automatizado.
- **Nessus** (comercial): escaneos y reports.
- **Nikto / Burp Suite**: pruebas web en entorno controlado.
- **sslyze / testssl.sh**: análisis profundo de TLS.
- **searchsploit** (local): buscar PoCs en Exploit-DB para reproducir en laboratorio.

### 7) Ejemplo de proceso práctico (resumen con comandos)

Objetivo: reconocer un host y producir hallazgos iniciales sin explotación.

1. Descubrir host:

```bash
nmap -sn 192.168.56.0/24
```

2. Escanear puertos + versiones:

```bash
sudo nmap -sS -sV -p 1-65535 192.168.56.101 -oN step2_ports.txt
```

3. Ejecutar scripts informativos y safe:

```bash
sudo nmap -sS -sV --script=default,safe,auth 192.168.56.101 -oN step3_nse.txt
```

4. Revisar certificado TLS:

```bash
nmap --script ssl-cert -p 443 192.168.56.101 -oN ssl_info.txt
```

5. Correlacionar versiones con CVEs (manual): anota versiones y consulta NVD/NSS/avisos del proveedor.
6. Generar hallazgos: listar servicio, versión, CVE(s) relevantes, riesgo (alto/medio/bajo), evidencia (fragmentos de salida), mitigación recomendada.

### 8) Ejemplo de entrada en un reporte (plantilla corta)

- **Objetivo**: 192.168.56.101
- **Fecha**: 2025-09-15
- **Hallazgo 1**: `OpenSSH 6.6.1` en puerto 22 — **Riesgo: Alto** — CVE-XXXX-YYYY — _Recomendación_: actualizar a OpenSSH ≥ X.Y, deshabilitar root login, usar autenticación por clave.
- **Evidencia**: salida de `nmap -sV` (incluye línea).
- **Estado**: pendiente / mitigado / no aplicable.

### 9) Buenas prácticas éticas y legales

- Autorizar por escrito (scope, IPs, horarios).
- Notificar a operaciones y equipo de seguridad antes de escaneos ruidosos.
- Evitar escaneos durante horarios críticos o ventanas de producción si no está autorizado.
- Mantener registros y outputs (para auditoría).
- En caso de encontrar vulnerabilidades críticas, sigue el proceso de divulgación responsable del cliente/organización.

### 10) Qué **no** harás con Nmap sin permiso

- No ejecutarás scripts de la categoría `exploit` ni intentarás explotar vulnerabilidades en sistemas de terceros.
- No usarás técnicas de evasión para ocultar actividad sin autorización.
- No realizarás brute force en sistemas reales sin permisos explícitos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**         | **Siguiente 3**      |
| ------------------ | ------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./13_1_dig.md) | [⏩](./13_3_ping.md) |
