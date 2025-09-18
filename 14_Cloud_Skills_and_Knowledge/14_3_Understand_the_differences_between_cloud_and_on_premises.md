| **Inicio**         | **atrás 2**                                                  | **Siguiente 4**                                                  |
| ------------------ | ------------------------------------------------------------ | ---------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_2_Understand_concepts_of_security_in_the_cloud.md) | [⏩](./14_4_Understand_the_concept_of_Infrastructure_as_Code.md) |

---

## **Índice**

| Temario                                                                                                                                                        |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [518. ¿Qué son los centros de datos locales frente a la computación en la nube?](#518-qué-son-los-centros-de-datos-locales-frente-a-la-computación-en-la-nube) |
| [519. Local vs. Nube: ¿Es la computación en la nube el futuro?](#519-local-vs-nube-es-la-computación-en-la-nube-el-futuro)                                     |

# **Comprenda las diferencias entre la nube y las instalaciones locales**

## **518. ¿Qué son los centros de datos locales frente a la computación en la nube?**

![Comprenda las diferencias entre la nube y las instalaciones locales](/img/14_Cloud_Skills_and_Knowledge/beneficios_almacenamiento_cloud.jpg "Comprenda las diferencias entre la nube y las instalaciones locales")

### 📌 Definición de centro de datos local

Un **centro de datos local (on-premises)** es la infraestructura de TI que una empresa posee, gestiona y mantiene dentro de sus instalaciones físicas.
Incluye:

- Servidores propios.
- Redes internas.
- Almacenamiento físico.
- Seguridad y mantenimiento por parte del personal de la empresa.

👉 **Ejemplo:**

Un banco en Lima que guarda sus bases de datos de clientes en servidores propios dentro de su edificio principal.

### 📌 Definición de computación en la nube

La **computación en la nube (cloud computing)** permite acceder a recursos de TI (servidores, bases de datos, aplicaciones, almacenamiento) a través de Internet, sin necesidad de poseer hardware físico.

Se contrata bajo modelos como:

- **IaaS (Infraestructura como Servicio)** → AWS EC2, Azure VM.
- **PaaS (Plataforma como Servicio)** → Google App Engine.
- **SaaS (Software como Servicio)** → Gmail, Office 365.

👉 **Ejemplo:**

Una startup de e-commerce usa **Amazon Web Services (AWS)** para almacenar productos, manejar pagos y desplegar su web sin tener que comprar servidores propios.

### 📌 Definición de computación en la nube privada

Una **nube privada** es una infraestructura en la nube dedicada a una sola organización. Puede estar:

- **En las instalaciones de la empresa (on-premises).**
- **Alojada en un proveedor externo, pero exclusiva para esa empresa.**

👉 **Ejemplo:**

Una empresa de salud en Perú crea su propia nube privada con VMware para cumplir con normativas de confidencialidad de datos médicos.

### 📌 Ventajas y desventajas de la informática local (on-premises)

✅ **Ventajas:**

- Mayor **control** sobre datos e infraestructura.
- Puede cumplir fácilmente regulaciones que exigen **datos dentro del país**.
- **Personalización total** de hardware y software.

❌ **Desventajas:**

- **Costos elevados** en hardware, energía, mantenimiento.
- **Escalabilidad limitada**: agregar servidores requiere tiempo y dinero.
- Mayor riesgo de **falla local** (corte de luz, incendio, robo).

👉 **Ejemplo:**

Una universidad que mantiene sus propios servidores para almacenar notas de estudiantes. Si la energía falla, el sistema se cae.

### 📌 Beneficios y desventajas de la computación en la nube

✅ **Beneficios:**

- **Escalabilidad bajo demanda**: creces o reduces capacidad fácilmente.
- **Menor inversión inicial**: pagas por uso.
- **Alta disponibilidad**: respaldos en múltiples centros de datos.
- **Actualizaciones automáticas**: seguridad y software al día.

❌ **Desventajas:**

- Dependencia de **conexión a Internet**.
- **Menor control** directo sobre hardware.
- Posibles **problemas de cumplimiento legal** (ej. dónde se almacenan los datos).

👉 **Ejemplo:**

Un e-commerce puede aumentar su capacidad en Navidad con AWS, y reducirla en enero para ahorrar costos.

### 📌 Comparación entre instalaciones locales y en la nube

| Aspecto                | On-Premises (Local) 🏢                  | Nube ☁️                                               |
| ---------------------- | --------------------------------------- | ----------------------------------------------------- |
| **Propiedad**          | La empresa compra y mantiene servidores | El proveedor gestiona la infraestructura              |
| **Costos**             | Inversión inicial alta + mantenimiento  | Pago por uso, costos variables                        |
| **Escalabilidad**      | Limitada, requiere comprar hardware     | Ilimitada, rápida y flexible                          |
| **Seguridad**          | Control total interno                   | Depende de configuración y proveedor                  |
| **Disponibilidad**     | Depende de la infraestructura local     | Alta, con redundancia global                          |
| **Cumplimiento legal** | Fácil de controlar localmente           | Puede ser complejo si datos se alojan en otros países |

### 📌 Productos y soluciones HPE para entornos locales y en la nube

**HPE (Hewlett Packard Enterprise)** ofrece soluciones híbridas:

- **HPE ProLiant Servers** → servidores físicos para on-premises.
- **HPE GreenLake** → plataforma de nube híbrida que combina lo mejor de on-premises y cloud:

  - Se instala infraestructura local, pero se paga como servicio (modelo nube).
  - Escalabilidad flexible sin necesidad de comprar hardware extra.

- **HPE Storage Solutions** → almacenamiento híbrido con backup en la nube.

👉 **Ejemplo:**

Una empresa de retail en Perú puede usar **HPE GreenLake** para tener sus datos sensibles en un centro local (control legal), pero aprovechar la escalabilidad de la nube para procesar ventas en campañas de descuentos.

✅ **En conclusión:**

- Los **centros de datos locales** ofrecen control y cumplimiento, pero son costosos y poco escalables.
- La **nube** brinda flexibilidad, reducción de costos y disponibilidad global, aunque con menos control.
- La mejor opción muchas veces es un **modelo híbrido**, combinando lo mejor de ambos (ej. soluciones HPE GreenLake).

---

[🔼](#índice)

---

## **519. Local vs. Nube: ¿Es la computación en la nube el futuro?**

Respuesta corta: **la nube dominará la mayoría de las cargas nuevas y la innovación**, pero **no eliminará** las instalaciones locales: lo realista es un futuro **híbrido** donde nube, edge y centros locales coexisten según necesidades de rendimiento, cumplimiento, coste y control.

### 1. Resumen rápido — cuándo gana la nube y cuándo no

- **Gana la nube**: startups, aplicaciones web escalables, análisis de datos, ML/AI, servicios globales, entornos que necesitan elasticidad o despliegues rápidos.
- **Gana local (on-prem / colo)**: requisitos estrictos de latencia/OT, datos altamente regulados sin opción de traslado, cargas muy predecibles con infra amortizada, o cuando el control físico es crítico.
- **Típico real**: la mayoría de las empresas adoptan **modelo híbrido**: datos sensibles o sistemas OT on-prem + workloads dinámicos y analítica en la nube.

### 2. Ventajas y desventajas (resumidas con ejemplos)

#### Nube — ventajas

- **Escalabilidad instantánea**: subes recursos en minutos (ej. e-commerce que escala en Black Friday).
- **Time-to-market**: aprovisionas infra y servicios gestionados (DBs, ML, CDN) sin comprar hw.
- **Pago por uso**: reduces CAPEX inicial. Ideal para picos.
- **Ecosistema y servicios avanzados**: IA, análisis, serverless, pipelines CI/CD.
- **Alta disponibilidad y recuperación** integrada (multi-AZ/region).

#### Nube — desventajas / riesgos

- **Costes variables y sorpresas en factura** si no se gestiona.
- **Dependencia del proveedor (lock-in)** si se usan servicios muy específicos.
- **Privacidad/ubicación de datos**: normas locales pueden exigir datos in-country.
- **Latencia / rendimiento determinista**: workloads industriales o trading HFT podrían necesitar local.

#### Local — ventajas

- **Control total** sobre hardware, datos y red. Útil para cumplimiento estricto.
- **Costes previsibles** cuando la infra está amortizada y cargas son estables.
- **Latencia ultra-baja** para control industrial y trading.

#### Local — desventajas

- **Alta inversión inicial (CAPEX)** y costes operativos (energía, personal).
- **Escalar es lento** (comprar/instalar hw).
- **Mantener servicios avanzados (AI, analytics) es más costoso** técnicamente y operacionalmente.

### 3. Casos reales / ejemplos ilustrativos

- **Startup SaaS**: casi siempre nube (AWS/GCP/Azure). Rápido a desarrollar, escalar y monetizar.
- **Banco grande**: datos regulados y core bancario crítico pueden quedar on-prem o en nube privada; analytics y aplicaciones móviles en nube pública.
- **Hospital**: historiales clínicos sensibles en nube privada o on-prem; backups y analytics no identificados en nube pública.
- **Fábrica (OT/IIoT)**: control en la planta on-prem por latencia y seguridad; telemetría agregada a la nube para análisis centralizado.

### 4. Economía: ¿nube cuesta menos? (lo importante a considerar)

No hay una respuesta única: compara **TCO** (Total Cost of Ownership) y **TVO** (Total Value Obtained).

Elementos a incluir en el cálculo:

- **CAPEX**: servidores, racks, refrigeración, UPS, red.
- **OPEX**: energía, mantenimiento, personal, licencias, espacio físico.
- **Costes de migración**: refactor, tests, downtime, formación.
- **Costes variables en la nube**: instancias, almacenamiento, egress, servicios gestionados.
- **Beneficios intangibles**: rapidez de innovación, menores tiempos de despliegue, capacidad de internacionalizarse.

> Regla práctica: para cargas **altamente variables o en fase de crecimiento**, nube suele ser más barato/agil. Para cargas **muy estables y largas** con uso alto constante, on-prem con hw amortizado puede salir más barato si gestionas bien.

### 5. Riesgos clave y cómo mitigarlos

- **Facturas inesperadas** → usar budgets, alerts, reservas (RI/Savings plans), tagging para accountability.
- **Lock-in** → diseñar con contenedores, IaC (Terraform), usar servicios estándar y APIs portables.
- **Seguridad y compliance** → aplicar modelo de responsabilidad compartida, cifrado, DLP, IAM con least-privilege, auditorías.
- **Latencia / rendimiento** → edge computing, regional placement, cache/CDN.
- **Dependencia de red** → redundancia de enlaces, direct connect/expressroute, fallbacks on-prem.

### 6. Arquitecturas intermedias: híbrida, multi-cloud y edge

- **Híbrida**: mezcla on-prem + cloud. Útil cuando ciertas apps deben quedar locales.
- **Multi-cloud**: usar varios proveedores para evitar vendor lock-in, resiliencia, optimización de costes. Incrementa complejidad de gestión.
- **Edge**: procesamiento cerca de la fuente (IoT, latencia crítica). Integra con nube para agregación y ML.

-

### 7. Migración práctica: roadmap paso a paso (plantilla ejecutable)

1. **Inventario y clasificación**: ¿qué tienes? ¿qué datos son sensibles?
2. **Evaluación de apps**: prioriza por valor/compatibilidad (6R: Rehost, Replatform, Refactor, Replace, Retain, Retire).
3. **Prueba piloto (POC)**: mueve un servicio no crítico (por ejemplo, entorno de staging).
4. **Diseño de red y seguridad**: VPN/direct connect, IAM, cifrado, logging central.
5. **Estrategia de datos**: replicación, sincronización, consistencia y egress costs.
6. **Automatización**: Infra como Código (Terraform/CloudFormation), pipelines CI/CD.
7. **Plan de cutover**: pruebas, rollback, ventanas, comunicación.
8. **Optimización post-migración**: rightsizing, reservas, monitorización, finanzas.
9. **Governance y training**: políticas, finops, seguridad, runbooks.

**Ejemplo de patrón**: migrar base de datos OLAP a data-warehouse en la nube (BigQuery/Redshift) para analytics, manteniendo la base OLTP sensible on-prem o en una VPC privada.

### 8. Mejores prácticas técnicas y organizativas

- **Adopta IaC** (Terraform) y pipelines para despliegues reproducibles.
- **Microservicios y contenedores** para portabilidad.
- **Observabilidad**: logs centralizados, tracing y SLOs.
- **FinOps**: control financiero de la nube (tagging, budgets, chargebacks).
- **Seguridad por diseño**: baselines, escaneo de imágenes, rotación de secretos, least-privilege.
- **Capacitación**: forma equipos en cloud ops, seguridad y optimización.

### 9. Tendencias que moldean el “futuro” (resumen)

- **Serverless y FaaS**: menos infra que gestionar, ideal para micro-workloads.
- **Edge & fog computing**: latencia ultra-baja para IoT/AR/VR.
- **Multicloud & hybrid cloud orchestration** con herramientas de gestión centralizada.
- **AI/ML gestionado por la nube**: facilitan capacidades avanzadas sin infra propia.
- **Sostenibilidad**: optimización energética y selección de regiones por huella de carbono.

Conclusión: estas tendencias favorecen la nube, pero no la sustituyen por completo.

### 10. Recomendaciones prácticas según tamaño de empresa

- **Startup / small business:** nativo en la nube. Usa servicios gestionados, serverless y contenedores.
- **PYME con datos sensibles:** híbrido: backups y servicios públicos no críticos en nube; datos sensibles en colocation o nube privada.
- **Empresa grande / regulada (banca, salud):** estrategia híbrida; refactorizar aplicaciones no críticas a la nube; mantener core sensible en on-prem o nube privada certificada.
- **Industrial / manufactura:** control OT on-prem con edge computing + nube para analytics.

### 11. Errores comunes al migrar (y cómo evitarlos)

- **Mover todo “tal cual” (lift & shift) sin optimizar** → factura alta. _Mitigación:_ evaluar refactor vs rehost.
- **Ignorar la red y latencia** → aplicaciones fallan. _Mitigación:_ hacer pruebas de rendimiento.
- **No tener gobernanza/finops** → gasto incontrolable. _Mitigación:_ tagging, alertas, reservas.
- **Fallar en seguridad básica (buckets públicos, claves embebidas)**. _Mitigación:_ policy as code, escaneos automáticos.

### 12. Decisión final: ¿es la nube el futuro?

Sí — para **innovación**, **escalado** y **servicios avanzados** la nube es el motor del futuro. Pero **no es una bala de plata**:

- **No reemplazará** el on-prem donde la latencia, control físico o requisitos regulatorios lo exigen.
- El **futuro real** es heterogéneo: nubes públicas + nubes privadas + edge + on-prem, gestionados de forma coherente.

### 13. ¿Qué puedes hacer hoy? (plan de 90 días)

1. Inventario y clasificación de aplicaciones y datos (semana 1–2).
2. Cost baseline (gastos on-prem actuales) y POC en la nube para 1 app (mes 1).
3. Diseñar pilot con IaC, seguridad básica y CI/CD (mes 2).
4. Evaluar resultados, TCO y decidir roadmap para 6–12 meses (mes 3).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                                                  | **Siguiente 4**                                                  |
| ------------------ | ------------------------------------------------------------ | ---------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_2_Understand_concepts_of_security_in_the_cloud.md) | [⏩](./14_4_Understand_the_concept_of_Infrastructure_as_Code.md) |
