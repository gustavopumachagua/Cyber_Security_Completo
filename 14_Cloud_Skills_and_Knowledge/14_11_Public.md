| **Inicio**         | **atrás 10**             | **Siguiente 12**        |
| ------------------ | ------------------------ | ----------------------- |
| [🏠](../README.md) | [⏪](./14_10_Private.md) | [⏩](./14_12_Hybrid.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [534. ¿Una nube pública?](#534-una-nube-pública)               |
| [535. ¿Qué es una nube pública?](#535-qué-es-una-nube-pública) |

# **Public**

## **534. ¿Una nube pública?**

![Public](/img/14_Cloud_Skills_and_Knowledge/public.png "Public")

Una **nube pública** es un conjunto de servicios de computación (cómputo, almacenamiento, redes, bases de datos, IA, etc.) ofrecidos por un proveedor externo (por ejemplo: AWS, Azure, Google Cloud). Los recursos se alojan en los centros de datos del proveedor y se entregan a múltiples clientes (multi-tenant) por Internet con facturación bajo demanda. La nube pública ofrece **escalabilidad, elasticidad y eficiencia operativa** porque el proveedor gestiona el hardware, la disponibilidad y gran parte de la seguridad física.

### Definición de nube pública

Una **nube pública** es un modelo de computación en el que un proveedor de servicios en la nube ofrece recursos informáticos compartidos a varios clientes a través de la red pública (Internet). Los clientes consumen servicios según necesidad —pagan por uso— sin poseer físicamente la infraestructura. El proveedor es responsable del mantenimiento del hardware, del networking básico, del aprovisionamiento y de la alta disponibilidad a nivel de infraestructura.

**Ejemplo conceptual:** alquilar instancias virtuales (VMs) en AWS EC2, almacenar objetos en S3, o ejecutar funciones serverless en Google Cloud Functions, sin administrar servidores físicos.

### ¿Cómo funciona la nube pública? (flujo y componentes principales)

1. **Multi-tenant + virtualización**

   - El proveedor ejecuta hipervisores y plataformas que aíslan cargas de trabajo de distintos clientes en el mismo hardware físico.

2. **Autoservicio y API**

   - Los usuarios piden recursos (VMs, buckets, bases de datos) por consola, CLI o API; el aprovisionamiento es inmediato o casi inmediato.

3. **Facturación por consumo**

   - Pago por hora/minuto/segundo de cómputo, por GB almacenado, por llamadas a API, etc.

4. **Zonas y regiones**

   - Los proveedores organizan centros de datos en regiones y zonas de disponibilidad para ofrecer redundancia y colocar datos cerca de usuarios.

5. **Serviços gestionados**

   - Además de infraestructura básica (IaaS), se ofrecen servicios gestionados: bases de datos PaaS, ML, analítica, CDN, funciones serverless, identidad, seguridad, etc.

6. **Economía de escala**

   - El proveedor optimiza hardware, refrigeración, energía y operaciones para reducir costes por unidad y ofrecer precios competitivos.

**Ejemplo de flujo sencillo:**

Desarrollador hace `aws ec2 run-instances` → AWS prepara VM en la región elegida → la VM queda accesible por IP pública o dentro de una VPC → facturación comienza por segundo/hora.

### Ventajas de la nube pública (por qué la eligen empresas)

1. **Escalabilidad y elasticidad**

   - Subir o bajar recursos según demanda (autoscaling) en minutos.
   - Ej.: e-commerce que aumenta instancias en Black Friday y las reduce después.

2. **Coste inicial bajo (OPEX vs CAPEX)**

   - No compras servidores; pagas por uso. Ideal para startups y pruebas.

3. **Time-to-market rápido**

   - Provisionas infra, bases de datos y servicios avanzados en minutos — acelera desarrollo.

4. **Amplia variedad de servicios**

   - Desde máquinas virtuales hasta servicios gestionados de IA, streaming, data lakes, etc.

5. **Alta disponibilidad y resiliencia**

   - Replicación entre zonas y regiones, SLAs del proveedor y recuperación ante fallas.

6. **Seguridad física y certificaciones**

   - Proveedores grandes invierten en seguridad física, auditorías y certificaciones (SOC, ISO, etc.).

7. **Operación simplificada**

   - Menos administración física y menor necesidad de equipos on-prem para mantener hardware.

8. **Innovación y ecosistema**

   - Acceso inmediato a nuevas tecnologías (ML managed services, serverless, big data).

### Limitaciones y riesgos (lo que debes evaluar)

- **Control reducido sobre el hardware** — menor control físico y dependencia del proveedor.
- **Posible vendor lock-in** si usas APIs o servicios propietarios muy específicos.
- **Costes variables** que pueden crecer si no se gestionan (egress, instancias innecesarias).
- **Residencia y soberanía de datos** — algunos reguladores exigen que datos permanezcan en país.
- **Supervisión y gobernanza**: necesitas buenas prácticas (IAM, cifrado, logging) para no exponer datos por mala configuración.

### Modelos de precios (resumen práctico)

- **Pago por uso (on-demand):** flexibilidad máxima, coste por unidad consumida.
- **Instancias reservadas / Savings Plans:** descuentos a cambio de compromiso a largo plazo.
- **Preemptible / Spot:** precios muy bajos para cargas batch que toleran interrupciones.
- **Modelos híbridos:** combinar pago por uso con instancias reservadas en función de la previsibilidad.

### Casos de uso comunes (ejemplos prácticos)

1. **Startups y MVPs**

   - Levantar servicios rápidos sin infraestructura propia. _Ejemplo:_ lanzar una app web en EC2 + RDS o usar serverless.

2. **Web y aplicaciones móviles**

   - Backends escalables, CDNs y bases de datos gestionadas. _Ejemplo:_ API en Fargate + CloudFront para assets estáticos.

3. **Big Data y Analytics**

   - Data lakes, procesamiento por lotes y clusters Spark gestionados. _Ejemplo:_ ingestión masiva en S3 + EMR/Dataproc para ETL.

4. **Machine Learning / IA**

   - Entrenamiento en GPUs/TPUs y servicios gestionados para inferencia. _Ejemplo:_ usar instancias GPU para entrenamiento y endpoint gestionado para inferencia.

5. **Dev/Test y CI/CD**

   - Entornos efímeros por PR; pipelines que crean y destruyen infra con IaC. _Ejemplo:_ cada PR crea un environment con Terraform y se destruye al cerrar.

6. **Recuperación ante desastres (DR)**

   - Replicar infra crítica en la nube pública como sitio de DR.

7. **Escenarios de tráfico impredecible**

   - Eventos puntuales, campañas o picos (Black Friday, lanzamientos).

8. **SaaS y servicios globales**

   - Ofrecer servicios a usuarios internacionales con baja latencia (multi-región).

### Buenas prácticas al usar nube pública (resumen accionable)

1. **Diseña con seguridad primero:** IAM, MFA, least-privilege, cifrado in-transit y at-rest.
2. **Automatiza todo:** IaC (Terraform/CloudFormation), pipelines CI/CD.
3. **Monitorea y alerta:** logs centralizados, métricas, SLOs y on-call.
4. **Optimiza costes:** tagging, budgets, rightsizing, usar reserved/spot cuando convenga.
5. **Plan de gobernanza:** policies-as-code (Guardrails), revisiones de arquitectura.
6. **Estrategia de datos y soberanía:** define dónde se alojan datos sensibles.
7. **Pruebas de resiliencia:** chaos engineering, failover drills.

### Comparativa rápida con otros modelos

- **Nube pública vs Nube privada:** pública = recursos compartidos, alta elasticidad y menor CAPEX; privada = infra dedicada, más control y cumplimiento, coste mayor.
- **Nube pública vs Híbrida:** híbrida combina lo mejor de ambas — workloads sensibles on-prem y cargas dinámicas en la pública.
- **Nube pública vs On-prem tradicional:** on-prem da control total pero requiere inversión y operaciones propias; pública ofrece agilidad y economía de escala.

### Checklist para elegir/usar una nube pública (rápido)

- [ ] ¿Necesitas escalabilidad rápida y global?
- [ ] ¿Tienes restricciones regulatorias o de residencia de datos?
- [ ] ¿Puedes diseñar para resiliencia multi-zona/región?
- [ ] ¿Tienes políticas de coste y tagging definidas?
- [ ] ¿Cuentas con controles IAM y cifrado?
- [ ] ¿Planeas evitar vendor lock-in (contenerizar, usar estándares)?

### Conclusión

La **nube pública** ofrece **escalabilidad, eficiencia y acceso rápido a servicios avanzados** que aceleran el desarrollo y la innovación. Es la opción natural para la mayoría de nuevas aplicaciones y para workloads con demanda variable. Para maximizar sus beneficios hay que combinar buenas prácticas de seguridad, automatización, gobernanza y control de costes.

---

[🔼](#índice)

---

## **535. ¿Qué es una nube pública?**

Una **nube pública** es un conjunto de servicios de computación (máquinas virtuales, almacenamiento, redes, bases de datos, funcionalidades de IA, CDN, etc.) que un **proveedor externo** (por ejemplo: AWS, Azure, Google Cloud) ofrece a través de Internet. Los recursos se alojan en los centros de datos del proveedor y se comparten entre muchos clientes (modelo _multi-tenant_), provisionándose bajo demanda y facturándose normalmente por uso.

### ¿Cómo funciona (en la práctica)?

1. **Proveedor**: opera data centers globales, virtualización y plataformas gestionadas.
2. **Autoservicio**: tú pides recursos desde consola/CLI/API y el proveedor los aprovisiona en segundos o minutos.
3. **Virtualización y aislamiento**: máquinas/contenedores de distintos clientes coexisten en el mismo hardware pero aislados lógicamente.
4. **Facturación por consumo**: pagas por tiempo de cómputo, almacenamiento, tráfico, llamadas a API, etc.
5. **Regiones y zonas**: el proveedor organiza recursos por regiones (ubicación geográfica) y zonas (AZ) para ofrecer baja latencia y redundancia.

**Mini-flujo realista:** desarrollas una web → `aws ec2 run-instances` o despliegas un contenedor en Cloud Run → el proveedor crea la instancia/contenedor en la región elegida → conectas DNS/CDN → el sitio queda público.

### Características principales

- **Escalabilidad rápida**: subir o bajar recursos en minutos.
- **Pago por uso (OPEX)**: casi sin CAPEX inicial.
- **Servicios gestionados**: bases de datos, colas, ML, analytics, serverless, etc.
- **Alta disponibilidad**: replicación entre zonas/regiones y SLAs del proveedor.
- **Ecosistema amplio**: integraciones, market place, herramientas de seguridad y compliance.

### Ventajas (con ejemplos)

- **Time-to-market**: montar infra en horas. _Ejemplo:_ una startup despliega front+API en AWS y lanza MVP la misma semana.
- **Elasticidad para picos**: autoscaling para campañas (Black Friday).
- **Acceso a servicios avanzados**: por ejemplo, usar un endpoint gestionado de ML en lugar de entrenar/servir tú mismo.
- **Costes iniciales bajos**: pagar solo por lo que usas; ideal para pruebas y prototipos.

### Limitaciones y riesgos (y cómo mitigarlos)

- **Menor control físico**: no manejas el hardware. → _Mitigación:_ cifrado, contratos y SLAs.
- **Vendor lock-in**: usar servicios propietarios dificulta migrar. → _Mitigación:_ contenerizar, usar estándares, abstraer con IaC.
- **Costes variables si no se controlan**: egress, instancias sobredimensionadas, recursos olvidados. → _Mitigación:_ tagging, budgets, rightsizing, reservas.
- **Residencia de datos / cumplimiento**: algunas normas exigen datos en país. → _Mitigación:_ elegir regiones adecuadas, acuerdos de procesamiento.

### Modelos de precio (resumen)

- **On-demand**: pago por segundo/hora (máxima flexibilidad).
- **Reserved / Savings**: descuentos por compromiso a 1–3 años.
- **Spot / Preemptible**: muy barato para jobs tolerantes a interrupciones (ETL, batch).
- **Pay-per-use en servicios gestionados** (requests, tiempo de ejecución, almacenamiento).

### Casos de uso comunes (ejemplos reales)

- **Web apps y APIs**: backend en contenedores/ECS/Cloud Run + RDS/Cloud SQL.
- **Big Data / Analytics**: data lake en S3 + procesamiento en EMR / Dataflow.
- **Machine Learning**: training en GPUs/TPUs y endpoints de inferencia gestionados.
- **Dev/Test / CI**: entornos efímeros creados por PRs.
- **SaaS**: ofrecer aplicaciones globales con baja latencia y escalado automático.
- **DR / Backup**: usar la nube pública como sitio de recuperación.

### Componentes típicos en la arquitectura de nube pública

- **Compute**: VMs, contenedores, funciones serverless.
- **Storage**: object storage (S3), block storage (volúmenes), file storage.
- **Networking**: VPC, subnets, load balancers, CDN (CloudFront).
- **Managed services**: DBs (RDS/Cloud SQL), colas (SQS/PubSub), secret managers.
- **Observability**: monitoring, logs, tracing.
- **Seguridad**: IAM, WAF, KMS, SIEM.

Texto-diagrama:

```
Usuarios -> CDN -> Load Balancer -> Containers / Functions -> DB managed
                         ↳ Logs/Monitoring -> SIEM
                         ↳ Secrets -> KMS
```

### Buenas prácticas (accionables)

- **IaC** (Terraform/CloudFormation) — nada de clicks manuales en prod.
- **Least privilege (IAM)** y MFA para administradores.
- **Cifrado in-transit y at-rest**; claves en KMS/HSM.
- **Tagging obligatorio** (owner, project, env) para FinOps.
- **Budgets y alertas de coste**; rightsizing y uso de reserved/spot según patrón.
- **Backups y DR probados**; usar múltiples zonas/regiones si aplica.
- **Políticas-as-code / Guardrails** para evitar buckets públicos, etc.

### Mini-proyecto sugerido (práctico, 60–90 min)

Despliega un sitio estático en la nube pública (ej.: AWS) con estos pasos:

1. Subir `index.html` a un bucket S3.
2. Habilitar hosting estático y bloquear acceso público.
3. Crear distribución CloudFront apuntando al bucket (certificado ACM).
4. Añadir registro DNS (Route53) y probar HTTPS.
   Resultado: sitio rápido, barato y global.

(Si quieres, te doy los archivos Terraform + GitHub Actions listos para clonar.)

### Comparación rápida con nube privada/híbrida

- **Pública:** recursos compartidos, máxima elasticidad y menor CAPEX.
- **Privada:** infra dedicada, más control y cumplimiento, coste mayor.
- **Híbrida:** combinación — datos sensibles on-prem, workloads dinámicos en pública.

### Conclusión

La **nube pública** es la opción natural cuando buscas **agilidad, escalado y acceso rápido a servicios avanzados** sin operar hardware. Es ideal para startups, proyectos de innovación, cargas con picos y servicios globales. Para aprovecharla al máximo necesitas disciplina (IaC, seguridad, gobernanza y FinOps) para evitar sorpresas en costes y riesgos operativos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**             | **Siguiente 12**        |
| ------------------ | ------------------------ | ----------------------- |
| [🏠](../README.md) | [⏪](./14_10_Private.md) | [⏩](./14_12_Hybrid.md) |
