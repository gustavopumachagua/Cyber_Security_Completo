| **Inicio**         | **atrás 24**                 | **Siguiente 26**          |
| ------------------ | ---------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./12_24_Containment.md) | [⏩](./12_26_Recovery.md) |

---

## **Índice**

| Temario                                                                                          |
| ------------------------------------------------------------------------------------------------ |
| [402. Erradicación - AWS](#402-erradicación---aws)                                               |
| [403. ¿Qué es la erradicación en Ciberseguridad?](#403-qué-es-la-erradicación-en-ciberseguridad) |

# **Erradicación**

## **402. Erradicación - AWS**

![Erradicación](/img/12_Secure_vs_Unsecure_Protocols/contencion_erradicacion_y_recuperacion.jpg "Erradicación")

**Erradicación** = las acciones técnicas y operativas dirigidas a **eliminar la amenaza raíz** de un incidente (malware, puerta trasera, claves comprometidas, configuraciones vulnerables, imágenes/containers maliciosos, etc.). Es la fase que sigue a la contención: ya limitaste el alcance, ahora debes **borrar el problema y cerrar el vector de entrada**.

**Objetivos**

- Eliminar código malicioso, cuentas/keys mal usadas y persistencias.
- Parchar/vacunar vectores explotados.
- Asegurar que la infraestructura restaurada no volverá a ser explotada por la misma técnica.
- Conservar evidencia para posterior análisis/legal.

### Principios antes de comenzar

1. **Preservar evidencia antes de cambios destructivos** (snapshots, AMIs, logs).
2. **Coordinar con forense/legal** sobre qué se puede/ debe tocar.
3. **Priorizar erradicación del root cause** vs. acciones superficiales.
4. **Registrar cada acción** (quién, cuándo, por qué).
5. **Testear en entorno aislado** antes de volver a producción.

### Flujo general de erradicación (alta nivel)

1. Confirmar root cause (qué explotó y cómo).
2. Preservar artefactos forenses (snapshots, logs, memoria si aplica).
3. Remover artefactos maliciosos en host/workload (o reemplazar por imagen limpia).
4. Rotar/invalidar credenciales y revisar políticas IAM.
5. Parchar/mitigar la vulnerabilidad explotada.
6. Limpiar servicios gestionados (S3, Lambda, ECR, RDS) de artefactos maliciosos.
7. Validar detección y endurecer controles.
8. Restaurar desde backups verificados y monitorizar.

### Ejemplos concretos + comandos (acciones típicas en AWS)

> **Nota**: antes de terminar o borrar algo, crea snapshots/AMIs y duplica logs si no lo hiciste en la fase de contención.

#### A) Instancia EC2 comprometida (ej. cryptominer o ransomware)

**Acciones recomendadas**

1. **Snapshot / AMI forense** (preservar disco y estado):

```bash
# identificar volúmenes
aws ec2 describe-volumes --filters Name=attachment.instance-id,Values=i-123456

# crear snapshot del EBS
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "Forensic snapshot i-123456 $(date +%F)"

# crear AMI (coordinar con forense; --no-reboot tiene implicaciones)
aws ec2 create-image --instance-id i-123456 --name "forensic-image-i-123456-$(date +%F)" --no-reboot
```

2. **Eliminar o aislar la instancia** (si ya hiciste snapshot):

```bash
# opcional: detener instancia
aws ec2 stop-instances --instance-ids i-123456

# o terminar (si vas a reemplazar)
aws ec2 terminate-instances --instance-ids i-123456
```

3. **Remediación final**: crear una nueva instancia desde AMI limpia/actualizada, aplicar parches y restaurar datos desde backups limpios.
4. **Patching masivo via SSM** (si no reemplazas):

```bash
aws ssm send-command --instance-ids "i-234567" --document-name "AWS-RunPatchBaseline" --comment "Apply critical patches"
```

5. **Eliminar persistencias**: revisar cron, systemd, servicios, usuarios, SSH authorized_keys (en análisis previo o en imagen forense).

#### B) Credenciales IAM comprometidas (access key abuse)

**Acciones recomendadas**

1. **Inhabilitar o borrar Access Keys**:

```bash
# inactivar
aws iam update-access-key --user-name alice --access-key-id AKIA... --status Inactive

# eliminar
aws iam delete-access-key --user-name alice --access-key-id AKIA...
```

2. **Resetear contraseña de consola**:

```bash
aws iam update-login-profile --user-name alice --password-reset-required
```

3. **Revisar roles asumidos** y modificar trust policy para evitar que terceros sigan asumiendo el rol:

```bash
aws iam update-assume-role-policy --role-name CompromisedRole --policy-document file://new-trust-policy.json
```

4. **Invalidar sesiones temporales**: no hay un “revoke STS tokens” universal — mitigación: rotar/change policies, eliminar roles, cambiar trust relationships y eliminar refresh tokens en aplicaciones. Para SSO/IdP, revocar sesiones desde esa capa.

#### C) Lambda comprometida o función maliciosa

**Acciones recomendadas**

1. **Aislar la función**:

```bash
aws lambda put-function-concurrency --function-name myFunction --reserved-concurrent-executions 0
```

2. **Crear snapshot del código (descargar zip) y publicar para análisis**:

```bash
aws lambda get-function --function-name myFunction --query 'Code.Location' --output text
# luego descargar y guardar copia en bucket forense
```

3. **Eliminar la función maliciosa y redeploy desde código fuente verificado**:

```bash
aws lambda delete-function --function-name myFunction
# redeploy: zip y create-function / update-function-code
```

4. **Rotar roles (IAM role attached to Lambda) y revisar policies**.

#### D) Contenedor malicioso en ECS / imagen maliciosa en ECR

**Acciones recomendadas**

1. **Detener servicios / tareas comprometidas**:

```bash
aws ecs update-service --cluster myCluster --service myService --desired-count 0
```

2. **Listar imágenes en ECR y eliminar las comprometidas**:

```bash
aws ecr list-images --repository-name myrepo
aws ecr batch-delete-image --repository-name myrepo --image-ids imageDigest=sha256:...
```

3. **Escanear imágenes restantes** (ECR image scanning or third-party scanners) y forzar rebuild desde fuente confiable.
4. **Rotar credenciales del registry** si se detectó abuso.

#### E) RDS / Base de datos comprometida

**Acciones recomendadas**

1. **Snapshot forense**:

```bash
aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier mydb-forensic-$(date +%F)
```

2. **Crear una instancia restaurada en entorno aislado** para análisis:

```bash
aws rds restore-db-instance-from-db-snapshot --db-instance-identifier mydb-restore --db-snapshot-identifier mydb-forensic-...
```

3. **Rotar credenciales de DB, revisar privilegios y aplicar parches**.

#### F) S3 exfil / objetos maliciosos

**Acciones recomendadas**

1. **Preservar versiones y logs**: si el bucket tiene versioning, registra versiones antes de borrar. Copia versiónes a bucket forense con Object Lock:

```bash
aws s3api list-object-versions --bucket mybucket
# borrar versión específica
aws s3api delete-object --bucket mybucket --key 'path/file' --version-id 'VERSIONID'
```

2. **Aplicar bloqueo público inmediato y políticas restrictivas**:

```bash
aws s3api put-public-access-block --bucket mybucket --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

3. **Eliminar objetos maliciosos** o restaurar desde backups limpios.

### Persistencia, backdoors y artefactos a buscar

- Nuevos **usuarios IAM** o keys desconocidas.
- **Roles** con trust policy modificada.
- Objetos en S3 subidos recientemente.
- Tareas en ECS/EKS corriendo imágenes desconocidas.
- Lambdas con código nuevo.
- Cronjobs, systemd units, scripts en EC2.
- Snapshots/AMIs no reconocidas.
- Reglas de SG/NACL creadas recientemente.
- Logs borrados o rotados: CloudTrail, VPC Flow Logs, ALB logs.

### Validación post-erradicación

1. **Re-escaneo** con Amazon Inspector / 3rd party scanners para confirmar que la vulnerabilidad fue mitigada.
2. **Verificar GuardDuty / Security Hub findings** que hayan desaparecido o estén resueltos.
3. **Revisar CloudTrail** para confirmar que no hay actividad maliciosa posterior.
4. **Monitoreo intensivo** (increased logging, CloudWatch Alarms, WAF rules) por 7–30 días.
5. **Pruebas de penetración** controladas en el vector previamente explotado.

### Automatización de erradicación (orquestación segura)

- **AWS Systems Manager Automation**: playbooks para parcheo, reinicio controlado, ejecución de comandos remotos.
- **EventBridge → Lambda**: reglas que reaccionen a GuardDuty/CloudTrail findings y ejecuten remediación (ej. bloquear IP en WAF, aplicar SG cuarentena).
- **AWS Config Remediation**: reglas que auto-remedian (por ejemplo, si una bucket es pública, ejecutar un remediador que cambie la configuración).
- **Security Hub + SOAR** (externo) para orquestar y documentar respuestas automatizadas con aprobaciones humanas cuando sean destructivas.

Ejemplo conceptual: EventBridge rule que detecte GuardDuty finding `UnauthorizedAccess:EC2/RemoteCredential` y dispare un Lambda que:

- Cree snapshot,
- Asigne SG de cuarentena,
- Notifique al SOC.

### Buenas prácticas post-erradicación (endurecimiento)

- **Rotar todas las credenciales** potencialmente afectadas (IAM keys, DB passwords, tokens).
- **Habilitar MFA** en cuentas críticas (root, admins).
- **Aplicar principio de mínimo privilegio** y revisar policies (Access Advisor, IAM Access Analyzer).
- **Harden images**: build automatizado y firmado de AMIs/containers (image provenance).
- **Activar detección avanzada**: GuardDuty, Inspector, Security Hub, Detective.
- **S3: versioning + Object Lock** para prevención de borrado malicioso.
- **Backups offline / air-gapped** y prueba regular de restores.
- **Segmentación**: microsegmentación y least-trust network design.
- **Revisar IaC (CloudFormation/Terraform)**: parchear templates que reprovisionen recursos inseguros.

### Artefactos forenses y cadena de custodia

- Guarda snapshots, AMIs y logs en bucket forense con **Object Lock** y control de acceso estricto.
- Documenta quién creó cada snapshot y por qué.
- Usa herramientas forenses (Velociraptor, SIFT) en imágenes descargadas a entorno controlado.
- No realices borrados masivos sin autorización legal si hay potenciales implicaciones regulatorias.

### Checklist rápido de erradicación (acción práctica)

- [ ] ¿Se crearon snapshots/AMIs y se guardaron logs?
- [ ] ¿Se removieron/aislaron workloads comprometidos (EC2/ECS/EKS/Lambda)?
- [ ] ¿Se rotaron/clasificaron credenciales y roles implicados?
- [ ] ¿Se parchearon vulnerabilidades (SSM, Inspector)?
- [ ] ¿Se limpiaron S3 / ECR / RDS / snapshots maliciosos?
- [ ] ¿Se validó con Inspector / escáner / GuardDuty?
- [ ] ¿Se documentó todo y se coordinó con legal/PR si aplica?
- [ ] ¿Se definieron y aplicaron medidas preventivas para evitar repetición?

### Cierre: medidas organizativas y siguientes pasos

- Ejecutar un **post-mortem** formal (root cause, timeline, decisiones, gaps).
- Actualizar playbooks, runbooks y alertas SIEM.
- Programar auditoría externa o pentest sobre vectores críticos.
- Formar al equipo según lecciones aprendidas.

---

[🔼](#índice)

---

## **403. ¿Qué es la erradicación en Ciberseguridad?**

La **erradicación** es la fase del **plan de respuesta a incidentes** en la que se **elimina la causa raíz del incidente de seguridad** y cualquier rastro de la amenaza del entorno afectado.

No basta con contener el ataque (ej. desconectar una máquina o bloquear un puerto), hay que **limpiar completamente el sistema** para que el adversario no pueda regresar ni reutilizar la misma vulnerabilidad.

👉 En otras palabras: si **contención = apagar el incendio**, la **erradicación = retirar los materiales inflamables que lo causaron**.

### 🔹 Incidentes de seguridad y respuesta a incidentes

En ciberseguridad, un **incidente de seguridad** puede ser:

- **Malware** en estaciones de trabajo.
- **Ransomware** en servidores.
- **Phishing exitoso** con robo de credenciales.
- **Intrusión en red** con movimiento lateral.
- **Amenaza interna** (usuario con malas intenciones).

La **respuesta a incidentes (IR, Incident Response)** es el conjunto de pasos para:

1. **Preparar** (prevención y planes).
2. **Detectar** (descubrir anomalías).
3. **Contener** (limitar daños inmediatos).
4. **Erradicar** (eliminar la causa raíz).
5. **Recuperar** (restaurar operaciones normales).
6. **Lecciones aprendidas** (mejorar seguridad a futuro).

### 🔹 Agenda de la erradicación en un plan de respuesta a incidentes

La agenda de erradicación suele incluir estos pasos:

#### 1. **Confirmar la causa raíz**

- Analizar logs, alertas de SIEM, y evidencias forenses.
- Ejemplo: detectar que el ataque de ransomware entró por un **servidor con RDP sin parchear**.

#### 2. **Preservar evidencia**

- Antes de limpiar, capturar imágenes de disco, memoria y logs.
- Ejemplo: hacer un **snapshot de un servidor comprometido** para análisis posterior.

#### 3. **Eliminar el código malicioso**

- Desinstalar malware, borrar scripts y troyanos, desactivar procesos.
- Ejemplo: limpiar un **rootkit en Linux** con herramientas forenses.

#### 4. **Remover accesos no autorizados**

- Eliminar **cuentas creadas por atacantes**.
- Revocar **tokens, certificados o claves API comprometidas**.
- Ejemplo: deshabilitar la cuenta de un atacante en **Active Directory**.

#### 5. **Aplicar parches y mitigar vulnerabilidades**

- Instalar actualizaciones de seguridad en sistemas.
- Configurar firewalls, IDS/IPS, y reglas adicionales.
- Ejemplo: parchear la **vulnerabilidad Log4j** en servidores.

#### 6. **Validar la limpieza**

- Reescanear sistemas con antivirus/EDR.
- Comprobar que el vector de ataque ya no existe.
- Ejemplo: ejecutar un **scan con Nessus o OpenVAS** para confirmar que el exploit ya no es posible.

### 🔹 Recomendaciones para la erradicación

1. **No actuar sin forense previo** → borrar rápido puede destruir evidencias clave.
2. **Priorizar sistemas críticos** → erradicar primero en servidores esenciales y luego en endpoints secundarios.
3. **Automatizar tareas repetitivas** (rotación de contraseñas, revocación de llaves, despliegue de parches).
4. **Coordinar con el área legal/compliance** → algunas industrias requieren conservar evidencias por norma.
5. **Revisar persistencia** → muchos atacantes instalan backdoors, por lo que hay que eliminar cronjobs, servicios ocultos, claves SSH extra, etc.
6. **Validar antes de declarar limpio** → usar escaneos, logs y EDR para comprobar que la amenaza ya no está presente.
7. **Documentar cada acción** → servirá para auditorías y para mejorar planes de respuesta.

### 🔹 Ejemplo práctico

👉 Caso: una empresa detecta actividad extraña en un servidor web Linux.

- **Contención**: desconectar el servidor de la red.
- **Erradicación**:

  - Hacer snapshot para forense.
  - Eliminar webshells y cuentas no autorizadas.
  - Parchar Apache y PHP.
  - Revocar claves SSH comprometidas.
  - Reforzar reglas del firewall.

- **Validación**: escanear nuevamente el servidor, comprobar que no hay tráfico sospechoso.

### 🔹 Conclusión

La **erradicación en ciberseguridad** es una fase crítica del **plan de respuesta a incidentes**, ya que asegura que el adversario no pueda volver a aprovechar la misma brecha.

Si solo se contiene y se recupera sin erradicar, el ataque **puede repetirse en minutos**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 24**                 | **Siguiente 26**          |
| ------------------ | ---------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./12_24_Containment.md) | [⏩](./12_26_Recovery.md) |
