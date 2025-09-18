| **Inicio**         | **atrás 7**                   | **Siguiente 9**        |
| ------------------ | ----------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./11_7_Diamond_Model.md) | [⏩](./11_9_ATT&CK.md) |

---

## **Índice**

| Temario                                                                                                |
| ------------------------------------------------------------------------------------------------------ |
| [347. Cadena de muerte cibernética](#347-cadena-de-muerte-cibernética)                                 |
| [348. Aprenda la cadena de eliminación cibernética](#348-aprenda-la-cadena-de-eliminación-cibernética) |
|                                                                                                        |

# **Cyber Kill Chain**

## **347. Cadena de muerte cibernética**

![Cyber Kill Chain](/img/11_Basics_of_Cryptography/Cyber_Kill_Chain.jpg "Cyber Kill Chain")

### 1 — ¿Qué es la _Cadena de Muerte Cibernética_?

La **Cadena de Muerte Cibernética** es un modelo (framework) que descompone un ataque dirigido en **etapas secuenciales**. Fue popularizado por Lockheed Martin como **Cyber Kill Chain**. Su propósito es entender el proceso de un adversario para identificar puntos donde interrumpir el ataque (detección/mitigación) y priorizar defensas.

La idea clave: si detectas y detienes una etapa temprana, evitas daño posterior. También sirve para estructurar hunting, detección y respuesta.

### 2 — Etapas clásicas de la Kill Chain (Lockheed Martin)

1. **Reconocimiento (Reconnaissance)**
2. **Armamento (Weaponization)**
3. **Entrega (Delivery)**
4. **Explotación (Exploitation)**
5. **Instalación (Installation)**
6. **Comando y Control (C2 — Command & Control)**
7. **Acciones sobre el Objetivo (Actions on Objectives / Exfiltration / Impact)**

A continuación explico cada etapa con ejemplos y defensas/detecciones.

### 3 — Etapas con ejemplos y medidas defensivas

#### 1) Reconocimiento

**Qué hace el atacante:** recopila información pública y privada sobre la organización: perfiles de empleados, direcciones de correo, subdominios, versiones de software, ASNs, puertos abiertos, mapas de red, empleados en LinkedIn, etc.

**Ejemplo:** atacante usa Google Dorking, social media scraping, y Nmap sobre IPs públicas para encontrar servidores RDP expuestos.

**Detecciones / Mitigaciones:**

- **Mitigación:** reducir la huella pública (hardening, WAF, ocultar servicios), políticas de privacidad en redes sociales, segmentación de red, reducir servicios expuestos.
- **Detección:** logs de escaneo en perimeter firewall (many SYNs), IDS/IPS signatures de escaneo, alertas de múltiples intentos a puertos distintos desde misma IP/ASN.
- **Telemetría útil:** netflow, firewall logs, webserver logs, honeypots, threat intel sobre ASN.

#### 2) Armamento

**Qué hace el atacante:** prepara la herramienta/malware que explotará la vulnerabilidad o el vector (ej. crear un payload PE con un loader, empaquetar un exploit en un doc con macro, empaquetar ransomware). Puede crear spear-phishing emails con adjuntos maliciosos o preparar páginas de phishing.

**Ejemplo:** compilar un ejecutable que contenga un loader que descarga Cobalt Strike, o crear un documento Word con macro que ejecuta PowerShell.

**Detecciones / Mitigaciones:**

- **Mitigación:** bloquear ejecución de macros por política, sandboxing de correos, filtrar tipos de adjuntos, inspección de archivos en gateway.
- **Detección:** sandboxing y AV/Detección de malware pre-execution, análisis de attachments con detonation-sandbox, YARA en reposo de correo.
- **Telemetría útil:** email gateway logs, sandbox reports, DLP, antivirus alerts.

#### 3) Entrega

**Qué hace el atacante:** envía el arma al objetivo: correo de spear-phishing, exploit en página web (drive-by), USB con malware, paquete de software comprometido, SMS (smishing).

**Ejemplo:** spear-phishing dirigido a jefe de finanzas con un documento que parece una factura — al abrirla ejecuta macro.

**Detecciones / Mitigaciones:**

- **Mitigación:** formación de usuarios (phishing awareness), MFA, bloqueo de macros, políticas de adjuntos, correo filtrado, DMARC/DKIM/SPF.
- **Detección:** correlación de envíos masivos, patrones de spear-phish (same template), clicks en enlaces detectados por proxy, sandbox detonations showing persistence attempts.
- **Telemetría útil:** email logs, web proxy logs, URL reputation, user-behavior analytics.

#### 4) Explotación

**Qué hace el atacante:** aprovecha una vulnerabilidad o técnica (macro → PowerShell, RCE en web, exploiting RDP) para ejecutar código en el objetivo.

**Ejemplo:** documento ejecuta comando PowerShell que descarga y ejecuta un loader, aprovechando una máquina sin parchear.

**Detecciones / Mitigaciones:**

- **Mitigación:** patching/gestión de vulnerabilidades; aplicar least privilege; deshabilitar características innecesarias (p. ej. macros).
- **Detección:** EDR detecta comportamiento sospechoso (child processes, encoded commands), Sysmon observando EventID 1 (Process Create) con comandos largos o suspicious parent-child.
- **Telemetría útil:** EDR, Sysmon, Windows Event Logs, PowerShell logging.

#### 5) Instalación (Persistencia)

**Qué hace el atacante:** instala backdoors, crea cuentas, configura servicios, programas en inicio, tareas programadas, modifica claves de registro para persistir.

**Ejemplo:** el loader escribe `sysupdate.exe` en `C:\Windows\Temp` y añade tarea programada o servicio para reinicio.

**Detecciones / Mitigaciones:**

- **Mitigación:** aplicación de whitelisting (AppLocker), control de cuentas privilegiadas, restricciones de escritura en carpetas críticas.
- **Detección:** EDR que detecte creación de servicios, nuevas tareas programadas, persistencia via registry Run keys; monitor file writes en lugares clave.
- **Telemetría útil:** EDR alerts, Sysmon EventID 11 (file create), EventID 4698 (task created).

#### 6) Comando y Control (C2)

**Qué hace el atacante:** canal de comunicación con el implant para recibir órdenes y entregar datos. Puede usar HTTP/S, DNS, covert channels, servicios legítimos (cloud storage, social media) como C2.

**Ejemplo:** implant beaconing via HTTPS to `updates-svc[.]com` every 30s, instructs to download additional payloads.

**Detecciones / Mitigaciones:**

- **Mitigación:** egress filtering, proxy con SSL inspection (si viable), bloquear dominios y IPs maliciosas.
- **Detección:** anomalous beaconing patterns (regular intervals), DNS tunneling detection, user agent oddities, unusual destinations or suspicious certs.
- **Telemetría útil:** DNS logs, proxy/HTTP logs, netflow, TLS fingerprints, EDR network connection logs.

#### 7) Acciones sobre el objetivo (Exfiltración / Impact)

**Qué hace el atacante:** mover lateralmente, escalada de privilegios, buscar datos sensibles, exfiltrar información, cifrar archivos (ransomware) o destruir.

**Ejemplo:** adversary finds HR database, compresses CSVs and uploads to attacker-controlled cloud bucket; later triggers ransomware to encrypt file shares.

**Detecciones / Mitigaciones:**

- **Mitigación:** segmentation, DLP, least privilege, MFA, backups air-gapped, application allowlists, EDR response, offline backups.
- **Detección:** unusual data transfers, bulk file read by user not normal, sudden encryption behavior (大量 file modifications with rename/extension changes), anomalous privileged use.
- **Telemetría útil:** file server logs, DLP, netflow, EDR, SIEM correlation.

### 4 — Variantes y mapas a MITRE ATT\&CK

La kill chain es complementaria con **MITRE ATT\&CK**: cada etapa puede mapearse a técnicas ATT\&CK (ej: Recon → T1592; Delivery → T1204 (spearphishing); Exploitation → T1203; Persistence → T1547; C2 → T1071; Exfiltration → T1041 / T1567; Impact → T1486 ransomware). Usar ambos frameworks ayuda a diseñar detecciones concretas y playbooks.

### 5 — Detecciones prácticas / reglas ejemplo (conceptuales)

#### Detección básica (PowerShell encoded + outbound to new domain)

- Regla: Si hay `ProcessCreate` Powershell con `-EncodedCommand` **y** en 2 minutos posteriores el host resolvió/conn a dominio no visto before → alerta alta.

#### Detección de beaconing (C2)

- Identificar hosts que hacen conexiones regulares (ej. every 30s–5m) a dominios/internet endpoints con baja entropy (rare) → posible beacon.

#### Detección de exfil

- Alertar transferencias TCP/HTTPS con altos volúmenes desde servidores de archivos a destinos no aprobados; correlacionar con procesos iniciadores.

(Implementa estas ideas en Sigma / Splunk / Elastic adaptando campos específicos.)

### 6 — Caso práctico, paso a paso (historia ficticia pero realista)

#### Escenario

1. **Recon:** adversary identifica un servidor de facturación con port 3389 abierto.
2. **Armamento / Delivery:** envía spear-phish a contabilidad con un ZIP que contiene un LNK que ejecuta `powershell -EncodedCommand` (payload).
3. **Exploitation:** empleado abre el LNK → PowerShell descarga `updater.exe` y lo ejecuta.
4. **Installation:** `updater.exe` crea tarea programada `Updater` y un servicio `SysUpdate`.
5. **C2:** el binario beacons a `secure-updates[.]com` cada 45s (HTTPS).
6. **Actions:** el adversary mueve lateralmente, volcando credenciales y accediendo a la base de datos, exfiltrando CSVs a un S3 malicioso; luego despliega ransomware.

#### Detección & respuesta (aplicada)

- Detección inicial: EDR flagged PowerShell with encoded command → SOC investiga.
- Correlación: Proxy logs show host resolved `secure-updates[.]com` → VT shows domain suspicious.
- Contención: aislar host, bloquear dominio en firewall, detener tarea programada, recollect memoria, recoger IOC (hashes, domain, IP).
- Remediation: restaurar desde backup, rotar credenciales, aplicar parche/mitigar vector RDP/exposed service, búsqueda lateral (hunt) por IOC.
- Lecciones: habilitar MFA, bloquear macros/LNK execution, mejorar detección de beacon patterns.

### 7 — Buenas prácticas defensivas organizadas por etapas

- **Antes (Preventivo):** gestión de vulnerabilidades, MFA, WAF, egress filtering, hardening, least privilege, código firmado, políticas de email (DMARC/DKIM), formación.
- **Durante (Detección):** EDR, Sysmon, DNS/proxy logging, SIEM correlation, CTI enrichment, sandboxing de attachments.
- **Después (Respuesta):** playbooks, backups, forensic imaging, rotation credenciales, communication plans, lessons learned.

### 8 — Cómo usar la Kill Chain para diseñar detecciones efectivas

1. **Mapea tu telemetría** a etapas (qué logs apoyan cada etapa).
2. **Prioriza activos**: si una etapa ocurre en un host crítico, prioridad alta.
3. **Crea reglas que crucen etapas** (ej. detection = Delivery ∧ C2).
4. **Automatiza enriquecimiento** (VT, passive DNS, WHOIS) para aumentar confianza.
5. **Construye playbooks** por etapa (qué hacer si detectas explotación vs C2).
6. **Ejercicios y drills**: simula spear-phish, beaconing y exfil para medir MTTD/MTTR.

### 9 — Limitaciones del modelo

- **Secuencialidad aparente:** ataques reales pueden ser no lineales o simultáneos (p. ej. recon y entrega repetidos).
- **No cubre motivación/atribución** por sí mismo.
- **Puede dar foco en técnicas tradicionales**; adversarios modernos usan living-off-the-land (LOTL) que pasa por alto controles basados en binaries.
- **Necesita telemetría adecuada**: sin logs no se detecta nada.

### 10 — Resumen visual (ASCII)

```
Recon → Armamento → Entrega → Explotación → Instalación → C2 → Actions on Objective
```

### 11 — Mini-playbook de respuesta rápido (si detectas actividad en cualquier etapa)

1. **Recolectar:** guardar logs relevantes, memoria, imágenes, captura de red.
2. **Aislar:** desconectar host afectado (según impacto).
3. **Correlacionar:** buscar IOC (domain, hash, IP) en toda la red.
4. **Bloquear:** egress a dominios/IPs maliciosas, bloquear cuentas comprometidas.
5. **Erradicar:** eliminar payloads, tareas programadas, servicios maliciosos.
6. **Recuperar:** restaurar desde backup verificado; rotar credenciales.
7. **Aprender:** postmortem, aplicar lecciones (patch, políticas, formación).

---

[🔼](#índice)

---

## **348. Aprenda la cadena de eliminación cibernética**

### ¿Qué es la Cadena de Eliminación Cibernética?

Es un modelo secuencial que describe las **fases** por las que pasa un adversario para lograr un objetivo (espionaje, exfiltración, cifrado). Fue popularizado por Lockheed Martin. Se usa como marco para **pensar defensivamente**: si detectas o bloqueas una etapa temprana, evitas etapas posteriores.

Las etapas clásicas (Lockheed) son:

1. Reconocimiento
2. Armamento
3. Entrega
4. Explotación
5. Instalación (persistencia)
6. Comando y Control (C2)
7. Acciones sobre el objetivo (movimiento lateral, exfiltración, impacto)

A partir de aquí, vamos a desglosarlo.

#### Etapa 1 — Reconocimiento (Recon)

**Qué hace el adversario:** recopila información pública y privada (direcciones, servicios expuestos, empleados, versiones de software, subdominios, puertos).

**Ejemplos concretos:** scraping de LinkedIn para perfiles, escaneo externo con Nmap para detectar RDP/SSH expuestos, enumeración de subdominios con herramientas públicas.

**Cómo defender/detectar:**

- Minimizar huella pública (ocultar emails directos, limitar info en redes).
- Monitorizar logs de firewall/IDS para patrones de escaneo (muchos SYN, intentos a múltiples puertos desde una IP/ASN).
- Deploy honeypots para detectar escaneos.
  **Señal de alerta:** ráfagas de probes a puertos no públicos desde un mismo ASN.

#### Etapa 2 — Armamento (Weaponization)

**Qué hace el adversario:** prepara la “herramienta” (malware, exploit empaquetado, documento con macro, instalador modificado).

**Ejemplos:** crear un documento Word con macro que descarga un loader; empaquetar un ejecutable con un packer/obfuscator.

**Defensa/detección:**

- Sandboxing/Detonation en gateway de correo.
- Filtrado de adjuntos, bloquear extensiones (p.ej. .exe en correos).
- Revisión y whitelisting de artefactos legítimos.
  **Señal de alerta:** archivos adjuntos nuevos con comportamiento de persistencia en sandbox.

#### Etapa 3 — Entrega (Delivery)

**Qué hace el adversario:** envía el arma al objetivo: spear-phishing, drive-by (sitio comprometido), USB, supply-chain.

**Ejemplos:** correo dirigido con factura falsa; URL en campaña SMS (smishing).

**Defensa/detección:**

- Filtrado AV + sandbox en mail gateways.
- DMARC/DKIM/SPF bien configurados.
- Formación de usuarios y phishing tests.

  **Señal de alerta:** usuarios que abren el mismo adjunto o hacen click en links de nuevo dominio no usado.

#### Etapa 4 — Explotación (Exploitation)

**Qué hace el adversario:** aprovecha una vulnerabilidad o técnica para ejecutar código en el host.

**Ejemplos:** macro que ejecuta PowerShell, RCE en web app, credenciales expuestas para RDP.

**Defensa/detección:**

- Patching y gestión de vulnerabilidades.
- EDR con detección de comportamientos (child processes inusuales, PowerShell encoded).
- Sysmon/Enhanced logging para detectar procesos sospechosos.

  **Señal de alerta:** parent-child process chains inusuales (p.ej. winword.exe → powershell.exe → rundll32).

#### Etapa 5 — Instalación / Persistencia

**Qué hace el adversario:** implanta backdoors, servicios, tareas programadas, modifica registry run keys o instala agentes.

**Ejemplos:** servicio `UpdaterSvc`, tarea programada `SysPatch`, creación de cuenta local con privilegios.

**Defensa/detección:**

- App whitelisting (AppLocker), hardening de endpoints.
- Monitoreo de creación de servicios y tareas (Sysmon Events, EDR).
- Control de cambios en AD y logs de creación de usuarios.

  **Señal de alerta:** nuevo servicio en host crítico, tarea programada creada fuera de ventana de mantenimiento.

#### Etapa 6 — Comando y Control (C2)

**Qué hace el adversario:** establece comunicación entre el implant y el operador (polling, long-poll, DNS, HTTPS, DNS tunneling).

**Ejemplos:** beaconing cada 30–60s a `updates-svc[.]com`, exfil via DNS TXT, uso de Google Drive como staging.

**Defensa/detección:**

- Egress filtering y proxies con inspección TLS (si es viable).
- Detección de patrones de beaconing (intervalos regulares, small packets).
- Análisis de DNS (tunneling detection), detección de dominios recién registrados o con baja reputación.

  **Señal de alerta:** host persistente conectando a dominio raramente visto en intervalos regulares.

#### Etapa 7 — Acciones sobre el objetivo (Objective: exfiltration / impact)

**Qué hace el adversario:** movimiento lateral, escalada de privilegios, recolección y exfiltración de datos, cifrado de dispositivos (ransomware), destrucción.

**Ejemplos:** uso de Mimikatz para robar hashes, RDP lateral movement, compresión de archivos y upload a S3 malicioso, cifrado en masa.

**Defensa/detección:**

- Segmentación de red, least privilege, MFA.
- DLP para detectar exfil (volúmenes atípicos, copying large file sets).
- Detectores de ransomware (crecimiento de I/O y extensiones nuevas).

  **Señal de alerta:** lectura masiva de shares sensibles seguida de conexiones a endpoints externos no aprobados.

### Cómo mapear detecciones a la Kill Chain (práctica)

Para cada etapa define preguntas, telemetría y reglas. Ejemplo:

- **Recon** → Telemetría: firewall/IDS/netflow. Regla: muchos puertos probados por una IP en 1 minuto → alerta de scanning.
- **Delivery** → Telemetría: mail gateway/sandbox. Regla: sandbox flags suspicious macro → quarantine email + alert.
- **Exploitation** → Telemetría: Sysmon/EDR. Regla: ProcessCreate where CommandLine contains `-EncodedCommand` → escalate and isolate.
- **C2** → Telemetría: proxy/DNS. Regla: host connecting to domain with reputation < threshold and beaconing pattern → block and investigate.
- **Exfil** → Telemetría: netflow/DLP. Regla: large outbound transfer from file server to external IP not in whitelist → high-priority incident.

### Integración con MITRE ATT\&CK

La Kill Chain es un marco de proceso; **ATT\&CK** te da técnicas/IDs concretos para cada etapa. Mapear tus detecciones a ATT\&CK te permite:

- Construir reglas basadas en técnicas (TTPs).
- Priorizar playbooks: si observas T1059 (Command and Scripting Interpreter) + T1071 (Application Layer Protocol) → alta probabilidad de C2.

### Ejemplo completo, paso a paso (caso realista)

**Escenario**: Empresa financiera detecta actividad inusual en servidor de nóminas.

1. **Recon**: logs firewall muestran barridos a RDP desde ASN sospechoso.
2. **Delivery**: correo con factura adjunta (ZIP→.LNK) llega a contabilidad y es abierto.
3. **Exploitation**: LNK ejecuta `powershell -EncodedCommand` que descarga `updator.exe`. EDR marca proceso inusual.
4. **Installation**: `updator.exe` crea tarea programada `SysUpdater` y persistencia en registry.
5. **C2**: el proceso beaconea a `secure-updates[.]com` cada 30s.
6. **Actions**: atacante usa Mimikatz, accede a share `\\fileserver\HR`, comprime CSV y lo sube a un bucket S3 malicioso; luego despliega ransomware.

**Respuesta recomendada (playbook resumido):**

- Aislar host (network quarantine).
- Recolectar memoria y volcar proceso (forensic).
- Bloquear dominio/IP en firewall y proxy.
- Buscar IOCs (hash, domain, IP) en toda la red y contener hosts afectados.
- Rotar credenciales, restaurar desde backups, notificar stakeholders y preparar post-mortem.

### Ejercicios prácticos para aprender la Kill Chain (hands-on)

1. **Ejercicio 1 — Mapear incidentes reales**: toma un informe público de breach (e.g., report de APT) y dibuja la kill chain, identificando detecciones que pudieron evitar cada etapa.
2. **Ejercicio 2 — Simulación de phishing controlada**: lanza un ejercicio de phishing a un grupo y monitorea detecciones en mail gateway/proxy/EDR. Mapea resultados a etapas Delivery → Exploitation.
3. **Ejercicio 3 — Beacon detection lab**: montar un pequeño implant (simulado que solo beacon a un domain) y prueba reglas de detección de beaconing (regular intervals) en tu proxy/logs.
4. **Ejercicio 4 — Tabletop**: simula un incidente (role play SOC/IR/CISO) y usa la kill chain para decidir contención: ¿aislar host? ¿bloquear dominio? ¿apagar servidor? — registra tiempos (MTTD/MTTR).

### Métricas útiles relacionadas con la Kill Chain

- **MTTD por etapa**: tiempo medio para detectar un evento en Recon vs C2 vs Exfil.
- **% ataques detenidos en etapa X**: p.ej. cuántos ataques fueron bloqueados en Delivery.
- **Tiempo entre etapas**: latencia entre Delivery → Exploitation → C2 (útil para ajustar playbooks).
- **Cobertura de telemetría**: % hosts con EDR, % consultas DNS registradas, % emails sandboxeados.

### Buenas prácticas para aplicar la Kill Chain en tu organización

1. **Inventario y priorización de activos**: mapa de activos críticos para priorizar detecciones.
2. **Cobertura de logs**: EDR en endpoints, DNS/proxy/flow en red, mail gateway con sandbox.
3. **Reglas cruzadas**: no confiar en una sola fuente — correlaciona (p. ej. ProcessCreate + DNS suspicious = alerta).
4. **Playbooks por etapa**: tener pasos definidos para contención/erradicación por cada fase detectada.
5. **Ejercicios regulares**: phishing/labs/ransomware drills para medir eficacia.
6. **CTI & ATT\&CK mapping**: enriquecer detecciones con feeds y mapear técnicas.
7. **Automatización**: bloqueos automáticos de IOC de alta confianza (firewall/proxy) y tickets en SIEM.

### Limitaciones y cómo compensarlas

- **No todos los ataques son lineales**: adversarios pueden iterar o usar living-off-the-land (LOTL). → Compensar con detección basada en comportamiento.
- **Falsa sensación de seguridad** si dependes sólo de controles en una etapa → defensa en profundidad.
- **Dependencia de telemetría** → mejorar cobertura (EDR, DNS logs) para hacer el modelo útil.

### Resumen final (chuleta)

- La Kill Chain: Recon → Armamento → Entrega → Explotación → Instalación → C2 → Acciones.
- Útil para **diseñar detecciones**, priorizar respuesta y realizar hunting.
- Mapear telemetría/ATT\&CK a cada etapa y crear reglas que crucen vértices reduce falsos positivos y acelera respuesta.
- Practica con ejercicios reales: phishing tests, beacon lab y tabletop.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**                   | **Siguiente 9**        |
| ------------------ | ----------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./11_7_Diamond_Model.md) | [⏩](./11_9_ATT&CK.md) |
