| **Inicio**         | **atrás 51**                |
| ------------------ | --------------------------- |
| [🏠](../README.md) | [⏪](./13_51_Compliance.md) |

---

## **Índice**

| Temario                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------ |
| [512. ¿Quién tiene la responsabilidad última de la ciberseguridad?](#512-quién-tiene-la-responsabilidad-última-de-la-ciberseguridad) |
| [513. Ciberseguridad: una responsabilidad de la alta dirección](#513-ciberseguridad-una-responsabilidad-de-la-alta-dirección)        |

# **Management**

## **512. ¿Quién tiene la responsabilidad última de la ciberseguridad?**

![Management](/img/13_Tools_for_Incident_Response_and_Discovery/Management.webp "Management")

### 1) Comprender la responsabilidad compartida en la protección de su organización

La realidad es _colaborativa_. La ciberseguridad es una función transversal que necesita:

- **Board / CEO (responsabilidad última / accountability):**

  - Define el apetito de riesgo, aprueba políticas mayores, asigna recursos y garantiza cumplimiento legal.
  - Ejemplo: el Board aprueba la estrategia de riesgo cibernético y los presupuestos para SOC y seguro ciber.

- **CISO (responsabilidad operativa / dirección de seguridad):**

  - Diseña la estrategia de seguridad, prioriza controles, dirige IR, reporta al CEO/Board.
  - Ejemplo: el CISO presenta métricas trimestrales de MTTD/MTTR al Board y pide presupuesto para EDR.

- **CIO/CTO (responsabilidad tecnológica / implementación):**

  - Implementa tecnologías, coordina con equipos de infra/DevOps, gestiona proveedores cloud.
  - Ejemplo: el CIO despliega MFA y segmentación de red pedida por CISO.

- **Legal & Compliance:**

  - Interpreta obligaciones regulatorias, guías de notificación, contratos y cláusulas de proveedor.
  - Ejemplo: Legal confirma los plazos de notificación ante brechas (GDPR 72 h, por ejemplo).

- **RRHH:**

  - Políticas de contratación/desvinculación, formación y gestión de la amenaza interna.
  - Ejemplo: RRHH aplica controles en salida de empleados (revocar accesos, recuperar activos).

- **Operaciones / Negocio (Product Owners, Managers):**

  - Integran seguridad en requisitos de producto y definen prioridades del negocio.
  - Ejemplo: Product Owner acuerda despliegues canary con pruebas de seguridad obligatorias.

- **Equipos de seguridad (SOC, IR, Pentest, GRC):**

  - Detección, respuesta, pruebas y controles continuos.

- **Todos los empleados (línea de defensa 1):**

  - Cumplir políticas, reportar phishing, usar MFA y buenas prácticas.

> **Modelo mental**: responsabilidad _compartida_ + _rendición de cuentas_ jerárquica. El CEO/Board pagan las consecuencias legales y reputacionales si la organización falla.

### 2) Descubriendo conceptos erróneos comunes

- **“La seguridad es cosa del IT”** — FALSO. IT implementa, pero la política, riesgo y aceptación son de la dirección.
- **“Si tengo antivirus, estoy cubierto”** — FALSO. Controles múltiples, procesos y gobernanza importan tanto como herramientas.
- **“Cumplir = seguro”** — FALSO. El cumplimiento es mínimo; seguridad es gestión de riesgo activa.
- **“Yo no tengo nada que ocultar”** — FALSO. La confidencialidad, integridad y disponibilidad afectan operaciones, finanzas y reputación.
- **“Las responsabilidades están claras”** — A menudo FALSO: muchas organizaciones presentan solapamientos y huecos.

### 3) Pasar de la responsabilidad a la rendición de cuentas (de _responsible_ a _accountable_)

Tener responsabilidad en el organigrama no basta: hay que formalizar **rendición de cuentas**. Recomendaciones prácticas:

1. **Define RACI para actividades críticas** (ejemplo simplificado):

   - **R**esponsible: SOC / Infraestructura (ejecuta).
   - **A**ccountable: CISO / CIO (responde ante Board).
   - **C**onsulted: Legal, RRHH, Product.
   - **I**nformed: CEO, Managers, Empleados.

   Ejemplo: despliegue de MFA

   - R: Infra / SecOps — instalar MFA.
   - A: CISO — decidir estándar.
   - C: HR — plan de comunicación.
   - I: Toda la empresa — recibir notificación.

2. **Objetivos y KPIs claros**:

   - MTTD, MTTR, % de endpoints con EDR, % de parches críticos aplicados.
   - El Board debería revisar 3–5 KPIs que traduzcan riesgo.

3. **Ciclo de reportes formales**:

   - CISO reporta trimestralmente al Board y semanalmente al CEO sobre riesgos críticos.

4. **Asignación de presupuesto ligada a rendición**:

   - Recursos asignados con OKR/metricas que la dirección puede auditar.

5. **Contratos y responsabilidades externas**:

   - Proveedores con SLAs y cláusulas de notificación; la empresa mantiene la responsabilidad última por datos de clientes.

### 4) Navegando la ambigüedad — cómo clarificar roles cuando no está todo blanco o negro

Muchas organizaciones sufren “zonas grises” (cloud, OT, M\&A). Estrategias para manejar eso:

- **Mapeo de límites de autorización (Authorization Boundary)**: ¿qué sistemas entran en el ATO, quién los controla? Documentar explícitamente.

  - Ejemplo: nube pública: AWS X recursos gestionados por DevOps, pero seguridad de datos es responsabilidad del equipo de producto para cifrado y del CISO para polizas.

- **Playbooks y runbooks con flujos de decisión**:

  - Definir escalación: si incidente > X, escalar a Comité Ejecutivo; si involucra datos PII de clientes EU → notificar Legal.

- **Tabletop exercises y simulacros**:

  - Practicar escenarios (brecha, ransomware, insider) para identificar huecos en la cadena de decisión.

- **Delegación con límites**:

  - Delegar autoridad para decisiones operativas pero mantener “checkpoints” para decisiones estratégicas (p. ej. pagar rescate → decisión del CEO/Board con asesoría legal).

- **Documentar excepciones y acuerdos de nivel**:

  - Cuando una unidad necesita excepciones por urgencia, documentarlas y registrar compensating controls.

### 5) La amenaza interna: un enfoque de equipo para la ciberseguridad

Los “insider threats” son uno de los mayores riesgos porque combinan acceso legítimo y conocimiento. Abordarlo requiere coordinación entre Legal, HR, IT y Seguridad.

#### Tipos de amenazas internas

- **Maliciosas**: empleado descontento que roba datos (espionaje, exfiltración).
- **Negligentes**: malas prácticas (usar USBs, caer en phishing).
- **Comprometidas**: cuenta de empleado comprometida por un atacante externo.

#### Enfoque multidisciplinario — acciones concretas

1. **Prevención**

   - Principio de **least privilege** (mínimos privilegios).
   - Detección de anomalías (UEBA — User and Entity Behavior Analytics).
   - Políticas claras: uso aceptable de datos, sanciones, cláusulas contractuales.
   - Ejemplo: acceso a DBs sensibles mediante just-in-time (JIT) y revisión por ticket.

2. **Detección**

   - Monitoreo de DLP, logs de acceso, alertas de comportamiento (descargas masivas, accesos fuera de horario).
   - Correlación SOC + HR signals (un aumento de estrés o conflicto en HR puede correlacionarse con riesgo).

3. **Respuesta**

   - IR playbook específico para insiders (revocar accesos, forense, entrevistas).
   - Legal/RRHH involucrados para preservar evidencia y respetar derechos laborales.

4. **Remediación**

   - Revisar permisos, bloqueo de cuentas, revocar tokens y rotación de certificados/secretos.

5. **Cultura y formación**

   - Formación continua, canales de denuncia confidenciales, programas de bienestar para reducir riesgo de insiders maliciosos.

6. **Salida controlada**

   - Checklist de offboarding: revocar accesos, recuperar activos, cambiar contraseñas compartidas, remover claves SSH.

#### Ejemplo realista

- Un desarrollador con acceso a repositorios sensibles muestra comportamiento anómalo (descarga masiva fuera de horario). El SOC alerta, IT bloquea la cuenta temporalmente, HR coordina entrevista, Legal prepara preservación de evidencia y, si procede, acciones disciplinarias y remediación.

### 6) Plantilla práctica: mini-RACI para incidentes críticos

```
Actividad: Detección inicial de incidente severo
R: SOC / Security Analyst
A: CISO
C: IT / Forense / Legal / Communications
I: CEO / Board (si incidente >= impacto financiero X o datos clientes)
```

```
Actividad: Decisión de notificación regulatoria (GDPR, etc.)
R: Legal / CISO (prepara facts)
A: CEO (autoriza notificación)
C: DPO / Forense
I: Board
```

### 7) KPIs y métricas para que la dirección vea rendición de cuentas

- % de activos críticos con controles mínimos aplicados.
- MTTD (Mean Time To Detect) — objetivo: reducir año tras año.
- MTTR (Mean Time To Recover).
- % de empleados con formación anual completada.
- Tiempo medio de revocación de accesos tras offboarding.
- Número de incidentes reportados vs. acciones correctivas implementadas.

### 8) Recomendaciones de implementación — pasos accionables ahora

1. **Asegure que el Board y CEO están informados y firmen la política de riesgo cibernético.**
2. **Nomine un CISO con acceso directo al CEO y obligaciones claras.**
3. **Cree un RACI formal para 10 procesos críticos** (IR, parches, offboarding, provisión de accesos, contratación de proveedores).
4. **Ejecute tabletop exercises** con participación de Legal, RRHH, TI y el Board.
5. **Implemente métricas clave (MTTD/MTTR, cobertura EDR, % parches)** y reporte mensual al CEO y trimestral al Board.
6. **Prepara playbooks para insiders** (detección + offboarding + legal hold).
7. **Integrar cumplimiento contractual**: cláusulas mínimas y derecho de auditoría en contratos con proveedores críticos.

### 9) Mensaje final (resumen)

- **Responsabilidad última = Board / CEO.**
- **Rendición de cuentas operativa = CISO/CIO + equipos funcionales.**
- **Ciberseguridad es responsabilidad compartida**: tecnología, procesos, gente y cultura.
- **Para convertir responsabilidad en resultados** necesitas RACI, KPIs, presupuesto, simulacros y coordinación entre Legal, RRHH, TI y Negocio.
- **La amenaza interna exige un enfoque de equipo**: prevención técnica + monitoreo + políticas y gestión humana.

---

[🔼](#índice)

---

## **513. Ciberseguridad: una responsabilidad de la alta dirección**

### 1) ¿Por qué la ciberseguridad es responsabilidad de la alta dirección?

- **Responsabilidad legal y fiduciaria.** El Board y el CEO son quienes responden ante reguladores, accionistas y clientes por las consecuencias financieras y reputacionales de una brecha.
- **Asignación de recursos.** Solo la dirección tiene autoridad para aprobar presupuestos, contratar talento y cambiar prioridades del negocio.
- **Alineación con la estrategia de negocio.** Riesgo cibernético es riesgo de negocio: afecta continuidad, ingresos, cumplimiento y ventaja competitiva.
- **Cultura organizacional.** La dirección marca el tono (tone from the top): si la alta dirección prioriza seguridad, la organización la toma en serio.

**Ejemplo concreto:** un Board que valida una política de tolerancia al riesgo y asigna presupuesto para un SOC+EDR reduce el tiempo medio para detectar y contener (MTTD/MTTR), evitando pérdidas mayores en un ataque.

### 2) La comunicación abierta beneficiaría a todos

- **Por qué funciona:** la gente reporta incidentes y dudas cuando confía en que no habrá castigos automáticos ni represalias. Comunicación abierta acelera la detección temprana.
- **Qué promover:** canales seguros para reportes (anónimos si hace falta), políticas claras de “no represalia” y retroalimentación sobre acciones tomadas tras los reportes.
- **Cómo medirlo:** número de reportes de phishing/alertas recibidas por empleados, tiempo desde reporte hasta acción. Un aumento inicial de reportes suele indicar mejor concienciación y confianza.

**Ejemplo realista:** tras instaurar un buzón anónimo y recompensas por reportes válidos, una empresa duplica los reportes de phish en 3 meses — como resultado detectan campañas dirigidas antes de que lleguen a clientes.

### 3) Una estrategia adecuada respalda la toma de decisiones ciberseguras

- **Elementos clave de la estrategia:**

  1. **Apetito de riesgo** definido y aprobado por el Board.
  2. **Inventario de activos críticos** y su priorización.
  3. **Controles mínimos obligatorios** (MFA, cifrado, backups, EDR, patching).
  4. **Plan de respuesta a incidentes y playbooks** con roles y tiempos de escalado.
  5. **Política de terceros** y due diligence en proveedores.

- **Beneficio:** decisiones operativas (p. ej. desplegar a producción sin pentest) toman un contexto: “¿se ajusta al apetito de riesgo?” — si no, la decisión debe mitigarse o escalarse.

**Ejemplo:** el Board define que el negocio no acepta exposición de datos de clientes; el CISO exige pentest y revisión legal antes de integrar un nuevo proveedor de analytics. El producto solicita excepción; la dirección la valida solo si el proveedor firma cláusulas y pasa un audit SOC2.

### 4) La práctica hace al maestro: ejercicios y simulacros (tabletops)

- **Por qué practicar:** los planes fallan en los detalles; los tabletop exercises (simulacros de mesa) revelan huecos de comunicación, roles ambiguos y problemas de decisión en tiempo real.
- **Tipos de ejercicios:**

  - **Tabletop (ejercicio de mesa):** escenarios guiados con stakeholders (Board, CISO, Legal, RRHH, Comunicaciones, CTO).
  - **Simulacros técnicos:** ejercicio de IR con SOC y forense (ej. restauración desde backup tras ransomware).
  - **Red team / purple team:** pruebas ofensivas controladas y ejercicio de detección.

- **Cadencia recomendada:** tabletop trimestral para temas críticos; simulacros técnicos semestrales; red team anual (o más frecuente según riesgo).
- **Resultados esperados:** playbooks mejorados, roles clarificados, tiempos de escalado reducidos.

**Ejemplo de ejercicio de mesa (breve):**

- **Escenario:** detección de ransomware en servidores de facturación.
- **Participantes:** CISO (moderador), CEO, CFO, Legal, Comunicaciones, CTO, RRHH, SOC.
- **Objetivos:** decidir aislamiento, notificación a clientes, evaluar obligación de pago, coordinar PR y legal, validar backups.
- **Deliverables tras el ejercicio:** checklist de contención, plantilla de comunicación para clientes y autoridades, plan de recuperación.

### 5) De la responsabilidad a la rendición de cuentas: gobernanza práctica

- **RACI claro para procesos críticos:**

  - **R**esponsible: equipo que ejecuta (SOC, Infra).
  - **A**ccountable: CISO (y Board para decisiones estratégicas).
  - **C**onsulted: Legal, RRHH, Producto.
  - **I**nformed: CEO, Managers, empleados.

- **KPIs que el Board debe exigir** (máximo 5–7):

  - MTTD (Mean Time to Detect)
  - MTTR (Mean Time to Recover)
  - % de endpoints con EDR activo
  - % parches críticos aplicados a tiempo
  - % de empleados con formación anual completada
  - Número y tiempo de cierre de hallazgos críticos (POA\&M)

- **Reporting al Board:** dashboard ejecutivo trimestral + notificaciones inmediatas para incidentes de alto impacto.

**Ejemplo:** el Board aprueba como KPI objetivo reducir MTTD a <24h en 12 meses y asigna presupuesto para EDR y contratación SOC; el CISO reporta progreso mensual.

### 6) Navegando la ambigüedad: decisiones prácticas y escalado

- **Política de escalado:** define umbrales (impacto financiero, número de clientes afectados, datos sensibles comprometidos) para decidir escalado a CEO/Board.
- **Delegación segura:** la dirección delega decisiones operativas (aislar un host, detener un despliegue) a equipos técnicos, pero mantiene autoridad sobre decisiones estratégicas (pagar rescate, comunicación pública).
- **Documentar excepciones:** si se acepta riesgo (excepción), documentarlo con compensating controls y fecha de revisión.

**Ejemplo:** si el impacto estimado < \$50k y no hay PII expuesto, el CISO puede ordenar medidas operativas sin escalar; por encima de ese umbral se notifica al CEO.

### 7) La amenaza interna: enfoque de equipo

- **Prevención:** offboarding rápido, least privilege, revisiones de acceso periódicas, DLP y controles de secretos.
- **Detección:** UEBA, alertas anómalas, correlación entre señales HR (conflictos, salidas) y SOC.
- **Respuesta y proceso disciplinario:** playbook que coordina Legal y RRHH para preservar evidencias y respeto a procesos laborales.
- **Cultura:** canales de denuncia y programas de bienestar para reducir motivaciones maliciosas.

**Ejemplo:** implementar JIT (just-in-time) access para bases de datos sensibles reduce exposición por insiders y facilita auditoría de acceso.

### 8) Métricas y cómo demostrar impacto a la dirección

- **Traduce métricas técnicas a impacto de negocio:** “Reducir MTTD de 72h a 24h reduce exposición promedio y cuesta estimado por incidente en X%”.
- **Ejemplos de KPIs traducidos:**

  - MTTD → «Reducción esperada del coste por incidente» (adicional)
  - % parches aplicados → «Menor probabilidad de explotación en tiempo X»
  - % de backups verificados → «Aumento de probabilidad de recuperación sin pagar rescate»

- **ROI de inversiones:** coste del SOC/EDR vs. coste promedio de brechas (pérdida de ingresos, multas, remediation, reputación).

### 9) Pasos accionables (lista corta para el CEO/Board esta semana)

1. Aprobación formal de la **politica de riesgo cibernético** y definición del apetito de riesgo.
2. Nombrar o confirmar quién reporta al Board (CISO o equivalente) y la frecuencia de reportes.
3. Establecer 5 KPIs trimestrales mínimos y exigir dashboard ejecutivo.
4. Mandar un **tabletop exercise** de 2 horas en 30–60 días (ransomware o brecha de datos) con Board presente.
5. Revisar y asegurar presupuesto para controles fundamentales (MFA, EDR, patch mgmt, backups verificables).
6. Aprobar política de reporte abierto (canal seguro + no represalias).

### 10) Plantillas rápidas (listas para usar ahora mismo)

#### Mini-RACI (incidentes críticos)

```
Detección inicial:
R: SOC / Security Analyst
A: CISO
C: IT, Forensics, Legal, Communications
I: CEO (si impacto > threshold), Board (si impacto sistémico)
```

#### Dashboard ejecutivo (1 página)

- Semana: Nº incidentes detectados | nº incidentes críticos
- MTTD (hoy / objetivo) | MTTR (hoy / objetivo)
- % endpoints con EDR | % parches críticos aplicados
- Nº hallazgos críticos abiertos (y tiempo medio abierto)
- Estado tabletop / ejercicios programados

#### Mini-checklist tabletop (2 horas)

1. Escenario breve + objetivos.
2. Roles presentes y reglas.
3. Línea temporal del incidente (fact pattern).
4. Preguntas dirigidas al Board: ¿aislamos? ¿notificamos? ¿pagamos?
5. Action items y responsible + due date.
6. Informe post-mortem y actualización de playbooks.

### 11) Mensaje final — tono para presentar al Board

> “La ciberseguridad ya no es solo un problema técnico: es riesgo empresarial. Necesitamos que el Board fije el apetito de riesgo, asigne recursos y participe activamente en simulacros. Con claridad de responsabilidades, KPIs accionables y práctica regular reduciremos tiempo de exposición y protegemos el valor de la empresa.”

---

[🔼](#índice)

---

| **Inicio**         | **atrás 51**                |
| ------------------ | --------------------------- |
| [🏠](../README.md) | [⏪](./13_51_Compliance.md) |
