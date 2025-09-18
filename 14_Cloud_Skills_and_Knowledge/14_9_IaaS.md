| **Inicio**         | **atrás 8**          | **Siguiente 10**         |
| ------------------ | -------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./14_8_PaaS.md) | [⏩](./14_10_Private.md) |

---

## **Índice**

| Temario                                    |
| ------------------------------------------ |
| [530. ¿Qué es IaaS?](#530-qué-es-iaas)     |
| [531. IaaS explicado](#531-iaas-explicado) |

# **IaaS**

## **530. ¿Qué es IaaS?**

![PaaS](/img/14_Cloud_Skills_and_Knowledge/IaaS.png "PaaS")

**Respuesta corta:** IaaS es un modelo de computación en la nube que entrega recursos de infraestructura (máquinas virtuales, redes, almacenamiento, balanceadores, IPs públicas, etc.) como servicios bajo demanda. En lugar de comprar y mantener hardware físico, **provisiónas, escalas y pagas** por esos recursos a un proveedor cloud (AWS, Azure, GCP, etc.).

### 🔎 Definición: “Infraestructura como Servicio” (IaaS)

IaaS proporciona **capacidad de cómputo virtualizada** y recursos de red/almacenamiento accesibles por API o consola. El proveedor administra el _hardware físico_, la virtualización y la disponibilidad; tú gestionas el sistema operativo, runtime, middleware y aplicaciones. Es el nivel más bajo de abstracción cloud (más control que PaaS/SaaS).

**Analogía:** en vez de comprar un servidor físico y un rack, alquilas una máquina virtual con CPU/RAM/disco configurables y la administras tú.

### ⚙️ ¿Cómo funciona IaaS? (flujo operativo)

1. **Provisionamiento:** pides recursos (ej. VM con 4 vCPU y 16 GB RAM, disco SSD de 200 GB, IP pública) vía consola, CLI o API.
2. **Configuración:** instalas SO, parches, herramientas, tus aplicaciones o contenedores.
3. **Conectividad y seguridad:** configuras redes virtuales (VPC/VNet), subredes, security groups, reglas de firewall y ACLs.
4. **Escalado y autoscaling:** puedes aumentar o reducir instancias manual o automáticamente según métricas (CPU, tráfico, latencia).
5. **Almacenamiento y backup:** adjuntas volúmenes persistentes, configuras snapshots/backups y replicación.
6. **Gestión y monitorización:** integras logs, métricas, alertas y herramientas de administración (patching, CM, etc.).

**Ejemplo técnico simple:**

- Crear una VM (EC2/Compute Engine/VM) → instalar Nginx → conectar a una base de datos gestionada o a otra VM → añadir un Load Balancer delante y un Auto Scaling Group que suba instancias cuando CPU > 60%.

### ✅ Beneficios de IaaS

- **Control y flexibilidad:** administras OS, middleware y apps; ideal si necesitas personalización.
- **Rapidez de aprovisionamiento:** servidores disponibles en minutos sin compra de hardware.
- **Escalabilidad:** scale up/down dinámicamente; pagas por lo que usas.
- **Costes CAPEX → OPEX:** sin gran inversión inicial; facturación por uso (pay-as-you-go).
- **Acceso a servicios avanzados:** integración sencilla con servicios cloud (monitoring, AI, CDN).
- **Recuperación y redundancia:** snapshots, réplicas y distribución multi-AZ/region facilitan DR.
- **Portabilidad (relativa):** máquinas virtuales e imágenes pueden moverse entre proveedores con trabajo; más portable que soluciones muy propietarias PaaS.

### ⚠️ Limitaciones y riesgos (a considerar)

- **Responsabilidad operativa:** tú gestionas SO, parches, parcheo de seguridad y hardening.
- **Costos si no se gestiona:** instancias sobrantes, EBS no usados, IPs elásticas pueden generar factura inesperada.
- **Complejidad de red y seguridad:** configurar redes y IAM correctamente requiere buenas prácticas.
- **Vendor lock-in parcial:** uso intensivo de servicios propietarios (p. ej. snapshots orquestados de un provider) puede dificultar migración.

### 🧾 Escenarios comerciales comunes de IaaS (con ejemplos)

1. **Datacenter en la nube / Rehosting (lift-and-shift)**

   - Migrar servidores on-prem a VMs en la nube para retirar el datacenter.
   - _Ejemplo:_ una empresa de retail levanta sus servidores de ERP en instancias EC2 para ahorrar costes de centro físico.

2. **Ambientes de desarrollo, prueba y staging**

   - Provisionar máquinas temporales para CI/CD, tests automáticos y entornos efímeros.
   - _Ejemplo:_ cada PR crea un environment con VMs y DB temporal para pruebas de integración.

3. **Aplicaciones legacy o con requisitos especiales**

   - Apps que requieren control de SO, librerías nativas o drivers específicos.
   - _Ejemplo:_ software de manufactura que necesita acceso a controladores hardware virtualizados.

4. **Big Data y procesamiento batch**

   - Clústeres Hadoop/Spark en VMs configurables para jobs de ETL/analytics.
   - _Ejemplo:_ lanzar 100 nodos en un clúster por 4 horas para un job ETL intensivo.

5. **Disaster Recovery (DR) y replicación**

   - Réplica de entornos on-prem o en otra región para recuperación ante fallos.
   - _Ejemplo:_ réplicas de DB y AMIs a otra región con failover plan.

6. **Infraestructura temporal / picos de carga**

   - Campañas de marketing, Black Friday, lanzamientos.
   - _Ejemplo:_ auto-scaling de servidores web que soporta picos del 500% en tráfico.

7. **Workloads con requisitos regulatorios**

   - Mantener control de OS y logs para auditoría; ubicar VMs en regiones específicas por soberanía de datos.
   - _Ejemplo:_ VMs en región local para cumplir data-residency de regulación local.

### 🔧 Comparación rápida: IaaS vs PaaS vs SaaS

| Nivel    |                    Qué gestionas tú | Qué gestiona el proveedor              | Ejemplo             |
| -------- | ----------------------------------: | -------------------------------------- | ------------------- |
| **IaaS** | OS, runtime, apps, seguridad del SO | hardware, virtualización, red física   | EC2, Compute Engine |
| **PaaS** |          Código y configuración app | todo lo de IaaS + runtime y middleware | Heroku, App Engine  |
| **SaaS** |                       Uso de la app | todo lo anterior + producto            | Gmail, Salesforce   |

### 🛠 Buenas prácticas al usar IaaS

1. **Automatiza con IaC** (Terraform/CloudFormation) — no clicks.
2. **Etiquetado (tagging)** por proyecto, ambiente y coste para FinOps.
3. **Autoscaling + Policies** para ahorrar costes y responder al tráfico.
4. **Imagen base hardened** (AMIs/VM images con parches y configuraciones seguras).
5. **Backups y snapshots** regulares con retention definida.
6. **Security groups / NSGs / firewalls** restrictivos por defecto (`deny all` → permitir lo necesario).
7. **Monitorización** (metrics, logs, alertas) y centralizar en SIEM/Splunk/CloudWatch.
8. **Rotación de claves / gestión de secretos** con Secret Manager / Vault.
9. **Patching programado y automatizado** (SSM, Update Manager).
10. **Pruebas de DR y simulacros** periódicos.

### 💸 Gestión de costes (recomendaciones)

- Usa **instancias reservadas** o **savings plans** para cargas estables.
- Habilita **autoscaling** y apaga entornos de dev fuera de horas.
- Revisa **volúmenes no adjuntos** y snapshots viejos.
- Aplica **tagging** y reportes de coste por proyecto.
- Establece **budgets y alertas** (AWS Budgets / Azure Cost Alerts).

### 🧩 Ejemplo práctico (end-to-end, simplificado)

**Objetivo:** desplegar app web legacy en IaaS.

1. **Provisionar VPC** con subredes públicas + privadas.
2. **Crear una instancia VM (t2.medium)** en subred privada para app, y otra pública con nginx como reverse proxy (o ALB).
3. **Configurar Security Group:** ALLOW 443/80 desde Internet al proxy; ALLOW 5432 solo desde la VM app (si DB está en otra VM).
4. **Adjuntar EBS de 200GB** para datos persistentes; establecer snapshots diarios.
5. **Instalar y configurar SO/app** (patch, users, servicio systemd).
6. **Configurar monitoreo** (agent CloudWatch/Datadog).
7. **Crear un AMI** una vez el sistema esté listo para reproducir nuevas instancias.
8. **Configurar Auto Scaling Group** (mín 2, máx 10) con health checks y políticas por CPU.
9. **Pruebas y DR:** restaurar desde snapshot a otra zona/región y validar.

### ✅ Cuándo elegir IaaS (criterios de decisión)

- Necesitas control profundo del SO o entornos especiales.
- Tienes aplicaciones legacy que no se pueden refactorizar rápido.
- Requieres portabilidad y control sobre la configuración de red.
- Quieres gestionar tu propia seguridad y parcheo por requisitos regulatorios.
- Necesitas escalabilidad elástica pero con control operativo.

### 📌 Conclusión

IaaS es la opción ideal cuando necesitas **control, flexibilidad y capacidad para manejar entornos personalizados** pero quieres evitar la compra y operación de hardware físico. Proporciona la capa base sobre la que puedes construir PaaS y SaaS, y es especialmente útil para migraciones, workloads legacy, análisis de datos y estrategias DR. Para maximizar sus beneficios: automatiza, aplica políticas de seguridad estrictas y controla costes con prácticas FinOps.

---

[🔼](#índice)

---

## **531. IaaS explicado**

**IaaS (Infrastructure as a Service)** es el modelo cloud que te entrega recursos de infraestructura (máquinas virtuales, red virtual, almacenamiento, IPs, balanceadores, etc.) bajo demanda. El proveedor mantiene el hardware físico y la virtualización; tú gestionas el sistema operativo, el middleware y las aplicaciones. Es la capa más flexible (y operativa) de la nube.

### 1) ¿Cómo funciona IaaS? (flujo práctico)

1. **Provisionas** recursos por consola, CLI o API (p. ej. “crea una VM con 4 vCPU, 16 GB RAM y un volumen de 100 GB”).
2. **Configuras** el sistema operativo y software (patching, usuarios, agentes de monitorización).
3. **Conectas** redes virtuales (VPC/VNet), subredes y security groups/firewalls.
4. **Adjuntas** almacenamiento persistente (volúmenes) y snapshots/backups.
5. **Escalas** manual o automáticamente (Auto Scaling Groups) según métricas.
6. **Gestionas** operaciones: parches, imágenes base (AMIs/VM images), copias y DR.

**Diagrama sencillo (texto):**
Usuarios → Balanceador (LB) → VMs (Auto Scaling) → Volúmenes/DBs → Backups / Replicación

### 2) Componentes clave de IaaS

- **Compute**: VMs/instancias (EC2, Compute Engine).
- **Storage**: block storage (EBS), object storage (S3-like), file storage.
- **Network**: VPC, subredes, rutas, NAT, IPs públicas/privadas.
- **Load balancer**: ALB/NLB para distribuir tráfico.
- **Images**: AMIs/VM images para reproducir servidores.
- **Identity & Access**: IAM, roles y policies.
- **Monitorización y logs**: métricas, alertas, agentes.
- **Autoscaling & Orquestación**: grupos de autoescalado, autoscaling policies.
- **Snapshots & backup**: snapshots de volúmenes, replicación cross-region.

### 3) Beneficios (con ejemplos)

- **Control total**: instalar drivers nativos o run-times especiales. _Ejemplo:_ una app que necesita un driver GPU específico.
- **Flexibilidad y compatibilidad**: migración de aplicaciones legacy sin reescribir. _Ejemplo:_ lift-and-shift de ERP a VMs en la nube.
- **Provisionamiento rápido**: servidores listos en minutos para pruebas o picos. _Ejemplo:_ clúster de 100 nodos para un job ETL por 4 horas.
- **Escala y disponibilidad**: usa multi-AZ/region + autoscaling para resiliencia.
- **Costo OPEX y elasticidad**: pagar por uso y usar instancias spot para batch jobs baratos.

### 4) Casos de uso comunes (con ejemplos)

- **Lift-and-shift (migración de datacenter)**: replicar servidores on-prem en VMs.
- **Entornos dev/test efímeros**: crear y destruir entornos por PR/ci.
- **Workloads legacy** que no funcionan en contenedores.
- **Big Data / HPC**: clústeres que se crean por tiempo limitado.
- **Disaster Recovery**: réplica de entornos críticos en otra región.
- **Aplicaciones con requisitos de red avanzados** (VPNs, BGP, IPs públicas).

### 5) IaaS vs PaaS vs SaaS — comparación rápida

| Pregunta            |                     IaaS |                       PaaS |                        SaaS |
| ------------------- | -----------------------: | -------------------------: | --------------------------: |
| ¿Quién gestiona OS? |                       Tú |                  Proveedor |                   Proveedor |
| ¿Control?           |                   Máximo |                      Medio |                      Mínimo |
| ¿Ideal para?        | Apps legacy, control red | Web apps modernas, rapidez | Uso directo de aplicaciones |
| Ejemplos            |      EC2, Compute Engine |        Heroku, App Service |           Gmail, Salesforce |

### 6) Buenas prácticas (operativas y de seguridad)

- **IaC siempre**: Terraform/CloudFormation para evitar clicks y drift.
- **Imágenes base hardened**: AMIs/VM images con parches y configuraciones seguras.
- **Least privilege** en IAM/roles; no usar credenciales root.
- **Security groups / firewalls estrictos**: deny-by-default, abrir solo puertos necesarios.
- **Patching automatizado** (SSM, Update Manager) y mantenimiento planificado.
- **Backups y snapshots periódicos** + política de retención.
- **Monitorización y alertas** (CPU, I/O, latencia, errores) con umbrales y playbooks.
- **Registro y auditoría** de acciones (CloudTrail-like).
- **Tagging** por proyecto, ambiente y coste — imprescindible para FinOps.
- **Pruebas de DR**: simular failover periódicamente.

### 7) Optimización de costes (tácticas)

- **Rightsizing**: ajustar tipo de instancia según uso real.
- **Autoscaling** y apagar entornos dev fuera de horas.
- **Instancias reservadas / Savings Plans** para cargas estables.
- **Spot/Preemptible** para jobs batch no críticos.
- **Monitorizar egress y storage** (S3/egress puede pesar en la factura).
- **Tagging + Budgets** y alertas para evitar sorpresas.

### 8) Migración práctica (pasos y estrategia)

1. **Inventario**: descubrir apps, dependencias y datos.
2. **Clasificar** por criticidad y complejidad.
3. **Decidir estrategia (6R)**: Rehost (lift-and-shift), Replatform, Refactor, Repurchase, Retire, Retain.
4. **POC**: mover una app no crítica y validar performance/costos.
5. **Plan de red y seguridad**: VPN/direct connect, rutas, firewall, roles.
6. **Implementar IaC** y pipelines para automatizar aprovisionamiento.
7. **Migrar datos**: replicación o sync; validar integridad.
8. **Pruebas**: smoke, load, failover y DR.
9. **Cutover y optimización**: monitorizar y ajustar.

### 9) Ejemplo mínimo con Terraform (inicio rápido)

Archivo `main.tf` (ejemplo muy simple — adapta variables y provider):

```hcl
provider "aws" { region = "us-east-1" }

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public" {
  vpc_id = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"
  subnet_id     = aws_subnet.public.id
  tags = { Name = "web-server-demo" }
}
```

Flujo:

```
terraform init
terraform plan
terraform apply
```

(Esto crea VPC, subnet y una instancia; en producción añade security groups, key pairs, userdata, y backend remoto para el state).

### 10) KPIs y métricas para operaciones IaaS

- **Uptime / disponibilidad** del servicio.
- **MTTD / MTTR** (detección y recuperación).
- **Costo por workload / por entorno**.
- **Utilización CPU/RAM / IOPS** (para rightsizing).
- **% de instancias parcheadas** (compliance).
- **Número de snapshots / cumplimiento de RPO**.

### 11) Errores comunes y cómo evitarlos

- _Editar en consola producción_ → generar drift. **Solución:** IaC + PRs.
- _No etiquetar_ → facturas ininteligibles. **Solución:** políticas de tagging obligatorias.
- _Seguridad laxa (SGs abiertos)_ → exposiciones. **Solución:** deny-by-default, escaneo automático.
- _No usar snapshots/DR_ → pérdida de datos. **Solución:** backup policy + pruebas.
- _Ignorar costos de egress_ → facturas altas. **Solución:** CDN, cache, revision de arquitectura de datos.

### 12) Mini-proyectos para practicar (ideas)

1. **Deploy “Hello World”**: VPC + 2 subnets + 2 VMs detrás de un LB con auto-scaling.
2. **Environment efímero por PR**: pipeline que crea entorno con Terraform y lo destruye al cerrar PR.
3. **Batch Spot**: un job que usa instancias spot para procesar archivos y guarda resultados en object storage.
4. **DR playbook**: configurar snapshots cross-region y ejecutar failover test.

### 13) Resumen rápido / checklist de inicio

- [ ] Inventario y clasificación de aplicaciones.
- [ ] Decidir estrategia (6R).
- [ ] Crear imágenes base seguras (hardened AMI).
- [ ] Definir IaC y pipelines.
- [ ] Configurar monitorización y alertas.
- [ ] Políticas de backup y DR probadas.
- [ ] Etiquetado y control de costes (budgets).
- [ ] Security: IAM, MFA, SGs, logging.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**          | **Siguiente 10**         |
| ------------------ | -------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./14_8_PaaS.md) | [⏩](./14_10_Private.md) |
