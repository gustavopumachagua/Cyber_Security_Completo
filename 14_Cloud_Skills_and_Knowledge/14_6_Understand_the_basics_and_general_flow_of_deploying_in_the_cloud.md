| **Inicio**         | **atrás 5**                                          | **Siguiente 7**      |
| ------------------ | ---------------------------------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./14_5_Understand_the_concept_of_Serverless.md) | [⏩](./14_7_SaaS.md) |

---

## **Índice**

| Temario                                                                                  |
| ---------------------------------------------------------------------------------------- |
| [524. ¿Qué es la implementación en la nube?](#524-qué-es-la-implementación-en-la-nube)   |
| [525. Implementación de un sitio web en AWS](#525-implementación-de-un-sitio-web-en-aws) |

# **Comprenda los conceptos básicos y el flujo general de la implementación en la nube.**

## **524. ¿Qué es la implementación en la nube?**

**Definición corta:**

La **implementación en la nube** (cloud deployment) es el proceso de publicar, poner en marcha y operar una aplicación, servicio o infraestructura en un proveedor de nube (AWS, Azure, Google Cloud u otros), o en una combinación híbrida/privada, de forma automatizada y repetible. Incluye aprovisionamiento de recursos, empaquetado del artefacto (contenerización o build), orquestación del despliegue, verificación, monitoreo y —si hace falta— rollback.

A continuación te explico qué engloba, por qué importa, estrategias, pasos concretos y ejemplos prácticos.

### 1. ¿Por qué es importante la implementación en la nube?

- **Velocidad:** despliegues rápidos y repetibles reducen el time-to-market.
- **Consistencia:** evita diferencias entre entornos (dev/staging/prod).
- **Escalabilidad y resiliencia:** la nube permite escalar y distribuir servicios geográficamente.
- **Automatización y trazabilidad:** todo queda versionado (código + infra), reversible y auditado.
- **Costos y elasticidad:** pagas por lo que usas y escalas según demanda.

### 2. Componentes principales de una implementación en la nube

1. **Código / Artefacto:** binario, contenedor Docker, función (zip), paquete serverless.
2. **Infraestructura:** VPC, subredes, balanceadores, bases de datos, buckets. (Se crea vía IaC).
3. **Pipeline CI/CD:** construye, prueba, analiza y despliega automáticamente.
4. **Configuración / Secrets:** variables de entorno, secretos gestionados (Secrets Manager, Key Vault).
5. **Orquestador / Runtime:** Kubernetes, ECS, App Service, funciones Lambda/Functions.
6. **Monitoreo y Observabilidad:** métricas, logs, tracing, alertas (CloudWatch, Prometheus, DataDog).
7. **Governance:** políticas de seguridad, compliance y control de costes.

### 3. Modelos de despliegue en la nube (dónde se implementa)

- **Nube pública:** AWS, Azure, GCP — mayor agilidad.
- **Nube privada:** infra dedicada a una sola organización (on-prem o hosted).
- **Híbrido:** combinación (datos sensibles on-prem + apps en la nube).
- **Multi-cloud:** usar varios proveedores para resiliencia o requisitos específicos.

### 4. Estrategias de despliegue (cómo se despliega)

Breve resumen con ejemplos y cuándo usar:

- **Recreate / Replace:** detener versión antigua y lanzar la nueva. (Simple, riesgo de downtime).
- **Rolling update:** actualizar instancias por lotes para mantener servicio disponible. (K8s, ASG)
- **Blue/Green:** tener dos entornos (blue=prod actual, green=nueva versión). Cambias tráfico al green cuando está listo.

  - _Ejemplo:_ cambiar el alias del balanceador o actualizar DNS.

- **Canary:** lanzar la nueva versión a un pequeño porcentaje de usuarios; aumentar progresivamente si no hay problemas.

  - _Ejemplo:_ 5% tráfico → 25% → 100%.

- **A/B (feature flags):** exponer funcionalidades solo a ciertos usuarios; ideal para experimentos y rollback instantáneo.
- **Shadow / Mirroring:** enviar tráfico a la nueva versión sin afectar respuesta real (solo para pruebas de carga y validación).

### 5. Pipeline típico CI/CD (pasos)

1. **Commit** en repo → trigger pipeline.
2. **Build**: compilar, empaquetar (jar, docker image).
3. **Tests**: unitarios, integración, seguridad (SAST), dependencia scanning.
4. **Push de artefacto** al registry (Docker Hub, ECR, ACR).
5. **Plan de IaC** (`terraform plan` / `arm/bicep` / `cloudformation change set`).
6. **Aprobación** (manual si aplica) → `apply` infraestructura o release.
7. **Deploy**: actualizar servicio (rolling / canary / blue-green).
8. **Smoke tests & health checks**.
9. **Monitor & promote** a prod o rollback si métricas fallan.
10. **Notificación y registro** del despliegue.

### 6. Ejemplo práctico: pipeline GitHub Actions → Docker → AWS ECS (simplificado)

```yaml
name: CI-CD to AWS ECS

on:
  push:
    branches: [main]

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to Amazon ECR
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build and push image
        run: |
          IMAGE=123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app:${{ github.sha }}
          docker build -t $IMAGE .
          docker push $IMAGE
        env:
          AWS_REGION: us-east-1

      - name: Deploy to ECS (update service taskDefinition)
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition: ecs-task.json
          service: my-service
          cluster: my-cluster
          wait-for-service-stability: true
```

Este pipeline: construye imagen, la sube a ECR y actualiza la tarea del servicio ECS (podrías combinarlo con canary rules y health checks).

### 7. IaC + Deploy: ejemplo resumido con Terraform (aprovisionar infra básica)

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "artifacts" {
  bucket = "mi-artifacts-bucket-demo-12345"
  acl    = "private"
}
```

Flujo: `terraform init` → `terraform plan` → revisar plan → `terraform apply`.
En CI: `terraform plan` en PR y `terraform apply` sólo tras merge y aprobación.

### 8. Validación post-despliegue y observabilidad

- **Smoke tests**: llamadas simples a endpoints para validar salud.
- **Canary metrics**: latencia, error rate, saturation, business metrics (conversion, checkout).
- **Alertas**: si error rate > X% o latencia subida, activar rollback.
- **Tracing & logs**: correlación de request id para depuración (OpenTelemetry, X-Ray).
- **SLA/SLO/SLOs**: definir objetivos y medir.

### 9. Seguridad y gobernanza en despliegues

- **Least privilege** para las credenciales del pipeline.
- **Secrets manager** (no variables en repo).
- **Scan de dependencias y SAST** en pipeline.
- **Policy as code**: bloquear infra no permitida (buckets públicos, reglas inseguras).
- **Auditoría**: registrar quién lanzó qué despliegue y cuándo.

### 10. Estrategias de rollback y recuperación

- **Rollback rápido:** volver a la versión anterior (tag anterior de imagen).
- **Blue/Green facilita rollback**: cambiar el tráfico de vuelta en segundos.
- **Rollback automatizado por métricas**: el pipeline o la plataforma detecta degradación y hace rollback automático.
- **Backups y snapshots**: para bases de datos, tener snapshot previo y plan de restore.

### 11. KPIs y métricas para medir éxito de la implementación

- **Lead time for changes**: tiempo desde commit hasta despliegue en producción.
- **Deployment frequency**: despliegues por día/semana.
- **Change failure rate**: % de despliegues que provocan incidentes.
- **MTTR (Mean Time To Recover)**: tiempo medio de restauración tras fallo.
- **SLO compliance**: % de tiempo en que se cumplen SLOs.

### 12. Errores frecuentes y cómo evitarlos

- **Desplegar sin pruebas en staging:** siempre validar en entorno identico.
- **No controlar el state de la infra:** usar backend remoto y locking para Terraform.
- **Hardcode de secretos:** usar Secret Manager / Vault.
- **No tener métricas de negocio en canary:** monitorizar solo infra puede esconder regresiones funcionales.
- **Acceso de pipeline con privilegios excesivos:** aplicar least privilege.

### 13. Checklist rápido para un despliegue en la nube seguro y confiable

- [ ] Código empaquetado con versión (tag).
- [ ] Artefacto publicado en registry seguro.
- [ ] IaC versionado y plan revisado en PR.
- [ ] Pipeline automatizado con tests y SAST.
- [ ] Secrets en gestor seguro.
- [ ] Estrategia de despliegue definida (canary/blue-green).
- [ ] Smoke tests y monitorización configurada.
- [ ] Rollback plan probado.
- [ ] Auditoría y notificaciones (Slack/email) activas.

### 14. Mini-casos de ejemplo (rápidos)

**Caso A — Startup (SaaS)**

- Infra: AWS ECS + RDS.
- Pipeline: GitHub Actions → build Docker → push ECR → deploy rolling (ECS).
- Estrategia: Canary (5% → 50% → 100%).
- Benefit: despliegues frecuentes, escalado automático.

**Caso B — Banco regulado**

- Infra: híbrida. Core on-prem, APIs en nube privada.
- Pipeline: IaC + approvals manuales, escaneo de compliance.
- Estrategia: Blue/Green con validación legal y auditoría.
- Benefit: cumplimiento y control físico de datos sensibles.

---

[🔼](#índice)

---

## **525. Implementación de un sitio web en AWS**

- **Sitio estático (recomendado si solo HTML/CSS/JS):** S3 + CloudFront + ACM + Route53. Barato, rápido y altamente escalable.
- **Aplicación dinámica (API/backend):** ECR + ECS Fargate (o EKS) + ALB + RDS + Secrets Manager + CloudFront opcional. Escalable y controlado.
- **Infra como Código:** Terraform (o CloudFormation/CDK) para reproducibilidad.
- **CI/CD:** GitHub Actions o CodePipeline para automatizar builds, despliegues y invalidaciones.

### Requisitos previos

1. Cuenta AWS con permisos suficientes para crear S3, CloudFront, IAM, ECR, ECS, RDS, Route53.
2. AWS CLI configurado (aws configure) o credenciales en CI.
3. (Opcional) Dominio en Route53 o control de DNS en tu proveedor.
4. Terraform (si vas a usar IaC) y GitHub (si vas a usar Actions).

### Parte A — **Implementar un sitio estático (S3 + CloudFront)**

#### 1) Idea / arquitectura

- Contenido estático (index.html, CSS, JS, imágenes) almacenado en **S3**.
- **CloudFront** delante para CDN, HTTPS y protección.
- Certificado TLS con **ACM** (en us-east-1 para CloudFront).
- **Route53** apuntando al distribution.
- Habilitar logging, WAF y políticas de bloqueo Público en S3.

#### 2) Pasos manuales (rápido)

1. Crear bucket S3:

```bash
aws s3api create-bucket \
  --bucket mi-sitio-ejemplo.com \
  --region us-east-1 \
  --create-bucket-configuration LocationConstraint=us-east-1
```

2. Bloquear acceso público y activar versioning/logging:

```bash
aws s3api put-public-access-block --bucket mi-sitio-ejemplo.com \
  --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

aws s3api put-bucket-versioning --bucket mi-sitio-ejemplo.com --versioning-configuration Status=Enabled
```

3. Subir sitio:

```bash
aws s3 sync ./site/ s3://mi-sitio-ejemplo.com --acl private
```

4. Crear Origin Access Identity (OAI) para CloudFront y restringir bucket al OAI:

```bash
aws cloudfront create-cloud-front-origin-access-identity --cloud-front-origin-access-identity-config CallerReference=$(date +%s),Comment="OAI for mi-sitio-ejemplo"
# luego ajusta policy del bucket para permitir GetObject al OAI
```

5. Crear certificado ACM en us-east-1 y validar (Route53 validation recomendado).
6. Crear distribución CloudFront con origin apuntando al bucket y asociar el certificado TLS.
7. Crear registro DNS en Route53 tipo A (alias) apuntando a la distribución CloudFront.

> **Nota:** CloudFront requiere que el certificado ACM esté en **us-east-1**, aunque el bucket esté en otra región.

#### 3) Ejemplo Terraform (estático, reducido)

Archivo `main.tf` (simplificado — deberás ajustar variables y providers):

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "site" {
  bucket = var.domain_name
  acl    = "private"
  force_destroy = true
}

resource "aws_s3_bucket_public_access_block" "block" {
  bucket = aws_s3_bucket.site.id
  block_public_acls = true
  ignore_public_acls = true
  block_public_policy = true
  restrict_public_buckets = true
}

resource "aws_cloudfront_origin_access_identity" "oai" {
  comment = "OAI for ${var.domain_name}"
}

resource "aws_s3_bucket_policy" "allow_cloudfront" {
  bucket = aws_s3_bucket.site.id
  policy = data.aws_iam_policy_document.cf_to_s3.json
}

data "aws_iam_policy_document" "cf_to_s3" {
  statement {
    actions = ["s3:GetObject"]
    principals {
      type = "AWS"
      identifiers = [aws_cloudfront_origin_access_identity.oai.iam_arn]
    }
    resources = ["${aws_s3_bucket.site.arn}/*"]
  }
}

resource "aws_acm_certificate" "cert" {
  domain_name = var.domain_name
  validation_method = "DNS"
  lifecycle { create_before_destroy = true }
}

# Validación con Route53 (asume zona disponible)
resource "aws_route53_record" "cert_validation" {
  for_each = {
    for dvo in aws_acm_certificate.cert.domain_validation_options : dvo.domain_name => {
      name  = dvo.resource_record_name
      type  = dvo.resource_record_type
      value = dvo.resource_record_value
    }
  }
  zone_id = var.zone_id
  name    = each.value.name
  type    = each.value.type
  records = [each.value.value]
  ttl     = 60
}

resource "aws_acm_certificate_validation" "cert_validation" {
  certificate_arn = aws_acm_certificate.cert.arn
  validation_record_fqdns = [for record in aws_route53_record.cert_validation : record.fqdn]
}

resource "aws_cloudfront_distribution" "cdn" {
  enabled = true
  origins {
    domain_name = aws_s3_bucket.site.bucket_regional_domain_name
    origin_id   = "s3-${aws_s3_bucket.site.id}"
    s3_origin_config { origin_access_identity = aws_cloudfront_origin_access_identity.oai.cloudfront_access_identity_path }
  }
  default_cache_behavior {
    allowed_methods = ["GET","HEAD"]
    cached_methods  = ["GET","HEAD"]
    target_origin_id = "s3-${aws_s3_bucket.site.id}"
    viewer_protocol_policy = "redirect-to-https"
    forwarded_values { query_string = false, cookies { forward = "none" } }
  }
  viewer_certificate {
    acm_certificate_arn = aws_acm_certificate.cert.arn
    ssl_support_method  = "sni-only"
  }
  # ... (precio/optimización/policies omitted)
}
```

Variables: `domain_name`, `zone_id`. Después `terraform init`, `plan`, `apply`. Luego subir archivos a S3 (aws s3 sync).

#### 4) Pipeline CI/CD (GitHub Actions) — desplegar estático + invalidar CloudFront

Archivo `.github/workflows/deploy-static.yml` (esqueleto):

```yaml
name: Deploy static site

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-region: us-east-1
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }} # o use AWS_ACCESS_KEY_ID / SECRET

      - name: Sync to S3
        run: aws s3 sync ./site s3://mi-sitio-ejemplo.com --delete

      - name: Create CloudFront invalidation
        run: aws cloudfront create-invalidation --distribution-id ${{ secrets.CLOUDFRONT_ID }} --paths "/*"
```

Guarda credenciales y IDs en Github Secrets.

### Parte B — **Implementar una app dinámica (ECS Fargate + RDS)**

#### 1) Arquitectura recomendada

- **ECR** (repositorio) para imágenes Docker.
- **ECS Fargate** con Service + Task Definitions (sin gestionar servidores).
- **ALB** (Application Load Balancer) público frente a ECS para HTTP/HTTPS.
- **RDS (Postgres/MySQL)** en subnets privadas para persistencia.
- **Secrets Manager / Parameter Store** para credenciales.
- **CloudWatch + X-Ray** para logs, métricas y trazas.
- **CloudFront** opcional (para CDN frente a ALB).
- **VPC privada con subnets públicas/privadas y NAT**.

#### 2) Flujo de despliegue manual (resumen)

1. Crear ECR repo y construir/push imagen:

```bash
aws ecr create-repository --repository-name mi-app
$(aws ecr get-login --no-include-email --region us-east-1)
docker build -t mi-app:latest .
docker tag mi-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/mi-app:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/mi-app:latest
```

2. Crear RDS (PG) en subnets privadas (multi-AZ opcional).
3. Crear Cluster ECS, Task Definition (apuntar al ECR image), Service con ALB target group.
4. Configurar seguridad: Security Groups: ALB (80/443) público, ECS tasks bloqueadas salvo desde ALB; RDS aceptando solo desde tasks/security group.
5. Configurar AutoScaling en ECS (target tracking CPU).
6. Configurar logging (CloudWatch) en Task Definition.

#### 3) Ejemplo Terraform (esqueleto, simplificado)

Un despliegue completo es largo — a continuación fragmentos clave (debes complementarlos):

**Provider y VPC omitted** — asume `aws_vpc.main`, `aws_subnet.private[*]`, `aws_subnet.public[*]`.

ECR:

```hcl
resource "aws_ecr_repository" "app" {
  name = "mi-app"
}
```

Task Role / Task definition:

```hcl
resource "aws_iam_role" "ecs_task_execution" {
  name = "ecsTaskExecutionRole"
  assume_role_policy = data.aws_iam_policy_document.ecs_task_assume.json
}
# attach policy AmazonECSTaskExecutionRolePolicy
# create task definition
resource "aws_ecs_task_definition" "app" {
  family                   = "mi-app"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "512"
  memory                   = "1024"
  execution_role_arn       = aws_iam_role.ecs_task_execution.arn
  container_definitions = jsonencode([
    {
      name = "app"
      image = "${aws_ecr_repository.app.repository_url}:latest"
      portMappings = [{ containerPort = 8080, hostPort = 8080, protocol = "tcp" }]
      logConfiguration = {
        logDriver = "awslogs"
        options = {
          awslogs-group = "/ecs/mi-app"
          awslogs-region = "us-east-1"
          awslogs-stream-prefix = "app"
        }
      }
      environment = [
        { name = "DATABASE_URL", value = "postgres://..." } # prefer secrets
      ]
    }
  ])
}
```

ECS Service + ALB:

```hcl
resource "aws_lb" "alb" { ... }
resource "aws_lb_target_group" "tg" { port = 8080, protocol = "HTTP", vpc_id = aws_vpc.main.id }
resource "aws_lb_listener" "http" { load_balancer_arn = aws_lb.alb.arn, port = 80, default_action { type = "forward", target_group_arn = aws_lb_target_group.tg.arn } }

resource "aws_ecs_service" "app" {
  name            = "mi-app-svc"
  cluster         = aws_ecs_cluster.cluster.id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = 2
  launch_type     = "FARGATE"
  network_configuration { subnets = aws_subnet.private[*].id, security_groups = [aws_security_group.ecs_sg.id] }
  load_balancer { target_group_arn = aws_lb_target_group.tg.arn, container_name = "app", container_port = 8080 }
  depends_on = [aws_lb_listener.http]
}
```

RDS (simplificado):

```hcl
resource "aws_db_instance" "db" {
  identifier = "mi-db"
  engine = "postgres"
  instance_class = "db.t3.micro"
  allocated_storage = 20
  name = "miappdb"
  username = var.db_user
  password = var.db_password
  skip_final_snapshot = true
  vpc_security_group_ids = [aws_security_group.rds_sg.id]
  db_subnet_group_name = aws_db_subnet_group.dbsubnet.name
}
```

> Este esqueleto muestra relaciones. Para prod deberás añadir backups, multi-AZ, maintenance windows y monitorización.

#### 4) CI/CD para app dinámica — GitHub Actions (esqueleto)

`.github/workflows/deploy-ecs.yml`:

```yaml
name: Build and deploy to ECR & ECS

on:
  push:
    branches: [main]

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-region: us-east-1
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}

      - name: Login to ECR
        run: aws ecr get-login-password | docker login --username AWS --password-stdin ${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.us-east-1.amazonaws.com

      - name: Build and push Docker image
        run: |
          IMAGE=${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.us-east-1.amazonaws.com/mi-app:${{ github.sha }}
          docker build -t $IMAGE .
          docker push $IMAGE

      - name: Deploy to ECS (update service)
        uses: jwalton/gh-ecr-push@v2 # or use aws ecs update-service via aws-cli script
        # or run aws ecs update-service --cluster ... --service ... --force-new-deployment
```

En deploy puedes usar `aws ecs update-service --cluster my-cluster --service mi-app-svc --force-new-deployment` para forzar que ECS reemplace tareas con la nueva imagen (si la task definition usa \:latest, es preferible registrar una nueva revision de task definition con la nueva image tag y actualizar el service).

### Seguridad esencial (aplica a ambos enfoques)

- **Principio de menor privilegio (IAM)**: crea roles específicos para CI, tareas ECS y lambdas. Evita usar credenciales raíz.
- **HTTPS/TLS**: ACM para CloudFront/ALB.
- **S3**: bloquear acceso público, usar OAI/OAC.
- **Secrets**: AWS Secrets Manager o Parameter Store + encriptación KMS. Nunca hardcodees secretos.
- **WAF**: reglas para OWASP, bloqueo de bots y rate limiting.
- **Shield / DDoS**: Shield Standard está activado por defecto para CloudFront; Shield Advanced si necesitas más protección.
- **Detect & respond**: habilita CloudTrail, GuardDuty, AWS Config y Alertas (SNS/CloudWatch Alarms).
- **Registro de auditoría**: conserva logs de acceso (CloudFront logs, ALB logs, S3 access logs).

### Monitorización y observabilidad

- **CloudWatch Logs/metrics**: definir alarmas para 5xx, latencia, error rate.
- **X-Ray**: tracing de requests para identificar cuellos de botella.
- **Dashboards**: métricas de rendimiento, utilización de tareas ECS, RDS CPU/I/O.
- **SLO/SLA**: definir SLOs y configurar alertas que lleguen a on-call (SNS -> PagerDuty).

### Optimización de costes

- **Static site**: usar CloudFront reduce egress y latencia; S3/CloudFront suele ser muy barato.
- **ECS Fargate**: rightsizing (CPU/memoria por tarea), autoscaling por demanda.
- **RDS**: elegir tamaño correcto, evaluar Aurora Serverless para cargas variables.
- **Savings Plans / Reserved Instances** para cargas estables.
- **Monitorizar egress y API calls**; optimizar cache con CloudFront y TTLs.

### Checklist final (antes de abrir al público)

- [ ] Certificado TLS válido (ACM) y obligatorio HTTPS.
- [ ] DNS (Route53) configurado con alias a CloudFront/ALB.
- [ ] Seguridad: IAM roles mínimos, S3 sin acceso público, Secrets Manager configurado.
- [ ] Logging habilitado (CloudFront/ALB/CloudTrail/CloudWatch).
- [ ] Alarms para errores y latencia (SNS + escalado).
- [ ] Backup / snapshot programado para RDS.
- [ ] Pruebas de carga y validación (smoke tests + canary).
- [ ] WAF si exposición pública sensible.
- [ ] Política de respuesta a incidentes y runbooks listos.

### Troubleshooting (problemas comunes y soluciones)

- **404/403 en CloudFront origin:** verifica bucket policy y que CloudFront OAI tenga permiso `s3:GetObject`.
- **Certificado no válido:** ACM certificado en región equivocada (CloudFront requiere us-east-1).
- **Errores 5xx en ALB -> ECS:** revisa logs de las tareas en CloudWatch, verifica health checks y que el container escucha en el puerto correcto.
- **Latency alta en DB:** comprobar queries lentas, escalado de RDS, índices, usar read replicas.
- **Costos inesperados:** activa AWS Budgets y revisa tags para identificar proyecto.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**                                          | **Siguiente 7**      |
| ------------------ | ---------------------------------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./14_5_Understand_the_concept_of_Serverless.md) | [⏩](./14_7_SaaS.md) |
