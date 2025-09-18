| **Inicio**         | **atrás 47**           | **Siguiente 49**                 |
| ------------------ | ---------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./13_47_Whois.md) | [⏩](./13_49_Human_Resources.md) |

---

## **Índice**

| Temario                                                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [504. Publicación del NIST sobre ingeniería de sistemas seguros y confiables](#504-publicación-del-nist-sobre-ingeniería-de-sistemas-seguros-y-confiables) |
| [505. Glosario NIST](#505-glosario-nist)                                                                                                                   |

# **Stakeholders**

## **504. Publicación del NIST sobre ingeniería de sistemas seguros y confiables**

![Stakeholders](/img/13_Tools_for_Incident_Response_and_Discovery/Stakeholders.webp "Stakeholders")

### 1) ¿Qué es SP 800-160 (resumen rápido)?

- **SP 800-160 Vol. 1 (Engineering Trustworthy Secure Systems)** establece principios, conceptos y actividades para incorporar la seguridad como una propiedad del sistema desde el diseño y a lo largo del ciclo de vida. Trata a la **seguridad como una propiedad emergente** que debe ser diseñada usando prácticas de ingeniería de sistemas.
- **SP 800-160 Vol. 2** complementa a Vol.1 enfocándose en **ciber-resiliencia**: diseñar sistemas que anticipen, resistan, se recuperen y se adapten ante fallos o ataques.

### 2) ¿Por qué es importante esta publicación?

- Porque desplaza la seguridad del parcheo y controles reactivos a un **enfoque de ingeniería** integrado: requisitos, arquitectura, verificación y operación.
- Integra «seguridad», «confiabilidad» y «resiliencia» en el ciclo de vida (Concepto → Diseño → Implementación → Verificación → Operación → Retiro).

### 3) Principios clave (qué te pide aplicar) — con ejemplos

1. **Seguridad como propiedad emergente** — no basta con sumar controles; hay que diseñar la interacción de componentes.

   _Ejemplo:_ dos subsistemas seguros por separado pueden, al integrarse, permitir una ruta de escalada; el diseño de interfaces evita ese fallo.

2. **Pensamiento sistémico / multidisciplinario** — coordinación entre arquitectos, devs, operaciones, legal y negocio.

   _Ejemplo:_ equipos de producto y operaciones diseñan conjuntamente la política de actualización de firmware para evitar ventanas de ataque.

3. **Defensa en profundidad + principio de menor privilegio** — varias capas y acceso mínimo.

   _Ejemplo:_ IoT: root of trust hardware + arranque seguro + aislamiento de procesos + firewall por zona.

4. **Diseño para resiliencia** — detectar, aislar, recuperar y continuar servicios parciales.

   _Ejemplo:_ servicio web crítico que degrada funciones no esenciales cuando detecta ataque DDoS, manteniendo autenticación básica.

5. **Verificación continua** — pruebas dinámicas, fuzzing, pruebas de integración, red team y monitoreo en producción.

   _Ejemplo:_ pipeline CI/CD que ejecuta tests de seguridad automáticos + análisis estático y despliegue a entorno canario.

(Estos principios están desarrollados en SP 800-160 y referencias asociadas).

### 4) Actividades por fase del ciclo de vida (qué hacer — con ejemplos concretos)

NIST recomienda integrar actividades de SSE (Systems Security Engineering) en cada fase:

**a) Concepto / Requisitos**

- Definir objetivos de seguridad y propiedades emergentes (confidencialidad, integridad, disponibilidad, seguridad funcional).
- _Ejemplo:_ requisito: “firmware debe poder actualizarse en <24h con verificación de firma” y “tiempo máximo de contención <1h”.

**b) Análisis de riesgos y modelado**

- Threat modelling (STRIDE/DREAD), análisis de consecuencias y priorización.
- _Ejemplo:_ modelar vectores de actualización OTA en un dispositivo y mitigaciones (signed firmware, rollback safe).

**c) Arquitectura y diseño**

