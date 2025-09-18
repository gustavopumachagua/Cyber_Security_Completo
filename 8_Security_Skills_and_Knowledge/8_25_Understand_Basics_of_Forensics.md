| **Inicio**         | **atrás 24**                                   | **Siguiente 26**                                      |
| ------------------ | ---------------------------------------------- | ----------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_24_Understand_Concept_of_Runbooks.md) | [⏩](./8_26_Basics_and_Concepts_of_Threat_Hunting.md) |

---

## **Índice**

| Temario                                                                                                     |
| ----------------------------------------------------------------------------------------------------------- |
| [250. Introducción a la investigación forense digital](#250-introducción-a-la-investigación-forense-digita) |
| [251. Análisis forense digital](#251-análisis-forense-digital)                                              |

# **Comprender los conceptos básicos de la ciencia forense**

## **250. Introducción a la investigación forense digita**

![ciencia forense](/img/8_Security_Skills_and_Knowledge/ciencia_forense_ciberseguridad.jpg "ciencia forense")

La **investigación forense digital** (digital forensics) es la disciplina que aplica métodos científicos y procedimientos jurídicos para **identificar, preservar, analizar y presentar evidencias digitales** derivadas de incidentes (intrusiones, fraude, fuga de datos, abuso interno, etc.). Aquí tienes una guía completa —conceptos, fases, herramientas, ejemplos técnicos y buenas prácticas— pensada para quien se inicia o quiere estructurar investigaciones reales de forma profesional y defensiva.

> Aviso importante: todo lo que sigue está orientado a uso **legal y defensivo** (auditorías internas, respuesta a incidentes con autorización, pruebas en laboratorios). Antes de actuar sobre sistemas que no administras, consigue **autorización escrita** y consulta la legislación local.

### 1. ¿Cuál es el objetivo de la forense digital?

- **Preservar evidencia** con la máxima integridad (evitar alteraciones).
- **Determinar qué pasó, cuándo, cómo y quién** (causa raíz y alcance).
- **Proporcionar evidencia admisible** en procesos legales o disciplinarios.
- **Apoyar la respuesta y mitigación** (remediación y mejoras de seguridad).

### 2. Principios básicos y requisitos

- **Integridad**: usar hashing (MD5/SHA256) para demostrar que la evidencia no se modificó.
- **Cadena de custodia (chain of custody)**: registrar quién tocó qué, cuándo y por qué.
- **Documentación exhaustiva**: pasos, timestamps, herramientas/versión, resultados.
- **Repetibilidad y reproducibilidad**: procedimientos que otros pueden verificar.
- **Separación entre análisis y operación**: no usar la evidencia original como entorno de investigación (trabajar sobre copias/imágenes).

### 3. Tipos de forense digital

- **Forense de discos/hosts (computer forensics)**: análisis de discos, ficheros, metadatos.
- **Forense de memoria (memory forensics / RAM)**: análisis de procesos en memoria, credenciales en RAM, malware residente.
- **Forense de red (network forensics)**: análisis de capturas de tráfico (pcap), detección de exfiltración.
- **Forense móvil**: artefactos en Android/iOS (mensajes, app data, IMEI).
- **Forense en la nube**: logs, snapshots, metadatos y artefactos en servicios cloud.
- **Forense IoT/embebidos**: dispositivos con firmware, logs locales.

### 4. Fases de una investigación forense (modelo estándar)

1. **Preparación**

   - Políticas, herramientas probadas, playbooks, legal hold y formación.

2. **Identificación / Detección**

   - ¿Qué sistemas están potencialmente afectados? generar listados iniciales.

3. **Preservación**

   - Aislar sistemas (si procede), preservar volúmenes y memoria; crear imágenes forenses.

4. **Colección**

   - Recolectar imágenes de disco, dumps de memoria, logs, captures de red, cuentas, snapshots cloud.

5. **Análisis**

   - Examinar imágenes (timeline, carving, análisis de memoria, artefactos de usuarios, red).

6. **Documentación y reporte**

   - Informe técnico + resumen ejecutivo; anexos con evidencia (hashes, pcaps).

7. **Acción / Presentación**

   - Soporte a proceso legal, acciones disciplinarias, y remediación.

8. **Lecciones aprendidas**

   - Actualizar controles, runbooks, detección y formación.

### 5. Herramientas comunes (breve descripción)

- **Autopsy / The Sleuth Kit (TSK)** — análisis de imágenes, carving, timelines.
- **Volatility / Rekall** — análisis de memoria RAM.
- **Wireshark / tshark / tcpdump** — captura e inspección de tráfico.
- **FTK / EnCase** — suites comerciales para adquisición y análisis forense.
- **Plaso / log2timeline** — generación de timelines forenses extensos.
- **Hashing tools**: `sha256sum`, `md5sum`.
- **libewf / ewfacquire** — trabajar con imágenes E01 (EnCase format).
- **LiME / winpmem / AVML** — acquiring memory images (Linux/Windows).
- **YARA** — búsqueda de patrones/malware en archivos y memoria.
- **Photorec / scalpel** — file carving (recuperar ficheros borrados).
- **Cellebrite, Magnet, UFED** — herramientas comerciales para móvil.
- **Elastic Stack / SIEM** — correlación y timeline extendido.

### 6. Ejemplos técnicos prácticos y comandos (laboratorio / autorizado)

#### 6.1. **Adquirir imagen de disco (bit-for-bit)** — ejemplo con `dd` (Linux)

> Haz esto **solo** en equipo bajo tu control o con permiso; en caso de discos con errores usa `conv=noerror,sync`.

```bash
# crear imagen raw de /dev/sdb
sudo dd if=/dev/sdb of=/srv/forensics/sdb.raw bs=4M conv=sync,noerror status=progress

# calcular hashes para integridad
sha256sum /srv/forensics/sdb.raw > /srv/forensics/sdb.raw.sha256
md5sum /srv/forensics/sdb.raw > /srv/forensics/sdb.raw.md5
```

**Alternativa E01 (libewf/ewfacquire)**:

```bash
# crear E01 (preserva metadata enriquecida)
sudo ewfacquire /dev/sdb /srv/forensics/sdb.E01
```

#### 6.2. **Obtener un dump de memoria** (Windows) — ejemplo conceptual

- Herramientas: **WinPmem**, **DumpIt**, **AVML** (Microsoft).
- Comando (si tienes WinPmem.exe y permisos):

```powershell
# en Windows (ejemplo conceptual)
winpmem.exe -o C:\forensics\memory.raw
```

Luego analizar con Volatility:

```bash
volatility -f memory.raw --profile=Win7SP1x64 pslist
volatility -f memory.raw --profile=Win7SP1x64 netscan
volatility -f memory.raw --profile=Win7SP1x64 filescan
```

_(nota: el `--profile` depende de OS; Volatility 3 maneja perfiles de forma distinta)._

#### 6.3. **Captura de tráfico de red**

```bash
# tcpdump: captura todo el tráfico de eth0
sudo tcpdump -i eth0 -s 0 -w /srv/forensics/capture.pcap

# filtrar HTTP con tshark (ej. extraer URIs)
tshark -r capture.pcap -Y "http.request" -T fields -e http.request.method -e http.request.full_uri
```

Abre `capture.pcap` con **Wireshark** para análisis interactivo.

#### 6.4. **Montar imagen en modo sólo lectura** (para examinar archivos)

1. Obtener offsets de particiones:

```bash
mmls /srv/forensics/sdb.raw
```

2. Calcular offset y montar:

```bash
# ejemplo: si la partición empieza en sector 2048
offset=$((2048 * 512))
sudo mount -o loop,ro,offset=$offset /srv/forensics/sdb.raw /mnt/forensics
```

#### 6.5. **Timeline con Plaso / log2timeline**

```bash
# crear plaso storage
log2timeline.py /tmp/timeline.plaso /srv/forensics/sdb.raw

# exportar timeline en CSV
psteal.py /tmp/timeline.plaso --output_format=csv > /tmp/timeline.csv
# (en plaso moderno se usa psort)
psort.py -o L2tcsv -w timeline.csv /tmp/timeline.plaso
```

#### 6.6. **Carving (recuperar ficheros borrados)** — Photorec

- `photorec` puede extraer ficheros por firma desde una imagen. Es excelente para recuperar jpeg, docx, pdf borrados.

### 7. Análisis de memoria: ¿por qué es crítico?

- Credenciales en claro, procesos activos, sockets de red, DLL inyectadas y cargas dinámicas solo están en **RAM**.
- Muchas técnicas de evasión no dejan trazas en disco pero sí en memoria (shellcode, hooks).
- Tools típicas: **Volatility**, **Rekall**. Ejemplos de artefactos a buscar: procesos/injectados, conexiones TCP, credenciales de navegador, DLL sospechosas.

### 8. Forense de red: qué buscar

- Conexiones salientes inusuales (IPs de C2).
- Transferencias de archivos (HTTP POST, DNS tunneling).
- Atípicos patrones de escaneo o exfiltración (sustained transfers fuera de horario).
- Identificación por JA3 (fingerprint TLS) o por Hash de User-Agent.

### 9. Forense móvil y cloud — consideraciones especiales

- **Móvil**: dos tipos de adquisición — _lógica_ (datos accesibles a través del OS / ADB) y _física_ (imagen bit-for-bit). Herramientas comerciales (Cellebrite, Magnet) facilitan adquisición y interpretación; en OSS puedes usar `adb` para Android (con permiso).
- **Cloud**: no hay “disco físico” que tocar; hay que preservar: snapshots de volúmenes, CloudTrail logs, VPC Flow Logs, metadata de instancias, registros de IAM, objetos S3 y sus versiones. Solicitar conservación (legal hold) y usar APIs para extraer artefactos.
- **Importante**: los procesos legales y acuerdos de proveedor (CSP) dictan cómo y cuánto puedes recopilar.

### 10. Cadena de custodia (Chain of Custody)

Siempre registra en cada recolección:

- **Quién** recogió la evidencia.
- **Fecha y hora** exactas (timezone).
- **Qué** se recogió (device, serial, imagen filename).
- **Cómo** se recogió (herramienta, comando, flags).
- **Dónde** se almacena y controles de acceso.
- **Hash** (MD5/SHA256) y firmas.
- **Transferencias** (si otro analista tomó la evidencia).

**Ejemplo breve de registro**:

```
Evidencia: sdb.raw
Recolección: dd if=/dev/sdb of=/srv/forensics/sdb.raw bs=4M conv=sync,noerror
Hash SHA256: a3b2f...
Recolector: Ana Pérez (analista)
Fecha/Hora: 2025-09-08T14:30:00Z
Almacenamiento: /forensics/locked/box1 (acceso: forensics-team)
Observaciones: disco con sectores dañados; se usó conv=noerror
```

### 11. Anti-forensics (técnicas de los atacantes) y cómo detectarlas

- **Timestomping**: cambiar timestamps de archivos. Detectable con inconsistencias entre MFT, USN, logs y eventlogs.
- **Borrado seguro / shred**: complica carving; recuperar artefactos de volcado de memoria o backups.
- **Cifrado completo**: sin claves o TPM, recuperar evidencia se complica; buscar metadatos o claves en memoria (embebidas por malware).
- **Logs tampering**: los atacantes limpian logs; busca replicados en SIEM o fuentes externas (firewall, proxy).

La detección suele venir de **correlación de múltiples fuentes** (endpoints, red, logs cloud).

### 12. Ejemplo de flujo de investigación (caso: posible malware en servidor de base de datos)

1. **Detección:** alerta EDR muestra comportamiento inusual (creación de proceso no firmado).
2. **Triage:** identificar si hay riesgo activo; aislar host de red (modo de mitigación).
3. **Preservación y colección:**

   - Captura memoria con `winpmem` o `AVML` (Windows) o `LiME` (Linux).
   - Crear imagen forense del disco (E01 o raw) con `ewfacquire` o `dd`.
   - Recolectar logs (syslog, application logs), registros del EDR y del hypervisor.
   - Realizar `tcpdump`/pcap si posible para capturar tráfico adicional.

4. **Análisis:**

   - Analiza memoria con Volatility (`pslist`, `netscan`, `malfind`).
   - Extrae binarios sospechosos y calcula hashes; escanéalos con YARA y motores de malware.
   - Timeline: usa Plaso para ver actividad cronológica previa a la alerta.
   - Revisa cuentas/escalados en logs (SSH, su/sudo logs).

5. **Reporte:** redacta informe técnico y ejecutivo con evidencia (hashes, pcap, capturas), riesgo, impacto y recomendaciones.
6. **Acción:** limpieza/restore desde backup, rotación de credenciales, parcheo, mejorar detección y prevención.
7. **Post-mortem:** actualizar runbooks y playbooks.

### 13. Presentación y admisibilidad en tribunal

- El informe debe ser claro, preciso, reproducible y explicar métodos.
- Conserva metadatos, firmas y logs de cadena de custodia.
- Prepárate para preguntas del tipo: **“cómo se capturó la evidencia”, “qué herramientas/versiones se usaron”** y para demostrar que no hubo alteración.
- En la práctica, se busca que el análisis sea **objeto científico**: métodos estándar, documentación y transparencia.

### 14. Buenas prácticas resumidas / checklist forense

- Tener **playbooks/runbooks** preparados para distintos tipos de incidentes.
- Mantener una **caja de herramientas forense** actualizada (USB Read-Only, software, kits de memoria).
- **Probar** procedimientos regularmente (tabletop + ejercicios).
- **Versionar** scripts y documentar parámetros exactos.
- **Usar roles mínimos** en cuentas que inician adquisiciones (AutomationAssumeRole en cloud).
- **Centralizar logs** (SIEM) para correlación.
- **Formación** continua (memory forensics, timeline analysis, nuevas técnicas de anti-forensics).

### 15. Recursos y certificaciones útiles

- **NIST SP 800-86** (Guide to Integrating Forensic Techniques into Incident Response) — guía técnica de referencia.
- **The Art of Memory Forensics** (book) — referencia clave para análisis RAM.
- Certificaciones: **EnCE** (EnCase Certified Examiner), **GCFA / GCFE** (GIAC), **SANS FOR508/FOR610**; **OSINT** y cursos de SANS.
- Práctica: laboratorios como **SANS NetWars**, CTFs forenses y repositorios de imágenes forenses públicas (para entrenar).

### 16. Conclusión y siguiente paso práctico

La investigación forense digital combina **técnica, metodología y legalidad**. Aprenderla exige practicar sobre imágenes y muestras en laboratorios, dominar herramientas (Volatility, Autopsy, Wireshark) y documentar cada acción.

---

[🔼](#índice)

---

## **251. Análisis forense digital**

### 1. ¿Qué es el análisis forense digital y cuál es su objetivo?

El **análisis forense digital** es la disciplina que aplica métodos científicos para **identificar, preservar, analizar y presentar** evidencias digitales sobre un incidente (intrusión, fraude, fuga de datos, abuso interno).

**Objetivos principales**:

- Determinar _qué pasó, cuándo, cómo y quién_.
- Preservar la integridad de la evidencia (hashes, cadena de custodia).
- Producir un informe técnico y otro ejecutivo que sean reproducibles y, si procede, admisibles en un proceso legal.
- Extraer IOCs (IPs, hashes, dominios, cuentas) y recomendaciones de mitigación.

### 2. Fases del proceso forense (modelo estándar)

1. **Preparación** — herramientas, playbooks, legal hold, entorno controlado.
2. **Identificación** — detección y triage inicial (¿qué sistemas están afectados?).
3. **Preservación** — aislar si es necesario; no modificar evidencia; tomar fotografías/registro.
4. **Adquisición / Colección** — crear imágenes bit-for-bit y dumps de memoria; recolectar logs y pcaps.
5. **Examinación / Análisis** — filesystem, memoria, red, móvil, cloud; generar timelines; buscar IOCs.
6. **Reporte** — resumen ejecutivo, hallazgos técnicos, evidencias con hashes, recomendaciones.
7. **Acción / Presentación** — soporte legal, remediación y lecciones aprendidas.

### 3. Tipos de evidencia y fuentes comunes

- **Disco/almacenamiento**: imágenes (raw, E01), ficheros, particiones.
- **Memoria RAM**: procesos en ejecución, credenciales en memoria, DLL inyectadas.
- **Red**: pcaps, logs de firewall, proxy y DNS, flow logs.
- **Logs del sistema y aplicaciones**: WinEvent, syslog, auditd, web server logs.
- **Artefactos específicos**: MFT, USN, Prefetch, LNK, Recycle Bin, browser history, cookies, cache.
- **Móvil**: DBs de apps, SMS, registros de llamadas, archivos del sistema.
- **Cloud**: CloudTrail, S3 object/version, snapshots EBS, metadatos de instancias.

**Importancia:** siempre recolecta desde múltiples fuentes para corroborar (cross-evidence).

### 4. Adquisición: buenas prácticas y comandos (laboratorio/autorizado)

- **Nunca trabajar sobre el original**: crea copias bit-for-bit.
- **Usar hardware write-blocker** para discos físicos cuando sea posible.
- **Registrar todo**: quién, cuándo, cómo, herramienta/versión, hashes.

#### Ejemplos (Linux / laboratorio autorizado)

```bash
# Crear imagen raw con dd (NO usar en producción sin plan)
sudo dd if=/dev/sdb of=/forensics/sdb.raw bs=4M conv=sync,noerror status=progress

# Calcular hashes
sha256sum /forensics/sdb.raw > /forensics/sdb.raw.sha256
md5sum   /forensics/sdb.raw > /forensics/sdb.raw.md5
```

> Si quieres E01 (formato EnCase) usa `ewfacquire`/`guymager` o FTK Imager.

#### Adquisición de memoria (Windows/Linux)

- Windows: **winpmem**, **AVML**, **DumpIt**.
- Linux: **LiME** (acquire RAM to file).

Ejemplo (conceptual):

```powershell
# WinPmem (ejemplo conceptual)
winpmem64.exe -o C:\forensics\memory.raw
```

### 5. Montar y examinar imágenes (montaje en solo-lectura)

1. Obtener particiones (mmls, fdisk -l).
2. Calcular offset y montar en modo `ro`:

```bash
# ejemplo de cálculo de offset si partición inicia en sector 2048
offset=$((2048 * 512))
sudo mount -o loop,ro,offset=$offset /forensics/sdb.raw /mnt/forensics
```

### 6. Técnicas de análisis principales

#### 6.1. Análisis de sistema de ficheros y artefactos

- **Sleuth Kit / Autopsy**: listar ficheros, recuperar borrados, MFT.
- **Artefactos Windows**: `NTFS MFT`, `$LogFile`, `Registry (HKLM\SYSTEM, SOFTWARE, SAM)`, `Prefetch`, `LNK`, `Recycle Bin`, `Event Logs`.
- **Artefactos Linux/macOS**: `/var/log`, shell history (`.bash_history`), crontabs, `systemd` journal, `/etc/` configurations.

Comandos útiles:

```bash
# SleuthKit examples
mmls /forensics/sdb.raw            # particiones
fls -r -m / /forensics/sdb.raw    # listar ficheros
icat /forensics/sdb.raw <inode> > recovered_file
```

#### 6.2. Timeline forense (time-based correlation)

- Herramientas: **Plaso (log2timeline)** + **psort** para crear timeline normalizado.
- Objetivo: construir cronología de eventos combinando múltiples fuentes (logs, MFT, eventos).

Ejemplo:

```bash
log2timeline.py /tmp/timeline.plaso /forensics/sdb.raw
psort.py -o L2tcsv -w timeline.csv /tmp/timeline.plaso
```

#### 6.3. Memory forensics (crítico)

- **Volatility 3** o **Rekall**: listar procesos, conexiones de red, módulos inyectados, malfind, dumped DLLs.
- Comandos (Volatility 3 conceptual):

```bash
vol -f memory.raw windows.pslist
vol -f memory.raw windows.netscan
vol -f memory.raw windows.malfind
vol -f memory.raw windows.dlllist --pid 1234
```

- **Por qué**: muchos indicadores de compromiso (C2, credenciales, shellcode) aparecen sólo en RAM.

#### 6.4. Network forensics

- **Wireshark / tshark / Zeek**: inspección de pcaps.
- Extraer HTTP requests:

```bash
tshark -r capture.pcap -Y "http.request" -T fields -e http.host -e http.request.uri
```

- **Zeek/IDS** para detección automatizada (DNS tunneling, exfiltration).

#### 6.5. Malware analysis (estático / dinámico)

- **Estático**: `strings`, `rabin2`, `pefile`, YARA.
- **Dinámico**: ejecutar en sandbox (Cuckoo, VMs aisladas) y observar TTPs.
- Registra hashes SHA256 y compara con threat intel (VirusTotal, MISP).

### 7. Herramientas clave (resumen)

- **Autopsy / Sleuth Kit**, **Plaso (log2timeline)**, **Volatility 3**, **Wireshark / tshark**, **tcpdump**, **YARA**, **HashMyFiles / sha256sum**, **FTK / EnCase** (comerciales), **Cellebrite / Magnet** (móvil), **Cuckoo Sandbox** (malware), **Zeek** (network analysis), **MISP** (threat intel sharing).

### 8. Correlación y IOCs

- Extraer IOCs (hashes, IPs, dominios, cuentas) y **correlacionar** con: logs de firewall, proxy, SIEM, threat feeds.
- Crear reglas Sigma para detección en logs; compartir IOCs en MISP.

### 9. Informe forense: estructura recomendada

1. **Resumen ejecutivo** (impacto, estado).
2. **Alcance y limitaciones** (qué se incluyó/excluyó).
3. **Metodología** (herramientas/versión, pasos de adquisición).
4. **Hallazgos técnicos** (timeline, IOCs, evidencias con hashes).
5. **Artefactos importantes** (capturas, archivos, líneas de logs relevantes).
6. **Recomendaciones** (remediación técnica y de procesos).
7. **Anexos**: chain of custody, comandos ejecutados, salida completa, hashes, pcaps.

**Ejemplo de entrada de evidencia (tabla):**

| ID    | Tipo         | Nombre fichero | Hash SHA256 | Fecha recolecta   | Localización    | Observaciones              |
| ----- | ------------ | -------------- | ----------- | ----------------- | --------------- | -------------------------- |
| E-001 | Imagen disco | sdb.raw        | a3b2...     | 2025-09-08T14:30Z | /forensics/box1 | Imagen con dd conv=noerror |

### 10. Cadena de custodia — plantilla mínima

- **Evidencia**: (qué)
- **Recolectada por**: (nombre)
- **Fecha/Hora**: (UTC)
- **Método**: (herramienta/comando)
- **Hash**: (SHA256 / MD5)
- **Almacenamiento**: (ruta física/virtual)
- **Transferencias**: (quién/por qué/cuándo)

Mantén firmas y logs físicos si hay manipulación física.

### 11. Retos y anti-forense (y cómo mitigarlos)

- **Timestomping** (manipular timestamps) → correlación entre múltiples fuentes (MFT, logs, timestamps de red).
- **Wiping / shred** → recuperar desde backups o snapshots; carro de recuperación.
- **Cifrado completo** → buscar claves en memoria o en backups, solicitar custodia legal si es cloud.
- **Logs tampered** → buscar registros en dispositivos externos (firewall, proxy, SIEM).

Consejo: **prioriza la memoria** (volátil) cuando sospechas manipulación activa.

### 12. Caso práctico (hipotético) — paso a paso (alto nivel)

**Escenario:** alerta EDR: proceso no firmado en servidor Windows prod.

1. **Triage**: identificar criticidad, aislar host de la red si riesgo activo.
2. **Preservación**: documentar, tomar fotos (si físico), no reiniciar.
3. **Adquisición**: dump de memoria (winpmem), imagen disco (E01), recolectar logs EDR + syslog + proxy.
4. **Análisis**: `volatility` → `pslist`, `netscan` → identificar proceso y sus sockets → extraer ejecutable desde memoria o disco → YARA / VirusTotal.
5. **Network**: analizar pcaps por conexiones a C2; buscar DNS sospechoso.
6. **Timeline**: plaso/psort con combinaciones de MFT + logs.
7. **Reporte**: pruebas, evidencias, hashes, IOCs, plan de remediación (rebuild recommended if persistence found).
8. **Remediación y seguimiento**: reset creds, rotación claves, reimage host, mejorar reglas IDS, lecciones aprendidas.

### 13. Buenas prácticas resumidas

- Trabaja **siempre sobre copias**; conserva originales.
- **Hash** al crear y verificar; documenta.
- Automatiza adquisición y checklist (runbooks).
- Versiona scripts y usa repositorio privado.
- Prueba procedimientos periódicamente (ejercicios, tabletop).
- Integra análisis forense con SIEM para detección y prevención futuras.
- Mantén formación y actualización en nuevas técnicas de anti-forense.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 24**                                   | **Siguiente 26**                                      |
| ------------------ | ---------------------------------------------- | ----------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_24_Understand_Concept_of_Runbooks.md) | [⏩](./8_26_Basics_and_Concepts_of_Threat_Hunting.md) |
