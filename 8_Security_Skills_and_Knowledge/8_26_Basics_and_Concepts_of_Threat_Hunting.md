| **Inicio**         | **atrás 25**                                   | **Siguiente 27**                                                    |
| ------------------ | ---------------------------------------------- | ------------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_25_Understand_Basics_of_Forensics.md) | [⏩](./8_27_Conceptos_basicos_de_la_gestion_de_vulnerabilidades.md) |

---

## **Índice**

| Temario                                                                                                                                        |
| ---------------------------------------------------------------------------------------------------------------------------------------------- |
| [252. ¿Qué es la caza de amenazas?](#252-qué-es-la-caza-de-amenazas)                                                                           |
| [253. ¿Qué es la caza de amenazas? Tipos y técnicas de caza de amenazas](#253-qué-es-la-caza-de-amenazas-tipos-y-técnicas-de-caza-de-amenazas) |
| [254. Explicación de la búsqueda de amenazas de ciberseguridad](#254-explicación-de-la-búsqueda-de-amenazas-de-ciberseguridad)                 |

# **Conceptos básicos de la caza de amenazas**

## **252. ¿Qué es la caza de amenazas?**

![caza de amenazas](/img/8_Security_Skills_and_Knowledge/caza_de_amenazas.png "caza de amenazas")

### 🔎 ¿Qué es la caza de amenazas?

La **caza de amenazas (Threat Hunting)** es una práctica **proactiva de ciberseguridad** en la que los analistas buscan señales de actividad maliciosa que **no han sido detectadas automáticamente** por las herramientas de seguridad (como antivirus, EDR, IDS/IPS o SIEM).

👉 En lugar de esperar alertas, el cazador de amenazas parte de la premisa:
_"El adversario ya podría estar dentro de la red; vamos a buscarlo antes de que cause más daño."_

**Ejemplo:**

Un equipo de seguridad nota tráfico DNS extraño en horarios no laborales. En lugar de esperar a que el firewall lo bloquee, inician una investigación manual para descubrir si se trata de **exfiltración de datos vía DNS tunneling**.

### ⚙️ Cómo funciona la caza de ciberamenazas

La caza sigue un **ciclo estructurado**:

1. **Hipótesis inicial** → basada en inteligencia de amenazas, TTPs conocidos (MITRE ATT\&CK) o anomalías observadas.

   Ejemplo: “El atacante puede haber usado PowerShell para movimiento lateral.”

2. **Recolección de datos** → logs, eventos, pcaps, telemetría de EDR, SIEM, firewall, Active Directory.

3. **Análisis** → correlación de patrones, ejecución de queries, creación de timelines, búsqueda de IOCs (hashes, dominios, IPs).

4. **Confirmación de hallazgos** → determinar si se trata de una amenaza real o un falso positivo.

5. **Respuesta** → notificar al SOC, contener, erradicar y generar reglas preventivas (detecciones en SIEM, reglas YARA, Sigma, Snort).

### 🧭 Tipos de caza de amenazas

Existen tres enfoques principales:

1. **Caza basada en hipótesis**

   - Se basa en inteligencia de amenazas (CTI) y en modelos como MITRE ATT\&CK.

   - Ejemplo: “APT29 usa WMI para persistencia. Vamos a buscar eventos de WMI sospechosos en el entorno.”

2. **Caza basada en indicadores (IOC hunting)**

   - Se buscan artefactos específicos: hashes, IPs, dominios, cadenas en archivos.

   - Ejemplo: buscar conexiones salientes a un dominio reportado en un feed de amenazas.

3. **Caza basada en anomalías (Behavioral hunting)**

   - Busca comportamientos inusuales que no necesariamente coinciden con un IOC conocido.

   - Ejemplo: un usuario normal inicia sesión en 30 servidores distintos en 5 minutos → posible compromiso de credenciales.

### 🏹 Modelos de caza

Los más utilizados en la industria:

1. **Modelo de Diamante (Diamond Model of Intrusion Analysis)**

   - Analiza cuatro aspectos: adversario, infraestructura, capacidad, víctima.

   - Ejemplo: correlacionar una IP maliciosa (infraestructura) con un malware detectado (capacidad).

2. **Modelo MITRE ATT\&CK**

   - Marco más usado hoy. Basado en Tácticas, Técnicas y Procedimientos (TTPs) de atacantes.

   - Ejemplo: si la táctica es _Exfiltration_, buscar técnicas como _Exfiltration Over Alternative Protocol_.

3. **Modelo de Kill Chain (Lockheed Martin)**

   - Se centra en las fases de un ataque: Reconocimiento → Intrusión → Explotación → C2 → Acciones en el objetivo.

   - Ejemplo: detectar actividad sospechosa en la fase de _Command and Control_ (C2) para cortar el ataque antes de la exfiltración.

### 🛠️ Herramientas de búsqueda de amenazas

Un cazador de amenazas usa múltiples categorías de herramientas:

- **SIEM**: Splunk, ELK (Elastic), IBM QRadar, Azure Sentinel.
- **EDR/XDR**: CrowdStrike Falcon, Microsoft Defender, SentinelOne.
- **Network Forensics**: Zeek (Bro), Suricata, Wireshark, tcpdump.
- **Threat Intelligence**: MISP, OpenCTI, VirusTotal, AlienVault OTX.
- **Análisis forense**: Volatility (memoria), Autopsy/Sleuth Kit (discos).
- **Frameworks de automatización**: Sigma (detecciones genéricas), YARA (patrones de malware).

**Ejemplo real:**

Un analista puede usar **Splunk** para crear una búsqueda que detecte intentos de autenticación RDP fallidos desde direcciones IP externas inusuales.

### ⚔️ Caza de amenazas versus inteligencia de amenazas

- **Inteligencia de amenazas (Threat Intelligence - TI):**

  - Se centra en **recolectar, analizar y compartir información** sobre amenazas conocidas (IOCs, tácticas de grupos APT, nuevas vulnerabilidades).
  - Ejemplo: un feed que publica dominios maliciosos asociados a un ransomware.

- **Caza de amenazas (Threat Hunting):**

  - Se centra en **buscar amenazas activas dentro del entorno interno**, usando o no información de inteligencia.

  - Ejemplo: un analista busca conexiones a esos dominios en los logs de proxy y firewall.

👉 La TI alimenta la caza de amenazas; la caza de amenazas valida y enriquece la TI.

✅ **En resumen:**

La **caza de amenazas** es un enfoque proactivo para descubrir intrusiones que han evadido las defensas automáticas. Se apoya en hipótesis, indicadores y anomalías; se basa en modelos como MITRE ATT\&CK; y se implementa con herramientas de SIEM, EDR, forense y threat intel.

---

[🔼](#índice)

---

## **253. ¿Qué es la caza de amenazas? Tipos y técnicas de caza de amenazas**

### 🔎 Definición de caza de amenazas

La **caza de amenazas** es una práctica **proactiva de ciberseguridad** donde analistas buscan **indicios de actividad maliciosa oculta** dentro de una red o sistema que **no ha sido detectada automáticamente** por las defensas tradicionales (firewall, antivirus, IDS/IPS).

👉 Idea clave: se parte de la hipótesis de que _el atacante ya podría estar dentro_ y se trabaja para descubrirlo antes de que cause daño.

**Ejemplo:**

Un analista nota que un usuario inicia sesión desde un país inusual. Aunque el SIEM no genera alerta, comienza una caza para investigar si la cuenta fue comprometida.

### ⚔️ Caza de amenazas vs. inteligencia de amenazas

- **Inteligencia de amenazas (Threat Intelligence, TI):**

  - Se centra en recolectar y analizar información externa sobre amenazas conocidas (grupos APT, IOCs, malware, exploits).

  - Ejemplo: un feed de TI informa que el dominio `malicious-c2.com` está asociado a ransomware.

- **Caza de amenazas (Threat Hunting):**

  - Usa esa información y otras hipótesis para **buscar actividad maliciosa dentro de la red interna**.

  - Ejemplo: revisar si algún host en la empresa se conectó a `malicious-c2.com`.

👉 La TI alimenta a la caza de amenazas, y la caza de amenazas a su vez genera nueva inteligencia (hallazgos internos).

### 🏢 ¿Cuál es el papel de la caza de amenazas en la seguridad empresarial?

- **Detectar ataques avanzados** que evaden las defensas automáticas.
- **Reducir el tiempo de permanencia del atacante** (_dwell time_).
- **Fortalecer la defensa en profundidad** al descubrir brechas que los sistemas automáticos no detectan.
- **Mejorar la preparación ante incidentes** al generar IOCs internos y playbooks para respuesta.

**Ejemplo:** una empresa descubre, mediante hunting, que un atacante llevaba semanas extrayendo datos usando un canal cifrado de HTTPS disfrazado como tráfico legítimo.

### 🌐 ¿Dónde encaja la caza de amenazas en la ciberseguridad?

Encaja en el **SOC (Centro de Operaciones de Seguridad)** como complemento a:

- **SIEM** (detección automatizada de anomalías).
- **EDR/XDR** (monitoreo de endpoints).
- **Respuesta a incidentes (IR)**.

La caza actúa en la capa proactiva:
🔹 Prevención → Detección → **Caza de amenazas** → Respuesta → Recuperación.

### 🚨 ¿Cómo la caza de amenazas mejora la respuesta a incidentes?

- Proporciona **detecciones más rápidas** y personalizadas (basadas en el entorno real de la empresa).
- Genera **playbooks y runbooks** que facilitan la respuesta futura.
- Ayuda a **contener ataques** antes de que se conviertan en una brecha mayor.
- Enriquece al SOC con nuevos indicadores y reglas de correlación.

### 🕵️ Cómo detectar ataques avanzados con la búsqueda de ciberamenazas

Los atacantes avanzados (APT, ransomware, insiders) suelen evadir antivirus y firmas. La caza detecta **comportamientos anómalos**:

- Inicios de sesión en horarios o lugares inusuales.
- Movimiento lateral con herramientas nativas (ej. PowerShell, WMI).
- Tráfico cifrado hacia dominios poco frecuentes.
- Creación de cuentas de administrador ocultas.

**Ejemplo:**

Un hunting identifica conexiones RDP internas entre servidores que normalmente no deberían comunicarse → indicio de movimiento lateral.

### 🧭 Métodos y técnicas clave de búsqueda de amenazas

#### 1. Caza basada en hipótesis

- Usa modelos como MITRE ATT\&CK para formular hipótesis.

- Ejemplo: “El atacante podría usar PowerShell para ejecutar scripts maliciosos.”

#### 2. Caza basada en indicadores (IOC hunting)

- Se buscan hashes, IPs, dominios, firmas reportadas por feeds de TI.

- Ejemplo: buscar si un hash de malware aparece en endpoints internos.

#### 3. Caza basada en anomalías (Behavioral hunting)

- Busca comportamientos que se desvían de lo normal.

- Ejemplo: detectar un usuario que descarga 10 GB de datos de SharePoint a medianoche.

### 🛠️ Técnicas de búsqueda de amenazas

- **Análisis de logs** (SIEM, Windows Event Logs, syslog).
- **Correlación de eventos** (SIEM + threat intel).
- **Análisis de comportamiento** con UEBA.
- **Consultas ad-hoc** en Splunk/ELK para buscar patrones sospechosos.
- **Memory forensics** con Volatility para detectar malware en RAM.
- **Network hunting** con Zeek o Suricata (análisis de protocolos inusuales).

### 🖥️ Las 5 mejores herramientas eficaces para la búsqueda de amenazas

1. **Splunk** → SIEM con gran capacidad de búsqueda y correlación.
2. **ELK Stack (Elasticsearch, Logstash, Kibana)** → plataforma open-source para análisis de logs.
3. **CrowdStrike Falcon / SentinelOne** → EDR/XDR con hunting avanzado.
4. **Zeek (Bro)** → análisis de tráfico de red en profundidad.
5. **MISP (Malware Information Sharing Platform)** → gestión de IOCs para enriquecer hunts.

### 👤 Análisis del comportamiento de usuarios y entidades (UEBA)

UEBA = **User and Entity Behavior Analytics**.

- Usa machine learning y análisis estadístico para **detectar comportamientos anómalos**.

- Ejemplo:

  - Usuario normal trabaja 9–6.
  - UEBA detecta descargas de bases de datos completas a las 3 AM.
  - Genera una señal para que el equipo de hunting investigue posible **insider threat**.

### ✅ Ocho consejos para una búsqueda eficaz de amenazas

1. **Tener hipótesis claras** (basadas en MITRE ATT\&CK o TI).
2. **Recolectar datos completos** (logs, EDR, tráfico, memoria).
3. **Usar múltiples fuentes de inteligencia**.
4. **Automatizar consultas y scripts** para hunts recurrentes.
5. **Documentar hallazgos** y mantener un historial de hunts.
6. **Colaborar entre equipos** (SOC, IR, Threat Intel).
7. **Retroalimentar la defensa** con nuevas detecciones.
8. **Practicar regularmente** (simulaciones tipo Red Team/Purple Team).

✅ **En conclusión**:

La **caza de amenazas** es clave en la defensa moderna: va más allá de la detección pasiva, identifica ataques avanzados, refuerza la respuesta a incidentes y se apoya en herramientas como SIEM, EDR, UEBA y threat intel. Es un ciclo continuo que transforma un SOC reactivo en uno **proactivo**.

---

[🔼](#índice)

---

## **254. Explicación de la búsqueda de amenazas de ciberseguridad**

La **búsqueda de amenazas** es la práctica proactiva de buscar actividad maliciosa _que no ha sido detectada_ automáticamente por las defensas. Mientras que las herramientas (EDR, SIEM, IPS) generan alertas, el cazador de amenazas formula hipótesis, explora telemetría rica y descubre adversarios que han “pasado por debajo del radar”.

A continuación tienes una guía práctica: conceptos, ciclo, métodos, fuentes de datos, ejemplos (queries), reglas de detección, métricas y recomendaciones operativas.

### 1 — ¿Por qué hacer threat hunting?

- **Reducir el dwell time** (tiempo que un atacante permanece sin ser detectado).
- **Descubrir técnicas evasivas** que firmas/AV no detectan.
- **Generar detecciones y reglas** para que las defensas automáticas detecten lo mismo en el futuro.
- **Mejorar visibilidad** y madurez del SOC.

### 2 — Objetivos típicos de una caza

- Encontrar compromisos no detectados.
- Validar hipótesis (ej. “APT X usa WMI para movimiento lateral”).
- Buscar exfiltración (DNS, HTTPS, cloud storage).
- Identificar persistencia (servicios, Run keys, cronjobs).
- Mapear alcance y TTPs del atacante.

### 3 — Datos / telemetría que necesitas (fuentes clave)

- **Endpoint**: procesos, creación de procesos, imágenes ejecutables, conexiones de red, registros de PowerShell/CommandLine (Sysmon).
- **Red**: pcaps, netflow, logs de proxy, DNS logs, TLS metadata (JA3).
- **Logs de infraestructura**: autenticaciones (Active Directory), VPN, RDP, sudo/auditd, cloud (CloudTrail, S3 access).
- **EDR/XDR**: telemetría de procesos, hashes, comportamiento.
- **SIEM**: agregación y correlación.
- **Threat Intel**: feeds IOCs (MISP, VirusTotal).

> **Consejo:** sin telemetría de **creación de procesos** y **DNS** tu hunting está muy limitado. Habilita Sysmon/OSQuery/EDR y DNS logging.

### 4 — Modelos y marcos para caza

- **MITRE ATT\&CK** — tácticas/técnicas para formular hipótesis (ej. T1059 PowerShell).
- **Diamond Model** — vincula atacante, infraestructura, víctima y capacidades.
- **Kill Chain** — fases (reconocimiento → ejecución → exfiltración) para priorizar búsquedas.

### 5 — Ciclo / metodología de hunting (práctico)

1. **Hipótesis** (basada en TI, IOC, anomalía o inteligencia interna).
2. **Colección** (reunir logs/pcaps/EDR/AD/cloud).
3. **Búsqueda / análisis** (queries, correlación, timeline).
4. **Enriquecimiento** (WHOIS, passive DNS, VirusTotal, MISP).
5. **Validación** (confirmar con EDR, memory forensics, snapshots).
6. **Remediación y detección** (aislar host, rotar credenciales, reglas SIEM/Sigma).
7. **Documentar y cerrar el ciclo** (crear detección automatizada / playbook).

### 6 — Tipos de caza (resumen)

- **IOC hunting** — buscar hashes / IPs / dominios conocidos.
- **Hypothesis-driven hunting** — p. ej. “busquemos abuso de PowerShell en servidores críticos”.
- **Anomaly / behavioral hunting** — encontrar desviaciones en patrones normales con UEBA/ML.
- **Threat-centric hunting** — investigar técnicas asociadas a un actor (APT).

### 7 — Técnicas y tácticas concretas (qué buscar)

- Creaciones de procesos inusuales (PowerShell con `-EncodedCommand`).
- Uso de herramientas administrativas nativas (PsExec, WMI, RDP) en horas raras.
- DNS con cadenas largas o alto volumen (posible DNS tunneling).
- Conexiones TLS a dominios nuevos / SNI extraño / JA3 mismatch.
- Transferencias grandes a cloud storage (S3/GDrive/OneDrive) fuera de patrón.
- Hashes de ejecutables desconocidos en hosts críticos.
- Modificaciones de Run keys / servicios que no coinciden con inventario.

### 8 — Herramientas efectivas (top 5 prácticas)

1. **Splunk** — búsquedas ad-hoc y correlación (muy usado para hunting).
2. **Elastic Stack (Elasticsearch + Kibana)** — búsquedas y detecciones con reglas.
3. **CrowdStrike / SentinelOne (EDR/XDR)** — telemetría de procesos y respuesta.
4. **Zeek (Bro)** — análisis rico de tráfico de red y scripts para detectar TTPs.
5. **osquery / sysmon** — telemetría de endpoints y queries SQL para procesos / persistence.

También útiles: MISP (TI), Sigma (reglas), YARA (binaries), Volatility (memoria).

### 9 — Ejemplos prácticos (hipótesis → queries → análisis)

> **Nota:** las queries son orientativas — adáptalas a tus índices/fields.

#### Ejemplo A — **Hipótesis:** un atacante usa PowerShell para persistencia

**Datos:** Sysmon (EventID 1/4688), EDR, proceses commandline.

**Splunk (SPL)**:

```spl
index=wineventlog OR index=sysmon EventCode=1 OR EventCode=4688
(CommandLine="*powershell* -EncodedCommand*" OR CommandLine="*powershell* -nop -w hidden*")
| stats count BY host, user, CommandLine, _time
| sort -count
```

**osquery (SQL)**:

```sql
SELECT pid, name, cmdline, uid, start_time
FROM processes
WHERE name = 'powershell.exe' AND cmdline LIKE '%EncodedCommand%';
```

**Acciones de hunting / validación:**

- Verificar parent process (¿explorer.exe legítimo o mshta/office?).
- Revisar conexiones de red iniciadas por ese PID (netconns).
- Dump de memoria del proceso (si EDR lo permite) y análisis con YARA/Volatility.
- Si es malicioso: aislar host, capturar evidencia, matar proceso, investigar lateral movement.

**Regla Sigma** (conceptual):

```yaml
title: Suspicious PowerShell EncodedCommand Execution
detection:
  selection:
    EventID: 4688
    CommandLine|contains: "-EncodedCommand"
  condition: selection
level: high
```

#### Ejemplo B — **Hipótesis:** exfiltración por DNS (DNS tunneling)

**Datos:** DNS logs (resolver/proxy), Zeek DNS log, pcap.

**Zeek / tshark quick check** (pcap):

```bash
# buscar respuestas DNS con payloads largos (posible tunneling)
tshark -r capture.pcap -Y 'dns.count.answers > 1' -T fields -e dns.qry.name | sort | uniq -c | sort -nr | head
```

**Splunk (SPL)**:

```spl
index=dns sourcetype=dns
| eval qlen=len(query)
| stats avg(qlen) as avglen max(qlen) as maxlen by src_ip
| where maxlen > 100
```

**Acciones:**

- Extraer queries sospechosas y consultar Passive DNS / WHOIS.
- Reproducir en sandbox decodificando la carga útil (base64/hex).
- Añadir reglas en DNS firewall / bloquear dominio y crear detección.

#### Ejemplo C — **Hipótesis:** exfiltración a cloud storage (OneDrive/GDrive)

**Datos:** CloudTrail / Cloud provider logs, proxy logs, S3 access logs.

**Elastic/KQL (conceptual)**:

```kql
event.dataset:("aws.cloudtrail") AND event.action:("PutObject" OR "Upload")
AND user.identity.principalType:("IAMUser" OR "AssumedRole")
| stats count() by user.name, event.time, requestParameters.bucketName
| where count > 100
```

**Acciones:**

- Revisar listas de objetos recientes, revisar origen de la API call (IP, user-agent).
- Verificar si la cuenta está con MFA y si la API key fue usada fuera de horario.
- Revocar credenciales si mal uso confirmado y restaurar artefactos de backup.

### 10 — De la caza a la detección automática (operacionalizar)

- **Cerrar el ciclo**: transformar queries exitosas en reglas Sigma / detecciones SIEM.
- **Inyectar IOCs** en herramientas (EDR, proxy) y en MISP.
- **Automatizar respuestas** con SOAR: cuando la regla dispara, ejecutar playbook que recoja evidencia y aísle el host.
- **Testeo**: realizar purple team para verificar que la detección funciona frente a técnicas reales.

### 11 — Métricas y KPIs para medir hunting

- **Mean Time to Detect (MTTD)** / **Mean Time to Respond (MTTR)**.
- **Dwell time** (antes/después de hunts).
- **Número de hunts / mes** y cobertura (qué % de activos fueron inspeccionados).
- **Tasa de reglas derivadas** (número de reglas sigma creadas / hunts realizadas).
- **Tasa de falsos positivos** de las reglas nuevas.

### 12 — Retos comunes y cómo mitigarlos

- **Falta de datos / telemetría** → prioriza instrumentar endpoints y DNS.
- **Retención corta de logs** → aumenta RPO y considera cold storage para investigación.
- **Escasez de skills** → formación, playbooks y plantillas; combinar hunting con outsourcing puntual.
- **Saturación de información** → automatiza enrichment y prioriza hipótesis con mayor impacto.

### 13 — Ocho consejos prácticos para una caza eficaz

1. **Empieza con una hipótesis clara** (qué técnica/actor buscas y por qué).
2. **Asegura telemetría mínima**: process creation, DNS, network flows, auth logs.
3. **Usa MITRE ATT\&CK** para mapear técnicas y priorizar.
4. **Automatiza enriquecimiento** (WHOIS, VT, Passive DNS) para acelerar triage.
5. **Documenta todo**: queries, hallazgos, evidencia y pasos siguientes.
6. **Crea detecciones reutilizables** (Sigma rules) y pruébalas en staging.
7. **Integra con SOAR** para acciones repetibles (aislar, capturar pcap, rotar credenciales).
8. **Practica regularmente** con ejercicios Purple Team / red team para validar efectividad.

### 14 — Ejemplo de playbook corto (acción tras hallazgo)

1. **Trigger:** regla Sigma detecta `PowerShell -EncodedCommand`.
2. **SOAR:**

   - Enriquecer con host info (AD, EDR, last seen).
   - Ejecutar query para procesos relacionados.
   - Aislar host si existen conexiones C2 activas.
   - Dump de memoria y recolección de logs.
   - Crear ticket y notificar al IR lead.

3. **Post-action:** agregar IOC, crear alerta permanente y plan de mitigación.

### 15 — Recursos y próximos pasos (qué puedes hacer ahora)

- Habilita y centraliza **Sysmon**, **EDR** y **DNS logging**.
- Empieza con hunts sencillas (PowerShell, RDP brute force, DNS anomalies).
- Versiona tus queries y reglas (repo Git).
- Convierte hunts que tuvieron éxito en detecciones automáticas (Sigma → SIEM).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 25**                                   | **Siguiente 27**                                                    |
| ------------------ | ---------------------------------------------- | ------------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_25_Understand_Basics_of_Forensics.md) | [⏩](./8_27_Conceptos_basicos_de_la_gestion_de_vulnerabilidades.md) |
