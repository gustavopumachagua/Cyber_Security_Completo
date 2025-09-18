| **Inicio**         | **atrás 4**          | **Siguiente 6**                                   |
| ------------------ | -------------------- | ------------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_4_MacOS.md) | [⏩](./2_6_Different_Versions_and_Differences.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [46. Importancia de una instalación y configuración adecuadas](#46-importancia-de-una-instalación-y-configuración-adecuadas) |
| [47. Consideraciones de configuración](#47-consideraciones-de-configuración)                                                 |

---

# **Instalación y configuración**

## **46. Importancia de una instalación y configuración adecuadas**

### 1) ¿Por qué es crítica una buena instalación/configuración?

- **Seguridad:** configuraciones por defecto o incompletas dejan puertas abiertas (credenciales por defecto, servicios innecesarios, permisos laxos).
- **Disponibilidad y fiabilidad:** una instalación mal probada puede fallar bajo carga o tras un reinicio (servicios que no arranque).
- **Rendimiento:** parámetros por defecto (buffers, límites de memoria, I/O) no optimizados provocan latencia o cuellos de botella.
- **Mantenibilidad:** sistemas mal documentados o configurados manualmente son difíciles de actualizar y propensos a errores humanos.
- **Cumplimiento/Legal:** configuración incorrecta puede romper requisitos regulatorios (p.ej. cifrado para datos personales).
- **Coste:** errores de configuración generan downtime, incidentes, multas o sobrecosto de infraestructura.
- **Experiencia de usuario:** tiempos de respuesta, integridad de datos y disponibilidad afectan directamente la percepción del servicio.

### 2) Ejemplos concretos (qué puede pasar si se instala/configura mal)

- **Servidor web sin TLS o con certificado expirado →** pérdida de confianza y tráfico; navegadores bloquean el site.
- **Base de datos con permisos excesivos →** fuga de datos por falla de una app comprometida.
- **Disco sin cifrar en equipo robado →** exposición de datos sensibles.
- **IoT con credenciales por defecto →** dispositivo comprometido y usado en botnets.
- **Configuración manual en 50 servidores (sin automatizar) →** diferencias entre nodos → bugs que sólo aparecen en producción.
- **No probar actualizaciones →** parche “seguro” rompe compatibilidad y tumba servicios.

### 3) Buenas prácticas (pasos y flujo recomendado)

1. **Planificación**

   - Definir objetivos (seguridad, SLAs, rendimiento).
   - Inventario de dependencias (OS, versión, librerías, drivers).

2. **Baselines & políticas**

   - Usar plantillas oficiales (CIS, vendor security baselines).

3. **Instalación en laboratorio**

   - Crear un entorno idéntico (VM/containers) para pruebas.

4. **Automatización**

   - Usar IaC (Terraform), CM (Ansible/Chef/Puppet) o containers para reproducibilidad.

5. **Harden y configuración segura**

   - Desactivar servicios innecesarios, aplicar least privilege, activar cifrado y logging.

6. **Pruebas**

   - Funcionales, carga, seguridad (scans, fuzzing, pentest).

7. **Documentación & versionado**

   - Mantener playbooks, scripts y cambios en un repo (git).

8. **Despliegue controlado**

   - Canary / blue-green / phased rollouts.

9. **Monitoreo y alertas**

   - Logs centralizados, métricas, alertas automáticas.

10. **Plan de rollback y backups**

    - Snapshots, backups probados y plan documentado para restaurar.

11. **Patching**

    - Política de parcheo con ventanas y pruebas previas.

### 4) Checklist práctico posterior a la instalación (post-install)

- ¿Arranca el servicio automáticamente? → `systemctl enable --now <servicio>` (Linux).
- ¿Pruebas de configuración OK? → (p. ej. `nginx -t`).
- ¿Puertos mínimos expuestos? → `ss -tulpen` / `netstat -tulpen`.
- ¿TLS válido? → `openssl s_client -connect host:443 -servername host`.
- ¿Backups configurados y probados? → restauración de prueba.
- ¿Métricas y logs llegan a SIEM/monitor? → revisar dashboards.
- ¿Credenciales seguras (no hardcoded)? → revisar vault/secret manager.
- ¿Documentación de pasos y parámetros guardada en git? → yes.

### 5) Comprobaciones y comandos útiles (ejemplos prácticos)

#### Linux — comprobar servicio web Nginx

```bash
# 1) Test configuración nginx
sudo nginx -t

# 2) Estado del servicio
sudo systemctl status nginx

# 3) Puertos escuchando
ss -tulpen | grep nginx

# 4) Ver certificados TLS (ejemplo)
openssl s_client -connect ejemplo.com:443 -servername ejemplo.com
```

#### Windows — checks básicos

```powershell
# Actualizaciones instaladas
Get-HotFix

# Estado BitLocker
Get-BitLockerVolume

# Reglas firewall
Get-NetFirewallRule | Where-Object Enabled -eq "True"
```

#### Base de datos — ejemplo MySQL: comprobar usuarios y permisos

```sql
SELECT User, Host FROM mysql.user;
SHOW GRANTS FOR 'appuser'@'localhost';
```

### 6) Automatización: ejemplo mínimo con Ansible (instalar nginx y configurar)

```yaml
# playbook.yml (Ansible)
- hosts: web
  become: yes
  tasks:
    - name: instalar nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: desplegar config nginx (hardening básico)
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: restart nginx

  handlers:
    - name: restart nginx
      systemd:
        name: nginx
        state: restarted
```

Automatizar asegura que cada servidor quede **idéntico**, auditable y reproducible.

### 7) Configuración segura: ejemplos rápidos

- **Habilitar firewall mínimo (UFW ejemplo Linux):**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp   # SSH (si lo necesitas)
sudo ufw allow 443/tcp  # HTTPS
sudo ufw enable
```

- **Principio de mínimos privilegios**: cuentas de servicio sin shell, usuarios separados para DB y app.
- **Cifrado en tránsito y en reposo**: TLS para conexiones; BitLocker/FileVault/LUKS para disco.
- **Gestión de secretos**: usar vaults (HashiCorp Vault, AWS Secrets Manager) en vez de archivos `.env` en repositorio.

### 8) Testing & validación

- **Tests funcionales**: endpoints básicos, workflows principales.
- **Pruebas de carga**: JMeter, k6 — asegurar comportamiento bajo concurrencia.
- **Pruebas de seguridad**: escaneo de vulnerabilidades (Nessus, OpenVAS), SCA (dependabot, Snyk).
- **Smoke tests post-deploy**: automatizados como parte del pipeline.

### 9) Plan de rollback y backups (imprescindible)

- Tener **snapshots** (volúmenes, VMs) antes de cambios grandes.
- **Backups inmutables** para proteger contra ransomware.
- Documentar procedimientos: **paso a paso** para restaurar un servicio y un plan de comunicación.

### 10) Ejemplo real resumido: despliegue seguro de una API en producción

1. **Plan**: requisitos (SLA 99.9%, TLS obligado, logs centralizados).
2. **Build**: containerizar app (Docker), escanear imagen (Snyk), firmar imagen.
3. **Infra**: Terraform para red y balanceador; Ansible para configuración de VM si procede.
4. **Harden**: desactivar servicios innecesarios, configurar firewall, policy de IAM mínima.
5. **Pruebas**: unit + integration + carga en entorno staging.
6. **Despliegue**: canary en Kubernetes (10% tráfico), monitorizar error rate.
7. **Validación**: smoke tests, métricas OK → aumentar tráfico.
8. **Post-deploy**: alertas y runbook listo; checkpoints de seguridad y backup.

### 11) Errores frecuentes y cómo evitarlos

- **"Instalo manualmente en prod"** → use automatización/versionado.
- **No probar actualizaciones** → tener staging y procesar parches por OUs/riesgo.
- **Dejar credenciales en código** → usar managers de secretos.
- **No documentar** → cada cambio debe tener ticket y repositorio con razonamiento.
- **No monitorizar** → sin métricas, no sabes cuándo algo empeora.

### 12) Recomendaciones finales (resumen accionable)

- **Automatiza** todo lo repetible (IaC + CM).
- **Prueba** antes de desplegar (funcional, carga, seguridad).
- **Aplica baselines** y hardening (CIS/vendor).
- **Documenta** y versiona (git).
- **Asegura**: least privilege, cifrado, gestión de secretos.
- **Monitorea** y establece alertas y runbooks.
- **Ten plan de recuperación** y haz simulacros (DR tests).

---

[🔼](#índice)

---

## **47. Consideraciones de configuración**

### Principios guía (lo que siempre debes aplicar)

- **Reproducibilidad:** la misma configuración produce el mismo sistema (infra-as-code / plantillas).
- **Idempotencia:** aplicar la configuración varias veces deja el sistema en el mismo estado (Ansible, Terraform).
- **Seguridad por defecto:** valores por defecto seguros; exposición mínima.
- **Least privilege:** quien modifica/configura sólo tiene los permisos necesarios.
- **Separación de entornos:** `dev/staging/prod` nunca comparten secretos o bases de datos.
- **Versionado + auditabilidad:** todo cambio debe estar en Git con revisión (PR) y registro (audit log).
- **Validación y testing:** linting, tests unitarios, pruebas en staging antes de prod.
- **Observabilidad:** config debe habilitar métricas, logs y trazas para detectar regresiones.

### Áreas clave y ejemplos prácticos

#### 1) **Dónde almacenas la configuración**

Opciones: archivos en disco, variables de entorno, ConfigMaps (Kubernetes), almacenes de secretos (Vault, AWS Secrets Manager), base de datos de configuración.

**Recomendación:** valores no sensibles en repositorio (config-as-code); secretos en un secret manager.

**Ejemplo (archivo YAML con placeholders):**

```yaml
# config/app.yml (versionada sin secretos)
app:
  port: 8080
  log_level: INFO
database:
  host: db.prod.internal
  port: 5432
  user: app_user
# NO incluir password aquí — usar secret manager
```

#### 2) **Gestión de secretos**

Nunca en texto plano en Git. Usa gestores y control de acceso.

**Ejemplo: Kubernetes — ConfigMap vs Secret**

```yaml
# configmap.yml (no sensibles)
apiVersion: v1
kind: ConfigMap
metadata: { name: app-config }
data:
  LOG_LEVEL: "info"
---
# secret.yml (sólo usar sealed/secreto manager, aquí en claro sólo ilustrativo)
apiVersion: v1
kind: Secret
metadata: { name: app-secrets }
type: Opaque
stringData:
  DATABASE_PASSWORD: "REEMPLAZAR_POR_VAULT"
```

Mejor: usar **SealedSecrets**, SOPS o HashiCorp Vault + CSI driver para montar secretos.

#### 3) **Separación de entornos y override**

Mantén config base y overrides por entorno.

**Estructura Git sugerida**

```
/config
  /base
    app.yml
  /overrides
    dev.yml
    staging.yml
    prod.yml
```

En runtime la app aplica `base` + `override`.

#### 4) **Automatización y reproducibilidad (IaC & CM)**

Usa Terraform/CloudFormation + Ansible/Chef/Puppet/Helm.

**Ejemplo Ansible (plantilla Jinja)**

```yaml
# roles/app/templates/app.env.j2
PORT={{ app.port }}
LOG_LEVEL={{ app.log_level }}
DB_HOST={{ db.host }}
DB_USER={{ db.user }}
DB_PASS={{ vault_db_password }}
```

Ansible recupera `vault_db_password` desde Ansible Vault o integración con HashiCorp Vault.

#### 5) **Validación y testing de configuración**

- Lint: `yamllint`, `terraform validate`, `ansible-lint`, `kubeval`.
- Tests: _molecule_ para roles Ansible; _terratest_ o _kitchen-terraform_ para IaC.
- Pruebas de integración en CI antes de merge.

**Ejemplo GitHub Actions (yaml lint + terraform fmt):**

```yaml
name: CI
on: [pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: yamllint config/
      - run: terraform fmt -check
```

#### 6) **Feature flags / runtime configuration**

Para cambiar comportamiento sin deploy. Ej: LaunchDarkly, Unleash o toggles propios.

**Ejemplo (pseudocódigo):**

```python
if feature_flag("payments_v2", user_id):
    use_payments_v2()
else:
    use_payments_v1()
```

#### 7) **Configuración en contenedores y Kubernetes**

- Usa **ConfigMaps** para config no sensibles; **Secrets** para credenciales.
- Define **readiness** y **liveness probes** para controlar despliegues.

**Ejemplo de probe en un Deployment:**

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### 8) **Gestión de configuraciones en el sistema operativo**

- Preferir **unit files systemd** y variables de entorno en `/etc/sysconfig/` o `/etc/default/` (según distro).
- Evitar editar el unit file directamente; usar `systemctl edit --full` para cambios gestionables.

**Ejemplo systemd unit que carga env file:**

```ini
[Service]
EnvironmentFile=/etc/myapp/myapp.env
ExecStart=/usr/local/bin/myapp
Restart=always
```

#### 9) **Seguridad de configuración**

- Desactivar servicios innecesarios.
- Forzar TLS (HSTS), ciphers actuales.
- Aplicar **CSP**, headers de seguridad en web servers.
  **Ejemplo Nginx HTTPS básico + headers:**

```nginx
server {
  listen 443 ssl;
  server_name ejemplo.com;
  ssl_certificate /etc/ssl/certs/fullchain.pem;
  ssl_certificate_key /etc/ssl/private/privkey.pem;
  add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
  add_header X-Content-Type-Options nosniff;
  ...
}
```

#### 10) **Límites, recursos y rendimiento**

- Fija `ulimit`, límites de systemd `LimitNOFILE`, quotas de disco.
- Para contenedores, usa `requests/limits` en Kubernetes para CPU y memoria.

**Ejemplo systemd limit:**

```ini
[Service]
LimitNOFILE=65536
LimitNPROC=4096
```

### Plantillas y snippets útiles

#### Docker Compose con `env_file`

```yaml
version: "3.8"
services:
  web:
    image: myapp:latest
    env_file:
      - .env.prod
    ports:
      - "8080:8080"
```

#### Terraform (ejemplo: inyectar user-data con variables)

```hcl
variable "app_port" { default = 8080 }

resource "aws_instance" "app" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
  user_data     = templatefile("${path.module}/cloud-init.tpl", { port = var.app_port })
}
```

### Checklist pre-deploy (rápido)

1. `lint` y `validate` de todos los artefactos (YAML, Terraform, Helm).
2. Secrets en secret manager — no en repositorio.
3. Configuración por entorno probada en staging.
4. Readiness/liveness configurados.
5. Alertas y dashboards listos para cambio.
6. Backups y plan de rollback validados.
7. Revisión de seguridad (scans SCA, pruebas básicas).
8. Documentación y runbook para el cambio.

### Errores comunes (y cómo evitarlos)

- **Secrets en Git:** usar Sops/Vault + CI integraciones.
- **Cambios manuales en prod:** automatiza e impide SSH directo con políticas.
- **No versionar config:** usar Git (prs) + tagging de releases.
- **Valores por defecto inseguros:** aplicar “secure default” y exigir override explícito.
- **No tests:** incluir validaciones automáticas en CI (kubeval, yamllint, terraform plan).

### Buenas prácticas operativas

- **GitOps**: todo cambio por PR y merge — ArgoCD/Flux para aplicar cambios en clusters.
- **Config as code**: plantillas parametrizadas, no copy-paste.
- **Audit & policy**: OPA/Gatekeeper para políticas en Kubernetes; Terraform Cloud policies.
- **Rotation de secretos**: automatiza caducidad/rotación.
- **Documenta**: explica la intención de cada llave de config (README en el repo).

### Cómo comprobar que tu configuración está funcionando (comandos útiles)

- Lint y validación: `yamllint`, `kubeval`, `terraform validate`, `ansible-lint`.
- Ver unidades systemd: `systemctl status myapp`
- Puertos escuchando: `ss -tulpen`
- Checks en Kubernetes: `kubectl describe pod mypod`, `kubectl get cs`, `kubectl logs`.
- Revisar secrets montados: `kubectl exec -it pod -- printenv | grep SECRET` (solo en debugging, evita exponer).

### Resumen y prioridad de inicio

1. **Secret manager + versionado en Git** (lo primero).
2. **Automatización (IaC + CM)** para reproducibilidad.
3. **Pruebas + CI** que validen config y detecten regresiones.
4. **Observabilidad y rollback** para seguridad y recuperación.
5. **Hardening y least privilege** antes del go-live.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**          | **Siguiente 6**                                   |
| ------------------ | -------------------- | ------------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_4_MacOS.md) | [⏩](./2_6_Different_Versions_and_Differences.md) |
