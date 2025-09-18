| **Inicio**         | **atrás 11**                                     | **Siguiente 13**                                  |
| ------------------ | ------------------------------------------------ | ------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_11_Roles_of_Compliance_and_Auditors.md) | [⏩](./8_13_Understand_Backups_and_Resiliency.md) |

---

## **Índice**

| Temario                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------------- |
| [217. ¿Qué es el riesgo de ciberseguridad?](#217-qué-es-el-riesgo-de-ciberseguridad)                                            |
| [218. Análisis de riesgos: conozca su tolerancia a las amenazas](#218-análisis-de-riesgos-conozca-su-tolerancia-a-las-amenazas) |

# **Comprender la definición de riesgo**

## **217. ¿Qué es el riesgo de ciberseguridad?**

### ✅ Definición de riesgo de ciberseguridad

El **riesgo de ciberseguridad** es la **posibilidad de que una amenaza explote una vulnerabilidad tecnológica o humana**, generando un **impacto negativo en la confidencialidad, integridad o disponibilidad (CIA Triad) de la información** de una organización.

Se mide en función de:

$$
Riesgo = Probabilidad \times Impacto
$$

👉 Ejemplo:

- Una vulnerabilidad en un servidor web mal parcheado puede tener **alta probabilidad** de ser explotada y causar una **pérdida crítica de datos financieros** → **alto riesgo**.

### 🎯 Por qué la gestión de riesgos de ciberseguridad es una prioridad estratégica

En 2025, la ciberseguridad dejó de ser solo un problema técnico para convertirse en un **riesgo empresarial**.

### Razones principales:

1. **Aumento de ataques avanzados** (ransomware-as-a-service, APTs).
2. **Mayor dependencia digital** (IA, IoT, nube, trabajo remoto).
3. **Regulaciones más estrictas** (GDPR, NIS2, leyes de privacidad en LatAm).
4. **Impacto financiero directo** (interrupción de operaciones, multas, pérdida de clientes).
5. **Daño reputacional**: una brecha puede afectar la confianza de clientes e inversionistas.

👉 Ejemplo real:

- En 2024, varias empresas de retail sufrieron **ataques de ransomware** que paralizaron sus sistemas de pagos. Esto no solo causó pérdidas millonarias, sino también fuga de clientes hacia competidores.

### ⚙️ Componentes del riesgo de ciberseguridad

1. **Amenazas**

   - Hackers, ransomware, phishing, insiders maliciosos, ataques de IA.
   - Ejemplo: un empleado descontento filtra credenciales.

2. **Vulnerabilidades**

   - Sistemas desactualizados, contraseñas débiles, falta de monitoreo.
   - Ejemplo: un ERP sin parches de seguridad.

3. **Impacto**

   - Pérdida financiera, sanciones regulatorias, interrupción de negocio, reputación dañada.
   - Ejemplo: filtración de datos médicos en un hospital → multas + pérdida de confianza.

4. **Controles de mitigación**

   - Firewalls, autenticación multifactor, auditorías de seguridad, Zero Trust.

### 📈 Principales tendencias de riesgo de ciberseguridad en 2025

1. **Ataques impulsados por IA**

   - Deepfakes usados en ingeniería social.
   - Phishing con textos generados por IA que imitan perfectamente a directivos.

2. **Ransomware 3.0**

   - No solo cifrado de datos, sino **doble y triple extorsión**: roban datos, amenazan publicarlos y presionan a terceros (clientes o proveedores).

3. **Ataques a la cadena de suministro digital**

   - Compromiso de proveedores de software o servicios en la nube.
   - Ejemplo: incidentes tipo _SolarWinds_.

4. **Exposición en la nube**

   - Configuraciones incorrectas de entornos multicloud.
   - Riesgo elevado por adopción masiva de SaaS y PaaS.

5. **Amenazas internas (insiders)**

   - Personal que, por error o de forma intencional, compromete datos sensibles.

6. **Riesgos en IoT y OT (tecnología operacional)**

   - Dispositivos médicos, fábricas y vehículos conectados → mayor superficie de ataque.

7. **Cumplimiento regulatorio más complejo**

   - Multas millonarias por no cumplir con leyes de protección de datos o normas internacionales.

### 🛡️ Cómo gestionar el riesgo de ciberseguridad en 2025

1. **Identificación de riesgos**

   - Inventario de activos digitales (servidores, aplicaciones, datos críticos).
   - Evaluación de amenazas y vulnerabilidades.

2. **Análisis y priorización**

   - Usar metodologías como **NIST RMF, ISO 27005, FAIR**.
   - Clasificar riesgos según probabilidad e impacto.

3. **Implementar controles de mitigación**

   - Seguridad Zero Trust.
   - Segmentación de redes.
   - MFA y gestión de identidades (IAM).
   - Cifrado de datos.
   - Respuesta ante incidentes.

4. **Monitoreo y detección continua**

   - SOC (Centro de Operaciones de Seguridad).
   - SIEM y SOAR para correlación de eventos.
   - Threat Intelligence (CTI) para anticipar ataques.

5. **Concientización y capacitación**

   - Simulaciones de phishing.
   - Programas de cultura de ciberseguridad.

6. **Planes de respuesta y recuperación**

   - Backups probados y aislados.
   - Ejercicios de Red Team / Blue Team.
   - Plan de continuidad de negocio.

### 📌 Conclusión: Tratar el riesgo cibernético como un riesgo empresarial

En 2025, la ciberseguridad ya no es un tema exclusivo de TI:

- El **CEO y la junta directiva** deben tratar el riesgo cibernético con la misma importancia que los **riesgos financieros, legales o operativos**.
- Gestionar el riesgo de ciberseguridad requiere una **visión integral**, que combine:

  - Tecnología (herramientas y controles).
  - Personas (capacitación y cultura).
  - Procesos (gobernanza, cumplimiento y respuesta).

👉 Las organizaciones que integren la **gestión de riesgos cibernéticos en su estrategia empresarial** estarán mejor preparadas para resistir y recuperarse de ataques inevitables en la era digital.

---

[🔼](#índice)

---

## **218. Análisis de riesgos: conozca su tolerancia a las amenazas**

### 1) Qué es el análisis de riesgos y qué es la tolerancia a las amenazas

**Análisis de riesgos** es el proceso sistemático de identificar activos, amenazas y vulnerabilidades; estimar la probabilidad y el impacto; y priorizar acciones para reducir el riesgo.

**Tolerancia a las amenazas (risk tolerance)** es el **nivel máximo de impacto que una organización está dispuesta a aceptar** antes de tomar medidas correctivas. Es el “límite” entre lo aceptable y lo inaceptable para la organización —no solo técnico, sino financiero, operativo y reputacional.

- **Risk appetite** (apetito): cuánto riesgo quiere asumir la organización en términos generales.
- **Risk tolerance** (tolerancia): límites concretos para riesgos específicos (ej.: “no aceptamos > 4 horas de indisponibilidad en pagos”).
- **Risk threshold** (umbral): valor numérico que desencadena acción (ej.: pérdida anual > \$50,000 → remediar).

### 2) Por qué conocer la tolerancia importa

- Prioriza correctamente inversiones en controles (¿parcho o compensación?).
- Alinea seguridad con objetivos de negocio (lo que protege = lo que más impacta al negocio).
- Evita gastar demasiado en riesgos aceptables o subproteger riesgos críticos.
- Facilita decisiones ante incidentes: “¿aceptamos esta interrupción temporal o la mitigamos ya?”.

### 3) Proceso práctico de análisis de riesgos (pasos implementables)

1. **Inventario y clasificación de activos**

   - Activos: aplicaciones, datos, servidores, procesos críticos.
   - Clasificar por criticidad (Alta / Media / Baja) y por tipo de impacto (financiero, regulatorio, reputacional).

2. **Identificación de amenazas y vulnerabilidades**

   - Threat modelling, escaneo de vulnerabilidades, OSINT, logs históricos.

3. **Estimación de probabilidad e impacto**

   - Escalas: cualitativa (Bajo/Medio/Alto) o cuantitativa (1–5, o valores financieros).
   - Si usas valores numéricos, define claramente qué significa cada número.

4. **Cuantificación del riesgo**

   - Métrica simple: **Riesgo = Probabilidad × Impacto**.
   - Modelos más avanzados: **FAIR** para pérdidas financieras, ALE (Annual Loss Expectancy), escenarios Monte Carlo.

5. **Determinar tolerancia / umbrales**

   - Trabajar con dirección/finanzas para fijar límites (ej.: máximo pérdida anual tolerable, máximo downtime aceptable).
   - Documentar la **risk appetite statement** y los umbrales por categoría.

6. **Priorización y tratamiento**

   - Decide: **Aceptar / Mitigar / Transferir / Evitar**.
   - Planea controles coste-efectivos acorde a la tolerancia.

7. **Monitoreo y revisión**

   - KPIs: MTTD, MTTR, % controles implementados, % riesgos por encima del umbral.
   - Revisiones periódicas y tras cambios (nuevas apps, fusiones).

### 4) Métodos y métricas (cualitativo VS cuantitativo)

- **Cualitativo**: matrices (probabilidad × impacto), heatmaps, descriptores (Bajo/Medio/Alto). Rápido y útil en primeras etapas.
- **Cuantitativo**: conviertes impacto en valores monetarios (pérdida esperada) o en métricas medibles (horas de inactividad). Necesario para decisiones de inversión.

#### Ejemplo de cuantificación con ALE (FAIR-style)

Supongamos:

- Valor del activo (AV) = \$500,000
- Exposición esperada si se compromete = 20% (0.2)
- Probabilidad anual de que ocurra el evento (ARO) = 0.10 (una vez cada 10 años)

Cálculo paso a paso (digit-by-digit):

**SLE (Single Loss Expectancy)** = AV × Exposure Factor

- AV = 500,000
- Exposure Factor = 0.2

Multiplicación: 500,000 × 0.2

- 500,000 × 2 = 1,000,000.
- Dividir por 10 para multiplicar por 0.2 → 1,000,000 ÷ 10 = 100,000.
  → **SLE = \$100,000**

**ALE (Annual Loss Expectancy)** = SLE × ARO

- SLE = 100,000
- ARO = 0.10

Multiplicación: 100,000 × 0.10

- 100,000 × 1 = 100,000.
- Dividir por 10 para multiplicar por 0.10 → 100,000 ÷ 10 = 10,000.
  → **ALE = \$10,000 por año**

Interpretación: si tu **tolerancia financiera** para este tipo de riesgo es menor a \$10,000/año deberías mitigar; si tu tolerancia es mayor, podrías aceptarlo o transferir (seguro).

### 5) Ejemplos prácticos de tolerancia y decisiones

#### Ejemplo A — Banco grande (tolerancia baja)

- Sistema de pagos: **RTO máximo tolerable = 1 hora**.

  - Coste por hora de indisponibilidad = \$250,000/hora.
  - Umbral definido: si riesgo proyectado implica > 1 hora de downtime → **evitar o mitigar inmediatamente** (redundancia, geo-replicación, DR).

#### Ejemplo B — Startup SaaS pequeña (tolerancia moderada)

- Portal marketing: RTO aceptable = 8 horas.

  - Coste por hora = \$2,000/hora.
  - Umbral máximo pérdidas aceptadas = 2,000 × 8 = 16,000.
  - Cálculo paso a paso:

    - 2,000 × 8 = 2,000 × (4 + 4) = (2,000 × 4) + (2,000 × 4).
    - 2,000 × 4 = 8,000.
    - 8,000 + 8,000 = 16,000.

  - Si mitigación cuesta \$5,000/año y reduce ALE por debajo de 16,000 → comprar mitigación.

#### Ejemplo C — Servicio público crítico (tolerancia prácticamente cero)

- Sistema SCADA de planta: tolerancia a fallas operativas = prácticamente 0; incluso pequeños riesgos se tratan con prioridad máxima. Política: “sin aceptación”; controles técnicos y operativos obligatorios.

### 6) Plantilla simple de Risk Register (ejemplo)

| ID  | Activo    | Amenaza    | Prob (1–5) | Impacto (1–5) | Riesgo = P×I | Controles actuales    | Riesgo residual | Acción                 |
| --- | --------- | ---------- | ---------: | ------------: | -----------: | --------------------- | --------------: | ---------------------- |
| R1  | API pagos | Ransomware |          4 |             5 |     4×5 = 20 | Backups, segmentation |               8 | Mitigar: EDR + DR test |

Cálculo mostrado: Prob 4 × Impact 5 =

- 4 + 4 + 4 + 4 + 4 = 20 → **Riesgo = 20**.

  Definir umbrales (ej.: ≥15 = tratar urgentemente).

### 7) Mapeo riesgo → decisión: Accept / Mitigate / Transfer / Avoid

- **Accept (aceptar)**: riesgo por debajo del umbral y coste de mitigación mayor al beneficio.
- **Mitigate (mitigar)**: aplicar controles técnicos/operativos.
- **Transfer (transferir)**: seguro, outsourcing, SLA con proveedor.
- **Avoid (evitar)**: eliminar actividad o detener proyecto riesgoso.

### 8) Determinar tolerancia en tu organización (pasos concretos)

1. **Reunir stakeholders**: CEO, CFO, CIO, legal, continuidad, operaciones.
2. **Business Impact Analysis (BIA)**: calcular impacto por interrupción en tiempo/£/clientes.
3. **Definir métricas cuantificables**: pérdida anual aceptable (ALE), tiempo máximo de downtime, máximo % de datos sensibles publicables.
4. **Documentar risk appetite statement** y umbrales por tipo de riesgo.
5. **Aprobar y comunicar** a toda la organización.
6. **Revisar anualmente** o tras cambios significativos.

### 9) Cómo incorporar tolerancia en controles y arquitectura

- **Controles técnicos**: redundancia, alta disponibilidad, backups air-gapped, segmentación, MFA.
- **Controles administrativos**: políticas, SLAs, rotación de credenciales, formación.
- **Financieros**: seguro cibernético con límites alineados a tolerancia.
- **Operativos**: playbooks IR, ejercicios tabletop, runbooks de recuperación.

### 10) KPIs útiles para gestionar tolerancia y riesgos

- **ALE por categoría** (USD/año).
- **% riesgos por encima del umbral**.
- **MTTD (Mean Time To Detect)** y **MTTR (Mean Time To Remediate)**.
- **Número de incidentes que superaron la tolerancia** (y tiempo hasta contención).
- **% de activos críticos con controles requeridos implementados**.

### 11) Errores comunes y cómo evitarlos

- **No involucrar negocio** → tolerancias irrelevantes.
- **Usar solo cualitativo** → difícil priorizar inversiones.
- **No revisar tolerancias** tras cambios (nuevos productos, regulaciones).
- **Ignorar riesgos residuales** → acumulan y subestiman exposición.

### 12) Checklist rápido para empezar hoy (acciónable)

- Hacer inventario de activos críticos (top 20).
- Ejecutar BIA para esos activos.
- Definir 3–5 umbrales claros (ej.: pérdida anual aceptable, RTO crítico, % datos sensibles permitidos expuestos).
- Crear o actualizar Risk Appetite Statement aprobado por la dirección.
- Ejecutar 1 ejercicio tabletop para validar umbrales en un escenario realista.
- Publicar dashboard con KPIs de riesgo (ALE, % riesgos críticos).

### Conclusión práctica

Conocer la **tolerancia a las amenazas** transforma el análisis de riesgos de un ejercicio técnico en una **herramienta de decisión empresarial**. Te permite gastar recursos donde el negocio lo necesita, responder rápidamente cuando se cruza un umbral y justificar inversiones (reducción de ALE) ante la dirección.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**                                     | **Siguiente 13**                                  |
| ------------------ | ------------------------------------------------ | ------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_11_Roles_of_Compliance_and_Auditors.md) | [⏩](./8_13_Understand_Backups_and_Resiliency.md) |
