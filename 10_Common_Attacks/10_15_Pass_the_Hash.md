| **Inicio**         | **atrás 14**                 | **Siguiente 16**                     |
| ------------------ | ---------------------------- | ------------------------------------ |
| [🏠](../README.md) | [⏪](./10_14_Memory_Leak.md) | [⏩](./10_16_Directory_Traversal.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [327. ¿Qué es un ataque pass-the-hash?](#327-qué-es-un-ataque-pass-the-hash) |
| [328. Pasar el ataque de hash](#328-pasar-el-ataque-de-hash)                 |

# **Pass the Hash**

## **327. ¿Qué es un ataque pass-the-hash?**

![Pass the Hash](/img/10_Common_Attacks/Pass_the_Hash.jpg "Pass the Hash")

**Pass-the-Hash (PtH)** es una técnica de movimiento lateral en entornos Windows en la que un atacante reutiliza **hashes de contraseñas** (normalmente hashes NTLM) para autenticarse ante otros equipos sin conocer nunca la contraseña en texto claro. En lugar de “robar” la contraseña y volver a escribirla, el atacante entrega directamente el hash al protocolo de autenticación para obtener acceso.

Es una técnica clásica en compromisos de red y muy poderosa porque rompe la suposición “si no conozco la contraseña no puedo autenticarme”.

### ¿Cómo funciona un ataque Pass-the-Hash? (flujo y componentes)

Resumen del flujo típico — **sin pasos operativos**:

1. **Compromiso inicial**: el atacante obtiene acceso a una máquina en la red (phishing, exploit, credenciales débiles).
2. **Recolección de credenciales**: extrae hashes almacenados en la máquina comprometida (p. ej. NTLM hashes en memoria LSASS o en SAM/NTDS.dit). Herramientas o técnicas conocidas pueden volcar la memoria del proceso LSASS o extraer hashes del disco si el atacante tiene permisos.
3. **Reutilización del hash**: el atacante usa el hash para autenticarse en otros equipos/servicios que aceptan NTLM (SMB, RPC, WinRM, etc.) sin necesidad de la contraseña. Esto permite **movimiento lateral** y escalado de privilegios.
4. **Pivot y persistencia**: con cuentas más privilegiadas (local admin, domain admin) el atacante puede acceder a servidores críticos, extraer más credenciales y persistir.

Técnicamente: NTLM realiza un challenge-response donde el servidor envía un reto y el cliente responde usando el hash. Si el atacante puede inyectar el hash en la pila de autenticación del sistema (o usar APIs que aceptan hash), el protocolo lo acepta como si fuese legítimo.

**Distinción importante:** PtH usa hashes NTLM; hay ataques relacionados (Pass-the-Ticket) que reutilizan tickets Kerberos (TGT/TGS). Ambos permiten autenticarse sin la contraseña clara pero atacan mecanismos distintos.

### Por qué PtH es peligroso

- Los hashes se **pueden volcar** desde memoria si el atacante obtiene privilegios locales.
- Muchas infraestructuras aún permiten NTLM y autenticación basada en hashes.
- Cuentas con privilegios locales replicables (misma contraseña en muchos equipos) facilitan el abuso.
- Permite movimiento lateral rápido y compromisos a gran escala (p. ej. hasta Domain Admin).

### Señales y detección (qué mirar)

- Eventos Windows que muestren autenticaciones NTLM inusuales: `EventID 4624` (Logon) con _Authentication Package_ = `NTLM` y logon type que no corresponde (p.ej. 3/10).
- Autenticaciones con la misma cuenta desde muchas máquinas en corto intervalo.
- Volcados de LSASS o procesos usados para extracción de credenciales (herramientas EDR suelen alertar).
- Detección de ejecución de herramientas de credenciales (cmd.exe/tprolling, rundll32 con inyección, etc.).
- Uso de cuentas de servicio en contextos interactivos o accesos remotos inesperados.
  (Implementar alertas en SIEM/EDR con correlación.)

### Mitigaciones (detalladas, con pasos prácticos)

#### 1) Limite el acceso a la red y los privilegios de la cuenta

**Objetivo:** minimizar cuentas con privilegios amplios y reducir la superficie de robo de hashes.

Pasos concretos:

- **Eliminar uso de cuentas “admin local” con la misma contraseña** en muchos equipos. Usa Managed Local Admin (p. ej. Microsoft LAPS) para rotar contraseñas locales por equipo.
- **Principio de menor privilegio:** las cuentas deben tener solo los permisos necesarios; evita que usuarios estándar tengan derechos de administración local.
- **Separación de cuentas:** no usar cuentas administrativas para tareas diarias; emplea cuentas normales y cuentas dedicadas de administración (Privileged Access Workstations, PAW).
- **Segmentación de red:** limita el acceso administrativo a segmentos concretos (bastion hosts, jump boxes).
- **Desactivar la autenticación NTLM** cuando sea posible; migrar a Kerberos/AES y políticas que restrinjan NTLM: configurar Group Policy `Network security: Restrict NTLM` para auditar/deny.
- **Evitar cuentas de servicio con privilegios excesivos** y crear cuentas administradas para servicios.

Ejemplo práctico: desplegar **LAPS** (Local Administrator Password Solution) para que cada equipo tenga una contraseña de administrador local única y rotada automáticamente; así un hash volcado en un host no sirve para acceder a otros hosts.

### 2) Implementar una solución de detección y respuesta a amenazas de identidad (Identity Threat Detection & Response)

**Objetivo:** detectar explotación temprana, movimiento lateral y compressión de credenciales.

Pasos concretos:

- Desplegar EDR/EDR-like con capacidades de detección de robo de credenciales y volcado de memoria (Microsoft Defender for Endpoint, CrowdStrike, SentinelOne, etc.). Estas soluciones detectan: creación de procesos sospechosos, inyección en LSASS, volcado de memoria y uso de herramientas conocidas (Mimikatz).
- Implementar **Identity Threat Detection** (por ejemplo, Microsoft ATA/Defender for Identity, Okta ThreatInsight integraciones) para detectar patrones de abuso de credenciales.
- Configurar alertas en SIEM para: múltiples inicios de sesión NTLM con misma cuenta desde numerosas IPs, ejecución de `rundll32` o `procdump` con argumentos inusuales, y lecturas del registro de seguridad relacionadas.
- Habilitar y monitorizar **Event ID** relevantes: 4624 (Logon), 4625 (Logon failed), 4672 (special privileges), 4776 (Credential validation), 4648 (explicit credentials), etc. Crea playbooks de respuesta automatizada.

Resultado: la detección temprana puede bloquear lateralidad antes de que el atacante alcance cuentas de dominio.

### 3) Hacer cumplir la higiene de TI

**Objetivo:** reducir vectores de obtención de hashes y limitar permanencia.

Medidas concretas:

- **Parches y vulnerabilidades:** mantener sistemas y software actualizados (mitigar exploits que permiten ejecución y extracción de credenciales).
- **Restricciones en LSASS:** habilitar LSASS protection (LSA Protection/RunAsPPL) y Credential Guard en endpoints Windows 10/11 Enterprise/Server para proteger credenciales en memoria.
- **Deshabilitar almacenamiento de credenciales en texto claro** (WDIGEST/LSA) y eliminar caching innecesario (`AllowProtectedCreds`, `DisablePasswordCaching`).
- **Configurar políticas de bloqueo de cuentas y MFA:** aplicar MFA obligatoria para accesos remotos y administrativos (incluyendo RDP/WinRM/Portal).
- **Revisar y limitar uso de servicios que requieren contraseñas almacenadas**; usar Managed Service Accounts y gMSA para servicios.
- **Inventario y hardening de cuentas con privilegios**: auditar y recertificar privilegios regularmente.

Ejemplo: habilitar **Windows Defender Credential Guard** (contenerización de credenciales) en endpoints compatibles para evitar que hashes/credenciales se expongan en memoria.

### 4) Realice pruebas de penetración periódicas

**Objetivo:** validar controles y descubrir huecos antes de que lo haga un atacante.

Cómo:

- Ejecutar pentests autorizados que incluyan simulación de robo de credenciales (red team), pero siempre **seguros y controlados**.
- Fuzzing de procesos de autenticación y evaluación de la configuración NTLM/Kerberos.
- Revisiones de configuración Active Directory: cuentas con derechos excesivos, ACLs en AD, delegaciones peligrosas y privilegios de replicación (DCSync).
- Validar segmentación y hardening de endpoints (LAPS, Credential Guard, 802.1X).

Resultado: corregir hallazgos, priorizar runbooks y verificar que mitigaciones son efectivas.

### 5) Adopte la búsqueda proactiva de amenazas (Threat Hunting)

**Objetivo:** encontrar señales débiles y actividad anómala que las alertas automáticas no captan.

Pasos prácticos:

- Crear queries hunting en logs: por ejemplo, buscar `4624` con `Authentication Package = NTLM` y correlacionar con `originating IP` y `time`.
- Hunting por procesos que acceden a `LSASS` o invocan `procdump`, `rundll32`, `reg.exe`, `ntdsutil`.
- Monitorizar creación de nuevas cuentas de servicio, asignaciones de grupo Domain Admin y replicación inusual del controlador.
- Realizar ejercicios de **purple team**: combinar hunters y detección para validar reglas SIEM/EDR.

Herramientas: SIEM (Splunk, Microsoft Sentinel), EDR, Defender for Identity, Elastic, GRR, y playbooks de respuesta.

### Checklist resumido (acción inmediata)

- [ ] Activar LAPS para rotación de contraseñas locales.
- [ ] Desplegar/forzar MFA en accesos administrativos.
- [ ] Habilitar y exigir Credential Guard / LSA protection en endpoints compatibles.
- [ ] Limitar NTLM mediante políticas GPO y auditar su uso.
- [ ] Implementar EDR con detección de volcado de LSASS y alertas.
- [ ] Separar cuentas de administración (PAW) y aplicar principio de menor privilegio.
- [ ] Ejecutar pentests regulares y hunting proactivo.

### FAQ corto

**Q: ¿Pass-the-Hash necesita acceso físico?**

No necesariamente — basta con que el atacante obtenga un hash (desde memoria o disco) con privilegios locales o de dominio.

**Q: ¿Desactivar NTLM lo arregla todo?**

Desactivar NTLM reduce la superficie, pero requiere planificación: muchas aplicaciones legacy usan NTLM. Mejor auditar/mitigar progresivamente y sustituir por Kerberos/JWKerberos/MFA.

**Q: ¿Credential Guard previene PtH?**

Sí: **Credential Guard** protege secretos en un contenedor aislado, haciendo mucho más difícil extraer hashes de LSASS. No es una panacea (hay vectores complementarios), pero es una defensa fuerte en endpoints compatibles.

---

[🔼](#índice)

---

## **328. Pasar el ataque de hash**

### Visión conceptual (NO operativa) — cómo piensan los atacantes

(esto **explica** el riesgo sin dar instrucciones exploitables)

- **Paso conceptual 1 — Obtención de hashes**

  El atacante busca un sistema ya comprometido o un vector que le permita acceder a credenciales o a la memoria donde se almacenan derivados de contraseñas (hashes).

- **Paso conceptual 2 — Reutilización del hash para autenticarse**

  Con un hash válido, el atacante intenta autenticar servicios/protocolos que aceptan ese hash (p. ej. mecanismos históricos de Windows como NTLM), para moverse lateralmente sin conocer la contraseña en texto plano.

- **Paso conceptual 3 — Movimiento lateral y acceso a recursos**

  Con acceso a nuevas máquinas y cuentas, el atacante escala privilegios, extrae más credenciales y persiste, hasta alcanzar objetivos de alto valor.

### Defensa alineada a los 3 pasos (qué hacer inmediatamente)

#### Contra Paso 1 — Prevención de robo de hashes

Objetivo: reducir la capacidad del atacante para obtener hashes desde endpoints/servidores.

Medidas clave (prácticas y urgentes)

- **Higiene de cuentas**: separar cuentas administrativas de cuentas de usuario; no usar cuentas admin para tareas diarias.
- **LAPS (Local Admin Password Solution)**: rotar/individualizar contraseñas de administrador local.
- **Deshabilitar/limitar NTLM**: auditar uso NTLM y, donde sea viable, migrar a Kerberos y políticas que restrinjan NTLM.
- **Credential Guard / LSA Protection**: habilitar protecciones que dificultan volcado de credenciales en memoria.
- **Aplicar parcheo** y reducir la superficie de explotación que permitiría acceso inicial (exploits remotos, servicios expuestos).
- **Segmentación de red** y control de acceso (jump boxes, bastion hosts) para que las cuentas administrativas no tengan acceso indiscriminado.

Detecciones a implementar

- Monitorizar intentos de volcado/lectura de procesos sensibles (LSASS), y creación o ejecución de herramientas de volcado.
- Alertas EDR/EDR-like para procesos que acceden a LSASS (o intentos de lectura de memoria del sistema) y para uso de utilidades de extracción.

Eventos Windows útiles a monitorear

- `EventID 4688` (process creation) — buscar creación de procesos sospechosos que suelen volcar memoria.
- `EventID 4672` / `4624` / `4625` — logins privilegiados y fallos.
- Detección de usos de APIs inusuales o herramientas de dumping (que tu EDR debería identificar).

#### Contra Paso 2 — Detección de uso de hashes (Pass-the-Hash attempts)

Objetivo: detectar cuando hashes son reutilizados para autenticarse.

Detección y reglas (ejemplos defensivos)

- **Correlación de logins anómalos**: misma cuenta autenticándose desde múltiples hosts en un corto intervalo; especialmente con _Authentication Package = NTLM_.
- **Logon type inusual**: RDP/Network logons desde cuentas de servicio o equipos que nunca antes las usaron.
- **Múltiples autenticaciones con credenciales administrativas** originadas desde una estación de trabajo que no es una consola administrativa.
- **Eventos Windows a vigilar**: `4624` (successful Logon — revisar AuthenticationPackage), `4648` (logon using explicit credentials), `4776` (credential validation).
- **EDR/Identity Threat Detection**: alertas sobre uso de credenciales en contextos anómalos, o sobre reutilización de hashes.

Ejemplo de query SIEM (pseudo-SQL / KQL) — úsalo como punto de partida defensivo:

```
/* Buscar logins NTLM desde muchos hosts en ventana de 10 min */
Event
| where EventID == 4624 and AuthenticationPackageName == "NTLM"
| summarize by Account = AccountName, Hosts = make_set(Computer, 100), Count = count()
| where Count > 5
```

(Ajusta umbrales a tu entorno.)

#### Contra Paso 3 — Contención / Erradicación / Recuperación

Objetivo: cuando detectes actividad sospechosa, contener lateralidad y limpiar credenciales comprometidas.

Respuesta inmediata (playbook resumido)

1. **Contención rápida**

   - Aislar host(s) comprometidos de la red (network quarantine).
   - Bloquear cuentas sospechosas, forzar rotación de contraseñas críticas y revocar sesiones activas.

2. **Investigación forense**

   - Capturar imágenes/memory dumps de los hosts afectados (solo con EDR/forense autorizado).
   - Recolectar logs: eventos de seguridad, NetFlow, DNS, alertas EDR.

3. **Erradicación**

   - Eliminar backdoors, malware y cuentas persistentes.
   - Aplicar parches y reconfigurar privilegios.

4. **Recuperación**

   - Reinstalar/actualizar hosts críticos si es necesario.
   - Restaurar servicios desde backups limpias.

5. **Post-incidente**

   - Rotación masiva de credenciales (especialmente locales y administrativas).
   - Revisar y endurecer controles: LAPS, Credential Guard, políticas NTLM.
   - Reportar e incorporar lecciones aprendidas.

### Artefactos defensivos listos para usar

#### 1) Checklist operativo (rápido)

- [ ] Habilitar LAPS para administradores locales.
- [ ] Forzar MFA en accesos administrativos y remoto (RDP).
- [ ] Desplegar/afinar EDR con detección de volcado de LSASS.
- [ ] Habilitar Credential Guard / LSA protection donde sea compatible.
- [ ] Auditar y reducir NTLM (políticas GPO).
- [ ] Implementar segmentación y jump boxes para administración.
- [ ] Incluir tests de pentest/red team autorizados y ejercicios de purple team.

#### 2) Reglas/Queries de SIEM (defensivas — ejemplos)

- Detección de múltiples logins NTLM desde distintos hosts:

  ```
  index=security EventID=4624 AuthenticationPackageName="NTLM"
  | stats dc(ComputerName) as hosts by AccountName
  | where hosts > 3
  ```

- Detección de procesos que acceden a LSASS (vía EDR telemetry):

  ```
  ProcessCreation where ProcessName in ("procdump.exe","rundll32.exe","taskmgr.exe") and ParentProcess not in ("explorer.exe","svchost.exe")
  ```

#### 3) Reglas y alertas EDR recomendadas

- Alertar si un proceso no estándar abre handle a `lsass.exe`.
- Alertar creación de herramientas de dump o presencia de binarios conocidos de credential dumping.
- Alertar actividad de lateral movement: uso de `PsExec`/WinRM/SMB desde hosts no administrativos.

### Buenas prácticas organizacionales (estratégicas)

- **Zero Trust / Principle of Least Privilege**: segmentar y minimizar rutas de autenticación directa entre hosts.
- **Privileged Access Workstations (PAW)**: estaciones dedicadas para administración, sin navegación o correo.
- **Rotación de secretos y credenciales** automatizada (LAPS, Vaults).
- **Formación y phishing simulations** para reducir compromiso inicial.
- **Threat hunting y purple team** regulares para validar detección.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**                 | **Siguiente 16**                     |
| ------------------ | ---------------------------- | ------------------------------------ |
| [🏠](../README.md) | [⏪](./10_14_Memory_Leak.md) | [⏩](./10_16_Directory_Traversal.md) |
