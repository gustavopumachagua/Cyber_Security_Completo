| **Inicio**         | **atrás 26**                                          | **Siguiente 28**                              |
| ------------------ | ----------------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_26_Basics_and_Concepts_of_Threat_Hunting.md) | [⏩](./8_28_Basics_of_Reverse_Engineering.md) |

---

## **Índice**

| Temario                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------- |
| [255. ¿Qué es la gestión de vulnerabilidades? - Rapid7](#255-qué-es-la-gestión-de-vulnerabilidades---rapid7)           |
| [256. ¿Qué es la gestión de vulnerabilidades? - CrowdStrike](#256-qué-es-la-gestión-de-vulnerabilidades---crowdstrike) |
| [257. Gestión de vulnerabilidad explicada por expertos](#257-gestión-de-vulnerabilidad-explicada-por-expertos)         |

# **Conceptos básicos de la gestión de vulnerabilidades**

## **255. ¿Qué es la gestión de vulnerabilidades? - Rapid7**

![gestión de vulnerabilidades](/img/8_Security_Skills_and_Knowledge/gestión_de_vulnerabilidades.jpg "gestión de vulnerabilidades")

La **gestión de vulnerabilidades** es un proceso continuo que permite a las organizaciones **identificar, evaluar, priorizar y remediar vulnerabilidades** en sistemas, redes, aplicaciones y dispositivos.

👉 Una vulnerabilidad es una **debilidad en un sistema** que puede ser explotada por un atacante (ejemplo: un puerto abierto sin control, un software sin parchear, una mala configuración en la nube).

**Ejemplo:**

- Una empresa detecta que su servidor web usa **Apache con una versión vulnerable** (CVE-2021-41773).
- La gestión de vulnerabilidades implica:

  1. Detectar el fallo.
  2. Evaluar el riesgo.
  3. Aplicar el parche o mitigación.
  4. Verificar que se corrigió.

### 📊 ¿Qué es la gestión de vulnerabilidades basada en riesgos (RBVM)?

La **RBVM (Risk-Based Vulnerability Management)** es una evolución de la gestión tradicional:

- En lugar de corregir todas las vulnerabilidades (lo cual es imposible en entornos grandes), se enfoca en **las que representan mayor riesgo real para el negocio**.
- Usa **contexto de amenazas** y **criticidad de activos** para decidir qué vulnerabilidad atender primero.

**Ejemplo práctico:**

- Dos vulnerabilidades encontradas en un escaneo:

  1. **CVE-2020-1472 (Zerologon)** en un **controlador de dominio** crítico.
  2. Una vulnerabilidad media en un servidor de pruebas desconectado de la red.

- Aunque ambas aparecen en el informe, la **RBVM prioriza el Zerologon** porque afecta un sistema crítico y tiene exploits públicos activos.

### ⚖️ Gestión de vulnerabilidades vs. Evaluación de vulnerabilidades

- **Evaluación de vulnerabilidades**: es un **escaneo puntual** que identifica fallos de seguridad en un momento dado.
- **Gestión de vulnerabilidades**: es un **ciclo continuo**, que incluye escaneo, priorización, remediación y verificación.

**Ejemplo:**

- Un **escaneo con Nessus** que detecta 200 vulnerabilidades = _evaluación_.
- El **ciclo mensual** donde el SOC revisa esas vulnerabilidades, aplica parches y monitorea la mejora = _gestión_.

### 🔄 Proceso de gestión de vulnerabilidades de 4 pasos

1. **Identificación**

   - Escaneos automáticos (Nessus, Qualys, OpenVAS, Rapid7).
   - Revisión de configuraciones (CIS Benchmarks).
   - Ejemplo: detectar que un servidor expone el puerto 3389 (RDP) sin protección.

2. **Evaluación**

   - Asignar severidad (CVSS), probabilidad de explotación (EPSS), contexto de negocio.
   - Ejemplo: RDP expuesto en un servidor crítico → riesgo alto.

3. **Tratamiento / Remediación**

   - Parcheo, deshabilitación de servicios, reconfiguración.
   - Ejemplo: cerrar el puerto 3389 o restringirlo con VPN.

4. **Monitoreo / Verificación**

   - Escaneo posterior para confirmar que la vulnerabilidad ya no existe.
   - Ejemplo: repetir el escaneo para verificar que el puerto está cerrado.

### 🤖 Automatización de la gestión de vulnerabilidades

En entornos grandes, la **automatización es clave**:

- **Escaneo programado** → detección periódica (ej. cada semana).
- **Integración con SIEM/SOAR** → creación automática de tickets.
- **Priorización automática** con IA (EPSS, inteligencia de amenazas).
- **Remediación automatizada** → despliegue de parches con herramientas como WSUS, Ansible, SCCM.

**Ejemplo:**

- Un escaneo detecta una vulnerabilidad crítica en Apache.
- El sistema genera un ticket en Jira.
- Un playbook en Ansible actualiza Apache automáticamente.
- Se ejecuta un escaneo de verificación.

### 🎯 Cómo aplicar la priorización basada en riesgos en la gestión de la exposición

La **gestión de la exposición** va más allá de vulnerabilidades: analiza todo el entorno (usuarios, aplicaciones SaaS, nube, IoT).
La priorización de riesgos considera:

1. **Criticidad del activo** → ¿es un servidor de producción o de pruebas?
2. **Probabilidad de explotación** → ¿hay exploit público o actividad en foros underground?
3. **Impacto en el negocio** → ¿afecta datos sensibles, disponibilidad de servicios críticos?
4. **Contexto del atacante** → ¿qué tácticas y herramientas están usando grupos activos (MITRE ATT\&CK)?

**Ejemplo práctico:**

- Una vulnerabilidad crítica en **un servidor expuesto a internet con base de datos de clientes** tiene prioridad 1.
- Una vulnerabilidad crítica en **un servidor aislado sin acceso externo** tiene prioridad baja.

✅ En resumen:

- La **gestión de vulnerabilidades** es continua, no un escaneo puntual.
- La **RBVM** permite optimizar recursos enfocándose en lo que realmente pone en riesgo al negocio.
- La **automatización y priorización por riesgo** son la clave para defender infraestructuras modernas.

---

[🔼](#índice)

---

## **256. ¿Qué es la gestión de vulnerabilidades? - CrowdStrike**

La **gestión de vulnerabilidades** es un proceso **continuo y sistemático** que busca **identificar, evaluar, priorizar, mitigar y monitorear** debilidades en sistemas, redes, aplicaciones y dispositivos antes de que los atacantes las exploten.

👉 No se trata de “hacer un escaneo y listo”, sino de un **ciclo constante** para reducir la superficie de ataque de la organización.

**Ejemplo práctico:**

Un servidor tiene un **servicio RDP expuesto en internet**. Un programa de gestión de vulnerabilidades lo detecta, asigna prioridad alta, y el equipo de TI lo mitiga cerrando el puerto o restringiéndolo con VPN.

### ⚖️ Diferencias entre vulnerabilidad, riesgo y amenaza

Es común confundirlos. Aquí va la diferencia clara:

- **Vulnerabilidad** → debilidad técnica o de configuración.

  _Ejemplo:_ Apache sin parchear (CVE-2021-41773).

- **Amenaza** → actor o evento que puede aprovechar una vulnerabilidad.

  _Ejemplo:_ un grupo de ransomware que explota esa falla en Apache.

- **Riesgo** → impacto potencial en la organización si la amenaza explota la vulnerabilidad.

  _Ejemplo:_ fuga de datos de clientes y pérdida de reputación por el ataque.

👉 Fórmula simplificada:

**Riesgo = Amenaza × Vulnerabilidad × Impacto**

### 🏷️ ¿Cómo se clasifican y categorizan las vulnerabilidades?

Se suelen clasificar por:

1. **Severidad técnica (CVSS Score):**

   - Crítica (9.0–10.0) → fácilmente explotable, alto impacto.
   - Alta (7.0–8.9).
   - Media (4.0–6.9).
   - Baja (0.1–3.9).

2. **Tipo de vulnerabilidad:**

   - Software sin parchear.
   - Configuración insegura.
   - Fallo en permisos de acceso.
   - Vulnerabilidad en protocolos (ej. SMBv1, TLS 1.0).

3. **Categoría de CWE (Common Weakness Enumeration):**

   - CWE-79: Cross-Site Scripting (XSS).
   - CWE-89: SQL Injection.
   - CWE-200: Exposición de información sensible.

4. **Impacto en la seguridad (CIA triad):**

   - Confidencialidad.
   - Integridad.
   - Disponibilidad.

**Ejemplo:**

Una base de datos MySQL expuesta sin contraseña → vulnerabilidad crítica (alta probabilidad + impacto severo en confidencialidad).

### 🔍 Diferencia entre gestión de vulnerabilidades y evaluación de vulnerabilidades

- **Evaluación de vulnerabilidades:** es un escaneo puntual que detecta y lista debilidades.

  _Ejemplo:_ ejecutar Nessus y obtener un informe con 500 vulnerabilidades.

- **Gestión de vulnerabilidades:** es un **ciclo continuo** que incluye identificación, priorización, remediación y seguimiento.

  _Ejemplo:_ el SOC analiza las 500 vulnerabilidades, prioriza las críticas, aplica parches, valida y repite mensualmente.

### 🔄 El proceso de gestión de vulnerabilidades

El ciclo incluye:

1. **Identificación** → descubrimiento de activos y escaneo.
2. **Evaluación y priorización** → analizar criticidad con CVSS + contexto de negocio.
3. **Remediación** → aplicar parches, reconfigurar, segmentar.
4. **Verificación** → comprobar que las vulnerabilidades se corrigieron.
5. **Monitoreo continuo** → repetir el ciclo de forma periódica.

### 📌 Trabajo previo para un programa de gestión de vulnerabilidades

Antes de iniciar, se debe:

- **Inventariar activos** (servidores, endpoints, dispositivos de red, aplicaciones).
- **Clasificar los activos por criticidad** (servidores de producción > pruebas).
- **Definir responsabilidades** (seguridad, TI, compliance).
- **Definir umbrales de riesgo aceptables** (ejemplo: toda vulnerabilidad crítica debe mitigarse en <7 días).
- **Elegir herramientas de escaneo y gestión** (ejemplo: Nessus, Qualys, Rapid7, OpenVAS).

### 🛠️ Los 5 pasos del ciclo de gestión de vulnerabilidades

1. **Descubrimiento** → ¿qué tengo en mi red? (asset discovery).
2. **Escaneo** → identificar vulnerabilidades en sistemas y aplicaciones.
3. **Análisis y priorización** → aplicar contexto de riesgo (CVSS, EPSS, criticidad del activo).
4. **Remediación / mitigación** → aplicar parches, desactivar servicios, controles compensatorios.
5. **Validación y reporte** → confirmar que la corrección funcionó y reportar al negocio.

**Ejemplo:**

- Paso 1: se descubre un servidor olvidado en DMZ.
- Paso 2: el escaneo detecta que corre Tomcat vulnerable.
- Paso 3: se prioriza porque está expuesto a internet.
- Paso 4: se parchea o se da de baja.
- Paso 5: el nuevo escaneo confirma que ya no hay vulnerabilidad.

#### ✅ Soluciones de gestión de vulnerabilidades: qué buscar

Una buena solución debería ofrecer:

1. **Descubrimiento de activos** → inventario automático (on-premise y cloud).
2. **Escaneo completo y flexible** → SO, redes, bases de datos, aplicaciones web.
3. **Priorización basada en riesgos** → CVSS + exploitabilidad + contexto de negocio.
4. **Integración con SIEM/SOAR** → para automatizar respuestas y tickets.
5. **Remediación automatizada** → integración con patch management (WSUS, Ansible, SCCM).
6. **Reportes personalizables** → para equipos técnicos y ejecutivos.
7. **Cobertura híbrida** → soporte para entornos on-premise, cloud y contenedores.

**Ejemplos de soluciones populares:**

- **Nessus** (Tenable).
- **Qualys VMDR**.
- **Rapid7 InsightVM**.
- **OpenVAS** (open source).

📌 En resumen:

- La **gestión de vulnerabilidades** va más allá de escanear: es un ciclo continuo.
- La diferencia clave con la evaluación es la **gestión y seguimiento**.
- La clasificación depende de severidad, tipo y contexto de negocio.
- Un programa sólido requiere inventario, priorización de riesgos y automatización.

---

[🔼](#índice)

---

## **257. Gestión de vulnerabilidad explicada por expertos**

### 1. ¿Qué es la gestión de vulnerabilidades (MV)?

**Definición corta:** proceso continuo para **identificar → clasificar → priorizar → remediar → verificar** debilidades (vulnerabilidades) en activos tecnológicos con el objetivo de reducir el riesgo de explotación y el impacto en el negocio.

**Ámbito:** servidores, endpoints, red, aplicaciones (web/móvil), contenedores, infraestructura cloud, IaC, dependencias (SCA), dispositivos IoT, terceras partes.

### 2. Roles y gobernanza (quién hace qué)

- **CISO / Sponsor**: define política, SLA y financiación.
- **Vulnerability Manager / Program Owner**: opera el programa, coordina escaneos, priorización y reporting.
- **Asset Owner / Service Owner**: responsable de remediar en su dominio.
- **Patch Manager / Ops Team**: aplica parches/configuraciones.
- **SOC / Threat Intel**: aporta contexto de explotación y detección.
- **ITSM (Service Desk)**: gestiona tickets y excepciones.

Buen gobierno = políticas claras + responsabilidades (RACI) + cadencias.

### 3. Ciclo de vida de una vulnerabilidad (4–6 pasos)

1. **Descubrimiento / Inventario** — asset discovery + mantenido en CMDB.
2. **Detección** — escaneo (una o varias herramientas) + ingestión en plataforma de VM.
3. **Priorizar / Enriquecer** — CVSS, EPSS, CVE metadata, exploit availability, exposición (internet-facing), criticidad del activo, negocio.
4. **Remediación / Mitigación** — parche, configuración, microsegmentación, WAF rule, bloqueos.
5. **Verificación** — re-scan, control de cambios.
6. **Reporte / Cierre / Lessons Learned** — métricas, auditoría y excepciones documentadas.

### 4. Evaluación vs Gestión de vulnerabilidades

- **Evaluación (vulnerability assessment):** una toma puntual (scan) que devuelve hallazgos.
- **Gestión (vulnerability management):** programa continuo que **gestiona** esos hallazgos hasta mitigarlos y prorroga controlada.

### 5. Prioridad basada en riesgo (RBVM) — enfoque experto

#### Concepto

No puedes parchear todo a la vez. RBVM combina **impacto del activo** + **probabilidad de explotación** + **contexto operativo** para priorizar.

#### Factores habituales

- **Criticidad del activo (A)**: 1–10 según negocio (producción DB=10).
- **CVSS v3 score (C)**: 0–10 (se normaliza a 0–1).
- **Exposición (E)**: internet-facing (1) o interno (0.5) o aislado (0.1).
- **Exploit availability / EPSS (X)**: 0–1 (alta probabilidad → 1).
- **ThreatIntel (T)**: 0–1 (si hay actividad APT o exploit in the wild → 1).

#### Fórmula ejemplo (ponderada)

```
RiskScore = 0.40*A_norm + 0.20*C_norm + 0.15*E + 0.15*X + 0.10*T
```

Donde `A_norm = A/10`, `C_norm = CVSS/10`.

#### Ejemplo numérico

- Servidor DB producción → A = 9 → A_norm=0.9
- Vulnerabilidad CVSS = 9.8 → C_norm=0.98
- Exposición = internet-facing → E = 1
- EPSS (exploit published) X = 1
- ThreatIntel T = 1 (campaign active)

Calculamos:

- 0.40\*0.9 = 0.36
- 0.20\*0.98 = 0.196
- 0.15\*1 = 0.15
- 0.15\*1 = 0.15
- 0.10\*1 = 0.10
  **RiskScore = 0.36+0.196+0.15+0.15+0.10 = 0.956 → Prioridad: Crítica (remediar en 24–72h)**

**Buckets** (ejemplo):

- ≥0.8 → Crítico (SLA 24–72h)
- 0.6–0.79 → Alto (SLA 3–7 días)
- 0.4–0.59 → Medio (SLA 30 días)
- <0.4 → Bajo (SLA ciclo de parche regular)

> **Consejo experto:** añade ajuste manual por compensating controls (p. ej. WAF + IPS reducen prioridad).

### 6. Tipos de escaneo y cuándo usarlos

- **Autenticado (credentialed) scans**: mayor cobertura (recomendado en servidores/hosts críticos).
- **No autenticado (unauthenticated)**: discovery externo / red perimeter.
- **SAST/DAST**: para aplicaciones (codificación estática y pruebas dinámicas).
- **SCA (software composition analysis)**: dependencias OSS (CVE en libs).
- **Container/Registries scans**: en CI/CD (Trivy, Clair, Anchore).
- **IaC scanning**: detectar malas configuraciones en Terraform/CloudFormation (Checkov, tfsec).
- **Cloud-native assessment**: AWS Inspector, Azure Defender, GCP Security Command Center.
- **Pen testing / Red team**: validar impacto real en assets críticos (complemento no sustituto).

### 7. Automatización y orquestación (práctica)

Automatiza: escaneo → ingestión → priorización → ticketing → remediación → re-scan → cierre.

#### Flujo ejemplo (alto nivel)

1. **Scanner** (Qualys/Nessus/Inspector) ejecuta scan.
2. **VM Platform** ingiere findings y calcula RiskScore (algoritmo RBVM).
3. **SOAR** (Cortex XSOAR, Demisto) crea ticket en ServiceNow con playbook.
4. **Patch Tool** (Ansible/SCCM/WSUS/Jamf) aplica parche en ventana aprobada.
5. **Post-scan** automático verifica y actualiza ticket.
6. **If fail** → escalate to human (Vuln Manager).

### Ejemplo de playbook pseudo (Ansible + API)

```yaml
# Pseudo-ansible playbook triggered by SOAR for host list
- name: Patch hosts for CVE-2025-XXXX
  hosts: "{{ hosts_list }}"
  tasks:
    - name: download patch
      get_url:
        url: "{{ patch_url }}"
        dest: /tmp/patch.rpm
    - name: install patch
      yum:
        name: /tmp/patch.rpm
        state: present
    - name: reboot if required
      reboot:
        msg: "Reboot for patch CVE-2025-XXXX"
        reboot_timeout: 600
```

> SOAR ejecuta playbook tras validar ventana y aprobación.

### 8. Runbook de remediación (ejemplo — vulnerabilidad crítica en Linux)

**Objetivo:** parchear y verificar CVE crítico en servidor Prod.

1. **Notificación automática** (SOAR → ServiceNow ticket, assignee = Patch Team).
2. **Preparación**:

   - Snapshot / backup del host (VM snapshot).
   - Validar ventana de mantenimiento (si aplica).

3. **Remediación**:

   - `sudo yum update package` o desplegar package vía Ansible.
   - Ejecutar tests de sanity (app health endpoints).

4. **Verificación**:

   - Re-scan con scanner credentialed.
   - Validar que CVE desapareció; actualizar ticket.

5. **Rollback plan**: restore snapshot si tests fallan.
6. **Cierre**: documentar la acción, time-to-remediate y lessons learned.

### 9. Excepciones y controles compensatorios

Si no puedes parchear (legacy, vendor constraint), documenta mediante **Exception Workflow**:

- Business risk acceptance form (explicita duración/timebox).
- Compensating controls: segmentación, WAF rule, IPS signature, MFA, limitación de accesos.
- Reassess periodicamente (30/60 días).

### 10. Métricas (KPIs) que importa medir

**Técnicas**

- **MTTR (Mean Time to Remediate)** por severidad (objetivo: Crit < 7 días).
- **MTTD (Mean Time to Detect)** para vulnerabilidades explotadas detectadas.
- **% de activos con escaneo reciente** (target >95%).
- **% de vulnerabilidades críticas remediadas SLA** (target 100% within SLA).
- **Dwell time** (cuando hay exploit activo) — reducirlo.
- **Vulnerabilities trend** (open vs closed over time).
- **False positive rate** (mejorar tuning de scanners).

**Ejecutivo**

- **Risk posture score** (normalizado 0–100).
- **Top 10 risks by business unit**.
- **Cost avoidance estimate** (estimación de impacto mitigado).

### 11. Reportes — qué entregar y a quién

- **Executive summary (CISO/Board)**: posture, top 5 risks, trend, actions needed. 1 página.
- **Operational report (IT/SecOps)**: listados por prioridad, tickets abiertos, SLAs.
- **Technical appendix**: evidence, steps applied, re-scan hashes, timestamps.

**Ejemplo de Executive slide**:

- Top 3 vulnerabilidades críticas (host, CVE, business impact)
- % Críticos remedied last 7 days
- Next actions & blockers

### 12. Integraciones imprescindibles (ecosistema)

- **CMDB / Asset Inventory** (ServiceNow CMDB, CMDB-lite) — base para criticidad.
- **Scanner(s)** (Qualys, Tenable/Nessus, Rapid7, OpenVAS).
- **SCA / DevSecOps** (Snyk, Dependabot, GitHub Advanced Security).
- **Container / Image scanners** (Trivy, Clair, Anchore).
- **IaC scanners** (Checkov, tfsec).
- **Patch management** (SCCM, WSUS, Jamf, Ansible).
- **ITSM** (ServiceNow, Jira) — tickets y excepciones.
- **SOAR** (automación y playbooks).
- **Threat Intel / Feeds** (MISP, AlienVault OTX, commercial feeds).
- **EDR/XDR** (CrowdStrike, SentinelOne) — detección complementaria y mitigaciones.

### 13. Seguridad en pipelines y cloud (prácticas modernas)

- **Shift-left**: integrar SCA/IaC/container scanning en CI/CD (reject builds with high severity).
- **SBOM**: crear y almacenar SBOM (SPDX/CycloneDX) para rastrear dependencias. Herramientas: Syft, CycloneDX.
- **Cloud posture**: continuous scanning (Inspector, Security Hub) y infra-as-code checks.
- **Ephemeral workloads**: escaneo en build time + runtime monitoring (EDR, runtime protection).

### 14. Errores comunes (y cómo evitarlos)

1. **No tener inventario fiable** → priorizar discovery y reconciliación CMDB.
2. **Solo escanear sin priorizar** → adoptar RBVM.
3. **No usar credentialed scans** → pierdes vulnerabilidades internas.
4. **No medir SLAs ni progresos** → no hay mejora continua.
5. **No coordinar con Dev/OPS** → parches no aplicados por riesgo de ruptura; usar staging + blue-green.
6. **Tolerar excepciones indefinidas** → timebox y controles compensatorios.

### 15. Maturity model (4 niveles) — ¿dónde estás y qué seguir?

- **Nivel 0 – Ad-hoc**: escaneos esporádicos, sin CMDB ni métricas. → Prioridad: inventario + política.
- **Nivel 1 – Básico**: escaneos regulares, tickets manuales. → Prioridad: automatización básica + SLAs.
- **Nivel 2 – Gestionado**: RBVM, integraciones ITSM, playbooks SOAR. → Prioridad: shift-left + SCA.
- **Nivel 3 – Optimizado**: pipeline-secure, SBOM, full orchestration, métricas de negocio. → Prioridad: continuous improvement & red-team validation.

### 16. Herramientas recomendadas (categorías + ejemplos)

- **Scanners de vulnerabilidades:** Tenable.io / Nessus, Qualys VMDR, Rapid7 InsightVM, OpenVAS (OSS).
- **Orquestación / SOAR:** Palo Alto Cortex XSOAR, Splunk SOAR (Phantom), Swimlane.
- **ITSM / CMDB:** ServiceNow, Jira Service Management.
- **Patch mgmt / Config mgmt:** SCCM, WSUS, Jamf, Ansible, SaltStack.
- **EDR / XDR:** CrowdStrike Falcon, SentinelOne, Microsoft Defender for Endpoint.
- **SCA / DevSecOps:** Snyk, GitHub Dependabot, WhiteSource.
- **Container & IaC scanning:** Trivy, Clair, Anchore, Checkov, tfsec.
- **SBOM & software supply chain:** Syft, CycloneDX, ORT.
- **Threat Intel / Sharing:** MISP, VirusTotal.

### 17. Plantillas prácticas (listas y ejemplos listos para usar)

#### 17.1 SLA / Policy (ejemplo rápido)

- **Critical (RiskScore ≥ 0.8):** remediar en **≤ 72 horas**; must create mitigation immediately if cannot patch.
- **High (0.6–0.79):** remediar en **≤ 14 días**.
- **Medium (0.4–0.59):** remediar en **≤ 30 días**.
- **Low (<0.4):** remediar según ciclo de mantenimiento (≤ 90 días).

#### 17.2 Ticket template (ServiceNow / Jira fields)

- Title: `CVE-YYYY-XXXX - [Host] - [Package] - Priority`
- Fields: AssetID, Owner, BusinessCriticality, CVSS, EPSS, ExploitAvailable (Y/N), Exposure, RiskScore, SLA due date, Remediation steps, Rollback plan, Snapshot/Backup yes/no, Attachments (evidence).

#### 17.3 Runbook (crítico) — checklist rápido

1. Snapshot/backup ✓
2. Notify owners & stakeholders ✓
3. Pull approved patch ✓
4. Deploy to staging ✓ (smoke tests)
5. Deploy to prod in window ✓
6. Post-deploy validation (health checks) ✓
7. Rescan & close ticket ✓

### 18. Caso práctico corto (hipotético)

**Contexto:** CVE-2025-XXXX en Apache con exploit público.

- Host: `web-prod-01` (DB behind, PCI environment).
- Aplicación critica: e-commerce.

**Acciones:**

1. RBVM calcula RiskScore = 0.92 → Crítico.
2. SOAR crea ticket, snapshots y apertura ventana inmediata.
3. Patch aplicado via Ansible, smoke test passed.
4. Re-scan confirma remedio; ticket cerrado y reporte para auditoría PCI.

### 19. Revisión final — checklist para arrancar o mejorar hoy mismo

- [ ] Inventario de activos actualizado en CMDB.
- [ ] Escaneo credentialed semanal + external weekly.
- [ ] Algoritmo RBVM en plataforma (o spreadsheet si eres pequeño).
- [ ] Integración scanner → SOAR → ITSM (tickets automáticos).
- [ ] Runbooks para remediación crítica con snapshots/rollback.
- [ ] KPIs definidos (MTTR por severidad, cobertura).
- [ ] Pipeline CI: SCA/IaC/container scanning en PRs.
- [ ] SBOM para componentes críticos.
- [ ] Revisión trimestral de excepciones y lecciones aprendidas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 26**                                          | **Siguiente 28**                              |
| ------------------ | ----------------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_26_Basics_and_Concepts_of_Threat_Hunting.md) | [⏩](./8_28_Basics_of_Reverse_Engineering.md) |
