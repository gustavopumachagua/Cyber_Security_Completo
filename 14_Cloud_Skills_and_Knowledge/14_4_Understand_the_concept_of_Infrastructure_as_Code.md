| **Inicio**         | **atrás 3**                                                              | **Siguiente 5**                                      |
| ------------------ | ------------------------------------------------------------------------ | ---------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_3_Understand_the_differences_between_cloud_and_on_premises.md) | [⏩](./14_5_Understand_the_concept_of_Serverless.md) |

---

## **Índice**

| Temario                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------ |
| [520. ¿Qué es Infraestructura como Código? - Explicación de IaC - AWS](#520-qué-es-infraestructura-como-código---explicación-de-iac---aws) |
| [521. What is infrastructure as code (IaC)? - Azure DevOps](#521-what-is-infrastructure-as-code-iac---azure-devops)                        |

# **Comprender el concepto de Infraestructura como Código**

## **520. ¿Qué es Infraestructura como Código? - Explicación de IaC - AWS**

![Comprender el concepto de Infraestructura como Código](/img/14_Cloud_Skills_and_Knowledge/Infraestructura.jpg "Comprender el concepto de Infraestructura como Código")

### 🔹 ¿Qué es la infraestructura como código (IaC)?

La **infraestructura como código (IaC)** es la práctica de **definir, aprovisionar y gestionar la infraestructura de TI mediante archivos de código** (YAML, JSON, HCL, etc.) en lugar de configurarla manualmente en consolas gráficas o con scripts ad hoc.

👉 Ejemplo:

En lugar de entrar al panel de AWS y crear manualmente una máquina EC2, una VPC y un balanceador de carga, puedes describir esa infraestructura en un archivo **Terraform** o **CloudFormation**, y luego desplegarla con un comando.

Archivo Terraform (ejemplo simple):

```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "Servidor-App"
  }
}
```

Con este código, cada vez que lo ejecutes, tendrás **la misma máquina creada automáticamente**.

### 🔹 ¿Cuáles son los beneficios de la IaC?

1. **Consistencia y reproducibilidad**

   - Evitas el error humano. La misma definición de infraestructura se puede usar en desarrollo, pruebas y producción.
   - Ejemplo: una app que necesita 3 instancias y un balanceador. En lugar de configurarlas manualmente en cada entorno, un solo archivo IaC asegura que todos sean idénticos.

2. **Velocidad y eficiencia**

   - Aprovisionar infra con un comando en minutos vs. días configurando a mano.
   - Ejemplo: levantar un clúster Kubernetes con IaC toma minutos; a mano podría tardar horas o días.

3. **Escalabilidad automática**

   - Puedes definir “quiero 10 servidores” en vez de crearlos manualmente uno por uno.
   - Ejemplo: en Terraform cambias `count = 3` a `count = 10` y listo: el sistema despliega más instancias.

4. **Trazabilidad y versionado**

   - El código vive en Git, con control de cambios y auditoría.
   - Ejemplo: si alguien cambia la configuración de red, puedes ver quién lo hizo, cuándo, y revertirlo.

5. **Integración con CI/CD**

   - IaC se integra con pipelines, desplegando infra junto con la aplicación.
   - Ejemplo: cada vez que merges en main, se despliega tanto la app como la infraestructura necesaria automáticamente.

### 🔹 ¿Cómo funciona la infraestructura como código?

El ciclo de vida típico de IaC es así:

1. **Definición**

   - Escribes la infraestructura en un archivo declarativo (Terraform, CloudFormation, Ansible, Pulumi).

2. **Planificación**

   - El sistema genera un _plan de ejecución_ (qué va a crear, modificar o destruir).

3. **Ejecución (aplicar)**

   - Se crea o actualiza la infraestructura en la nube/local.

4. **Gestión continua**

   - El código puede actualizarse y volver a aplicarse para modificar la infraestructura.

👉 Ejemplo con Terraform:

- `terraform plan` → muestra qué va a cambiar.
- `terraform apply` → crea/modifica la infra según el código.

### 🔹 ¿Cuál es el papel de la IaC en DevOps?

La IaC es **fundamental en DevOps** porque:

- **Automatiza** el aprovisionamiento de entornos, reduciendo fricción entre Dev y Ops.
- Permite que **infraestructura y aplicación viajen juntas** en el pipeline de CI/CD.
- Fomenta el enfoque **inmutable**: en lugar de parchear servidores manualmente, destruyes y vuelves a desplegar desde el código.

👉 Ejemplo:

En un pipeline DevOps, cuando un desarrollador hace un _merge_, el pipeline no solo despliega la app en un contenedor, sino que también crea (con IaC) la base de datos, la red y el balanceador en la nube.

### 🔹 ¿Cómo puede AWS satisfacer sus necesidades de IaC?

AWS ofrece varias herramientas para IaC:

1. **AWS CloudFormation**

   - Servicio nativo de AWS. Permite definir recursos en YAML/JSON.
   - Ejemplo: archivo YAML que crea automáticamente una VPC, subredes, instancias EC2 y un balanceador.

2. **AWS CDK (Cloud Development Kit)**

   - Permite definir infraestructura con lenguajes de programación (Python, TypeScript, Java).
   - Ejemplo: en Python puedes escribir una clase que defina una API Gateway y un Lambda en lugar de usar YAML.

3. **Integración con Terraform y Ansible**

   - Aunque no son nativos de AWS, se integran muy bien.
   - Terraform es muy usado cuando trabajas con **multi-cloud** (AWS, Azure, GCP).

4. **Automatización con CI/CD en AWS**

   - Puedes usar **AWS CodePipeline** o GitHub Actions para aplicar CloudFormation o Terraform al hacer cambios en el repositorio.

✅ **Conclusión**:

La **infraestructura como código (IaC)** es clave en la nube moderna porque **convierte la infraestructura en software**: automatizable, auditable, repetible y escalable. Con IaC y DevOps, aprovisionar un entorno completo pasa de ser un proceso manual y lento a un **pipeline reproducible en minutos**.

---

[🔼](#índice)

---

## **521. What is infrastructure as code (IaC)? - Azure DevOps**

**Definición breve:**

La **Infraestructura como Código (IaC)** es la práctica de describir y gestionar infraestructura (redes, máquinas, balanceadores, bases de datos, políticas IAM, etc.) mediante ficheros de código legibles y versionables en lugar de realizar cambios manuales en consolas o servidores. Con IaC la infraestructura se trata como _software_: la declaras, la versionas, la revisas (code review), la pruebas y la despliegas de forma automática.

### Por qué IaC elimina la configuración manual y asegura coherencia

1. **Declarativo y reproducible:** defines el estado deseado (por ejemplo: "3 instancias con este AMI y este security group") y la herramienta lo realiza. Cualquier ejecución parte de la misma definición, evitando deriva de configuración (configuration drift).
2. **Versionado y auditoría:** el código IaC vive en Git — cada cambio queda registrado, puedes revisar quién cambió qué y revertir fácilmente.
3. **Planificación previa:** comandos como `terraform plan` o `cloudformation change-set` muestran exactamente qué se va a cambiar antes de aplicar nada, reduciendo errores humanos.
4. **Automatización en pipelines:** la creación/actualización se hace por CI/CD, con pruebas y gates, no por clicks de consola.
5. **Pruebas y entornos replicables:** el mismo código genera entornos idénticos (dev/staging/prod) — lo que pruebas en staging se reproduce en prod.
6. **Inmutabilidad y rollbacks:** prácticas como “recreate rather than mutate” facilitan revertir a un estado conocido si algo falla.

### Modelos y paradigmas: declarativo vs imperativo

- **Declarativo (recomendado):** describes _qué_ quieres (Terraform, CloudFormation, Kubernetes YAML). La herramienta calcula _cómo_ hacerlo. Mejor para consistencia.
- **Imperativo:** describes _pasos_ para alcanzar el estado (scripts Bash, `aws-cli` paso a paso). Más frágil y propenso a errores humanos.

### Herramientas comunes (ejemplos)

- **Terraform** (HashiCorp) — _multi-cloud_, HCL declarativo.
- **CloudFormation / CDK (AWS)** — nativo AWS (YAML/JSON o CDK con idiomas).
- **Azure ARM / Bicep** — nativo Azure.
- **Ansible** — gestión de configuración + provisioning (puede ser declarativo/imperativo).
- **Pulumi** — IaC con lenguajes de programación (TypeScript, Python, Go).
- **Kubernetes manifests / Helm** — IaC para orquestación de contenedores.

### Ejemplos prácticos (código) — para ver cómo evita la configuración manual

#### Ejemplo 1 — Terraform (archivo `main.tf`) — crear un bucket S3 y una instancia EC2 (simplificado)

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "app_bucket" {
  bucket = "mi-app-bucket-ejemplo-12345"
  acl    = "private"
  tags = {
    Environment = "prod"
    Owner       = "equipo-dev"
  }
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name = "web-server"
    Env  = "prod"
  }
}
```

Flujo:

```
git add main.tf
git commit -m "Añadir S3 y EC2"
terraform init
terraform plan   # revisar cambios
terraform apply  # aplicar automáticamente
```

Cada vez que ejecutes `terraform apply` en otro entorno con el mismo código, obtendrás la **misma** configuración.

#### Ejemplo 2 — CloudFormation (YAML) — crear un bucket S3 (simplificado)

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: S3 bucket demo

Resources:
  AppBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: mi-app-bucket-ejemplo-12345
      AccessControl: Private
      Tags:
        - Key: Environment
          Value: prod
```

Workflow: crea un _change set_ y aplica — CloudFormation aplica los cambios de forma declarativa.

#### Ejemplo 3 — Ansible (playbook) — instalar Nginx en servidores (configuración reproducible)

```yaml
- name: Instalar nginx en servidores web
  hosts: webservers
  become: yes

  tasks:
    - name: Instalar nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Asegurar que nginx está habilitado y en ejecución
      systemd:
        name: nginx
        enabled: yes
        state: started
```

Ansible aplica los mismos pasos en todos los hosts del inventario: coherencia garantizada sin SSH manual.

### IaC + DevOps: integración y flujo recomendado

1. **Repositorio Git** para infraestructura (`infrastructure/terraform`, `infrastructure/cfn`).
2. **Branching + Pull Requests**: cambios en IaC se revisan como código (code review).
3. **CI Pipeline (ej. GitHub Actions / GitLab CI / Jenkins)**:

   - `terraform fmt` / `tflint` / `cfn-lint` — linters.
   - `terraform plan` o `aws cloudformation validate` — generar plan.
   - Revisión humana del plan (PR) → si OK → `terraform apply` en workspace protegido (o mediante pipeline con credenciales).

4. **State management**: usar backend remoto y bloqueos (Terraform Cloud, S3 + DynamoDB lock) para evitar concurrencia.
5. **Testing**: pruebas unitarias (eg. `terratest`), pruebas de integración en entornos temporales.
6. **Policy as Code**: herramientas como `OPA / Conftest`, `Sentinel` o `Guardrails` para validar políticas antes de aplicar.

### Buenas prácticas para garantizar coherencia y seguridad con IaC

- **Usar declarativo siempre que sea posible.**
- **Versionar todo**: código IaC + variables + módulos.
- **Revisiones de PR obligatorias** antes de aplicar cambios.
- **Automatizar `plan` y mostrar el plan en PR** para que reviewers lo verifiquen.
- **Backend remoto para estado + locking** (evita sobrescritura de state).
- **Módulos/plantillas reutilizables**: estandariza stacks (network, security, compute).
- **Variables secretas en vaults/secret managers**, nunca en repositorios.
- **Testear en entornos efímeros** (spin-up, smoke tests, teardown).
- **Policy-as-code** para bloquear configuraciones inseguras (buckets públicos, IAM _wildcards_).
- **Tagging obligatorio** (propósito, owner, environment) para gobernanza y facturación.
- **Documentar runbooks** para operaciones y rollback.

### Errores comunes y cómo evitarlos

- **Editar en consola en producción** → produce deriva de configuración. _Solución:_ prohibir cambios manuales; cualquier cambio debe pasar por IaC.
- **No usar locking del state** → corrupción de estado si dos usuarios aplican simultáneamente. _Solución:_ backend con locking.
- **Almacenar secretos en texto plano** → exposiciones. _Solución:_ integrate con Secrets Manager / Vault.
- **No revisar `plan` antes de aplicar** → cambios inesperados. _Solución:_ exigir revisión PR del plan.
- **Lock-in por módulos locales sin abstracción** → difícil portar entre clouds. _Solución:_ escribir módulos con buena abstracción y usar herramientas multi-cloud si es necesario.

### Checklist mínimo para empezar a evitar la configuración manual

- [ ] Poner la definición de infra en Git (repositorio).
- [ ] Elegir herramienta IaC (Terraform / CloudFormation / Bicep / Pulumi).
- [ ] Configurar backend remoto y locking del state.
- [ ] Definir pipeline CI que ejecute linter y `plan`.
- [ ] Reglas de revisión de PR para cambios de infra.
- [ ] Política de secretos (Vault / Secret Manager).
- [ ] Crear módulos/base templates reutilizables.
- [ ] Implementar políticas automáticas (policy-as-code).
- [ ] Hacer pruebas en entornos efímeros antes de prod.
- [ ] Deshabilitar cambios manuales en consolas (o marcar excepciones con control).

### Mini-workflow ejemplo con GitHub Actions + Terraform (resumen)

1. Dev crea PR con cambios en `infra/` (ej. `add-s3-bucket`).
2. CI ejecuta:

   - `terraform fmt` / `tflint`
   - `terraform init` (backend remoto)
   - `terraform plan -out plan.tfplan`
   - Publica `plan` como artefacto/comment en PR.

3. Reviewer (security/infra) revisa plan y aprueba PR.
4. Merge dispara job protegido que ejecuta `terraform apply plan.tfplan` usando credenciales almacenadas en GitHub Secrets.
5. Estado queda sincronizado en backend; logs y cambios quedan auditados en Git.

### Conclusión — por qué IaC es la forma correcta de **evitar la configuración manual** y asegurar coherencia

- IaC convierte la infraestructura en **software**: repetible, testeable y versionable.
- Elimina el error humano de clicks y pasos manuales, reduciendo drift y fallos en producción.
- Con prácticas DevOps (CI/CD, revisiones, policies-as-code) garantizas que cada cambio sea visible, aprobado y rastreable.
- En resumen: IaC no solo **evita** la configuración manual — la previene, la audita y la controla.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**                                                              | **Siguiente 5**                                      |
| ------------------ | ------------------------------------------------------------------------ | ---------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_3_Understand_the_differences_between_cloud_and_on_premises.md) | [⏩](./14_5_Understand_the_concept_of_Serverless.md) |
