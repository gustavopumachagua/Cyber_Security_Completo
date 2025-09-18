| **Inicio**         | **atrás 27**             | **Siguiente 29**        |
| ------------------ | ------------------------ | ----------------------- |
| [🏠](../README.md) | [⏪](./13_27_WADCOMS.md) | [⏩](./13_29_syslog.md) |

---

## **Índice**

| Temario                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------ |
| [465. ¿Qué es un registro de eventos?](#465-qué-es-un-registro-de-eventos)                                                           |
| [466. ¿Qué son los registros de eventos y por qué son importantes?](#466-qué-son-los-registros-de-eventos-y-por-qué-son-importantes) |

# **Event Logs**

## **465. ¿Qué es un registro de eventos?**

![Event Logs](/img/13_Tools_for_Incident_Response_and_Discovery/Event_Logs.png "Event Logs")

Un **registro de eventos** es un archivo o base de datos donde un sistema operativo, aplicación, servicio o dispositivo de red **almacena de manera cronológica los sucesos que ocurren** en él.

En ciberseguridad, son la **fuente primaria de evidencia** porque permiten responder preguntas como:

- ¿Quién accedió al sistema?
- ¿Qué cambios se realizaron?
- ¿Cuándo ocurrió una actividad sospechosa?
- ¿Hubo fallos o intentos de intrusión?

👉 Ejemplo:

En Windows, los **Windows Event Logs** guardan eventos de inicio de sesión, instalación de software, fallos de seguridad, etc.

En Linux, se usan archivos como `/var/log/auth.log`, `/var/log/syslog`, `/var/log/secure`.

### 📌 Definición del registro de eventos

Un **registro de eventos** es:

> “Un conjunto estructurado de datos que describe una acción, su contexto (quién, qué, cuándo, dónde, cómo) y su resultado, generado automáticamente por un sistema, aplicación o dispositivo para fines de auditoría, operación o seguridad.”

### 📌 ¿Qué contiene un registro de eventos?

Cada evento es una **entrada** en el registro, y contiene información clave sobre lo ocurrido.

Ejemplo en Windows (inicio de sesión):

```
Log Name: Security
Event ID: 4624
Level: Information
User: JuanPerez
Computer: SERVER01
Time: 2025-09-16 11:23:55
Source: Microsoft Windows security auditing.
Message: An account was successfully logged on.
```

### 📌 Campos comunes del registro de eventos

Aunque varían según el sistema, los más comunes son:

1. **Fecha y hora** (timestamp) → Cuándo ocurrió.
2. **Origen/Source** → Qué sistema o aplicación generó el evento.
3. **ID de evento** → Número único para identificar el tipo de evento (ej. 4624 = inicio de sesión exitoso en Windows).
4. **Nivel de severidad** → Informativo, Advertencia, Error, Crítico.
5. **Usuario o cuenta** → Quién realizó la acción.
6. **Equipo/host** → Dónde ocurrió.
7. **Descripción/mensaje** → Detalles del evento.
8. **Dirección IP / PID / Process** → (cuando aplica) información técnica adicional.

👉 Ejemplo en Linux (`/var/log/auth.log`):

```
Sep 16 11:25:10 server1 sshd[2531]: Accepted password for root from 192.168.1.15 port 54321 ssh2
```

- Fecha/Hora → `Sep 16 11:25:10`
- Host → `server1`
- Proceso → `sshd[2531]`
- Usuario → `root`
- Acción → `Accepted password`
- IP origen → `192.168.1.15`

### 📌 ¿Cómo se rellenan los registros de eventos?

Se generan automáticamente mediante **sistemas de logging**:

- **Windows** → a través de Windows Event Log Service (`eventlog.dll`) que guarda en archivos `.evtx`.
- **Linux** → mediante `syslog`, `journald`, o servicios como `rsyslog`.
- **Aplicaciones** → muchas apps (ej. Apache, Nginx, SQL Server) tienen sus propios logs.
- **Dispositivos de red** → routers/firewalls generan syslogs o SNMP traps enviados a un servidor central.

### 📌 ¿Por qué son tan importantes los registros de eventos?

1. **Detección de incidentes** → permiten identificar intrusiones, accesos no autorizados, malware, abuso de credenciales.
2. **Investigación forense** → reconstruyen la línea de tiempo de un ataque.
3. **Cumplimiento normativo** → regulaciones como GDPR, PCI-DSS, HIPAA exigen mantener registros.
4. **Monitoreo operativo** → ayudan a diagnosticar fallos técnicos.
5. **Automatización** → los SIEM (Security Information and Event Management) procesan logs en tiempo real para generar alertas.

👉 Sin registros de eventos, un ataque podría pasar desapercibido.

### 📌 Plataforma nativa de IA para SIEM y gestión de registros

Hoy en día, los **SIEM de nueva generación** (Next-Gen SIEM) usan **IA y machine learning** para:

- Normalizar y correlacionar registros de distintas fuentes (Windows, Linux, firewall, nube).
- Detectar **patrones anómalos** que un humano o una regla estática no detectaría.
- Reducir **falsos positivos**.
- Priorizar incidentes críticos mediante análisis de riesgo.

Ejemplos de SIEM líderes que usan IA nativa:

- **Splunk Enterprise Security**
- **Microsoft Sentinel (Azure)**
- **Elastic Security**
- **IBM QRadar con Watson**
- **Chronicle Security (Google)**

✅ Resumen rápido:

- Un **registro de eventos** = archivo/base de datos con sucesos de un sistema.
- Contiene: fecha, origen, ID, usuario, host, descripción.
- Se rellenan automáticamente por el SO, apps o dispositivos.
- Son críticos para seguridad, auditoría y forense digital.
- SIEMs modernos con IA permiten analizar millones de logs en segundos y detectar ataques avanzados.

---

[🔼](#índice)

---

## **466. ¿Qué son los registros de eventos y por qué son importantes?**

Un **registro de eventos** es un archivo o base de datos en el que un sistema operativo, aplicación o dispositivo **almacena cronológicamente sucesos relevantes**: inicios de sesión, errores, accesos, instalaciones, cambios de configuración, etc.

👉 **Importancia**:

- Son la **evidencia digital primaria** para diagnosticar fallos y detectar intrusiones.
- Permiten a administradores y equipos de ciberseguridad **auditar**, **investigar incidentes** y **cumplir regulaciones** (PCI-DSS, ISO 27001, HIPAA, etc.).

Ejemplo:

Si un atacante entra con credenciales robadas, un registro de eventos de Windows **Security ID 4624 (Logon exitoso)** puede evidenciar la hora, el usuario y la máquina origen.

### 📌 ¿Qué contiene un registro de eventos?

Cada **entrada de evento** tiene varios elementos clave que describen la acción:

- **Fecha y hora (timestamp)** → cuándo ocurrió.
- **Origen (source)** → qué sistema/proceso lo generó.
- **Tipo o ID de evento** → un número único para clasificar el suceso.
- **Usuario involucrado** → quién ejecutó la acción.
- **Resultado** → éxito o fallo.
- **Descripción** → mensaje detallado del evento.

### 📌 Campos comunes del registro de eventos

Independientemente del sistema, la mayoría de logs incluyen:

1. **Event ID** → Identificador único del evento.
2. **Nivel** → Informativo, Advertencia, Error, Crítico.
3. **Fecha/Hora** → Precisión temporal.
4. **Usuario / Cuenta** → Relacionado con la acción.
5. **Equipo/Host** → Dónde ocurrió.
6. **Proceso / PID** → Qué aplicación lo disparó.
7. **Mensaje** → Texto descriptivo.

👉 Ejemplo en **Windows** (logon):

```
Event ID: 4624
Level: Information
Account Name: Juan
Source Network Address: 192.168.1.15
Message: Se inició sesión correctamente.
```

👉 Ejemplo en **Linux (`/var/log/auth.log`)**:

```
Sep 16 14:12:01 server1 sshd[2111]: Failed password for root from 10.0.0.5 port 50532 ssh2
```

### 📌 ¿Dónde se almacenan los registros de eventos?

- **Windows** → en archivos `.evtx` dentro de `C:\Windows\System32\winevt\Logs\` (ej: Security.evtx, Application.evtx). Se accede con el _Visor de eventos_.
- **Linux/Unix** → en `/var/log/` (ej: `auth.log`, `syslog`, `messages`). Administrados por `rsyslog`, `journald`.
- **Aplicaciones** → servidores web (Apache: `access.log`, `error.log`), bases de datos, antivirus, etc.
- **Dispositivos de red** → generan logs que suelen enviarse vía **Syslog** a un servidor central o SIEM.

### 📌 Uso de registros de eventos para seguridad

1. **Detección de intrusiones** → intentos de inicio de sesión fallidos, movimientos laterales, escalada de privilegios.
2. **Monitoreo continuo** → identificar comportamientos anómalos (ej. acceso fuera de horario).
3. **Forense digital** → reconstrucción de la línea de tiempo de un ataque.
4. **Cumplimiento normativo** → mantener logs centralizados y auditables.
5. **Correlación en SIEMs** → combinar miles de eventos para detectar ataques complejos.

### 📌 Registros de eventos de seguridad comunes de Windows y su significado

| **Event ID** | **Significado**                                            |
| ------------ | ---------------------------------------------------------- |
| **4624**     | Inicio de sesión exitoso.                                  |
| **4625**     | Intento de inicio de sesión fallido.                       |
| **4634**     | Cierre de sesión.                                          |
| **4672**     | Asignación de privilegios especiales (ej. admin).          |
| **4688**     | Creación de un nuevo proceso (útil para detectar malware). |
| **4720**     | Creación de una nueva cuenta de usuario.                   |
| **4726**     | Eliminación de una cuenta de usuario.                      |
| **4740**     | Cuenta de usuario bloqueada.                               |

👉 Estos eventos son fundamentales para **detectar intrusiones en Windows**.

### 📌 Mejores prácticas de registro de seguridad

1. **Centralización** → enviar todos los logs a un **servidor de registros o SIEM** (Splunk, ELK, Microsoft Sentinel, QRadar).
2. **Retención adecuada** → conservar logs el tiempo que exija la normativa (ej. 90 días a 1 año).
3. **Correlación y alertas** → crear reglas (ej. 10 intentos 4625 en 1 minuto = alerta de fuerza bruta).
4. **Protección de la integridad** → asegurar que los atacantes no borren o alteren logs.
5. **Clasificación** → dar prioridad a eventos críticos (IDs de seguridad, escalada, creación de cuentas).
6. **Automatización con IA** → SIEMs modernos usan machine learning para detectar patrones anómalos en logs.

✅ **En resumen**:

- Los registros de eventos son la **caja negra** de un sistema.
- Contienen datos vitales para administración y seguridad.
- Su análisis permite detectar ataques, cumplir regulaciones y hacer análisis forense.
- Usarlos bien implica **centralización, correlación y protección de la integridad**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 27**             | **Siguiente 29**        |
| ------------------ | ------------------------ | ----------------------- |
| [🏠](../README.md) | [⏪](./13_27_WADCOMS.md) | [⏩](./13_29_syslog.md) |
