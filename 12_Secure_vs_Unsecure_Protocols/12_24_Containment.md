| **Inicio**         | **atrás 23**                    | **Siguiente 25**             |
| ------------------ | ------------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_23_Identification.md) | [⏩](./12_25_Eradication.md) |

---

## **Índice**

| Temario                                                                                                                                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [400. Gestión de incidentes de seguridad de Microsoft: contención, erradicación y recuperación](#400-gestión-de-incidentes-de-seguridad-de-microsoft-contención-erradicación-y-recuperación) |
| [401. Contención - AWS](#401-contención---aws)                                                                                                                                               |

# **Containment**

## **400. Gestión de incidentes de seguridad de Microsoft: contención, erradicación y recuperación**

![Containment](/img/12_Secure_vs_Unsecure_Protocols/contencion_erradicacion_y_recuperacion.jpg "Containment")

Microsoft sigue un proceso estructurado de **respuesta a incidentes de seguridad**, muy similar al marco del **NIST**. La meta es **detectar, contener, erradicar y recuperarse** lo más rápido posible para reducir el impacto en los clientes.

### 🛑 1. Contención

La **contención** busca **limitar el alcance del incidente** mientras se prepara la erradicación.

Ejemplos en Microsoft:

- **Aislar sistemas afectados** (servidores en Azure, máquinas virtuales comprometidas).
- **Restringir accesos** → deshabilitar cuentas comprometidas en Azure AD o bloquear claves API.
- **Reglas temporales en firewalls o WAF** → para frenar tráfico sospechoso.
- **Snapshot de máquinas virtuales o logs** → asegurar evidencia antes de limpiar.

👉 Ejemplo práctico: si se detecta un ataque de ransomware en una máquina de Azure, Microsoft puede desconectarla de la red y bloquear accesos mientras analiza el malware.

### 🧹 2. Erradicación

Una vez contenida la amenaza, se procede a **eliminar la causa raíz del incidente**.

Acciones típicas:

- **Eliminar malware** de sistemas comprometidos.
- **Revocar certificados o claves comprometidas**.
- **Aplicar parches de seguridad** en Azure, Windows Server o Microsoft 365.
- **Revisar configuraciones** inseguras detectadas (ej. reglas de firewall demasiado abiertas).
- **Forzar restablecimiento de credenciales** de usuarios afectados.

👉 Ejemplo práctico: si un atacante explotó una vulnerabilidad en Exchange, Microsoft despliega parches y limpia el acceso no autorizado en los servidores.

### 🔄 3. Recuperación

La **recuperación** busca **restaurar los servicios y operaciones normales** de forma segura.

Medidas clave:

- **Restaurar sistemas** a partir de backups seguros (en Azure Backup, OneDrive o SharePoint).
- **Monitoreo reforzado** → vigilar los sistemas recuperados para detectar reinfecciones.
- **Pruebas de integridad** en aplicaciones y bases de datos.
- **Reincorporación gradual a la red** para evitar nuevos ataques.

👉 Ejemplo práctico: tras eliminar un ataque de phishing en Microsoft 365, los buzones se restauran desde copias seguras y se monitoriza el tráfico de correo para descartar reinfecciones.

### 📢 4. Notificación al cliente sobre incidentes de seguridad

Microsoft tiene un compromiso de **transparencia con los clientes**:

- Si un incidente afecta directamente a los **datos de un cliente**, este es **notificado sin demora injustificada**.
- Las notificaciones incluyen:

  - Qué ocurrió.
  - Qué datos o servicios fueron afectados.
  - Qué medidas tomó Microsoft.
  - Qué pasos debe seguir el cliente.

- Los clientes pueden recibir avisos vía:

  - **Centro de Mensajes de Microsoft 365**.
  - **Portal de Azure Service Health**.
  - **Correo electrónico a contactos designados**.

👉 Ejemplo práctico: si una vulnerabilidad en Exchange Online expone correos de un cliente, Microsoft notifica al administrador de su organización, explica la situación y sugiere acciones como revisar logs de acceso.

✅ **En resumen:**

- **Contención:** detener la propagación del incidente.
- **Erradicación:** eliminar la amenaza y corregir vulnerabilidades.
- **Recuperación:** restaurar operaciones y verificar integridad.
- **Notificación:** informar a clientes afectados de forma clara y oportuna.

---

[🔼](#índice)

---

## **401. Contención - AWS**

### 1) ¿Qué es “contención” en AWS y cuál es su objetivo?

**Contención** = acciones rápidas y controladas para **limitar el alcance** de un incidente (ej. compromiso de instancia, exfiltración, malware, abuso de credenciales) mientras se preserva evidencia y se prepara la erradicación y recuperación.
Objetivos concretos:

- Detener la propagación lateral.
- Impedir exfiltración adicional.
- Preservar la evidencia (snapshots, logs).
- Mantener la mayor disponibilidad posible para servicios no afectados.

### 2) Estrategia de contención: niveles y principios

- **Contención inmediata (short-term):** medidas rápidas que minimizan daño (aislar, bloquear IP, quitar acceso).
- **Contención intermedia (mid-term):** acciones que estabilizan el entorno sin interrumpir más de lo necesario (mover recursos a VPC/quarantine, aplicar políticas SCP).
- **Contención a largo plazo (long-term):** ajustes de arquitectura y políticas para prevenir reaparición (SCP, cambios de IAM, revisiones de red).

Principios clave:

- Priorizar _preservación de evidencia_ (hacer snapshots/AMIs antes de modificar) salvo si la amenaza exige desconexión inmediata.
- Mantener _accionabilidad auditada_ (registro de todo lo que haces).
- Actuar con _mínimo privilegio_ y coordinar con forense/legal.

### 3) Controles AWS útiles para contención (qué usar y cuándo)

#### A. Red / nivel instancia

##### Security Groups (SG) — **aislar rápido**

Idea: asignar a la instancia comprometida un **Security Group de cuarentena** que solo permita acceso desde la IP/host del SOC o lo deje sin acceso (remover reglas).
Ejemplo CLI (asignar SG de cuarentena a una instancia):

```bash
# Asume que sg-quarantine existe y que i-123456 es la instancia afectada
aws ec2 modify-instance-attribute --instance-id i-123456 --groups sg-quarantine
```

Notas:

- Los SG son _permiso-por-defecto_ (no hay denies). Para “bloquear todo”, usa un SG vacío o sólo con acceso desde la IP de los analistas.

##### Network ACLs (NACL) — **deny a nivel subnet**

Útil si quieres negar tráfico a toda una subred:

```bash
# ejemplo para crear una entrada deny (requiere rule-number, e.g. 100)
aws ec2 create-network-acl-entry --network-acl-id acl-01234 --rule-number 100 --protocol -1 --port-range From=0,To=65535 --egress false --cidr-block 0.0.0.0/0 --rule-action deny
```

Cuidado: NACLs afectan toda la subred; úsalas con precaución.

##### Detach o aislar ENI (si procede)

Detaching del ENI principal puede dejar la instancia inaccesible; sólo hacerlo si entiendes consecuencias:

```bash
aws ec2 detach-network-interface --attachment-id eni-attach-abcdef
```

#### B. IAM / credenciales

##### Inactivar o borrar claves y forzar re-autenticación

Para cuentas afectadas, inactivar access keys y forzar reset de contraseña:

```bash
# Inactivar access key
aws iam update-access-key --user-name alice --access-key-id AKIA... --status Inactive

# O eliminar
aws iam delete-access-key --user-name alice --access-key-id AKIA...
```

Forzar reset de password:

```bash
aws iam update-login-profile --user-name alice --password-reset-required
```

**Nota:** para tokens temporales (STS) no hay una “revocación global” inmediata — la receta es rotar credenciales, eliminar roles comprometidos y cambiar trust relationships.

##### Service Control Policy (SCP) — bloqueo a nivel organización

Si sospechas compromiso general, aplicar un SCP que bloquee acciones peligrosas (ej. creación de instancias, eliminación de logs). Ejemplo conceptual (deny ec2\:RunInstances):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyEC2Launch",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": "*"
    }
  ]
}
```

Aplica con cuidado — un SCP malo puede romper operaciones.

#### C. Captura y preservación (forense)

##### Crear AMI / snapshot (imagen forense)

Antes de modificar: crea snapshot del volumen y/o AMI de la instancia.

```bash
# crear snapshot del volumen
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "Forensic snapshot i-123456 2025-09-14"

