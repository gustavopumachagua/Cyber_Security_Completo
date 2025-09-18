| **Inicio**         | **atrás 39**              | **Siguiente 41**                   |
| ------------------ | ------------------------- | ---------------------------------- |
| [🏠](../README.md) | [⏪](./13_39_Patching.md) | [⏩](./13_41_Endpoint_Security.md) |

---

## **Índice**

| Temario                                                                                                              |
| -------------------------------------------------------------------------------------------------------------------- |
| [488. ¿Qué es un servidor Jump?](#488-qué-es-un-servidor-jump)                                                       |
| [489. ¿Qué es un Bastion Host y por qué es tan importante?](#489-qué-es-un-bastion-host-y-por-qué-es-tan-importante) |

# **Jump Server**

## **488. ¿Qué es un servidor Jump?**

![Jump Server](/img/13_Tools_for_Incident_Response_and_Discovery/Jump_Server.jpg "Jump Server")

### 🔹 ¿Qué es un servidor _Jump_ (jump server / bastion host)?

Un **jump server** (también llamado _bastion host_) es una máquina con conectividad controlada que actúa como **punto único y controlado de acceso** para administradores que necesitan entrar a entornos internos o sensibles.

Es la “puerta de servicio” entre redes de administración y redes protegidas: en vez de permitir acceso directo desde Internet a servidores internos, los administradores se conectan primero al jump host y desde allí hacen la administración.

### 🔹 ¿Qué hace un servidor Jump?

Funciones principales:

- **Controlar y mediar el acceso** a sistemas internos.
- **Autenticar** administradores (passwords / claves / certificados / MFA).
- **Registrar y auditar** sesiones (grabación, logs).
- **Aplicar políticas** (qué hosts pueden alcanzarse, qué puertos se permiten, time windows).
- **Actuar como proxy para SSH/RDP/túneles** (ProxyJump, ProxyCommand, RDP Gateway).
- **Reducir la superficie de ataque**: minimiza cantidad de hosts expuestos públicamente.

### 🔹 ¿Quién los usa?

- Equipos de **operaciones**, **SRE**, **administradores de bases de datos**, **equipos de seguridad** y **consultores** que requieren acceso a infraestructuras internas.
- Organizaciones reguladas (financiero, salud, gobierno) que necesitan **auditoría** y control estricto del acceso privilegiado.
- Proveedores de servicios gestionados (MSPs) que administran entornos de clientes.

### 🔹 ¿Cómo funcionan? (arquitectura y flujo)

Esquema típico:

1. El usuario (admin) se conecta al **jump host** (normalmente en una DMZ o VPC de gestión) usando SSH/RDP o una herramienta de acceso centralizada.
2. El jump host autentica al usuario (clave, certificado, MFA).
3. Desde el jump host se inicia la conexión hacia los servidores de administración internos (bajo reglas de firewall restrictivas — solo el jump puede hablar con ellos).
4. El jump **audita** y **registra** la sesión (grabación shell, stdout/stderr, tty, duración, comandos).

Variantes:

- **Single hop**: admin → jump → target.
- **Tiered**: admin → jump1 → jump2 → target (más seguro para entornos con zonas múltiples).
- **ProxyJump / SSH bastion**: el cliente local usa `ssh -J jump user@target` y la conexión pasa por el jump host sin iniciar un shell interactivo en él (transparent proxy).
- **SSM / Session Manager / Cloud Bastion**: proveedores (AWS SSM, Azure Bastion) permiten acceso sin exponer puertos SSH/RDP públicamente.

### 🔹 Ejemplos prácticos (SSH)

#### A — Conexión tradicional (primero al jump, luego al destino)

```bash
# Paso 1: conectar al jump
ssh admin@bastion.example.com

# Paso 2 (desde bastion): conectar al servidor interno
ssh admin@10.0.1.15
```

#### B — ProxyJump (modo moderno, una sola línea)

En tu `~/.ssh/config`:

```text
Host bastion
  HostName bastion.example.com
  User admin
  IdentityFile ~/.ssh/id_rsa

Host internal-*
  User admin
  ProxyJump bastion
```

Ahora:

```bash
ssh internal-db-01   # ssh usará bastion como hop intermedio automáticamente
# internals se pueden definir con HostName 10.0.1.15 etc.
```

#### C — Uso de certificados SSH (sin keys distribuidas muchas veces)

Crear CA (host de firma) y firmar claves de usuario:

```bash
# Generar CA (una vez, en CA offline)
ssh-keygen -t rsa -b 4096 -f ca_user

# Firmar la clave pública de usuario
ssh-keygen -s ca_user -I user_julio -n julio -V +52w user_julio.pub

# En bastion y en servidores internos, permitir usuarios cuyas claves están firmadas por la CA:
# en /etc/ssh/sshd_config:
TrustedUserCAKeys /etc/ssh/ca_user.pub
```

Ventaja: no tienes que distribuir `authorized_keys` en cada servidor — confías en la CA y controlas expiración.

### 🔹 Ventajas de usar servidores Jump Box

1. **Reducción de la superficie de ataque**: menos hosts expuestos públicamente.
2. **Control centralizado**: autenticación, políticas (MFA, bloqueo horario, listas blancas).
3. **Auditoría y cumplimiento**: puedes registrar y revisar sesiones privilegiadas.
4. **Facilita microsegmentación**: sólo el bastion tiene reglas para alcanzar sistemas de gestión.
5. **Gestión de claves**: con certificados y PAM se simplifica renovación/revocación.
6. **Integración con PAM/SSO**: se integra con soluciones modernas (MFA, SSO, PAM).

### 🔹 Desventajas y riesgos de usar Jump Boxes

1. **Punto único de compromiso**: si el bastion se compromete, el atacante puede pivotar.
2. **Gestión adicional**: debes parchearlo, auditarlo y endurecerlo.
3. **Desempeño y escala**: en organizaciones grandes puede volverse un cuello de botella.
4. **Experiencia de usuario**: si no está bien diseñada (p. ej. sin ProxyJump) puede añadir fricción.
5. **Complejidad operativa**: sesiones, registros y retención requieren infraestructura (almacenamiento, SIEM).

Mitigaciones: redundancia, hardening, mínimo software, logs inmutables, segmentación estricta, JIT access, MFA y session recording.

### 🔹 Buenas prácticas y controles recomendados (lista rápida)

- **Definir bastion en una red de gestión aislada** (DMZ o management VPC).
- **Usar autenticación fuerte**: SSH keys + certificados + MFA (YubiKey, TOTP, FIDO2).
- **Evitar passwords** para acceso privilegiado.
- **Implementar certificados SSH con expiración** (no keys sin caducidad).
- **Habilitar `ProxyJump` o `ProxyCommand`** para conexiones transparentes y registros.
- **Registrar y grabar sesiones** (tlog, asciinema, auditd, session manager) y almacenar hashes/firmas para integridad.
- **Políticas de acceso Just-in-Time (JIT)**: conceder acceso temporal y revocar automáticamente.
- **Control de sesiones (limitar comandos, ForceCommand)** si necesario.
- **Enforce AllowTcpForwarding=no si no requieres port forwarding**.
- **Mantener bastion minimalista** (sólo sshd, sin servicios adicionales), parcheado y con MFA/2FA.
- **Auditar y enviar logs a SIEM** (syslog/journald → SIEM).
- **Separar roles**: acceso a jump vs privilegios sudo en target (principio de separación de funciones).
- **Hardening OS**: deshabilitar cuentas locales innecesarias, usar SELinux/AppArmor, firewall local.

### 🔹 Ejemplo de configuración de `sshd_config` (hardening en bastion)

Archivo `/etc/ssh/sshd_config` (fragmento recomendado):

```text
PermitRootLogin no
PasswordAuthentication no
ChallengeResponseAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2
PermitEmptyPasswords no
UsePAM yes
AllowTcpForwarding no         # bloquear port-forward por defecto
X11Forwarding no
MaxAuthTries 3
LoginGraceTime 30
PermitTunnel no
ClientAliveInterval 300
ClientAliveCountMax 2
TrustedUserCAKeys /etc/ssh/ca_user.pub
ForceCommand /usr/local/bin/jump-audit-wrapper   # opcional: wrapper para auditoría
```

`ForceCommand` puede disparar un wrapper que inicia grabación de sesión o aplica restricción por rol.

### 🔹 Grabación y auditoría de sesiones (por qué y cómo)

¿Por qué? Para **cumplimiento** y **forense**: saber qué comandos ejecutó un admin y cuándo.

Opciones:

- **tlog / ttyrec / script**: grabar tty en texto con timestamps.
- **Auditd + process accounting**: recoger execve, user, PID.
- **Session managers**: Teleport, BeyondTrust, CyberArk, Teleport graban sesiones SSH/RDP y ofrecen búsqueda.
- **ProxyJump + server-side recording**: `ForceCommand` que enruta la sesión a un recorder y envía salida a S3 o SIEM.

Ejemplo sencillo (wrapper usando `script`):

```bash
#!/bin/bash
LOGDIR=/var/log/jump_sessions/$(date +%F)
mkdir -p "$LOGDIR"
USERLOG="$LOGDIR/$USER-$(date +%s).log"
script -q -c "/bin/bash" "$USERLOG"
```

(Esto debe estar integrado con controles de integridad y rotación/retención seguros).

### 🔹 Redefiniendo el acceso privilegiado con SSH (prácticas modernas)

SSH tradicional con `authorized_keys` está bien, pero las organizaciones modernas usan:

1. **SSH Certificates (CA model)**

   - No hay que distribuir claves públicas por todo; firmas temporales y revocación centralizada.
   - Controlas validez e identidad.

2. **ProxyJump / SSH Agent Forwarding evitado**

   - Evitar `ForwardAgent` porque puede exponer claves; mejor usar certificados o short-lived keys.

3. **MFA y SSO**

   - Integración con SSO (SAML/OIDC) y MFA: ex. Teleport, platform PAMs integran SSH con SSO.
   - SSH + FIDO2 hardware keys o TOTP.

4. **Just-in-Time & Just-Enough-Access**

   - Crear accesos temporales con scope limitado (solo a hosts requeridos) y duración corta.

5. **Session management + recording + command filtering**

   - Grabación automatizada, búsqueda de comandos y bloqueo de ciertos binarios sensibles.

6. **Zero Trust model**

   - Validar no sólo identidad sino posture del cliente (EDR, MDM) antes de conceder acceso.

Herramientas reconocidas: **Teleport**, **BastionZero**, **BeyondTrust**, **CyberArk** (PAM), **AWS SSM Session Manager** y **Azure Bastion** — muchas ofrecen certificados, session recording, MFA, RBAC y JIT.

### 🔹 Arquitectura ejemplo (producción pequeña)

```
Internet
   |
   +-- Load Balancer (opcional para bastion HA)
         |
       Bastion Cluster (HA)  <-- MFA, CA-SSH, Session Recorder
         |
  Management VPC/Subnet (ACLs muy restrictivas)
    |    |    |
  App1  DB1  Kafka1  (solo accesible desde bastion por SSH)
```

- El bastion tiene reglas de seguridad (SGs) que solo aceptan SSH desde IPs corporativas o MFA gateway.
- Targets aceptan SSH **solo** desde IPs del bastion.
- Todos los bastion logs van al SIEM y las sesiones grabadas a almacenamiento inmutable.

### 🔹 Checklist de implementación (rápido)

- [ ] Definir propósito y alcance del bastion.
- [ ] Crear imagen hardenizada (minimal OS + sshd + logging + no servicios extra).
- [ ] Habilitar autenticación por certificados SSH y MFA.
- [ ] Hacer `sshd_config` seguro (sin PasswordAuthentication).
- [ ] Implementar ProxyJump en `~/.ssh/config` para UX.
- [ ] Habilitar session recording + SIEM ingestion.
- [ ] Restringir firewall: sólo bastion puede reach internal hosts.
- [ ] Automatizar parcheo e imagen inmutable (AMIs, Golden Image).
- [ ] Documentar procedimientos JIT, escalado y rollback.

### 🔹 Resumen — ¿Debes usar un Jump server?

Sí, si necesitas **gestionar acceso privilegiado a entornos internos** de forma segura, trazable y controlada. Un buen jump server, bien diseñado, reduce riesgos, facilita el cumplimiento y centraliza control. Pero requiere **operación y protección** (no es una solución “ponlo y olvídate”): parcheo, monitoreo, MFA, grabación y rotación de credenciales son obligatorios.

---

[🔼](#índice)

---

## **489. ¿Qué es un Bastion Host y por qué es tan importante?**

Un **Bastion Host** es una máquina endurecida y controlada que actúa como **punto único de acceso** a una red o zona protegida. En lugar de exponer muchos servidores al exterior, los administradores se conectan al bastion y desde allí acceden a los recursos internos. Su importancia radica en **reducir la superficie de ataque, centralizar control, auditar sesiones y cumplir requisitos de seguridad**.

### ¿Qué hace exactamente un bastion host?

Funciones principales:

- **Control de acceso**: punto centralizado donde la autenticación y autorización ocurren (SSH, RDP, MFA, certificados).
- **Proxy/porteador**: sirve como hop intermedio (ProxyJump / ProxyCommand), o como gateway RDP/SSH.
- **Registro y auditoría**: graba sesiones (TTY, comandos, archivos transferidos) para cumplimiento y forense.
- **Políticas**: aplica restricciones (qué hosts se pueden alcanzar, cuándo, quién).
- **Mitigación de riesgo**: reduce hosts públicos; los servidores internos sólo aceptan conexiones desde el bastion.

### ¿Quién usa bastion hosts?

- **SRE / DevOps / Admins** que gestionan infraestructuras.
- **Equipos de seguridad** para acceder a sensores, firewalls o appliances.
- **Proveedores de servicio gestionado** (MSP) que administran infra de clientes.
- Organizaciones sujetas a **regulación** (finanzas, salud, gobierno) que necesitan trazabilidad y control.

### ¿Cómo funciona (flujo típico)?

1. Admin desde su laptop se autentica al bastion (SSH/RDP) — idealmente con clave + MFA o certificado temporal.
2. El bastion valida identidad (RADIUS/LDAP/SSO/CA de SSH) y registra la sesión.
3. El bastion con sus reglas de firewall puede conectarse a los hosts internos; los hosts internos permiten conexiones **solo** desde las IPs del bastion.
4. Sesión grabada y logs enviados a SIEM para monitoreo y auditoría.

### Arquitectura típica (ASCII rápido)

```
Internet (admin)
    |
  [VPN / Corporate IP filter]  (opcional)
    |
  +-------------+
  |  Bastion HA |  <-- Solo estos tienen puerto SSH/RDP público
  +-------------+
        |
  Internal Management Subnet (no accesible desde Internet)
    |    |    |
  App1  DB1  JumpDB (targets)
```

En la nube: bastion en VPC pública + hosts internos en subnets privadas; Security Groups permiten SSH desde bastion únicamente.

### Ejemplo práctico: conexión con SSH ProxyJump

`~/.ssh/config`:

```text
Host bastion
  HostName bastion.example.com
  User admin
  IdentityFile ~/.ssh/id_rsa

Host internal-*
  ProxyJump bastion
  User admin
```

Uso:

```bash
ssh internal-db-01    # se conecta transparéntemente pasando por bastion
```

Ventaja: no necesitas abrir puerto SSH en todos los servidores ni mantener `authorized_keys` en cada servidor si usas SSH Certificates.

### Ejemplo: **sshd_config** duro para el bastion (fragmento recomendado)

Colocar en `/etc/ssh/sshd_config`:

```text
PermitRootLogin no
PasswordAuthentication no
ChallengeResponseAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PermitEmptyPasswords no
UsePAM yes

AllowTcpForwarding no
X11Forwarding no
PermitTunnel no

MaxAuthTries 3
LoginGraceTime 30
ClientAliveInterval 300
ClientAliveCountMax 2

TrustedUserCAKeys /etc/ssh/ca_user.pub   # para usar SSH certificates
ForceCommand /usr/local/sbin/jump-audit  # opcional: wrapper que inicia grabación
```

**Notas:** `AllowTcpForwarding=no` evita túneles no deseados; `TrustedUserCAKeys` permite firmar keys con CA.

### Autenticación moderna: SSH Certificates + JIT + MFA

- **SSH Certificates**: en vez de distribuir `authorized_keys` en cada host, se firma la clave pública del usuario con una CA. El servidor confía en la CA y acepta claves firmadas por ella (con expiración).
- **Just-In-Time (JIT)**: acceso temporal (p. ej. se concede por 1 hora).
- **MFA/SSO**: obligar segundo factor antes de emitir certificado o permitir sesión (integración con Okta, Azure AD, etc.).

Comando ejemplo para firmar (simplificado):

```bash
# CA firma la clave pública:
ssh-keygen -s ca_user -I "julio" -n julio -V +1h julio.pub
```

### Políticas de red (ejemplo AWS Security Groups)

- **Bastion SG**: Inbound SSH (22) desde IPs de administración o VPN; Outbound: a Management Subnet (destinos port 22).
- **Server SG**: Inbound SSH solo desde bastion SG (o IPs internas del bastion). No abrir SSH a Internet.

Ejemplo conceptual:

```
SG-Bastion:
 In: TCP 22 from 203.0.113.0/24 (corporate IPs)
 Out: TCP 22 to 10.0.1.0/24

SG-Internal:
 In: TCP 22 from SG-Bastion
```

### Grabación de sesiones y auditoría (por qué y cómo)

**Por qué:** cumplimiento (PCI, HIPAA, ISO), forense, responsabilidades legales, inspección de cambios.
**Cómo (opciones):**

- **tlog/ttyrec/script**: grabar stdout/stderr + timestamps.
- **Auditd / process accounting**: recoge execve, pid, uid.
- **PAM + wrapper**: `ForceCommand` llama un wrapper que inicia `script` para grabar.
- **Soluciones comerciales/OSS**: Teleport, BastionZero, CyberArk, BeyondTrust, AWS Session Manager (SSM) — ofrecen recording, search y control RBAC.

Ejemplo simple de wrapper (`/usr/local/sbin/jump-audit`):

```bash
#!/bin/bash
LOGDIR=/var/log/jump_sessions/$(date +%F)
mkdir -p "${LOGDIR}"
USERLOG="${LOGDIR}/${PAM_USER}-$(date +%s).cast"
exec /usr/bin/script -q -f "${USERLOG}"
```

(asegurar permisos, integridad, rotación, cifrado y envío a SIEM/S3)

### Ventajas de un bastion host

- **Reducción de superficie de ataque**: menos hosts expuestos.
- **Centralización**: política, autenticación y registro en un único punto.
- **Visibilidad**: todas las sesiones pasan por un control central (fácil monitoreo).
- **Cumplimiento**: facilita auditoría y trazabilidad.
- **Mejor gestión de claves**: con certificados y JIT reduces keys repartidas.

### Desventajas y riesgos

- **Punto único de compromiso**: si el bastion cae o se compromete, un atacante puede pivotar.
- **Necesidad de operación**: requiere hardening, parcheo, backups y monitoreo.
- **Escalado**: en grandes entornos puede ser cuellos de botella si no se diseñan HA y escalado.
- **Sobrecarga administrativa**: gestión de sesiones, retención y privacy considerations.

Mitigaciones: desplegar bastion en **alta disponibilidad**, hardening estricto, segmentación, políticas JIT y monitoreo en tiempo real.

### Buenas prácticas (lista imprescindible)

1. **Imagen minimalista y endurecida** (solo sshd y agentes necesarios).
2. **Autenticación fuerte**: claves + SSH certs + MFA; evitar passwords.
3. **Forzar expiración de credenciales** (certs cortos).
4. **No ForwardAgent** (evitar agent forwarding).
5. **Deshabilitar tunneling y X11** a menos que sea requerido.
6. **Session recording** y envío de logs a SIEM inmutable.
7. **Patching automatizado** y monitor de integridad (tripwire/OS hardening).
8. **RBAC**: controlar quién puede usar el bastion y a qué hosts.
9. **Networking**: hosts internos aceptan conexiones solo desde bastion.
10. **JIT & time windows**: acceso temporal, aprobado y registrado.
11. **Backups & HA**: múltiples bastions con LB o DNS/HA y logging central.
12. **Rotación de claves y revocación**: tener proceso para revocar certificados/keys comprometidas.

### Implementación: checklist paso-a-paso

1. Diseña la **ubicación de red** (DMZ / management VPC).
2. Crea una **imagen base** hardened (OS updates, firewall, minimal packages).
3. Habilita **sshd_config** con políticas seguras (ver ejemplo arriba).
4. Implementa **SSH Certificates** o integra con IdP/MFA.
5. Implementa **Session Recorder** (tlog/ForceCommand o solución comercial).
6. Configura **firewalls / security groups**: bastion público, targets privados.
7. Automatiza creación con IaC (Terraform/CloudFormation) y CI/CD.
8. Configura **monitoring & alerting** (CPU, auth failures, unusual traffic).
9. Tests: acceso normal, fallas, session replay, revocación de cert.
10. Poner en producción con pilot users y luego ampliar.

### Ejemplo de flujo de uso real (scenario)

**Objetivo:** Admin Julio necesita conectarse al servidor `db-prod-01` sin exponer SSH públicamente.

1. Julio abre su terminal:

```bash
ssh -J admin@bastion.example.com admin@db-prod-01
```

2. El bastion autentica a Julio, verifica su certificado, registra la sesión y la permite.
3. DB server sólo acepta conexiones SSH desde la IP privada del bastion.
4. Al finalizar, la sesión se graba y el fichero se almacena cifrado en S3; SIEM crea evento.

### Alternativas y complementos (cuando no quieres bastion tradicional)

- **AWS Systems Manager (SSM) Session Manager**: acceso sin abrir puerto SSH, con recording y auditoría integrados.
- **Azure Bastion**: RDP/SSH sin exposición de puertos.
- **Proxy-based solutions**: Teleport, BastionZero, Twingate — ofrecen autenticación, certificados, y sesiones registradas.
- **VPN + RBAC**: combinación de VPN corporativa + acceso limitado (pero pierde auditable single-hop).

### Problemas comunes y troubleshoot rápidos

- **Fallo de autenticación**: revisar `sshd` logs: `/var/log/auth.log` o `journalctl -u sshd`.
- **Latency/throughput**: bastion saturado → dimensionar (más instancias / LB).
- **Sessions not recorded**: revisar `ForceCommand` permiso y script path; asegurar que `script` instalado.
- **Hosts no aceptan conexión desde bastion**: revisar reglas de firewall / security group y rutas.
- **Key revoked / expired**: verificar CA revocation list o short-lived cert policy.

### Cumplimiento y gobernanza

- Registrar: **quién**, **qué**, **cuándo**, **duración**, **comandos ejecutados**.
- Retención de logs según normativa (PCI/HIPAA/ISO).
- Auditorías periódicas de accesos privilegiados.
- Separación de funciones: admin del bastion ≠ admin de servidores objetivo.

### Conclusión

Un **Bastion Host** bien diseñado es una pieza crucial para proteger, auditar y controlar el acceso privilegiado a infraestructuras. Reduce la superficie de ataque, centraliza control y facilita el cumplimiento, pero **requiere** operaciones continuas: hardening, parcheo, logging y diseño de alta disponibilidad. Hoy, la mejor práctica combina bastion hosts con **SSH Certificates**, **MFA**, **JIT access** y/o soluciones gestionadas (SSM / Teleport) para minimizar riesgos y mejorar la experiencia operativa.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 39**              | **Siguiente 41**                   |
| ------------------ | ------------------------- | ---------------------------------- |
| [🏠](../README.md) | [⏪](./13_39_Patching.md) | [⏩](./13_41_Endpoint_Security.md) |
