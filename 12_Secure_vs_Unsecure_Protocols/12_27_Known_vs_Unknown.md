| **Inicio**         | **atrás 26**              | **Siguiente 28**     |
| ------------------ | ------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_26_Recovery.md) | [⏩](./12_28_APT.md) |

---

## **Índice**

| Temario                                                                                                                                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| [406. De lo conocido a lo desconocido](#406-de-lo-conocido-a-lo-desconocido)                                                                         |
| [407. Atrapando todas las amenazas: conocidas, desconocidas y desconocidas](#407-atrapando-todas-las-amenazas-conocidas-desconocidas-y-desconocidas) |
| [408. Detectar amenazas conocidas](#408-detectar-amenazas-conocidas)                                                                                 |
| [409. Cómo afrontar las amenazas desconocidas](#409-cómo-afrontar-las-amenazas-desconocidas)                                                         |

# **Conocido vs desconocido**

## **406. De lo conocido a lo desconocido**

![Recuperación](/img/12_Secure_vs_Unsecure_Protocols/contencion_erradicacion_y_recuperacion.jpg "Recuperación")

### 🏢 Teoría empresarial

En las empresas, la ciberseguridad tradicional se ha basado en **lo conocido**: firmas de malware, reglas predefinidas en firewalls, listas negras de IP, y políticas estáticas.

- Esto permite **detectar amenazas repetitivas**, pero es insuficiente ante **ataques nuevos, sin huella previa** (zero-day, APTs, ataques internos).

La transformación hacia la **detección proactiva con IA** cambia el enfoque:

- **De lo reactivo a lo predictivo.** La empresa no solo responde a alertas, sino que detecta comportamientos sospechosos antes de que se materialicen en un ataque.
- **De lo estático a lo dinámico.** Algoritmos de IA (machine learning, deep learning) aprenden el comportamiento normal de usuarios, dispositivos y redes, y detectan anomalías.
- **De lo puntual a lo continuo.** La detección es en tiempo real, alimentada por Big Data y flujos constantes de telemetría.

Ejemplo empresarial:

- Una entidad financiera que usa IA para detectar transacciones inusuales que no corresponden al historial del cliente, evitando fraudes antes de que el dinero salga.

### 📚 La teoría

En términos académicos/técnicos:

1. **Lo conocido**

   - Detección basada en firmas y patrones ya catalogados.
   - Limitación: solo funciona con amenazas registradas.

2. **Lo desconocido**

   - Amenazas sin precedentes (zero-day, ataques polimórficos, insiders).
   - Necesita técnicas como **análisis de anomalías, detección no supervisada, correlación de eventos**.

3. **Transformación con IA**

   - **Modelos supervisados:** entrenados con ejemplos de ataques conocidos (clasificación de malware, phishing, etc.).
   - **Modelos no supervisados:** detectan desviaciones en el comportamiento normal sin requerir muestras previas.
   - **IA generativa:** simulación de ataques para fortalecer defensas y pruebas de penetración automatizadas.
   - **Sistemas híbridos:** combinación de reglas tradicionales con IA que cubre lo desconocido.

Ejemplo técnico:

- Un sistema de **User and Entity Behavior Analytics (UEBA)** con IA detecta que un empleado, que siempre accede en horario laboral desde Lima, ahora inicia sesión de madrugada desde otro país. Aunque no hay firma de malware, el sistema alerta de un posible robo de credenciales.

### 📝 El resumen

La ciberseguridad está evolucionando:

- Antes: se protegía principalmente de lo **conocido** (patrones de ataque registrados).
- Ahora: gracias a la **IA**, se puede detectar y responder también a lo **desconocido**, analizando comportamientos y anomalías.
- Resultado: una defensa **proactiva y dinámica**, que no solo reacciona al ataque, sino que lo anticipa.

Ejemplo resumido:

➡️ Antes: antivirus detecta un virus por su firma.

➡️ Ahora: IA detecta un proceso sospechoso que cifra archivos aunque nunca haya visto ese ransomware.

---

[🔼](#índice)

---

## **407. Atrapando todas las amenazas: conocidas, desconocidas y desconocidas**

### 1. Amenazas **conocidas**

Son ataques que ya han sido identificados y catalogados.

- 🔹 Ejemplo: un virus con firma registrada en una base de datos de antivirus.
- ✅ Cómo se detectan: mediante **firmas, reglas IDS/IPS, listas negras y parches**.
- 📌 Defensa clásica: antivirus, firewalls, filtros de correo.

### 2. Amenazas **desconocidas**

Son variantes de ataques ya existentes, pero modificados para evadir defensas tradicionales.

- 🔹 Ejemplo: un ransomware nuevo basado en otro ya conocido, que cambia su código para no coincidir con firmas previas.
- ✅ Cómo se detectan: mediante **análisis de comportamiento, machine learning y sandboxing**.
- 📌 Defensa moderna: **EDR/XDR** que observa qué hace el archivo (por ejemplo, si intenta cifrar archivos en masa).

### 3. Amenazas **desconocidas-desconocidas** (lo “impredecible”)

Son amenazas completamente nuevas, sin precedentes en la red de la víctima ni en registros globales. También llamadas **zero-day**.

- 🔹 Ejemplo: una vulnerabilidad en un software que nunca antes fue explotada (ataque de día cero en un firewall).
- ✅ Cómo se detectan: mediante **IA avanzada, detección de anomalías en tráfico y telemetría en tiempo real**.
- 📌 Defensa: **Zero Trust, threat hunting proactivo, inteligencia de amenazas (TI)**.

### 🔐 Cómo atraparlas antes de que dañen

1. **Capas de seguridad combinadas**

   - Firewall + IDS/IPS + EDR + XDR + SIEM.
   - Ejemplo: si un atacante rompe una capa, otra lo detiene antes del impacto.

2. **IA y machine learning**

   - Detecta comportamientos anómalos que los humanos no verían.
   - Ejemplo: un usuario descarga 10 GB a medianoche desde un servidor interno → alerta automática.

3. **Modelo Zero Trust**

   - No confiar en nada por defecto, siempre verificar identidad y permisos.
   - Ejemplo: incluso dentro de la red, un empleado debe autenticarse para acceder a bases sensibles.

4. **Threat Hunting (caza proactiva de amenazas)**

   - Analistas buscan actividad sospechosa antes de que se convierta en un incidente.
   - Ejemplo: buscar conexiones ocultas a direcciones IP extrañas en la red.

### 📌 Ejemplo real unificado

Imagina un **hospital conectado a IoT** (cámaras, monitores de pacientes, servidores con historiales médicos):

- **Amenaza conocida** → phishing con un malware ya catalogado. El antivirus lo bloquea.
- **Amenaza desconocida** → una variante de ransomware cifra parte del sistema de facturación, pero el EDR detecta el comportamiento anómalo y lo detiene.
- **Amenaza desconocida-desconocida** → un atacante explota un fallo de día cero en un monitor IoT. El sistema de IA nota un tráfico extraño desde ese dispositivo hacia Rusia y lo aísla antes de que robe datos.

Resultado: el hospital sigue funcionando sin interrupciones.

✅ En resumen:

La clave para **“atrapar todas las amenazas antes de que puedan dañarte”** es **complementar defensas tradicionales (para lo conocido) con detección basada en IA, Zero Trust y threat hunting (para lo desconocido y lo impredecible)**.

---

[🔼](#índice)

---

## **408. Detectar amenazas conocidas**

Detectar _amenazas conocidas_ significa identificar ataques o artefactos que ya tienen huella: firmas, hashes, dominios/IPs maliciosas, patrones de comportamiento previamente documentados (IoC). Es la base de la defensa: si tienes las señales de lo malo, puedes crear reglas y detenerlo rápidamente. Abajo te explico cómo, con ejemplos prácticos y buenas prácticas.

### ¿Qué son las “amenazas conocidas” (IoC típicos)?

- **Hashes de archivos** (MD5/SHA1/SHA256) de malware.
- **Dominios o URLs** de C2 (command-and-control) o phishing.
- **Direcciones IP** asociadas a botnets o exfil.
- **Firmas de red** (patrones TCP/HTTP específicos).
- **Registros/entradas de registro (Windows Registry)**, nombres de servicios, mutexes, rutas de archivo.
- **Comportamientos** detectables (p. ej. proceso que cifra muchos ficheros, descarga ejecutables desde URL conocida).

### Fuentes de intel para alimentar detección

- Feeds comerciales (vendor TI), feeds OSS (AlienVault OTX, AbuseIPDB), bases CVE, MISP y feeds internos (IoC observados en tu entorno).
- Antivirus/EDR vendors (firmas y detecciones).
- Comunidades y repositorios (YARA rules, Snort/Suricata rules).
- VirusTotal / servicios de enriquecimiento para validar hashes/domains.

### Tecnologías y capas donde detectar

- **Antivirus / Endpoint (AV/EDR/XDR):** detectan hashes, comportamientos en endpoints y parent-child process trees.
- **IDS/IPS (Snort, Suricata):** inspección de paquetes y firmas de red.
- **Network sensors (Zeek/Bro):** análisis de protocolos y extracción de dominios/URLs.
- **SIEM (Splunk, Elastic):** centraliza logs y aplica correlaciones/consultas contra TI.
- **YARA:** detección de patrones dentro de binarios/pefiles.
- **WAF / Proxy / DNS logs:** detección de accesos a dominios/URLs maliciosas.

### Pipeline típico de detección (cómo funciona en la práctica)

1. **Ingesta** de telemetría (logs, netflow, EDR events, DNS, web proxy).
2. **Normalización** (mapear campos).
3. **Enriquecimiento** con threat intel (lookup contra listas de hashes/domains/IP).
4. **Correlación / reglas / firmas** (al detectar coincidencia, generar alerta).
5. **Scoring / priorización** (criticialidad del activo, confianza del feed).
6. **Triage humano / playbook automático** (investigar, contener, remediar).

### Ejemplos prácticos — reglas y queries

#### 1) Regla Suricata para dominio C2

```text
alert http any any -> any any (msg:"ET MALWARE C2 Domain detected - malicious.example.com";
  flow:established,to_server;
  http.host; content:"malicious.example.com"; nocase;
  classtype:trojan-activity; sid:1000001; rev:1;)
```

> Si un cliente hace requests con `Host: malicious.example.com`, Suricata genera alerta.

#### 2) YARA — detectar muestras con cadenas características

```yara
rule KnownBadSample {
  meta:
    author = "Gustavo"
    description = "Ejemplo: detecta strings usados por muestra maliciosa"
  strings:
    $s1 = "evil_function_init" ascii
    $s2 = "CONNECT /c2.php" ascii
  condition:
    any of them
}
```

> Escanea ejecutables o zips en repositorios para detectar coincidencias.

#### 3) Splunk (SPL) — buscar hash de archivo en logs de endpoints

```spl
index=endpoint_logs sourcetype=edr:file_events file_sha256="SHA256_HASH_A_DETECTAR"
| stats count by host, user, file_path, _time
```

> Si el hash coincide, obtienes hosts afectados, usuarios y rutas.

#### 4) Splunk — detectar PowerShell con `-EncodedCommand`

```spl
index=wineventlog EventCode=4688 Image="*\\powershell.exe" (CommandLine="*-EncodedCommand*" OR CommandLine="*IEX*")
| table _time host user CommandLine
```

> Patrón conocido de técnicas de ejecución ofuscada; genera alerta para triage.

#### 5) Kibana / Elastic (KQL) — acceso a dominio listado en feed TI

```
http.request.url : "*malicious.example.com*" or dns.question.name : "malicious.example.com"
```

> Encuentra client requests o consultas DNS.

### Ejemplo de flujo de detección → respuesta

1. TI feed ingesta: `malicious_hashes.csv` cargado al SIEM.
2. SIEM correlaciona `file_create` events en endpoints y encuentra coincidencia.
3. Alerta automática (alta prioridad) enviada al SOC.
4. SOAR enriquece alert con VirusTotal + asset owner + criticidad.
5. Playbook automático: aislar host en EDR + generar ticket + tomar snapshot forense.

### Buenas prácticas para detectar amenazas conocidas

- **Actualizar firmas y feeds con frecuencia.** Parchea y renueva TI.
- **Gestionar la calidad de feeds:** prioriza feeds con baja tasa de falsos.
- **Enriquecer IoC con contexto:** activo crítico, vulnerabilidades, usuario.
- **Tuning de reglas:** evitar reglas demasiado genéricas que generan _false positives_.
- **Versionar y probar firmas** en entornos de staging antes de desplegar en prod.
- **Aplicar whitelist para reducir ruido** (p. ej. hashes internos legítimos).
- **Automatizar enriquecimiento** (VirusTotal, WHOIS, GeoIP, MISP) para acelerar triage.
- **Correlación entre fuentes**: evento de red + evento de endpoint = mayor confianza.
- **Retirar/actualizar IoC obsoletos** (IPs dinámicas, dominios ya neutralizados).

### Limitaciones y cómo compensarlas

- **Evasión:** atacantes usan ofuscación/polimorfismo para evitar hashes y firmas. → Compensar con EDR/behavioral detections y YARA.
- **Falsos positivos:** las reglas tienen ruido; ajustar basado en contexto.
- **Cobertura:** no todas las amenazas tienen IoC públicos. → Complementar con detección basada en anomalías y threat hunting.

### Métricas clave (qué medir)

- **MTTD** (tiempo medio para detectar).
- **Número de detecciones de IoC** (por tipo: hash, domain, IP).
- **Tasa de falsos positivos** y tiempo de triage promedio.
- **Cobertura de endpoints / sensores** (porcentaje de hosts/reportando).

### Caso práctico resumido

- Un feed TI reporta `SHA256=ABC...` como ransomware. SIEM ingiere el feed (tarea programada).
- Un host reporta `file_create` con ese SHA256: SPL lo detecta → alerta.
- SOAR aísla host via EDR, captura snapshot y ejecuta playbook: revocar sessions, notificar CSIRT.
- Forense confirma infección; se erradica y se restaura desde backup.

### Recomendación final

Detectar amenazas conocidas es rápido y efectivo si tienes: **buenas fuentes de threat intel, sensores bien desplegados (EDR/IDS/SIEM), reglas firmes y procesos automáticos de enriquecimiento y respuesta**. Pero no confíes solo en firmas: combínalo con detección de comportamiento y threat hunting para cubrir lo desconocido.

---

[🔼](#índice)

---

## **409. Cómo afrontar las amenazas desconocidas**

### 1) ¿Qué son las _amenazas desconocidas_ y por qué son un problema?

- **Definición:** ataques o artefactos que no existen en las bases de firmas ni en feeds de IOC: zero-day, malware polimórfico, técnicas nuevas de ejecución o exfiltración.
- **Por qué son peligrosas:** pasan bajo las defensas basadas en firmas; pueden permanecer más tiempo sin ser detectadas; suelen usarse en campañas dirigidas (APT) o en compromisos de cadena de suministro.

### 2) Enfoque estratégico (la idea principal)

Para cubrir lo desconocido necesitas pasar de “detectar lo que ya conozco” a **detectar comportamientos y anomalías**. Resumen de enfoque:

1. **Telemetría amplia y de calidad** (endpoints, red, DNS, proxy, logs de aplicaciones, cloud).
2. **Detección basada en comportamiento** (EDR/XDR, UEBA, reglas heurísticas).
3. **Caza de amenazas proactiva (threat hunting)** con hipótesis y modelos estadísticos.
4. **Deception & canaries** para exponer lateral movement temprano.
5. **Respuesta rápida y prep. forense** (snapshots, volcado RAM, chain of custody).
6. **Retroalimentación**: aprender y convertir hallazgos en detecciones automáticas (detección engineering).

### 3) Técnicas concretas para detectar lo desconocido

#### A. Telemetría mínima imprescindible

Recolecta y centraliza:

- Eventos de procesos (EDR), command line, hashes.
- Conexiones de red y flow logs (VPC Flow, NetFlow).
- DNS queries y respuestas (resolver logs).
- Logs de proxy/HTTP, syslogs, autenticaciones (AD/AzureAD), cloud audit (CloudTrail).
- Integridad de archivos / cambios en masa.

> Sin datos no hay detección. Conserva telemetría suficiente (retención acorde al riesgo).

#### B. Detección de comportamiento (EDR / XDR / UEBA)

- Detecciones por _técnicas_, no por firma — mapear a MITRE ATT\&CK.
- Ejemplos: ejecución de procesos hijos inusuales (`winword -> powershell`), procesos que abren muchas conexiones salientes, procesos que renombraron sbin/cron o crean servicios.
- UEBA: detectar usuarios que se desvían de su “peer group” (ubicación, volumen de accesos, horarios).

#### C. Detección por anomalías y ML (no supervisado)

- Modelos de clustering / outlier detection (k-means, DBSCAN, isolation forest) para encontrar patrones raros.
- Métricas simples: z-score sobre bytes transferidos, número de dominios visitados por hora, cantidad de procesos que escriben a disco.
- Importante: interpretar y explicar las detecciones (evitar “cajas negras” sin contexto).

#### D. Rules & Indicators heurísticos

- Reglas genéricas que no dependen de firmas:

  - “Proceso escribe >1000 ficheros en 10 min”.
  - “Proceso con nombre legítimo ejecuta base64/EncodedCommand”.
  - “Host consulta cientos de subdominios distintos (DNS flux)”.

#### E. Sandboxing y análisis dinámico

- Subir muestras sospechosas a sandbox (automático) y analizar comportamiento: sockets creados, archivos modificados, llamadas WinAPI.
- Usar ejecución aislada para ver comportamientos que no son firmas.

#### F. Deception (honeypots/canaries)

- Crear cuentas canary, ficheros señuelo, máquinas trampas que idealmente no reciben tráfico legítimo: si interactúan, indican compromiso temprano.

#### G. Threat Hunting programático

- Definir hipótesis (ej.: “¿Hay señales de exfil por DNS en los últimos 7 días?”) y ejecutar búsquedas periódicas.
- Convertir hallazgos en reglas o playbooks.

### 4) Medidas preventivas que reducen el impacto (no detectan, evitan)

- **Seguridad de identidad:** MFA obligatorio, bloqueo geográfico, session management.
- **Minimizar superficie de ataque:** least privilege, aplicación de control de aplicaciones (allowlist), deshabilitar RDP público.
- **Patching & virtual patching:** respuesta rápida a vulnerabilidades públicas y WAF con reglas genéricas.
- **Segmentación y microsegmentación:** limitar movimiento lateral.
- **Imagenes e infraestructura vetadas/provisionadas firmado:** AMIs/containers firmados (image provenance).
- **Backups frecuentes y probados** (air-gapped o immutable).

### 5) Playbook resumido para **un posible evento desconocido** (paso a paso)

**Detección inicial (0–minutes)**

- Alerta: EDR/UEBA detecta anomalía (ej. proceso que cifra muchos archivos).
- Triage rápido: confirmar con logs (process tree, command line, network connections).

**Contención (first 0–60 min)**

- Aislar host con EDR (isolate).
- Tomar snapshot de disco y volcado de memoria si aplica.
- Capturar logs relevantes y copiar a bucket forense read-only.

**Análisis forense**

- Analizar volcado RAM para detectar payloads en memoria (Volatility/Velociraptor).
- Revisar parent/child process tree, módulos cargados, comunicaciones de red.

**Erradicación**

- Si es malware: eliminar binario y persistencias o rebuild desde imagen limpia.
- Rotar credenciales asociadas con la cuenta/host.
- Cerrar vectores (aplicar reglas firewall, bloquear dominios).

**Recuperación**

- Restaurar desde backup comprobado, reintroducir host con monitoreo intensivo.
- Aumentar detección sobre indicadores derivados.

**Lecciones aprendidas**

- Crear nuevas reglas de detección (EDR/SIEM), agregar IoC al TI, actualizar playbooks.

### 6) Ejemplos prácticos y consultas útiles

#### Ejemplo A — Ransomware _nuevo_ detectado por comportamiento

- **Señal:** un proceso interno (no firmado) comienza a cifrar ficheros masivamente.
- **Regla heurística (SIEM):**

  ```spl
  index=file_events event=write | stats count by host, process_name, user, file_extension
  | where count > 500 AND file_extension IN ("docx","xls","pdf","db")
  ```

- **Acciones automáticas:** aislar host, snapshot EBS, bloquear SMB share en firewall.

#### Ejemplo B — Exfil vía DNS (técnica desconocida)

- **Señal:** muchos queries DNS a subdominios largos/aleatorios por una sola IP interna.
- **Query (Splunk):**

  ```spl
  index=dns sourcetype=dns_logs earliest=-1h
  | stats dc(query) as unique_subdomains, count by src_ip
  | where unique_subdomains > 200
  ```

- **Acción:** bloquear resolución para ese host, capturar pcap del tráfico DNS, investigar proceso que generó queries.

#### Ejemplo C — Comando PowerShell ofuscado (EncodedCommand)

- **Query (EDR/SIEM):**

  ```spl
  index=processes process_name="powershell.exe" CommandLine="*EncodedCommand*" OR CommandLine="*IEX*"
  | table _time host user CommandLine
  ```

- **Acción:** aislar host, extraer commandline y decodificar, sandbox de payload.

### 7) Detección engineering y reducción de falsos positivos

- **Priorización por contexto**: activo crítico, usuario privilegiado, hora inusual.
- **Peer-group baselining**: compara el comportamiento con otros servidores del mismo rol.
- **Ajuste iterativo**: validar y medir tasa de falsos positivos; afinar umbrales.
- **Enriquecimiento automático**: GeoIP, reputación de dominio, owner del asset.

### 8) Orquestación y automatización segura

- Usa **SOAR** para tareas repetibles (snapshot, block IP, create ticket).
- **Human-in-the-loop** para acciones destructivas (terminate instance, revoke role).
- Registra todas las acciones (audit trail).

### 9) Cultura, procesos y people

- **Threat hunting regular** y ejercicios purple/red teaming.
- **Playbooks probados** en tabletop exercises.
- Formación continua (phishing drills, awareness).
- SLA y escalamiento claros para incidentes de alta severidad.

### 10) Métricas clave para medir eficacia frente a lo desconocido

- **MTTD (Mean Time To Detect)** para anomalías.
- **MTTR (Mean Time To Respond)** tras una anomalía.
- % de detecciones no basadas en firmas.
- Número de hunts que derivan en detecciones nuevas.
- Falsos positivos por regla (para tuning).

### 11) Casos reales (resumidos, qué aprender)

- **Cadena de suministro (p. ej. SolarWinds):** solución → monitorizar cambios en software firmado, validar hashes y comportamiento de procesos de actualización.
- **Zero-day en un appliance IoT:** mitigación → segmentación y aplicacion de microsegmentación + bloqueo de comportamientos anómalos de dispositivo.
- **APT con living-off-the-land (LOTL):** detectar por uso anómalo de comandos legítimos (PowerShell, WMI), crear reglas basadas en contextos y parent-child.

### 12) Checklist rápido (qué debes tener ya)

- [ ] Telemetría completa (EDR + DNS + proxy + network flows + cloud logs).
- [ ] Playbooks de contención con snapshots forenses.
- [ ] Sistema UEBA y reglas de anomalías configuradas.
- [ ] Programa de threat hunting activo (mensual/quincenal).
- [ ] Deception/canaries en segmentos sensibles.
- [ ] Automatización SOAR con guardrails humanos.
- [ ] Backups verificables y procesos de reinstalación limpia.
- [ ] Inventario de activos y clasificación por criticidad.

### Conclusión corta

Afrontar lo desconocido no es posible solo con firmas: requiere **datos buenos**, **detección por comportamiento**, **caza proactiva**, **deception**, y **respuesta rápida**. Lo ideal es un ciclo continuo: **telemetría → detección → hunting → automatización → aprendizaje**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 26**              | **Siguiente 28**     |
| ------------------ | ------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_26_Recovery.md) | [⏩](./12_28_APT.md) |
