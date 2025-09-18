| **Inicio**         | **Siguiente 2**                                              |
| ------------------ | ------------------------------------------------------------ |
| [🏠](../README.md) | [⏩](./14_2_Understand_concepts_of_security_in_the_cloud.md) |

---

## **Índice**

| Temario                                                                                                                          |
| -------------------------------------------------------------------------------------------------------------------------------- |
| [514. 7 habilidades de computación en la nube que debes conocer](#514-7-habilidades-de-computación-en-la-nube-que-debes-conocer) |
| [515. ¿Qué habilidades en la nube son esenciales?](#515-qué-habilidades-en-la-nube-son-esenciales)                               |

# **Habilidades y conocimientos en la nube**

## **514. 7 habilidades de computación en la nube que debes conocer**

![Habilidades y conocimientos en la nube](/img/14_Cloud_Skills_and_Knowledge/Habilidades_y_conocimientos_en_la_nube.jpg "Habilidades y conocimientos en la nube")

### 1. Lenguajes de programación para el desarrollo en la nube

**Qué es:** conocer al menos uno o dos lenguajes populares para construir servicios, funciones serverless, automatizar infra y escribir backends.

**Por qué importa:** la nube se programa — despliegues, pipelines, APIs y funciones dependen del lenguaje.

**Lenguajes comunes:** Python, JavaScript/TypeScript (Node.js), Go, Java, C#.

**Ejemplos prácticos:**

- Escribir una _AWS Lambda_ en Python que procese archivos S3.
- Crear una micro-API en Node.js/Express alojada en Cloud Run (GCP) o Azure App Service.

  **Herramientas relacionadas:** pip/npm, frameworks serverless (Serverless Framework, SAM, Terraform + providers).

  **Nivel sugerido:** básico → scripts y APIs simples; intermedio → patrones asíncronos, manejo de concurrencia; avanzado → diseño de microservicios.

  **Mini-proyecto:** crea una API REST en Node.js que suba imágenes a un bucket y devuelva la URL pública (despliega en un servicio serverless).

### 2. Conocimiento de los proveedores de servicios en la nube

**Qué es:** saber cómo funcionan los principales proveedores (AWS, Azure, Google Cloud) y qué servicios ofrecen (compute, storage, networking, IAM, managed DBs).

**Por qué importa:** cada provider tiene su terminología y servicios equivalentes; elegir e integrar correctamente reduce coste y complejidad.

**Ejemplos prácticos:**

- Elegir entre EC2 / ECS / Lambda en AWS según workloads.
- Configurar redes VPC, subnets y gateways para aislar entornos.

  **Herramientas/Conceptos clave:** consola web, CLI (awscli/az/gcloud), IAM, regiones/zones, pricing calculators.

  **Nivel sugerido:** básico → lanzar VM y bucket; intermedio → diseñar infra con VPC, IAM, autoscaling; avanzado → multicloud/hybrid, optimización de costes.

  **Mini-proyecto:** desplegar un “three-tier” demo: frontend (Cloud Run/ App Service), backend (managed DB) y CDN (CloudFront/Cloud CDN).

### 3. Habilidades de gestión de bases de datos

**Qué es:** entender bases relacionales (PostgreSQL, MySQL), NoSQL (DynamoDB, Firestore), y servicios gestionados (RDS, Cloud SQL).

**Por qué importa:** casi todas las apps necesitan persistencia; en la nube tienes opciones gestionadas que cambian arquitectura y operativa.

**Ejemplos prácticos:**

- Diseñar esquema relacional y configurar replicas/backup en RDS.
- Usar DynamoDB para un catálogo con alto throughput y baja latencia.

  **Conceptos clave:** replicación, sharding, índices, backups, escalado vertical vs horizontal, latencia, consistencia eventual vs fuerte.

  **Nivel sugerido:** básico → CRUD y backup; intermedio → tuning, replicas y migraciones; avanzado → diseño de datos para escala y coste.

  **Mini-proyecto:** migrar una pequeña app monolítica con SQLite a PostgreSQL en RDS y configurar backups automáticos.

### 4. Linux

**Qué es:** manejo de la shell, administración básica, networking y troubleshooting en entornos Unix/Linux.

**Por qué importa:** la mayoría de servidores, contenedores y pipelines corren sobre Linux. Saber usarlo acelera debugging y operaciones.

**Ejemplos prácticos:**

- Conectar por SSH, inspeccionar logs (`journalctl`, `/var/log`), administrar servicios (`systemctl`).
- Escribir scripts Bash para automatizar despliegues o tareas de mantenimiento.

  **Comandos / skills clave:** `ssh`, `scp`, `rsync`, `top/htop`, `grep/awk/sed`, permisos, procesos, cron, systemd.

  **Nivel sugerido:** básico→ navegación y edición; intermedio→ scripting y administración; avanzado→ kernel tuning y seguridad.

  **Mini-proyecto:** crear un script que automatice la rotación de logs y lo despliegue como cronjob en una VM Linux.

### 5. Seguridad de la información

**Qué es:** prácticas para proteger infra, apps y datos en la nube: IAM, cifrado, gestión de secretos, seguridad en contenedores y monitoreo.

**Por qué importa:** los riesgos aumentan en la nube (malas políticas IAM, buckets públicos), y un pequeño error puede exponer datos.

**Ejemplos prácticos:**

- Configurar políticas IAM con _least privilege_ y roles temporales (STS).
- Usar KMS/Cloud KMS para cifrar datos en reposo, y Secret Manager/Vault para credenciales.

  **Conceptos clave:** identidad, autenticación, autorización, cifrado, logging, detección (SIEM), posture assessment (CIS Benchmarks).

  **Nivel sugerido:** básico→ MFA, HTTPS, cifrado básico; intermedio→ IAM policies, vaults, pentesting cloud; avanzado→ arquitectura zero-trust y SOC.

  **Mini-proyecto:** asegurar un bucket S3 evitando accesos públicos, habilitar logging y probar con herramientas de escaneo (e.g., Scout Suite).

### 6. Interfaces de programación de aplicaciones (API) en la nube

**Qué es:** diseñar, consumir y asegurar APIs (REST/GraphQL/gRPC) y usar API gateways, rate limiting y autenticación.

**Por qué importa:** las aplicaciones cloud-native exponen funcionalidades via APIs; buen diseño facilita escalado, seguridad y versionado.

**Ejemplos prácticos:**

- Crear una API en Flask/Express y exponerla detrás de API Gateway (AWS API GW, Apigee, Azure APIM).
- Implementar autenticación con OAuth2 / JWT y throttling.

  **Conceptos clave:** idempotencia, versionado, documentación (OpenAPI/Swagger), CORS, SLAs.

  **Nivel sugerido:** básico→ crear y documentar API; intermedio→ autenticación y gateways; avanzado→ diseño para alta disponibilidad y observabilidad.

  **Mini-proyecto:** diseñar una API con OpenAPI, desplegarla en Cloud Run y protegerla con JWT y un API Gateway.

### 7. DevOps

**Qué es:** cultura + prácticas que integran desarrollo y operaciones: CI/CD, infra como código (IaC), contenedores y orquestación.

**Por qué importa:** DevOps automatiza despliegues, pruebas y escalado, reduciendo errores humanos y acelerando releases.

**Ejemplos prácticos:**

- Definir pipeline CI: tests → lint → build container → publicar en registry → desplegar a staging/production.
- Usar Terraform/CloudFormation para versionar infra (VPC, DB, roles).

  **Herramientas clave:** Docker, Kubernetes, Terraform, GitHub Actions/GitLab CI/Jenkins, Helm.

  **Nivel sugerido:** básico→ pipelines simples y Docker; intermedio→ IaC y k8s; avanzado→ GitOps, observabilidad, SRE practices.

  **Mini-proyecto:** crear CI/CD que compile una app, genere una imagen Docker, la publique en Docker Registry y despliegue en un clúster GKE/EKS usando Terraform.

### Plan rápido de aprendizaje (6–9 meses)

1. **Mes 0–1:** Linux básico + Python/Node.js (tutoriales y proyectos pequeños).
2. **Mes 2–3:** Aprende un proveedor (AWS/Azure/GCP): consola, CLI, IAM, S3/Blob, Compute. Haz labs prácticos.
3. **Mes 4:** Bases de datos gestionadas y seguridad básica (KMS, Secret Manager, MFA).
4. **Mes 5:** APIs y serverless (Lambda/Cloud Functions + API Gateway).
5. **Mes 6:** DevOps: Docker + Terraform + CI (un proyecto end-to-end).
6. **Mes 7–9:** Profundiza en seguridad cloud (pentesting básico, CIS benchmarks) y en orquestación (Kubernetes). Construye portafolio con mini-proyectos.

### Certificaciones recomendadas

- **Cloud provider fundamentals:** AWS Cloud Practitioner / Azure Fundamentals / Google Cloud Digital Leader.
- **Dev/Infra:** AWS Certified Developer / Azure Developer / GCP Associate Cloud Engineer.
- **DevOps:** HashiCorp Terraform Associate, CNCF Certified Kubernetes Administrator (CKA).
- **Seguridad:** CCSK (Cloud Security Alliance), AWS Certified Security Specialty, (ISC)² CCSP.

#### Consejos finales rápidos

- Prioriza **práctica** sobre teoría: monta proyectos reales en la capa gratuita (free tier).
- Versiona todo: código y **infra como código**.
- Aprende a **leer facturas cloud**: optimización de costes es una skill valiosa.
- Documenta tus proyectos en GitHub y escribe mini-posts explicando decisiones (esto ayuda en entrevistas).

---

[🔼](#índice)

---

## **515. ¿Qué habilidades en la nube son esenciales?**

### 1) Fundamentos del proveedor cloud (AWS / Azure / GCP)

**Por qué:** cada proveedor tiene servicios, límites y paradigmas propios; entenderlos evita decisiones costosas.

**Herramientas:** consola web, CLI (`aws`, `az`, `gcloud`).

**Tarea práctica:** crear una cuenta gratuita, lanzar una VM, crear un bucket/almacenamiento y desplegar una app simple.

### 2) Linux y scripting (Bash, Python)

**Por qué:** la mayoría de servidores, contenedores y pipelines corren sobre Linux; automatizar tareas es clave.

**Herramientas:** `ssh`, `systemd`, `journalctl`, `bash`, Python.

**Mini-proyecto:** script que hace backup de un directorio a un bucket S3/Blob y rota versiones.

### 3) Lenguajes de programación (Python, JavaScript/TypeScript, Go)

**Por qué:** para construir microservicios, lambdas, automatizar infra y procesar datos.

**Tarea práctica:** crear una API REST pequeña y desplegarla en un servicio serverless (Lambda, Cloud Functions, Azure Functions).

### 4) Infraestructura como Código (IaC) — Terraform / CloudFormation / ARM

**Por qué:** reproducibilidad, versionado y seguridad en infra. IaC es estándar.

**Herramientas:** Terraform (multi-cloud), CloudFormation (AWS), ARM/Bicep (Azure).

**Ejemplo:** escribir módulos Terraform que creen VPC, subnets, security groups y un clúster EKS/GKE.

### 5) Contenedores y orquestación (Docker + Kubernetes)

**Por qué:** despliegue consistente, escalado automático, microservicios. Kubernetes es la plataforma dominante.

**Herramientas:** Docker, kubectl, Helm, Minikube, EKS/GKE/AKS.

**Proyecto:** contenerizar una app, orquestarla en un clúster local y exponerla con Ingress.

### 6) CI/CD y automatización (GitHub Actions, GitLab CI, Jenkins)

**Por qué:** despliegues seguros y repetibles; reduce errores manuales.

**Tarea práctica:** pipeline que corre tests, construye imagen Docker, la empuja a registry y despliega al ambiente staging.

### 7) Redes en la nube (VPC, Subnets, routing, peering)

**Por qué:** seguridad, separación de entornos, conectividad híbrida.

**Conceptos clave:** subnetting, NAT/Gateway, security groups, NACL, peering, VPN, Direct Connect/ExpressRoute.

**Ejemplo:** diseñar VPC con subnets públicas/privadas y NAT para salidas seguras.

### 8) Bases de datos y almacenamiento (RDS, DynamoDB, Cloud SQL, S3)

**Por qué:** elegir la base adecuada impacta latencia, coste y consistencia.

**Práctica:** desplegar una base relacional gestionada, configurar replicas y backups automáticos; probar un modelo NoSQL (DynamoDB/Firestore) para alta escala.

### 9) Seguridad e identidad (IAM, KMS, gestión de secretos)

**Por qué:** errores en IAM o secretos expuestos son vectores muy comunes de brechas.

**Herramientas:** IAM/roles/policies, KMS, Secrets Manager, Vault, WAF, Security Hub.

**Mini-proyecto:** implementar least-privilege IAM roles y almacenar secretos en Secret Manager; automatizar rotación.

### 10) Observabilidad (logs, métricas, tracing — ELK/Prometheus/Grafana, X-Ray)

**Por qué:** no puedes asegurar lo que no puedes medir. Observabilidad = detección + diagnóstico + SLOs.

**Práctica:** instrumentar una app con prom métricas, centralizar logs en ELK o Cloud Logging, crear alertas en el servicio de monitoreo.

### 11) Cost management & optimización

**Por qué:** cloud mal administrado se vuelve caro. Saber medir y optimizar es valioso para negocio.

**Acción práctica:** usar pricing calculators, habilitar tags por proyecto, analizar un mes de factura y proponer optimizaciones (reservas, rightsizing).

### 12) Serverless y arquitecturas event-driven

**Por qué:** rápido desarrollo, pago por uso; ideal para workloads específicos.

**Ejemplo práctico:** pipeline de ingestión: S3 → Lambda que procesa → SNS → función que escribe en BD.

### Habilidades no técnicas pero imprescindibles

- **Troubleshooting / debugging**: seguir logs, reproducir errores, aislar la causa.
- **Arquitectura y trade-offs**: elegir entre coste/latencia/resiliencia.
- **Comunicación y documentación**: diagramas, runbooks, post-mortems.
- **Seguridad y cumplimiento**: entender requisitos regulatorios (GDPR, PCI, HIPAA).
- **Trabajo en equipo / Agile / DevOps mindset**.

### Mapa por roles (qué priorizar según tu objetivo)

- **Cloud Developer:** programación, serverless, APIs, CI/CD, testing.
- **Cloud Engineer / Infra:** IaC, redes, seguridad, Linux, gestión de costes.
- **DevOps / SRE:** Kubernetes, CI/CD, observabilidad, automatización, SLO/SLI.
- **Data Engineer:** managed DBs, data warehouses, ETL, streaming (Kafka / PubSub).
- **Cloud Security Engineer:** IAM, KMS, WAF, SOC tools, pentesting en cloud.

### Plan de aprendizaje (práctico, 0 → 6 meses)

**Mes 0–1:** fundamentos cloud + Linux + Git + Python básico.

**Mes 2–3:** IaC (Terraform) + desplegar infra básica + S3/DB + scripting.

**Mes 4:** Docker → Kubernetes (minikube) → desplegar microservicio.

**Mes 5:** CI/CD pipelines + observabilidad (Prom/Grafana o Cloud Monitoring).

**Mes 6:** seguridad IAM + secretos + proyecto final: app completa (API + DB + infra como código + CI/CD + monitoring).

### Cómo demostrar las habilidades (portafolio / entrevistas)

- **GitHub:** repos con Terraform modules, Dockerfiles, manifests k8s, scripts de IaC y README con instrucciones reproducibles.
- **Proyectos concretos para mostrar:**

  - “Infra para app X”: repos con Terraform + CI pipeline (Actions) + despliegue en free tier.
  - “Monitorización”: demo con Prometheus + Grafana + alertas.
  - “Seguridad”: política IAM mínima, demo de Secret Manager + evidencia de prueba.

- **Certificaciones útiles:** AWS/Azure/GCP Associate, Terraform Associate, CKA (Kubernetes).
- **Entrevistas técnicas:** prepárate para explicar decisiones de arquitectura, costes y trade-offs; practicar tareas hands-on en un entorno controlado.

### Checklist de autoevaluación (marca si lo sabes hacer)

- [ ] Lanzar y configurar una VM Linux + SSH
- [ ] Escribir y ejecutar script de backup a cloud storage
- [ ] Crear y versionar infra con Terraform
- [ ] Contenerizar app y crear manifiesto k8s
- [ ] Construir pipeline CI que despliegue a staging
- [ ] Configurar IAM roles least-privilege
- [ ] Instrumentar app con métricas y alertas
- [ ] Analizar factura y proponer optimización

### Para terminar — 3 mini-proyectos para tu portafolio (listos para comenzar)

1. **Todo-in-one App**: App Node/Python + PostgreSQL → Docker → Terraform infra (VPC, subnets, RDS, bucket) → GitHub Actions que despliega → Grafana monitorea.
2. **Serverless pipeline**: upload a S3 → Lambda transforma → escribe a DynamoDB → CloudWatch logs + alerts.
3. **K8s microservices**: 2 servicios, Ingress + autoscaling + Prometheus/Grafana + Helm charts.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**                                              |
| ------------------ | ------------------------------------------------------------ |
| [🏠](../README.md) | [⏩](./14_2_Understand_concepts_of_security_in_the_cloud.md) |
