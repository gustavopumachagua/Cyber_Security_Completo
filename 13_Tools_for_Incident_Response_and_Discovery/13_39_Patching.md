| **Inicio**         | **atrás 38**          | **Siguiente 40**             |
| ------------------ | --------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./13_38_ACLs.md) | [⏩](./13_40_Jump_Server.md) |

---

## **Índice**

| Temario                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------ |
| [486. ¿Qué es la gestión de parches?](#486-qué-es-la-gestión-de-parches)                                                                   |
| [487. ¿Qué es la gestión de parches y por qué la necesita su empresa?](#487-qué-es-la-gestión-de-parches-y-por-qué-la-necesita-su-empresa) |

# **Patching**

## **486. ¿Qué es la gestión de parches?**

![Patching](/img/13_Tools_for_Incident_Response_and_Discovery/Patching.png "Patching")

La **gestión de parches** es el proceso organizado y repetible de **identificar, probar, desplegar y verificar** correcciones (parches) para software, sistemas operativos, firmware y aplicaciones en una organización. El objetivo es mantener los sistemas **segros, estables y actualizados**, reduciendo la exposición a vulnerabilidades y errores.

### ✅ ¿Por qué es importante la gestión de parches?

Porque sin parches:

- Las vulnerabilidades conocidas permanecen explotables (ataques RCE, privilege escalation, etc.).
- Se elevan riesgos de incumplimiento normativo (auditorías que exigen parches).
- Los errores y falta de nuevas funciones afectan la disponibilidad, rendimiento y experiencia.
- La deuda técnica crece y es más costoso arreglar a posteriori.

Ejemplo real: muchos ransomwares explotan vulnerabilidades conocidas para las que ya existía parche; organizaciones sin proceso de gestión de parches quedan expuestas.

### Tipos de actualizaciones (qué cubre la gestión de parches)

#### 1. **Actualizaciones de seguridad (Security patches)**

Corrigen vulnerabilidades que permiten ataques (por ejemplo CVE con exploit público). Son **prioritarias**.

**Ejemplo**: parche de Windows para CVE crítico que corrige una ejecución remota de código.

#### 2. **Actualizaciones de funciones (Feature updates)**

Agregan o cambian funcionalidades (a menudo programadas, p. ej. Windows Feature Updates).

**Ejemplo**: nueva versión de Office con funcionalidad colaborativa.

#### 3. **Corrección de errores (Bug fixes / hotfixes)**

Arreglan fallos, cuelgues o pérdidas de datos; pueden ser críticos para la estabilidad.

**Ejemplo**: parche que corrige memory leak en un servicio de base de datos.

### Objetivos prácticos que persigue el proceso

- **Minimizar ventanas de exposición** a vulnerabilidades.
- **Mantener uptime** y disponibilidad con cambios controlados.
- **Cumplir** con políticas, SLA y normas regulatorias.
- **Mantener compatibilidad** y rendimiento.
- **Registrar evidencia** para auditoría.

### ✅ Cómo minimizar el tiempo de inactividad (prácticas y técnicas)

- **Mantenimiento planificado:** ventanas aprobadas y comunicadas (ej.: sábados 02:00–05:00).
- **Faseado / despliegue escalonado:** canary → pilot → roll-out general (evita impactar toda la flota).
- **Rolling updates:** actualizar nodos uno a uno en un clúster (sin downtime).
- **Blue-green / red-black deploy:** mantener entorno paralelo y conmutar tráfico tras verificación.
- **Snapshots / backups previos:** instantáneas de VM o backup de config para rollback rápido.
- **Live migration / HA:** migrar cargas antes de aplicar patch en el host.
- **Pruebas automatizadas (smoke tests):** scripts post-patch que verifican servicios críticos.
- **Mecanismos de rollback:** paquetes firmados y procedimientos claros para revertir (o reinstalar snapshot).
- **Ventanas de mantenimiento fuera de horario pico** y comunicación con stakeholders.

Ejemplo: actualizar un cluster de bases de datos PostgreSQL con rolling upgrades y snapshots en cada nodo; si el nodo 2 falla tras el patch, se restaura desde snapshot y se pausa el despliegue hasta investigar.

### ✅ Cumplimiento normativo (por qué piden parches)

Muchas normas exigen un proceso documentado y evidencia de parches oportunos:

- **PCI-DSS:** requiere mantener sistemas actualizados y monitorear parches de seguridad.
- **HIPAA:** exige protección de información de salud; parches son control de seguridad.
- **ISO 27001:** pide controles para gestión de vulnerabilidades y cambios.
- **GDPR/LGPD:** no parchear puede ser considerado negligencia en protección de datos.

En auditoría te pedirán inventario, fecha de aplicación de parches, excepciones aprobadas y pruebas de testing.

### 🔁 El ciclo de vida de la gestión de parches (paso a paso)

A continuación un flujo práctico (puedes incorporarlo a tu runbook):

1. **Inventario (Asset discovery)**

   - Inventariar hardware, OS, aplicaciones, versiones y firmware (CMDB).
   - Herramientas: SCCM, Lansweeper, CMDB, agentes EDR.

2. **Detección y evaluación**

   - Alimentar con fuentes de vulnerabilidades (NVD, advisories vendor, CVE feeds).
   - Correlacionar CVEs con activos para identificar exposición.

3. **Priorización / clasificación (Risk-based)**

   - Priorizar por: criticidad del CVE (CVSS), exploit known/PoC, exposición internet, valor del activo, compensating controls.
   - Establecer SLAs: por ejemplo, parche crítico con exploit → 72h; medium → 30 días.

4. **Pruebas en entorno controlado (Test / Staging)**

   - Instalar parche en entorno QA/staging que simule producción.
   - Ejecutar pruebas automatizadas y de regresión para identificar incompatibilidades.

5. **Planificación del despliegue**

   - Definir grupos (Pilot group, pilot size), ventanas de mantenimiento, comunicación y rollback plan.

6. **Despliegue (Deployment / Remediation)**

   - Automatizar despliegue (SCCM, WSUS, Intune, Ansible, Puppet, Chef, Jamf, apt/yum automation).
   - Hacerlo por oleadas y monitorizar.

7. **Verificación (Post-deploy validation)**

   - Verificar con health checks, logs, UAT, monitoreo APM.
   - Registrar resultados.

8. **Documentación y reporting**

   - Documentar qué se parcheó, cuándo, quién, evidencia de verificación y excepciones aprobadas.

9. **Rollback si es necesario**

   - Ejecutar rollback plan (snapshot restore, uninstall patch, revert package).
   - Analizar root cause antes de volver a intentar.

10. **Lessons Learned / mejora contínua**

- Actualizar playbooks y criterios de priorización.

(Visual mental: Inventory → Detect → Prioritize → Test → Deploy → Verify → Document → Learn)

### ✅ Soluciones y herramientas de gestión de parches (comerciales y open source)

Herramientas típicas — elige según plataforma:

#### Microsoft / Windows

- **WSUS** (Windows Server Update Services) — básico para Windows.
- **Microsoft Endpoint Configuration Manager** (SCCM / ConfigMgr) — enterprise.
- **Intune** (MDM) — para dispositivos gestionados y Windows 10/11.

#### Unix / Linux

- **apt / apt-cron / unattended-upgrades** (Debian/Ubuntu)
- **yum/dnf + yum-cron** (RHEL/CentOS/Fedora)
- **Spacewalk / Katello / Satellite (Red Hat)**
- **Canonical Landscape** (Ubuntu)

#### Multi-plataforma y enterprise

- **Ivanti (antes LANDESK / Shavlik)**
- **ManageEngine Patch Manager Plus**
- **PDQ Deploy** (Windows)
- **SolarWinds Patch Manager**
- **Qualys / Rapid7 / Tenable** — integran scanning y orquestación de remediación.
- **BigFix (HCL)** — gestión a escala.
- **Jamf** — gestión macOS/iOS.
- **Chocolatey + Boxstarter** — automatización en Windows.
- **Ansible / Puppet / Chef / Salt** — orquestación de parches (especialmente en Linux/Unix / infra-as-code).
- **AWS Systems Manager Patch Manager**, **Azure Update Management** — para nube.

#### Vulnerability Management + Patching

- **Qualys, Tenable.sc/IO, Rapid7 InsightVM** detectan vulnerabilidades y pueden integrarse con herramientas de parcheo para priorizar.

### ✅ Buenas prácticas / recomendaciones prácticas

- **Política de parches formal**: SLAs por criticidad, ventanas, excepciones, roles/responsabilidades.
- **Risk-based patching**: prioriza activos críticos y vectores expuestos.
- **Despliegues por oleadas**: pilot → staged → full-rollout.
- **Automatiza** tanto discovery como despliegue donde sea seguro.
- **Backups y snapshots** antes de producción.
- **Rollback plan probado** (simula un rollback en staging).
- **Documenta excepciones** (si no puedes parchear, aplica mitigación compensatoria y documenta fecha objetivo).
- **Combina patching con compensating controls** (WAF, microsegmentación, EDR).
- **Comunicación**: notificar usuarios afectados y equipos de soporte.
- **Métricas**: patch compliance %, MTTD/MTTR, % systems up-to-date, average days-to-patch.
- **Integrar con CMDB y change control** (evitar cambios no autorizados).
- **Revisiones post-deploy** y KPI dashboards para auditoría.

### ✅ Ejemplos concretos (casos prácticos)

- **Windows Patch Tuesday**: Microsoft publica parches el segundo martes: proceso típico → test en pilot (50 equipos) → despliegue por OU vía SCCM/Intune → verificación → cierre.
- **Linux kernel critical**: kernel update que requiere reboot — se programa en ventana de mantenimiento, VMs migradas, snapshot y rolling reboot de hosts.
- **Aplicación web**: vendor declara RCE en Tomcat; se parchea primero en staging, se corrigen config incompatibles y se despliega fuera de horario pico; WAF bloquea exploit hasta despliegue.

### ✅ Manejo de emergencias: parches de emergencia (out-of-band)

- Proceso acelerado con aprobación rápida (CAB de emergencia): detectar, test mínimo, despliegue rápido a activos expuestos, monitorización intensiva.
- Comunicar status a stakeholders y documentar acciones en post-mortem.

### ✅ Métricas y KPI sugeridas

- **% devices compliant** (objetivo > 95%).
- **Time-to-patch mean** (por severidad).
- **Number of exceptions open** y tiempo de resolución.
- **Incidentes relacionados con vulnerabilidades parcheables**.
- **Success rate of deployments** (pilot vs production).

### ✅ Riesgos y limitaciones

- Parches pueden **romper compatibilidades** o causar regresiones.
- En entornos legacy, algunos parches no son viables → se requiere mitigaciones.
- Automatización sin testing puede causar outages masivos.
- Falta de inventario actualizado lleva a lagunas.

### ✅ Control y gobernanza (qué incluir en la política)

- Roles (Owner, Patch Manager, Change Approver).
- SLAs (72h, 30d, 90d según criticidad).
- Proceso de prueba y rollback.
- Excepciones: autorización y controles compensatorios.
- Reporting y auditoría.

---

[🔼](#índice)

---

## **487. ¿Qué es la gestión de parches y por qué la necesita su empresa?**

### 1) Definición breve

La **gestión de parches** es el proceso sistemático de **identificar, evaluar, probar, desplegar y verificar** actualizaciones (parches) para sistemas operativos, aplicaciones, firmware y dispositivos de red con el fin de corregir vulnerabilidades, fallos o añadir mejoras funcionales.

### 2) ¿Por qué su empresa la necesita? (beneficios clave)

1. **Reducción de riesgo de seguridad** — los parches corrigen vulnerabilidades explotables (prevenir RCE, privilege escalation, etc.).
2. **Disponibilidad y estabilidad** — corrigen bugs que causan cuelgues, memory leaks o pérdida de datos.
3. **Cumplimiento normativo** — muchas normas (PCI-DSS, HIPAA, ISO 27001) exigen evidencias de parcheo.
4. **Mejor rendimiento y nuevas funcionalidades** — algunos parches optimizan rendimiento o añaden capacidades.
5. **Menor coste a largo plazo** — reparar tras un incidente suele costar mucho más que aplicar parches a tiempo.

### 3) Casos reales (por qué importan los parches)

- **WannaCry (mayo 2017)**: ransomware que explotó la vulnerabilidad SMB `CVE-2017-0144` (EternalBlue). Microsoft había publicado parche (MS17-010) semanas antes; organizaciones sin parche quedaron comprometidas y muchas tuvieron pérdidas graves.
- **Breach de Equifax (2017)**: explotaron una vulnerabilidad en **Apache Struts** (CVE publicado) en sistemas no parcheados — filtración de datos personales de decenas de millones de personas.

  Estos ejemplos muestran que **un parche disponible y no aplicado** puede derivar en brechas masivas.

### 4) Tipos de actualizaciones

- **Parches de seguridad** (prioridad máxima).
- **Actualizaciones de funciones** (features, mejoras).
- **Hotfixes/bugfixes** (corrigen fallos específicos).
- **Firmware / BIOS / microcódigo** (para hardware: routers, switches, servidores).

### 5) Ciclo de vida (pasos operativos — plantilla práctica)

1. **Inventario / discovery**

   - Mantenga CMDB con SO, versión de software, firmware, roles de servicio y exposición (internet-facing?).

2. **Detección / feed de vulnerabilidades**

   - Fuentes: NVD, advisories vendors, CVE feeds. Mantenga integraciones con scanner (Tenable, Qualys, Rapid7).

3. **Evaluación / priorización**

   - Priorice por: CVSS, exploit disponible, exposición (internet-facing), criticidad del activo (prod, dev), impacto negocio.

4. **Pruebas en entorno controlado**

   - Canary → Piloto → Staging. Ejecutar suites de smoke/regresión.

5. **Planificación del despliegue**

   - Ventanas de mantenimiento, rollback plan, backups/snapshots.

6. **Despliegue (orquestado)**

   - Por oleadas (canary → 10% → 50% → 100%). Herramientas de automatización.

7. **Verificación post-deploy**

   - Health checks, monitoreo, logs, alertas.

8. **Documentación / evidencias**

   - Qué se parchó, cuándo, por quién, resultado, tickets.

9. **Rollback si es necesario**

   - Procedimiento pre-probado (snapshot restore, uninstall).

10. **Lecciones aprendidas**

- Actualizar runbooks, ajustar SLAs.

### 6) Priorización: cómo decidir qué parchear primero

Use una matriz de riesgo que combine:

- **Severidad del CVE (CVSS)** — ejemplo: CVSS ≥ 9 = crítico.
- **Existencia de exploit público / PoC** (si hay exploit público subir prioridad).
- **Exposición** — servicio público (Internet-facing) o solo interno.
- **Valor del activo** — servidor de pagos > estación de trabajo de oficina.
- **Mitigaciones alternativas** — si hay WAF/IDS que mitigan temporalmente.

**SLA sugerido (ejemplo práctico)**

- Crítico (exploit activo / internet-facing): **72 horas**.
- Alto: **7 días**.
- Medio: **30 días**.
- Bajo / features: **90+ días** (según gobernanza).

(Ajuste SLAs según su riesgo de negocio).

### 7) Controles técnicos y herramientas recomendadas

- **Windows**: WSUS (básico), Microsoft Endpoint Configuration Manager (SCCM), Intune.
- **Linux**: unattended-upgrades (Debian/Ubuntu), yum/dnf automation, Canonical Landscape, Red Hat Satellite.
- **Cross-platform enterprise**: BigFix (HCL), ManageEngine Patch Manager Plus, Ivanti, PDQ Deploy.
- **Infraestructura as code / Orquestación**: Ansible, Puppet, Chef para despliegue y validación.
- **Nube**: AWS Systems Manager Patch Manager, Azure Update Management.
- **Vulnerability scanners**: Qualys, Tenable, Rapid7 (para priorizar).
- **Endpoint / EDR**: integración EDR para detectar actividad durante despliegue.

### 8) Cómo minimizar interrupciones (prácticas operativas)

- **Canary & staged rollouts**: parchear primero un subconjunto (pilot).
- **Snapshots y backups** antes del deploy (VM snapshots, DB dumps).
- **Rolling updates** en clústeres (no parar todo a la vez).
- **Blue/green deployments** para servicios web críticos cuando sea posible.
- **Smoke tests / monitoring** automáticos post-patch.
- **Comunicación** con stakeholders y ventanas planificadas.

### 9) Si no puede parchear (mitigaciones compensatorias)

Cuando un sistema legacy o vendor lock impide parchear:

- **Aísle** el activo (VLAN, ACL, microsegmentación).
- **Web Application Firewall (WAF)** para mitigar vulnerabilidades web conocidas.
- **Restricción de acceso** (solo IPs de gestión).
- **EDR + monitorización** para detectar comportamiento anómalo.
- **Plan de modernización** con plazos y recursos asignados.

### 10) Métricas y KPIs recomendados

- **% de dispositivos compliant** (objetivo > 95%).
- **Mean Time to Patch (MTP)** por severidad.
- **Número de excepciones abiertas** y tiempo de resolución.
- **% de despliegues exitosos (sin rollback)**.
- **Incidentes causados por vulnerabilidades parcheables** (ideal = 0).

### 11) Política mínima que debe tener su empresa (extracto práctico)

- Inventario obligatorio y actualizado.
- Parches de seguridad críticos aplicados en ≤72 horas, a menos que exista excepción aprobada.
- Entorno de pruebas obligatorio antes de producción.
- Rollback y backup obligatorio antes de despliegue.
- Documentación de cada despliegue y auditoría trimestral.

(Puedo generar una **Política formal** lista para incorporar a su manual de seguridad si quieres.)

### 12) Ejemplo de playbook rápido (checklist operativo)

**Antes del deploy**

- [ ] Identificar parche y CVE.
- [ ] Evaluar exposición y priorizar.
- [ ] Crear ticket y notificar stakeholders.
- [ ] Backup/snapshot.
- [ ] Probar en Pilot.
      **Durante deploy**
- [ ] Aplicar en Pilot.
- [ ] Validar smoke tests.
- [ ] Escalar a oleada 1.
      **Después**
- [ ] Verificar logs y rendimiento.
- [ ] Confirmar cierre de ticket.
- [ ] Actualizar CMDB.

### 13) Riesgos de no aplicar parches (resumen)

- Exposición a ransomware y malware automatizado.
- Brechas de datos y multas por incumplimiento.
- Coste de recuperación (forense, comunicaciones, demanda).
- Daño reputacional y pérdida de confianza de clientes.

### 14) Primeros pasos prácticos para su empresa (roadmap 30/60/90 días)

- **30 días**: inventario completo; configurar feeds CVE; definir SLAs; aplicar parches críticos pendientes en pilot.
- **60 días**: establecer automatización (WSUS/Intune/Ansible); ejecutar pilot canary; crear playbooks de rollback.
- **90+ días**: despliegue escalonado en producción; dashboards KPI; integrar scanner-vulnerability → orquestador de parcheo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 38**          | **Siguiente 40**             |
| ------------------ | --------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./13_38_ACLs.md) | [⏩](./13_40_Jump_Server.md) |
