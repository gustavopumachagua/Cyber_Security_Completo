| **Inicio**         | **atrás 12**                                      | **Siguiente 14**                 |
| ------------------ | ------------------------------------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./8_12_Understand_the_Definition_of_Risk.md) | [⏩](./8_14_Cyber_Kill_Chain.md) |

---

## **Índice**

| Temario                                                                                                                                                                                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [219. Copia de seguridad y restauración](#219-copia-de-seguridad-y-restauración)                                                                                                                  |
| [220. ¿Por qué las copias de seguridad deberían ser parte de su plan de resiliencia cibernética?](#220-por-qué-las-copias-de-seguridad-deberían-ser-parte-de-su-plan-de-resiliencia-cibernética)  |
| [221. AWS 2023: Estrategias de respaldo y recuperación ante desastres para una mayor resiliencia](#221-aws-2023-estrategias-de-respaldo-y-recuperación-ante-desastres-para-una-mayor-resiliencia) |

# **Comprenda las copias de seguridad y la resiliencia**

## **219. Copia de seguridad y restauración**

### 1. ¿Qué es una copia de seguridad y qué es restaurar?

- **Copia de seguridad (backup):** copia de datos (archivos, bases de datos, máquinas) creada para recuperarlos si se pierden, corrompen o son atacados.
- **Restauración (restore):** proceso de recuperar datos desde una copia de seguridad a su estado operativo (parcial o completo) después de un incidente o pérdida.

Objetivo: minimizar impacto del fallo sobre el negocio (perdida de datos, downtime).

### 2. Conceptos clave

- **RPO (Recovery Point Objective):** cuánto dato puedes _perder_ como máximo (ej.: RPO = 1 hora → toleras perder 1 hora de datos).
- **RTO (Recovery Time Objective):** cuánto tiempo puedes _tardar en recuperar_ servicios (ej.: RTO = 2 horas).
- **Retention (retención):** cuánto tiempo guardas cada backup (días/meses/años).
- **Full, incremental, differential** (ver sección 4).
- **Immutability / WORM:** copias que no se pueden modificar ni borrar (útil contra ransomware).
- **Air-gapped backup:** copia físicamente desconectada de la red (proteger contra ataques que borran backups).
- **Verificación/Integrity checks:** comprobar que las copias son legibles y completas.
- **Disaster Recovery (DR):** plan más amplio que incluye backups, recuperación de infra, comunicaciones, roles y testing.

### 3. Estrategias y buenas prácticas (alto nivel)

- **3-2-1:** 3 copias, en 2 medios distintos, 1 copia off-site (mínimo).
- **Extensión moderna (3-2-1-1-0):** +1 copia off-line/air-gapped, 0 errores (probar restores).
- **Principio de menor privilegio:** procesos de backup no deben tener acceso inutilemente amplio.
- **Cifrado en tránsito y en reposo.**
- **Automatización + monitorización + alertas.**
- **Pruebas regulares de restauración (al menos trimestralmente, más si RTO/RPO estrictos).**
- **Documentar runbooks de restauración (paso a paso).**

### 4. Tipos de backup (qué elegir según necesidad)

- **Full (completa):** copia de todo el conjunto; restaura más rápida pero exige más espacio.
- **Incremental:** guarda sólo cambios desde la última copia (full o incremental anterior). Ahorra espacio; restaure suele requerir cadena completa.
- **Differential:** guarda cambios desde la última full. Más rápida de restaurar que incremental pero ocupa más que incremental.
- **Snapshots (instantáneas):** punto en el tiempo (almacenamiento o hipervisor). Muy útiles para RPO muy bajos; atención a retención y consistencia de bases de datos.
- **Replication (replicación):** copia continua a otro host/centro (útil para alta disponibilidad; no reemplaza backups porque replicaría corrupción/crypto-ransom).
- **File-level vs Block-level vs Image/Bare-metal:** archivos individuales, bloques de disco, o imagen completa del sistema (útil para recuperación completa del servidor).

### 5. Ejemplos prácticos — comandos y flujos

#### 5.1 Backup de archivos con `rsync` y snapshots con hardlinks (Linux)

Usa `rsync` + `--link-dest` para mantener snapshots incrementales eficientes.

```bash
# Estructura: /backups/daily.2025-09-01 etc.
TODAY=/backups/$(date +%F)
YESTERDAY=/backups/$(date -d "yesterday" +%F)

mkdir -p "$TODAY"
rsync -a --delete --link-dest="$YESTERDAY" /data/ "$TODAY/"
```

- Resultado: cada snapshot parece completo pero comparte inodos (eficiente en espacio).
- Restauración: copiar archivos desde `/backups/<fecha>/`.

#### 5.2 Backup y restore con `restic` (sencillo, seguro)

```bash
# init repo (local o remoto, e.g. S3)
restic -r /backups/restic-repo init

# backup
restic -r /backups/restic-repo backup /data

# listar snapshots
restic -r /backups/restic-repo snapshots

# restore (ejemplo: restaurar al directorio /restore)
restic -r /backups/restic-repo restore <snapshot-id> --target /restore
```

`restic` incluye cifrado, deduplicación y comprobación de integridad.

#### 5.3 Base de datos PostgreSQL (pg_dump y base backup)

- **Consistencia lógica (pg_dump):**

```bash
# Dump lógico
pg_dump -U dbuser -Fc -f /backups/dbname_$(date +%F).dump dbname

# Restore
pg_restore -U dbuser -d dbname_restored /backups/dbname_2025-09-01.dump
```

- **Backup consistente a nivel físico (pg_basebackup + WAL archiving)** para point-in-time recovery (PITR):

```bash
pg_basebackup -D /var/lib/postgresql/basebackups/ -F tar -z -P
# Configure wal archiving and then to restore use recovery.conf / restore_command
```

#### 5.4 SQL Server (T-SQL) — full + log + restore point-in-time

```sql
-- Full backup
BACKUP DATABASE [MiDB] TO DISK = N'C:\backups\MiDB_FULL.bak' WITH COMPRESSION;

-- Transaction log backup (para PITR)
BACKUP LOG [MiDB] TO DISK = N'C:\backups\MiDB_LOG.trn';

-- Restore full
RESTORE DATABASE [MiDB] FROM DISK = N'C:\backups\MiDB_FULL.bak' WITH NORECOVERY;

-- Restore logs up to point-in-time
RESTORE LOG [MiDB] FROM DISK = N'C:\backups\MiDB_LOG.trn' WITH STOPAT = '2025-09-01T12:34:00', RECOVERY;
```

PITR: permite restaurar la base hasta un instante concreto.

#### 5.5 AWS EBS snapshot (CLI) — snapshot + crear volumen

```bash
# snapshot
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "backup 2025-09-01"

# crear volumen desde snapshot (en otra zona si quieres DR)
aws ec2 create-volume --snapshot-id snap-0abcdef1234567890 --availability-zone us-east-1b
```

Para RDS usa snapshots gestionados; para S3 activa versioning y lifecycle rules.

#### 5.6 VM / Bare-metal — ejemplo con `dd` (imagen) y `partclone` / herramientas específicas

- `dd` hace copia bit-a-bit (rápida y simple, pero sin dedup):

```bash
dd if=/dev/sda of=/backups/server1.img bs=4M status=progress
```

- Para VMs, usa herramientas del hipervisor (VMware snapshots + Veeam/Commvault).

### 6. Backups en entornos cloud y containers

- **SaaS:** exportar datos (backup nativo de app o API).
- **Kubernetes:** herramientas como **Velero** para snapshots de volúmenes y recursos (manifests).
- **Cloud-native:** usar snapshots de bloque (EBS), S3 para objetos (activar Versioning + MFA Delete para mayor protección), y políticas de lifecycle para retención.
- **Notas:** la "shared responsibility" — cloud provider protege infraestructura, tú proteges datos y configuración.

### 7. Verificación y pruebas (lo más importante)

- **Verificar cada backup** (hashes, `restic check`, `verify` en herramientas comerciales).
- **Test restore regular:** simular recuperaciones (full restore, DB PITR, VM boot).
- **Tabla de pruebas:** periodicidad (diaria/semana/mes) según criticidad.
- **Checklist post-restore:** integridad de datos, funcionalidad de la app, latencias, logs.

### 8. Runbook de restauración — ejemplo práctico (plantilla rápida)

1. **Detección y clasificación:** ¿Qué falló? (hardware, ransomware, borrado humano).
2. **Comunicación:** notifica incident response, IT y stakeholders (lista de contactos).
3. **Contención:** aislar sistemas si hay malware.
4. **Seleccionar backup apropiado:** elegir snapshot o backup con RPO compatible.
5. **Preparar destino:** aprovisionar servidor/volumen limpio si es bare-metal.
6. **Ejecutar restore:** seguir pasos (ejemplos en sección 5).
7. **Validar:** pruebas de integridad, pruebas funcionales, ver logs de errores.
8. **Poner en producción:** cutover controlado y monitorizado.
9. **Postmortem:** documentar causa raíz y acciones para evitar repetición.

### 9. Seguridad y cumplimiento en backups

- **Cifrar datos en tránsito (TLS) y en reposo (AES-256).**
- **Gestionar claves (KMS) con control de acceso y rotación.**
- **Restricciones de retención por cumplimiento (ej.: GDPR, HIPAA).**
- **Registro de accesos a backups (audit logging).**
- **Immutability y WORM** para proteger contra borrado por ransomware o usuarios maliciosos.

### 10. Políticas y retención — ejemplo típico

- **Datos críticos (financieros, transaccionales):** full semanal + logs incrementales diarios; retención 7 años (por regulación).
- **Datos operativos:** full mensual + incrementales diarios; retención 1 año.
- **Datos temporales / dev:** retención corta (7–30 días).
  Siempre alinear a requisitos legales y costo.

### 11. Errores comunes y cómo evitarlos

- **No probar restores.** (El peor error).
- **Confiar sólo en replicación:** la replicación copia corrupción/crypto.
- **Backups sin cifrado ni control de acceso.**
- **Retención mal definida** (borrar algo que debes conservar por ley).
- **Falsos positivos en backup (se marca como OK pero hay corrupción).** — solución: checksums / verify.

### 12. Métricas a monitorizar (KPI)

- % de backups completados OK (target 100%).
- Tiempo medio de backup.
- Tiempo medio de restore (MTTR real comparado con RTO).
- Tasa de verificación fallida.
- % de pruebas de restore exitosas.
- Volumen de datos protegidos y coste mensual.

### 13. Casos de uso resumidos con decisiones

- **Laptop personal:** RPO flexible (horas), usar backup en la nube (OneDrive/Google Drive con versioning) + imagen ocasional.
- **Pequeña empresa (web y DB):** full nightly backup, incremental cada hora, backups off-site (S3) y pruebas mensuales de restore.
- **Empresa grande / financiero:** RPO muy bajo (minutes), RTO muy bajo (horas), solución híbrida: replicación + snapshots + backups off-site con immutability y runbooks probados.

### 14. Checklist rápido (implementación inmediata)

- [ ] Inventario de activos críticos.
- [ ] Definir RPO y RTO por activo.
- [ ] Implementar política 3-2-1 (o 3-2-1-1-0).
- [ ] Automatizar backups y alertas.
- [ ] Cifrar backups y proteger claves.
- [ ] Programar y documentar pruebas de restore.
- [ ] Mantener runbooks y contactos actualizados.
- [ ] Revisar retención y cumplimiento legal.
- [ ] Implementar immutability o air-gap para datos críticos.

### 15. Conclusión

Las copias de seguridad y la restauración son **pilares de la resiliencia**: bien diseñadas reducen pérdidas económicas y reputacionales, y permiten recuperarte rápidamente de fallos, errores humanos o ataques (ransomware). La diferencia entre “tener backup” y “poder recuperar en el tiempo y forma acordados” está en la **verificación, pruebas y runbooks**.

---

[🔼](#índice)

---

## **220. ¿Por qué las copias de seguridad deberían ser parte de su plan de resiliencia cibernética?**

Las copias de seguridad (backups) no son solo “por si acaso”: son **una garantía operativa** que permite recuperar datos y servicios tras incidentes —ya sean fallos hardware, errores humanos, desastres naturales, fallas en la cadena de suministro o ciberataques (p. ej. ransomware). Integradas correctamente:

- **Reducen el impacto** económico y operativo cuando ocurre un incidente.
- **Minimizan el tiempo de inactividad** (RTO) y la pérdida de datos aceptable (RPO).
- **Permiten decisiones informadas** (aceptar, mitigar, transferir riesgo).
- **Sirven como medida de control** frente a extorsión (si tienes backups fiables, es menos probable que pagues rescates).
- **Sostienen cumplimiento** (retenciones legales, restauración ante auditorías).

### Ejemplos reales (resumidos)

- **Ransomware**: organizaciones con backups probados pudieron restaurar sistemas sin pagar rescates; otras que no los tenían sufrieron paradas largas y costes millonarios.
- **Error humano**: eliminación accidental de una base de datos. Un backup reciente permite recuperación completa en minutos u horas.
- **Configuración cloud errónea**: S3 mal configurada que expone datos → si hay historial/versioning, puedes restaurar versiones previas y auditar qué cambió.

### Beneficios concretos (qué gana la empresa)

1. **Disponibilidad del negocio**: reduce MTTR y permite continuidad.
2. **Integridad y recuperación**: restauras puntos consistentes (PITR para bases de datos).
3. **Reducción del riesgo financiero**: menos pérdidas por downtime y menos necesidad de pagar rescates.
4. **Prueba ante reguladores**: demuestras capacidad de recuperación y cumplimiento.
5. **Confianza de clientes**: transparencia y rapidez reduce daño reputacional.

### Ejemplo numérico (coste de downtime vs coste de backup) — cálculo paso a paso

Imagina: downtime cuesta **USD 50,000 por hora**. Sin backups probados, un incidente causa 10 horas de downtime al año. Una solución de backups robusta cuesta **USD 100,000 anuales** y reduce el downtime esperado a **2 horas al año**.

Calculamos ahorro anual por reducción de downtime:

- **Costo sin backups** = 50,000 × 10.
  Paso a paso: 50,000 × 10 = 500,000. → **USD 500,000**.

- **Costo con backups** = 50,000 × 2.
  Paso a paso: 50,000 × 2 = 100,000. → **USD 100,000**.

- **Ahorro por downtime** = 500,000 − 100,000.
  Paso a paso: 500,000 − 100,000 = 400,000. → **USD 400,000**.

- **ROI neto anual** = ahorro − coste de backups
  Paso a paso: 400,000 − 100,000 = 300,000. → **USD 300,000**.

Conclusión: invertir en backups aquí genera **ROI positivo** y reduce riesgo material.

### ¿Cómo encajar backups dentro del plan de resiliencia cibernética? (visión práctica)

1. **Alinearlos con el negocio**: definir RTO y RPO por servicio/activo (no “uno para todos”).
2. **3-2-1-1-0 como regla**: 3 copias, en 2 medios, 1 off-site, 1 offline/air-gapped, 0 errores (probar restores).
3. **Inmutabilidad / WORM**: algunas copias deben ser inmutables para proteger contra borrado por ransomware.
4. **Air-gapped y off-site**: tener una copia físicamente inaccesible desde la red reduce riesgo de eliminación simultánea.
5. **Cifrado y control de acceso**: backups cifrados en tránsito y en reposo; roles y separación de funciones (no todos pueden borrar backups).
6. **Verificación y prueba**: automatizar checks de integridad y planificar restores periódicos (pruebas reales).
7. **Automatización + alertas**: fallos de backup deben alertar inmediatamente (no confiar en cron sin monitoreo).
8. **Integración con IR / DR**: los runbooks de incident response deben incluir pasos de restauración y validación desde backups.
9. **Retención y cumplimiento**: definir retenciones que cumplan leyes y contrato.

### Tipos de backups y cuándo usarlos (resumen)

- **Full**: cuando RTO debe ser bajo; se usa periódicamente.
- **Incremental/Differential**: ahorro de espacio; usar cuando se necesita mucha frecuencia.
- **Snapshots**: para RPO bajos en sistemas virtualizados; tener cuidado con consistencia de DB.
- **Off-site/Cloud**: para protección geográfica; combinar con immutability.
- **Image/Bare-metal**: para recuperación completa de servidores.

### Runbook de restauración (escenario: ransomware) — pasos prácticos

1. **Detección y contención**

   - Aislar segmentos afectados.
   - Desconectar hosts comprometidos de la red.

2. **Evaluación inicial**

   - ¿Qué sistemas están afectados? ¿Qué backups existen (fecha, integridad)?
   - Verificar que las copias candidatas no están infectadas (hashes, sandbox).

3. **Priorizar servicios**

   - Restaurar primero servicios críticos (pagos, AD, directorio, bases de datos).

4. **Preparar entorno aislado de restore**

   - Restaurar en VLAN/entorno de recuperación o DR site para validar sin riesgo.

5. **Restaurar desde backup verificado**

   - Ejecutar restore (full o punto en el tiempo).
   - Registrar logs de restauración y hashes.

6. **Validación funcional**

   - Probar transacciones básicas, integridad de datos y conectividad.

7. **Rotación de credenciales**

   - Cambiar contraseñas y certificados (si la intrusión pudo haber capturado credenciales).

8. **Poner en producción**

   - Cortar/realizar cutover controlado y monitorizar intensamente.

9. **Forense y lecciones**

   - Preservar evidencias, analizar vector de ataque y actualizar controles y playbooks.

### Métricas / KPIs recomendadas para gobernar backups

- % de backups completados con éxito (objetivo ≥ 99–99.9%).
- Tiempo medio de restauración para servicios críticos vs RTO.
- % de pruebas de restauración exitosas en pruebas programadas.
- Edad de la copia más reciente por activo (debe cumplir RPO).
- Nº de backups inmutables / air-gapped disponibles.

### Errores comunes y cómo evitarlos

- **No probar restores** → backups inútiles si no restauran.
- **Confiar solo en replicación** → la replicación propagará corrupción/ransom.
- **Backups accesibles desde la misma red sin protecciones** → vulnerables a eliminación.
- **No cifrar backups** → riesgo por fuga de copias.
- **Retención mal definida** → borrar datos que necesitas por cumplimiento.

### Gobernanza y roles (quién hace qué)

- **CISO / Dirección**: aprobar RTO/RPO, presupuesto y policy.
- **IT Ops / Backup Admin**: implementar, monitorizar y ejecutar restores.
- **SOC / IR**: coordinar restauración en incidentes y validar seguridad post-restore.
- **Legal / Compliance**: definir retenciones legales y requerimientos de auditoría.

### Recomendaciones prácticas inmediatas (checklist rápido)

- [ ] Definir RTO/RPO por servicio crítico.
- [ ] Implementar 3-2-1-1-0 (o mejor).
- [ ] Habilitar immutability + versioning en repositorios cloud.
- [ ] Automatizar verificaciones de integridad y alertas.
- [ ] Programar y ejecutar pruebas de restore (mensual/trimestral según criticidad).
- [ ] Documentar runbooks y responsables.
- [ ] Cifrar backups y controlar accesos.
- [ ] Incluir backups en el plan IR/DR y en ejercicios tabletop.

### Conclusión (resumen ejecutivo)

Las copias de seguridad son **una pieza no negociable** de la resiliencia cibernética: actúan como seguro operativo, reducen la dependencia de pagar rescates, garantizan continuidad ante fallos y demuestran madurez ante reguladores y clientes. Pero su valor real aparece **solo si** están bien diseñadas, protegidas, comprobadas y alineadas con objetivos del negocio (RTO/RPO).

---

[🔼](#índice)

---

## **221. AWS 2023: Estrategias de respaldo y recuperación ante desastres para una mayor resiliencia**

### 1 Visión general rápida

En AWS hay dos frentes complementarios: **(A)** protección de datos mediante backups (S3 versioning, EBS/RDS snapshots, backups gestionados) y **(B)** recuperación operativa (patrones DR que definen cuánto tiempo y datos puedes aceptar perder — RTO/RPO). AWS ofrece herramientas dedicadas (AWS Backup, AWS Elastic Disaster Recovery, S3 CRR, snapshots) para automatizar ambos frentes y orquestar recuperaciones.

### 2 Patrón de recuperación: cuándo usar cada estrategia (resumen y RTO/RPO típico)

AWS y la práctica del sector definen 4 estrategias habituales — elige según **RTO (tiempo a recuperar)** y **RPO (datos que puedes perder)**:

1. **Backup & Restore** — _más barato, RTO: horas/días; RPO: horas/días_

   - Guardas backups (S3, Glacier, snapshots) y restauras tras desastre.
   - Uso: datos no críticos o aplicaciones que toleran restauración desde backup.
   - Documento AWS: lista Backup & Restore como estrategia DR.

2. **Pilot Light** — _coste medio, RTO: decenas de minutos/horas; RPO: bajo_

   - Mantienes datos replicados y componentes mínimos (DB, infra de datos) encendidos en la región de recuperación; orquestas el resto al fallar la primaria.
   - Útil para servicios con dependencia de datos pero que no requieren full capacity en standby.

3. **Warm Standby** — _coste más alto, RTO: minutos; RPO: sub-minutos a minutos_

   - Infra reducida pero ya ejecutándose en la región secundaria; escala para producción si falla la principal.
   - Uso para servicios críticos que requieren rápida restauración sin correr todo en multi-site.

4. **Multi-site Active-Active** — _coste máximo, RTO: near-zero, RPO: near-zero_

   - Tráfico activo en múltiples regiones o cuentas, replicación en tiempo real; failover automático.
   - Uso: aplicaciones con tolerancia cero al downtime y requisitos regulatorios de alta disponibilidad.

(El whitepaper/guía de AWS describe exactamente estas opciones y ayuda a mapear RTO/RPO y costes).

### 3 Servicios clave de AWS y para qué usarlos (con ejemplos prácticos)

#### AWS Backup (servicio centralizado de backups)

- Orquesta backups de RDS, EFS, DynamoDB, EBS, FSx, Storage Gateway, etc.
- Permite **cross-region** y **cross-account** copies automáticas (útil para cumplimiento y resiliencia geográfica).
- Ejemplo de uso: crea un **Backup Plan** que haga snapshots diarios de RDS y copie automáticamente a otra región para retención 90 días.

#### Amazon S3 (versioning + CRR + Object Lock)

- **Versioning**: recuperas versiones anteriores tras borrado accidental.
- **Cross-Region Replication (CRR)**: replica objetos nuevos a otra región (útil para disponibilidad/regulatorio).
- **S3 Object Lock (WORM)** y **MFA Delete**: inmutabilidad para proteger backups contra borrado intencional (ransomware).

#### EBS snapshots / AMIs

- Snapshots puntuales y AMIs para recuperación de máquinas. Snapshots se pueden copiar entre regiones.
- Ejemplo CLI: `aws ec2 create-snapshot --volume-id vol-0123abcd --description "daily backup"` (luego copiar snapshot a otra región si se requiere DR).

#### RDS automated backups & snapshots (PITR)

- RDS soporta backups automáticos y **point-in-time recovery (PITR)** (MySQL, PostgreSQL, Aurora) — crucial para minimizar RPO en DB críticas.

#### DynamoDB On-Demand Backups + PITR

- Backups puntuales y **continuous PITR** para restaurar a un instante determinado.

#### AWS Elastic Disaster Recovery (DRS, ex-CloudEndure)

- Herramienta para replicación continua y recuperación rápida (minimiza downtime y pérdida de datos; orquesta pruebas y failover). Ideal para migrar y/o usar AWS como sitio de recuperación (on-prem → AWS).

#### Otros: AWS Storage Gateway (hybrid backups), AWS Backup Audit Manager (compliance), S3 Glacier/Glacier Deep Archive (cold storage)

- Storage Gateway: puente backups on-prem → S3.
- Glacier: retención de largo plazo, menor coste.

### 4 Ejemplo de arquitecturas (3 casos con pasos concretos)

#### A) _Backup & Restore_ (pequeña aplicación web)

- Producción en us-east-1: EC2 + RDS (prod).
- Backups:

  - EBS snapshots nightly (lifecycle 14d).
  - RDS automated backups + daily snapshot export to S3.
  - S3 buckets con versioning + cross-region replication a us-west-2.

- Recuperación:

  1. Restaurar RDS snapshot o PITR.
  2. Crear nueva EC2 desde AMI / snapshot.
  3. Reconfigurar DNS (Route 53) para apuntar a la nueva IP.

- Coste: bajo-medio; RTO = horas.

#### B) _Pilot Light_ (aplicación con DB crítica)

- Mantén la base de datos replicada (continuous replication o Aurora Global DB / read replicas en DR region).
- En región de recuperación: solo DB replicas (y minimal infra de bootstrap).
- Al fallar: scale up infra (ASG, ALB, RDS) automáticamente via CloudFormation/Auto Scaling.
- Ejemplo: usar **Aurora Global Database** o replicación binlog+DRS.

#### C) _Warm Standby / Multi-site_ (servicio crítico)

- Infra reducida pero corriendo en DR region (autoscaling en min capacity); datos replicados en near-real time (Aurora Global DB, DMS, etc.).
- En failover: aumentas capacidad en DR region (auto scale) y balanceas tráfico global con **Route 53** (latency/health checks) o **Global Accelerator**.
- RTO: minutos.

(Decisiones de diseño: qué partes del stack se mantienen calientes, qué se reconstruye, y quién aprueba failover).

Referencias y plantillas de AWS describen estos patrones y recomendaciones.

### 5 Pasos técnicos prácticos — cómo empezar (playbook mínimo)

1. **Clasifica servicios por criticidad (BIA)** → fija RTO/RPO por workload.
2. **Elige patrón DR** por workload (backup, pilot, warm, multi-site). Refiérete al Well-Architected Reliability/DR guidance.
3. **Configura backups automatizados**:

   - AWS Backup: definir backup plan y lifecycle; habilitar cross-region & cross-account copy.
   - RDS: activar automated backups y PITR; testear restores.
   - S3: habilitar versioning + CRR y proteger buckets críticos con Object Lock (WORM).

4. **Automatiza infra de failover** con IaC (CloudFormation/Terraform) y pipelines para “bootstrap” de la región secundaria.
5. **Implementa replicación de datos en near-real time** donde RPO exige (Aurora Global DB, DMS, Kinesis, DDB Streams).
6. **Protege backups**: cifrado (KMS), IAM con least privilege, S3 Object Lock para inmutabilidad, logging y alertas (CloudWatch, AWS Backup reports).
7. **Prueba y valida**: ejercicios regulares (game days / DR drills). AWS recomienda y provee material para Game Days y workshops.

### 6 Seguridad de backups y hardening (prácticas imprescindibles)

- **Cifrado** en tránsito (TLS) y en reposo (KMS-managed keys).
- **S3 Object Lock (WORM)** + **MFA Delete** para buckets backup críticos (protege contra borrado malicioso).
- **Cross-account backups**: copia a una cuenta de recuperación con permisos separados (reduce blast radius).
- **Control de accesos**: roles bien definidos, separación de funciones (backup admin ≠ prod admin).
- **Auditoría**: habilita CloudTrail para logs de acceso a backups y restauraciones.
- **Immutable backup snapshots** donde sea posible (Object Lock, Backup Vault Lock de AWS Backup).

### 7 Pruebas y gobernanza — no hay DR sin pruebas

- **Game Days**: simula fallos (region down, pérdida de DB) y mide MTTD/MTTR; AWS distribuye workshops y plantillas.
- **Pruebas periódicas**: restauración completa de un workload en entorno aislado (canary recovery).
- **KPIs**: % de backups OK, tiempo medio de restore, éxito de pruebas, edad del último backup vs RPO.
- **Runbooks**: documenta pasos de failover, contactos, scripts IaC para levantar la región DR.

### 8 Costes y trade-offs (práctico)

- **Backup & Restore**: menor coste operativo (almacenamiento + operaciones de restore), mayor RTO.
- **Pilot Light / Warm Standby**: balance coste/RTO — pagas algunos recursos siempre encendidos.
- **Multi-site**: alto coste (capacidad activa en dos lugares) → usar sólo para workloads críticos.
- **Optimización**:

  - Lifecycle rules → mover a Glacier/Deep Archive cuando no necesitas acceso rápido.
  - Use snapshots incremental (EBS) y deduplicación (services like Backup or third-party solutions).

- **Presupuesto**: incluir coste de pruebas de DR y personal en ejercicios en el cálculo de ROI.

### 9 Comandos y snippets útiles (rápidos, para probar en tu cuenta)

- Crear snapshot EBS:

```bash
aws ec2 create-snapshot --volume-id vol-0123456789abcdef0 --description "daily-backup-$(date +%F)"
```

- Copiar snapshot a otra región:

```bash
aws ec2 copy-snapshot --source-region us-east-1 --source-snapshot-id snap-0123456789abcdef0 --region us-west-2 --description "cross-region copy"
```

- Listar backups en AWS Backup:

```bash
aws backup list-backup-vaults
aws backup list-recovery-points-by-backup-vault --backup-vault-name MyVault
```

(Usa IAM roles con permisos mínimos y prueba en cuentas sandbox antes de producción.)

### 10 Errores comunes y cómo evitarlos

- **No probar restores** → backups que no funcionan en producción. (Haz drills).
- **Depender solo de replicación** (replicar corrupción/ransom). Siempre tener backup immutable/off-site.
- **Backups accesibles desde la misma cuenta/sin separación** → un atacante que compromete la cuenta puede borrar backups. Usa cross-account copies.
- **No proteger claves KMS** → cifrado sin control es inútil.
- **No medir RTO/RPO reales** — define, prueba y ajusta.

### 11 Checklist operativo (implementación inmediata)

- [ ] Hacer BIA y mapear RTO/RPO por workload.
- [ ] Configurar AWS Backup plans con cross-region & cross-account copies para workloads críticos.
- [ ] Habilitar S3 versioning + CRR + Object Lock en buckets de backup.
- [ ] Implementar AWS Elastic Disaster Recovery para sistemas on-prem/legacy que requieren RTO bajos.
- [ ] Automatizar IaC para recovery (CloudFormation/Terraform) y mantener runbooks.
- [ ] Programar Game Days / DR drills y registrar métricas.

### Conclusión — mensaje práctico

En 2023 (y hoy) **la resiliencia en AWS combina backups bien protegidos + estrategias DR bien escogidas**. Empieza por clasificar (BIA → RTO/RPO), luego implementa backups automatizados (AWS Backup, S3, snapshots) y selecciona el patrón DR que equilibre coste y objetivo de negocio (backup/pilot/warm/active). Protege tus copias (Object Lock, KMS, cross-account), automatiza la recuperación con IaC y **prueba** con regularidad (Game Days).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 12**                                      | **Siguiente 14**                 |
| ------------------ | ------------------------------------------------- | -------------------------------- |
| [🏠](../README.md) | [⏪](./8_12_Understand_the_Definition_of_Risk.md) | [⏩](./8_14_Cyber_Kill_Chain.md) |
