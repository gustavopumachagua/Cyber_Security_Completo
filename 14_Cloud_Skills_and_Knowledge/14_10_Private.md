| **Inicio**         | **atrás 9**          | **Siguiente 11**        |
| ------------------ | -------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./14_9_IaaS.md) | [⏩](./14_11_Public.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [532. ¿Qué es una nube privada?](#532-qué-es-una-nube-privada) |
| [533. Reglas de nube privada](#533-reglas-de-nube-privada)     |

# **Private**

## **532. ¿Qué es una nube privada?**

![Private](/img/14_Cloud_Skills_and_Knowledge/Private.png "Private")

Una **nube privada** es un modelo de computación en la nube en el que toda la infraestructura (servidores, almacenamiento, red y software de virtualización) está **dedicada exclusivamente a una sola organización**.

- Puede estar **alojada internamente** en un centro de datos propio (on-premise) o
- **gestionada por un proveedor externo**, pero siempre con acceso exclusivo para una sola empresa.

👉 Ejemplo:

Un banco que no puede compartir infraestructura por motivos regulatorios crea su propia nube privada con VMware + Kubernetes, solo accesible para sus empleados y aplicaciones internas.

### ⚖️ Diferencia entre nube privada, pública e híbrida

| Característica    | Nube Privada                                              | Nube Pública                                        | Nube Híbrida                                                      |
| ----------------- | --------------------------------------------------------- | --------------------------------------------------- | ----------------------------------------------------------------- |
| **Propiedad**     | Exclusiva para una organización                           | Compartida por varios clientes (multi-tenant)       | Combinación de ambas                                              |
| **Control**       | Máximo (empresa define seguridad, red, datos)             | Limitado (el proveedor gestiona la infraestructura) | Mixto                                                             |
| **Escalabilidad** | Limitada al hardware disponible (o al proveedor dedicado) | Muy alta, escalado casi infinito                    | Flexible: elástico en la nube pública + control en la privada     |
| **Coste**         | Alto (infraestructura propia o servicio dedicado)         | Bajo inicial, pago por uso                          | Variable, depende del equilibrio                                  |
| **Ejemplos**      | Nube privada con OpenStack en un datacenter propio        | AWS EC2, Google Cloud, Azure                        | Empresa que mantiene SAP en nube privada y usa AWS para analítica |

### 🕰️ Origen del término “nube privada”

El término **“private cloud”** empezó a usarse a mediados de los 2000.

- Surgió como respuesta al auge de la nube pública (Amazon AWS 2006).
- Muchas empresas, especialmente reguladas (banca, salud, gobierno), **temían perder control y seguridad** en la nube pública.
- Se acuñó “nube privada” para referirse a **infraestructura cloud-like (autoservicio, virtualización, escalabilidad)** pero dedicada a un solo cliente.

👉 Básicamente: _“Queremos los beneficios de la nube, pero con seguridad y control total en casa”_.

### 🌟 Beneficios de una nube privada

1. **Seguridad y cumplimiento** → Control total sobre datos, ideal para regulaciones (HIPAA, GDPR, PCI DSS).

   - _Ejemplo_: un hospital mantiene historiales médicos en nube privada con acceso restringido.

2. **Personalización** → Hardware, redes y software adaptados a las necesidades del negocio.
3. **Predecibilidad de costos** → Gastos fijos (CAPEX si es propia, OPEX si es gestionada).
4. **Alto rendimiento** → Recursos dedicados, sin compartir con otros clientes.
5. **Integración con sistemas legacy** → Ideal para aplicaciones antiguas que no migran bien a la nube pública.
6. **Mayor control operativo** → Se pueden definir políticas de seguridad, backup y disponibilidad a medida.

### ⚙️ ¿Cómo funciona una nube privada?

Una nube privada se construye sobre **tecnologías de virtualización** y/o **contenedores** que permiten:

- **Hipervisores** (VMware vSphere, KVM, Hyper-V) → crean máquinas virtuales aisladas.
- **Orquestadores** (OpenStack, Kubernetes) → gestionan recursos de cómputo, red y almacenamiento.
- **Automatización** → los usuarios pueden aprovisionar recursos de forma similar a la nube pública (autoservicio).

👉 Ejemplo de flujo:

1. Un desarrollador solicita una VM vía un portal interno.
2. El sistema de nube privada (ej. OpenStack) asigna CPU, RAM y almacenamiento de los servidores físicos.
3. El recurso se provisiona en minutos, como en AWS, pero dentro del datacenter privado.

### 🏗️ Tipos de soluciones de nube privada

1. **On-premise (local)**

   - Infraestructura en el datacenter propio de la empresa.
   - Ejemplo: OpenStack desplegado en servidores Dell/HP de una empresa.

2. **Nube privada gestionada**

   - Un proveedor externo la aloja y gestiona, pero los recursos siguen siendo dedicados.
   - Ejemplo: Rackspace ofrece nubes privadas basadas en VMware.

3. **Nube privada virtual (VPC)**

   - Infraestructura dentro de una nube pública, pero con red y recursos aislados.
   - Ejemplo: Amazon VPC en AWS → red privada virtual dentro de la nube pública.

### ☁️ AWS y la nube privada

Aunque AWS es sinónimo de nube **pública**, ofrece servicios para construir entornos de **nube privada o híbrida**:

1. **Amazon VPC (Virtual Private Cloud):**

   - Crea una red virtual aislada dentro de AWS.
   - Puedes definir subredes privadas/públicas, reglas de firewall y VPN hacia tu empresa.

2. **AWS Outposts:**

   - Lleva infraestructura de AWS a tu datacenter on-premise.
   - Ejemplo: servidores físicos en tu empresa, gestionados por AWS, con la misma API que en la nube pública.

3. **AWS Snowball Edge / Snowmobile:**

   - Dispositivos físicos para cómputo y almacenamiento en local, con opción de sincronización con la nube.

4. **AWS Direct Connect:**

   - Conexión dedicada entre tu datacenter y AWS, útil para híbridos.

👉 Caso práctico:

Una aerolínea usa **AWS Outposts** en sus aeropuertos para procesar datos localmente (baja latencia), pero replica todo en AWS para análisis global.

### 📌 Conclusión

- Una **nube privada** te da **control, seguridad y cumplimiento**, sacrificando algo de elasticidad y costo.
- La **pública** es la más flexible y barata, pero con menos control directo.
- La **híbrida** mezcla lo mejor de ambas, y es tendencia hoy en día.
- AWS, aunque pública, ofrece **VPCs, Outposts y Direct Connect** para acercarse al concepto de nube privada.

---

[🔼](#índice)

---

## **533. Reglas de nube privada**

### 1. Principios guía (qué deberían perseguir las reglas)

- **Seguridad por diseño** (secure-by-default).
- **Menor privilegio** (least privilege).
- **Automatización y “policy-as-code”** (las reglas se ejecutan en CI/CD).
- **Auditable y reproducible** (logs, versionado, trazabilidad).
- **Separación de responsabilidades** (RACI clara).
- **Resiliencia y recuperación** (backup + DR verificados).
- **Cumplimiento legal y residencia de datos**.

### 2. Reglas de gobernanza y organización

**Regla G1 — Propiedad y responsabilidades**

- Cada recurso (red, proyecto, tenant) debe tener un _owner_ documentado y un SLA.
- **Ejemplo**: `Owner: equipo-plataforma, Contacto: infra-team@empresa.com, SLA: 99.9%`

**Regla G2 — Política de cambios**

- Todo cambio en infra debe pasar por PR en Git y `plan`/`review` automatizado antes de `apply`.
- Mantener **changelogs** y ventanas de mantenimiento programadas.

**Regla G3 — Clasificación de datos**

- Definir niveles: Public / Internal / Confidential / Restricted. Aplicar controles mínimos por nivel (cifrado, lugares de almacenamiento, access control).

### 3. Reglas de identidad y acceso (IAM / RBAC)

**Regla I1 — Principio de menor privilegio**

- Ningún usuario o servicio tiene permisos por defecto. Los roles se otorgan por necesidad y tiempo limitado.

**Regla I2 — Autenticación fuerte**

- MFA obligatorio para administradores; SSO con IdP corporativo para todos los usuarios.

**Regla I3 — Roles y separación de funciones**

- Definir roles: `platform-admin`, `network-admin`, `security-analyst`, `dev-team`.
- **Ejemplo RACI simple**:

  - Provisioning: R=dev-team, A=platform-admin, C=security, I=finance

**Ejemplo de política de acceso (JSON genérico)**

```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["compute:CreateInstance", "compute:DescribeInstance"],
      "Resource": ["project:dev-*"],
      "Condition": { "TimeOfDay": "08:00-18:00" }
    }
  ]
}
```

_(Adaptar al sistema IAM que uses.)_

### 4. Reglas de red y segmentación

**Regla N1 — Zonas de confianza / microsegmentación**

- Separar redes: Management, Workload-Private, Workload-Public, Database, Backup.
- Comunicación entre zonas mediante firewalls/ACLs con listas explícitas (deny-by-default).

**Regla N2 — Prohibir rutas directas sin inspección**

- Todo tráfico Norte-Sur y Este-Oeste inspeccionado por controles de seguridad (IDS/IPS / NGFW).

**Ejemplo: Security Group mínimo (pseudo)**

- ALB: permitir 80/443 desde Internet.
- App tier: only allow 443 from ALB SG.
- DB tier: allow 5432 only from App SG.

**Ejemplo Kubernetes NetworkPolicy**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-to-db
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              role: app
      ports:
        - protocol: TCP
          port: 5432
```

### 5. Reglas de protección de datos y cifrado

**Regla D1 — Cifrado obligatorio**

- Datos _at-rest_ cifrados con claves gestionadas y rotación periódica (KMS/HSM).
- TLS 1.2+/HTTPS obligatorio _in-transit_.

**Regla D2 — Key management**

- Claves de producción en HSM o KMS; acceso restringido a roles específicos y registrado.

**Regla D3 — Exportación de datos y residencia**

- Definir reglas de dónde se pueden almacenar datos según clasificación y legislación (ej.: datos EU → permanecer en región EU).

**Ejemplo de política de cifrado (declarativa)**

- `AllStorage.Encrypted == true`
- `Database.EncryptionKey.Region == Database.Region`

### 6. Reglas de provisión / lifecycle de workloads

**Regla W1 — Catálogo de servicios**

- Solo desplegar recursos desde un catálogo aprobado (images/máquinas, templates IaC).

**Regla W2 — Imágenes duras (hardened images)**

- Las AMI/VM images oficiales deben pasar linters y escaneo de vulnerabilidades antes de ser aprobadas.

**Regla W3 — Quotas y límites**

- Implementar cuotas por proyecto/tenant (CPU, RAM, storage) para evitar noisy neighbors.

**Ejemplo de workflow (onboarding de tenant)**

1. Crear proyecto en infra con owner.
2. Aplicar policy guardrails (RBAC, quotas, network segmentation).
3. Provisionar VMs/Pods desde template aprobado.
4. Registrar en CMDB y activar monitorización automática.

### 7. Reglas de parcheo y hardening

**Regla P1 — Patching programado**

- Parches críticos: aplicar en ≤48h; parches regulares en ventana mensual.

**Regla P2 — Imágenes inmutables**

- Preferir imagenes inmutables; para cambios rebuild + redeploy en vez de patch manual.

**Regla P3 — Escaneo automatizado**

- Escanear continuamente imágenes y VMs (SCA/SAST/DAST) y remediar hallazgos con prioridad.

### 8. Reglas de backup, continuidad y DR

**Regla B1 — Backups mínimos**

- RPO/RTO definidos por clase de datos; por ejemplo:

  - Critical: RPO 1h, RTO 1h
  - Important: RPO 24h, RTO 8h

**Regla B2 — Retención y pruebas**

- Backups periódicos + pruebas de restore semestrales (tabletop + ejercicio real) documentadas.

**Ejemplo de política de backup (tabla)**

| Nivel        | RPO | RTO | Retención |
| ------------ | --: | --: | --------: |
| Critical     |  1h |  1h |   90 días |
| Important    | 24h |  8h |   30 días |
| Non-critical |  7d | 72h |   14 días |

### 9. Reglas de monitorización, logging y auditoría

**Regla M1 — Logs centralizados e inmutables**

- Todos los eventos de plataforma (auth, network, infra, kernel, audit) se envían a un SIEM/log store con retención y tamper-evidence.

**Regla M2 — Nivel mínimo de telemetría**

- Metrics: CPU, Mem, I/O, latencia p95/p99, error rate, saturación de red.
- Alertas con playbooks enlazados (runbooks).

**Regla M3 — Retención y acceso a logs**

- Logs de seguridad: retención mínima 1 año; acceso a logs: solo security-analyst y auditor.

### 10. Reglas de respuesta a incidentes y forense

**Regla IR1 — Playbooks y legal hold**

- Tener playbooks por tipo (ransomware, data exfil, insider); emitir _legal hold_ inmediatamente si hay presunción de incidente.

**Regla IR2 — Preservación de evidencia**

- Capturar snapshots de volúmenes, exportar logs y preservar chain-of-custody al activar IR.

**Regla IR3 — Comunicación y notificaciones**

- Escalado claro: cuándo notificar a Board, reguladores, clientes; tiempos máximos (ej.: notificar regulador en 72h si PII afectado).

### 11. Reglas de cumplimiento y auditoría

**Regla C1 — Mapear controles a normativas**

- Mapear ISO27001 / GDPR / PCI / HIPAA a controles técnicos (en matrix).

**Regla C2 — Auditorías periódicas**

- Auditoría interna trimestral, auditoría externa anual (SOC2/ISO27001/Pentest).

### 12. Reglas de automatización y “policy-as-code”

**Regla A1 — Policies automatizadas**

- Implementar guardrails con herramientas: OPA/Conftest (policies en CI), admission controllers (K8s), o herramientas cloud-nativas (guardrails).

\*_Ejemplo Rego_ (pseudo) — No public buckets\*\*

```rego
package policies.s3

deny[msg] {
  input.resource.type == "s3_bucket"
  input.resource.public == true
  msg = sprintf("Bucket %s es publico", [input.resource.name])
}
```

_(Integrar en pipeline: bloquea `apply` si la policy falla.)_

### 13. Reglas de facturación y FinOps

**Regla F1 — Tagging obligatorio**

- Todos los recursos deben tener tags: `owner`, `project`, `environment`, `cost_center`.

**Regla F2 — Alertas y budgets**

- Budget alerts automáticos por proyecto; chargeback trimestral.

**Regla F3 — Lifecyle/cleanup**

- Entornos temporales (dev/test) con TTL automático (ej.: destroy 7 días después).

### 14. Reglas para multi-tenant y aislamiento entre clientes

**Regla T1 — Aislamiento lógico o físico según sensibilidad**

- Para datos sensibles o clientes regulados, exigir aislamiento físico (single-tenant) o DB separada y strong RBAC.

**Regla T2 — No compartir credenciales ni keys**

- Cada tenant tiene su propio key material (KMS) y roles auditables.

### 15. Checklist mínimo obligatorio (resumen rápido)

1. [ ] Owner documentado y SLA.
2. [ ] IAM: MFA, least-privilege, roles.
3. [ ] Network: deny-by-default, microsegmentación en zonas.
4. [ ] Cifrado en tránsito y reposo + KMS.
5. [ ] Catalogo de images aprobadas + scanning.
6. [ ] Backups automáticos y pruebas de restore.
7. [ ] Logs centralizados y retención mínima para seguridad.
8. [ ] Policies-as-code en CI (bloqueo de cambios inseguros).
9. [ ] Tagging obligatorio y budgets.
10. [ ] Playbooks IR y legal hold listos.

### 16. Ejemplo práctico: política corta para el manual de operaciones

**Título:** Política de seguridad — Nube Privada (extracto)
**Alcance:** Todos los recursos en la nube privada gestionada por plataforma TI.
**Política:**

- Todo recurso nuevo debe crearse mediante IaC desde repositorio autorizado.
- Los buckets/volúmenes deben cifrarse por defecto.
- Acceso administrativo se concede solo mediante roles temporales con MFA y registro de sesión.
- Backups críticos se realizan cada 1h y se mantienen 90 días.
- Fallas de seguridad crítica: notificar al CISO en 1 hora y activar IR playbook.

### 17. Cómo implementar y hacer cumplir las reglas (sugerencia de roadmap técnico)

1. **Escribir las reglas en lenguaje humano (política).**
2. **Traducir a policy-as-code** (OPA, Conftest, AWS Config Rules, Azure Policy).
3. **Integrar en CI/CD** (PR -> plan -> policy checks).
4. **Provisionar guardrails en la plataforma** (network defaults, quotas).
5. **Automatizar detección** (SIEM, Config rules, periodic scans).
6. **Auditoría y métricas** (porcentaje de recursos compliant).
7. **Capacitación y onboarding** (formar equipos en reglas y runbooks).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**          | **Siguiente 11**        |
| ------------------ | -------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./14_9_IaaS.md) | [⏩](./14_11_Public.md) |
