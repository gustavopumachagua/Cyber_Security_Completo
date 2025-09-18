| **Inicio**         | **atrás 11**             |
| ------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./14_10_Private.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [536. ¿Qué es una nube híbrida?](#536-qué-es-una-nube-híbrida) |

# **Híbrido**

## **536. ¿Qué es una nube híbrida?**

![Híbrido](/img/14_Cloud_Skills_and_Knowledge/Hybrid.webp "Híbrido")

La **nube híbrida** es un modelo de **computación en la nube** que combina:

- **Infraestructura local (on-premises / centro de datos privado)**
- **Nube privada (infra dedicada en la nube, para mayor control)**
- **Nube pública (servicios escalables y bajo demanda como AWS, Azure, GCP)**

👉 La clave: estos entornos se integran y **trabajan como un solo sistema**, lo que permite mover datos, aplicaciones y cargas de trabajo entre ellos según las necesidades de la empresa.

### 📘 Definición de nube híbrida

> Es un modelo de despliegue de TI que **mezcla entornos de nube pública, nube privada y/o infra on-premises**, con conectividad y orquestación entre ellos, para aprovechar lo mejor de cada mundo: escalabilidad, control, seguridad y optimización de costos.

### 🖼️ Ejemplos de nube híbrida

1. **Banco**

   - Datos de clientes y transacciones → en nube privada/on-prem (por seguridad y regulación).
   - Aplicación móvil y análisis de datos → en nube pública (Azure o AWS) para escalar con picos de uso.

2. **E-commerce en Black Friday**

   - Infra principal en centro de datos propio.
   - Durante la campaña, se amplían recursos en AWS para manejar el tráfico extra.

3. **Hospital**

   - Historias clínicas → on-prem (cumplimiento HIPAA).
   - Inteligencia artificial para imágenes médicas → en nube pública con GPUs.

### ⚙️ ¿Cómo funciona una nube híbrida?

Funciona gracias a **conectividad e integración entre entornos**:

1. **Red segura**: VPNs, MPLS o conexiones dedicadas (ej. AWS Direct Connect, Azure ExpressRoute).
2. **Orquestación**: plataformas que gestionan apps/recursos entre nubes (ej. Kubernetes, Red Hat OpenShift).
3. **Portabilidad**: uso de contenedores y APIs estandarizadas para mover cargas fácilmente.
4. **Gestión unificada**: monitoreo, seguridad e identidades centralizadas (ej. Azure Arc, Anthos, VMware HCX).

👉 Así, una empresa puede **ejecutar cargas sensibles en privado y escalar cargas dinámicas en público**.

### 🎯 ¿Para qué se usa un enfoque de nube híbrida?

- Cumplir **regulaciones** (datos sensibles en privado, operaciones en público).
- **Escalado elástico**: crecer hacia la nube pública en picos de demanda.
- **Innovación**: usar IA/Big Data en la nube sin migrar todo.
- **Optimización de costos**: usar recursos locales ya amortizados y pagar nube solo cuando se necesite.
- **Migraciones progresivas**: mover apps a la nube paso a paso.

### 🛠️ Soluciones de nube híbrida

Ejemplos de plataformas y servicios que permiten nube híbrida:

- **AWS Outposts** → lleva hardware y servicios de AWS al centro de datos del cliente.
- **Azure Stack / Azure Arc** → extiende Azure a infra local.
- **Google Anthos** → gestiona Kubernetes y servicios híbridos/multinube.
- **VMware Cloud** → entorno VMware que corre tanto en local como en nube pública.
- **Red Hat OpenShift** → plataforma Kubernetes híbrida.

### ✅ Beneficios de la nube híbrida

1. **Flexibilidad y escalabilidad** (usa nube pública cuando lo necesites).
2. **Cumplimiento y seguridad** (datos sensibles en privado).
3. **Reducción de costos** (optimizar infra ya existente y pagar solo por uso adicional).
4. **Continuidad del negocio** (respaldo en la nube pública si falla el data center).
5. **Velocidad de innovación** (acceso a IA, IoT, Big Data sin migrar todo).

### ⚠️ Desventajas de la nube híbrida

1. **Complejidad de gestión** → requiere integrar múltiples entornos.
2. **Costos ocultos** → redes privadas, licencias, duplicación de infra.
3. **Seguridad más desafiante** → múltiples superficies de ataque.
4. **Latencia** → si no se diseña bien, mover datos entre nubes puede ser lento o caro.
5. **Necesidad de expertos** → DevOps, cloud architects y redes avanzadas.

### 🏗️ Cómo configurar una nube híbrida (paso a paso conceptual)

1. **Evaluar cargas de trabajo**: decidir qué se queda on-prem, qué va a nube pública.
2. **Conectividad segura**: implementar VPNs, Direct Connect o ExpressRoute.
3. **Contenerización y orquestación**: usar Kubernetes, OpenShift o Anthos.
4. **Gestión de identidades**: unificar IAM (ej. Azure AD, AWS IAM Federation).
5. **Monitorización y seguridad unificada**: SIEM, logging centralizado, políticas comunes.
6. **Automatización (IaC)**: Terraform, Ansible, CloudFormation para consistencia.

### 🛒 Productos y servicios relacionados

- **AWS**: Outposts, EKS Anywhere, Direct Connect.
- **Azure**: Azure Arc, Azure Stack Hub, ExpressRoute.
- **Google Cloud**: Anthos, Cloud VPN, Interconnect.
- **VMware**: VMware Cloud on AWS, NSX.
- **Red Hat**: OpenShift, Ansible Automation.

### 📌 Conclusión

La **nube híbrida** es la opción ideal para organizaciones que quieren lo mejor de dos mundos: **control y seguridad** de lo privado + **escalabilidad e innovación** de lo público. Sin embargo, requiere **buena planificación, expertos y herramientas de gestión** para evitar que la complejidad y los costos se disparen.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**             |
| ------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./14_10_Private.md) |