- Diseñar límites de confianza, interfaces endurecidas, canales cifrados, segregación de funciones.
- _Ejemplo:_ diseñar una gateway que filtre y autentique telemetría antes de exponerla al cloud.

**d) Implementación**

- Secure coding, gestión de secretos, dependencias verificadas, verificación de builds reproducibles.
- _Ejemplo:_ firmar artefactos y comprobar firmas en runtime; evitar claves embebidas.

**e) Integración y verificación**

- Pruebas de integración, fuzzing, pruebas de penetración, análisis de comportamiento.
- _Ejemplo:_ ejecutar fuzzing sobre parsers de red del dispositivo y test de recuperación tras corrupción.

**f) Operación y mantenimiento**

- Monitoreo, respuesta a incidentes, telemetría, actualizaciones seguras y pruebas de parche.
- _Ejemplo:_ políticas de patching, despliegue progresivo (canary) y rollback seguro.

**g) Retiro**

- Plan de descomisión (revocar claves, notificar usuarios, borrar datos sensibles).

Estos pasos se deben coordinar con gestión de requisitos y gestión de configuración (versionado y trazabilidad).

### 5) Ejemplos prácticos (mini-casos)

#### Caso A — **Dispositivo IoT (cámara conectada)**

- **Requisitos:** arranque seguro, actualizaciones firmadas, autenticación basada en certs, telemetría mínima.
- **Diseño:** SoC con TPM/secure element; partición de bootloader; firmware firmado; canal TLS mutuamente autenticado para actualizaciones.
- **Verificación:** pruebas OTA en laboratorio, fuzzing del parser HTTP, escaneo de dependencias (SBOM) y simulación de pérdida de conectividad.
- **Operación:** despliegue canario, telemetría de integridad, rotación de claves programada.
  Esto implementa principios de SP 800-160 (root of trust, least privilege, verificación continua).

#### Caso B — **Plataforma web crítica (SaaS)**

- **Requisitos:** aislamiento multitenant, recuperación RTO/RPO, logging inmune a manipulación.
- **Diseño:** microservicios con autenticación zero-trust, network policies, segmentación, backups inmutables.
- **Verificación:** tests de carga y de degradación, ejercicios de recuperación ante incidentes, red-team.
- **Operación:** monitorización (detección temprana), escalado automático con políticas de seguridad, proceso de parcheo automatizado.
  Alineado con resiliencia y sistemas de NIST Vol.2.

### 6) Artefactos y deliverables recomendados

- **Security Concept of Operations (SecConOps)** — cómo el sistema debe comportarse en condiciones normales y anómalas.
- **Security requirements traceability matrix** — trazabilidad de requisitos a pruebas.
- **Threat model + attack surface register.**
- **Arquitectura de confianza (trust boundaries, root of trust).**
- **SBOM (Software Bill of Materials)** y verificación de supply chain.
- **Planes de operación y recuperación** (playbooks de IR).
  SP 800-160 describe la naturaleza y uso de esos artefactos.

### 7) Métricas y cómo medir el progreso

- **Mean Time to Detect (MTTD)** y **Mean Time to Recover (MTTR)**.
- **Porcentaje de requisitos de seguridad verificados** (Trazabilidad \* 100).
- **Número de hallazgos críticos por release** (tendencia decreciente esperada).
- **Cobertura de pruebas automatizadas de seguridad** (porcentual).
  Estas métricas ayudan a gobernar la mejora continua en el enfoque SSE.

### 8) Supply chain y confianza en componentes (qué exige NIST)

- Verificar proveedores, exigir SBOMs, controles de integridad de componentes, firmar artefactos y validar la procedencia. SP 800-160 insiste en tratar la cadena de suministro como parte del sistema.

### 9) Cómo empezar en tu organización (roadmap práctico)