# crear AMI (no-reboot opcional; discutir con forense)
aws ec2 create-image --instance-id i-123456 --name "forensic-image-i-123456" --no-reboot
```

**Precaución:** `--no-reboot` evita reinicio (útil para disponibilidad) pero puede afectar integridad del FS; coordina con peritos.

##### Preservar logs

- Asegurar CloudTrail multi-region y que los logs se entregan a bucket con Object Lock o WORM si es necesario.
- Hacer backup del bucket de CloudTrail o copiar logs antes de cambios:

```bash
# ejemplo simple: copiar objetos a otro bucket forense (aws s3 cp / sync)
aws s3 sync s3://my-cloudtrail-logs s3://forensic-preserve-logs-123
```

#### D. Endpoints y workloads (Lambda / ECS / EKS / RDS)

##### Lambda — bloquear invocación

Reservar concurrencia a 0 evita ejecuciones:

```bash
aws lambda put-function-concurrency --function-name myFunction --reserved-concurrent-executions 0
```

##### ECS — detener servicios comprometidos

```bash
aws ecs update-service --cluster myCluster --service myService --desired-count 0
```

##### EKS / Kubernetes — cordon/evict pods

Usa `kubectl` para aislar nodos/pods (p. ej. `kubectl cordon <node>`; `kubectl scale deployment ... --replicas=0`).

##### RDS — snapshot y bloquear acceso

Crear snapshot y restringir SG del DB:

```bash
aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier mydb-forensic-20250914
# luego modificar SG para DB subnet
```

#### E. Perímetro (WAF / Shield / Route53)

- **WAF**: bloquear IPs / rate-limit immediately.
- **Shield Advanced**: activar mitigaciones DDoS si es ataque volumétrico.
- **Route53**: considerar desviar tráfico a página de mantenimiento si el frontend está comprometido (usar con cautela).

#### F. Detección y análisis a mano

- **GuardDuty**: listar findings relacionados con la instancia:

```bash
aws guardduty list-findings --detector-id <detector-id> --finding-criteria '{"Criterion":{"resource.instanceDetails.instanceId":{"Eq":["i-123456"]}}}'
aws guardduty get-findings --detector-id <detector-id> --finding-ids <id1,id2>
```

- **AWS Detective / Security Hub**: correlacionan para ampliar el scope.

### 4) Playbook de contención: paso a paso (ejemplo: instancia EC2 comprometida — ransomware / criptominería)

#### Primera hora (0–60 min) — “First 60”

1. **Detectar & confirmar** (alerta GuardDuty/EDR/SOC).
2. **Identificar alcance**: qué instancia(s), VPC, subredes, roles IAM, buckets S3 implicados.

   - `aws ec2 describe-instances --instance-ids i-123456`

3. **Preservar evidencia**: crear snapshot del EBS y AMI (ver sección forense).
4. **Aislar**:

   - Asignar `sg-quarantine` a la instancia (ver arriba) o revocar reglas existentes.
   - Si es exfiltración en curso, añadir NACL deny en la subred.

5. **Revocar credenciales comprometidas**: inactivar keys, forzar reset de contraseña para usuarios implicados.
6. **Bloquear IPs externas maliciosas**: WAF/block lists o ACL a nivel infra.
7. **Notificar stakeholders**: CSIRT, legal, operaciones, backups.

#### 1–24 horas — estabilizar

1. Analizar snapshot/AMI con equipo forense en entorno controlado.
2. Buscar lateral movement: revisar CloudTrail, VPC Flow Logs, logs de SSM, ALB logs.
3. Parchear vectores explotados y cerrar puertos abiertos innecesarios.
4. Revisar y bloquear roles IAM abusados; crear nuevos roles/keys si necesario.
5. Preparar restauración desde backups sanos y probar en entorno aislado.

#### Post-24h — erradicación y recuperación

1. Erradicar root cause (remover backdoors, parchar).
2. Restaurar producción desde backups limpios.
3. Re-habilitar conectividad gradualmente con monitoreo intensivo.
4. Reporte post-mortem y lecciones aprendidas.

### 5) Ejemplo concreto — comandos resumen para un caso típico

**Contexto:** i-123456 detectada con comportamiento sospechoso.

```bash
# 1. Crear snapshot forense
aws ec2 describe-volumes --filters Name=attachment.instance-id,Values=i-123456
# suponiendo vol-0123
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "Forensic snapshot i-123456 $(date +%F)"

