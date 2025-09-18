| **Inicio**         | **atrás 8**                      | **Siguiente 10**      |
| ------------------ | -------------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./11_8_Cyber_Kill_Chain.md) | [⏩](./11_10_SIEM.md) |

---

## **Índice**

| Temario                                                                           |
| --------------------------------------------------------------------------------- |
| [349. MITRE ATT&CK Framework](#349-mitre-attck-framework)                         |
| [350. Introducción al marco MITRE ATT&CK](#350-introducción-al-marco-mitre-attck) |
|                                                                                   |

# **ATT&CK**

## **349. MITRE ATT&CK Framework**

![ATT&CK](/img/11_Basics_of_Cryptography/ATT&CK.jpg "ATT&CK")

### ¿Qué es MITRE ATT\&CK?

**MITRE ATT\&CK** (Adversarial Tactics, Techniques, and Common Knowledge) es una **base de conocimiento** pública y mantenida que describe las tácticas (objetivos) y técnicas (cómo se alcanzan esos objetivos) que usan los adversarios reales contra sistemas informáticos.
No es solo una lista: es una **herramienta operativa** para diseñar detecciones, evaluar controles, crear playbooks, priorizar defenses y poner en marcha ejercicios Red/Purple/Blue team.

### Estructura fundamental

- **Tácticas** = _el “porqué”_ del comportamiento adversario (objetivo operativo: p. ej. Discovery, Lateral Movement).
- **Técnicas** = _el “cómo”_ (métodos concretos para lograr la táctica: p. ej. PowerShell, Pass the Hash, Exploitation for Client Execution).
- **Subtécnicas** = variantes más específicas de una técnica.
- **Mitigaciones y detecciones sugeridas** para cada técnica.
- **Casos y ejemplos** reales (observados en campañas) que dan contexto.

### Tácticas comunes (visión práctica)

A continuación te doy las tácticas más utilizadas en ATT\&CK con **ejemplos concretos** de técnicas y artefactos para que lo veas realista:

1. **Reconnaissance (Recon)**

   - Qué: recopilación de información sobre personas, infraestructura, software.
   - Técnicas: search open sources, phishing kit research, scanning.
   - Ejemplo: uso de `theHarvester` para obtener correos y subdominios; logs: múltiples consultas DNS hacia subdominios desconocidos.

2. **Resource Development**

   - Qué: crear infraestructura (dominios, cuentas, tooling).
   - Ejemplo: registrar dominio `updates-svc[.]com` y crear buckets en cloud para staging.

3. **Initial Access**

   - Qué: obtener primer foothold.
   - Técnicas: spearphishing attachment (T1566.001), Exploit Public-Facing Application, Valid Accounts.
   - Ejemplo: email con .docm que ejecuta macro → PowerShell descarga payload.

4. **Execution**

   - Qué: ejecutar código en el endpoint.
   - Técnicas: `cmd`, `PowerShell` (T1059.001), `schtasks`, `rundll32`.
   - Ejemplo: `powershell -EncodedCommand ...` — artefactos en Sysmon: EventID 1 (Process Create) con parent `winword.exe`.

5. **Persistence**

   - Qué: mantener acceso tras reinicios.
   - Técnicas: Scheduled Task/Job (T1053), Services (T1543), Registry Run Keys.
   - Ejemplo: creación de tarea `Updater` o clave `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.

6. **Privilege Escalation**

   - Qué: obtener permisos más altos (local admin, SYSTEM).
   - Técnicas: Exploits, Bypass UAC, Token Impersonation (T1134).
   - Ejemplo: uso de exploit para CVE sin parchear o `wmic` para crear servicios.

7. **Defense Evasion**

   - Qué: evitar detección.
   - Técnicas: obfuscation/packing, timestomping, disable security tools.
   - Ejemplo: binario ofuscado, uso de `Base64` y `Invoke-Expression` en PowerShell, ejecución Living-Off-The-Land (LOTL).

8. **Credential Access**

   - Qué: robar credenciales (hashes, plain text).
   - Técnicas: Mimikatz (T1003.001), credential dumping, phishing for creds.
   - Ejemplo: `sekurlsa::logonpasswords` en memoria → búsqueda de `lsass.exe` dumps.

9. **Discovery**

   - Qué: mapear la red local y servicios.
   - Técnicas: Netstat, ARP scanning, Account Discovery (T1087).
   - Ejemplo: `net user /domain` ejecutado por un proceso sospechoso; logs: comandos de red inusuales.

10. **Lateral Movement**

    - Qué: moverse entre hosts (RDP, WMIC, PsExec).
    - Técnicas: Remote Services (T1021), Pass the Hash (T1550.002).
    - Ejemplo: `psexec.exe \\host -u domain\user -p Pass` o uso de WMI remoting.

11. **Collection**

    - Qué: agrupar datos de interés (screenshots, key files).
    - Técnicas: Screen Capture, File and Directory Discovery.
    - Ejemplo: scripts que buscan directorios `\\fileserver\HR` y los copian a carpeta temporal.

12. **Command and Control (C2)**

    - Qué: canal de comunicación con el implant.
    - Técnicas: HTTPS (T1071.001), DNS Tunneling (T1071.004), Web Services.
    - Ejemplo: beacons periódicos cada N segundos a dominio detectado en proxy logs.

13. **Exfiltration**

    - Qué: mover data fuera.
    - Técnicas: Exfil over Web Service (T1041), Exfil via Cloud Storage (T1537).
    - Ejemplo: upload a S3 con credenciales robadas o upload de zip por HTTPS a servidor externo.

14. **Impact**

    - Qué: acciones para interrumpir o dañar (ransomware, destruction).
    - Técnicas: Data Encrypted for Impact (T1486), Service Stop.
    - Ejemplo: procesos que renombraron y cifraron múltiples archivos y crearon nota de rescate.

> Nota: las técnicas tienen códigos Txxxx (por ejemplo T1059 = Command and Scripting Interpreter), y muchas técnicas tienen subtécnicas (por ejemplo T1059.001 = PowerShell). No intento listar todo — MITRE mantiene la matriz completa y actualizada.

### ¿Cómo usar ATT\&CK en la práctica? Casos de uso

1. **Diseño de detecciones/Hunting**

   - Mapea logs y telemetría a técnicas (Sysmon → ProcessCreate → map to T1059, DNS logs → T1071.004).
   - Crea rules/hunts que combinen indicadores: p. ej. `ProcessCreate(CommandLine contains '-EncodedCommand') AND DNS query to new domain` → alta probabilidad de initial access → execution + C2.

2. **Evaluación de controles (gap analysis)**

   - Para cada técnica relevante a tu industria, evalúa si tienes detección, bloqueo y ejercicios para ella. Generas “coverage heatmap”.

3. **Threat Intelligence / Attribution**

   - Relaciona TTPs observados con perfiles de actores (siempre con cautela): si ves Mimikatz + Cobalt Strike + domain patterns podría indicar cierto kit o actor.

4. **Purple/Red Teaming**

   - Construye ejercicios específicos por técnica para probar detecciones. Ej.: ejercitar T1059.001 (PowerShell) con variaciones de obfuscation para probar EDR.

5. **Playbooks y respuesta a incidentes**

   - Para cada técnica que se detecte, enlazas a pasos de respuesta recomendados (por ejemplo, si detectas T1059.001 encender triage: dump memory, isolate host, bloquear egress).

### Ejemplos prácticos y artefactos detectables

Aquí tienes ejemplos concretos con **qué buscar** en tus logs:

- **PowerShell EncodedCommand (T1059.001 / Execution)**

  - Sysmon: EventID 1 ProcessCreate where `CommandLine` contains `-EncodedCommand` or long base64 string.
  - Splunk/KQL: buscar `CommandLine` matching `-EncodedCommand` or `IEX (New-Object Net.WebClient).DownloadString`.

- **Mimikatz (T1003 / Credential Dumping)**

  - EDR: alertas sobre `sekurlsa` patterns, `logonpasswords`, or suspicious `lsass.exe` reading.
  - Windows Security: EventID 4624 (logon) anomalous patterns; process reading memory of `lsass.exe`.

- **Beaconing to C2 (T1071 / C2)**

  - Network: repeated outbound connections from host to same external IP/domain at regular interval; unusual user-agent strings.
  - Netflow/proxy: small packets, regular intervals, DNS queries with increasing entropy.

- **Pass-the-Hash (T1550.002)**

  - Windows Eventing + EDR: authentication using NTLM hashes or lateral authentication without interactive login; suspicious scheduled tasks created remotely.

### Detecciones: ejemplo de regla Sigma (genérica, PowerShell encoded)

```yaml
title: PowerShell Encoded Command Execution
id: 00000000-0000-0000-0000-000000000000
status: experimental
description: Detects PowerShell execution with -EncodedCommand which is commonly used by adversaries.
author: Gustavo
logsource:
  product: windows
  service: sysmon
detection:
  selection:
    EventID: 1
    ProcessName: '*\\powershell.exe'
    CommandLine|contains:
      - "-EncodedCommand"
  condition: selection
falsepositives:
  - Administrative scripts that legitimately use encoded commands (verify context)
level: high
```

(Adáptala a tu esquema: campos de Sysmon/Windows Event IDs pueden variar.)

### Integración con otros marcos: Kill Chain y Diamond

- **Kill Chain:** ATT\&CK es granular y mapea bien a las fases de la Kill Chain. Por ejemplo, Initial Access (Kill Chain) → técnicas ATT\&CK como `spearphishing attachment (T1566.001)`.
- **Diamond Model:** ATT\&CK describe las **capabilities** y TTPs que ligan Adversary↔Capability↔Infrastructure↔Victim — usar ATT\&CK para identificar la _Capacidad_ y las _Tácticas/Técnicas_ observadas que conectan con infraestructura y víctimas.

### Cómo operacionalizar ATT\&CK en un SOC

1. **Inventario de telemetría**: lista qué técnicas puedes cubrir con EDR, DNS, Proxy, Netflow, Sysmon, Auth logs.
2. **Coverage matrix**: crear matriz técnica vs. logsource → gap analysis.
3. **Priorizar**: mapear técnicas con impacto probable (ransomware vs reconnaissance) y activos críticos.
4. **Playbooks**: por técnica/ táctica crear pasos de respuesta y queries/hunts.
5. **Testing**: ejecutar red-team (o Atomic Red Team tests) contra técnicas para validar detections.
6. **Automatización**: cuando una técnica de alta confianza se detecte, disparar SOAR playbook (aislar host, borrar sesiones, recollection).

### Herramientas y recursos útiles

- **MITRE ATT\&CK website** — matriz interactiva y listados de técnicas (mitre.org/attack).
- **Atomic Red Team** — pruebas pequeñas (atomic tests) mapeadas a ATT\&CK para ejecutar técnicas en laboratorio.
- **ATT\&CK Navigator** — herramienta para visualizar matrices y marcar cobertura.
- **Sigma** — formato portable para reglas SIEM.
- **Caldera / Red Canary / MITRE tools** — frameworks para simular y evaluar.

### Buenas prácticas al aplicar ATT\&CK

- **No confundir técnica con actor**: técnicas se repiten entre grupos y herramientas.
- **Agregar contexto y score de confianza**: enriquecer detecciones con CTI y telemetría para reducir falsos positivos.
- **Versiona tu coverage map** y revisa regularmente (ATT\&CK se actualiza).
- **Entrena equipos**: crea labs donde se ejecuten técnicas y se mapeen detecciones (purple teaming).
- **Documenta playbooks mapeados a ATT\&CK IDs** para una respuesta consistente y reproducible.

### Ejemplo práctico: hunting paso a paso

**Objetivo:** cazar ejecuciones PowerShell sospechosas y potencial beaconing.

1. Hunt 1 — Sysmon ProcessCreate: busca `powershell.exe` con `-EncodedCommand` en los últimos 24h.
2. Hunt 2 — DNS join: por hosts identificados en Hunt1, buscar consultas DNS a dominios con baja reputación o recién vistos.
3. Correlación: si host muestra EncodedCommand **y** DNS a dominio sospechoso → escalar y aislar host; recolectar memoria y capturar procesos.
4. Enriquecimiento: lookup domain in passive DNS, WHOIS, VT; lookup hash in VT.
5. Remediate: bloquear dominio/IP y realizar lateral hunt (buscar resoluciones/ conexiones en otros hosts).

### Ejercicios prácticos (para mejorar dominio)

1. **Coverage map**: identifica 20 técnicas ATT\&CK relevantes para tu organización y mapea si tienes detección (EDR, DNS, Proxy).
2. **Atomic Red Team**: ejecuta 5 tests (p. ej. T1059.001 PowerShell, T1003 Mimikatz) en lab y mide si tus detections saltan.
3. **Purple Team**: planifica 1 día de ejercicios donde red-team ejecuta 3 técnicas y blue-team detecta y responde, documentando MTTD/MTTR.
4. **Crear 10 reglas Sigma** para técnicas comunes en tu entorno y validarlas con logs históricos.

### Resumen (lo esencial)

- **MITRE ATT\&CK** es la referencia para describir lo que hacen los adversarios (tácticas y técnicas).
- Úsalo para **diseñar detecciones**, **evaluar cobertura**, **crear playbooks** y **ejercicios**.
- Mapea técnicas a tu telemetría (EDR, DNS, Proxy, Sysmon, logs) y prioriza por activos críticos.
- Practica con Atomic Red Team y purple teaming para validar y mejorar.

---

[🔼](#índice)

---

## **350. Introducción al marco MITRE ATT&CK**

### 1) ¿Qué es MITRE ATT\&CK?

**MITRE ATT\&CK** (Adversarial Tactics, Techniques, and Common Knowledge) es una **enciclopedia pública y estructurada** de comportamientos observados de adversarios reales. Describe **qué hacen** los atacantes (tácticas = objetivos; técnicas = métodos) y ofrece ejemplos, mitigaciones y recomendaciones de detección. Es una herramienta operativa para mejorar defensas, diseñar detecciones y planear pruebas (red/purple teams).

Origen: construido por MITRE con contribuciones públicas; se actualiza periódicamente con nuevas técnicas y referencias a campañas reales.

### 2) ¿Por qué usar ATT\&CK?

- **Estandariza el lenguaje** entre equipos (SOC, IR, TI, Threat Intel, Red Team).
- **Mapea TTPs** (tácticas, técnicas y procedimientos) a telemetría y controles.
- **Permite medir cobertura** de detección (gap analysis).
- **Facilita ejercicios**: pruebas controladas (Atomic Red Team, CALDERA).
- **Conecta inteligencia**: relaciona observaciones con campañas/actores (con cautela).

### 3) Estructura básica del marco

- **Tácticas**: columnas en la matriz; representan _por qué_ un adversario actúa (ej. Initial Access, Execution, Persistence, Lateral Movement, Exfiltration, Impact).
- **Técnicas**: filas; representan _cómo_ se consigue la táctica (ej. PowerShell = T1059.001).
- **Subtécnicas**: variantes más específicas (ej. T1566.001 = spearphishing attachment).
- **Mitigaciones y detecciones**: para cada técnica MITRE sugiere controles y fuentes de telemetría.
- **Procedimientos**: ejemplos concretos — comandos, malware, campañas que utilizaron la técnica.

### 4) Matrices y dominios

ATT\&CK no es una sola matriz: hay varias según el objetivo:

- **Enterprise** (Windows / macOS / Linux / Cloud) — la más usada en SOC.
- **Mobile** — Android/iOS.
- **ICS** — sistemas industriales.
- **Cloud** — técnicas específicas de entornos cloud (algunas integradas en Enterprise cloud).

Elige la matriz relevante según tu entorno.

### 5) Ejemplos concretos (tácticas → técnica → artefactos detectables)

1. **Initial Access** → _Spearphishing Attachment_ (T1566.001)

   - Artefacto: .docm con macro; proxy/mail logs muestran enlace; sandbox report con cadena de PowerShell.
   - Señales a buscar: envío a empleados con roles financieros, vínculo entre mensaje y descarga de payload, sandbox detonation alert.

2. **Execution** → _PowerShell (T1059.001)_

   - Artefacto: `powershell.exe -EncodedCommand <base64>` en Sysmon EventID 1.
   - Regla: detectar `-EncodedCommand`, `IEX (New-Object Net.WebClient).DownloadString(...)`, o longitud de CommandLine sospechosa.

3. **Credential Access** → _Credential Dumping: LSASS Memory (T1003.001)_

   - Artefacto: proceso que lee `lsass.exe` o volcado de memoria de LSASS.
   - Señales: EDR alerta sobre lectura de memoria de `lsass`; procesos que llaman `MiniDumpWriteDump` sobre `lsass.exe`.

4. **Command and Control** → _C2 over HTTPS (T1071.001)_

   - Artefacto: host con conexiones periódicas a dominio externo con patrones beaconing.
   - Señales: patrón regular de conexiones, user-agent inusual, TLS certs extraños.

5. **Impact** → _Data Encrypted for Impact (Ransomware) (T1486)_

   - Artefacto: gran número de archivos modificados/renombrados con nueva extensión; creación de nota de rescate.
   - Señales: EDR alertas de procesos que abren muchos archivos, cambios masivos en filesystem.

### 6) Cómo aplicar ATT\&CK en detección y hunting (pasos prácticos)

1. **Inventario de telemetría**: qué logs tienes (EDR, DNS, proxy, netflow, Sysmon, autenticación).
2. **Mapea telemetría → técnicas**: construye matriz «técnica vs. logsource» (¿puedo detectar T1059 con Sysmon? ¿T1003 con EDR?).
3. **Gap analysis**: marca técnicas sin cobertura → prioriza adquisición/configuración (ej. habilitar Sysmon).
4. **Crea reglas/hunts**: usa ATT\&CK para nombrar y organizar las reglas (por ejemplo: `T1059.001 - PowerShell EncodedCommand detection`).
5. **Valida con tests**: usa _Atomic Red Team_ (pequeños tests mapeados a ATT\&CK) para ejecutar técnicas de forma segura y verificar detecciones.
6. **Itera**: ajustar reglas, medir MTTD/MTTR, actualizar matriz.

### 7) Herramientas útiles y ecosistema

- **ATT\&CK Navigator** — herramienta web para marcar cobertura, crear capas y colaborar en mapas de detección.
- **Atomic Red Team** — repositorio con tests ejecutables (pequeños) mapeados a ATT\&CK para validar controles.
- **CALDERA** (MITRE) — framework automatizado de adversarial emulation que usa ATT\&CK.
- **Purple Teaming tools** — integran red/blue testing con ATT\&CK.
- **SIEM / SOAR** — integrar reglas mapeadas a ATT\&CK para orquestar respuestas.
- **ATT\&CK for Enterprise/ICS/Mobile** en la web de MITRE — documentación completa por técnica.

### 8) Ejemplo práctico: regla simple y flujo de hunting

**Objetivo**: detectar Execution (PowerShell) + C2 (DNS beaconing).

1. **Hunt 1 — Sysmon**: buscar ProcessCreate (`EventID 1`) donde `ProcessName` contiene `powershell.exe` y `CommandLine` contiene `-EncodedCommand`.
2. **Hunt 2 — DNS**: por cada host detectado en Hunt1, buscar consultas DNS en los últimos 10 minutos a dominios con baja reputación o recién creados.
3. **Correlación**: si host presenta both → escalar, aislar host y recolectar memoria.
4. **Enriquecimiento**: lookup domain (passive DNS, WHOIS), command hash in VT.
5. **Remediate**: bloquear dominio, rotar credenciales si se sospecha exfil, investigación forense.

(Esto cubre técnicas T1059.001 + T1071.004/T1071.001 según evidencias).

### 9) Mapeo con otros marcos — cómo encaja

- **Kill Chain**: ATT\&CK ofrece técnicas que se enlazan a las fases de la kill chain (Initial Access, Execution, etc.).
- **Diamond Model**: ATT\&CK te da las _capacidades_ que llenan el vértice «Capacidad» del diamante (ej. `PowerShell`, `Mimikatz`) y ayuda a ligar infraestructura/adversario/víctima.
  Usarlos juntos mejora la narrativa y la priorización.

### 10) Buenas prácticas y recomendaciones para empezar (learning path)

1. **Empieza por la matriz Enterprise** (si trabajas con servidores/PC).
2. **Instala y usa ATT\&CK Navigator**: crea una capa con lo que ya detectas.
3. **Haz un coverage map**: lista de técnicas relevantes → marca si tienes detección, prevención o pruebas.
4. **Ejecuta Atomic Red Team tests** en un laboratorio para 10 técnicas críticas (PowerShell execution, scheduled task, credential dumping, C2 beacon).
5. **Versiona y documenta**: mantén un registro de tu matriz de cobertura y actualízala trimestralmente.
6. **Automatiza enriquecimiento CTI** para aumentar confianza en detecciones.
7. **Participa en la comunidad**: MITRE publica actualizaciones, y hay repositorios y scripts comunitarios.

### 11) Ejercicios rápidos para practicar ahora mismo

- **Ejercicio 1**: usa ATT\&CK Navigator y marca las técnicas que crees que ocurren en tu organización — pregunta: ¿qué técnicas puedes detectar hoy con tus logs?
- **Ejercicio 2**: ejecuta un _Atomic test_ (por ejemplo: PowerShell encoded command) en un entorno controlado y confirma que tu Sysmon/EDR lo registra.
- **Ejercicio 3**: toma una alerta histórica y mapea sus TTPs a ATT\&CK IDs; genera un pequeño playbook por cada técnica detectada.

### 12) Recursos y referencias (para profundizar)

- MITRE ATT\&CK (sitio oficial) — matrices, técnicas y referencias.
- ATT\&CK Navigator — visualización y capas.
- Atomic Red Team — tests mapeados.
- CALDERA — emulación automatizada.
- Repositorios de Sigma rules / Sigma-to-SIEM: reglas para detección mapeadas a ATT\&CK.

### Resumen corto

- **MITRE ATT\&CK** es la guía operativa para describir y analizar _qué hacen_ realmente los adversarios.
- Se compone de **tácticas** (objetivos) y **técnicas/subtécnicas** (métodos).
- Útil para diseño de detecciones, gap analysis, red/purple teaming y documentación de incidentes.
- Empieza mapeando tu telemetría a ATT\&CK, valida con Atomic Red Team y usa Navigator para priorizar trabajo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**                      | **Siguiente 10**      |
| ------------------ | -------------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./11_8_Cyber_Kill_Chain.md) | [⏩](./11_10_SIEM.md) |