1. **Leer SP 800-160 Vol.1 y Vol.2** (referencias abajo).
2. **Formar un equipo interdisciplinario** (ingeniería, seguridad, operaciones, arquitectura de producto).
3. **Definir un piloto** (un sistema pequeño: IoT o microservicio) y aplicar SSE end-to-end.
4. **Crear artefactos mínimos**: SecConOps, requisitos de seguridad y threat model.
5. **Integrar verificación automática** en CI/CD y establecer telemetría.
6. **Escalar aprendizajes** y adaptar por dominios de riesgo.

### 10) Limitaciones y cosas a tener en cuenta

- SP 800-160 es **guía** y marco; su implementación exige inversión en procesos, cultura y herramientas.
- No elimina la necesidad de controles prácticos modernos (WAF, EDR, etc.): los complementa dentro de la ingeniería.
- Requiere compromiso de niveles gerenciales para incorporar actividades en presupuestos y calendarizaciones.

### 11) Recursos oficiales y lectura recomendada

- **NIST SP 800-160, Vol. 1 — Engineering Trustworthy Secure Systems (Rev.1 / final)**.
- **NIST SP 800-160, Vol. 2 — Developing Cyber-Resilient Systems (Rev.1 / final)**.
- Nota de NIST sobre la revisión y enfoque en sistemas: comunicado oficial.

### 12) Checklist rápido (usar como plantilla)

- [ ] ¿Tenemos SecConOps para el sistema?
- [ ] ¿Los requisitos de seguridad están trazados a pruebas?
- [ ] ¿Existe un threat model actualizado?
- [ ] ¿Root of trust definido e implementado?
- [ ] ¿SBOM disponible para componentes críticos?
- [ ] ¿Pipeline CI/CD con análisis estático/dinámico automatizado?
- [ ] ¿Plan de recuperación y ejercicios de resiliencia realizados?
- [ ] ¿Métricas (MTTD/MTTR/coverage) definidas y reportadas?

---