# 2. Crear AMI forense (no-reboot solo si es aceptado por forense)
aws ec2 create-image --instance-id i-123456 --name "forensic-image-i-123456-$(date +%F)" --no-reboot

# 3. Aislar instancia (asignar SG de cuarentena)
aws ec2 modify-instance-attribute --instance-id i-123456 --groups sg-quarantine

# 4. Inactivar access key de usuario comprometido
aws iam update-access-key --user-name alice --access-key-id AKIA... --status Inactive

# 5. Buscar findings GuardDuty relacionados
aws guardduty list-findings --detector-id <detector-id> --finding-criteria '{"Criterion":{"resource.instanceDetails.instanceId":{"Eq":["i-123456"]}}}'
```

Siempre documenta cada comando (quién lo ejecutó, por qué y hora).

### 6) Preservación de pruebas y buenas prácticas forenses

- **Antes de modificar:** IMAGEN/SNAPSHOT/AMI (si es posible).
- **No reiniciar la instancia** si necesitas análisis de memoria (RAM). Para volcado de RAM se requieren técnicas especializadas; coordina con expertos forenses.
- **Duplicar logs** a bucket forense con Object Lock (WORM).
- **Registro de cadena de custodia**: quién accedió a qué y cuándo.

### 7) Consideraciones organizativas y de proceso

- Tener **playbooks probados** (tabletop) para los escenarios más probables.
- Roles y permisos predefinidos: define qué acciones puede ejecutar cada miembro del CSIRT en la cuenta AWS (imprescindible evitar “too many cooks”).
- Mantener cuentas de emergencia separadas (break-glass) con MFA y monitorización.
- Pruebas periódicas de restauración de backups.

### 8) Riesgos y advertencias

- **Cambios drásticos** (SCP que niega todo, eliminación masiva de keys) pueden detener operaciones. Úsalos con control y ventana de comunicación.
- **`--no-reboot`** al crear AMI preserva disponibilidad pero puede afectar integridad forense; decide con equipo pericial.
- No intentes _hackear de vuelta_ ni acciones fuera de la jurisdicción legal; coordina con Legal.

### 9) Checklist rápido de contención en AWS (resumen accionable)

- [ ] Identificar recursos afectados (instances, roles, buckets, RDS).
- [ ] Crear snapshots/AMIs y preservar CloudTrail/FlowLogs.
- [ ] Aislar: asignar SG de cuarentena / NACL deny / detener tasks/scale-to-0.
- [ ] Inactivar/rotar credenciales comprometidas.
- [ ] Bloquear IPs maliciosas (WAF / Security Groups / NACL).
- [ ] Notificar CSIRT / Legal / Operaciones.
- [ ] Iniciar análisis forense y búsqueda lateral (Detective, GuardDuty, Athena queries).
- [ ] Plan de recuperación con backups verificados.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 23**                    | **Siguiente 25**             |
| ------------------ | ------------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_23_Identification.md) | [⏩](./12_25_Eradication.md) |
