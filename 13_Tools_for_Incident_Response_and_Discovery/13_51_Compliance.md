| **Inicio**         | **atrás 50**           | **Siguiente 52**            |
| ------------------ | ---------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_50_Legal.md) | [⏩](./13_52_Management.md) |

---

## **Índice**

| Temario                                                                                                |
| ------------------------------------------------------------------------------------------------------ |
| [510. ¿Qué es el cumplimiento de la ciberseguridad?](#510-qué-es-el-cumplimiento-de-la-ciberseguridad) |
| [511. Cumplimiento de ciberseguridad 101](#511-cumplimiento-de-ciberseguridad-101)                     |

# **Compliance**

## **510. ¿Qué es el cumplimiento de la ciberseguridad?**

![Compliance](/img/13_Tools_for_Incident_Response_and_Discovery/Compliance.jpg "Compliance")

**Definición corta:**

El _cumplimiento de la ciberseguridad_ (compliance) es el conjunto de actividades, políticas, controles técnicos y procesos organizativos que una entidad implementa para satisfacer obligaciones externas (leyes, normas, contratos, regulaciones sectoriales) y exigencias internas sobre seguridad de la información. No es sólo tecnología: incluye gobernanza, riesgo, auditoría y evidencia práctica que demuestre que las medidas están en vigor y funcionan.

### ¿Por qué importa?

- **Legal y financiero:** evita multas, sanciones y demandas (ej. violaciones de privacidad).
- **Reputación:** reduce pérdida de confianza y daño de marca tras incidentes.
- **Operativo:** obliga a madurar procesos (backup, patching, continuidad).
- **Comercial:** muchos clientes exigen certificaciones (ISO 27001, SOC2) o cumplimiento (PCI-DSS) para contratar.

### Tipos de obligaciones que cubre el cumplimiento de ciberseguridad

1. **Regulatorias / legales:** leyes de protección de datos (p. ej. GDPR), requisitos sectoriales (HIPAA para salud).
2. **Estándares y marcos voluntarios:** ISO 27001, NIST CSF/SP, CIS Controls. Se usan como referencia o para certificación.
3. **Requisitos contractuales:** cláusulas de seguridad en contratos con clientes/proveedores (DPA, SLA, indemnizaciones).
4. **Políticas internas y buenas prácticas:** políticas corporativas, códigos de conducta, procedimientos.

### Componentes clave de un programa de cumplimiento en ciberseguridad

1. **Gobernanza y liderazgo**

   - Roles claros (CISO, Data Protection Officer, comité de riesgo).
   - Políticas aprobadas por la dirección.

2. **Gestión de riesgos**

   - Identificar activos críticos y amenazas.
   - Evaluación de impacto y probabilidad; priorizar controles.

3. **Controles técnicos y organizativos**

   - Ejemplos: control de accesos (MFA), cifrado, WAF, patch management, gestión de vulnerabilidades, backups, logging.
   - Controles administrativos: clasificaciónd e información, acuerdos de confidencialidad, formación.

4. **Documentación y evidencia**

   - Políticas, procedimientos, registros de configuración, logs, informes de pentest, actas de formación.

5. **Monitoreo, detección y respuesta**

   - SIEM/EDR, runbooks de respuesta, ejercicios (tabletop), playbooks de incidentes.

6. **Evaluación y auditoría**

   - Auditorías internas y externas, pruebas de cumplimiento, gaps y POA\&M (Plans of Actions & Milestones).

7. **Gestión de terceros**

   - Due diligence, cláusulas contractuales, revisiones periódicas y control de subprocesadores.

8. **Formación y cultura**

   - Capacitación periódica (phishing, manejo de datos, roles/responsabilidades).

### Cómo se ve en la práctica — flujo básico para lograr cumplimiento

1. **Inventario y clasificación** de activos y datos.
2. **Mapeo de requisitos** (qué exige GDPR / PCI / ISO para tu activo).
3. **Evaluación gap** entre estado actual y requisitos.
4. **Plan de remediación** (priorizar por riesgo).
5. **Implementación de controles** técnicos/organizativos.
6. **Generación de evidencia** (logs, configuraciones, informes de pruebas).
7. **Auditoría / certificación** y cierre de hallazgos.
8. **Monitoreo y mejora continua**.

### Ejemplos concretos (casos y evidencias)

#### Ejemplo A — Comercio electrónico (cumplimiento PCI-DSS)

- **Requisito**: proteger datos de tarjeta.
- **Controles típicos**: segmentación de red, cifrado de datos en tránsito y reposo, control de accesos, registros de logs, pruebas de pentest trimestrales.
- **Evidencia**: informe de escaneo trimestral ASV, registros de acceso, configuración de cifrado en DB, informe de pentest.

#### Ejemplo B — Empresa que procesa datos de ciudadanos (GDPR)

- **Requisito**: bases legales y notificaciones de brechas en plazos.
- **Controles típicos**: DPIA (Data Protection Impact Assessment), contratos DPA con procesadores, rotación de claves y gestión de consentimientos.
- **Evidencia**: DPIA documentado, DPA firmado con proveedores, registros de consentimiento, registro de violaciones (incidentes) y notificaciones realizadas.

#### Ejemplo C — Pequeña empresa SaaS que quiere certificar ISO 27001

- **Paso práctico**: definir alcance (servicio SaaS), introducir ISMS (política, objetivos), hacer evaluación de riesgo, planificar controles (AC, CM, BC), evidenciar e implementar auditoría externa.

### Mapeo rápido: requisito → control → evidencia (mini tabla)

| Requisito (ejemplo)       |           Control recomendado | Evidencia                                          |
| ------------------------- | ----------------------------: | -------------------------------------------------- |
| Notificar brechas (GDPR)  | Playbook IR + responsable DPO | Timeline de incidente + borradores de notificación |
| Protección tarjetas (PCI) |        Tokenización / cifrado | Configuraciones de tokenización + logs de acceso   |
| Gestión de parches        | Patch management automatizado | Reporte de cumplimiento de parches por host        |

### Consecuencias de no cumplir

- Multas (GDPR puede llegar a porcentajes altos del turnover).
- Pérdida de clientes/contratos y bloqueo de cuentas.
- Costes de remediación e incident response mucho mayores.
- Acciones legales y daños reputacionales.

### Métricas y KPIs útiles para medir cumplimiento

- % de controles implementados vs. requeridos.
- Tiempo medio para parchear (patch time).
- MTTD / MTTR (detección y recuperación).
- Nº de hallazgos abiertos en auditoría / tiempo medio de cierre.
- % de empleados formados en seguridad.

### Buenas prácticas y errores comunes

**Buenas prácticas**

- Integrar cumplimiento en el ciclo de vida del producto (shift-left).
- Usar frameworks como NIST CSF o ISO 27001 como “mapa” y luego adaptar a reglas locales.
- Automatizar evidencia (CI/CD que ejecuta tests, escaneos y guarda artefactos).
- Mantener catálogos de datos y SBOMs para dependencias.

**Errores comunes**

- Tratar cumplimiento como un evento puntual en vez de proceso continuo.
- Falta de evidencia/documentación (no sirve “hacer” si no puedes demostrarlo).
- No incluir a Legal y a Procurement temprano en contratos con proveedores.

### Checklist rápido para empezar (5-10 ítems)

1. Inventario de activos y clasificación de datos.
2. Mapeo de obligaciones regulatorias y contractuales.
3. Evaluación de gaps (risk assessment).
4. Plan de remediación priorizado por riesgo.
5. Implementación de controles críticos (MFA, cifrado, patching, backups).
6. Playbook de respuesta ante incidentes y roles definidos.
7. Registro y almacenamiento de evidencia (logs, informes).
8. Auditoría interna y externalización de auditoría/certificación si aplica.
9. Programas de formación y phishing.
10. Revisión periódica (trimestral/semestral) y mejora continua.

---

[🔼](#índice)

---

## **511. Cumplimiento de ciberseguridad 101**

### 📌 ¿Qué es el cumplimiento de la seguridad cibernética?

El **cumplimiento de la ciberseguridad** es el proceso de implementar políticas, controles y medidas técnicas para garantizar que una organización cumpla con:

- **Leyes** (ej. GDPR en Europa, Ley de Protección de Datos en Perú).
- **Normas internacionales** (ej. ISO 27001, NIST).
- **Requisitos de la industria** (ej. PCI DSS para tarjetas de crédito, HIPAA para salud).
- **Contratos con clientes o proveedores** que exigen estándares de seguridad.

👉 **Ejemplo:**

Una empresa SaaS que almacena datos de usuarios europeos debe cumplir con el GDPR: esto significa cifrar datos, obtener consentimiento claro, y notificar brechas en menos de 72 horas.

### 📌 Importancia del cumplimiento de la ciberseguridad

1. **Evita multas y sanciones** → GDPR puede multar hasta el 4% de la facturación anual.
2. **Protege la reputación** → un incidente sin cumplimiento genera pérdida de clientes.
3. **Genera confianza con socios y clientes** → muchos piden certificaciones como SOC 2 o ISO 27001 antes de firmar contratos.
4. **Mejora la seguridad interna** → obliga a la empresa a tener backups, control de accesos y gestión de riesgos.

👉 **Ejemplo:**

Un hospital en EE. UU. que incumple HIPAA puede recibir multas millonarias y perder la confianza de los pacientes.

### 📌 Tipos de datos sujetos al cumplimiento de la ciberseguridad

- **Datos personales**: nombres, correos electrónicos, teléfonos.
- **Datos financieros**: tarjetas de crédito, cuentas bancarias.
- **Datos médicos**: historiales clínicos, diagnósticos.
- **Datos confidenciales corporativos**: propiedad intelectual, contratos.

👉 **Ejemplo:**

Un e-commerce que procesa pagos con tarjeta debe cumplir PCI DSS porque maneja **datos financieros sensibles**.

### 📌 5 pasos para comenzar con el cumplimiento de la ciberseguridad

1. **Identificar los datos críticos y regulaciones aplicables**

   - Ejemplo: si procesas pagos → PCI DSS; si trabajas con datos de salud → HIPAA.

2. **Realizar una evaluación de riesgos y brechas (gap analysis)**

   - Comparar lo que ya tienes con lo que pide la norma.

3. **Implementar controles técnicos y organizativos**

   - Ejemplo: activar MFA, cifrar bases de datos, capacitar empleados.

4. **Generar evidencia y documentación**

   - Ejemplo: políticas internas, registros de accesos, reportes de auditoría.

5. **Monitorear y mejorar continuamente**

   - Revisar auditorías, pruebas de phishing, escaneos de vulnerabilidades.

### 📌 Tipos de regulaciones de cumplimiento de ciberseguridad

- **SOC 2** → asegura buenas prácticas de seguridad en SaaS y empresas tecnológicas.
- **ISO 27001** → estándar internacional para sistemas de gestión de seguridad de la información.
- **NIST CSF** → marco de referencia para gestión de riesgos en ciberseguridad.
- **GDPR** → regula la protección de datos en la Unión Europea.
- **HIPAA** → regula datos médicos en EE. UU.
- **PCI DSS** → regula transacciones con tarjetas de crédito.

👉 **Ejemplo práctico:**

Una startup de pagos online en Perú que opera en EE. UU. debe cumplir con **PCI DSS** y con **GDPR** si tiene clientes europeos.

### 📌 Automatice el cumplimiento con informes en tiempo real (ejemplo Sprinto)

Herramientas como **Sprinto, Drata o Vanta** permiten:

- Conectar sistemas (AWS, Google Workspace, GitHub).
- Monitorear cumplimiento en tiempo real.
- Generar automáticamente evidencias para auditorías (contraseñas seguras, registros de accesos, logs).

👉 **Ejemplo:**

En lugar de recopilar manualmente logs de acceso a servidores, la herramienta genera un informe automático que se presenta al auditor.

### 📌 Preguntas frecuentes

**1. ¿Es lo mismo seguridad que cumplimiento?**

No. Seguridad es proteger sistemas. Cumplimiento es demostrar que cumples con leyes y normas.

**2. ¿Cumplir garantiza que no tendré brechas?**

No. El cumplimiento reduce riesgos y multas, pero no elimina los ataques.

**3. ¿Qué pasa si no cumplo?**

Multas, pérdida de contratos, demandas de clientes.

**4. ¿Una PyME necesita cumplimiento?**

Sí. Aunque no siempre se exige certificación, muchas empresas piden evidencias de seguridad antes de contratar.

✅ **En resumen:**

El **cumplimiento de la ciberseguridad** no es solo un requisito legal, sino una estrategia para proteger datos, ganar confianza y mejorar procesos internos. Aunque parece complejo, se puede empezar paso a paso: identificar regulaciones, implementar controles básicos, documentar y automatizar con herramientas modernas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 50**           | **Siguiente 52**            |
| ------------------ | ---------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_50_Legal.md) | [⏩](./13_52_Management.md) |
