| **Inicio**         | **atrás 23**                                           | **Siguiente 25**                               |
| ------------------ | ------------------------------------------------------ | ---------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_23_Understand_Concept_of_Defense_in_Depth.md) | [⏩](./8_25_Understand_Basics_of_Forensics.md) |

---

## **Índice**

| Temario                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------ |
| [248. ¿Qué es un runbook?](#248-qué-es-un-runbook)                                                                             |
| [249. Crear runbooks de automatización con AWS Systems Manager](#249-crear-runbooks-de-automatización-con-aws-systems-manager) |

# **Comprender el concepto de Runbooks**

## **248. ¿Qué es un runbook?**

![Runbooks](/img/8_Security_Skills_and_Knowledge/Runbooks.jpg "Runbooks")

Un **Runbook** (o “libro de ejecución”) es un **documento o guía estructurada que describe paso a paso cómo ejecutar tareas operativas repetitivas** en TI, DevOps, seguridad o soporte.

👉 Pueden ser **manuales** (un PDF o wiki con instrucciones) o **automatizados** (scripts en plataformas como Ansible, Azure Automation, AWS SSM, etc.).

**Ejemplo sencillo:**

Un runbook para **reiniciar un servidor caído** incluiría:

1. Verificar logs del sistema.
2. Ejecutar comando de reinicio remoto (`ssh server "sudo reboot"`).
3. Validar estado del servicio con `systemctl status`.
4. Confirmar monitoreo en la consola de alertas.

### 📅 ¿Cuándo se deben utilizar los Runbooks?

Los runbooks se usan para **tareas frecuentes, críticas o repetitivas** que deben seguir un proceso claro y estandarizado.

🔹 Casos típicos:

- Respuesta a incidentes (ej. detener un ataque DDoS).
- Reinicio de servicios o máquinas virtuales.
- Creación de usuarios o gestión de accesos.
- Aplicación de parches programados.
- Backups y restauración de datos.
- Escalado de aplicaciones en la nube.

👉 Ejemplo: Si una aplicación deja de responder cada cierto tiempo, un **runbook automatizado** puede reiniciar el servicio sin intervención humana.

### 📖 Diferencia entre un Runbook y un Playbook

Aunque suelen confundirse, tienen **propósitos diferentes**:

| **Característica** | **Runbook**                                                | **Playbook**                                                                                  |
| ------------------ | ---------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| **Definición**     | Documento paso a paso para ejecutar una tarea específica.  | Colección de procedimientos y estrategias para manejar un escenario completo (ej. incidente). |
| **Alcance**        | Tarea puntual y repetitiva.                                | Respuesta integral a situaciones más complejas.                                               |
| **Ejemplo**        | Reiniciar un servidor, rotar credenciales, verificar logs. | Manejar un ataque de ransomware (contención, comunicación, recuperación).                     |
| **Automatización** | Suele ser más sencillo (scripts o instrucciones).          | Puede involucrar múltiples runbooks y coordinación entre equipos.                             |

👉 Piensa así:

- **Runbook** = manual de una sola tarea.
- **Playbook** = orquesta varios runbooks y decisiones estratégicas.

### 🏗️ Creación de una plantilla de Runbook para tu empresa

Para que un runbook sea útil, debe ser **claro, accesible y probado**. Aquí te muestro los pasos recomendados:

#### 🔹 Paso 1: Planificación de un nuevo runbook

- **Identificar la necesidad:** ¿Qué tarea se repite y merece estandarización?

  - Ejemplo: “Reiniciar el servicio web si se detiene.”

- **Definir responsables:** ¿Quién puede ejecutarlo (N1, N2, ingenieros de seguridad)?
- **Establecer condiciones de uso:** ¿Cuándo aplicarlo y cuándo escalarlo?

  - Ejemplo: “Ejecutar si el tiempo de inactividad < 10 minutos; escalar si persiste.”

📌 _Tip Security+:_ Documentar roles evita errores de personal no autorizado.

### 🔹 Paso 2: Escribir tu runbook

Estructura recomendada:

1. **Título:** “Reinicio del servicio web Apache en Linux”.
2. **Objetivo:** Restaurar el servicio en caso de caída.
3. **Precondiciones:**

   - Acceso SSH.
   - Usuario con permisos de `sudo`.
   - Monitoreo habilitado (Nagios, Zabbix, Datadog).

4. **Pasos detallados:**

   - `ssh usuario@servidor`
   - `sudo systemctl restart apache2`
   - `systemctl status apache2`
   - Verificar disponibilidad desde navegador o `curl`.

5. **Resultados esperados:** servicio activo en <2 min.
6. **Acciones en caso de error:** escalar al equipo N3 o crear ticket crítico.

#### 🔹 Paso 3: Probar, actualizar y mejorar runbooks

- **Prueba inicial:** ejecutar en entorno de prueba o mantenimiento.
- **Validación:** otro miembro del equipo debe seguirlo para comprobar claridad.
- **Revisión periódica:** actualizar cuando cambian sistemas (ej. `systemctl` reemplaza `service`).
- **Retroalimentación:** registrar cuánto tarda la ejecución y qué problemas surgieron.

👉 Ejemplo:

Un runbook para **restaurar base de datos** debe actualizarse si se cambia de **MySQL a PostgreSQL** o si se implementa un nuevo sistema de backups (como AWS RDS snapshots).

### ✅ Resumen final

- Un **Runbook** es un manual paso a paso para ejecutar tareas repetitivas en TI o seguridad.
- Se usan en **operaciones críticas, mantenimiento y respuesta a incidentes**.
- **Diferencia clave:** Runbook = tarea puntual; Playbook = escenario amplio que puede usar múltiples runbooks.
- Para crear un runbook:

  1. Planifica (identifica tarea, condiciones y responsables).
  2. Escríbelo (objetivo, precondiciones, pasos, resultados, fallback).
  3. Pruébalo y actualízalo regularmente.

---

[🔼](#índice)

---

## **249. Crear runbooks de automatización con AWS Systems Manager**

### 1) ¿Qué es un _runbook_ en Systems Manager?

En AWS, un **runbook** es un **Systems Manager document de tipo `Automation`** que define un flujo (pasos) para realizar tareas operativas: parchar instancias, crear AMIs, ejecutar remediaciones, etc. Cada runbook contiene pasos (`mainSteps`) con acciones específicas (llamadas _Automation actions_) que se ejecutan de forma secuencial o condicional.

### 2) ¿Cuándo usar runbooks?

Casos típicos:

- Backups/crear AMIs periódicos.
- Patching y remediación automática de vulnerabilidades.
- Respuesta automatizada a alertas (p. ej. revocar claves, aislar instancias).
- Tareas repetitivas de operaciones (provisionamiento, tagging, validaciones).
  AWS publica muchos runbooks predefinidos que puedes usar o adaptar.

### 3) Cómo puedes **author** (crear) runbooks — opciones

- **Document Builder (console visual)**: diseñador low-code en la consola para arrastrar/soltar acciones.
- **YAML / JSON**: escribes el documento (`schemaVersion: "0.3"`) y lo subes.
- **VS Code (AWS Toolkit)**: plantillas y editor integrado para crear y publicar documentos.
- **AWS CLI / SDK / CloudFormation / Terraform**: para publicar documentos de forma automatizada.

  Elegir depende de si quieres control fino (YAML) o rapidez (Document Builder).

### 4) Anatomía rápida de un runbook (elementos clave)

- `schemaVersion` — versión del esquema (p.ej. `0.3`).
- `description` — texto (puede usar Markdown).
- `assumeRole` (opcional) — rol IAM que Automation asume para actuar sobre recursos (`AutomationAssumeRole`).
- `parameters` — entrada configurable al ejecutar el runbook.
- `mainSteps` — lista de pasos; cada paso tiene `name`, `action` y `inputs`.
- `outputs` (opcional) — valores de salida del runbook.
- **Actions**: hay muchas (`aws:runCommand`, `aws:createImage`, `aws:executeAwsApi`, `aws:executeScript`, `aws:branch`, `aws:pause`, `aws:invokeLambdaFunction`, etc.). Puedes encadenar outputs de un paso como inputs a otro.

### 5) Ejemplo 1 — runbook simple: ejecutar comandos en una instancia (YAML)

Este runbook **ejecuta un comando shell** (a través de Run Command) en una instancia objetivo. Úsalo en laboratorio o en entornos con autorización.

```yaml
description: "Run a simple shell command on target EC2 instance"
schemaVersion: "0.3"
assumeRole: "{{ AutomationAssumeRole }}"
parameters:
  InstanceIds:
    type: "StringList"
    description: "One or more EC2 instance IDs"
  Commands:
    type: "StringList"
    description: "Shell commands to run"
    default:
      - "echo 'Hello from SSM Automation'"
mainSteps:
  - name: runShell
    action: aws:runCommand
    inputs:
      DocumentName: "AWS-RunShellScript"
      InstanceIds: "{{ InstanceIds }}"
      Parameters:
        commands: "{{ Commands }}"
      CloudWatchOutputConfig:
        CloudWatchLogGroupName: "/aws/ssm/automation/example"
        CloudWatchOutputEnabled: true
```

- `aws:runCommand` invoca el documento `AWS-RunShellScript` en la(s) instancia(s).
- La salida puede enviarse a **CloudWatch Logs** (útil para depuración).

### 6) Ejemplo 2 — runbook realista: crear AMI y eliminar AMIs antiguas

Patrón clásico: crear AMI de una instancia y eliminar la imagen más antigua. Resumen de pasos (según la guía de AWS):

1. `aws:createImage` → crea la AMI.
2. `aws:waitForAwsResourceProperty` → esperar que la AMI esté `available`.
3. `aws:executeScript` → ejecutar script (Python) que liste AMIs del instance-id y devuelva la AMI más antigua.
4. `aws:deleteImage` → eliminar la AMI antigua.

Esto te permite extender o adaptar runbooks gestionados por AWS (por ejemplo `AWS-CreateImage`) con lógica adicional.

### 7) Publicar y ejecutar runbooks (CLI / Console / CloudFormation)

- **Subir documento (CLI)**:

```bash
aws ssm create-document \
  --name "My-Automation-Runbook" \
  --document-type "Automation" \
  --content file://my-runbook.yaml \
  --document-format YAML
```

(`--content` no debe exceder 64 KiB; se recomienda mantener el documento modular).

- **Ejecutar (start)**:

```bash
aws ssm start-automation-execution \
  --document-name "My-Automation-Runbook" \
  --parameters "InstanceIds=['i-0123456789abcdef0']" \
  --mode "Auto"
```

La llamada devuelve `AutomationExecutionId` que puedes consultar con `aws ssm get-automation-execution`.

- **CloudFormation**: puedes crear `AWS::SSM::Document` con la propiedad `Content` que contenga tu YAML/JSON. Útil para infra-as-code y versionado.

### 8) IAM y permisos — el punto crítico de seguridad

- **AutomationAssumeRole / assumeRole**: la mayoría de runbooks que tocan recursos requieren un rol que la Automation _asuma_ para realizar acciones (p. ej. crear AMI, llamar a EC2, eliminar snapshots). Otorga sólo los permisos mínimos en el rol.
- Además, las instancias gestionadas deben tener el **Instance Profile** adecuado (para `aws:runCommand` / Run Command) y SSM Agent instalado.

### 9) Registro, monitoreo y depuración

- **CloudWatch Logs**: puedes enviar la salida de `aws:executeScript` y Run Command a CloudWatch Logs para analizarla. Muy útil para depurar runbooks.
- **CloudWatch Metrics & EventBridge**: Automation emite métricas y eventos que puedes usar para alarmas o pipelines (p. ej. lanzar workflows Step Functions o crear OpsItems).

### 10) Limitaciones, cuotas y orquestación a gran escala

- Concurrency: por cuenta/región hay **límites de concurrencia** (p. ej. 100 automations simultáneas por cuenta por región; hay colas y rate control). Planifica rate control si apuntas a muchas instancias.
- Tamaño del documento: contenido máximo \~64 KiB para `create-document`.

### 11) Buenas prácticas y recomendaciones (resumidas)

- **Prueba en staging** (snapshots) antes de ejecutar en prod. (los pasos de create/delete pueden ser destructivos).
- **Principio de menor privilegio**: role para Automation con permisos mínimos.
- **Usa Parameter Store (SecureString)** para secretos y recupéralos dentro del runbook (no embebas secretos en el YAML). Puedes recuperar parámetros con `aws:executeAwsApi` o `aws:executeScript`.
- **Aprobaciones manuales**: incorpora `aws:approve` si alguna acción debe ser aprobada por un humano (p. ej. eliminar AMIs).
- **Registra salida en CloudWatch** y configura EventBridge para integración con tus pipelines/SOC.
- **Document Builder + Versionado**: aprovechar Document Builder para prototipado y luego versionar YAML en repositorio (Git) y desplegar via CI/CD (CloudFormation/Terraform).

### 12) Integraciones útiles

- **Step Functions**: invocar runbooks desde Step Functions para orquestación avanzada (wait-for-token patterns). Útil para flujos que requieren espera/compensación.
- **EventBridge / OpsCenter**: disparar runbooks automáticamente cuando llegue una alerta o crear OpsItems con runbooks asociados.

### 13) Checklist rápido para crear tu primer runbook seguro

1. Definir caso de uso y parámetros (qué necesita el runbook).
2. Escribir YAML con `schemaVersion: '0.3'`, `parameters` y `mainSteps`.
3. Crear rol IAM mínimo y probar `assumeRole`.
4. Probar en una instancia de staging (usar `Mode: Interactive` para pasos manuales).
5. Habilitar CloudWatch Logs para salida.
6. Versionar documento en Git; desplegar con CI (CloudFormation/Terraform).
7. Agregar alarmas/metrics y pruebas periódicas.

#### Recursos oficiales (para lectura y copia de ejemplos)

- Crear runbooks / Authoring Automation runbooks — AWS Systems Manager.
- Lista de Automation actions (aws\:runCommand, aws\:createImage, aws\:executeScript, etc.).
- Ejemplo práctico (crear AMI → wait → ejecutar script → delete oldest AMI).
- `aws ssm start-automation-execution` / `create-document` (CLI examples).
- CloudWatch Logs + Automation output (debugging).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 23**                                           | **Siguiente 25**                               |
| ------------------ | ------------------------------------------------------ | ---------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_23_Understand_Concept_of_Defense_in_Depth.md) | [⏩](./8_25_Understand_Basics_of_Forensics.md) |
