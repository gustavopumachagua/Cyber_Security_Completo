| **Inicio**         | **atrás 6**                 | **Siguiente 8**                  |
| ------------------ | --------------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./11_6_Obfuscation.md) | [⏩](./11_8_Cyber_Kill_Chain.md) |

---

## **Índice**

| Temario                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [345. El modelo del diamante: análisis de intrusiones simple basado en inteligencia](#345-el-modelo-del-diamante-análisis-de-intrusiones-simple-basado-en-inteligencia) |
| [346. El modelo de diamante para la detección de intrusiones](#346-el-modelo-de-diamante-para-la-detección-de-intrusiones)                                              |
|                                                                                                                                                                         |

# **Diamond Model**

## **345. El modelo del diamante: análisis de intrusiones simple basado en inteligencia**

![Diamond Model](/img/11_Basics_of_Cryptography/Diamond_Model.webp "Diamond Model")

### ¿Qué es el Modelo Diamante?

El **Modelo Diamante** es un marco conceptual para analizar incidentes de seguridad y actividad maliciosa. Fue diseñado para ayudar al analista a **descomponer un evento en sus piezas fundamentales**, enfocarse en relaciones entre ellas y, a partir de ahí, integrar inteligencia, priorizar detección y planear respuestas. Visualmente se representa como un **diamante** con cuatro vértices interconectados: **Adversario**, **Capacidad**, **Infraestructura** y **Víctima**.

La fuerza del modelo es su simplicidad: reduce eventos complejos a componentes accionables y sus relaciones, facilitando el **enriquecimiento de inteligencia**, la búsqueda de patrones y la construcción de cadenas de actividad (pivotar de un evento a otro).

### Los cuatro componentes del Modelo Diamante

Cada intrusión o “evento” idealmente contiene (al menos) estos cuatro vértices:

#### 1) Adversario

**Quién** está detrás del comportamiento malicioso: actor, grupo o una entidad (puede ser un atacante individual, un APT, un script kiddie).

- Ejemplos: APT28, un cibercriminal que vende ransomware-as-a-service, o un empleado malicioso.

Datos útiles: motivación (financiera, espionaje), TTPs conocidos, afiliaciones, aliases, infraestructura conocida.

#### 2) Capacidad (Capability)

**Qué** usa el adversario para ejecutar la operación: malware, exploit, script, herramienta (Cobalt Strike, Mimikatz, ransomware), o hasta una vulnerabilidad específica (CVE-XXXX-YYYY).

- Ejemplos: un droppers en PowerShell, un exploit RCE para un servidor web, credenciales robadas.

Importante: capacidades pueden reutilizarse por varios adversarios y evolucionan con el tiempo.

#### 3) Infraestructura

**Cómo** se conecta el adversario al objetivo: servidores C2 (command & control), dominios, IPs, servidores proxy, cuentas en servicios cloud, cuentas de correo usadas para phishing.

- Ejemplos: dominio `updates-svc[.]com` que sirve como C2, VPS en un proveedor X, uso de Google Drive para staging.

La infraestructura puede ser alquilada, comprometida o dinámica (fast-flux, bulletproof hosting).

#### 4) Víctima (Victim)

**A quién** atacan: organización, persona, dispositivo, servicio o sector.

- Ejemplos: una empresa financiera, usuarios de una universidad, un servidor de correo corporativo.

La identificación de la víctima permite priorizar y contextualizar impacto y respuesta.

### Axiomas del Modelo Diamante (principios operativos)

En vez de “axiomas matemáticos” estrictos, el Diamond Model define **principios operativos** para guiar el análisis. Aquí te los presento como axiomas/principios prácticos, con explicación:

1. **Todo evento tiene (o puede mapearse a) Adversario–Capacidad–Infraestructura–Víctima.**

   - Cualquier intrusión puede representarse por esos cuatro vértices y las aristas que los conectan.

2. **Las relaciones son bidireccionales y contextuales.**

   - Por ejemplo, la infraestructura conecta adversario y víctima, pero un dominio también puede relacionar varias campañas/atores.

3. **Los metadatos (tiempo, ubicación, resultados) importan para enlazar eventos.**

   - Fechas, hashes, user-agents, y ASNs ayudan a correlacionar y distinguir eventos.

4. **Compartir características (capacidad, infraestructura, víctimas) permite agrupar eventos y atribuir actividad.**

   - Si dos ataques usan el mismo binario y dominio C2, probablemente están relacionados.

5. **Las capacidades pueden ser adquiridas, reutilizadas o compartidas.**

   - Tooling comercial o “off-the-shelf” (Cobalt Strike) puede aparecer en ataques muy distintos.

6. **Infraestructura es efímera y cambiante; su observación temporal es crítica.**

   - IPs y dominios cambian rápido; el análisis temporal detecta patrones (fast-flux, rotación).

7. **La intención/objetivo del adversario puede inferirse a partir de patrones en los vértices y en la secuencia temporal.**

   - Exfil + escaneo interno → posible espionaje; cifrado masivo de archivos → ransomware.

8. **Correlación inteligente (intelligence-driven) mejora detección y respuesta.**

   - Enriquecer con TI (Threat Intel) y pivotar entre eventos permite anticipar campañas.

> Nota: distintos autores/implementaciones enumeran y nombran estos “axiomas” de forma ligeramente distinta; lo importante es entenderlos como **reglas prácticas** que guían la construcción del grafo de eventos y la inferencia.

### Cuándo utilizar el Modelo Diamante

Usa el Diamond Model cuando quieras:

- **Analizar un incidente** y descomponerlo en componentes claros.
- **Correlacionar eventos** (pivotar de IP a malware a actor a víctima).
- **Construir inteligencia accionable** para hunting o para bloquear infraestructuras.
- **Priorizar respuesta** — entendiendo impacto (¿fue espionaje o simple reconocimiento?).
- **Comunicar hallazgos** a equipos no técnicos (visual simple: diamante).

Es ideal en SOCs, equipos IR, threat hunting y en informes de inteligencia.

### Limitaciones del Modelo Diamante

El Diamond Model es poderoso pero no mágico. Entre sus límites:

- **Simplicidad vs. complejidad**: reduce un ataque a cuatro vértices; incidentes muy complejos pueden requerir grafos más ricos o varias capas temporales.
- **No resuelve atribución por sí solo**: compartir infraestructura no prueba necesariamente que el mismo adversario actuó (puede ser false-flag o infraestructura alquilada).
- **Dependencia de datos de calidad**: si tu telemetry es pobre, el grafo quedará incompleto o equívoco.
- **Escalabilidad**: en grandes entornos con miles de eventos necesitas automatización/graph DB o tooling para no perder patrones.
- **Temporalidad**: relacionar eventos requiere timestamps precisos; sin ellos la correlación es débil.
- **Puede inducir a sobregeneralizar**: forzar una puerta causa-consecuencia donde hay coincidencias inocuas.

### El Modelo Diamante en acción — ejemplo paso a paso

Te doy un **caso realista** (resumido) y cómo aplicar el modelo:

#### Escenario

Una empresa detecta tráfico saliente inusual a un dominio extraño y archivos comprimidos grandes saliendo de un servidor de archivos. El SOC abre un incidente.

##### Paso 1 — Recolección inicial y mapeo al diamante

- **Víctima**: `corp.example.com`, servidor de archivos `filesrv01` (HR).
- **Infraestructura**: dominio `updates-svc[.]com` (resuelto a IP 198.51.100.22), puerto 443, user-agent inusual.
- **Capacidad**: binario cargado `sysupdater.exe` con hash `abc123` (malware dropper + exfil module).
- **Adversario**: desconocido (etiquetado como _UNKNOWN_ o _APT?_).

Gráfico simple:

```
Adversario (UNKNOWN)
    ↕
Infraestructura: updates-svc[.]com (198.51.100.22)
    ↕
Capacidad: sysupdater.exe (hash abc123)
    ↕
Víctima: filesrv01.corp.example.com
```

##### Paso 2 — Enriquecimiento de inteligencia

- Lookup WHOIS del dominio → registrante en proveedor bulletproof hosting.
- VT/Hybrid-analysis → `sysupdater.exe` flagged as backdoor/C2.
- Pivot por hash → el binario aparece en otro caso público asociado con exfil y con un actor “FINANCIAL-SEA” (hipotético).
- Revisión de logs → se detecta conexión a IP 198.51.100.22 varias noches a la misma hora (indicio de exfil nocturna).

##### Paso 3 — Extender el diamante y conectar eventos

Se encuentran otras víctimas en la red que contactaron al mismo dominio: `db01`, `websrv02`. Añadimos nodos y aristas. Ahora vemos un patrón de lateral movement antes del exfil.

##### Paso 4 — Inferencia y respuesta

- **Inferencia**: la secuencia sugiere espionaje y exfil de HR/finanzas. La capacidad (malware) y la infraestructura (dominio C2) apuntan a una campaña estructurada.
- **Respuesta**: bloquear dns/IP, aislar `filesrv01`, volcar memoria del proceso `sysupdater.exe`, capturar muestras y feeds a TI externa (CTI), resetear credenciales, forense del servidor y lineas de tiempo.

##### Paso 5 — Reporte y hunt

- Generar IOC: hash `abc123`, domain `updates-svc[.]com`, IP `198.51.100.22`.
- Distribuir IOCs y buscar en logs históricos (hunt) para ver alcance.
- Usar el diamante para priorizar mitigaciones: **bloquear infra** → alto impacto en detener exfil, mientras el patching del vector de entrada (por ejemplo, un RDP mal configurado) es parcheado.

### Cómo operacionalizar el Modelo Diamante (tips prácticos)

- **Grafos y bases**: usa graph DB (Neo4j) o herramientas de TI (MISP, CRITS, TheHive + Cortex) para modelar vértices y aristas.
- **Enriquecimiento automático**: al detectar un hash, automatisch create node Capability(hash) y buscar relación con infra/domains/actor.
- **Metadatos**: añade timestamps, resultados, confidence, fuente (sensor), tags (APT, ransomware) — ayudan a filtrar y priorizar.
- **Automatiza la creación de vínculos**: si dos eventos comparten una IP o dominio, generar enlace automático para investigarlo.
- **Use-case hunting**: construir queries tipo “buscar todos los eventos donde Capability=CobaltStrike AND Infraestructura in ASN X” para cazar campañas.
- **Integración con TI**: exportar IOCs (STIX/TAXII) y compartir internamente o con ISACs.

### Comparación rápida con otros modelos

- **Kill Chain (Lockheed Martin)**: linea temporal y fases (recon, delivery, execution...). Diamond complementa al Kill Chain: Kill Chain explica _fase_; Diamond explica _quién/qué/como/quién_ en un grafo.
- **MITRE ATT\&CK**: catálogo de técnicas (TTPs) que puedes mapear a la **Capacidad** y al **Adversario** del Diamond. Ambos se usan juntos: Diamond para relaciones, ATT\&CK para técnicas y detección.

### Resumen — lo esencial que debes recordar

- El **Modelo Diamante** descompone intrusiones en 4 vértices: **Adversario, Capacidad, Infraestructura, Víctima**.
- Sus **axiomas** son principios prácticos: todo evento puede mapearse con esos vértices; compartir características permite correlación; la temporalidad y metadatos son clave.
- Es ideal para **análisis basado en inteligencia**, hunting, priorización y comunicación entre equipos.
- Limitaciones: dependencia de datos, complejidad en entornos grandes, y no garantiza atribución.
- En la práctica se usa para **pivotar** (hash → dominio → actor → víctimas), generar IOCs y priorizar respuesta rápida.

---

[🔼](#índice)

---

## **346. El modelo de diamante para la detección de intrusiones**

### Resumen rápido

El **Modelo Diamante** (Adversario ↔ Capacidad ↔ Infraestructura ↔ Víctima) es un marco para **pensar** las intrusiones. Para detección, lo usamos para **correlacionar** telemetría (hashes, dominios, IPs, hosts, usuarios) y priorizar alertas que representen la intersección de dos o más vértices (p. ej. `capability seen + infrastructure seen + victim affected = alto riesgo`).

### 1) Mapeo conceptual: vértices → tipos de telemetría

- **Adversario** → indicadores CTI: actor name, campaign tags, TTPs, reputación (feed TI).
  _Telemetría:_ Threat intel feeds, ATT\&CK technique mappings, tagging en SIEM.
- **Capacidad** → herramientas/malware/exploits/firmware.
  _Telemetría:_ file hashes (MD5/SHA\*), process names, YARA matches, suspicious commands, loaded modules.
- **Infraestructura** → C2, dominios, subdominios, IPs, cloud accounts, certificados.
  _Telemetría:_ DNS logs, proxy/web, netflow, TLS fingerprints, WHOIS, passive DNS.
- **Víctima** → hosts/users/roles/servicios atacados.
  _Telemetría:_ endpoint telemetry (EDR), Windows logs (Sysmon), authentication logs, file server logs.

### 2) Principio operativo para detección

Crear **correlaciones** (reglas/hunts) que crucen vértices. Ejemplos:

- Detectar cuando una **capability** (hash) conocida conecta a infraestructura maliciosa y ese tráfico proviene de un **host crítico** → ALERTA ALTA.
- Buscar cuando un host (víctima) ejecuta un binario firmado por entidad desconocida mientras hace DNS a dominio sospechoso → HUNT.

Clave: **enriquecer** cada evento con CTI (reputación IP/domain/hash) y etiquetar sus vértices.

### 3) Patrones de detección útiles (plantillas conceptuales)

1. **Capacidad + Infraestructura**: archivo malicious (hash) realizó conexión saliente a dominio C2.
2. **Infraestructura + Víctima**: host crítico realizó resolución DNS a dominio nuevo con TTL bajo + conexiones inusuales.
3. **Capacidad + Víctima**: proceso inusual (powershell w/ encoded command) que crea un proceso hijo `rundll32` o `schtasks`.
4. **Adversario + Infraestructura**: indicador CTI (campaign tag) + infraestructura observada en red → aumentar auditoría/hunt scope.
5. **Cadena completa**: actor A (CTI) → C2 domains D → malware hash H → host X → exfil pattern → respuesta.

### 4) Ejemplos concretos: reglas y hunts

#### A) Sigma (regla genérica)

Sigma es formato para reglas portables. Ejemplo: detectar ejecución de PowerShell con comandos base64 _y_ DNS a dominio con mala reputación (conceptual):

```yaml
title: Suspicious Powershell Exec with Malicious DNS
id: 11111111-1111-1111-1111-111111111111
status: experimental
description: Detect PowerShell Base64 execution followed by DNS to known-malicious domain within 2 minutes.
author: Gustavo
logsource:
  product: windows
  service: sysmon
detection:
  selection_powershell:
    EventID: 1
    CommandLine|contains:
      - "-EncodedCommand"
  selection_dns:
    EventID: 22 # Sysmon DNS query or DNS server logs equivalent
    QueryName|contains:
      - "updates-svc." # example malicious domain fragment
  timeframe: 2m
  condition: selection_powershell and selection_dns
falsepositives:
  - Admin scripts that call remote services (validate whitelist)
level: high
```

(En Sigma, mapearías fields específicos según tus fuentes.)

#### B) Splunk (correlation search)

Correlate process creation with DNS to suspicious domain within 2 minutes:

```spl
index=wineventlog OR index=sysmon
| eval is_ps=if(ProcessName="C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe" AND like(CommandLine,"%EncodedCommand%"),1,0)
| transaction host maxspan=2m startswith="is_ps=1" endswith="EventCode=22" keepevicted=true
| search is_ps=1 EventCode=22 QueryName="*updates-svc.*"
| table _time host user CommandLine QueryName
```

(Adaptar `EventCode` y campos a tu esquema.)

#### C) Elastic/KQL hunting

Hunt: hosts resolving new domains + suspicious user agent + outbound bytes > threshold:

```kql
// DNS logs + Network logs correlation within 5 minutes
event.dataset:dns and dns.question.name: "*updates-svc.*"
| join
  (event.dataset:network and network.direction:outbound and network.bytes_out > 100000)
  on host.name
| where host.os.type == "windows" and user.name != "SYSTEM"
```

#### D) Neo4j / Cypher (graph pivot)

Crear nodos y buscar cadenas que unan hash→domain→hosts:

```cypher
// create example nodes (one-off)
MERGE (h:Hash {value:"sha256:abc123"})
MERGE (d:Domain {name:"updates-svc.com"})
MERGE (host:Host {name:"filesrv01.corp.local"})
MERGE (h)-[:CONTACTED_BY]->(d)
MERGE (host)-[:RESOLVED]->(d)
// query: find hosts connected to domain for known bad hash
MATCH (h:Hash)-[:CONTACTED_BY]->(d:Domain)<-[:RESOLVED]-(host:Host)
WHERE h.value = "sha256:abc123"
RETURN host.name, d.name, h.value
```

### 5) Caso práctico paso a paso (detección + respuesta usando Diamond)

#### Escenario breve

- Evento inicial: EDR alerta ejecución de `sysupdater.exe` (hash H) en host `filesrv01`.
- Telemetría adicional: DNS logs muestran resolución a `updates-svc.com` (domain D). VirusTotal marca H as malicious. Host es servidor HR (alta prioridad).

#### Aplicación Diamond

1. **Capacidad** = `sysupdater.exe` (hash H).
2. **Infraestructura** = `updates-svc.com` (domain D).
3. **Víctima** = `filesrv01` (host crítico).
4. **Adversario** = _UNKNOWN_ initially; enriched with CTI may map to APT-X later.

#### Detección en SIEM

- Regla correlation: `file.hash == H` **AND** `dns.query == D` within 10 minutes on same host → create high-priority incident.

#### Respuesta inmediata (playbook)

- Isolate `filesrv01` from network (prevent exfil).
- Pull memory image / EDR artifact collection.
- Dump network connections and files created by process.
- Pivot CTI: lookup D & H across environment to find lateral movement.
- Triage: identify scope, rotate credentials, patch vector.

### 6) Cómo integrar ATT\&CK y CTI con Diamond para mejores detecciones

- Mapea **Capacidad** a ATT\&CK techniques (p. ej. `T1059` PowerShell, `T1566` Phishing).
- Mapea **Infraestructura** a `T1105` (Exfil via C2), `T1071` (Application layer protocol).
- Usa tags CTI (actor, campaign) para marcar **Adversary** y ampliar hunting (buscar infra reuse).
- En la lógica de reglas, eleva prioridad si la técnica observada corresponde a ATT\&CK techniques asociadas a ransomware/espionaje.

### 7) Priorización de alertas basada en Diamond

Asignar score combinando vértices y confianza:

- `score = weight_adversary*conf_adversary + weight_capability*conf_hash + weight_infra*reputation + weight_victim*criticality`
- Ejemplo: si `conf_hash=1 (VT malicious)`, `reputation(domain)=0.9`, `host criticality=0.8` → alerta crítica.

Esto evita inundación de falsos positivos: un hash malicioso en laptop no crítico podría recibir menos prioridad que el mismo hash en domain controller.

### 8) Operationalización: pipelines y enriquecimiento

- **Ingest**: centraliza logs (EDR, DNS, Proxy, Sysmon, Netflow).
- **Enrichment**: on ingest add CTI lookups (VT, abuseIPDB, passive DNS, WHOIS, ASN). Tag events with diamond labels: `event.vertex.capability`, `event.vertex.infra`, `event.vertex.adversary_tag`, `event.vertex.victim_asset`.
- **Correlation engine**: implement rules that require >=2 vertices: e.g., `capability ∧ infra` OR `infra ∧ victim`.
- **Graph store**: mirror events into graph DB (Neo4j, Amazon Neptune) for pivoting.
- **Hunting notebooks**: Jupyter/ELK notebooks to search patterns linking diamonds across time windows.

### 9) Detecciones prácticas adicionales (hunting recipes)

- Hunt: **new domain + short TTL + host with process spawn**:

  - Query DNS logs for new domains seen first time in last 7 days and TTL < 300 → then join to hosts resolving them → check process creation logs.

- Hunt: **rarely seen binary executed on many hosts**:

  - Count hosts where `file.hash` executed in last 30 days — if > threshold and hosts across departments → investigate lateral movement.

- Hunt: **certificate reuse across tenants**:

  - Find same TLS cert fingerprint used by different hosts or domains — potential misuse/impersonation.

### 10) Limitaciones prácticas y contramedidas

- **Falta de telemetría**: sin DNS/EDR algunos vértices no aparecen → mejorar cobertura.
- **CTI ruidoso/false positives**: reputaciones cambian → usar TTL/confidence on CTI.
- **Attribution pitfalls**: iguales infra no implica mismo adversary (use confidence labels).
- **Escalabilidad**: correlaciones complejas requieren graph DB / stream processing (Kafka + Flink) para performance.

Contramedidas:

- Establecer **confidence scoring** y expiración de CTI.
- Implementar **automated enrichment** y **rate limiting** en reglas para reducir false positives.
- Mantener pipeline de detección versionado y pruebas.

### 11) Métricas y KPIs (útil para SOC)

- Detections by diamond pattern (counts of `capability+infra`, `infra+victim`, etc.).
- Mean Time to Detect (MTTD) when diamond full chain observed vs partial.
- % alerts with CTI enrichment.
- False positive rate per diamond rule.
- Coverage: % hosts with EDR, % DNS logs captured, % TLS inspected.

### 12) Ejemplo visual breve (ASCII diamond)

```
        [Adversary: APT-RED]
           /            \
[Capability: sysupdater]  [Victim: filesrv01]
           \            /
     [Infra: updates-svc.com (198.51.100.22)]
```

Detector ideal dispara cuando se observan 2+ aristas con alta confianza (p.ej. Capability↔Infra y Infra↔Victim).

### 13) Recomendaciones finales — checklist práctico

- Instrumenta **telemetría**: EDR (process/file), DNS, Proxy, Netflow, Auth logs, TLS.
- Automatiza **enriquecimiento CTI** y etiqueta eventos por vértice.
- Implementa reglas de correlación que crucen vértices (≥2) y prioricen por criticality.
- Usa **graph DB** para pivoting y visualización.
- Mapea detecciones a **ATT\&CK** y documenta playbooks de respuesta por patrón diamond.
- Evalúa y tunea reglas con métricas y ejercicios de threat hunting.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                 | **Siguiente 8**                  |
| ------------------ | --------------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./11_6_Obfuscation.md) | [⏩](./11_8_Cyber_Kill_Chain.md) |