[🔼](#índice)

---

## **505. Glosario NIST**

**1. NIST**

Definición: National Institute of Standards and Technology (EE. UU.), organismo que publica guías, estándares y buenas prácticas en seguridad de la información.

Por qué importa: sus publicaciones (p. ej. SP 800-53, SP 800-37, CSF) son referencias ampliamente aceptadas para gestionar riesgo y controles.

Ejemplo: seguir NIST CSF como marco para estructurar un programa de seguridad en una pyme.

**2. CSF (Cybersecurity Framework)**

Definición: Marco de NIST orientado a la gestión de riesgo y a la mejora continua (funciones: Identify, Protect, Detect, Respond, Recover).

Ejemplo: usar CSF para mapear controles técnicos y procesos de negocio antes de pedir presupuesto al CTO.

**3. RMF (Risk Management Framework)**

Definición: Proceso estructurado para categorizar sistemas, seleccionar controles, autorizar operación y realizar monitoreo continuo.

Ejemplo: aplicar RMF (SP 800-37) para obtener un **ATO** (Authorization To Operate) de un sistema crítico.

**4. SP (Special Publication)**

Definición: Serie de publicaciones técnicas del NIST (ej. SP 800-53, SP 800-160).

Ejemplo: consultar SP 800-53 para elegir controles de acceso para una aplicación web.

**5. SP 800-53 (Security and Privacy Controls)**

Definición: catálogo de controles organizados por familias (AC, IA, CM, etc.).

Ejemplo: implementar AC-2 (Account Management) y IA-2 (Authenticator Management) en una plataforma SaaS.

**6. SP 800-37**

Definición: guía del RMF — cómo autorizar sistemas y mantener el ATO.

Ejemplo: pasar las etapas del RMF para la compra e integración de un sistema ERP.

**7. SP 800-160**

Definición: ingeniería de sistemas seguros — enfoque para diseñar seguridad como propiedad del sistema.

Ejemplo: diseñar un dispositivo IoT con root of trust, arranque seguro y actualizaciones firmadas.

**8. Categoría de impacto (FIPS 199)**

Definición: clasificación de un sistema como _Low / Moderate / High_ para confidencialidad, integridad y disponibilidad.

Ejemplo: un sistema de nóminas suele ser _Moderate_ en confidencialidad por datos personales.

**9. Security Control (control de seguridad)**

Definición: medida técnica, administrativa o física destinada a reducir riesgo.

Ejemplo: un WAF es un control técnico que mitiga inyección SQL.

**10. Control Family (familias de SP 800-53)**

Definición: grupos temáticos de controles (AC = Access Control, CM = Configuration Management, IR = Incident Response, CP = Contingency Planning, etc.).

Ejemplo: CM-2 para control de cambios de configuración en servidores.

**11. Baseline (línea base de seguridad)**

Definición: conjunto mínimo de controles aplicables a una categoría de impacto.

Ejemplo: baseline _Moderate_ para aplicaciones web incluye cifrado TLS, autenticación MFA y registro de auditoría.

**12. SSP (System Security Plan)**

Definición: documento que describe el sistema, los controles implementados y su estado.

Ejemplo: el SSP de una API describe arquitectura, controles AC/IA/SC y responsables.

**13. POA\&M (Plan of Actions and Milestones)**

Definición: registro de debilidades/deficiencias con acciones planificadas y fechas objetivo.

Ejemplo: "Corregir CVE-XXXX antes del 30/09 — responsable: equipo infra."

**14. ATO (Authorization To Operate)**

Definición: autorización formal para que un sistema opere en un entorno dado, tras evaluación de riesgo.

Ejemplo: el sistema CRM obtiene ATO condicionado a mitigar 3 hallazgos en POA\&M.

**15. Risk (riesgo)**

Definición: combinación de _impacto_ y _probabilidad_ de que ocurra un evento adverso.

Ejemplo: riesgo = exfiltración de datos por RCE (alta probabilidad + alto impacto).

**16. Threat (amenaza) y Threat Actor**

Definición: evento o persona/entidad que puede explotar vulnerabilidades (APT, delincuentes script kiddies, insiders).

Ejemplo: actor: grupo ransomware que apunta a servidores SMB.

**17. Vulnerability (vulnerabilidad)**

Definición: fallo o debilidad que puede ser explotada.

Ejemplo: servicio con versión vulnerable a CVE conocida sin parche.

**18. Residual Risk (riesgo residual)**

Definición: riesgo que permanece después de aplicar controles.

Ejemplo: aceptar riesgo residual de servicio en cloud por costo de mitigación adicional.

**19. Likelihood (probabilidad) / Impact (impacto)**

Definición: dimensiones para evaluar riesgo (cuánto puede pasar y qué daño causa).

Ejemplo: probabilidad baja pero impacto crítico → requiere plan de contingencia.

**20. Compensating Control**

Definición: medida alternativa que mitiga un requisito cuando el control primario no es factible.

Ejemplo: si no se puede cifrar un legado, añadir segmentación de red y monitoreo intenso.

**21. Principle of Least Privilege (principio de mínimo privilegio)**

Definición: dar sólo los permisos estrictamente necesarios.

Ejemplo: cuentas de servicio con permisos de solo lectura en DB donde corresponda.

**22. Defense in Depth (defensa en profundidad)**

Definición: capas múltiples de protección para evitar fallo único.

Ejemplo: perímetro (FW) + WAF + EDR + backups inmutables.

**23. Separation of Duties (separación de funciones)**

Definición: distribuir tareas críticas para evitar fraude o errores.

Ejemplo: quien desarrolla no aprueba producción; deployments requieren aprobación distinta.

**24. Continuous Monitoring (monitoreo continuo)**

Definición: vigilancia automatizada de la seguridad y los controles en tiempo real o periódica.

Ejemplo: SIEM con alertas para cambios de configuración críticos detectados por CMDB.

**25. Security Assessment / Assessment Plan**

Definición: pruebas y revisiones para verificar que controles funcionan (auditorías, pen tests, vulnerab. scans).

Ejemplo: pentest anual + escaneo de vulnerabilidades mensual.

**26. Penetration Test / Red Team**

Definición: evaluación activa que simula ataques reales (red team = ejercicio más amplio y persistente).

Ejemplo: red team simula phishing + lateral movement para medir detección.

**27. Incident Response (IR) y IRP (Incident Response Plan)**

Definición: proceso y plan para detectar, contener, erradicar y recuperar de incidentes.

Ejemplo: playbook IR: contener (aislar host), erradicar (eliminar binario), recuperar (restaurar desde backup).

**28. Forensics / Forense digital**

Definición: recolección e investigación de evidencias digitales para identificar causas y atacantes.

Ejemplo: captura de imagen de disco y análisis con Volatility y sleuth kit.

**29. MTTR / MTTD / MTTF**

Definición: métricas — Mean Time To Recover (MTTR), Mean Time To Detect (MTTD), Mean Time To Failure (MTTF).

Ejemplo: objetivo: reducir MTTD a < 15 minutos con alertas de EDR.

**30. CVE / CVSS / CPE**

Definición: CVE = identificador de vulnerabilidad; CVSS = puntuación de severidad; CPE = identificador de productos.

Ejemplo: CVE-2021-44228 (Log4Shell) con CVSS 10; priorizar parcheo.

**31. SBOM (Software Bill of Materials)**

Definición: lista de componentes (librerías/paquetes) que componen un software.

Ejemplo: SBOM en formato SPDX para un build que incluye OpenSSL v1.0.2.

**32. Supply Chain Security (seguridad de la cadena de suministro)**

Definición: controles para asegurar componentes, proveedores y procesos externos.

Ejemplo: exigir SBOM y escaneo SCA a proveedores antes de integrar su librería.

**33. Zero Trust**

Definición: modelo que asume que no hay confianza implícita, verifica todo (identidad, dispositivo, contexto).

Ejemplo: acceso a apps requiere MFA + posture check del dispositivo.

**34. IAM (Identity & Access Management)**

Definición: gestión de identidades, autenticación, autorización y cuentas.

Ejemplo: provisionamiento automatizado con roles y auditoría de acceso.

**35. PKI (Public Key Infrastructure) / Root of Trust**

Definición: sistema de gestión de certificados y claves; root of trust = componente inicial de confianza.

Ejemplo: firma de firmware con PKI y verificación en arranque seguro de los dispositivos.

**36. STIX / TAXII / MISP / MAEC**

Definición: formatos y plataformas para compartir indicadores y análisis (STIX/TAXII para IOCs y automatización; MISP = plataforma; MAEC = comportamiento de malware).

Ejemplo: publicar IOC (dominios, hashes) en MISP para que el SOC los consuma.

**37. Control Enhancement / Derived Requirement**

Definición: fortalecimiento o requisito adicional que especifica cómo cumplir un control base.

Ejemplo: AC-2 (base) + AC-2(3) (enhancement) que exige revisión periódica de cuentas inactivas.

**38. Traceability (trazabilidad)**

Definición: relación registrada entre requisitos, controles, pruebas y evidencias.

Ejemplo: matriz que muestra que requisito X está cubierto por control Y y probado por test Z.

**39. Assurance (aseguramiento)**

Definición: nivel de confianza en que un control funciona como se espera (evidencia, pruebas, certificaciones).

Ejemplo: evidencia de logs y pruebas automatizadas que aumentan la assurance de un control.

**40. Authorization Boundary (límite de autorización)**

Definición: alcance exacto (componentes, redes, servicios) del sistema considerado para la autorización.

Ejemplo: ATO aplica al sistema compuesto por app frontend, API y DB, pero no a servicios externos de terceros.

### Cómo usar este glosario en la práctica

- Para **documentar**: pega definiciones en tu SSP, POA\&M o playbooks.
- Para **evaluar controles**: usa las familias de SP 800-53 como checklist.
- Para **comunicar con stakeholders**: traduce riesgos y métricas (MTTD/MTTR) a lenguaje de negocio.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 47**           | **Siguiente 49**                 |
| ------------------ | ---------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./13_47_Whois.md) | [⏩](./13_49_Human_Resources.md) |
